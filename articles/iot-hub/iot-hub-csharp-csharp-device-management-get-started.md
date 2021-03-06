---
title: Introdução ao gerenciamento de dispositivo de Hub IoT do Azure (.NET/.NET) | Microsoft Docs
description: Como usar o gerenciamento de dispositivos do Hub IoT do Azure para iniciar uma reinicialização do dispositivo remoto. Use o SDK do dispositivo IoT do Azure para .NET para implementar um aplicativo de dispositivo simulado que inclua um método direto e o SDK do serviço do Azure IoT para .NET para implementar um aplicativo de serviço que invoque o método direto.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 3b37d7e049e7daabbbb4fe1a7b49feb654e8accc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110247"
---
# <a name="get-started-with-device-management-net"></a>Comece com o gerenciamento de dispositivos (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostra como:

* Usar o portal do Azure para criar um Hub IoT e criar uma identidade de dispositivo em seu Hub IoT.

* Crie um aplicativo de dispositivo simulado contendo um método direto que reinicia o dispositivo. Métodos diretos são invocados da nuvem.

* Criar um aplicativo de console .NET que chama um método direto de reinicialização no aplicativo de dispositivo simulado por meio do Hub IoT.

No fim deste tutorial, você terá dois aplicativos de console .NET:

* **SimularManagedDevice**. Este aplicativo se conecta ao seu hub de IoT com a identidade do dispositivo criada anteriormente, recebe um método direto de reinicialização, simula uma reinicialização física e relata o tempo da última reinicialização.

* **TriggerReboot**. Este aplicativo chama um método direto no aplicativo do dispositivo simulado, exibe a resposta e exibe as propriedades atualizadas relatadas.

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma conta ativa do Azure. Se você não tiver uma conta, você pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Verifique se a porta 8883 está aberta no firewall. A amostra do dispositivo neste artigo usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a seqüência de conexão do hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Disparar uma reinicialização remota no dispositivo usando um método direto

Nesta seção, você cria um aplicativo de console .NET, usando C#, que inicia uma reinicialização remota em um dispositivo usando um método direto. O aplicativo usa consultas de dispositivo gêmeo para descobrir o último horário de reinicialização para esse dispositivo.

1. No Visual Studio, selecione **Criar um projeto**.

1. Em **Criar um novo projeto,** encontre e selecione o modelo de projeto Console App **(.NET Framework)** e selecione **Next**.

1. Em **Configure seu novo projeto,** nomeie o projeto *TriggerReboot*e selecione .NET Framework versão 4.5.1 ou posterior. Selecione **Criar**.

    ![Novo projeto da Área de Trabalho Clássica do Windows no Visual C#](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. No **Solution Explorer,** clique com o botão direito do mouse no projeto **TriggerReboot** e selecione **Gerenciar pacotes NuGet**.

1. Selecione **Procurar,** procurar e selecionar **Microsoft.Azure.Devices**. Selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices.**

    ![Janela do Gerenciador de Pacotes NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   Esta etapa baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço IoT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e suas dependências.

1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Adicione os seguintes campos à classe **Program** . Substitua `{iot hub connection string}` o valor do espaço reservado pela seqüência de conexão IoT Hub que você copiou anteriormente em [Obter a seqüência de conexão de hub IoT](#get-the-iot-hub-connection-string).

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Adicione o seguinte método à classe **Programa.**  Esse código obtém o dispositivo gêmeo para o dispositivo de reinicialização e gera as propriedades relatadas.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Adicione o seguinte método à classe **Programa.**  Esse código inicia uma reinicialização no dispositivo usando um método direto.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. Por fim, adicione as seguintes linhas ao método **Principal:**

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Selecione **Build** > **Build Solution**.

> [!NOTE]
> Este tutorial executa somente uma única consulta para propriedades relatadas do dispositivo. No código de produção, recomendamos a sondagem para detectar alterações nas propriedades relatadas.

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você:

* Crie um aplicativo de console do .NET que responde a um método direto chamado pela nuvem.

* Dispare uma reinicialização do dispositivo simulado.

* Use as propriedades relatadas para habilitar consultas de dispositivo gêmeo para identificar os dispositivos e quando foi sua última reinicialização.

Para criar o aplicativo de dispositivo simulado, siga estas etapas:

1. No Visual Studio, na solução TriggerReboot que você já criou, selecione **File** > **New** > **Project**. Em **Criar um novo projeto,** encontre e selecione o modelo de projeto Console App **(.NET Framework)** e selecione **Next**.

1. Em **Configurar seu novo projeto,** nomear o projeto *SimularManagedDevice*e para **Solução,** **selecione Adicionar à solução**. Selecione **Criar**.

    ![Nomeie e adicione seu projeto à solução](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. No Solution Explorer, clique com o botão direito do mouse no novo projeto **SimulateManagedDevice** e, em seguida, **selecione Gerenciar pacotes NuGet**.

1. Selecione **Procurar,** em seguida, procurar e selecionar **Microsoft.Azure.Devices.Client**. Selecione **Instalar**.

    ![Aplicativo de cliente de janela do Gerenciador de Pacotes NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   Esta etapa baixa, instala e adiciona uma referência ao pacote SDK NuGet [do dispositivo Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e suas dependências.

1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Adicione os seguintes campos à classe **Program** . Substitua `{device connection string}` o valor do espaço reservado pela seqüência de conexão do dispositivo que você observou anteriormente em [Registrar um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. Adicione o seguinte para implementar o método direto no dispositivo:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. Por fim, adicione o seguinte código ao método **Principal** para abrir a conexão para o hub IoT e inicializar o ouvinte do método:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```

1. No Solution Explorer, clique com o botão direito do mouse na solução e selecione **Set StartUp Projects**.

1. Para**projeto de inicialização de**propriedades >  **comuns,** selecione projeto **de inicialização único**e selecione o projeto **Simulagerenciar.** Selecione **OK** para salvar suas alterações.

1. Selecione **Build** > **Build Solution**.

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (como um recuo exponencial), como sugerido no [manuseio de falhas transitórias](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. Para executar o aplicativo de dispositivo .NET **SimulateManageDDevice**, no Solution Explorer, clique com o botão direito do mouse no projeto **SimuladoManagedDevice,** selecione **Debug**e selecione **Iniciar nova instância**. O aplicativo deve começar a ouvir chamadas de método do seu hub de IoT.

1. Depois disso, o dispositivo está conectado e esperando por invocações do método, clique com o botão direito do mouse no projeto **TriggerReboot,** selecione **Debug**e selecione **Iniciar nova instância**.

   Você deverá ver “Reiniciando!” escrito no console **SimulatedManagedDevice** e as propriedades relatadas do dispositivo, que incluem a hora da última reinicialização, escritas no console **TriggerReboot**.

    ![Execução de aplicativo de serviço e dispositivo](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
