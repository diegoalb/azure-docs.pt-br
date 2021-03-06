---
title: Gerencie alertas do Gerente de Operações do System Center, Zabbix e Nagios no Monitor Azure
description: Gerencie alertas do Gerente de Operações do System Center, Zabbix e Nagios no Monitor Azure
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 5ef03f522fb91302bfdec985f1e3c5cf8138ae97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667441"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Gerencie alertas do Gerente de Operações do System Center, Zabbix e Nagios no Monitor Azure

Agora você pode exibir os alertas do Nagios, do Zabbix e do System Center Operations Manager no [Azure Monitor](https://aka.ms/azure-alerts-overview). Esses alertas provenientes de integrações com servidores Nagios/Zabbix ou o System Center Operations Manager para o Log Analytics. 

## <a name="prerequisites"></a>Pré-requisitos
Os registros do repositório do Log Analytics com um tipo de Alerta serão importados para o Azure Monitor e, portanto, você precisará executar a configuração necessária para coletar esses registros.
1. Para **alertas Nagios** e **Zabbix,** [configure esses servidores](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) para enviar [alertas](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) ao Log Analytics.
1. Para alertas **do System Center Operations Manager,** [conecte o grupo de gerenciamento do Gerente de Operações ao espaço de trabalho do Log Analytics.](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents) Depois disso, implante a solução [Gerenciamento de Alertas](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) por meio do marketplace de soluções do Azure. Quando você terminar, todos os alertas criados no System Center Operations Manager serão importados para o Log Analytics.

## <a name="view-your-alert-instances"></a>Exibir suas instâncias de alerta
Depois de configurar a importação para o Log Analytics, comece a exibir instâncias de alerta desses serviços de monitoramento no [Azure Monitor](https://aka.ms/azure-alerts-overview). Quando eles estiverem presentes no Azure Monitor, você poderá [gerenciar suas instâncias de alerta](https://aka.ms/managing-alert-instances), [gerenciar grupos inteligentes criados nesses alertas](https://aka.ms/managing-smart-groups) e [alterar o estado dos alertas e dos grupos inteligentes](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Essa solução só permite que você visualize o System Center Operations Manager/Zabbix/Nagios em ocorrências de alerta no Azure Monitor. A configuração de regra de alerta só pode ser exibida/editada nas respectivas ferramentas de monitoramento. 
>  1. Todas as instâncias de alerta disparadas estarão disponíveis no Azure Monitor e no Azure Log Analytics. Atualmente, não há nenhuma maneira de escolher entre os dois ou ingerir somente os alertas disparados específicos.
>  1. Todos os alertas do System Center Operations Manager, Zabbix e Nagios têm o tipo de sinal "Desconhecido", uma vez que o tipo de telemetria subjacente não está disponível.
>  1. Os alertas do Nagios não têm estado – por exemplo, a [condição de monitoramento](https://aka.ms/azure-alerts-overview) de um alerta não mudará de "Disparado" para "Resolvido". Em vez disso, "Disparado" e "Resolvido" são exibidos como instâncias de alerta separadas. 

