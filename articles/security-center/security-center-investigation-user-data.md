---
title: Gerenciar dados de usuário encontrados em uma investigação da Central de Segurança do Azure
description: " Saiba como gerenciar dados de usuário encontrados no recurso de investigação da Central de Segurança do Azure. "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: aa262b0be3902f6e143a53f8f1302156fc5aede6
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582978"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Gerenciar dados de usuário encontrados em uma investigação da Central de Segurança do Azure
Este artigo fornece informações sobre como gerenciar dados de usuário encontrados no recurso de investigação da Central de Segurança do Azure. Os dados da investigação são armazenados nos [registros do Monitor do Azure](../log-analytics/log-analytics-overview.md) e expostos no Security Center. O gerenciamento de dados de usuário inclui a capacidade de excluir dados ou exportar dados.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Pesquisar e identificar dados pessoais
No portal do Azure, você pode usar o recurso de investigação da Central de Segurança para pesquisar dados pessoais. O recurso de investigação está disponível em **alertas de segurança**.

O recurso de investigação mostra todas as entidades, informações de usuário e dados na guia **Entidades**.

## <a name="securing-and-controlling-access-to-personal-information"></a>Protegendo e controlando o acesso a informações pessoais
Um usuário da Central de Segurança com a função de Leitor, Proprietário, Colaborador ou Administrador da Conta pode acessar os dados do cliente dentro da ferramenta.

Confira [Funções internas para o controle de acesso baseado em função do Azure](../role-based-access-control/built-in-roles.md) para saber mais sobre as funções Leitor, Proprietário e Colaborador. Confira [Administradores da assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) para saber mais sobre a função Administrador da Conta.

## <a name="deleting-personal-data"></a>Excluir dados pessoais
Um usuário da Central de Segurança com a função Proprietário, Colaborador ou Administrador da Conta pode excluir informações sobre a investigação.

Para excluir uma investigação, você pode enviar uma solicitação `DELETE` à API REST do Azure Resource Manager:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

A entrada `incidentName` pode ser encontrada listando todos os incidentes usando uma solicitação `GET`:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Exportar dados pessoais
Um usuário da Central de Segurança com a função Proprietário, Colaborador ou Administrador da Conta pode exportar informações sobre a investigação. Para exportar informações de investigação, vá para a guia **Entidades** para copiar e colar as informações relevantes.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o gerenciamento de dados de usuário, confira [Gerenciar dados de usuário localizados na Central de Segurança do Azure](security-center-privacy.md).
Para saber mais sobre a exclusão de dados privados nos registros do Azure Monitor, consulte [Como exportar e excluir dados privados](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).
