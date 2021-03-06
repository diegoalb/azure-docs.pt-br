---
title: Ajuste de desempenho do Azure Data Lake Storage Gen1 - MapReduce
description: Diretrizes de Ajuste de Desempenho do Azure Data Lake Storage Gen1 MapReduce
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: a645049665bc1d51efa94a879b9d2e4e5529282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904581"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Orientação de ajuste de desempenho para o MapReduce no HDInsight e no Armazenamento de Data Lake do Azure Gen1

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure.** Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Storage Gen1**. Para obter instruções sobre como criar um, consulte [Comece com o Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** com acesso a uma conta do Azure Data Lake Storage Gen1. Veja [Criar um cluster HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de habilitar a área de trabalho remota para o cluster.
* **Usando mapReduce no HDInsight**. Para obter mais informações, consulte [Usar o MapReduce no Hadoop no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Revise as diretrizes de ajuste de desempenho para Data Lake Storage Gen1**. Para conceitos gerais de desempenho, consulte [Data Lake Storage Gen1 Performance Tuning Guidance](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parâmetros

Ao executar trabalhos do MapReduce, aqui estão os parâmetros mais importantes que você pode configurar para aumentar o desempenho no Data Lake armazenamento Gen1:

|Parâmetro      | Descrição  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  A quantidade de memória para alocar para cada mapeador.  |
|`Mapreduce.job.maps`     |  O número de tarefas do mapa por trabalho.  |
|`Mapreduce.reduce.memory.mb`     |  A quantidade de memória para alocar a cada redutor.  |
|`Mapreduce.job.reduces`    |   O número de tarefas de redução por trabalho.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>Mapreduce.map.memory / Mapreduce.reduce.memory

Ajuste esse número com base na quantidade de memória necessária para o mapa e/ou reduza a tarefa. Você pode visualizar os `mapreduce.map.memory` `mapreduce.reduce.memory` valores padrão de e em Ambari através da configuração Fio. Em Ambari, navegue até O Fio e visualize a guia **Configs.** A memória YARN será exibida.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>Mapreduce.job.maps / Mapreduce.job.reduces

Isso determina o número máximo de mapeadores ou redutores a serem criados. O número de splits determina quantos mappers são criados para o trabalho MapReduce. Portanto, você pode obter menos mapeadores do que você pediu se houver menos divisões do que o número de mapeadores solicitados.

## <a name="guidance"></a>Orientação

### <a name="step-1-determine-number-of-jobs-running"></a>Passo 1: Determine o número de empregos em execução

Por padrão, o MapReduce usará o cluster inteiro para o trabalho. Você pode usar menos do cluster usando menos mapeadores do que há recipientes disponíveis. As diretrizes neste documento pressupõem que seu aplicativo é o único aplicativo em execução no cluster.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.memory

O tamanho da memória para tarefas de mapeamento e redução dependerá do trabalho específico. Caso deseje aumentar a simultaneidade, você poderá reduzir o tamanho da memória. O número de tarefas em execução simultânea depende do número de contêineres. Diminuindo a quantidade de memória por mapeador ou redutor, mais contêineres podem ser criados, o que permite a execução simultânea de mais mapeadores ou redutores. Diminuir muito a quantidade de memória pode fazer com que alguns processos fiquem sem memória. Se você tiver um erro de pilha ao executar seu trabalho, aumente a memória por mapeador ou redutor. Considere que adicionar mais contêineres adiciona sobrecarga extra para cada contêiner adicional, o que pode potencialmente degradar o desempenho. Outra alternativa é de obter mais memória pelo uso de um cluster com maiores quantidades de memória ou pelo aumento do número de nós no cluster. Mais memória permitirá o uso de mais contêineres, o que significa mais simultaneidade.

### <a name="step-3-determine-total-yarn-memory"></a>Passo 3: Determine a memória completa do YARN

Para ajustar mapreduce.job.maps/mapreduce.job.reduces, considere a quantidade de memória Total de YARN disponível para uso. Essas informações estão disponíveis no Ambari. Navegue até O Fio e visualize a guia **Configs.** A memória YARN é exibida nesta janela. Multiplique a memória YARN com o número de nós no cluster para obter a memória Completa do YARN.

`Total YARN memory = nodes * YARN memory per node`

Se você estiver usando um cluster vazio, então a memória pode ser a memória Completa Do YARN para o seu cluster. Se outros aplicativos estiverem usando memória, você poderá usar apenas uma parte da memória do cluster, reduzindo o número de mapeadores ou redutores para o número de contêineres que você deseja usar.

### <a name="step-4-calculate-number-of-yarn-containers"></a>Passo 4: Calcule o número de recipientes De Fio

Contêineres YARN determinam a quantidade de simultaneidade disponível para o trabalho. Pegar a memória YARN total e divida-a por mapreduce.map.memory.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Etapa 5: definir mapreduce.job.maps/mapreduce.job.reduces

Defina mapreduce.job.maps/mapreduce.job.reduces para, no mínimo, o número de contêineres disponíveis. Você pode experimentar ainda mais aumentando o número de mapeadores e redutores para ver se obtém um melhor desempenho. Tenha em mente que mais mapeadores terão uma sobrecarga adicional, então ter um número excessivo de mapeadores pode degradar o desempenho.

O isolamento de CPU e agendamento de CPU são desligados por padrão para que o número de contêineres YARN seja restrito pela memória.

## <a name="example-calculation"></a>Exemplo de cálculo

Suponhamos que você tenha um cluster composto de oito nós D14 e deseje executar um trabalho com uso intensivo de E/S. Aqui estão os cálculos que você deve fazer:

### <a name="step-1-determine-number-of-jobs-running"></a>Passo 1: Determine o número de empregos em execução

Por exemplo, assumimos que nosso trabalho é o único em execução.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Passo 2: Definir mapreduce.map.memory/mapreduce.reduce.memory

Para nosso exemplo, você está executando um trabalho intensivo de I/O e decidir que 3 GB de memória para tarefas de mapa é suficiente.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>Passo 3: Determine a memória completa do YARN

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>Etapa 4: calcular o número de contêineres YARN

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Etapa 5: definir mapreduce.job.maps/mapreduce.job.reduces

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Limitações

**Limitação do data Lake armazenamento Gen1**

Como um serviço multilocatário, o Data Lake Storage Gen1 define limites de largura de banda de nível de conta. Se você atingir esses limites, começará a ver falhas de tarefas. Isso pode ser identificado observando os erros de limitação nos logs de tarefa. Se precisar de mais largura de banda para seu trabalho, entre em contato conosco.

Para verificar se há problemas de limitação, você precisa habilitar o log de depuração no lado do cliente. Veja como fazer isso:

1. Coloque a seguinte propriedade nas propriedades de log4j em Ambari > YARN > Configuração > Avançado yarn-log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Reinicie todos os nós/o serviço para que a configuração entre em vigor.

3. Se você estiver sendo estrangulado, você verá o código de erro HTTP 429 no arquivo de log do YARN. O arquivo de log do YARN está em /tmp/&lt;usuário&gt;/yarn.log

## <a name="examples-to-run"></a>Exemplos para execução

Para demonstrar como o MapReduce é executado no Data Lake Storage Gen1, a seguir está um código de exemplo que foi executado em um cluster com as seguintes configurações:

* 16 nós D14v2
* Cluster Hadoop executando HDI 3.6

Para um ponto de partida, aqui estão alguns comandos de exemplo para executar o MapReduce Teragen, Terasort e Teravalidate. Você pode ajustar esses comandos com base em seus recursos.

### <a name="teragen"></a>Teragen

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalid

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```
