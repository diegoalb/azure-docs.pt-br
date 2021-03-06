---
title: Recurso dtu limita piscinas elásticas
description: Esta página descreve alguns limites comuns de recursos DTU para pools elásticos no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
ms.date: 03/14/2019
ms.openlocfilehash: 7b7ef3b6f2d400dafb28cfb7a15cf95cbbe2c457
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351019"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Limites de recursos para piscinas elásticas usando o modelo de compras da DTU

Este artigo fornece os limites de recursos detalhados para os grupos elásticos do Banco de Dados Azure SQL e bancos de dados agrupados usando o modelo de compra do DTU.

Para obter limites de recursos do modelo de compra do DTU para bancos de dados únicos, consulte [os limites de recursos do DTU - bancos de dados únicos](sql-database-vcore-resource-limits-elastic-pools.md). Para os limites de recursos vCore, consulte [os limites de recursos do vCore - bancos de dados únicos](sql-database-vcore-resource-limits-single-databases.md) e limites de recursos [vCore - pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Pool elástico: tamanhos de armazenamento e de computação

Para pools elásticos do Banco de Dados SQL, as tabelas a seguir mostram os recursos disponíveis em cada tamanho da computação e camada de serviço. Você pode definir a camada de serviço, o tamanho da computação e a quantidade de armazenamento usando o [portal do Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), o [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), a [CLI do Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases) ou a [API REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Para orientação de dimensionamento e considerações, consulte [Dimensionar uma piscina elástica](sql-database-elastic-pool-scale.md)
> [!NOTE]
> Os limites de recursos de bancos de dados individuais em pools elásticos geralmente são os mesmos dos bancos de dados individuais fora dos pools com base em DTUs e na camada de serviço. Por exemplo, máximo de trabalhos simultâneos para um banco de dados S2 é 120. Assim, o máximo de trabalhos simultâneos para um banco de dados em um pool padrão também será 120 se o máximo de DTUs por banco de dados no pool for 50 DTUs (o que é equivalente a S2).

### <a name="basic-elastic-pool-limits"></a>Limites de pool elástico Básico

| eDTUs por pool | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Armazenamento incluído por pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Opções de máximo de armazenamento por pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Armazenamento máximo OLTP na memória por pool (GB) | N/D | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| Número máximo de DBs por pool <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Trabalhadores simultâneos máximos (pedidos) por pool <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Máximo de sessões simultâneas por pool <sup>2</sup> | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Opções de mínimo de eDTUs por banco de dados | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Opções de máximo de eDTUs por banco de dados | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Armazenamento máximo por banco de dados (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> Consulte [o gerenciamento de recursos em piscinas elásticas densas](sql-database-elastic-pool-resource-management.md) para considerações adicionais.

<sup>2</sup> Para os trabalhadores simultâneos máximos (solicitações) para qualquer banco de dados individual, consulte [Limites de recursos de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando gen5 e o max vCore por banco de dados for definido em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o max vCore por banco de dados estiver definido como 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, já que no Gen5 há um máximo de 100 trabalhadores simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="standard-elastic-pool-limits"></a>Limites de pool elástico Standard

| eDTUs por pool | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Armazenamento incluído por pool (GB) | 50 | 100 | 200 | 300 | 400 | 800 |
| Opções de máximo de armazenamento por pool (GB) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 |
| Armazenamento máximo OLTP na memória por pool (GB) | N/D | N/D | N/D | N/D | N/D | N/D |
| Número máximo de DBs por pool <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 |
| Trabalhadores simultâneos máximos (pedidos) por pool <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 |
| Máximo de sessões simultâneas por pool <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Opções de mínimo de eDTUs por banco de dados | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Opções de máximo de eDTUs por banco de dados | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Armazenamento máximo por banco de dados (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> Consulte [o gerenciamento de recursos em piscinas elásticas densas](sql-database-elastic-pool-resource-management.md) para considerações adicionais.

<sup>2</sup> Para os trabalhadores simultâneos máximos (solicitações) para qualquer banco de dados individual, consulte [Limites de recursos de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando gen5 e o max vCore por banco de dados for definido em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o max vCore por banco de dados estiver definido como 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, já que no Gen5 há um máximo de 100 trabalhadores simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="standard-elastic-pool-limits-continued"></a>Limites de pool elástico Standard (continuação)

| eDTUs por pool | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por pool (GB) | 1200 | 1600 | 2000 | 2500 | 3000 |
| Opções de máximo de armazenamento por pool (GB) | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Armazenamento máximo OLTP na memória por pool (GB) | N/D | N/D | N/D | N/D | N/D |
| Número máximo de DBs por pool <sup>1</sup> | 500 | 500 | 500 | 500 | 500 |
| Trabalhadores simultâneos máximos (pedidos) por pool <sup>2</sup> | 2400 | 3200 | 4000 | 5.000 | 6000 |
| Máximo de sessões simultâneas por pool <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Opções de mínimo de eDTUs por banco de dados | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Opções de máximo de eDTUs por banco de dados | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Opções de máximo de armazenamento por banco de dados (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Consulte [o gerenciamento de recursos em piscinas elásticas densas](sql-database-elastic-pool-resource-management.md) para considerações adicionais.

<sup>2</sup> Para os trabalhadores simultâneos máximos (solicitações) para qualquer banco de dados individual, consulte [Limites de recursos de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando gen5 e o max vCore por banco de dados for definido em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o max vCore por banco de dados estiver definido como 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, já que no Gen5 há um máximo de 100 trabalhadores simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="premium-elastic-pool-limits"></a>Limites de pool elástico Premium

| eDTUs por pool | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por pool (GB) | 250 | 500 | 750 | 1024 | 1536 |
| Opções de máximo de armazenamento por pool (GB) | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Armazenamento máximo OLTP na memória por pool (GB) | 1 | 2 | 4 | 10 | 12 |
| Número máximo de DBs por pool <sup>1</sup> | 50 | 100 | 100 | 100 | 100 |
| Trabalhadores simultâneos máximos por piscina (solicitações) <sup>2</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Máximo de sessões simultâneas por pool <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Mínimo de eDTUs por banco de dados | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| Máximo de eDTUs por banco de dados | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Armazenamento máximo por banco de dados (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Consulte [o gerenciamento de recursos em piscinas elásticas densas](sql-database-elastic-pool-resource-management.md) para considerações adicionais.

<sup>2</sup> Para os trabalhadores simultâneos máximos (solicitações) para qualquer banco de dados individual, consulte [Limites de recursos de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando gen5 e o max vCore por banco de dados for definido em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o max vCore por banco de dados estiver definido como 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, já que no Gen5 há um máximo de 100 trabalhadores simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

### <a name="premium-elastic-pool-limits-continued"></a>Limites de pool elástico Premium (continuação)

| eDTUs por pool | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por pool (GB) | 2.048 | 2560 | 3072 | 3548 | 4096 |
| Opções de máximo de armazenamento por pool (GB) | 2.048 | 2560 | 3072 | 3548 | 4096|
| Armazenamento máximo OLTP na memória por pool (GB) | 16 | 20 | 24 | 28 | 32 |
| Número máximo de DBs por pool <sup>1</sup> | 100 | 100 | 100 | 100 | 100 |
| Trabalhadores simultâneos máximos (pedidos) por pool <sup>2</sup> | 3200 | 4000 | 4800 | 5600 | 6400 |
| Máximo de sessões simultâneas por pool <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Opções de mínimo de eDTUs por banco de dados | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Opções de máximo de eDTUs por banco de dados | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Armazenamento máximo por banco de dados (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Consulte [o gerenciamento de recursos em piscinas elásticas densas](sql-database-elastic-pool-resource-management.md) para considerações adicionais.

<sup>2</sup> Para os trabalhadores simultâneos máximos (solicitações) para qualquer banco de dados individual, consulte [Limites de recursos de banco de dados único](sql-database-vcore-resource-limits-single-databases.md). Por exemplo, se o pool elástico estiver usando gen5 e o max vCore por banco de dados for definido em 2, então o valor máximo dos trabalhadores simultâneos é de 200.  Se o max vCore por banco de dados estiver definido como 0,5, então o valor máximo dos trabalhadores simultâneos é de 50, já que no Gen5 há um máximo de 100 trabalhadores simultâneos por vCore. Para outras configurações de máximo de vCore por banco de dados que sejam menores que 1 vCore, o número máximo de trabalhos simultâneos é redimensionado de forma semelhante.

> [!IMPORTANT]
> Mais de 1 TB de armazenamento no nível Premium está atualmente disponível em todas as regiões, exceto: China East, China North, Germany Central, Germany Northeast, West Central US, US DoD regiões e US Government Central. Nessas regiões, o armazenamento máximo na camada Premium é limitado a 1 TB.  Para obter mais informações, confira [Limitações atuais de P11-P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Se todas as DTUs de um pool elástico forem usadas, cada banco de dados no pool receberá uma quantidade igual de recursos para processar as consultas. O serviço de Banco de Dados SQL fornece integridade de compartilhamento de recursos entre os bancos de dados ao garantir fatias iguais de tempo de computação. A integridade de compartilhamento de recursos do pool elástico é adicional a qualquer quantidade de recursos garantidos de outra forma a cada banco de dados quando o mínimo de DTUs por banco de dados é definido com um valor diferente de zero.

> [!NOTE]
> Para `tempdb` limites, consulte [os limites de temperatura .](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)

### <a name="database-properties-for-pooled-databases"></a>Propriedades do banco de dados para bancos de dados em pool

A tabela a seguir descreve as propriedades dos bancos de dados em pool.

| Propriedade | Descrição |
|:--- |:--- |
| Máximo de eDTUs por banco de dados |O número máximo de eDTUs que qualquer banco de dados no pool pode usar, se disponível, com base na utilização por outros bancos de dados no pool. O máximo de eDTUs por banco de dados não é uma garantia de recursos para um banco de dados. Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. Defina um valor para o máximo de eDTUs por banco de dados que seja alto o suficiente para lidar com picos de utilização do banco de dados. Espera-se um grau de sobrecarga, uma vez que o pool normalmente assume padrões de uso dos bancos de dados com altos e baixos, em que todos os bancos de dados não atingem um pico simultaneamente. Por exemplo, suponha que o pico de utilização por banco de dados seja de 20 eDTUs e apenas 20% dos 100 bancos de dados no pool atinjam o pico simultaneamente. Se o máximo de eDTUs por banco de dados for definido para 20 eDTUs, será razoável sobrecarregar o pool em 5 vezes e definir os eDTUs por pool como 400. |
| Mínimo de eDTUs por banco de dados |O número mínimo de eDTUs garantido para qualquer banco de dados no pool. Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. O mínimo de eDTUs por banco de dados pode ser definido como 0 e também é o valor padrão. Essa propriedade é definida entre 0 e a utilização média de eDTUs por banco de dados. O produto do número de bancos de dados no pool e o mínimo de eDTUs por banco de dados não pode exceder os eDTUs por pool. Por exemplo, se um pool tiver 20 bancos de dados e o mínimo de eDTUs por banco de dados for definido como 10 eDTUs, os eDTUs por pool deverão ser de pelo menos 200 eDTUs. |
| Armazenamento máximo por banco de dados |O tamanho máximo do banco de dados definido pelo usuário para um banco de dados em um pool. No entanto, os bancos de dados em pool compartilham o armazenamento de pool alocado. Mesmo que o armazenamento máximo total *por banco* de dados seja definido como maior do que o espaço total de armazenamento disponível *do pool,* o espaço total realmente usado por todos os bancos de dados não será capaz de exceder o limite de pool disponível. O tamanho máximo por banco de dados refere-se ao tamanho máximo dos arquivos de dados e não inclui o espaço usado pelos arquivos de log. |
|||

## <a name="next-steps"></a>Próximas etapas

- Para obter limites de recursos vCore para um único banco de dados, consulte [limites de recursos para bancos de dados únicos usando o modelo de compra do vCore](sql-database-vcore-resource-limits-single-databases.md)
- Para limites de recursos do DTU para um único banco de dados, consulte [limites de recursos para bancos de dados únicos usando o modelo de compra do DTU](sql-database-dtu-resource-limits-single-databases.md)
- Para os limites de recursos vCore para piscinas elásticas, consulte [limites de recursos para piscinas elásticas usando o modelo de compra vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Para limites de recurso das instâncias gerenciadas, confira os [limites de recurso para instâncias gerenciadas](sql-database-managed-instance-resource-limits.md).
- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Para se informar sobre os limites de recursos em um servidor de banco de dados, confira a [visão geral dos limites de recursos em um servidor do Banco de Dados SQL](sql-database-resource-limits-database-server.md) para conferir os limites nos níveis do servidor e da assinatura.
