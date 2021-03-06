---
title: Configure uma rede virtual existente para instância gerenciada
description: Este artigo descreve como configurar uma rede virtual e a sub-rede existentes em que você pode implantar a Instância Gerenciada do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 50b832baa9253f47b5f10980ae1764c9425ed4d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476942"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Configurar uma rede virtual existente para uma Instância Gerenciada do Banco de Dados SQL do Azure

A Instância Gerenciada do Banco de Dados SQL do Azure deve ser implantada dentro da [rede virtual](../virtual-network/virtual-networks-overview.md) do Azure e da sub-rede dedicada somente a Instâncias Gerenciadas. Você poderá usar a rede virtual e a sub-rede existentes se elas estiverem configuradas conforme os [requisitos de rede virtual da Instância Gerenciada](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Se um dos seguintes casos se aplica a você, você pode validar e modificar sua rede usando o script explicado neste artigo:

- Você tem uma nova sub-rede que ainda não está configurada.
- Você não tem certeza de que a sub-rede está alinhada com os [requisitos](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
- Você deseja verificar se a sub-rede ainda está em conformidade com os [requisitos de rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements) depois que você fez alterações.

> [!Note]
> Você pode criar uma instância gerenciada somente em redes virtuais criadas por meio do modelo de implantação do Azure Resource Manager. Redes virtuais do Azure criadas por meio do modelo de implantação clássico não são compatíveis para esse fim. Calcule o tamanho da sub-rede, seguindo as diretrizes no artigo [Determinar o tamanho da sub-rede para Instâncias Gerenciadas](sql-database-managed-instance-determine-size-vnet-subnet.md). Você não poderá redimensionar a sub-rede depois de implantar os recursos dentro dela.
>
> Depois que uma instância gerenciada é criada, a movimentação da instância gerenciada ou do VNet para outro grupo de recursos ou assinatura não é suportada.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Validar e modificar uma rede virtual existente

Se você quer criar uma Instância Gerenciada dentro de uma sub-rede existente, recomendamos o seguinte script do PowerShell para preparar a sub-rede:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

O script prepara a sub-rede em três etapas:

1. Validar: Valida a rede virtual e a sub-rede selecionadas para os requisitos de rede de instância gerenciada.
2. Confirme: Ele mostra ao usuário um conjunto de alterações que precisam ser feitas para preparar a sub-rede para implantação de Instância Gerenciada. Ele também solicita seu consentimento.
3. Prepare-se: Configura corretamente a rede virtual e a sub-rede.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, confira [O que é uma Instância Gerenciada?](sql-database-managed-instance.md).
- Para um tutorial que mostra como criar uma rede virtual, criar uma Instância Gerenciada e restaurar um banco de dados com base em um backup de banco de dados, confira [Criar uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-get-started.md).
- Para problemas de DNS, consulte [Configurando um DNS personalizado](sql-database-managed-instance-custom-dns.md).
