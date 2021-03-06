---
title: Editar informações do grupo – Azure Active Directory | Microsoft Docs
description: Instruções sobre como editar as informações do grupo usando o Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc06abca08b2522ac57552e85f7c1bac3ef854af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561883"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Editar as informações do grupo usando o Azure Active Directory

Usando o Azure AD (Azure Active Directory), é possível editar as configurações de um grupo, incluindo a atualização de nome, descrição ou tipo de associação.

## <a name="to-edit-your-group-settings"></a>Para editar as configurações de grupo
1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de administrador Global para o diretório.

2. Selecione **Azure Active Directory** e, em seguida, selecione **Grupos**.

    A página **Grupos - Todos os grupos** é exibida, mostrando todos os grupos ativos.

3. Na página **Grupos - Todos os grupos**, digite o máximo possível do nome do grupo na caixa **Pesquisar**. Para os fins deste artigo, estamos pesquisando o grupo **Política de MDM - Oeste**.

    Os resultados da pesquisa aparecem na caixa **Pesquisar**, que é atualizada na medida em que você digita mais caracteres.

    ![Página Todos os grupos, com texto de pesquisa na caixa Pesquisar](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Selecione o grupo **Política de MDM - Oeste** e, em seguida, selecione **Propriedades** na área **Gerenciar**.

    ![Página de visão geral do grupo, com opção de membro e informações destacadas](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Atualize as informações **Configurações gerais** conforme necessário, incluindo:

    ![Configurações de propriedades para um grupo](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Nome do grupo.** Edite o nome do grupo existente.
    
    - **Descrição do grupo.** Edite a descrição do grupo existente.

    - **Tipo de grupo.** Não será possível alterar o tipo de grupo após ter sido criado. Para alterar o **Tipo de grupo**, é necessário excluir o grupo e criar um novo.
    
    - **Tipo de adesão.** Altere o tipo de associação. Para obter mais informações sobre os vários tipos de membros disponíveis, consulte [Como: Criar um grupo básico e adicionar membros usando o portal Azure Active Directory](active-directory-groups-create-azure-portal.md).
    
    - **ID do objeto.** Não é possível alterar a ID do objeto, mas é possível copiá-la para usar nos comandos do PowerShell para o grupo. Para obter mais informações sobre o uso de cmdlets do PowerShell, consulte [Cmdlets do Azure Active Directory para definir configurações de grupo](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Próximas etapas
Esses artigos fornecem mais informações sobre o Active Directory do Azure.

- [Exibir grupos e membros](active-directory-groups-view-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Como adicionar ou remover membros de um grupo](active-directory-groups-members-azure-portal.md)

- [Gerenciar regras dinâmicas para usuários em um grupo](../users-groups-roles/groups-create-rule.md)

- [Gerenciar associações de um grupo](active-directory-groups-membership-azure-portal.md)

- [Gerenciar acesso a recursos usando grupos](active-directory-manage-groups.md)

- [Associar ou adicionar uma assinatura do Azure ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
