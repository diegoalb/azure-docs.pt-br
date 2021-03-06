---
title: Recursos de vários modelos
description: O Banco de Dados SQL do Azure permite que você trabalhe com vários modelos de dados no mesmo banco de dados.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: 2e8519fa8d96b7fe016b9da4ba84ce481a57d94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73802825"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Recursos de multimodelos do Banco de Dados SQL do Azure

Os bancos de dados multimodelos permitem que você armazene e trabalhe com dados representados em vários formatos de dados como dados relacionais, grafos, documentos JSON/XML, pares chave-valor, etc.

## <a name="when-to-use-multi-model-capabilities"></a>Quando usar recursos multimodelos

O Banco de Dados SQL do Azure foi projetado para funcionar com o modelo relacional que fornece o melhor desempenho na maioria dos casos para uma variedade de aplicativos de uso geral. No entanto, o Banco de Dados SQL do Azure não limita-se apenas a dados relacionais. O Banco de Dados SQL do Azure permite usar uma variedade de formatos não relacionais que são totalmente integrados ao modelo relacional.
Você deve considerar o uso de recursos multimodelos do Banco de Dados SQL do Azure nos seguintes casos:
- Você tem algumas informações ou estruturas que são mais adequadas para modelos NoSQL e você não quer usar banco de dados NoSQL separado.
- A maioria dos seus dados é adequada para o modelo relacional, e você precisa modelar algumas partes de seus dados no estilo NoSQL.
- Você deseja aproveitar a linguagem transact-sql rica para consultar e analisar dados relacionais e NoSQL, e integrá-los com uma variedade de ferramentas e aplicativos que podem usar a linguagem SQL.
- Você deseja aplicar recursos de banco de dados, como [tecnologias na memória,](sql-database-in-memory.md) para melhorar o desempenho de suas estruturas de dados noSQL, usar [replicação transacional](sql-database-managed-instance-transactional-replication.md) ou [réplicas legíveis](sql-database-read-scale-out.md) para criar cópia de seus dados no outro lugar e descarregar algumas cargas de trabalho analíticas do banco de dados principal.

## <a name="overview"></a>Visão geral

