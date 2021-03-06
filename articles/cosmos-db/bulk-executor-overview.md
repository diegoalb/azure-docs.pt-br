---
title: Visão geral da biblioteca bulk executor do Azure Cosmos DB
description: Execute as operações em massa no Azure Cosmos DB por meio da importação em massa e APIs oferecidas pela biblioteca de executor em massa de atualização em massa.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 9d335bcf6daf0b38e7a68ca2d40894dd64c93e40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442148"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Visão geral da biblioteca bulk executor do Azure Cosmos DB
 
O Azure Cosmos DB é um serviço de banco de dados rápido, flexível e distribuído globalmente que foi projetado para elasticidade de expansão para dar suporte a: 

* Grandes taxas de transferência de leitura e gravação (em milhões de operações por segundo).  
* Armazenamento de grandes volumes de transações e dados operacionais (centenas de terabytes ou mais) com latência previsível de milissegundo.  

A biblioteca de executor em massa ajuda você a aproveitar essa enorme produtividade e armazenamento. A biblioteca de executor permite que você realize operações em massa no Azure Cosmos DB através da importar em massa e APIs de atualização em massa. Você pode ler mais sobre os recursos da biblioteca bulk executor nas seções a seguir. 

> [!NOTE] 
> Atualmente, a biblioteca de executor em massa dará suporte à importação e operações de atualização e essa biblioteca dará suporte apenas para contas de API de SQL do Azure Cosmos DB e API do Gremlin.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Os principais recursos da biblioteca bulk executor  
 
* Reduz significativamente os recursos de computação do lado do cliente necessários para saturar a taxa de transferência alocada para um contêiner. Um aplicativo de thread único que grava dados usando a API de importação em massa atinge 10 vezes maior taxa de transferência de gravação quando comparado a um aplicativo multithread que grava dados em paralelo ao saturar a CPU do computador do cliente.  

* Abstrai tarefas entediantes de escrever uma lógica de aplicativo para lidar com a limitação de solicitação, tempos limite de solicitação e outras exceções transitórias tratando-os com eficiência na biblioteca.  

* Ele fornece um mecanismo simplificado para aplicações que realizam operações em massa para dimensionar. Uma única instância de executor em massa em execução em uma VM Azure pode consumir mais de 500K RU/s e você pode obter uma taxa de throughput mais alta adicionando instâncias adicionais em VMs individuais do cliente.  
 
* Pode importar em massa mais de um terabyte de dados em uma hora usando uma arquitetura de expansão.  

* Ele pode atualizar em massa os dados existentes nos contêineres do Azure Cosmos como patches. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Como funciona o executor a granel? 

Quando uma operação em massa para importar ou atualizar documentos é disparada com um lote de entidades, eles são inicialmente embaralhados em segmentos correspondentes aos seus intervalos de chaves de partição do Azure Cosmos DB. Dentro de cada partição que corresponde a um intervalo de chave de partição, eles são divididos em minilotes e cada minilote atua como uma carga que é confirmada no lado do servidor. A biblioteca bulk executor criou otimizações para execução simultânea desses minilotes dentro e entre os intervalos de chave de partição. A imagem a seguir ilustra como bulk executor divide os dados de lotes em chaves de partição diferentes:  

![Arquitetura do BulkExecutor](./media/bulk-executor-overview/bulk-executor-architecture.png)

A biblioteca executor a granel garante utilizar ao máximo o throughput alocado em uma coleção. Ela usa um  [mecanismo de controle de congestionamento do estilo AIMD](https://tools.ietf.org/html/rfc5681) para cada intervalo de chave de partição do Azure Cosmos DB para tratar com eficiência a limitação e os tempos limite. 

## <a name="next-steps"></a>Próximas etapas 
  
* Saiba mais experimentando os aplicativos de amostra que consomem a biblioteca de executores em massa em [.NET](bulk-executor-dot-net.md) e [Java](bulk-executor-java.md).  
* Confira as informações e notas de versões do SDK de bulk executor no [.NET](sql-api-sdk-bulk-executor-dot-net.md) e [Java](sql-api-sdk-bulk-executor-java.md).
* A biblioteca executora em massa é integrada ao conector Cosmos DB Spark, para saber mais, consulte o artigo [do conector Azure Cosmos DB Spark.](spark-connector.md)  
* A biblioteca bulk executor também é integrada a uma nova versão do [conector do Azure Cosmos DB](https://aka.ms/bulkexecutor-adf-v2) para o Azure Data Factory copiar dados.
