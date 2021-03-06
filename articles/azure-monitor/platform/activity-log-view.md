---
title: Veja eventos de registro de atividades do Azure no Monitor do Azure
description: Veja o registro de atividade do Azure no Monitor Azure e recupere com powershell, CLI e API REST.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: d2423d04ead9040cce53d847d24efe75be680d94
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397315"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Exibir e recuperar eventos de registro de atividades do Azure

O [Azure Activity Log](platform-logs-overview.md) fornece informações sobre eventos de nível de assinatura que ocorreram no Azure. Este artigo fornece detalhes sobre diferentes métodos para visualizar e recuperar eventos do Registro de Atividades.

## <a name="azure-portal"></a>Portal do Azure
Exibir o Registro de Atividades para todos os recursos do menu **Monitor** no portal Azure. Exibir o Registro de atividades para um recurso específico da opção Registro de **atividades** no menu desse recurso.

![Exibir registro de atividades](./media/activity-logs-overview/view-activity-log.png)

Você pode filtrar eventos do Registro de Atividades pelos seguintes campos:

* **Timespan**: O horário de início e fim dos eventos.
* **Categoria**: A categoria do evento conforme descrito em [Categorias no Registro de Atividades](activity-log-view.md#categories-in-the-activity-log).
* **Assinatura**: Um ou mais nomes de assinatura do Azure.
* **Grupo de recursos**: Um ou mais grupos de recursos dentro das assinaturas selecionadas.
* **Recurso (nome)**: - O nome de um recurso específico.
* **Tipo de**recurso : O tipo de recurso, por exemplo _Microsoft.Compute/virtualmachines_.
* **Nome de operação** - O nome de uma operação do Azure Resource Manager, por _exemplo, Microsoft.SQL/servers/Write_.
* **Gravidade**: O nível de gravidade do evento. Os valores disponíveis são _Informativos,_ _Aviso,_ _Erro,_ _Crítico._
* **Evento iniciado por**: O usuário que realizou a operação.
* **Abrir pesquisa**: Abra a caixa de pesquisa de texto que procura por essa seqüência em todos os campos em todos os eventos.

## <a name="categories-in-the-activity-log"></a>Categorias no registro de atividades
Cada evento no Registro de Atividades tem uma categoria específica que está descrita na tabela a seguir. Para obter todos os detalhes sobre o esquema dessas categorias, veja o [esquema de eventos de Log de Atividades do Azure](activity-log-schema.md). 

| Categoria | Descrição |
|:---|:---|
| Administrativo | Contém o registro de todas as operações de criação, atualização, exclusão e ação realizadas através do Gerenciador de Recursos. Exemplos de eventos administrativos incluem _criar máquina virtual_ e excluir grupo de segurança de _rede_.<br><br>Todas as ações tomadas por um usuário ou aplicativo usando o Resource Manager são modeladas como uma operação em um determinado tipo de recurso. Se o tipo de operação for _Write_, _Delete_ou _Action_, os registros do início e do sucesso ou falha dessa operação são registrados na categoria Administrativa. Eventos administrativos também incluem quaisquer alterações no controle de acesso baseado em função em uma assinatura. |
| Integridade do Serviço | Contém o registro de quaisquer incidentes de saúde que tenham ocorrido no Azure. Um exemplo de um evento de Saúde de Serviço _SQL Azure no leste dos EUA está experimentando tempo de inatividade_. <br><br>Os eventos de Saúde do Serviço vêm em seis variedades: _Ação Necessária,_ _Recuperação Assistida,_ _Incidente,_ _Manutenção,_ _Informação_ou _Segurança_. Esses eventos só são criados se você tiver um recurso na assinatura que seria impactado pelo evento.
| Integridade de recursos | Contém o registro de quaisquer eventos de saúde de recursos que tenham ocorrido aos seus recursos do Azure. Um exemplo de evento de saúde de recursos é _o status de saúde da Máquina Virtual alterado para indisponível_.<br><br>Os eventos de Saúde de Recursos podem representar um dos quatro status de saúde: _Disponível_, _Indisponível,_ _Degradado_e _Desconhecido_. Além disso, os eventos de Saúde de Recursos podem ser categorizados como sendo _Iniciados por Plataforma_ ou _Iniciadopelo Usuário._ |
| Alerta | Contém o registro de ativações para alertas Do Zure. Um exemplo de evento alert é _cpu % no myVM tem sido mais de 80 nos últimos 5 minutos_.|
| Autoscale | Contém o registro de quaisquer eventos relacionados à operação do mecanismo de escala automática com base em quaisquer configurações de escala automática definidas em sua assinatura. Um exemplo de evento autoscale é a falha de _ação de escala de escala auto-scale_. |
| Recomendação | Contém eventos de recomendação do Azure Advisor. |
| Segurança | Contém o registro de quaisquer alertas gerados pelo Azure Security Center. Um exemplo de um evento de segurança é _o arquivo de extensão dupla suspeito executado_. |
| Política | Contém registros de todas as operações de ação de efeito realizadas pela Azure Policy. Exemplos de eventos de política incluem _Auditoria_ e _Negar_. Cada ação tomada pelo Policy é modelada como uma operação em um recurso. |

## <a name="view-change-history"></a>Exibir histórico de alterações

Ao revisar o Registro de Atividades, ele pode ajudar a ver quais mudanças aconteceram durante esse tempo de evento. Você pode visualizar essas informações com **o histórico de alteração**. Selecione um evento no Registro de Atividades em que você deseja investigar mais profundamente. Selecione a guia **Histórico de alteração (Visualização)** para exibir quaisquer alterações associadas a esse evento.

![Alterar lista de história para um evento](media/activity-logs-overview/change-history-event.png)

Se houver alguma alteração associada ao evento, você verá uma lista de alterações que você pode selecionar. Isso abre a página **'Alterar histórico' (Visualização).** Nesta página você vê as alterações no recurso. Como você pode ver no exemplo a seguir, podemos ver não apenas que a VM mudou de tamanho, mas o tamanho da VM anterior era antes da mudança e para o que foi alterado.

![Alterar página de histórico mostrando diferenças](media/activity-logs-overview/change-history-event-details.png)

Para saber mais sobre o histórico de alterações, consulte [Obter alterações de recursos](../../governance/resource-graph/how-to/get-resource-changes.md).






## <a name="powershell"></a>PowerShell
Use o [cmdlet Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) para recuperar o registro de atividades do PowerShell. A seguir estão alguns exemplos comuns.

> [!NOTE]
> `Get-AzLog` fornece apenas 15 dias de histórico. Use o parâmetro **-MaxEvents** para consultar os últimos eventos N além de 15 dias. Para acessar eventos com mais de 15 dias, use a API REST ou SDK. Se você não incluir **StartTime**, o valor padrão será **EndTime** menos uma hora. Se você não incluir **EndTime**, o valor padrão será a hora atual. Todas as horas estão no padrão UTC.


Obter entradas de log criadas após uma determinada data:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Obtenha entradas de registro entre um intervalo de data:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter entradas de log de um grupo de recursos específico:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Obtenha entradas de log de um provedor de recursos específico entre um intervalo de data:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtenha entradas de log com um chamador específico:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Receba os últimos 1000 eventos:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
Use [o registro de atividades](cli-samples.md#view-activity-log-for-a-subscription) do monitor az para recuperar o registro de atividades da CLI. A seguir estão alguns exemplos comuns.


Veja todas as opções disponíveis.

```azurecli
az monitor activity-log list -h
```

Obter entradas de log de um grupo de recursos específico:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Obtenha entradas de log com um chamador específico:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Obtenha logs por chamador em um tipo de recurso, dentro de um intervalo de datas:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>API REST
Use a [API REST do Monitor Azure](https://docs.microsoft.com/rest/api/monitor/) para recuperar o Registro de atividades de um cliente REST. A seguir estão alguns exemplos comuns.

Obtenha registros de atividades com filtro:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Obtenha registros de atividades com filtro e selecione:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obtenha registros de atividades com seleção:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obtenha registros de atividades sem filtro ou selecione:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Próximas etapas

* [Leia uma visão geral dos logs da plataforma](platform-logs-overview.md)
* [Criar configuração de diagnóstico para enviar logs de atividade para outros destinos](diagnostic-settings.md)
