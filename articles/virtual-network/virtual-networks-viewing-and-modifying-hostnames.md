---
title: Exibindo e modificando nomes do host | Microsoft Docs
description: Como exibir e alterar os nomes de host para máquinas virtuais do Azure, funções Web e de trabalho para a resolução de nomes
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: cce248e2906f4a36737388e8cc7124b1bb19fbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058668"
---
# <a name="viewing-and-modifying-hostnames"></a>Exibindo e modificando os nomes do host
Para permitir que as instâncias de função sejam referenciadas pelo nome do host, você deve definir o valor para o nome do host no arquivo de configuração de serviço para cada função. Faça isso adicionando o nome do host desejado ao atributo **vmName** do elemento **Função**. O valor do atributo **vmName** é usado como base para o nome de host de cada instância de função. Por exemplo, se **vmName** for *webrole* e houver três instâncias dessa função, os nomes de host das instâncias serão *webrole0*, *webrole1* e *webrole2*. Você não precisa especificar um nome do host para máquinas virtuais no arquivo de configuração, porque o nome do host para uma máquina virtual é preenchido com base no nome da máquina virtual. Para obter mais informações sobre como configurar um serviço do Microsoft Azure, consulte [Esquema de configuração do serviço do Azure (arquivo .cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Exibindo nomes do host
Você pode exibir os nomes de host das máquinas virtuais e as instâncias de função em um serviço de nuvem usando as ferramentas abaixo.

### <a name="service-configuration-file"></a>Arquivo de configuração de serviço
Você pode baixar o arquivo de configuração de serviço para um serviço implantado na folha **Configurar** do serviço no portal do Azure. Procure o atributo **vmName** para o elemento **Nome de Função** para ver o nome do host. Tenha em mente que esse nome do host é usado como base para o nome do host de cada instância de função. Por exemplo, se **vmName** for *webrole* e houver três instâncias dessa função, os nomes de host das instâncias serão *webrole0*, *webrole1* e *webrole2*.

### <a name="remote-desktop"></a>Área de Trabalho Remota
Após habilitar a área de trabalho remota (Windows), comunicação remota do Windows PowerShell (Windows) ou as conexões SSH (Linux e Windows) para suas máquinas virtuais ou instâncias de função, você pode exibir o nome do host de uma conexão de área de trabalho remota ativa de várias maneiras:

* Digite o nome do host no prompt de comando ou no terminal do SSH.
* Digite ipconfig /all no prompt de comando (somente Windows).
* Exiba o nome do computador nas configurações do sistema (somente Windows).

### <a name="azure-service-management-rest-api"></a>API REST de Gerenciamento de Serviços do Azure
Em um cliente REST, siga estas instruções:

1. Certifique-se de que você tenha um certificado do cliente para se conectar ao Portal do Azure. Para obter um certificado do cliente, execute as etapas apresentadas em [Como baixar e importar configurações de publicação e informações de assinatura](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Defina uma entrada de cabeçalho chamada x-ms-version com um valor de 2013-11-01.
3. Envie uma solicitação no seguinte\/formato:\<https: /management.core.windows.net/\>subscrição-id\</services/hostedservices/ service-name\>?embed-detail=true
4. Procure o elemento **HostName** para cada elemento **RoleInstance**.

> [!WARNING]
> Você também pode exibir o sufixo de domínio interno para o seu serviço de nuvem na resposta da chamada REST, verificando o elemento **InternalDnsSuffix** ou executando ipconfig /all em um prompt de comando em uma sessão de área de trabalho remota (Windows) ou ainda executando cat /etc/resolv.conf em um terminal SSH (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Modificando um nome de host
Você pode modificar o nome de host para qualquer máquina virtual ou instância de função, carregando um arquivo de configuração de serviço modificado ou renomeando o computador em uma sessão de área de trabalho remota.

## <a name="next-steps"></a>Próximas etapas
[Resolução de nomes (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Esquema de configuração de serviço do Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Esquema de configuração de Rede Virtual do Azure](https://go.microsoft.com/fwlink/?LinkId=248093)

[Especificar configurações de DNS usando arquivos de configuração de rede](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

