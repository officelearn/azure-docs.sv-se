---
title: Testa Azure Functions
description: Skapa automatiska tester för en C# funktion i Visual Studio och JavaScript-funktion i VS Code
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure functions, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur testning
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: cshoe
ms.openlocfilehash: 44d2960d9cf5828af588f9392667553c18dedb0f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103463"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategier för att testa din kod i Azure Functions

Den här artikeln visar hur du skapar automatiska tester för Azure Functions. 

Testa alla kod rekommenderas, men du får bästa resultat genom att avslutar en funktion logik och skapa tester utanför funktionen. Abstrahera logik direkt begränsar en funktion rader med kod och gör att funktionen ska vara bär hela ansvaret för att anropa andra klasser eller moduler. Den här artikeln, men visar hur du skapar automatiska tester mot en HTTP- och timerutlöst funktion.

Det innehåll som följer är uppdelad i två olika delar som är avsedd att fokusera på olika språk och miljöer. Du kan lära dig att bygga tester in:

- [C#i Visual Studio med xUnit](#c-in-visual-studio)
- [JavaScript i VS Code med Jest](#javascript-in-vs-code)

På lagringsplatsen för exemplet finns på [GitHub](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C#i Visual Studio
I följande exempel beskriver hur du skapar en C# Funktionsapp i Visual Studio och kör och testar med [xUnit](https://xunit.github.io).

![Testa Azure Functions med C# i Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Konfiguration

Skapa en funktion för att konfigurera din miljö och testa appen. Följande steg hjälper dig att skapa appar och funktioner som krävs för att stödja testerna:

1. [Skapa en ny Functions-app](./functions-create-first-azure-function.md) och ge den namnet *funktioner*
2. [Skapa en HTTP-funktion från mallen](./functions-create-first-azure-function.md) och ge den namnet *HttpTrigger*.
3. [Skapa en timerfunktion från mallen](./functions-create-scheduled-function.md) och ge den namnet *TimerTrigger*.
4. [Skapa en app för Test av xUnit](https://xunit.github.io/docs/getting-started-dotnet-core) och ge den namnet *Functions.Test*.
5. [Referens för den *Functions* app](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) från *Functions.Test* app.

### <a name="create-test-classes"></a>Skapa test-klasser

Nu när program som har skapats, kan du skapa de klasser som används för att köra testerna.

Varje funktion tar en instans av [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) att hantera meddelandeloggning. Vissa tester antingen inte logga meddelanden eller har någon betydelse för hur loggning har implementerats. Andra tester måste utvärdera meddelanden som har loggats för att avgöra om ett test passerar.

Den `ListLogger` klass är avsedd att implementera den `ILogger` gränssnitt och håll ned på interna listan över meddelanden för utvärdering under ett test.

**Högerklicka på** på den *Functions.Test* programmet och väljer **Lägg till > klass**, ge den namnet **ListLogger.cs** och ange följande kod:

```csharp
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions.Internal;
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

Den `ListLogger` klassen implementerar följande medlemmar som kontrakt genom den `ILogger` gränssnitt:

- **BeginScope**: scope lägga till kontext till din loggning. I det här fallet testet bara pekar på den statiska instansen på den [NullScope](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.abstractions.internal.nullscope) klass så att testet ska fungera.

- **IsEnabled**: standardvärdet `false` har angetts.

- **Log**: den här metoden använder den medföljande `formatter` fungera om du vill formatera meddelandet och lägger sedan till den resulterande texten till den `Logs` samling.

Den `Logs` samling är en instans av `List<string>` och har initierats i konstruktorn.

Nästa **högerklickar du på** på den *Functions.Test* programmet och väljer **Lägg till > klass**, ge den namnet **LoggerTypes.cs** och ange den följande kod:

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
Den här uppräkningen anger vilken typ av loggare som används av testerna. 

Nästa **högerklickar du på** på den *Functions.Test* programmet och väljer **Lägg till > klass**, ge den namnet **TestFactory.cs** och ange den följande kod:

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
Den `TestFactory` klassen implementerar följande medlemmar:

- **Data**: den här egenskapen returnerar en [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) uppsättning exempeldata. Nyckelvärdepar representerar värden som skickas till en frågesträng.

- **CreateDictionary**: den här metoden godkänner ett nyckel/värde-par som argument och returnerar en ny `Dictionary` används för att skapa `QueryCollection` att representera frågesträngsvärden.

- **CreateHttpRequest**: den här metoden skapar en HTTP-begäran initieras med de angivna parametrarna för frågesträngen.

- **CreateLogger**: beroende på vilken loggaren, den här metoden returnerar en logger-klass som används för testning. Den `ListLogger` håller reda på loggade meddelanden för utvärdering i tester.

Nästa **högerklickar du på** på den *Functions.Test* programmet och väljer **Lägg till > klass**, ge den namnet **FunctionsTests.cs** och ange den följande kod:

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
            TimerFunction.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```
Medlemmar som implementerats i den här klassen är:

- **Http_trigger_should_return_known_string**: det här testet skapar en begäran med frågan strängvärden för `name=Bill` att en HTTP-funktion och kontrollerar att det förväntade svaret returneras.

- **Http_trigger_should_return_string_from_member_data**: det här testet använder xUnit attribut för att tillhandahålla exempeldata till HTTP-funktionen.

- **Timer_should_log_message**: det här testet skapar en instans av `ListLogger` och skickar den till en timer-funktion. När funktionen körs, kontrolleras loggen för att se till att förväntade meddelandet finns.

### <a name="run-tests"></a>Kör test

Om du vill köra testerna, navigera till den **Test Explorer** och klicka på **köra alla**.

![Testa Azure Functions med C# i Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Felsöka tester

Ange en brytpunkt på ett test för att felsöka testerna, navigera till den **testa Explorer** och klicka på **kör > Felsöka senaste kör**.

## <a name="javascript-in-vs-code"></a>JavaScript i VS Code

I följande exempel beskriver hur du skapar en app med JavaScript-funktion i VS Code och kör som testas med [Jest](https://jestjs.io). Den här proceduren använder den [VS Code Functions extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) att skapa Azure Functions.

![Testa Azure Functions med JavaScript i VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Konfiguration

Om du vill konfigurera din miljö kan du initiera en ny Node.js-app i en tom mapp genom att köra `npm init`.

```bash
npm init -y
```
Installera Jest genom att köra följande kommando:

```bash
npm i jest
```
Uppdatera nu _package.json_ att ersätta det befintliga testkommandot med följande kommando:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Skapa test-moduler
Du kan skapa de moduler som används för att köra testerna med projekt som initierats. Börja med att skapa en ny mapp med namnet *testning* för support-moduler.

I den *testning* mappen Lägg till en ny fil, ge den namnet **defaultContext.js**, och Lägg till följande kod:

```javascript
module.exports = {
    log: jest.fn()
};
```
Den här modulen mocks den *log* funktionen för att representera standard körningskontexten skapades.

Därefter lägger du till en ny fil, ge den namnet **defaultTimer.js**, och Lägg till följande kod:

```javascript
module.exports = {
    isPastDue: false
};
```
Den här modulen implementerar den `isPastDue` egenskapen stå är som en falsk timer-instans.

Använd funktioner för VS Code-tillägg till [skapa en ny funktion i JavaScript HTTP](https://code.visualstudio.com/tutorials/functions-extension/getting-started) och ge den namnet *HttpTrigger*. När funktionen har skapats kan du lägga till en ny fil i samma mapp med namnet **index.test.js**, och Lägg till följande kod:

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
HTTP-funktion från mallen returnerar en sträng med ”Hello” sammanfogat med det namn som i frågesträngen. Det här testet skapar en falsk instans av en begäran och skickar dem till HTTP-funktionen. Testet kontrollerar att den *log* metoden anropas en gång och den returnerade texten är lika med ”Hello Bill”.

Använd funktioner för VS Code-tillägg att skapa en ny funktion i JavaScript Timer och ge den namnet *TimerTrigger*. När funktionen har skapats kan du lägga till en ny fil i samma mapp med namnet **index.test.js**, och Lägg till följande kod:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
Timer-funktion från mallen loggar ett meddelande i slutet av brödtexten till funktionen. Det här testet säkerställer den *log* anropas en gång.

### <a name="run-tests"></a>Kör test
Om du vill köra testerna, trycker du på **CTRL + ~** att öppna kommandofönstret och kör `npm test`:

```bash
npm test
```

![Testa Azure Functions med JavaScript i VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Felsöka tester

Om du vill felsöka dina tester, lägger du till följande konfiguration till din *launch.json* fil:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "program": "${workspaceRoot}\\node_modules\\jest\\bin\\jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Nu ska vi konfigurera en brytpunkt i test- och tryck på **F5**.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skriver automatiska tester för dina funktioner, fortsätter du med dessa resurser:

- [Azure Functions-felhantering](./functions-bindings-error-pages.md)
- [Azure-funktion Event Grid utlösa lokal felsökning](./functions-debug-event-grid-trigger-local.md)