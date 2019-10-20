---
title: Testa Azure Functions
description: Skapa automatiserade tester för C# en funktion i Visual Studio och JavaScript-funktionen i vs Code
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, functions, Event Processing, Webhooks, dynamisk beräkning, Server lös arkitektur, testning
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: 9155df315a5afb9a0fa7722c955333a47a73085a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596841"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategier för att testa koden i Azure Functions

Den här artikeln visar hur du skapar automatiserade tester för Azure Functions. 

Testning av all kod rekommenderas, men du kan få bästa möjliga resultat genom att omsluta en funktions logik och skapa tester utanför funktionen. Abstraktion av logik begränsar en funktions kodrader och gör att funktionen endast är ansvarig för att anropa andra klasser eller moduler. Den här artikeln visar dock hur du skapar automatiserade tester mot en HTTP-och timer-utlöst funktion.

Innehållet som följer delas upp i två olika avsnitt som är avsedda att rikta in sig på olika språk och miljöer. Du kan lära dig att bygga tester i:

- [C#i Visual Studio med xUnit](#c-in-visual-studio)
- [Java Script i VS Code med Jest](#javascript-in-vs-code)

Exempel lagrings platsen finns på [GitHub](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C#i Visual Studio
I följande exempel beskrivs hur du skapar en C# Function-app i Visual Studio och kör och testar med [xUnit](https://xunit.github.io).

![Testa Azure Functions med C# i Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Konfiguration

Om du vill konfigurera din miljö skapar du en funktion och testar appen. Följande steg hjälper dig att skapa appar och funktioner som krävs för att stödja testerna:

1. [Skapa en ny Functions-app](./functions-create-first-azure-function.md) och *namnge den-funktionen*
2. [Skapa en HTTP-funktion från mallen](./functions-create-first-azure-function.md) och ge den namnet *HttpTrigger*.
3. [Skapa en timer-funktion från mallen](./functions-create-scheduled-function.md) och ge den namnet *TimerTrigger*.
4. [Skapa en XUnit test-app](https://xunit.github.io/docs/getting-started-dotnet-core) i Visual Studio genom att klicka på **fil > nytt > C# projekt > Visual > .net Core > xUnit test Project** och ge den namnet *functions. test*. 
5. Använd NuGet för att lägga till en referens från test appen [Microsoft. AspNetCore. MVC](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Referera till *Functions* -appen](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) från *functions. test* -appen.

### <a name="create-test-classes"></a>Skapa test klasser

Nu när du har skapat programmen kan du skapa de klasser som används för att köra automatiserade tester.

Varje funktion tar en instans av [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) för att hantera meddelande loggning. Vissa tester loggar antingen inte in meddelanden eller har ingen risk för hur loggning implementeras. Andra tester måste utvärdera meddelanden som loggats för att avgöra om ett test skickas.

@No__t_0-klassen är avsedd att implementera `ILogger`-gränssnittet och lagra i en intern lista med meddelanden för utvärdering under ett test.

**Högerklicka** på programmet *functions. test* och välj **Lägg till > klass**, ge den namnet **NullScope.cs** och ange följande kod:

```csharp
using System;

namespace Functions.Tests
{
    public class NullScope : IDisposable
    {
        public static NullScope Instance { get; } = new NullScope();

        private NullScope() { }

        public void Dispose() { }
    }
}
```

**Högerklicka** sedan på programmet *functions. test* och välj **Lägg till > klass**, ge den namnet **ListLogger.cs** och ange följande kod:

```csharp
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel, 
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

@No__t_0-klassen implementerar följande medlemmar enligt `ILogger`-gränssnittet:

- **BeginScope**: omfattningar lägger till kontext i loggningen. I det här fallet pekar testet precis på den statiska instansen i `NullScope`-klassen så att testet kan fungera.

- **IsEnabled**: ett standardvärde för `false` anges.

- **Log**: den här metoden använder den tillhandahållna `formatter` funktionen för att formatera meddelandet och lägger sedan till den resulterande texten i `Logs`s samlingen.

Den `Logs` samlingen är en instans av `List<string>` och initieras i konstruktorn.

**Högerklicka** sedan på programmet *functions. test* och välj **Lägg till > klass**, ge den namnet **LoggerTypes.cs** och ange följande kod:

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```
Den här uppräkningen anger vilken typ av loggning som används av testerna. 

**Högerklicka** sedan på programmet *functions. test* och välj **Lägg till > klass**, ge den namnet **TestFactory.cs** och ange följande kod:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static DefaultHttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var request = new DefaultHttpRequest(new DefaultHttpContext())
            {
                Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue))
            };
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```
@No__t_0-klassen implementerar följande medlemmar:

- **Data**: den här egenskapen returnerar en [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) -samling med exempel data. Nyckel värdes paren representerar värden som skickas till en frågesträng.

- **CreateDictionary**: den här metoden accepterar ett nyckel/värde-par som argument och returnerar en ny `Dictionary` som används för att skapa `QueryCollection` som representerar frågesträngs värden.

- **CreateHttpRequest**: den här metoden skapar en http-begäran som initieras med de aktuella frågesträngs parametrarna.

- **CreateLogger**: den här metoden returnerar en loggnings klass som används för testning baserat på loggnings typ. @No__t_0 håller reda på loggade meddelanden som är tillgängliga för utvärdering i tester.

**Högerklicka** sedan på programmet *functions. test* och välj **Lägg till > klass**, ge den namnet **FunctionsTests.cs** och ange följande kod:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await HttpFunction.Run(request, logger);
            Assert.Equal("Hello, Bill", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await HttpFunction.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            TimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```
De medlemmar som implementeras i den här klassen är:

- **Http_trigger_should_return_known_string**: det här testet skapar en begäran med sträng värden för `name=Bill` till en http-funktion och kontrollerar att det förväntade svaret returneras.

- **Http_trigger_should_return_string_from_member_data**: det här testet använder xUnit-attribut för att tillhandahålla exempel data till http-funktionen.

- **Timer_should_log_message**: det här testet skapar en instans av `ListLogger` och skickar den till en timer-funktion. När funktionen har körts, kontrol leras loggen för att se till att det förväntade meddelandet finns.

Om du vill komma åt program inställningarna i dina tester kan du använda [system. Environment. GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables).

### <a name="run-tests"></a>Köra tester

Kör testerna genom att gå till **test Utforskaren** och klicka på **Kör alla**.

![Testa Azure Functions med C# i Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Felsök tester

Om du vill felsöka testerna anger du en Bryt punkt för ett test, navigerar till **test Utforskaren** och klickar på **Kör > Felsök senaste körning**.

## <a name="javascript-in-vs-code"></a>Java Script i VS Code

I följande exempel beskrivs hur du skapar en JavaScript Function-app i VS Code och kör och testar med [Jest](https://jestjs.io). I den här proceduren används [tillägget vs Code Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för att skapa Azure Functions.

![Testa Azure Functions med Java Script i VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Konfiguration

Om du vill konfigurera din miljö initierar du en ny Node. js-app i en tom mapp genom att köra `npm init`.

```bash
npm init -y
```
Installera sedan Jest genom att köra följande kommando:

```bash
npm i jest
```
Uppdatera nu _Package. JSON_ och Ersätt det befintliga test kommandot med följande kommando:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Skapa testmoduler
När projektet har initierats kan du skapa de moduler som används för att köra automatiserade tester. Börja med att skapa en ny mapp med namnet *test* som ska innehålla stödmodulerna.

Lägg till en ny fil i mappen *testning* , ge den namnet **defaultContext. js**och Lägg till följande kod:

```javascript
module.exports = {
    log: jest.fn()
};
```
Den här modulen är en blå *logg* funktion som representerar standard körnings kontexten.

Lägg sedan till en ny fil, ge den namnet **defaultTimer. js**och Lägg till följande kod:

```javascript
module.exports = {
    IsPastDue: false
};
```
Den här modulen implementerar egenskapen `IsPastDue` att stå som en falsk timer-instans.

Använd sedan tillägget VS Code Functions för att [skapa en ny JavaScript-HTTP-funktion](/azure/javascript/tutorial-vscode-serverless-node-01) och ge den namnet *HttpTrigger*. När funktionen har skapats lägger du till en ny fil i samma mapp med namnet **index. test. js**och lägger till följande kod:

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```
HTTP-funktionen från mallen returnerar en sträng med namnet "Hello" som är sammanfogat med det namn som anges i frågesträngen. Det här testet skapar en falsk instans av en begäran och skickar den till HTTP-funktionen. Testet kontrollerar att *logg* metoden anropas en gång och den returnerade texten motsvarar "Hello Bill".

Sedan använder du tillägget VS Code Functions för att skapa en ny JavaScript-timer och ge den namnet *TimerTrigger*. När funktionen har skapats lägger du till en ny fil i samma mapp med namnet **index. test. js**och lägger till följande kod:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
Funktionen timer från mallen loggar ett meddelande i slutet av bröd texten i funktionen. Det här testet ser till att *logg* funktionen anropas en gång.

### <a name="run-tests"></a>Köra tester
Kör testerna genom att trycka på **CTRL + ~** för att öppna kommando fönstret och köra `npm test`:

```bash
npm test
```

![Testa Azure Functions med Java Script i VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Felsök tester

Om du vill felsöka dina tester lägger du till följande konfiguration i filen *Launch. JSON* :

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "disableOptimisticBPs": true,
  "program": "${workspaceRoot}/node_modules/jest/bin/jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Ange sedan en Bryt punkt i testet och tryck på **F5**.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skriver automatiserade tester för dina funktioner kan du fortsätta med följande resurser:
- [Köra en icke-HTTP-utlöst funktion manuellt](./functions-manually-run-non-http.md)
- [Azure Functions fel hantering](./functions-bindings-error-pages.md)
- [Lokal felsökning av Event Grid-utlösare i Azure Functions](./functions-debug-event-grid-trigger-local.md)
