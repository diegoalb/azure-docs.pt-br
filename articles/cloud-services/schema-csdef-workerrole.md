---
title: Azure Cloud Services Def. WorkerRole Schema | Microsoft Docs
description: A função de trabalhador do Azure é usada para o desenvolvimento generalizado e pode realizar processamento de fundo para uma função web. Conheça o esquema de função do trabalhador do Azure.
services: cloud-services
ms.custom: ''
ms.date: 04/14/2015
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 41cd46bc-c479-43fa-96e5-d6c83e4e6d89
caps.latest.revision: 55
author: tgore03
ms.author: tagore
ms.openlocfilehash: 26225442c72fb209bb1ac4cd2bf4777fb39542fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534364"
---
# <a name="azure-cloud-services-definition-workerrole-schema"></a>Esquema WorkerRole de definição dos Serviços de Nuvem do Azure
A função de trabalho do Azure é uma função útil para o desenvolvimento generalizado e pode executar o processamento em segundo plano para uma função web.

A extensão padrão do arquivo de definição de serviço é .csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Esquema de definição de serviço básico para uma função de trabalho.
O formato básico do arquivo de definição de serviço que contém uma função de trabalho é o seguinte.

```xml
<ServiceDefinition …>
  <WorkerRole name="<worker-role-name>" vmsize="<worker-role-size>" enableNativeCodeExecution="[true|false]">
    <Certificates>
      <Certificate name="<certificate-name>" storeLocation="[CurrentUser|LocalMachine]" storeName="[My|Root|CA|Trust|Disallow|TrustedPeople|TrustedPublisher|AuthRoot|AddressBook|<custom-store>" />
    </Certificates>
    <ConfigurationSettings>
      <Setting name="<setting-name>" />
    </ConfigurationSettings>
    <Endpoints>
      <InputEndpoint name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<local-port-number>" port="<port-number>" certificate="<certificate-name>" loadBalancerProbe="<load-balancer-probe-name>" />
      <InternalEndpoint name="<internal-endpoint-name" protocol="[http|tcp|udp|any]" port="<port-number>">
         <FixedPort port="<port-number>"/>
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
      </InternalEndpoint>
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">
         <AllocatePublicPortFrom>
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
         </AllocatePublicPortFrom>
      </InstanceInputEndpoint>
    </Endpoints>
    <Imports>
      <Import moduleName="[RemoteAccess|RemoteForwarder|Diagnostics]"/>
    </Imports>
    <LocalResources>
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    </LocalResources>
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    <Runtime executionContext="[limited|elevated]">
      <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
      </Environment>
      <EntryPoint>
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>
         <ProgramEntryPoint commandLine="<application>" setReadyOnProcessStart="[true|false]"/>
      </EntryPoint>
    </Runtime>
    <Startup priority="<for-internal-use-only>">
      <Task commandLine="" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">
        <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
        </Environment>
      </Task>
    </Startup>
    <Contents>
      <Content destination="<destination-folder-name>" >
        <SourceDirectory path="<local-source-directory>" />
      </Content>
    </Contents>
  </WorkerRole>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementos de esquema
O arquivo de definição de serviço inclui estes elementos, descritos em detalhes nas seções subsequentes neste tópico:

[Workerrole](#WorkerRole)

[Configurationsettings](#ConfigurationSettings)

[Configuração](#Setting)

[LocalResources](#LocalResources)

[Localstorage](#LocalStorage)

[Extremidade](#Endpoints)

[Inputendpoint](#InputEndpoint)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certificados](#Certificates)

[Certificado](#Certificate)

[Imports](#Imports)

[Importar](#Import)

[Runtime](#Runtime)

[Ambiente](#Environment)

[Entrypoint](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Variável](#Variable)

[RoleInstanceValueInstância](#RoleInstanceValue)

[Inicialização](#Startup)

[Tarefa](#Task)

[Conteúdo](#Contents)

[Conteúdo](#Content)

[Diretório de Origem](#SourceDirectory)

##  <a name="workerrole"></a><a name="WorkerRole"></a> WorkerRole
O elemento `WorkerRole` descreve uma função útil para o desenvolvimento generalizado e pode executar o processamento em segundo plano para uma função web. Um serviço pode conter zero ou mais funções de trabalho.

A tabela a seguir descreve os atributos do elemento `WorkerRole`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|string|Obrigatórios. O nome da função de trabalho. O nome da função deve ser exclusivo.|
|enableNativeCodeExecution|booleano|Opcional. O valor padrão é `true`; a execução de código nativo e a confiança total são habilitadas por padrão. Defina este atributo como `false` para desabilitar a execução de código nativo para a função de trabalho e usar a confiança parcial do Azure em vez disso.|
|vmsize|string|Opcional. Defina esse valor para alterar o tamanho da máquina virtual alocada para esta função. O valor padrão é `Small`. Para obter uma lista de tamanhos de máquinas virtuais possíveis e seus atributos, consulte [Tamanhos de máquina virtual para os Serviços de Nuvem do Azure](cloud-services-sizes-specs.md).|

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a> ConfigurationSettings
O elemento `ConfigurationSettings` descreve a coleção de definições de configuração para uma função de trabalho. Esse elemento é o pai do elemento `Setting`.

##  <a name="setting"></a>Configuração <a name="Setting"></a>
O elemento `Setting` descreve um par de nome e valor que especifica uma definição de configuração para uma instância de uma função.

A tabela a seguir descreve os atributos do elemento `Setting`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|string|Obrigatórios. Um nome exclusivo para a definição de configuração.|

As definições de configuração para uma função são pares de nome e valor declarados no arquivo de definição de serviço e definidos no arquivo de configuração de serviço.

##  <a name="localresources"></a><a name="LocalResources"></a>Recursos locais
O elemento `LocalResources` descreve a coleção de recursos de armazenamento local para uma função de trabalho. Esse elemento é o pai do elemento `LocalStorage`.

##  <a name="localstorage"></a><a name="LocalStorage"></a> LocalStorage
O elemento `LocalStorage` identifica um recurso de armazenamento local que fornece espaço do sistema de arquivos para o serviço em runtime. Uma função pode definir zero ou mais recursos de armazenamento local.

> [!NOTE]
>  O elemento `LocalStorage` pode ser exibido como um filho do elemento `WorkerRole` para dar suporte à compatibilidade com versões anteriores do SDK do Azure.

A tabela a seguir descreve os atributos do elemento `LocalStorage`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|string|Obrigatórios. Um nome exclusivo para o repositório local.|
|cleanOnRoleRecycle|booleano|Opcional. Indica se o repositório local deve ser limpo quando a função é reiniciada. O valor padrão é `true`.|
|sizeInMb|INT|Opcional. A quantidade desejada de espaço de armazenamento a ser alocado para o repositório local, em MB. Se não estiver especificada, o espaço de armazenamento padrão alocado será 100 MB. A quantidade mínima de espaço de armazenamento que pode ser alocada é 1 MB.<br /><br /> O tamanho máximo dos recursos locais depende do tamanho da máquina virtual. Para obter mais informações, consulte [Tamanhos de máquina virtual para os Serviços de Nuvem](cloud-services-sizes-specs.md).|

O nome do diretório alocado ao recurso de armazenamento local corresponde ao valor fornecido para o atributo de nome.

##  <a name="endpoints"></a><a name="Endpoints"></a>Extremidade
O elemento `Endpoints` descreve a coleção de pontos de extremidade de entrada (externo), interno e de entrada da instância para uma função. Esse elemento é o pai dos elementos `InputEndpoint`, `InternalEndpoint` e `InstanceInputEndpoint`.

Os pontos de extremidade de entrada e internos são alocados separadamente. Um serviço pode ter, no total, 25 pontos de extremidade de entrada, internos e de entrada de instância que podem ser alocados entre as 25 funções permitidas em um serviço. Por exemplo, se houver 5 funções, será possível alocar 5 pontos de extremidade de entrada por função ou 25 pontos de extremidade de entrada a uma única função ou 1 ponto de extremidade de entrada, cada um, a 25 funções.

> [!NOTE]
>  Cada função implantada requer uma instância por função. O provisionamento padrão para uma assinatura é limitado a 20 núcleos e, portanto, é limitado a 20 instâncias de uma função. Se seu aplicativo exigir mais instâncias do que é fornecido pelo provisionamento padrão, consulte [Billing, Subscription Management and Quota Support](https://azure.microsoft.com/support/options/) (Cobrança, Gerenciamento de assinatura e Suporte de cota) para obter mais informações sobre como aumentar a cota.

##  <a name="inputendpoint"></a><a name="InputEndpoint"></a> InputEndpoint
O elemento `InputEndpoint` descreve um ponto de extremidade externo para uma função de trabalho.

É possível definir vários pontos de extremidade que são uma combinação de pontos de extremidade HTTP, HTTPS, UDP e TCP. É possível especificar qualquer número da porta escolhido para um ponto de extremidade de entrada, mas os números da porta especificados para cada função no serviço devem ser exclusivos. Por exemplo, se você especificar que uma função usa a porta 80 para HTTP e a porta 443 para HTTPS, você poderá, então, especificar que uma segunda função usa a porta 8080 para HTTP e a porta 8043 para HTTPS.

A tabela a seguir descreve os atributos do elemento `InputEndpoint`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|string|Obrigatórios. Um nome exclusivo para o ponto de extremidade externo.|
|protocolo|string|Obrigatórios. O protocolo de transporte para o ponto de extremidade externo. Para uma função de trabalho, os valores possíveis são `HTTP`, `HTTPS`, `UDP` ou `TCP`.|
|porta|INT|Obrigatórios. A porta do ponto de extremidade externo. É possível especificar qualquer número da porta escolhido, mas os números da porta especificados para cada função no serviço devem ser exclusivos.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1.7 ou superior).|
|certificado|string|Obrigatório para um ponto de extremidade HTTPS. O nome de um certificado definido por um elemento `Certificate`.|
|localPort|INT|Opcional. Especifica uma porta usada para conexões internas no ponto de extremidade. O atributo `localPort` mapeia a porta externa no ponto de extremidade para uma porta interna em uma função. Isso é útil em cenários em que uma função deve se comunicar com um componente interno em uma porta diferente da que está exposta externamente.<br /><br /> Se não estiver especificado, o valor de `localPort` será o mesmo que o do atributo `port`. Defina o valor de `localPort` como "*" para atribuir automaticamente uma porta não alocada que pode ser descoberta usando a API de runtime.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1.7 ou superior).<br /><br /> O atributo `localPort` só está disponível usando o SDK do Azure versão 1.3 ou superior.|
|ignoreRoleInstanceStatus|booleano|Opcional. Quando o valor deste atributo é definido como `true`, o status de um serviço é ignorado e o ponto de extremidade não será removido pelo balanceador de carga. Definir esse valor como `true` é útil para depurar instâncias ocupadas de um serviço. O valor padrão é `false`. **Observação:** um ponto de extremidade ainda poderá receber tráfego mesmo quando a função não estiver em um estado Pronto.|
|loadBalancerProbe|string|Opcional. O nome da sonda do balanceador de carga associada ao ponto de extremidade de entrada. Para obter mais informações, consulte o [LoadBalancerProbe Schema](schema-csdef-loadbalancerprobe.md) (Esquema LoadBalancerProbe).|

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a>Internalendpoint
O elemento `InternalEndpoint` descreve um ponto de extremidade interno para uma função de trabalho. Um ponto de extremidade interno está disponível somente para outras instâncias de função em execução dentro do serviço; ele não está disponível para clientes fora do serviço. Uma função de trabalho pode ter até cinco pontos de extremidade internos HTTP, UDP ou TCP.

A tabela a seguir descreve os atributos do elemento `InternalEndpoint`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|string|Obrigatórios. Um nome exclusivo para o ponto de extremidade interno.|
|protocolo|string|Obrigatórios. O protocolo de transporte para o ponto de extremidade interno. Os valores possíveis são `HTTP`, `TCP`, `UDP` ou `ANY`.<br /><br /> Um valor de `ANY` especifica que qualquer protocolo, qualquer porta é permitida.|
|porta|INT|Opcional. A porta usada para conexões com balanceamento de carga interno no ponto de extremidade. Um ponto de extremidade com balanceamento de carga usa duas portas. A porta usada para o endereço IP público e a porta usada no endereço IP privado. Normalmente, elas são definidas como a mesma coisa, mas você pode optar por usar portas diferentes.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1.7 ou superior).<br /><br /> O atributo `Port` só está disponível usando o SDK do Azure versão 1.3 ou superior.|

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a> InstanceInputEndpoint
O elemento `InstanceInputEndpoint` descreve um ponto de extremidade de entrada de instância para uma função de trabalho. Um ponto de extremidade de entrada de instância está associado a uma instância de função específica usando o encaminhamento de porta no balanceador de carga. Cada ponto de extremidade de entrada de instância é mapeado para uma porta específica de um intervalo de portas possíveis. Esse elemento é o pai do elemento `AllocatePublicPortFrom`.

O elemento `InstanceInputEndpoint` só está disponível usando o SDK do Azure versão 1.7 ou superior.

A tabela a seguir descreve os atributos do elemento `InstanceInputEndpoint`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|string|Obrigatórios. Um nome exclusivo para o ponto de extremidade.|
|localPort|INT|Obrigatórios. Especifica a porta interna que todas as instâncias de função escutarão a fim de receber tráfego de entrada encaminhado do balanceador de carga. Os valores possíveis variam entre 1 e 65535, inclusive.|
|protocolo|string|Obrigatórios. O protocolo de transporte para o ponto de extremidade interno. Os valores possíveis são `udp` ou `tcp`. Use `tcp` para tráfego baseado em http/https.|

##  <a name="allocatepublicportfrom"></a><a name="AllocatePublicPortFrom"></a>AlocaçãoPublicPortFrom
O elemento `AllocatePublicPortFrom` descreve o intervalo de porta pública que pode ser usado por clientes externos para acessar cada ponto de extremidade de entrada de instância. O número da porta pública (VIP) é alocado desse intervalo e atribuído a cada ponto de extremidade de instância de função individual durante a implantação e a atualização do locatário. Esse elemento é o pai do elemento `FixedPortRange`.

O elemento `AllocatePublicPortFrom` só está disponível usando o SDK do Azure versão 1.7 ou superior.

##  <a name="fixedport"></a><a name="FixedPort"></a>Porto Fixo
O elemento `FixedPort` especifica a porta para o ponto de extremidade interno, que permite conexões com balanceamento de carga no ponto de extremidade.

O elemento `FixedPort` só está disponível usando o SDK do Azure versão 1.3 ou superior.

A tabela a seguir descreve os atributos do elemento `FixedPort`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|porta|INT|Obrigatórios. A porta do ponto de extremidade interno. Isso tem o mesmo efeito que definir o `FixedPortRange` mín. e máx. para a mesma porta.<br /><br /> Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1.7 ou superior).|

##  <a name="fixedportrange"></a><a name="FixedPortRange"></a> FixedPortRange
O elemento `FixedPortRange` especifica o intervalo de portas atribuído ao ponto de extremidade interno ou de entrada de instância e define a porta usada para conexões com balanceamento de carga no ponto de extremidade.

> [!NOTE]
>  O elemento `FixedPortRange` funciona de maneira diferente dependendo do elemento no qual ele reside. Quando o elemento `FixedPortRange` está no elemento `InternalEndpoint`, ele abre todas as portas no balanceador de carga dentro do intervalo dos atributos mín. e máx. para todas as máquinas virtuais em que a função é executada. Quando o elemento `FixedPortRange` está no elemento `InstanceInputEndpoint`, ele abre apenas uma porta dentro do intervalo dos atributos mín. e máx. em cada máquina virtual que executa a função.

O elemento `FixedPortRange` só está disponível usando o SDK do Azure versão 1.3 ou superior.

A tabela a seguir descreve os atributos do elemento `FixedPortRange`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|Min|INT|Obrigatórios. A porta mínima no intervalo. Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1.7 ou superior).|
|max|string|Obrigatórios. A porta máxima no intervalo. Os valores possíveis variam entre 1 e 65535, inclusive (SDK do Azure versão 1.7 ou superior).|

##  <a name="certificates"></a><a name="Certificates"></a> Certificates
O elemento `Certificates` descreve a coleção de certificados para uma função de trabalho. Esse elemento é o pai do elemento `Certificate`. Uma função pode ter qualquer número de certificados associados. Para obter mais informações sobre o uso do elemento certificates, consulte [Modify the Service Definition file with a certificate](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files) (Modificar o arquivo de definição de serviço com um certificado).

##  <a name="certificate"></a><a name="Certificate"></a>Certificado
O elemento `Certificate` descreve um certificado associado a uma função de trabalho.

A tabela a seguir descreve os atributos do elemento `Certificate`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|string|Obrigatórios. Um nome para esse certificado, usado para se referir a ele quando ele é associado a um elemento `InputEndpoint` de HTTPS.|
|storeLocation|string|Obrigatórios. O local do repositório de certificados em que esse certificado pode ser encontrado no computador local. Os valores possíveis são `CurrentUser` e `LocalMachine`.|
|storeName|string|Obrigatórios. O nome do repositório de certificados em que esse certificado reside no computador local. Os valores possíveis incluem os nomes de repositório interno `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook` ou qualquer nome de repositório personalizado. Se um nome de repositório personalizado for especificado, ele será criado automaticamente.|
|permissionLevel|string|Opcional. Especifica as permissões de acesso fornecidas aos processos de função. Se você desejar que apenas processos com privilégios elevados possam acessar a chave privada, especifique a permissão `elevated`. A permissão `limitedOrElevated` permite que todos os processos de função acessem a chave privada. Os valores possíveis são `limitedOrElevated` ou `elevated`. O valor padrão é `limitedOrElevated`.|

##  <a name="imports"></a><a name="Imports"></a>Importações
O elemento `Imports` descreve uma coleção de módulos de importação para uma função de trabalho que adicionam componentes ao sistema operacional convidado. Esse elemento é o pai do elemento `Import`. Esse elemento é opcional e uma função pode ter apenas um bloco de runtime.

O elemento `Imports` só está disponível usando o SDK do Azure versão 1.3 ou superior.

##  <a name="import"></a><a name="Import"></a>Importação
O elemento `Import` especifica um módulo a ser adicionado ao sistema operacional convidado.

O elemento `Import` só está disponível usando o SDK do Azure versão 1.3 ou superior.

A tabela a seguir descreve os atributos do elemento `Import`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|moduleName|string|Obrigatórios. O nome do módulo a ser importado. Os módulos de importação válidos são:<br /><br /> -   RemoteAccess<br />-   RemoteForwarder<br />-   Diagnostics<br /><br /> Os módulos RemoteAccess e RemoteForwarder permitem que você configure sua instância de função para conexões de área de trabalho remota. Para obter mais informações, consulte [Enable Remote Desktop Connection](cloud-services-role-enable-remote-desktop-new-portal.md) (Habilitar Conexão de Área de Trabalho Remota).<br /><br /> O módulo Diagnóstico permite coletar dados de diagnóstico para uma instância de função|

##  <a name="runtime"></a><a name="Runtime"></a>Runtime
O elemento `Runtime` descreve uma coleção de configurações de variável de ambiente para uma função de trabalho que controlam o ambiente de runtime do processo de host do Azure. Esse elemento é o pai do elemento `Environment`. Esse elemento é opcional e uma função pode ter apenas um bloco de runtime.

O elemento `Runtime` só está disponível usando o SDK do Azure versão 1.3 ou superior.

A tabela a seguir descreve os atributos do elemento `Runtime`:

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|executionContext|string|Opcional. Especifica o contexto no qual o Processo de função é iniciado. O contexto padrão é `limited`.<br /><br /> -   `limited` – O processo é iniciado sem privilégios de administrador.<br />-   `elevated` – O processo é iniciado com privilégios de administrador.|

##  <a name="environment"></a><a name="Environment"></a>Ambiente
O elemento `Environment` descreve uma coleção de configurações de variável de ambiente para uma função de trabalho. Esse elemento é o pai do elemento `Variable`. Uma função pode ter qualquer número de conjunto de variáveis de ambiente.

##  <a name="variable"></a><a name="Variable"></a>Variável
O elemento `Variable` especifica uma variável de ambiente a ser definida no sistema operacional convidado.

O elemento `Variable` só está disponível usando o SDK do Azure versão 1.3 ou superior.

A tabela a seguir descreve os atributos do elemento `Variable`:

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|name|string|Obrigatórios. O nome da variável de ambiente a ser definida.|
|value|string|Opcional. O valor a ser definido para a variável de ambiente. É necessário incluir um atributo de valor ou um elemento `RoleInstanceValue`.|

##  <a name="roleinstancevalue"></a><a name="RoleInstanceValue"></a> RoleInstanceValue
O elemento `RoleInstanceValue` especifica o xPath do qual recuperar o valor da variável.

A tabela a seguir descreve os atributos do elemento `RoleInstanceValue`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|xpath|string|Opcional. Caminho do local de configurações de implantação para a instância. Para obter mais informações, consulte [Variáveis de configuração com o XPath](cloud-services-role-config-xpath.md).<br /><br /> É necessário incluir um atributo de valor ou um elemento `RoleInstanceValue`.|

##  <a name="entrypoint"></a><a name="EntryPoint"></a>Entrypoint
O elemento `EntryPoint` especifica o ponto de entrada para uma função. Esse elemento é o pai dos elementos `NetFxEntryPoint`. Esses elementos permitem que você especifique um aplicativo diferente do WaWorkerHost.exe padrão para atuar como o ponto de entrada da função.

O elemento `EntryPoint` só está disponível usando o SDK do Azure versão 1.5 ou superior.

##  <a name="netfxentrypoint"></a><a name="NetFxEntryPoint"></a> NetFxEntryPoint
O elemento `NetFxEntryPoint` especifica o programa a ser executado para uma função.

> [!NOTE]
>  O elemento `NetFxEntryPoint` só está disponível usando o SDK do Azure versão 1.5 ou superior.

A tabela a seguir descreve os atributos do elemento `NetFxEntryPoint`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|assemblyName|string|Obrigatórios. O caminho e o nome do arquivo do assembly que contém o ponto de entrada. O caminho é relativo à pasta `commandLine` ** \\%ROLEROOT%\Approot** (não especifique ** \\%ROLEROOT%\Approot** in , presume-se). **%ROLEROOT%** é uma variável de ambiente mantida pelo Azure e ela representa o local da pasta raiz da sua função. A pasta ** \\%ROLEROOT%\Approot** representa a pasta do aplicativo para sua função.|
|targetFrameworkVersion|string|Obrigatórios. A versão do .NET Framework na qual esse assembly foi criado. Por exemplo, `targetFrameworkVersion="v4.0"`.|

##  <a name="programentrypoint"></a><a name="ProgramEntryPoint"></a>Ponto de entrada do programa
O elemento `ProgramEntryPoint` especifica o programa a ser executado para uma função. O elemento `ProgramEntryPoint` permite que você especifique um ponto de entrada de programa que não seja baseado em um assembly .NET.

> [!NOTE]
>  O elemento `ProgramEntryPoint` só está disponível usando o SDK do Azure versão 1.5 ou superior.

A tabela a seguir descreve os atributos do elemento `ProgramEntryPoint`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|commandLine|string|Obrigatórios. O caminho o nome do arquivo e argumentos da linha de comando do programa a ser executado. O caminho é relativo à pasta **%ROLEROOT%\Approot** (não especifique **%ROLEROOT%\Approot** em commandLine; ele é presumido). **%ROLEROOT%** é uma variável de ambiente mantida pelo Azure e ela representa o local da pasta raiz da sua função. A pasta **%ROLEROOT%\Approot** representa a pasta do aplicativo da função.<br /><br /> Se o programa for encerrado, a função será reciclada. Portanto, geralmente, defina o programa para continuar sendo executado, em vez de ser um programa que apenas é inicializado e executa uma tarefa finita.|
|setReadyOnProcessStart|booleano|Obrigatórios. Especifica se a instância de função aguarda o programa de linha de comando indicar que ele é iniciado. Este valor deve ser definido como `true` neste momento. Definir o valor como `false` é reservado para uso futuro.|

##  <a name="startup"></a><a name="Startup"></a>Inicialização
O elemento `Startup` descreve uma coleção de tarefas que são executadas quando a função é iniciada. Esse elemento pode ser o pai do elemento `Variable`. Para obter mais informações sobre como usar as tarefas de inicialização de função, consulte [How to configure startup tasks](cloud-services-startup-tasks.md) (Como configurar tarefas de inicialização). Esse elemento é opcional e uma função pode ter apenas um bloco de inicialização.

A tabela a seguir descreve o atributo do elemento `Startup`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|priority|INT|Apenas para uso interno.|

##  <a name="task"></a><a name="Task"></a>Tarefa
O elemento `Task` especifica a tarefa de inicialização que ocorre quando a função é iniciada. As tarefas de inicialização podem ser usadas para executar tarefas que preparam a função a ser executada, como instalar componentes de software ou executar outros aplicativos. As tarefas são executadas na ordem em que aparecem dentro do bloco de elemento `Startup`.

O elemento `Task` só está disponível usando o SDK do Azure versão 1.3 ou superior.

A tabela a seguir descreve os atributos do elemento `Task`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|commandLine|string|Obrigatórios. Um script, como um arquivo CMD, que contém os comandos a serem executados. O comando de inicialização e os arquivos de lote devem ser salvos no formato ANSI. Os formatos de arquivo que definem um marcador de ordem de byte no início do arquivo não serão processados corretamente.|
|executionContext|string|Especifica o contexto no qual o script é executado.<br /><br /> -   `limited` [Padrão] – Executa com os mesmos privilégios que a função que hospeda o processo.<br />-   `elevated` – Executa com privilégios de administrador.|
|taskType|string|Especifica o comportamento de execução do comando.<br /><br /> -   `simple` [Padrão] – O sistema aguarda a tarefa ser encerrada antes de iniciar outras tarefas.<br />-   `background` – O sistema não aguarda a tarefa ser encerrada.<br />-   `foreground` – Semelhante à tela de fundo, a função de exceção não é reiniciada até que todas as tarefas em primeiro plano sejam encerradas.|

##  <a name="contents"></a><a name="Contents"></a>Conteúdo
O elemento `Contents` descreve a coleção de conteúdo para uma função de trabalho. Esse elemento é o pai do elemento `Content`.

O elemento `Contents` só está disponível usando o SDK do Azure versão 1.5 ou superior.

##  <a name="content"></a><a name="Content"></a>Conteúdo
O elemento `Content` define o local de origem do conteúdo a ser copiado para a máquina virtual do Azure e o caminho de destino para o qual ele é copiado.

O elemento `Content` só está disponível usando o SDK do Azure versão 1.5 ou superior.

A tabela a seguir descreve os atributos do elemento `Content`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|destino|string|Obrigatórios. Local na máquina virtual do Azure no qual o conteúdo é colocado. Esse local é relativo à pasta **%ROLEROOT%\Approot**.|

Esse elemento é o pai do elemento `SourceDirectory`.

##  <a name="sourcedirectory"></a><a name="SourceDirectory"></a>Diretório de Origem
O elemento `SourceDirectory` define o diretório local do qual o conteúdo é copiado. Use esse elemento para especificar o conteúdo local a ser copiado para a máquina virtual do Azure.

O elemento `SourceDirectory` só está disponível usando o SDK do Azure versão 1.5 ou superior.

A tabela a seguir descreve os atributos do elemento `SourceDirectory`.

| Atributo | Type | Descrição |
| --------- | ---- | ----------- |
|caminho|string|Obrigatórios. Caminho relativo ou absoluto de um diretório local cujo conteúdo será copiado para a máquina virtual do Azure. Há suporte para a expansão de variáveis de ambiente no caminho de diretório.|

## <a name="see-also"></a>Consulte também
[Esquema de definição do Serviço de Nuvem (clássico)](schema-csdef-file.md)



