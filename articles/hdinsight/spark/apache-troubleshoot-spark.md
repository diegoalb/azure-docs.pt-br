---
title: Solução de problemas Apache Spark no Azure HDInsight
description: Obtenha respostas para perguntas comuns sobre como trabalhar com o Apache Spark e o Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 80bca2dab1d07d9b99e75e283068bff99335fa18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271935"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Solucionar problemas do Apache Spark usando o Azure HDInsight

Conheça os principais problemas e suas resoluções ao trabalhar com cargas Apache Spark em [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Como fazer para configurar um aplicativo Apache Spark usando o Ambari nos clusters?

Os valores de configuração de `OutofMemoryError` faísca podem ser ajustados para evitar uma exceção do aplicativo Apache Spark. As etapas a seguir mostram valores de configuração padrão do Spark no Azure HDInsight:

1. Faça login no Ambari `https://CLUSTERNAME.azurehdidnsight.net` com suas credenciais de cluster. A tela inicial exibe um painel de visão geral. Há pequenas diferenças cosméticas entre o HDInsight 3.6 e o 4.0.

1. Navegue até **Spark2** > **Configs**.

    ![Selecione a guia Configurações](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. Na lista de configurações, selecione e **expanda os padrões personalizados de spark2**.

1. Procure a configuração do valor que você precisa ajustar, como **spark.executor.memory**. Neste caso, o valor de **9728m** é muito alto.

    ![Selecione custom-spark-defaults](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Defina o valor para a configuração recomendada. O valor de **2048m** é recomendado para essa configuração.

1. Salve o valor e, em seguida, salve a configuração. Selecione **Salvar**.

    ![Altere o valor para 2048m](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Escreva uma observação sobre as alterações de configuração e selecione **Salvar**.

    ![Insira uma observação sobre as alterações feitas](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    Você será notificado se todas as configurações precisarem de atenção. Observe os itens e, em seguida, selecione **Continuar Assim Mesmo**.

    ![Selecione Continuar Assim Mesmo](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. Sempre que uma configuração é salva, você é solicitado a reiniciar o serviço. Selecione **Reiniciar**.

    ![Selecione reiniciar](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Confirme a reinicialização.

    ![Selecione Confirmar Reiniciar Tudo](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    Você pode examinar os processos em execução.

    ![Examinar processos em execução](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. Você pode adicionar configurações. Na lista de configurações, selecione **Custom-spark2-defaults** e, em seguida, selecione **Adicionar Propriedade**.

    ![Selecione adicionar propriedade](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Defina uma nova propriedade. Você pode definir uma única propriedade usando uma caixa de diálogo para configurações específicas, como o tipo de dados. Ou você pode definir várias propriedades usando uma definição por linha.

    Neste exemplo, a propriedade **spark.driver.memory** é definida com um valor de **4g**.

    ![Definir nova propriedade](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. Salve a configuração e, em seguida, reinicie o serviço conforme descrito nas etapas 6 e 7.

Essas alterações valem para todo o cluster, mas podem ser substituídas quando você enviar o trabalho do Spark.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Como fazer para configurar um aplicativo Apache Spark usando um Jupyter Notebook nos clusters?

Na primeira célula do bloco de anotações do Jupyter, após a diretiva **%%configure**, especifique as configurações do Spark em um formato JSON válido. Altere os valores reais conforme necessário:

![Adicionar uma configuração](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Como fazer para configurar um aplicativo Apache Spark usando o Apache Livy nos clusters?

Envie o aplicativo Spark ao Livy usando um cliente REST, como cURL. Use um comando semelhante ao seguinte. Altere os valores reais conforme necessário:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Como fazer para configurar um aplicativo Apache Spark usando o envio spark nos clusters?

Inicie o shell do Spark usando um comando semelhante ao seguinte. Altere o valor real das configurações conforme necessário:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Leitura adicional

[Envio de trabalho do Apache Spark em clusters do HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* [Visão geral do gerenciamento de memória spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Depuração do aplicativo Spark em clusters HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
