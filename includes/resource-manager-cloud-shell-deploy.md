---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: aac2f3ea2b52ac0319f96279deed13c1145749bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74451401"
---
## <a name="deploy-template-from-cloud-shell"></a>Implantar o modelo do Cloud Shell

Você pode usar o [Cloud Shell](../articles/cloud-shell/overview.md) para implantar o modelo. Para implantar um modelo externo, forneça o URI do modelo, exatamente como você faria para qualquer implantação externa. Para implantar um modelo local, você deve carregar o modelo primeiro para a conta de armazenamento do seu Cloud Shell. Esta seção descreve como carregar o modelo na sua conta do Cloud Shell e implantá-lo como um arquivo local. Caso ainda não tenha usado o Cloud Shell, confira [Visão geral do Azure Cloud Shell](../articles/cloud-shell/overview.md) para saber mais sobre como configurá-lo.

1. Faça login no [portal Azure](https://portal.azure.com).

1. Selecione o grupo de recursos do Cloud Shell. O nome padrão é `cloud-shell-storage-<region>`.

   ![Escolha o grupo de recursos](./media/resource-manager-cloud-shell-deploy/select-cloud-shell-resource-group.png)

1. Selecione a conta de armazenamento do Cloud Shell.

   ![Escolher conta de armazenamento](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Selecione **Blobs**.

   ![Selecionar blobs](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Selecionar **+ Contêiner**.

   ![Adicionar contêiner](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Dê um nome e um nível de acesso a seu contêiner. O modelo de exemplo neste artigo não contém nenhuma informação confidencial, portanto o acesso de leitura anônimo é permitido. Selecione **OK**.

   ![Fornecer valores de contêiner](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Selecione o contêiner criado.

   ![Adicionar um novo contêiner](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Selecione **Carregar**.

   ![Carregar blob](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Localizar e carregar o modelo.

   ![Carregar arquivo](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Depois que ele foi carregado, selecione o modelo.

   ![Selecionar novo modelo](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Copie a URL.

   ![Copiar URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Abra o prompt.

   ![Abrir Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
