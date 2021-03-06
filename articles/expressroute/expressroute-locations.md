---
title: 'Locais e provedores de conectividade: Azure ExpressRoute | Microsoft Docs'
description: Este artigo fornece uma visão geral detalhada dos locais onde os serviços são oferecidos e de como se conectar a regiões do Azure. Classificado por provedor de conectividade.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 6482040f0d50f08f04ea87384a99af556f694075
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478805"
---
# <a name="expressroute-partners-and-peering-locations"></a>Locais de emparelhamento e parceiros do ExpressRoute

> [!div class="op_single_selector"]
> * [Localizações por provedor](expressroute-locations.md)
> * [Provedores por local](expressroute-locations-providers.md)


As tabelas deste artigo fornecem informações sobre a cobertura geográfica e localizações da ExpressRoute, provedores de conectividade ExpressRoute e SIs (ExpressRoute System Integrators).

> [!Note]
> As regiões azure e as localizações do ExpressRoute são dois conceitos distintos e diferentes, entender a diferença entre os dois é fundamental para explorar a conectividade de rede híbrida do Azure. 
>
>

## <a name="azure-regions"></a>Regiões do Azure
As regiões do Azure são data centers globais onde os recursos de computação, rede e armazenamento do Azure estão localizados. Ao criar um recurso do Azure, um cliente precisa selecionar um local de recurso. A localização do recurso determina em qual data center do Azure (ou zona de disponibilidade) o recurso é criado.

## <a name="expressroute-locations"></a>Localizações do ExpressRoute
Os locais do ExpressRoute (às vezes chamados de locais de peering ou meet-me-locations) são instalações de co-localização onde os dispositivos Microsoft Enterprise Edge (MSEE) estão localizados. As localizações do ExpressRoute são o ponto de entrada da rede da Microsoft – e são distribuídas globalmente, proporcionando aos clientes a oportunidade de se conectar à rede da Microsoft em todo o mundo. Esses locais são onde os parceiros ExpressRoute e ExpressRoute Direct emitem conexões cruzadas à rede da Microsoft. Em geral, a localização do ExpressRoute não precisa corresponder à região do Azure. Por exemplo, um cliente pode criar um circuito ExpressRoute com a localização de recursos *East US*, no local do *Seattle* Peering.

Você terá acesso aos serviços do Azure em todas as regiões dentro de uma região geopolítica se estiver conectado a pelo menos um local de ExpressRoute dentro da região geopolítica.

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Regiões do Azure para locais de ExpressRoute em uma região geopolítica.
A tabela a seguir fornece um mapa das regiões do Azure para locais de ExpressRoute em uma região geopolítica.

| **Região geopolítica** | **Regiões azure** | **Localizações do ExpressRoute** |
| --- | --- | --- |
| **Governo da Austrália** |Austrália Central, Austrália Central 2 |Canberra, Canberra2 |
| **Europa** | França Central, França Sul, Alemanha Norte, Alemanha Centro-Oeste, Norte da Europa, Noruega Leste, Noruega Oeste, Suíça Norte, Suíça Oeste, Reino Unido Oeste, Reino Unido Sul, Europa Ocidental |Amsterdã, Amsterdã2, Copenhague, Dublin, Frankfurt, Genebra, Londres, Londres2, Marselha, Milão, Munique, Newport(País de Gales), Oslo, Paris, Stavanger, Estocolmo, Zurique |
| **América do Norte** |Leste dos EUA, Oeste dos EUA, Leste dos EUA 2, Oeste dos EUA 2, Centro dos EUA, Centro-Sul dos EUA, Centro-Norte dos EUA, Centro-Oeste dos EUA, Centro do Canadá, Leste do Canadá |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, Nova York, San Antonio, Seattle, Vale do Silício, Vale do Silício2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto, Vancouver |
| **Ásia** | Leste da Ásia, Sudeste Asiático | Bangkok, Hong Kong, Hong Kong2, Jacarta, Kuala Lumpur, Cingapura, Cingapura2, Taipei |
| **Índia** | Oeste da Índia, Índia Central, Sul da Índia |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japão** | Oeste do Japão, Leste do Japão |Osaka, Tóquio, Tóquio2 |
| **Oceania** | Sudeste da Austrália, Leste da Austrália |Auckland, Melbourne, Perth, Sydney, Sydney2 |
| **Coreia do Sul** | Coreia Central, Sul da Coreia |Busan, Seul|
| **EAU** | Emirados Dos Emirados Central, Emirados Americanos | Dubai |
| **África do Sul** | África do Sul Oeste, África do Sul Norte |Cidade do Cabo, Joanesburgo |
| **América do Sul** | Sul do Brasil |São Paulo |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiões e limites geopolíticos para nuvens nacionais
A tabela a seguir fornece informações sobre regiões e limites geopolíticos para nuvens nacionais.

