---
title: incluir arquivo
description: incluir arquivo
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 55eee87ed1e07b61a7f8287e86ddc0a7a6aa4df9
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2020
ms.locfileid: "75456782"
---
Estes exemplos mostram como usar o Azure Policy com assinaturas que foram integradas para o gerenciamento de recursos delegados do Azure.

| **Modelo** | **Descrição** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Atribui uma política que adiciona ou remove uma marca (usando o efeito modificar) a uma assinatura delegada. Para saber mais, confira [Implantar uma política que pode ser corrigida em uma assinatura delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Atribui uma política que fará a auditoria de atribuições de delegação. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Atribui uma política que habilita o diagnóstico nos recursos do Azure Key Vault em assinaturas delegadas (usando o efeito deployIfNotExists). Para saber mais, confira [Implantar uma política que pode ser corrigida em uma assinatura delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Atribui várias políticas para habilitar o diagnóstico em uma assinatura delegada e conecta todas as VMs Windows e Linux ao workspace do Log Analytics criado pela política. Para saber mais, confira [Implantar uma política que pode ser corrigida em uma assinatura delegada](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Aplica uma iniciativa (várias definições de política relacionadas) a uma assinatura delegada. |

