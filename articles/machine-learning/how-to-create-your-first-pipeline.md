---
title: Criar, executar e controlar pipelines do ML
titleSuffix: Azure Machine Learning
description: Crie e execute um pipeline de aprendizado de máquina com o SDK do Azure Machine Learning para Python. Use os gasodutos ML para criar e gerenciar os fluxos de trabalho que costuram as fases de aprendizado de máquina (ML). Essas fases incluem preparação de dados, treinamento de modelos, implantação de modelos e inferência/pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: fa0a5bfe921687ad964e9321e3874de37ccf9b98
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549306"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Crie e execute pipelines de aprendizado de máquina com o Azure Machine Learning SDK
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a criar, publicar, executar e controlar um [pipeline de aprendizado de máquina](concept-ml-pipelines.md) usando o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Use **os pipelines ML** para criar um fluxo de trabalho que costure várias fases ml e, em seguida, publique esse pipeline no seu espaço de trabalho azure Machine Learning para acessar mais tarde ou compartilhar com outros.  Os pipelines ML são ideais para cenários de pontuação em lote, usando vários cálculos, reutilizando etapas em vez de reexecutá-los, bem como compartilhar fluxos de trabalho ML com outros.

Embora você possa usar um tipo diferente de pipeline chamado [Azure Pipeline](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) para automação de CI/CD de tarefas ML, esse tipo de pipeline nunca é armazenado dentro do seu espaço de trabalho. [Compare esses diferentes gasodutos.](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use)

Cada fase de um pipeline ML, como preparação de dados e treinamento de modelo, pode incluir uma ou mais etapas.

Os pipelines ML que você cria são visíveis para os membros do seu espaço de [trabalho](how-to-manage-workspace.md)Azure Machine Learning . 