| **Região geopolítica** | **Regiões azure** | **Localizações do ExpressRoute** |
| --- | --- | --- |
| **Nuvem do Governo dos EUA** |Gov. EUA - Arizona, US Gov Iowa, US Gov - Texas, US Gov Virginia, Região Central do US DoD, Leste do US DoD  |Atlanta, Chicago, Dallas, Nova Iorque, Phoenix, San Antonio, Seattle, Vale do Silício, Washington DC |
| **Leste da China** |Leste da China, Leste da China2 |Shanghai, Shanghai2 |
| **Norte da China** |Norte da China, Norte da China2 |Beijing, Beijing2 |
| **Alemanha** |Alemanha Central, Alemanha Oriental |Berlim+, Frankfurt |

Não há suporte para conectividade entre regiões geopolíticas no SKU de ExpressRoute padrão. Você precisará habilitar o complemento premium de ExpressRoute para dar suporte a conectividade global. Não há suporte a conectividade para ambientes de nuvem nacionais. Você pode trabalhar com seu provedor de conectividade se surgir necessidade de fazê-lo.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>Provedores de conectividade do ExpressRoute

A tabela a seguir mostra locais pelo provedor de serviços. Se você quiser exibir os provedores disponíveis por local, confira [Provedores de serviço por local](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>Comercial global Azure

| **Provedor de serviços** | **Microsoft Azure** | **Escritório 365**  | **Locais** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Com suporte |Com suporte |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/business/#/)** | Com suporte | Com suporte | Chennai2, Mumbai2 |
| **[AIS](https://business.ais.co.th/solution/en/azure-expressroute.html)** | Com suporte | Com suporte | Bangkok |
| **[Aryaka Networks](https://www.aryaka.com/)** |Com suporte |Com suporte |Amsterdã, Chicago, Dallas, Hong Kong SAR, São Paulo, Seattle, Vale do Silício, Cingapura, Tóquio, Washington DC |
| **[Data centers Ascenty](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Com suporte |Com suporte |São Paulo |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Com suporte |Com suporte |Amsterdã, Chicago, Dallas, Londres, Vale do Silício, Singapura, Sydney, Tóquio, Toronto, Washington, D.C. |
| **[EM TÓQUIO](https://www.attokyo.com/service/cloudsconnection/forazure.html)** | Com suporte | Com suporte | Tóquio2 |
| **[BBIX](https://www.bbix.net/en/service/ix/)** | Com suporte | Com suporte | Tóquio |
| **[BCX](https://www.bcx.co.za/solutions)** |Com suporte |Com suporte |Cidade do Cabo|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Com suporte |Com suporte |Montreal, Toronto, Cidade de Quebec |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |Com suporte |Com suporte |Amsterdã, Hong Kong SAR, Joanesburgo, Londres, Newport(País de Gales), São Paulo, Vale do Silício, Cingapura, Sydney, Tóquio, Washington DC |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Com suporte |Com suporte |Miami |
| **CDC** | Com suporte | Com suporte | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Com suporte |Com suporte |Amsterdam2, Chicago, Frankfurt, Hong Kong, Las Vegas, Londres2, Nova York, Paris, San Antonio, Vale do Silício, Tóquio, Toronto, Washington DC, Washington DC2 |
| **[Chief Telecom](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** |Com suporte |Com suporte |Hong Kong |
| **China Mobile International** |Com suporte |Com suporte | Singapura |
| **China Telecom Global** |Com suporte |Com suporte |RAE de Hong Kong |
| **China Unicom Global** |Com suporte |Com suporte | Cingapura2 |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Com suporte |Com suporte |Chicago, Dallas, Montreal, Toronto, Washington D.C. |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Com suporte |Com suporte |Amsterdã, Amsterdã2, Chicago, Dublin, Frankfurt, Londres, Londres2, Newport, Nova York, Osaka, Paris, Vale do Silício, Vale do Silício2, Cingapura2, Tóquio, Washington DC |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Com suporte |Com suporte |Chicago, Vale do Silício, Washington D.C. |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Com suporte |Com suporte |Chicago, Denver, Los Angeles, Nova Iorque, Vale do Silício, Vale do Silício2, Washington DC, Washington DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange/find-a-cloud-service/detail/microsoft-azure)** | Com suporte |Com suporte |Amsterdam2, Frankfurt, Marselha|
| **[Devoli](https://devoli.com/expressroute)** | Com suporte |Com suporte | Auckland |
| **du datamena** |Com suporte |Com suporte | Dubai2 |
| **eir** |Com suporte |Com suporte |Dublin|
| **[Epsilon Comunicações Globais](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Com suporte |Com suporte |Singapura, Singapura2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Com suporte |Com suporte |Amsterdã, Atlanta, Chicago, Dallas, Dublin, Frankfurt, Genebra, Hong Kong SAR, Londres, Londres2, Los Angeles, Melbourne, Miami, Nova York, Osaka, Paris, São Paulo, Seattle, Vale do Silício, Cingapura, Estocolmo, Sydney, Tóquio, Toronto, Washington DC |
| **Emirados Etisalat** |Com suporte |Com suporte |Dubai|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Com suporte |Com suporte |Amsterdã, Amsterdã2, Dublin, Londres |
| **FareasTone** |Com suporte |Com suporte |Taipé|
| **GÉANT** |Com suporte |Com suporte |Amsterdã, Frankfurt, Marselha |
| **[Global Cloud Exchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Com suporte| Com suporte | Chennai, Mumbai |
| **Intelsat** | Com suporte | Com suporte | Washington DC2 |
| **[InterCloud](https://www.intercloud.com/)** |Com suporte |Com suporte |Amsterdã, Chicago, Hong Kong, Londres, Nova York, Paris, Vale do Silício, Cingapura, Washington DC, Zurique |
| **[Internet2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |Com suporte |Com suporte |Chicago, Dallas, Vale do Silício, Washington DC |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Com suporte |Com suporte |Osaka, Tóquio |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Com suporte |Com suporte |Cidade do Cabo, Joanesburgo, Londres |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Com suporte |Com suporte |Amsterdã, Amsterdã2, Copenhague, Dublin, Frankfurt, Londres, Marselha, Paris, Zurique |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Com suporte |Com suporte | Amsterdã, Londres2, Vale do Silício, Toronto, Washington DC |
| **Rede Jaguar** |Com suporte |Com suporte |Marselha|
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Com suporte |Com suporte |London |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Com suporte |Com suporte |Seul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Com suporte |Com suporte |Auckland |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Com suporte | Com suporte | Amsterdã |
| **[Kt](https://cloud.kt.com/portal/ktcloudportal.epc.productintro.partnershipcloud_ConnectHub.html)** | Com suporte | Com suporte | Seul |
| **[Comunicações de Nível 3](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Com suporte |Com suporte |Amsterdã, Chicago, Dallas, Londres, Newport (País de Gales), São Paulo, Seattle, Vale do Silício, Singapura, Washington D.C. |
| **LG CNS** |Com suporte |Com suporte |Busan, Seul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Com suporte |Com suporte |Cidade do Cabo, Joanesburgo |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Com suporte |Com suporte |Amsterdã, Atlanta, Auckland, Chicago, Dallas, Denver, Dubai2, Dublin, Frankfurt, Genebra, Hong Kong SAR, Las Vegas, Londres, Londres2, Los Angeles, Melbourne, Miami, Montreal, Nova York, Oslo, Perth, Quebec City, San Antonio, Seattle, Vale do Silício, Cingapura, Cingapura2, Sydney, Sydney2, Tóquio, Toronto, Washington DC, Zurique |
| **[Mtn](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Com suporte |Com suporte |London |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Com suporte |Com suporte |Dallas, Los Angeles, Miami, São Paulo, Washington DC |
| **[Dados da Próxima Geração](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Com suporte |Com suporte |Newport (País de Gales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Com suporte |Com suporte |Melbourne, Perth, Sydney, Sydney2 |
| **[Comunicações NTT](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Com suporte |Com suporte |Amsterdã, Hong Kong SAR, Londres, Los Angeles, Osaka, Cingapura, Sydney, Tóquio, Washington DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Com suporte |Com suporte |Tóquio |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Com suporte |Com suporte |Osaka |
| **[Ooredoo Cloud Connect](https://www.ooredoo.qa/portal/OoredooQatar/cloud-connect-expressroute)** |Com suporte |Com suporte |Marselha |
| **[Optus](https://www.optus.com.au/enterprise/)** |Com suporte |Com suporte |Melbourne, Sydney |
| **[Laranja](https://www.orange-business.com/en/products/business-vpn-galerie)** |Com suporte |Com suporte |Amsterdã, Amsterdã2, Dubai2, Frankfurt, Hong Kong SAR, Joanesburgo, Londres, Paris, São Paulo, Vale do Silício, Cingapura, Sydney, Tóquio, Washington DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Com suporte | Com suporte | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |Com suporte |Com suporte |Chicago, Vale do Silício, Washington D.C. |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Com suporte |Com suporte |Chicago, Hong Kong SAR, Londres |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Com suporte |Com suporte |Seul |
| **[Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Com suporte |Com suporte | Washington, D.C. |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Com suporte |Com suporte |Chennai, Mumbai2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Com suporte |Com suporte |Singapura, Singapura2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Com suporte |Com suporte |Osaka, Tóquio |
| **[Centelha NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Com suporte |Com suporte |Auckland |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Com suporte |Com suporte |Chicago, Vale do Silício, Washington D.C. |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Com suporte | Com suporte | Zurique |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Com suporte |Com suporte |Amsterdã, Chennai, Hong Kong SAR, Londres, Mumbai, São Paulo, Vale do Silício, Cingapura, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Com suporte |Com suporte |Amsterdã, São Paulo |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Com suporte |Com suporte |Londres, Londres2 |
| **Telenor** |Com suporte |Com suporte |Amsterdã, Londres, Oslo |
| **[Telia Carrier](https://www.teliacarrier.com/)** | Com suporte | Com suporte |Amsterdã, Chicago, Dallas, Frankfurt, Hong Kong, Londres, Oslo, Paris, Vale do Silício, Estocolmo, Washington DC |
| **Telmex Uninet**| Com suporte | Com suporte | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Com suporte |Com suporte |Melbourne, Singapura, Sydney |
| **[Telus](https://www.telus.com)** |Com suporte |Com suporte |Montreal, Seattle, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Com suporte |Com suporte |Cidade do Cabo, Joanesburgo |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Com suporte | Com suporte | Kuala Lumpur |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Com suporte |Com suporte |Dallas, Los Angeles|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Com suporte |Com suporte |São Paulo |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Com suporte |Com suporte |Amsterdã, Chicago, Dallas, Hong Kong SAR, Londres, Mumbai, Vale do Silício, Cingapura, Sydney, Tóquio, Toronto, Washington DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Com suporte | Com suporte | Washington DC2 |
| **[Grupo Vocus NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Com suporte | Com suporte | Auckland |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Com suporte |Com suporte |Amsterdã2, Londres, Cingapura |
| **[Ideia Vodafone](https://discover.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Com suporte | Com suporte | Mumbai, Mumbai2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Com suporte |Com suporte |Amsterdã, Chicago, Dallas, Denver, Londres, Los Angeles, Montreal, Nova York, Paris, Seattle, Vale do Silício, Toronto, Washington DC, Washington DC2 |

 **+** denota em breve

### <a name="national-cloud-environment"></a>Ambientes de nuvem nacionais

Nuvens nacionais azure são isoladas umas das outras e do comercial global Azure. O ExpressRoute para uma nuvem Azure não pode se conectar às regiões do Azure nas outras. 

### <a name="us-government-cloud"></a>Nuvem do Governo dos EUA

| **Provedor de serviços** | **Microsoft Azure** | **Escritório 365** | **Locais** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Com suporte |Com suporte |Chicago, Phoenix, Vale do Silício, Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Com suporte |Com suporte |Nova Iorque, Phoenix, San Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Com suporte |Com suporte |Atlanta, Chicago, Dallas, Nova Iorque, Seattle, Vale do Silício, Washington DC |
| **[Comunicações de Nível 3](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Com suporte |Com suporte |Chicago, Vale do Silício, Washington D.C. |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Com suporte | Com suporte | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Com suporte |Com suporte |Chicago, Los Angeles, Nova York, Vale do Silício, Washington, D.C. |

### <a name="china"></a>China

| **Provedor de serviços** | **Microsoft Azure** | **Escritório 365** | **Locais** |
| --- | --- | --- | --- |
| **China Telecom** |Com suporte |Sem suporte |Pequim, Pequim2, Xangai, Xangai2 |
| **China Unicom** | Com suporte | Sem suporte | Beijing2 |
| **[Gds](http://www.gds-services.com/en/about_2.html)** |Com suporte |Sem suporte |Beijing2, Shanghai2 |

Para saber mais, confira [ExpressRoute na China](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Alemanha

| **Provedor de serviços** | **Microsoft Azure** | **Escritório 365** | **Locais** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Com suporte |Sem suporte |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Com suporte |Sem suporte |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Com suporte |Sem suporte |Berlim |
| **Interxion** |Com suporte |Sem suporte |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Com suporte  | Sem suporte | Berlim |
| **T-Systems** |Com suporte |Sem suporte |Berlim |

## <a name="connectivity-through-exchange-providers"></a>Conectividade através de provedores de Exchange

Se seu provedor de conectividade não estiver listado em seções anteriores, você ainda pode criar uma conexão.

* Verifique com seu provedor de conectividade para ver se eles estão conectados a qualquer um dos Exchanges na tabela acima. Você pode verificar os links a seguir para obter mais informações sobre os serviços oferecidos por provedores do Exchange. Vários provedores de conectividade já estão conectados a Exchanges de Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure) 
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)

* Faça com que seu provedor de conectividade estenda sua rede para o local de emparelhamento de sua escolha.
  * Certifique-se de que seu provedor de conectividade estenda sua conectividade de maneira altamente disponível para que não exista nenhum ponto de falha.
* Solicite um circuito do ExpressRoute com o Exchange como o provedor de conectividade para conectar-se à Microsoft.
  * Siga as etapas em [Criar um circuito do ExpressRoute](expressroute-howto-circuit-classic.md) para configurar a conectividade.

## <a name="connectivity-through-satellite-operators"></a>Conectividade através de operadores de satélite
Se você é remoto e não tem conectividade de fibra ou quer explorar outras opções de conectividade, você pode verificar os seguintes operadores de satélite. 

* Intelsat
* [Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Conectividade através de provedores de serviços adicionais

| **Provedor de conectividade** | **Exchange** | **Locais** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapura |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |Nova Iorque, Washington, D.C. |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tóquio |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Metroconnect](https://www.beanfield.com/business/cloud-exchange)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | London |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdã, Frankfurt, Londres, Singapura, Washington D.C. |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tóquio |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | RAE de Hong Kong |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburgo |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amsterdã | 
| **[CMC Telecom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Singapura | 
| **[Aptum Technologies](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](http://www.coreazure.com/expressroute/)**| Equinix | London |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Vale do Silício, Washington D.C. |
| **[Castelo Crown](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, Nova Iorque, Washington DC |
| **[Dados Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Londres, Singapura, Washington, D.C. |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdã |
| **[E Exponencial](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdã |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Cidade de Quebec |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington, D.C. |
| **[Gulf Bridge International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amsterdã |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Londres, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telecom Bahrain B.S.C](http://www.kalaam-telecom.com/azure/)**| Comunicações de Nível 3 |Amsterdã |
| **LGA Telecom** |Equinix |Singapura|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |RAE de Hong Kong 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdã |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington, D.C. |
| **[Mtn](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Cidade do Cabo, Joanesburgo |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amsterdã, Frankfurt |
| **[POST Telecom Luxemburgo](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdã |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdã, Dublin, Londres, Paris |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **[RETN](https://retn.net/services/cloud-connect/)** | Equinix | Amsterdã |
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Empresa de Espectro](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, Nova Iorque, Vale do Silício | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | London | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amsterdã | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdã |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amsterdã, Frankfurt |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdã |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapura |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | São Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | Nova Iorque |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Vale do Silício, Washington D.C. |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Vale do Silício, Vale do Silício 2|
| **Zain** |Equinix |London|
| **[Zertia](https://www.zertia.es)**| Nível 3 | Madri |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Conectividade através de provedores de datacenter

| **Provedor** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Banco](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[QTS Data Centers](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Centros de dados de fluxo]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach |
| **[Datacenters T5](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Conectividade através de Redes Nacionais de Pesquisa e Educação (NREN)

| **Provedor**|
| --- |
| **AARNET**| 
| **DeIC, por meio de GÉANT**|
| **GARR, por meio de GÉANT**|
| **GÉANT**|
| **HEAnet, por meio de GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS, por meio de GÉANT**|
| **SINET**|
| **Surfnet, por meio de GÉANT**|

* Se seu provedor de conectividade não estiver listado aqui, verifique se ele está conectado a qualquer um dos parceiros do ExpressRoute Exchange listados acima.

## <a name="expressroute-system-integrators"></a>Integradores de sistema de ExpressRoute
Habilitar a conectividade privada para atender às suas necessidades pode ser desafiador, dependendo da escala de sua rede. Você pode trabalhar com qualquer dos integradores de sistema listados na tabela a seguir para ajudá-lo com integração ao ExpressRoute.

| **Integrador de Sistema** | **Continente** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Europa |
| **[Avanade Inc.](https://www.avanade.com/)** | Ásia, Europa, América do Norte, América do Sul |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Europa
| **[Ensyst](https://www.ensyst.com.au)** | Ásia
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | América do Norte |
| **[FlexManage](https://www.flexmanage.com/cloud)** | América do Norte |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | América do Norte |
| **[O grupo de consultoria de TI](https://itconsult.com.au/)** | Austrália |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Austrália |
| **[Serviços de Mensagem](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Alemanha) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Europa |
| **[Nova Assinatura](https://newsignature.com/technologies/express-route/)** | Europa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Ásia |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | América do Norte |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | América do Norte |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Europa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | América do Sul |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Austrália |

## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre o ExpressRoute, consulte o [FAQ ExpressRoute](expressroute-faqs.md).
* Certifique-se que todos os pré-requisitos foram atendidos. Consulte [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de localização"
