---
title: Gerenciar recurso de computação para pool SQL
description: Aprenda sobre os recursos de escala de desempenho em um pool Synapse Analytics Azure. Escalar horizontalmente ajustando DWUs, ou reduzir os custos pausando o data warehouse.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: daf57c7e6ef40f75eac070c06547cf2a28338f21
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633235"
---
# <a name="manage-compute-in-azure-synapse-analytics-data-warehouse"></a>Gerenciar computação no data warehouse do Azure Synapse Analytics

Aprenda a gerenciar recursos de computação no pool SQL do Azure Synapse Analytics. Reduza os custos parando o pool SQL ou dimensione o data warehouse para atender às demandas de desempenho.

## <a name="what-is-compute-management"></a>O que é o gerenciamento de computação?

A arquitetura do data warehouse separa o armazenamento e a computação, permitindo que cada um seja escalado de forma independente. Como resultado, é possível dimensionar o cálculo para atender às demandas de desempenho independentes do armazenamento de dados. Além disso, você também pode pausar e retomar os recursos de computação. Uma consequência natural dessa arquitetura é que a [cobrança](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) pela computação e pelo armazenamento é separada. Se não for necessário usar o data warehouse por um tempo, você poderá economizar os custos de computação, pausando a computação.

## <a name="scaling-compute"></a>Dimensionar computação

Você pode dimensionar ou dimensionar a computação de volta ajustando a configuração das unidades do [data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) para o seu pool SQL. O desempenho de consultas e carregamento pode aumentar linearmente na medida em que você adicionar mais unidades de data warehouse.