Os gasodutos ML usam metas de computação remota para computação e o armazenamento dos dados intermediários e finais associados a esse pipeline. Eles podem ler e gravar dados para e a partir de locais de [armazenamento Azure](https://docs.microsoft.com/azure/storage/) suportados.

Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Pré-requisitos

* Crie um [Workspace do Azure Machine Learning](how-to-manage-workspace.md) para manter todos os seus recursos de pipeline.

* [Configure seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o Azure Machine Learning SDK ou use uma instância de [computação de aprendizado de máquina do Azure (visualização)](concept-compute-instance.md) com o SDK já instalado.

Comece anexando seu espaço de trabalho:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>Configurar recursos de aprendizado de máquina

Crie os recursos necessários para executar um pipeline ML:

* Configurar um repositório de dados usado para acessar os dados necessários nas etapas de pipeline.

* Configure um objeto `DataReference` para apontar para dados que residem ou podem ser acessados em um repositório de dados.

* Configure os [destinos de computação](concept-azure-machine-learning-architecture.md#compute-targets) em que suas etapas de pipeline serão executadas.

### <a name="set-up-a-datastore"></a>Configurar um repositório de dados

Um repositório de dados armazena os dados para o pipeline acessar. Cada workspace tem um repositório de dados padrão. Você pode registrar armazenamentos de dados adicionais. 

Quando você cria seu espaço de trabalho, [os arquivos Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) e [o armazenamento Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) são anexados ao espaço de trabalho. Um armazenamento de dados padrão é registrado para se conectar ao armazenamento Azure Blob. Para saber mais, consulte [Decidindo quando usar Arquivos do Azure, Blobs do Azure ou Discos do Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Carregar arquivos de dados ou diretórios para o repositório de dados para que eles possam ser acessados de seus pipelines. Este exemplo usa o armazenamento Blob como o armazenamento de dados:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

Um pipeline consiste em uma ou mais etapas. Uma etapa é uma unidade executada em um destino de computação. As etapas podem consumir fontes de dados e produzir dados "intermediários". Uma etapa pode criar dados como um modelo, um diretório com o modelo e arquivos dependentes ou dados temporários. Esses dados então ficam disponíveis para outras etapas posteriores no pipeline.

Para saber mais sobre como conectar seu pipeline aos seus dados, consulte os artigos [Como acessar dados](how-to-access-data.md) e como registrar [conjuntos de dados](how-to-create-register-datasets.md). 

### <a name="configure-data-reference"></a>Configurar referência de dados

Você acabou de criar uma fonte de dados que pode ser referenciada em um pipeline como entrada para uma etapa. Uma fonte de dados em um pipeline é representada por um objeto [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference). O objeto `DataReference` aponta para dados que residem ou podem ser acessados de um repositório de dados.

```python
from azureml.data.data_reference import DataReference

blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Dados intermediários (ou a saída de uma etapa) são representados por um objeto [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py). `output_data1` é produzido como a saída de uma etapa e usado como a entrada de um ou mais etapas futuras. O `PipelineData` introduz uma dependência de dados entre etapas e cria uma ordem de execução implícita no pipeline. Este objeto será usado mais tarde ao criar etapas do pipeline.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="configure-data-using-datasets"></a>Configurar dados usando conjuntos de dados

Se você tiver dados tabulares armazenados em um arquivo ou conjunto de `DataReference`arquivos, um [Conjunto TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) é uma alternativa eficiente a a . `TabularDataset`objetos suportam versões, difusores e estatísticas de resumo. `TabularDataset`s são avaliados preguiçosamente (como geradores Python) e é eficiente subdefini-los dividindo ou filtrando. A `FileDataset` classe fornece dados preguiçosas semelhantes representando um ou mais arquivos. 

Você cria `TabularDataset` um uso de métodos como [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-).

```python
from azureml.data import TabularDataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/tabular/iris.csv')])
```

 Você cria `FileDataset` um from_files de [uso](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-).

 Você pode aprender mais sobre como trabalhar com conjuntos de dados do [Add & registrar conjuntos de dados](how-to-create-register-datasets.md) ou este caderno de [amostras](https://aka.ms/train-datasets).

## <a name="set-up-compute-target"></a>Configurar destino de computação

No Azure Machine Learning, o termo __computação__ (ou __destino computacional)__ refere-se às máquinas ou clusters que executam as etapas computacionais em seu pipeline de aprendizado de máquina.   Veja [destinos de computação para treinamento do modelo](how-to-set-up-training-targets.md) para obter uma lista completa de destinos de computação e como criá-los e anexá-los ao seu workspace.  O processo para criar e/ou anexar um destino de computação é o mesmo, não importa se você está treinando um modelo ou executando uma etapa do pipeline. Depois de criar e anexar seu destino de computação, use o objeto `ComputeTarget` em na [etapa do pipeline](#steps).

> [!IMPORTANT]
> Não há suporte para operações de gerenciamento em destinos de computação de dentro de trabalhos remotos. Como os pipelines de aprendizado de máquina são enviados como um trabalho remoto, não use operações de gerenciamento em destinos de computação de dentro do pipeline.

Abaixo estão exemplos de como criar e anexar destinos de computação para:

* Computação do Azure Machine Learning
* Azure Databricks 
* Análise Azure Data Lake

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning

Você pode criar uma computação do Azure Machine Learning para executar suas etapas.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

As Azure Databricks são um ambiente baseado em Apache Spark na nuvem do Azure. Ele pode ser usado como um destino de computação com um pipeline do Azure Machine Learning.

Crie um workspace do Azure Databricks antes de usá-lo. Para criar um recurso de espaço de trabalho, consulte o trabalho Executar uma faísca no documento [Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

Para anexar o Azure Databricks como um destino de computação, forneça as seguintes informações:

* __Nome de cálculo de databricks__: O nome que você deseja atribuir a este recurso de computação.
* __Nome do espaço de trabalho databricks__: O nome do espaço de trabalho Azure Databricks.
* __Databricks access token__: O token de acesso usado para autenticar a Azure Databricks. Para gerar um token de acesso, consulte o documento [Autenticação](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html).

O código a seguir demonstra como anexar os Databricks do Azure como um alvo de computação com o Azure Machine Learning SDK (__O espaço de trabalho Databricks precisa estar presente na mesma assinatura do seu espaço de trabalho AML__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Para um exemplo mais detalhado, consulte um [notebook de exemplo](https://aka.ms/pl-databricks) no GitHub.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

O Azure Data Lake Analytics é uma plataforma de análise de big data na nuvem do Azure. Ele pode ser usado como um destino de computação com um pipeline do Azure Machine Learning.

Crie uma conta do Azure Data Lake Analytics antes de usá-lo. Para criar este recurso, consulte o documento [Introdução ao Google Analytics do Azure Data Lake](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Para anexar o Data Lake Analytics como um destino de computação, você deve usar o SDK do Aprendizado de Máquina do Azure e fornecer as seguintes informações:

* __Nome da computa__: o nome que você deseja atribuir a este recurso de computação.
* __Grupo de recursos__: O grupo de recursos que contém a conta Data Lake Analytics.
* __Nome da conta__: O nome da conta Data Lake Analytics.

O código a seguir demonstra como anexar o Data Lake Analytics como um destino de computação:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Para um exemplo mais detalhado, consulte um [notebook de exemplo](https://aka.ms/pl-adla) no GitHub.

> [!TIP]
> Os pipelines do Azure Machine Learning só podem funcionar com dados armazenados no armazenamento de dados padrão da conta do Data Lake Analytics. Se os dados com os que você precisa trabalhar estão [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) em uma loja não padrão, você pode usar um para copiar os dados antes do treinamento.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Construir suas etapas de pipeline

Depois de criar e anexar um destino de computação ao workspace, você está pronto para definir uma etapa do pipeline. Há muitas etapas internas disponíveis por meio do SDK do Azure Machine Learning. A mais básica dessas etapas é um [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), que executa um script Python em um alvo de computação especificado:

```python
from azureml.pipeline.steps import PythonScriptStep

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", output_data1],
    inputs=[blob_input_data],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

A reutilização de`allow_reuse`resultados anteriores ( ) é fundamental ao usar pipelines em um ambiente colaborativo, pois a eliminação de reprises desnecessárias oferece agilidade. Reutilização é o comportamento padrão quando o script_name, entradas e os parâmetros de um passo permanecem os mesmos. Quando a saída da etapa é reutilizada, o trabalho não é submetido ao cálculo, em vez disso, os resultados da execução anterior estão imediatamente disponíveis para a execução da próxima etapa. Se `allow_reuse` for definido como falso, uma nova execução será sempre gerada para esta etapa durante a execução do pipeline. 

Depois de definir suas etapas, você pode criar o pipeline usando algumas ou todas essas etapas.

> [!NOTE]
> Nenhum arquivo ou dados é carregado no Azure Machine Learning quando você define as etapas ou constrói o pipeline.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

O exemplo a seguir usa o destino de computação do Azure Databricks criado anteriormente: 

```python
from azureml.pipeline.steps import DatabricksStep

dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

### <a name="use-a-dataset"></a>Use um conjunto de dados 

Para usar `TabularDataset` um `FileDataset` ou em seu pipeline, você precisa transformá-lo em um objeto [DatasetConsumptionConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_consumption_config.datasetconsumptionconfig?view=azure-ml-py) chamando [as_named_input(nome)](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#as-named-input-name-). Você passa `DatasetConsumptionConfig` este objeto `inputs` como um dos passos para o seu oleoduto. 

Conjuntos de dados criados a partir do armazenamento Azure Blob, Arquivos Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database e Azure Database para PostgreSQL podem ser usados como entrada para qualquer etapa do pipeline. Com exceção da saída de gravação para um [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) ou [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py), os dados de saída[(PipelineData)](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)só podem ser gravados para os armazenamentos de dados de compartilhamento de arquivos Azure Blob e Azure.

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Em seguida, você recupera o conjunto de dados em seu pipeline usando o dicionário [Run.input_datasets.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets)

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Para obter mais informações, consulte o [pacote azure-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) e a referência [da classe Pipeline.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py)

## <a name="submit-the-pipeline"></a>Enviar o pipeline

Quando você envia o pipeline, o Azure Machine Learning verifica as dependências de cada etapa e faz upload de um instantâneo do diretório de origem especificado. Se nenhum diretório de origem for especificado, o diretório local atual será carregado. O instantâneo também é armazenado como parte do experimento em seu espaço de trabalho.

> [!IMPORTANT]
> Para evitar que os arquivos sejam incluídos no `.amlignore` snapshot, crie um [arquivo .gitignore](https://git-scm.com/docs/gitignore) ou no diretório e adicione os arquivos a ele. O `.amlignore` arquivo usa a mesma sintaxe e padrões do arquivo [.gitignore.](https://git-scm.com/docs/gitignore) Se ambos os `.amlignore` arquivos existirem, o arquivo tem precedência.
>
> Para obter mais informações, consulte [Snapshots](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Quando você executar um pipeline pela primeira vez, o Azure Machine Learning:

* Baixa o instantâneo do projeto para o destino de computação do armazenamento de Blobs associado ao workspace.
* Cria uma imagem do Docker correspondente a cada etapa no pipeline.
* Baixa a imagem docker para cada passo para o destino de computação do registro de contêiner.
* Monta o datastore `DataReference` se um objeto for especificado em uma etapa. Se não houver suporte para montagem, os dados serão copiados para o destino de computação.
* Executa a etapa no destino de computação especificado na definição da etapa. 
* Cria artefatos como logs, stdout e stderr, métricas e saída especificados pela etapa. Esses artefatos são então carregados e mantidos no armazenamento de dados padrão do usuário.

![Diagrama de execução de um experimento como um pipeline](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Para obter mais informações, consulte a referência da [classe Experiment.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)

### <a name="view-results-of-a-pipeline"></a>Ver resultados de um pipeline

Veja a lista de todos os seus pipelines e seus detalhes de execução no estúdio:

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com).

1. [Veja seu espaço de trabalho](how-to-manage-workspace.md#view).

1. À esquerda, selecione **Pipelines** para ver todas as suas operações de pipeline.
 ![lista de pipelines do aprendizado de máquina](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Selecione um pipeline específico para ver os resultados da execução.

## <a name="git-tracking-and-integration"></a>Rastreamento e integração do Git

Quando você inicia uma corrida de treinamento onde o diretório de origem é um repositório git local, as informações sobre o repositório são armazenadas no histórico de execução. Para obter mais informações, consulte [a integração Git para Aprendizado de Máquina do Azure](concept-train-model-git-integration.md).

## <a name="publish-a-pipeline"></a>Publicar um pipeline

Você pode publicar um pipeline para executá-lo com entradas diferentes mais tarde. Para o ponto de extremidade REST de um pipeline já publicado aceitar parâmetros, você deve parametrizar o pipeline antes da publicação.

1. Para criar um parâmetro de pipeline, use um objeto [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) com um valor padrão.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Adicione esse objeto `PipelineParameter` como um parâmetro a qualquer uma das etapas no pipeline da seguinte maneira:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Publique este pipeline que aceitará um parâmetro quando invocado.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Execute um pipeline publicado

Todos os pipelines publicados têm um ponto de extremidade REST. Esse ponto de extremidade REST invoca a execução do pipeline de sistemas externos, como clientes não Python. Esse ponto de extremidade permite a "repetibilidade gerenciada" em cenários de retreinamento e pontuação de lote.

Para invocar a execução do pipeline anterior, você precisa de um token de cabeçalho de autenticação do Azure Active Directory, conforme descrito na referência [da classe AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) ou obter mais detalhes no notebook [Authentication in Azure Machine Learning.](https://aka.ms/pl-restep-auth)

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Criar um ponto final de pipeline versionado
Você pode criar um Pipeline Endpoint com vários pipelines publicados por trás dele. Isso pode ser usado como um pipeline publicado, mas lhe dá um ponto final REST fixo à medida que você itera e atualiza seus pipelines ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Submeta um trabalho a um ponto final do gasoduto
Você pode enviar um trabalho para a versão padrão de um ponto final do pipeline:
```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```
Você também pode enviar um trabalho para uma versão específica:
```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

O mesmo pode ser realizado usando a API REST:
```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Use pipelines publicados no estúdio

Você também pode executar um pipeline publicado do estúdio:

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com).

1. [Veja seu espaço de trabalho](how-to-manage-workspace.md#view).

1. À esquerda, selecione **Endpoints**.

1. Na parte superior, selecione **pontos finais do Pipeline**.
 ![lista de aprendizado de máquina publicado pipelines](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Selecione um pipeline específico para executar, consumir ou revisar os resultados das corridas anteriores do ponto final do pipeline.


### <a name="disable-a-published-pipeline"></a>Desativar um pipeline publicado

Para ocultar um pipeline da sua lista de pipelines publicados, desabilitá-lo, seja no estúdio ou no SDK:

```
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Você pode habilitá-lo novamente com `p.enable()`. Para obter mais informações, consulte a referência [da classe PublishedPipeline.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py)


## <a name="caching--reuse"></a>Reutilização de & caching  

A fim de otimizar e personalizar o comportamento de seus pipelines, você pode fazer algumas coisas em torno de cache e reutilização. Por exemplo, você pode escolher:
+ **Desative a reutilização padrão da saída de execução de etapas,** definindo `allow_reuse=False` durante a definição da [etapa](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). A reutilização é fundamental ao usar pipelines em um ambiente colaborativo, pois eliminar corridas desnecessárias oferece agilidade. No entanto, você pode optar por não reutilizar.
+ **Regeneração da saída de força para todas as etapas em uma corrida** com`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Por padrão, `allow_reuse` para etapas `source_directory` está ativado e o especificado na definição de etapa é hashed. Assim, se o script para uma determinada`script_name`etapa permanece o mesmo (, entradas e os parâmetros), e nada mais no` source_directory` foi alterado, a saída de uma execução de etapa anterior é reutilizada, o trabalho não é submetido ao cálculo, e os resultados da execução anterior estão imediatamente disponíveis para o próximo passo em vez disso.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>Próximas etapas

- Use [esses Jupyter Notebooks no GitHub](https://aka.ms/aml-pipeline-readme) para explorar ainda mais pipelines de machine learning.
- Consulte a ajuda de referência do SDK para o pacote [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e o pacote [azureml-pipelines-steps.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)
- Consulte o ["how-to"](how-to-debug-pipelines.md) para obter dicas sobre depuração e solução de problemas.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
