---
title: 'Azure AD Connect: Configure a localização de dados preferida para os recursos do Office 365'
description: Descreve como colocar os recursos de usuário do Office 365 perto do usuário com a sincronização do Azure Active Directory Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a71c5328c6fa85f85db4bd7e6103f6470b86d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258321"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Sincronização do Azure Active Directory Connect: configurar o local de dados preferencial para recursos do Office 365
O objetivo deste tópico é mostrar como configurar o atributo para localização de dados preferidos no Azure Active Directory (Azure AD) Conectar sincronização. Quando alguém usa recursos Multi-Geo no Office 365, você usa esse atributo para designar a geolocalização dos dados do Office 365 do usuário. (Os termos *região* e *área geográfica* são usados de maneira intercambiável.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Habilitar a sincronização de local de dados preferenciais
Por padrão, os recursos do Office 365 para seus usuários estão localizados na mesma área geográfica que o locatário do Azure AD. Por exemplo, se o seu locatário estiver localizado na América do Norte, as caixas de correio do Exchange dos usuários também estarão localizadas na América do Norte. Para uma organização multinacional, isso pode não ser o ideal.

Com a configuração do atributo **preferredDataLocation**, a área geográfica do usuário poderá ser definida. Você pode ter os recursos do Office 365 do usuário, como o OneDrive e a caixa de correio, na mesma área geográfica que o usuário e ainda ter um locatário para toda a organização.

> [!IMPORTANT]
> A Multi-Geo está atualmente disponível para clientes com um Contrato Corporativo ativo e um mínimo de 500 assinaturas de Serviços Office 365. Converse com seu representante Microsoft para obter detalhes.
>
>

Uma lista de todos os geos para o Office 365 pode ser encontrada em [Onde estão localizados seus dados?](https://aka.ms/datamaps).

As áreas geográficas no Office 365, disponíveis para replicação Multigeográfica, são:

| Localização geográfica | Valor de preferredDataLocation |
| --- | --- |
| Pacífico Asiático | APC |
| Austrália | AUS |
| Canada | CAN |
| União Europeia | EUR |
| França | FRA |
| Índia | IND |
| Japão | JPN |
| Coreia do Sul | KOR |
| África do Sul | ZAF |
| Emirados Árabes Unidos | ARE |
| United Kingdom | GBR |
| Estados Unidos | NAM |

* Se uma área geográfica não estiver listada nesta tabela, por exemplo, América do Sul, ela não poderá ser usada para Multi-Geo.

* Nem todas as cargas de trabalho do Office 365 são compatíveis com o uso da configuração de área geográfica do usuário.

### <a name="azure-ad-connect-support-for-synchronization"></a>Suporte do Azure AD Connect para sincronização

O Azure AD Connect dá suporte à sincronização do atributo **PreferredDataLocation** para objetos de **Usuário** na versão 1.1.524.0 e posteriores. Especificamente:

* O esquema do tipo de objeto **Usuário** no Azure AD Connector é estendido para incluir o atributo **preferredDataLocation**. O atributo é do tipo de cadeia de caracteres de valor único.
* O esquema do tipo de objeto **Pessoa** no metaverso é estendido para incluir o atributo **preferredDataLocation**. O atributo é do tipo de cadeia de caracteres de valor único.

Por padrão, **preferredDataLocation** não está habilitado para sincronização. Esse recurso destina-se a organizações maiores. O esquema de diretório ativo no Windows Server 2019 tem um atributo **msDS-preferredDataLocation** que você deve usar para este fim. Se você não atualizou o esquema do Active Directory e não conseguiu fazê-lo, então você deve identificar um atributo para manter o Catálogo 365 geográfico para seus usuários. Isso vai ser diferente para cada organização.

> [!IMPORTANT]
> Atualmente, o Microsoft Azure Active Directory permite que o atributo **preferredDataLocation**, em **objetos de Usuário sincronizados**, seja configurado diretamente usando o PowerShell do Azure AD. Para configurar o atributo em **objetos de Usuário sincronizados**, use o Azure AD Connect.

Antes de habilitar a sincronização:

* Se você não atualizou o esquema do Active Directory para 2019, então decida qual atributo active directory no local será usado como atributo de origem. Ele deve ser do tipo **cadeia de caracteres de valor único**.
* Se você já tiver configurado o atributo **preferredDataLocation** anteriormente em **objetos de Usuário sincronizados** existentes no Azure AD usando o PowerShell do Azure AD, faça o backport dos valores de atributo para os objetos de **Usuário** correspondentes no Active Directory local.

    > [!IMPORTANT]
    > Caso você não faça o backport desses valores, o Azure AD Connect removerá os valores de atributo existentes no Azure AD quando a sincronização para o atributo **preferredDataLocation** for habilitada.

* Configure o atributo de origem em pelo menos alguns objetos de Usuário do Active Directory local agora. Você pode usar isso para verificação posterior.

As seções a seguir fornecem as etapas para habilitar a sincronização do atributo **preferredDataLocation**.

> [!NOTE]
> As etapas são descritas no contexto de uma implantação do Microsoft Azure Active Directory com topologia de floresta única e sem regras de sincronização personalizadas. Se você tiver uma topologia de várias florestas, regras de sincronização personalizadas configuradas ou possuir um servidor de preparo, você deverá ajustar as etapas apropriadamente.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Etapa 1: desabilitar o agendador de sincronização e verificar se não há nenhuma sincronização em andamento
Garanta que não haja nenhuma sincronização enquanto você estiver atualizando as regras de sincronização para evitar a exportação de alterações indesejadas para o Microsoft Azure Active Directory. Para desabilitar o agendador de sincronização interno:

1. Inicie uma sessão do PowerShell no servidor do Azure AD Connect.
2. Desabilite a sincronização agendada executando este cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Inicie o **Synchronization Service Manager** acessando **INICIAR** > **Serviço de Sincronização**.
4. Selecione a guia **Operações** e confirme se não há nenhuma operação cujo status seja *em andamento*.

![Captura de tela do Synchronization Service Manager](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>Passo 2: Atualize o esquema para o Active Directory
Se você atualizou o esquema do Active Directory para 2019 e o Connect foi instalado antes da extensão do esquema, então o cache do esquema Conectar não tem o esquema atualizado. Em seguida, você deve atualizar o esquema do assistente para que ele apareça na interface do usuário.

1. Inicie o assistente Azure AD Connect a partir da área de trabalho.
2. Selecione a opção Atualizar esquema **de diretório** e clique **em Seguir**.
3. Digite suas credenciais azure AD e clique **em Next**.
4. Na página **'Atualizar esquema de diretório',** certifique-se de que todas as florestas estão selecionadas e clique **em Next**.
5. Quando concluído, feche o assistente.

![Captura de tela do Esquema de Diretório de Atualização no assistente Conectar](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Passo 3: Adicione o atributo de origem ao esquema do conector ativo do diretório no local
**Esta etapa só é necessária se você executar a versão Connect 1.3.21 ou mais velha. Se você estiver em 1.4.18 ou mais novo, então pule para o passo 5.**  
Nem todos os atributos Azure AD são importados para o espaço de conector active directory no local. Se tiver optado por usar um atributo não sincronizado por padrão, você precisará importá-lo. Para adicionar o atributo de origem à lista de atributos importados:

1. Selecione a guia **Conectores** no Synchronization Service Manager.
2. Clique com o botão direito do mouse no Active Directory Connector local e selecione **Propriedades**.
3. Na caixa de diálogo pop-up, vá para a guia **Selecionar Atributos**.
4. Verifique se o atributo de origem que você selecionou está marcado na lista de atributos. Se você não vir o atributo, marque a caixa de seleção **Mostrar Tudo**.
5. Para salvar, selecione **OK**.

![Captura de tela do Synchronization Service Manager e da caixa de diálogo Propriedades](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Passo 4: Adicionar **dados preferidosLocalização** ao esquema do conector Azure AD
**Esta etapa só é necessária se você executar a versão Connect 1.3.21 ou mais velha. Se você estiver em 1.4.18 ou mais novo, então pule para o passo 5.**  
Por padrão, o atributo **PreferredDataLocation** não é importado para o espaço do Conector Azure AD. Para adicioná-lo à lista de atributos importados:

1. Selecione a guia **Conectores** no Synchronization Service Manager.
2. Clique com o botão direito do mouse no conector Azure AD e selecione **Propriedades**.
3. Na caixa de diálogo pop-up, vá para a guia **Selecionar Atributos**.
4. Selecione o atributo **preferredDataLocation** na lista.
5. Para salvar, selecione **OK**.

![Captura de tela do Synchronization Service Manager e da caixa de diálogo Propriedades](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>Passo 5: Crie uma regra de sincronização de entrada
A regra de sincronização de entrada permite que o valor do atributo flua do atributo de origem no Active Directory local para o metaverso.

1. Inicie o **Editor de Regras de Sincronização** acessando **INICIAR** > **Editor de Regras de Sincronização**.
2. Defina o filtro de pesquisa **Direção** como **Entrada**.
3. Para criar uma nova regra de entrada, selecione **Adicionar nova regra**.
4. Na guia **Descrição,** forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | --- | --- | --- |
    | Nome | *Fornecer um nome* | Por exemplo, "Entrada do AD – PreferredDataLocation do usuário" |
    | Descrição | *Forneça uma descrição personalizada* |  |
    | Sistema Conectado | *Selecione o Active Directory Connector local* |  |
    | Tipo de Objeto do Sistema Conectado | **Usuário** |  |
    | Tipo de Objeto de Metaverso | **Pessoa** |  |
    | Tipo de link | **Ingressar** |  |
    | Precedência | *Escolha um número entre 1 e 99* | 1 a 99 são reservados para regras de sincronização personalizadas. Não selecione um valor que seja usado por outra regra de sincronização. |

5. Mantenha o **filtro escopo** vazio, para incluir todos os objetos. Talvez seja necessário ajustar o filtro de escopo de acordo com sua implantação do Azure AD Connect.
6. Vá para a **guia Transformação**e implemente a seguinte regra de transformação:

    | Tipo de fluxo | Atributo de destino | Fonte | Aplicar uma vez | Tipo de mesclagem |
    | --- | --- | --- | --- | --- |
    |Direto | preferredDataLocation | Selecione o atributo de origem | Desmarcado | Atualizar |

7. Para criar a regra de entrada, selecione **Adicionar**.

![Captura de tela de Criar regra de sincronização de entrada](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>Passo 6: Crie uma regra de sincronização de saída
A regra de sincronização de saída permite que o valor de atributo flua do metaverso para o atributo **PreferredDataLocation** no Azure AD:

1. Vá para o **Editor de Regras de Sincronização**.
2. Defina o filtro de pesquisa **Direção** como **Saída**.
3. Selecione **Adicionar nova regra**.
4. Na guia **Descrição,** forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | ----- | ------ | --- |
    | Nome | *Fornecer um nome* | Por exemplo, "Saída para Microsoft Azure AD – PreferredDataLocation do usuário" |
    | Descrição | *Forneça uma descrição* ||
    | Sistema Conectado | *Selecionar o Azure AD Connector* ||
    | Tipo de Objeto do Sistema Conectado | **Usuário** ||
    | Tipo de Objeto de Metaverso | **Pessoa** ||
    | Tipo de link | **Ingressar** ||
    | Precedência | *Escolha um número entre 1 e 99* | 1 a 99 são reservados para regras de sincronização personalizadas. Não selecione um valor que seja usado por outra regra de sincronização. |

5. Vá para a guia **Filtro de escopo** e adicione um único grupo de filtro de escopo com duas cláusulas:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Usuário |
    | cloudMastered | NOTEQUAL | True |

    Filtro de escopo determina a quais objetos do Azure AD essa regra de sincronização de saída é aplicada. Neste exemplo, usamos o mesmo filtro de escopo da regra de sincronização OOB "Out to Azure AD – User Identity" (out-of-box). Ele impede que a regra de sincronização seja aplicada a objetos **do Usuário** que não estão sincronizados a partir de um Diretório Ativo no local. Talvez seja necessário ajustar o filtro de escopo de acordo com sua implantação do Azure AD Connect.

6. Vá para a guia **Transformação** e implemente a seguinte regra de transformação:

    | Tipo de fluxo | Atributo de destino | Fonte | Aplicar uma vez | Tipo de mesclagem |
    | --- | --- | --- | --- | --- |
    | Direto | preferredDataLocation | preferredDataLocation | Desmarcado | Atualizar |

7. Feche **Adicionar** para criar a regra de saída.

![Captura de tela de Criar regra de sincronização de saída](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>Passo 7: Execute o ciclo de sincronização completo
Em geral, o ciclo completo de sincronização é necessário. Isso ocorre porque você adicionou novos atributos ao Active Directory e ao esquema do Azure AD Connector e introduziu regras de sincronização personalizadas. Verifique as alterações antes de exportá-las para o Microsoft Azure Active Directory. Você pode usar as etapas a seguir para verificar as alterações enquanto executa manualmente as etapas que formam um ciclo completo de sincronização.

1. Execute **Importação completa** no Active Directory Connector local:

   1. Acesse a guia **Operações** no Synchronization Service Manager.
   2. Clique com o botão direito do mouse no **Active Directory Connector local** e selecione **Executar**.
   3. Na caixa de diálogo, selecione **Importação Completa** e clique em **OK**.
   4. Aguarde a conclusão da operação.

      > [!NOTE]
      > Você poderá ignorar a importação completa no Active Directory Connector local se o atributo de origem já estiver incluído na lista de atributos importados. Em outras palavras, você não precisou fazer nenhuma alteração durante a etapa 2 neste artigo.

2. Execute **Importação completa** no Azure AD Connector:

   1. Clique com o botão direito do mouse no **conector Azure AD**e selecione **Executar**.
   2. Na caixa de diálogo, selecione **Importação Completa** e clique em **OK**.
   3. Aguarde a conclusão da operação.

3. Verifique as alterações das regras de sincronização em um objeto **de Usuário** existente.

   O atributo de origem do Active Directory local e de **preferredDataLocation** do Azure Active Directory foi importado em cada respectivo espaço conector. Antes de continuar com a etapa de sincronização completa, faça uma visualização em um objeto de **Usuário** existente no espaço conector do Active Directory Connector local. O objeto escolhido deve ter o atributo de origem preenchido. Uma visualização bem-sucedida com **preferredDataLocation** preenchido no metaverso é um bom indicador que você configurou as regras de sincronização corretamente. Para obter informações sobre como fazer uma visualização, consulte [Verificar a alteração](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Execute **Sincronização Completa** no Active Directory Connector local:

   1. Clique com o botão direito do mouse no **Active Directory Connector local** e selecione **Executar**.
   2. Na caixa de diálogo, selecione **Sincronização Completa** e depois **OK**.
   3. Aguarde a conclusão da operação.

5. Verifique **Exportações Pendentes** para o Azure AD:

   1. Clique com o botão direito do mouse no **conector Azure AD**e selecione **Search Connector Space**.
   2. Na caixa de diálogo **Pesquisar Espaço Conector**:

        a. Defina o **Escopo** como **Exportação Pendente**.<br>
        b. Marque todas as três caixas de seleção, incluindo **Adicionar, Modificar e Excluir**.<br>
        c. Para exibir a lista de objetos com alterações para exportar, selecione **Pesquisar**. Para examinar as alterações para um determinado objeto, clique duas vezes nele.<br>
        d. Verifique se as alterações são esperadas.

6. Executar **exportação** no **conector Azure AD**

   1. Clique com o botão direito do mouse no **conector Azure AD**e selecione **Executar**.
   2. Na caixa de diálogo **Executar Conector**, selecione **Exportar** e depois **OK**.
   3. Aguarde a conclusão da operação.

> [!NOTE]
> Você pode observar que as etapas não incluem a etapa de sincronização completa no Azure AD Connector ou a etapa de exportação no Active Directory Connector. As etapas não são obrigatórias, já que os valores de atributo estão fluindo do Active Directory local para o Azure AD apenas.

## <a name="step-8-re-enable-sync-scheduler"></a>Passo 8: Reativar o agendador de sincronização
Reabilite o agendador de sincronização interno:

1. Inicie uma sessão do PowerShell.
2. Reabilite a sincronização agendada executando este cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-9-verify-the-result"></a>Passo 9: Verifique o resultado
Agora é hora de verificar a configuração e ativá-la para seus usuários.

1. Adicione a área geográfica ao atributo selecionado em um usuário. A lista de áreas geográficas disponíveis pode ser encontrada nessa tabela.  
![Captura de tela do atributo AD adicionado a um usuário](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Aguarde até que o atributo seja sincronizado com o Azure AD.
3. Usando o PowerShell do Exchange Online, verifique se a região da caixa de correio foi definida corretamente.  
![Captura de tela do PowerShell do Exchange Online](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Supondo que o locatário foi marcado para ser capaz de usar esse recurso, a caixa de correio será movida para a área geográfica correta. Isso pode ser verificado ao examinar o nome do servidor em que a caixa de correio está localizada.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o Multi-Geo no Office 365:

* [Sessões do Multi-Geo no Ignite](https://aka.ms/MultiGeoIgnite)
* [Multi-Geo no OneDrive](https://aka.ms/OneDriveMultiGeo)
* [Multi-Geo no SharePoint Online](https://aka.ms/SharePointMultiGeo)

Saiba mais sobre o modelo de configuração no mecanismo de sincronização:

* Leia mais sobre o modelo de configuração em [Noções básicas do provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Leia mais sobre a linguagem de expressão em [Noções básicas sobre expressões de provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Tópicos de visão geral:

* [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integrando suas identidades locais ao Azure Active Directory](whatis-hybrid-identity.md)
