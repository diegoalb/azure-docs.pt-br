---
title: Visão geral da transformação do fluxo de dados de mapeamento
description: Uma visão geral das diferentes transformações disponíveis no mapeamento do fluxo de dados
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94f7277c1b741fb5ef6d2807fabbb266c0109415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086930"
---
# <a name="mapping-data-flow-transformation-overview"></a>Visão geral da transformação do fluxo de dados de mapeamento

Abaixo está uma lista das transformações atualmente suportadas no mapeamento do fluxo de dados. Clique em cada transformação para saber seus detalhes de configuração.

| Nome | Categoria | Descrição |
| ---- | -------- | ----------- |
| [Agregado](data-flow-aggregate.md) | Modificador de esquema | Defina diferentes tipos de agregações, como Soma, MIN, MAX e COUNT agrupadas por colunas existentes ou computadas. | 
| [Alterar linha](data-flow-alter-row.md) | Modificador de linha | Defina políticas de inserção, exclusão, atualização e upsert em linhas. |
| [Divisão condicional](data-flow-conditional-split.md) | Múltiplas entradas/saídas | Rotas de dados para diferentes fluxos com base em condições de correspondência. |
| [Coluna derivada](data-flow-derived-column.md) | Modificador de esquema | gerar novas colunas ou modificar campos existentes usando a linguagem de expressão de fluxo de dados. | 
| [Exists](data-flow-exists.md) | Múltiplas entradas/saídas | Verifique se seus dados existem em outra fonte ou fluxo. | 
| [Filtrar](data-flow-filter.md) | Modificador de linha | Filtrar uma linha com base em uma condição. |
| [Achatar](data-flow-flatten.md) | Modificador de esquema |  Pegue os valores de matriz dentro de estruturas hierárquicas como JSON e desenrole-os em linhas individuais. |
| [Ingressar](data-flow-join.md) | Múltiplas entradas/saídas |  Combine dados de duas fontes ou fluxos. |
| [Pesquisar](data-flow-lookup.md) | Múltiplas entradas/saídas | Dados de referência de outra fonte. |
| [Novo branch](data-flow-new-branch.md) | Múltiplas entradas/saídas | Aplique vários conjuntos de operações e transformações contra o mesmo fluxo de dados. |
| [Dinâmico](data-flow-pivot.md) | Modificador de esquema | Uma agregação onde uma ou mais colunas de agrupamento tem seus valores de linha distintos transformados em colunas individuais. |
| [Selecionar](data-flow-select.md) | Modificador de esquema | Alias colunas e nomes de fluxo e soltar ou reordenar colunas |
| [Coletor](data-flow-sink.md) | - | Um destino final para seus dados |
| [Classificar](data-flow-sort.md) | Modificador de linha | Classificar as linhas recebidas no fluxo de dados atual |
| [Fonte](data-flow-source.md) | - | Uma fonte de dados para o fluxo de dados |
| [Chave alternativa](data-flow-surrogate-key.md) | Modificador de esquema | Adicionar um valor-chave arbitrário não-comercial incrementado |
| [Union](data-flow-union.md) | Múltiplas entradas/saídas | Combine vários fluxos de dados verticalmente |
| [Não Dinâmico](data-flow-unpivot.md) | Modificador de esquema | Colunas pivôs em valores de linha |
| [Janela](data-flow-window.md) | Modificador de esquema |  Defina agregações baseadas em janelas de colunas em seus fluxos de dados. |
