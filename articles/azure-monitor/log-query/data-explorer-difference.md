---
title: Diferenças na linguagem de consulta de log do Azure Monitor | Microsoft Docs
description: Informações de referência para a linguagem de consulta Kusto usada pelo Azure Monitor. Inclui elementos adicionais específicos ao Azure Monitor e elementos sem suporte às consultas do Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/01/2020
ms.openlocfilehash: 265179909c8ae4a6fa630b835bc9993f042d6460
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585706"
---
# <a name="azure-monitor-log-query-language-differences"></a>Diferenças na linguagem de consulta de log do Azure Monitor

Enquanto os [logs no Azure Monitor](log-query-overview.md) são criados no [Azure Data Explorer](/azure/data-explorer) e usam a [mesma linguagem de consulta Kusto](/azure/kusto/query), a versão da linguagem tem algumas diferenças. Este artigo identifica os elementos que são diferentes entre a versão do idioma usado para o Data Explorer e a versão usada para consultas de log do Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Elementos KQL não suportados no Azure Monitor
As seções a seguir descrevem os elementos da linguagem de consulta Kusto não compatíveis com o Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Instruções não compatíveis com o Azure Monitor

* [Alias](/azure/kusto/query/aliasstatement)
* [Parâmetros de consulta](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funções não compatíveis com o Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [banco de dados()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operadores não compatíveis com o Azure Monitor

* [Juntar-se em cluster sino](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Plug-ins não compatíveis com o Azure Monitor

* [Plugin Python](/azure/kusto/query/pythonplugin)
* [Plug-in do sql_request](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Operadores adicionais no Azure Monitor
Os seguintes operadores dão suporte a recursos específicos do Azure Monitor e não estão disponíveis fora do Azure Monitor.

* [aplicativo()](app-expression.md)
* [espaço de trabalho()](workspace-expression.md)

## <a name="next-steps"></a>Próximas etapas

- Obtenha referências para diferentes [recursos para escrever consultas de log do Azure Monitor](query-language.md).
- Acesse a [documentação de referência completa da linguagem de consulta Kusto](/azure/kusto/query/).
