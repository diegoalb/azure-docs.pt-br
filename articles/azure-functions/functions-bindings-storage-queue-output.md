---
title: Vinculação da saída de armazenamento do Azure Fila para funções do Azure
description: Aprenda a criar mensagens de armazenamento azure Fila nas funções do Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 76af5f398edd736874fa79095f2e80c02298eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277330"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Vinculações de saída de armazenamento do Azure Fila para funções do Azure

As funções do Azure podem criar novas mensagens de armazenamento azure Queue configurando uma vinculação de saída.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](./functions-bindings-storage-queue.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C #](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que cria uma mensagem da fila para cada solicitação HTTP recebida.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho de HTTP em um arquivo *function.json* e código [script C# (.csx)](functions-reference-csharp.md) que usa a associação. A função cria um item de fila com um conteúdo de objeto **CustomQueueMessage** para cada solicitação HTTP recebida.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código script C# que cria uma mensagem de fila única:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Você pode enviar várias mensagens ao mesmo tempo usando um parâmetro `ICollector` ou `IAsyncCollector`. Aqui está o código de script C# que envia várias mensagens, uma com os dados da solicitação HTTP e outra com valores codificados:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho HTTP em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função cria um item da fila para cada solicitação HTTP recebida.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Você pode enviar várias mensagens de uma vez com a definição de uma matriz de mensagem para a `myQueueItem` associação de saída. O código JavaScript a seguir envia duas mensagens de fila com valores codificados para cada solicitação HTTP recebida.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como produzir valores únicos e múltiplos em filas de armazenamento. A configuração necessária para *function.json* é a mesma de qualquer maneira.

Uma vinculação da fila de armazenamento é definida em `queue` *function.json* onde o *tipo* é definido para .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Para definir uma mensagem individual na fila, você `set` passa um único valor para o método.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Para criar várias mensagens na fila, declare um parâmetro como o tipo de lista apropriado e `set` passe uma matriz de valores (que correspondem ao tipo de lista) para o método.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

 O exemplo a seguir mostra uma função Java que cria uma mensagem de fila para quando acionada por uma solicitação HTTP.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

No [biblioteca de runtime de funções Java](/java/api/overview/azure/functions/runtime), use o `@QueueOutput` anotação em parâmetros cujo valor seria gravado no armazenamento de fila.  O tipo de `OutputBinding<T>`parâmetro `T` deve ser , onde está qualquer tipo java nativo de um POJO.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

O atributo se aplica a um `out` parâmetro ou o valor de retorno da função. O construtor do atributo usa o nome da fila, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte [Exemplo de saída](#example).

Você pode usar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, confira Gatilho – atributos.

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Os atributos não são suportados pelo script C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pelo Python.

# <a name="java"></a>[Java](#tab/java)

A `QueueOutput` anotação permite que você escreva uma mensagem como a saída de uma função. O exemplo a seguir mostra uma função acionada pelo HTTP que cria uma mensagem de fila.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Propriedade    | Descrição |
|-------------|-----------------------------|
|`name`       | Declara o nome do parâmetro na assinatura da função. Quando a função é acionada, o valor deste parâmetro tem o conteúdo da mensagem de fila. |
|`queueName`  | Declara o nome da fila na conta de armazenamento. |
|`connection` | Aponta para a seqüência de conexão da conta de armazenamento. |

O parâmetro associado `QueueOutput` à anotação é digitado como uma instância [T\<\> de vinculação](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) de saída.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de `Queue` vinculação que você definiu no arquivo *function.json* e no atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**type** | n/d | Deve ser definido como `queue`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como `out`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d | O nome da variável que representa a fila no código de função. Definido como `$return` para referenciar o valor de retorno da função.|
|**queueName** |**Queuename** | O nome da fila. |
|**Conexão** | **Conexão** |O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se `connection` você definir como "MyStorage", o tempo de execução funções procurará uma configuração de aplicativo chamada "MyStorage". Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C #](#tab/csharp)

Escreva uma única mensagem de fila usando `out T paramName`um parâmetro de método como . Você pode usar o tipo de retorno de método em vez de um `out` parâmetro, e `T` pode ser qualquer um dos seguintes tipos:

* Um objeto serializado como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se você tentar associar `CloudQueueMessage` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

Em C# e script C#, grave várias mensagens de fila usando um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Escreva uma única mensagem de fila usando `out T paramName`um parâmetro de método como . O `paramName` é o valor `name` especificado na propriedade de *function.json*. Você pode usar o tipo de retorno de método em vez de um `out` parâmetro, e `T` pode ser qualquer um dos seguintes tipos:

* Um objeto serializado como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se você tentar associar `CloudQueueMessage` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

Em C# e script C#, grave várias mensagens de fila usando um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascript"></a>[Javascript](#tab/javascript)

O item da fila `context.bindings.<NAME>` de `<NAME>` saída está disponível através de onde corresponde o nome definido em *function.json*. Você pode usar uma cadeia de caracteres ou um objeto serializável em JSON para o conteúdo de item de fila.

# <a name="python"></a>[Python](#tab/python)

Existem duas opções para a saída de uma mensagem do Event Hub de uma função:

- **Valor de**retorno `name` : Defina a `$return`propriedade em *function.json* para . Com essa configuração, o valor de retorno da função é persistido como uma mensagem de armazenamento na Fila.

- **Imperativo**: Passe um valor para o método [definido](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) do parâmetro declarado como um tipo [out.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) O valor `set` passado é persistido como uma mensagem de armazenamento na fila.

# <a name="java"></a>[Java](#tab/java)

Existem duas opções para a saída de uma mensagem do Event Hub de uma função usando a anotação [QueueOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput)

- **Valor**de retorno : Ao aplicar a anotação na função em si, o valor de retorno da função é persistido como uma mensagem do Event Hub.

- **Imperativo**: Para definir explicitamente o valor da mensagem, aplique a [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)anotação a um parâmetro específico do tipo, onde `T` está um POJO ou qualquer tipo java nativo. Com essa configuração, passar `setValue` um valor para o método persiste o valor como uma mensagem do Event Hub.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Associação |  Referência |
|---|---|
| Fila | [Fila de códigos de erro](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, tabela, fila | [Códigos de erro de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tabela, fila |  [Solução de problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>configurações de host.json

Esta seção descreve as configurações globais disponíveis para essa vinculação nas versões 2.x ou superior. O arquivo host.json de exemplo abaixo contém apenas as configurações da versão 2.x+ para esta vinculação. Para obter mais informações sobre configurações globais nas versões 2.x e além, consulte [a referência host.json para Funções Azure](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de host.json no Functions 1.x, confira [Referência de host.json para o Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------|
|maxPollingInterval|00:00:01|O intervalo máximo entre as sondagens de fila. A mínima é de 00:00:00.100 (100 ms) e incrementa até 00:01:00 (1 min).  Em 1.x o tipo de dados é milissegundos, e em 2.x e mais alto é um TimeSpan.|
|visibilityTimeout|00:00:00|O intervalo de tempo entre as repetições quando o processamento de uma mensagem falha. |
|batchSize|16|O número de mensagens em fila que o runtime de Funções recupera simultaneamente e processa em paralelo. Quando o número que está sendo processado chega até `newBatchThreshold`, o runtime obtém outro lote e começa a processar as mensagens. Portanto, o número máximo de mensagens simultâneas que estão sendo processadas por função é `batchSize` mais `newBatchThreshold`. Esse limite se aplica separadamente a cada função acionada por fila. <br><br>Se quiser evitar uma execução paralela para mensagens recebidas em uma fila, é possível definir `batchSize` como 1. No entanto, essa configuração elimina a simultaneidade desde que seu aplicativo de função seja executado em uma única máquina virtual (VM). Se o aplicativo de função se expande para várias VMs, cada VM pode executar uma instância de cada função acionada por fila.<br><br>O máximo `batchSize` é 32. |
|maxDequeueCount|5|O número de vezes para tentar processar uma mensagem antes de movê-la para a fila de mensagens suspeitas.|
|newBatchThreshold|batchSize/2|Sempre que o número de mensagens processadas simultaneamente chega a esse número, o runtime recupera outro lote.|

## <a name="next-steps"></a>Próximas etapas

- [Execute uma função como alterações de dados de armazenamento na fila (Trigger)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
