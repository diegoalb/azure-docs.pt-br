---
title: ST_ISVALIDDETAILED no idioma de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ST_ISVALIDDETAILED no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e640c223c2fef844b9b53e1f4afa3a5d398c8c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349344"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
 Retorna um valor JSON que contém um valor Booliano caso a expressão especificada de Ponto, Polígono ou LineString GeoJSON é válida e, se for inválida, adicionalmente o motivo como um valor de cadeia de caracteres.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*spatial_expr*  
   É um ponto GeoJSON ou expressão polígono.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna um valor JSON que contém um valor Booliano caso a expressão de ponto ou polígono GeoJSON especificada é válida e, se for inválida, adicionalmente o motivo como um valor de cadeia de caracteres.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir como verificar a `ST_ISVALIDDETAILED`validade (com detalhes) usando .  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Próximas etapas

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
