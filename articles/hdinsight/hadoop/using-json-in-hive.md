---
title: Analisar & processo JSON com Colmeia Apache - Azure HDInsight
description: Aprenda a usar documentos JSON e analisá-los usando a Colmeia Apache no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: db7c7ae9889d26479f51a7714e7e9fb04b444628
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757116"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Processar e analisar documentos JSON usando o Apache Hive no HDInsight do Azure

Saiba como processar e analisar arquivos JSON (JavaScript Object Notation) usando o Apache Hive no HDInsight do Azure. Este artigo usa o seguinte documento JSON:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

O arquivo pode ser encontrado em `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Para obter mais informações sobre como usar armazenamento de Blobs do Azure com HDInsight, consulte [Usar armazenamento de Blobs do Azure compatível com HDFS com Apache Hadoop no HDInsight](../hdinsight-hadoop-use-blob-storage.md). Você pode copiar o arquivo para o contêiner padrão do seu cluster.

Neste artigo, você usa o console Apache Hive. Para obter instruções sobre como abrir o console Hive, consulte [Use Apache Ambari Hive View com Apache Hadoop no HDInsight](apache-hadoop-use-hive-ambari-view.md).

> [!NOTE]  
> A exibição do Hive não está mais disponível no HDInsight 4.0.

## <a name="flatten-json-documents"></a>Nivelar os documentos JSON

Os métodos listados na próxima seção exigem que o documento JSON seja composto por uma única linha. Portanto, você deve nivelar o documento JSON com uma cadeia de caracteres. Se seu documento JSON já está nivelado, ignore esta etapa e vá diretamente para a próxima seção sobre como analisar dados JSON. Para nivelar o documento JSON, execute o seguinte script:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

O arquivo JSON bruto está localizado em `wasb://processjson@hditutorialdata.blob.core.windows.net/`. A tabela **StudentsRaw** Hive aponta para o documento JSON bruto que não é achatado.

A tabela **StudentsOneLine** do Hive armazena os dados no sistema de arquivos padrão do HDInsight no caminho **/json/students/**.

A instrução **INSERT** preenche a tabela **StudentOneLine** com os dados JSON nivelados.

A instrução **SELECT** retorna apenas uma linha.

Veja abaixo a saída da instrução **SELECT**:

![HDInsight achatando o documento JSON](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Analisar documentos JSON no Hive

O Hive fornece três mecanismos diferentes para executar consultas em documentos JSON, ou você pode escrever o seu:

* Use a função definida pelo usuário get_json_object (UDF).
* Use o json_tuple UDF.
* Use o Serializador/Desserializador (SerDe) personalizado.
* Grave seu próprio UDF usando Python ou outras linguagens. Para saber mais sobre como executar seu próprio código Python com o Hive, consulte [UDF do Python com Apache Hive e Pig](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Usar o UDF get_json_object

A Hive fornece um UDF incorporado chamado [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) que consulta JSON durante o tempo de execução. Este método requer dois argumentos: o nome da tabela e o nome do método. O nome do método tem o documento JSON achatado e o campo JSON que precisa ser analisado. Vamos ver um exemplo para ver como este UDF funciona.

A consulta a seguir retorna o nome e o sobrenome de cada aluno:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Aqui está a saída quando você executar essa consulta na janela do console:

![Colmeia Apache recebe objeto json UDF](./media/using-json-in-hive/hdinsight-get-json-object.png)

Há limitações do UDF get_json_object:

* Uma vez que cada campo na consulta requer uma nova análise da consulta, o desempenho é afetado.
* **GET\_JSON_OBJECT()** retorna a representação da cadeia de caracteres de uma matriz. Para converter essa matriz em uma matriz do Hive, você precisará usar expressões regulares para substituir os colchetes “[” e “]” e depois também realizar a divisão para obter a matriz.

Esta conversão é a razão pela qual a wiki Hive recomenda que você use **json_tuple**.  

### <a name="use-the-json_tuple-udf"></a>Usar o json_tuple UDF

Outra UDF fornecida pela Hive é chamada [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), que faz melhor do que [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Este método leva um conjunto de teclas e uma seqüência JSON. Em seguida, retorna uma tuplo de valores. A consulta a seguir retorna a ID e a série do aluno por meio do documento JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

A saída deste script no console do Hive:

![Resultados da consulta apache hive json](./media/using-json-in-hive/hdinsight-json-tuple.png)

O `json_tuple` UDF usa a sintaxe de [visão](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) lateral\_em Hive, que permite que json tuple crie uma tabela virtual aplicando a função UDT em cada linha da tabela original. JSONs complexos se tornam muito complicados devido ao uso repetido de **LATERAL VIEW**. Além disso, **JSON_TUPLE** não pode lidar com JSONs aninhados.

### <a name="use-a-custom-serde"></a>Usar um SerDe personalizado

SerDe é a melhor opção para análise de documentos JSON aninhados. Ele permite que você defina o esquema JSON e então use o esquema para analisar os documentos. Para obter instruções, veja [Como usar um SerDe JSON personalizado com o Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Resumo

O tipo de operador JSON em Hive que você escolhe depende do seu cenário. Com um documento JSON simples e um campo para procurar, escolha a Colmeia UDF **get_json_object**. Se você tem mais de uma chave para olhar para cima, então você pode **usájson_tuple**. Para documentos aninhados, use o **SerDe JSON**.

## <a name="next-steps"></a>Próximas etapas

Para artigos relacionados, veja:

* [Usar Apache Hive e HiveQL com Apache Hadoop no HDInsight para analisar um exemplo do arquivo log4j do Apache](../hdinsight-use-hive.md)
* [Analise os dados de atraso de voo usando consulta interativa no HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Analisar dados do Twitter usando Apache Hive no HDInsight](../hdinsight-analyze-twitter-data-linux.md)