O SQL do Azure fornece os seguintes recursos de multimodelos:
- Os [recursos de Grafo](#graph-features) permitem que você represente os dados como um conjunto de nós e bordas e use consultas Transact-SQL padrão aprimoradas com o operador do grafo `MATCH` para consultar os dados do grafo.
- Os [recursos JSON](#json-features) permitem que você coloque documentos JSON em tabelas, transforme dados relacionais em documentos JSON e vice-versa. Você pode usar a linguagem Transact-SQL padrão aprimorada com funções JSON para analisar documentos e usar índices não clusterizados, índices columnstore ou tabelas com otimização de memória para otimizar suas consultas.
- Os [Recursos espaciais](#spatial-features) permitem que você armazene dados geográficos e geométricos, indexe-os usando os índices espaciais e recupere os dados usando consultas espaciais.
- Os [Recursos XML](#xml-features) permitem que você armazene e indexe dados XML no banco de dados e use operações nativas XQuery/XPath para trabalhar com dados XML. O banco de dados SQL do Azure possui um mecanismo de consulta XML interno especializado que processa dados XML.
- [Os pares de valor-chave](#key-value-pairs) não são explicitamente suportados como recursos especiais, uma vez que os pares de valor-chave podem ser modelados nativamente como tabelas de duas colunas.

  > [!Note]
  > É possível usar expressões de Caminho JSON, expressões XQuery/XPath, funções espaciais e expressões de consulta de grafo na mesma consulta Transact-SQL para acessar quaisquer dados armazenados no banco de dados. Além disso, qualquer ferramenta ou linguagem de programação que possa executar consultas Transact-SQL também pode usar essa interface de consulta para acessar dados multimodelos. Essa é a principal diferença em comparação com os bancos de dados multimodelos como o [Azure Cosmos DB](/azure/cosmos-db/) que fornece API especializada para diferentes modelos de dados.

Nas seções a seguir, você aprenderá sobre os recursos de multimodelos mais importantes do Banco de Dados SQL do Azure.

## <a name="graph-features"></a>Recursos de grafo

O Banco de Dados SQL do Azure oferece recursos de banco de dados de grafo para modelar relações muitos para muitos no banco de dados. Um grafo é uma coleção de nós (ou vértices) e bordas (ou relações). Um nó representa uma entidade (por exemplo, uma pessoa ou uma organização) e uma borda representa uma relação entre os dois nós que ela conecta (por exemplo, curtidas ou amigos). Aqui estão alguns recursos que tornam um banco de dados de grafo único:
- Bordas ou relações são entidades de primeira classe em um Banco de Dados de Grafo e podem ter atributos ou propriedades associadas a elas.
- Uma única borda pode conectar com flexibilidade vários nós em um banco de dados de grafo.
- É possível expressar facilmente consultas de correspondência de padrões e navegação de salto múltiplo.
- É possível expressar fechamento transitivo e consultas polimórficas com facilidade.

Os recursos de consulta de grafo e as relações de grafo são integrados ao Transact-SQL e recebem os benefícios de usar o SQL Server como o sistema de gerenciamento de banco de dados fundamental.
O [Processamento de grafo](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) é o recurso principal do Mecanismo de Banco de Dados do SQL Server, portanto, é possível encontrar mais informações sobre o processamento do Grafo nesse local.

### <a name="when-to-use-a-graph-capability"></a>Quando usar um recurso do grafo

Não há nada que um banco de dados de grafo possa obter, que não possa ser obtido usando um banco de dados relacional. No entanto, um banco de dados de grafo pode facilitar a expressão de determinadas consultas. A decisão para a escolha de um ou de outro pode ser baseada nos seguintes fatores:

- Dados hierárquicos do modelo em que um nó pode ter vários pais, de modo que HierarchyId não pode ser usado
- Modelo do aplicativo tem relações muitos para muitos complexas, pois, conforme o aplicativo evolui, novas relações são adicionadas.
- É necessário analisar as relações e os dados interconectados.

## <a name="json-features"></a>Recursos JSON

O Banco de Dados SQL do Azure permite que você analise e consulte os dados representados no formato JavaScript Object Notation [(JSON)](https://www.json.org/) e exporte seus dados relacionais como texto JSON.

JSON é um formato de dados popular usado para a troca de dados em aplicativos Web modernos e móveis. O JSON também é usado para armazenar dados semi-estruturados em arquivos de log ou em bancos de dados NoSQL como [Banco de Dados do Azure Cosmos](https://azure.microsoft.com/services/cosmos-db/). Muitos serviços Web REST retornam resultados formatados como texto JSON ou aceitam dados formatados como JSON. A maioria dos serviços do Azure, como [Azure Cognitive Search,](https://azure.microsoft.com/services/search/) [Azure Storage](https://azure.microsoft.com/services/storage/)e [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) têm pontos finais REST que retornam ou consomem JSON.

O Banco de Dados SQL do Azure lhe permite trabalhar facilmente com dados JSON e integrar seu banco de dados com serviços modernos. O Banco de Dados SQL do Azure fornece as seguintes funções para trabalhar com dados JSON:

![Funções JSON](./media/sql-database-json-features/image_1.png)

Se você tiver texto JSON, pode extrair dados JSON ou verificar se o JSON está formatado corretamente usando as funções internas [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx) e [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). A função [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) permite que você atualize o valor dentro do texto JSON. Para consultas e análises mais avançadas, a função [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) pode transformar uma matriz de objetos JSON em um conjunto de linhas. Qualquer consulta SQL pode ser executada no conjunto de resultados retornado. Finalmente, há uma cláusula [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) que lhe permite formatar os dados armazenados nas tabelas relacionais como texto JSON.

Para obter mais informações, consulte [Como trabalhar com dados JSON no Banco de Dados SQL do Azure](sql-database-json-features.md).
O [JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) é o principal recurso do Mecanismo de Banco de Dados do Microsoft SQL Server, portanto, é possível encontrar mais informações sobre o recurso JSON nesse local.

### <a name="when-to-use-a-json-capability"></a>Quando usar um recurso JSON

Em alguns cenários específicos podem ser usados modelos de documentos, em vez dos modelos relacionais:
- A alta normalização do esquema não traz benefícios significativos porque você acessa todos os campos de objetos de uma vez, ou nunca atualiza partes normalizadas dos objetos. No entanto, o modelo normalizado aumenta a complexidade das consultas devido ao grande número de tabelas que precisam ser unidas para obter os dados.
- Você está trabalhando com os aplicativos que usam nativamente documentos JSON como modelos de dados ou comunicação e não quer introduzir camadas adicionais que transformam dados relacionais para JSON e vice-versa.
- É necessário simplificar o modelo de dados, desnormalizando tabelas secundárias ou padrões Entity-Object-Value.
- Você precisará carregar ou exportar dados armazenados no formato JSON sem ferramentas adicionais de análise de dados.

## <a name="spatial-features"></a>Recursos espaciais

Os dados espaciais representam informações sobre a localização física e a forma dos objetos geométricos. Esses objetos podem ser locais pontuais ou objetos mais complexos, como países/regiões, estradas ou lagos.

O Banco de Dados SQL do Azure oferece suporte a dois tipos de dados espaciais - tipo de dados Geometria e tipo de dados Geografia.
- O tipo Geometria representa dados em um sistema de coordenadas Euclidiano (plano).
- O tipo Geografia representa dados em um sistema de coordenadas esféricas.

Há vários objetos Espaciais que podem ser usados no banco de dados SQL do Azure, como [Point](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [Polygon](https://docs.microsoft.com/sql/relational-databases/spatial/polygon), etc.

O Banco de Dados SQL do Azure também fornece [índices Espaciais](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) que podem ser usados para melhorar o desempenho das consultas espaciais.

O [Suporte espacial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) é o principal recurso do Mecanismo de Banco de Dados do Microsoft SQL Server, portanto, é possível encontrar mais informações sobre o recurso espacial nesse local.

## <a name="xml-features"></a>Recursos XML

O SQL Server fornece uma poderosa plataforma de desenvolvimento de aplicativos avançados para gerenciamento de dados semiestruturados. O suporte para XML é integrado a todos os componentes no SQL Server e inclui o seguinte:

- O tipo de dados xml. Os valores XML podem ser armazenados nativamente em uma coluna de tipo de dados xml que pode ser tipada de acordo com uma coleção de esquemas XML ou deixada sem tipo. É possível indexar a coluna XML.
- A capacidade de especificar uma consulta XQuery em relação a dados XML armazenados em colunas e variáveis do tipo xml. As funcionalidades XQuery podem ser usadas em qualquer consulta Transact-SQL que acesse qualquer modelo de dados usado no banco de dados.
- Indexar automaticamente todos os elementos em documentos XML usando [índice XML primário](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) ou especificar os caminhos exatos que devem ser indexados usando [índice XML secundário](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET que permite o carregamento em massa de dados XML.
- Transforme dados relacionais em formato XML.

O [XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) é o principal recurso do Mecanismo de Banco de Dados do Microsoft SQL Server, portanto, é possível encontrar mais informações sobre o recurso XML nesse local.

### <a name="when-to-use-an-xml-capability"></a>Quando usar um recurso XML

Em alguns cenários específicos podem ser usados modelos de documentos, em vez dos modelos relacionais:
- A alta normalização do esquema não traz benefícios significativos porque você acessa todos os campos de objetos de uma vez, ou nunca atualiza partes normalizadas dos objetos. No entanto, o modelo normalizado aumenta a complexidade das consultas devido ao grande número de tabelas que precisam ser unidas para obter os dados.
- Você está trabalhando com os aplicativos que usam nativamente documentos XML como modelos de dados ou comunicação e não quer introduzir camadas adicionais que transformam dados relacionais para XML e vice-versa.
- É necessário simplificar o modelo de dados, desnormalizando tabelas secundárias ou padrões Entity-Object-Value.
- Você precisará carregar ou exportar dados armazenados no formato XML sem ferramentas adicionais de análise de dados.

## <a name="key-value-pairs"></a>Pares chave-valor

O Banco de Dados SQL do Azure não possui tipos ou estruturas especializados que dão suporte a pares chave-valor, pois as estruturas de chave-valor podem ser representadas nativamente como tabelas relacionais padrão:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

É possível personalizar essa estrutura de chave-valor para atender às suas necessidades sem restrições. Como exemplo, o valor pode ser um documento XML em vez de um tipo `nvarchar(max)` e, se o valor for um documento JSON, você poderá colocar a restrição `CHECK` que verificará a validade do conteúdo JSON. É possível colocar qualquer número de valores relacionados a uma chave nas colunas adicionais, adicionar colunas e índices calculados para simplificar e otimizar o acesso a dados, definir a tabela como tabela somente de esquema otimizada/memória para obter melhor desempenho, etc.

Consulte [como o BWin está usando OLTP in-memory para obter escala e desempenho sem precedentes](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) para a solução de cache do ASP.NET que obteve 1.200.000 lotes por segundo, como um exemplo de como o modelo relacional pode ser efetivamente usado como solução de par chave-valor na prática.

## <a name="next-steps"></a>Próximas etapas
Os recursos de multimodelos nos Bancos de Dados SQL do Azure também são os principais recursos do Mecanismo de Banco de Dados do Microsoft SQL Server compartilhados entre o Banco de Dados SQL do Azure e o SQL Server. Para obter mais detalhes sobre esses recursos, visite as páginas de documentação do banco de dados Relacional do SQL:

* [Processamento de grafo](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [Dados JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Suporte espacial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [Dados XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
