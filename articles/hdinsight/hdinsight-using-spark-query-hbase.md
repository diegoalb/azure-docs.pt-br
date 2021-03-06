---
title: Usar o Spark para ler e gravar dados do HBase - Azure HDInsight
description: Use o conector do HBase Spark para ler e gravar dados de um cluster Spark para um cluster HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/24/2020
ms.openlocfilehash: 888f24e13ce67c878592068927383dd8cbfefa60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623099"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Usar o Apache Spark para ler e gravar dados do Apache HBase

Apache HBase costuma ser consultada com sua API de nível inferior (verificações, obtenções e colocações) ou com uma sintaxe SQL usando Apache Phoenix. Apache também fornece conector HBase Apache Spark, que é uma alternativa conveniente e de alto desempenho para consultar e modificar os dados armazenados pelo HBase.

## <a name="prerequisites"></a>Pré-requisitos

* Dois clusters HDInsight separados implantados na mesma [rede virtual](./hdinsight-plan-virtual-network-deployment.md). Um HBase e um Spark com pelo menos O Spark 2.1 (HDInsight 3.6) instalado. Para obter mais informações, consulte [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* O [esquema de URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) do seu armazenamento primário de clusters. Esse esquema seria wasb:// para o Azure Blob Storage, abfs:// para o Azure Data Lake Storage Gen2 ou adl:// para o Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para `wasbs://`o Blob Storage, o URI será .  Confira também [Transferência segura](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Processo geral

O processo de alto nível para habilitar seu cluster Spark para consultar seu cluster HDInsight é o seguinte:

1. Preparar alguns dados de exemplo no HBase.
2. Adquira o arquivo hbase-site.xml da pasta de configuração de cluster HBase (/ etc/hbase/conf).
3. Coloque uma cópia do hbase-site.XML na sua pasta de configuração Spark 2 (/ etc/spark2/conf).
4. Execute `spark-shell` referenciar o conector do HBase Spark por seu Maven coordena na opção `packages`.
5. Defina um catálogo que mapeia o esquema do Spark para HBase.
6. Interagir com os dados do HBase usando as APIs de DataFrame ou RDD.

## <a name="prepare-sample-data-in-apache-hbase"></a>Preparar os dados de exemplo no Apache HBase

Nesta etapa, você cria e preenche uma tabela no Apache HBase que você pode consultar usando o Spark.

1. Use `ssh` o comando para se conectar ao cluster HBase. Edite o comando `HBASECLUSTER` abaixo substituindo com o nome do seu cluster HBase e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Use `hbase shell` o comando para iniciar o shell interativo HBase. Digite o seguinte comando em sua conexão de SSH:

    ```bash
    hbase shell
    ```

3. Use `create` o comando para criar uma tabela HBase com famílias de duas colunas. Insira o seguinte comando:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Use `put` o comando para inserir valores em uma coluna especificada em uma linha especificada em uma tabela específica. Insira o seguinte comando:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. Use `exit` o comando para parar o shell interativo HBase. Insira o seguinte comando:

    ```hbase
    exit
    ```

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>Copiar hbase-site.xml para cluster Spark

Copie o hbase-site.xml do armazenamento local para a raiz do armazenamento padrão do cluster Spark.  Edite o comando abaixo para refletir sua configuração.  Em seguida, da sessão SSH aberta ao cluster HBase, digite o comando:

| Valor da sintaxe | Novo valor|
|---|---|
|[Esquema de URI](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Modifique para refletir seu armazenamento.  A sintaxe abaixo é para armazenamento blob com transferência segura ativada.|
|`SPARK_STORAGE_CONTAINER`|Substitua pelo nome padrão do contêiner de armazenamento usado para o cluster Spark.|
|`SPARK_STORAGE_ACCOUNT`|Substitua pelo nome padrão da conta de armazenamento usada para o cluster Spark.|

```bash
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

Em seguida, saia de sua conexão ssh para o cluster HBase.

```bash
exit
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Colocar hbase-site.XML em seu cluster Spark

1. Conecte-se ao nó principal do cluster Spark usando o SSH. Edite o comando `SPARKCLUSTER` abaixo substituindo com o nome do seu cluster Spark e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

2. Digite o comando `hbase-site.xml` abaixo para copiar do armazenamento padrão do cluster Spark para a pasta de configuração Spark 2 no armazenamento local do cluster:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Execute o Shell de Spark referenciando o conector HBase Spark

1. Da sessão SSH aberta ao cluster Spark, digite o comando abaixo para iniciar uma concha de faísca:

    ```bash
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. Mantenha essa instância do Shell do Spark aberta e continue para a próxima etapa.

## <a name="define-a-catalog-and-query"></a>Definir um catálogo e consulta

Nesta etapa, você deve definir um catálogo que mapeia o esquema do Apache Spark para Apache HBase.  

1. Em sua Spark Shell aberta, digite as seguintes `import` instruções:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Digite o comando abaixo para definir um catálogo para a tabela Contatos que você criou no HBase:

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    O código faz o seguinte:  

     a. Definir um esquema de catálogo para a tabela do HBase chamada `Contacts`.  
     b. Identificar a rowkey como `key` e mapear os nomes de coluna usados no Spark para a família de coluna, o nome da coluna e o tipo de coluna como usado no HBase.  
     c. A rowkey também deve ser definida em detalhes como uma coluna nomeada (`rowkey`), que tem uma família de coluna específico `cf` de `rowkey`.  

1. Digite o comando abaixo para definir um `Contacts` método que forneça um DataFrame em torno de sua tabela no HBase:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. Crie uma instância do DataFrame:

    ```scala
    val df = withCatalog(catalog)
    ```  

1. Consulte o DataFrame:

    ```scala
    df.show()
    ```

    Você deve ver duas linhas de dados:

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. Registre uma tabela temporária para que você possa consultar a tabela do HBase usando Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

1. Emitir uma consulta SQL em relação a `contacts` tabela:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    Você deve ver os resultados como estes:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Inserir nova linha

1. Para inserir um novo registro de contato, defina uma `ContactRecord` classe:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. Crie uma instância de `ContactRecord` e colocá-la em uma matriz:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Salve a matriz de novos dados em HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. Examine os resultados:

    ```scala  
    df.show()
    ```

    Você deve ver uma saída como a abaixo:

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. Feche a concha de ignição digitando o seguinte comando:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Próximas etapas

* [Conector Apache Spark HBase](https://github.com/hortonworks-spark/shc)
