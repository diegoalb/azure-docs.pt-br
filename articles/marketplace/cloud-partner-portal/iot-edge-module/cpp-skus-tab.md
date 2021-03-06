---
title: Módulo Azure IoT Edge SKUs | Mercado Azure
description: Criar SKUs para um módulo do IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: a989113c80cd8189cce9da959fe27509297f677a
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745691"
---
# <a name="iot-edge-module-skus-tab"></a>Guia SKUs do módulo do IoT Edge

>[!Important]
>A partir de 30 de março de 2020, começaremos a mover o gerenciamento das ofertas do seu módulo IoT Edge para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [Criar uma oferta de módulo IoT Edge](https://aka.ms/AzureCreateIoT) para gerenciar suas ofertas migradas.

A guia **SKUs** da página **Nova Oferta** permite criar uma ou mais SKUs e associá-las à sua nova oferta.  É possível usar diferentes SKUs para diferenciar uma solução por conjuntos de recursos, modelos de cobrança ou alguma outra característica.


## <a name="sku-settings"></a>Configurações de SKU

Quando você começa a criar uma nova oferta, não há SKUs associados à oferta. Para criar uma nova SKU, siga estas etapas:

- Na página **Módulos do IoT Edge > Nova Oferta**, selecione a guia **SKUs**.
- Em SKUs, selecione **+ Nova SKU** para abrir uma caixa de diálogo.

  ![Novo botão SKU na guia Nova Oferta para os módulos do IoT Edge](./media/iot-edge-module-skus-tab-new-sku.png)

- Na caixa de diálogo **Nova SKU**, insira um identificador para a SKU e, em seguida, selecione **OK**.
(A tabela a seguir fornece as convenções de nomenclatura do identificador.)

A guia **SKUs** é atualizada e exibe os campos editados para configurar a SKU. Um asterisco (*) anexado ao nome do campo indica que é obrigatório.

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **Id do SKU\***       | Identificador dessa SKU. Esse nome tem no máximo 50 caracteres, consistindo de caracteres alfanuméricos minúsculos ou traços (-), mas não pode terminar com um traço. **Observação:** não será possível alterar esse nome depois que a oferta for publicada. O nome é publicamente visível nas URLs do produto. |
|  |  |


## <a name="sku-details"></a>Detalhes da SKU

Configure os **Detalhes da SKU** para definir como a SKU será exibida nos sites do Azure Marketplace e portal do Azure.

![Metadados de sku do módulo do IoT Edge](media/iot-edge-module-skus-tab-metadata.png)

A tabela a seguir descreve a finalidade, o conteúdo e a formatação dos campos em **Detalhes da SKU**. Os campos obrigatórios são indicados por um asterisco (*).

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **Título\***        | Título dessa SKU. Comprimento máximo de 50 caracteres. <br/> Ele será mostrado no portal do Azure e utilizado como um nome de módulo padrão (sem espaços e caracteres especiais) quando for implantado. Observe as imagens abaixo para ver exatamente onde esse campo é exibido.|
| **Resumo\***      | Breve resumo desta SKU. Comprimento máximo de 100 caracteres. **NÃO** resuma a oferta, apenas a SKU.  Este resumo será mostrado no Azure Marketplace. Observe as imagens abaixo para ver exatamente onde esse campo é exibido.|
| **Descrição\***  | Breve descrição desta SKU. Comprimento máximo de 3000 caracteres. NÃO descreva a oferta, mas apenas esta SKU. Ela será mostrada no Marketplace e no portal do Azure. No portal Azure, ele será anexado à Descrição do Marketplace descrevendo a oferta definida na guia Marketplace.  Pode ser o mesmo que o Resumo SKU. Observe as imagens abaixo para ver exatamente onde esse campo é exibido.|
| **Esconda este SKU\*** | Mantenha a configuração padrão, que é **Não**. |
|  |  |


### <a name="sku-example"></a>Exemplo de SKU

 Os exemplos a seguir mostram como os campos **Título**, **Resumo** e **Descrição** de SKU aparecem em diferentes exibições.
 

#### <a name="on-the-azure-marketplace-website"></a>No site do Azure Marketplace:

- Ao analisar os detalhes da SKU:

    ![Como as SKUs são exibidas no site do Azure Marketplace](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>No site do portal do Azure:

- Ao navegar por SKUs:

    ![Como o módulo IoT Edge é exibido ao navegar no portal #1 do Azure](media/iot-edge-module-portal-browse.png)

    ![Como o módulo IoT Edge é exibido durante a navegação no portal #2 do Azure](media/iot-edge-module-portal-product-picker.png)

- Ao pesquisar SKUs:

    ![Como o módulo IoT Edge é exibido ao pesquisar o portal do Azure](media/iot-edge-module-portal-search.png)

- Ao analisar os detalhes da SKU:

    ![Como o módulo IoT Edge é exibido ao procurar os detalhes do produto no portal](./media/iot-edge-module-portal-pdp.png)

- Ao implantar o módulo:
    
    ![Como o módulo do IoT Edge é exibido ao ser implantado](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>Conteúdo da SKU

Em **Imagens do Módulo de Borda,** forneça as informações necessárias para carregar seu módulo IoT Edge.

Conceda-nos acesso ao seu [ACR (Registro de Contêiner do Azure)](https://azure.microsoft.com/services/container-registry/) (ACR) que contém a imagem do módulo do IoT Edge para que possamos enviá-lo e certificá-lo. Após publicado, o módulo do IoT Edge será copiado e distribuído usando um registro de contêiner público hospedado pelo Azure Marketplace.

É possível segmentar várias plataformas e fornecer várias versões por meio de marcas. Saiba mais sobre [marcas e controle de versão em "Preparar os ativos técnicos do módulo do IoT Edge"](./cpp-create-technical-assets.md).

![Imagens do módulo do IoT Edge](./media/iot-edge-module-skus-tab-acr.png)

A tabela a seguir descreve o propósito, o conteúdo e a formatação dos campos para as seções **Detalhes do Repositório de Imagem** e **Versão de Imagem**.  Os campos obrigatórios são indicados por um asterisco (*).


|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***Detalhes do repositório de imagens***    |
| **ID de assinatura\***        | A ID da assinatura do Azure do ACR.|
| **Nome do grupo de recursos\***      | O nome do grupo de recursos do ACR.|
| **Nome do registro\***  | Seu nome do registro ACR. Copie apenas o nome do registro, NÃO o nome do servidor de logon (por exemplo, sem o `azurecr.io`.) |
| **Nome do repositório\***  | O nome do repositório do ACR que contém o módulo do IoT Edge. **Observação:** após definir o nome, ele não poderá ser alterado posteriormente. Use um nome exclusivo para garantir que nenhuma outra oferta na sua conta tenha o mesmo nome. |
| **Nome de Usuário\*** | O nome de usuário associado ao ACR (nome de usuário do administrador). |
| **Senha\*** | A senha associada ao ACR. |
|    |  ***Versão de imagem***   |
| **Tag de imagem ou digesto\*** | Ela deve incluir pelo menos uma marca `latest` e uma marca de versão (por exemplo, começando com `xx.xx.xx-` em que xx é um número). Devem ser [marcas de manifesto](https://github.com/estesp/manifest-tool) para segmentar várias plataformas. Todas as marcas referenciadas por uma marca de manifesto também deverão ser adicionadas para que possamos enviá-las. Você pode adicionar várias versões de um módulo do IoT Edge usando marcas. Todas as marcas de manifesto (exceto `latest`) devem começar com `X.Y-` ou `X.Y.Z-` em que X, Y, Z são inteiros. Saiba mais sobre [marcas e controle de versão em "Preparar os ativos técnicos do módulo do IoT Edge"](./cpp-create-technical-assets.md). <br/> Por exemplo, se uma marca `latest` apontar para esses pontos para `1.0.1-linux-x64`, `1.0.1-linux-arm32`,  , e `1.0.1-windows-arm32`, essas 6 marcas deverão ser adicionadas aqui. |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>Ajude seus clientes a iniciar usando configurações padrão

Defina as configurações mais comuns para implantar seu módulo do IoT Edge. Otimize as implantações de clientes, permitindo que eles iniciem o módulo do IoT Edge pronto para uso com esses padrões.

![Configurações padrão do módulo do IoT Edge na implantação](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

A tabela a seguir descreve a finalidade, o conteúdo e a formatação dos campos para **Rotas padrão**, **Propriedades desejadas do gêmeo padrão**, **Variáveis de ambiente padrão** e **CreateOptions padrão**.

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **Rotas padrão**        | Cada nome e valor de rota padrão deve ter menos de 512 caracteres. É possível definir até 5 rotas padrão. Certifique-se de usar uma [sintaxe de rota](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) correta no valor da rota. Para referir-se ao módulo, use o nome do módulo padrão que será o **Título da SKU** sem espaços e caracteres especiais. Para referir-se a outros módulos ainda não conhecidos, use a convenção `<FROM_MODULE_NAME>` para que os clientes saibam que precisam atualizar essas informações. Saiba mais sobre [Rotas do IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Por exemplo, se o módulo `ContosoModule` escuta entradas em `ContosoInput` e dados de saída em `ContosoOutput`, defina as 2 rotas padrão a seguir:<br/>- Nome nº 1: `ToContosoModule`<br/>- Valor nº 1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>- Nome nº 2: `FromContosoModuleToCloud`<br/>- Valor nº 2: `FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Propriedades padrão twin desejadas**      | Cada nome e valor das propriedades desejadas do gêmeo padrão devem ter menos de 512 caracteres. É possível definir até 5 propriedades desejadas de nome/valor do gêmeo. Os valores das propriedades desejadas do gêmeo devem ser válidos como JSON, sem escape, sem matrizes e com uma hierarquia aninhada máxima de 4. Saiba mais sobre as [propriedades desejadas do gêmeo](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Por exemplo, se um módulo der suporte a uma taxa de atualização configurável dinamicamente através de propriedades desejadas do gêmeo, é recomendável definir a seguinte propriedade desejada do gêmeo padrão:<br/> - Nome nº 1: `RefreshRate`<br/>- Valor nº 1: `60`|
| **Variáveis de ambiente padrão**  | Cada nome e valor de variáveis de ambiente padrão deve ter menos de 512 caracteres. Você pode definir até 5 variáveis de ambiente de nome/valor. <br/>Por exemplo, se um módulo precisar aceitar termos de uso antes de ser iniciado, será possível definir a seguinte variável de ambiente:<br/> - Nome nº 1: `ACCEPT_EULA`<br/>- Valor nº 1: `Y`|
| **Criação padrãoOpod**  | O createOptions deve ter menos de 512 caracteres. Deve ser um JSON válido, sem escape. Saiba mais sobre [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Por exemplo, se um módulo exigir associar uma porta, você poderá definir as seguintes createOptions:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

Selecione **Salvar** para salvar as configurações da SKU. 


## <a name="next-steps"></a>Próximas etapas

Use a [guia Marketplace](./cpp-marketplace-tab.md) para criar uma descrição do Marketplace para sua oferta.
