---
title: Utilizar a referência de modelo
description: Utilizar a referência de modelo do Azure Resource Manager para criar um modelo.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: b742982121a20a2b057eba4211584b0386dde411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373172"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>Tutorial: Utilizar a referência de modelo do ARM

Saiba como localizar as informações de esquema de modelo e usar as informações para criar modelos do ARM (Azure Resource Manager).

Neste tutorial, você usará um modelo de base dos modelos de Início Rápido do Azure. Usando a documentação de referência de modelo, você personaliza o modelo.

![Conta de armazenamento de implantação da referência de modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Entender o modelo
> * Encontrar a referência de modelo
> * Editar o modelo
> * Implantar o modelo

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* Visual Studio Code com a extensão de Ferramentas do Resource Manager. Confira [Usar o Visual Studio Code para criar modelos do ARM](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

[Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/) é um repositório de modelos do ARM. Em vez de criar um modelo do zero, você pode encontrar um exemplo de modelo e personalizá-lo. O modelo usado neste início rápido é chamado [Criar uma conta de armazenamento padrão](https://azure.microsoft.com/resources/templates/101-storage-account-create/). O modelo define um recurso da conta de Armazenamento do Azure.

1. No Visual Studio Code, escolha **Arquivo**>**Abrir Arquivo**.
1. Em **Nome do arquivo**, cole a seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Escolha **Abrir** para abrir o arquivo.
1. Escolha **Arquivo**>**Salvar como** para salvar o arquivo como **azuredeploy.json** em seu computador local.

## <a name="understand-the-schema"></a>Entenda o esquema

1. No VS Code, recolha o modelo até o nível raiz. Você tem uma estrutura mais simples com os seguintes elementos:

    ![Estrutura mais simples do modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * **$schema**: especifique o local do arquivo de esquema JSON que descreve a versão da linguagem do modelo.
    * **contentVersion**: especifique algum valor para esse elemento a fim de documentar alterações significativas no modelo.
    * **parameters**: especifique os valores que são fornecidos quando a implantação é executada para personalizar a implantação dos recursos.
    * **variables**: especifique os valores que são usados como fragmentos JSON no modelo para simplificar expressões de linguagem do modelo.
    * **resources**: especifique os tipos de recursos que são implantados ou atualizados em um grupo de recursos.
    * **gera**: especifique os valores que serão retornados após a implantação.

1. Expanda os **recursos**. Há um recurso `Microsoft.Storage/storageAccounts` definido.

    ![Definição da conta de armazenamento do modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

## <a name="find-the-template-reference"></a>Encontrar a referência de modelo

1. Navegue até [Referência de modelo do Azure](https://docs.microsoft.com/azure/templates/).
1. Na caixa **Filtrar por título**, insira **contas de armazenamento** e selecione a primeira **Conta de Armazenamento** em **Referência > Armazenamento**.

    ![Conta de armazenamento de referência de modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Um provedor de recursos geralmente tem várias versões de API:

    ![Versões da conta de armazenamento de referência de modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. Selecione **Todos os recursos** em **Armazenamento** no painel esquerdo. Esta página lista os tipos de recursos e as versões do provedor de recursos de armazenamento. É recomendável usar as versões de API mais recentes para os tipos de recursos definidos em seu modelo.

    ![Versões de tipos de conta de armazenamento de referência do modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Selecione a versão mais recente do tipo de recurso **storageAccount**.  A versão mais recente era **2019-06-01** quando este artigo foi escrito.

1. Esta página lista os detalhes do tipo de recurso storageAccount.  Por exemplo, lista os valores permitidos para o objeto **Sku**. Há mais SKUs do que as listadas no modelo de início rápido que você abriu anteriormente. Você pode personalizar o modelo de início rápido para incluir todos os tipos de armazenamento disponíveis.

    ![SKUs da conta de armazenamento de referência de modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>Editar o modelo

No Visual Studio Code, adicione os tipos de conta de armazenamento extras, conforme mostra a seguinte captura de tela:

![Recursos da conta de armazenamento do modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>Implantar o modelo

Consulte a seção [Implantar o modelo](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) do guia de início rápido do Visual Studio Code para o procedimento de implantação. Ao implantar o modelo, especifique o parâmetro **storageAccountType** com um valor recém-adicionado, por exemplo, **Premium_ZRS**. A implantação falhará se você usar o modelo de início rápido original porque **Premium_ZRS** não era um valor permitido.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.  Você deverá ver um total de seis recursos no grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar a referência de modelo para personalizar um modelo existente. Para saber como criar várias instâncias da conta de armazenamento, veja:

> [!div class="nextstepaction"]
> [Criar múltiplas instâncias](./template-tutorial-create-multiple-instances.md)
