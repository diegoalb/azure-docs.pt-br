---
title: Atribuir funções azure AD no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como atribuir funções azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048cefaae10cd55091dd72f0b73a3cf9d731a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253267"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Atribuir funções ad do Azure no gerenciamento de identidade privilegiado

Com o Azure Active Directory (Azure AD), um administrador global pode fazer atribuições **permanentes** de admin Azure AD. Essas atribuições de função podem ser criadas usando o [portal do Azure](../users-groups-roles/directory-assign-admin-roles.md) ou usando [comandos do PowerShell](/powershell/module/azuread#directory_roles).

O serviço Pim (AD Privileged Identity Management, gerenciamento de identidade privilegiado) do Azure AD também permite que os administradores de papéis privilegiados façam atribuições de função de administração permanente. Além disso, os administradores de funções privilegiadas podem tornar os usuários **elegíveis** para funções de administrador do Azure AD. Um administrador qualificado pode ativar a função quando necessário e suas permissões expirarão assim que forem feitas.

## <a name="determine-your-version-of-pim"></a>Determine sua versão do PIM

A partir de novembro de 2019, a parte de funções Azure AD do Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências para funções de recursos do Azure. Isso cria recursos adicionais, bem como [alterações na API existente](azure-ad-roles-features.md#api-changes). Enquanto a nova versão está sendo lançada, quais procedimentos você segue neste artigo dependem da versão do Gerenciamento de Identidade Privilegiada que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Gerenciamento de Identidade Privilegiada você tem. Depois de conhecer sua versão do Gerenciamento de Identidade Privilegiada, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Faça login no [portal Azure](https://portal.azure.com/) com um usuário que esteja na função [de administrador de funções privilegiadas.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Abra **o Azure AD Privileged Identity Management**. Se você tiver um banner na parte superior da página de visão geral, siga as instruções na guia **Nova versão** deste artigo. Caso contrário, siga as instruções na guia **versão anterior.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nova versão](#tab/new)

## <a name="assign-a-role"></a>Atribuir uma função

Siga estas etapas para tornar um usuário elegível para uma função de administrador azure AD.

1. Faça login no [portal Azure](https://portal.azure.com/) com um usuário que é membro da função [de administrador de funções privilegiadas.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Para obter informações sobre como conceder a outro administrador acesso para gerenciar o Gerenciamento de Identidade Privilegiada, consulte [Conceder acesso a outros administradores para gerenciar o Gerenciamento de Identidade Privilegiada](pim-how-to-give-access-to-pim.md).

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **funções Azure AD**.

1. Selecione **Funções** para ver a lista de funções para permissões Azure AD.

    ![Funções do Azure AD](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Selecione **Adicionar membro** para abrir a página **Nova atribuição.**

1. Selecione **Selecionar uma função** para abrir a página Selecionar uma função.

    ![Novo painel de atribuição](./media/pim-how-to-add-role-to-user/select-role.png)

1. Selecione uma função que você deseja atribuir e clique em **Selecionar**.

1. Selecione um membro a quem deseja atribuir à função e selecione **Selecionar Selecionar**.

1. Na lista **de tipos de atribuição** no painel **'Configurações** de associação', **selecione Elegível** ou **Ativo**.

    - **As** atribuições elegíveis exigem que o membro da função execute uma ação para usar a função. As ações podem incluir a execução de uma verificação de MDA (Autenticação Multifator), fornecimento de uma justificativa comercial ou solicitação de aprovação dos aprovadores designados.

    - **As** atribuições ativas não exigem que o membro execute qualquer ação para usar a função. Membros atribuídos como ativos sempre possuem privilégios atribuídos pela função.

1. Se a atribuição for permanente (permanentemente elegível ou permanentemente atribuído), selecione a caixa de seleção **Permanente.**

    Dependendo das configurações de função, a caixa de seleção poderá não aparecer ou não ser modificável.

1. Para especificar uma duração de atribuição específica, desmarque a caixa de seleção e modifique as caixas de data e hora de início e/ou término. Quando terminar, selecione **Concluído**.

    ![Configurações de associação - data e hora](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Para criar a nova atribuição de função, selecione **Adicionar**. Uma notificação do status é exibida.

    ![Nova atribuição - Notificação](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de função existente

Siga estas etapas para atualizar ou remover uma atribuição de função existente.

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **funções Azure AD**.

1. Selecione **Funções** para ver a lista de funções do Azure AD.

1. Selecione a função que você deseja atualizar ou remover.

1. Localize a atribuição de função nas guias **Funções qualificadas** ou **Funções ativas**.

    ![Atualizar ou remover atribuição de função](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Selecione **Atualizar** ou **Remover** para atualizar ou remover a atribuição de função.

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Qualificar um usuário para uma função

Siga estas etapas para tornar um usuário elegível para uma função de administrador azure AD.

1. Selecionar **Funções** ou **Membros**.

    ![Funções do Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Selecione **Adicionar membro** para abrir Adicionar **membros gerenciados**.

1. Selecione **Selecionar uma função,** selecionar uma função que deseja gerenciar e, em seguida, selecionar **Selecionar**.

    ![Selecione uma função](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Selecione **Selecionar membros,** selecione os usuários que deseja atribuir à função e, em seguida, **selecione Selecionar**.

    ![Selecione uma função](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Em **Adicionar membros gerenciados,** selecione **OK** para adicionar o usuário à função.

1. Na lista de funções, selecione a função que você acabou de atribuir para ver a lista de membros.

     Quando a função for atribuída, o usuário selecionado aparecerá na lista de membros como **Elegível** para a função.

    ![Usuário qualificado para uma função](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Agora que o usuário é elegível para a função, deixe-o saber que ele pode ativá-lo de acordo com as instruções em [Ativar minhas funções Azure AD em Gerenciamento de Identidade Privilegiada](pim-how-to-activate-role.md).

    Os administradores qualificados são solicitados a registrar na MFA (Autenticação Multifator do Microsoft Azure) durante a ativação. Se um usuário não pode se registrar no MFA, ou estiver usando uma conta da Microsoft (como), @outlook.comvocê precisa torná-lo permanente em todas as suas funções.

## <a name="make-a-role-assignment-permanent"></a>Tornar uma atribuição de função permanente

Por padrão, novos usuários só são *elegíveis* para uma função de administrador azure AD. Siga estas etapas se quiser tornar uma atribuição de função permanente.

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **funções Azure AD**.

1. Selecione **Membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecione uma **função elegível** que você deseja tornar permanente.

1. Selecione **Mais** e, em seguida, selecione **Fazer permanente**.

    ![Tornar a atribuição de função permanente](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    A função agora está listada como **permanente**.

    ![Lista de membros com alteração permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Remover um usuário de uma função

Você pode remover os usuários das atribuições de função, mas certifique-se de que sempre há pelo menos um usuário que é um administrador Global permanente. Se não tiver certeza de quais os usuários ainda precisam das suas atribuições de função, você poderá [iniciar uma revisão de acesso para a função](pim-how-to-start-security-review.md).

Siga estas etapas para remover um usuário específico de uma função de administrador do Azure AD.

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **funções Azure AD**.

1. Selecione **Membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecione uma atribuição de função que deseja remover.

1. Selecione **Mais** e, em seguida, **selecione Remover**.

    ![Remover uma função](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Na mensagem que pede para confirmar, selecione **Sim**.

    ![Remover uma função](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    A atribuição de função será removida.

## <a name="authorization-error-when-assigning-roles"></a>Erro de autorização ao atribuir funções

Se você habilitou recentemente o Gerenciamento de Identidade Privilegiada para uma assinatura e você recebe um erro de autorização quando você tenta tornar um usuário elegível para uma função de administrador azure AD, pode ser porque o diretor de serviço ms-PIM ainda não tem as permissões apropriadas. O diretor de serviço sin-pim deve ter a função [administrador de acesso ao usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) para atribuir funções a outros. Em vez de esperar até que o MS-PIM receba a função de Administrador de Acesso do Usuário, atribua-a manualmente.

Siga estas etapas para atribuir a função de Administrador de Acesso do Usuário à entidade de serviço do MS-PIM para uma assinatura.

1. Entre no portal do Azure como Administrador Global.

1. Escolha **Todos os serviços** e **Assinaturas**.

1. Escolha sua assinatura.

1. Clique em **Controle de acesso (IAM)**.

1. Escolha **Atribuições de função** para ver a lista atual de atribuições de função no escopo da assinatura.

   ![Folha IAM (controle) de acesso para uma assinatura](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Verifique se a entidade de serviço **MS-PIM** tem a função **Administrador de Acesso do Usuário**.

1. Caso contrário, escolha **Adicionar atribuição de função** para abrir o painel **Adicionar atribuição de função**.

1. Na lista suspensa **Função**, selecione a função **Administrador de Acesso do Usuário**.

1. Na lista **Selecionar**, localize e selecione a entidade de serviço **MS-PIM**.

   ![Adicionar painel de atribuição de função - Adicionar permissões para o diretor de serviço MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Escolha **Salvar** para atribuir a função.

   Após alguns momentos, verifique se a entidade de serviço do MS-PIM tem a função Administrador de Acesso do Usuário no escopo da assinatura.

   ![Página de controle de acesso mostrando atribuição de função de acesso ao usuário para o principal de serviço MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Próximas etapas

- [Configure as configurações de função de administrador do Azure AD no Gerenciamento de Identidade Privilegiada](pim-how-to-change-default-settings.md)
- [Atribuir funções de recurso do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-assign-roles.md)