Para etapas de escala horizontal, consulte os inícios rápidos do [Portal do Azure](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md) ou [T-SQL](quickstart-scale-compute-tsql.md). Também é possível executar operações de escala horizontal com uma [API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Para executar uma operação de escala, o pool SQL primeiro mata todas as consultas recebidas e, em seguida, reverte as transações para garantir um estado consistente. A escala ocorrerá somente depois que a reversão de transação estiver completa. Para uma operação em escala, o sistema destaca a camada de armazenamento dos nós de computação, adiciona nós de computação e, em seguida, reconecta a camada de armazenamento à camada Computação. Cada pool SQL é armazenado como 60 distribuições, que são distribuídas uniformemente aos nós de computação. Adicionar mais nós de computação adiciona mais poder de computação. À medida que o número de nós de computação aumenta, o número de distribuições por nó de computação diminui, fornecendo mais poder de computação para suas consultas. Da mesma forma, a diminuição das unidades de data warehouse reduz o número de nódulos computacionais, o que reduz os recursos de computação para consultas.

A tabela a seguir mostra como o número de distribuições por nó de Computação altera na medida em que as unidades do data warehouse mudam.  DW30000c fornece 60 nós de computação e alcança um desempenho de consulta muito maior do que o DW100c.

| Unidades de data warehouse  | \#de nódulos de computação | \# de distribuições por nó |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |

## <a name="finding-the-right-size-of-data-warehouse-units"></a>Localizar o tamanho correto das unidades de data warehouse

Para ver os benefícios de desempenho de escalamento horizontal, especialmente para unidades de data warehouse grandes, convém usar pelo menos um conjunto de dados de 1 TB. Para encontrar o melhor número de unidades de data warehouse para o seu pool SQL, tente escalar para cima e para baixo. Execute algumas consultas com diferentes números de unidades de data warehouse após o carregamento dos dados. Como o dimensionamento é rápido, você pode experimentar vários níveis de desempenho diferentes durante uma hora ou menos.

Recomendações para localizar o melhor número de unidades de data warehouse:

- Para um pool SQL em desenvolvimento, comece selecionando um número menor de unidades de data warehouse.  Um bom ponto de partida é DW400c ou DW200c.
- Monitore o desempenho do aplicativo, observando o número de unidades de data warehouse selecionadas em comparação com o desempenho observado.
- Suponha uma escala linear e determine quanto é necessário para aumentar ou diminuir as unidades do data warehouse.
- Continue fazendo ajustes até alcançar um nível de desempenho ideal para seus requisitos de negócios.

## <a name="when-to-scale-out"></a>Quando escalar horizontalmente

Escalar dimensionalmente unidades de data warehouse afeta os seguintes aspectos do desempenho:

- Melhora o desempenho linear do sistema para exames, agregações e instruções de CTAS.
- Aumenta o número de leitores e gravadores para carregamento de dados.
- Número máximo de consultas simultâneas e slots de simultaneidade.

Recomendações para quando escalar horizontalmente unidades de data warehouse:

- Antes de executar uma operação de transformação ou carregamento de dados pesados, escale horizontalmente para tornar os dados disponíveis mais rapidamente.
- Durante o horário comercial de pico, escale dimensionalmente para acomodar um número de consultas simultâneas maior.

## <a name="what-if-scaling-out-does-not-improve-performance"></a>E se escalar verticalmente não melhorar o desempenho?

Adicionar unidades de data warehouse aumentando o paralelismo. Se o trabalho for dividido uniformemente entre os nós de Computação, o paralelismo adicional irá melhorar o desempenho de consultas. Se ao escalar verticalmente o desempenho não melhorar, há alguns motivos pelos quais isso pode acontecer. Os dados podem estar distorcidos nas distribuições ou as consultas podem estar introduzindo uma grande quantidade de movimentação de dados. Para investigar os problemas de desempenho de consultas, consulte [ Solução de problemas de desempenho](sql-data-warehouse-troubleshoot.md#performance).

## <a name="pausing-and-resuming-compute"></a>Pausa e retomada de computação

Pausar a computação faz a camada de armazenamento desanexar dos nós de Computação. Os recursos de cálculo são liberados da sua conta. Você não será cobrado pela computação enquanto a computação estiver em pausa. Retomar a computação reanexa o armazenamento nos nós de Computação e retoma as cobranças de Computação.
Quando você pausa uma piscina SQL:

- Recursos de computação e memória são retornados ao pool de recursos disponíveis no data center
- Os custos da unidade de data warehouse serão nulos durante a pausa.
- O armazenamento de dados não é afetado e seus dados permanecem intactos.
- Todas as operações em execução ou enfileiradas são canceladas.

Quando você retoma um pool SQL:

- O pool SQL adquire recursos de computação e memória para a configuração de unidades do data warehouse.
- Os encargos de computação para as unidades de data warehouse são retomados.
- Os dados tornam-se disponíveis.
- Depois que o pool SQL estiver on-line, você precisa reiniciar suas consultas de carga de trabalho.

Se você sempre quiser que seu pool SQL seja acessível, considere dimensioná-lo para o menor tamanho em vez de fazer uma pausa.

Para etapas de pausa e retomada, consulte os inícios rápidos do [Portal do Azure](pause-and-resume-compute-portal.md) ou [PowerShell](pause-and-resume-compute-powershell.md). Também é possível usar a [API REST de pausa](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) ou a [API REST de retomada](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Esvaziar as transações antes de pausar ou dimensionar

É recomendável que as transações existentes sejam concluídas antes de iniciar uma operação de pausa ou de escala.

Quando você pausa ou escala seu pool SQL, nos bastidores suas consultas são canceladas quando você inicia a solicitação de pausa ou escala. Cancelar uma simples consulta SELECT é uma operação rápida e quase não terá impacto sobre o tempo que leva para pausar ou dimensionar sua instância.  No entanto, as consultas transacionais, que modificam os dados ou a estrutura dos dados, não conseguirão parar rapidamente. **As consultas transacionais, por definição, devem ser concluídas na íntegra ou reverter suas alterações.**  Reverter o trabalho concluído por uma consulta transacional pode levar tanto tempo, ou até mais, quanto a alteração original que a consulta estava aplicando. Por exemplo, se você cancelar uma consulta que estava excluindo linhas que já estava em execução por uma hora, poderá levar uma hora para o sistema inserir de volta as linhas que foram excluídas. Se você executar a pausa ou o dimensionamento enquanto as transações estiverem em andamento, a pausa ou o dimensionamento poderão demorar muito tempo porque eles têm que esperar a reversão ser concluída antes de prosseguir.

Consulte também [Noções básicas sobre transações](sql-data-warehouse-develop-transactions.md) e [Otimizar transações](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatizar o gerenciamento de computação

Para automatizar as operações de gerenciamento de computação, consulte [Gerenciar computação com o Azure Functions](manage-compute-with-azure-functions.md).

Cada operação de escala horizontal, pausa e retomada pode demorar vários minutos para ser concluída. Se você está escalando, pausando ou retomando automaticamente, é recomendável implementar a lógica para assegurar que determinadas operações tenham sido concluídas antes de prosseguir com outra ação. Verificar o estado do pool SQL através de vários pontos finais permite implementar corretamente a automação de tais operações.

Para verificar o estado do pool SQL, consulte o [powershell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) ou [t-sql](quickstart-scale-compute-tsql.md#check-data-warehouse-state) quickstart. Você também pode verificar o estado do pool SQL com uma [API REST](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).

## <a name="permissions"></a>Permissões

O dimensionamento do pool SQL requer as permissões descritas no [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Pausar e Retomar exige a permissão [Contribuidor do DB SQL](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#sql-db-contributor), especificamente Microsoft.Sql/servers/databases/action.

## <a name="next-steps"></a>Próximas etapas

Veja como orientar para [gerenciar a computação](manage-compute-with-azure-functions.md) Outro aspecto do gerenciamento de recursos computacionais é a alocação de diferentes recursos de computação para consultas individuais. Para obter mais informações, consulte [Classes de recurso para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md).
