---
title: Testa Azure Functions
description: Skapa automatiserade tester för en C#-funktion i Visual Studio och JavaScript-funktion i VS-kod
author: craigshoemaker
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: a37fd886e1bc70226b2e54750540dfcb79ee5973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768885"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategier för att testa din kod i Azure Functions

Den här artikeln visar hur du skapar automatiserade tester för Azure Functions. 

Testa all kod rekommenderas, men du kan få bästa resultat genom att slå in en funktions logik och skapa tester utanför funktionen. Abstraktion logik bort begränsar en funktions rader av kod och gör att funktionen att vara ensam ansvarig för att anropa andra klasser eller moduler. Den här artikeln visar dock hur du skapar automatiserade tester mot en HTTP- och timerutlösta funktioner.

Innehållet som följer är uppdelat i två olika avsnitt avsedda att rikta olika språk och miljöer. Du kan lära dig att bygga tester i:

- [C# i Visual Studio med xUnit](#c-in-visual-studio)
- [JavaScript i VS-kod med Jest](#javascript-in-vs-code)

Exempeldatabasen är tillgänglig på [GitHub](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C# i Visual Studio
I följande exempel beskrivs hur du skapar en C#-funktionsapp i Visual Studio och kör och testar med [xUnit](https://xunit.github.io).

![Testa Azure-funktioner med C# i Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Installation

Skapa en funktions- och testapp om du vill konfigurera din miljö. Följande steg hjälper dig att skapa de appar och funktioner som krävs för att stödja testerna:

1. [Skapa en ny funktionsapp](./functions-create-first-azure-function.md) och namnge den *Funktioner*
2. [Skapa en HTTP-funktion från mallen](./functions-create-first-azure-function.md) och namnge den *HttpTrigger*.
3. [Skapa en timerfunktion från mallen](./functions-create-scheduled-function.md) och namnge den *TimerTrigger*.
4. [Skapa en xUnit Test-app](https://xunit.github.io/docs/getting-started-dotnet-core) i Visual Studio genom att klicka på **Arkiv > Nytt > Project > Visual C# > .NET Core > xUnit Test Project** och namnge den *Functions.Test*. 
5. Använd NuGet för att lägga till en referens från testappen i [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Referera till *appen Funktioner* ](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) från appen *Functions.Test.*

### <a name="create-test-classes"></a>Skapa testklasser

Nu när programmen har skapats kan du skapa de klasser som används för att köra de automatiserade testerna.

Varje funktion tar en instans av [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) för att hantera meddelandeloggning. Vissa tester loggar antingen inte meddelanden eller har ingen oro för hur loggning implementeras. Andra tester måste utvärdera meddelanden som loggas för att avgöra om ett test går.

Klassen `ListLogger` implementerar `ILogger` gränssnittet och innehåller en intern lista över meddelanden för utvärdering under ett test.

**Högerklicka** på *programmet Functions.Test* och välj **Lägg till > klass**, ge den namnet **NullScope.cs** och ange följande kod:

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

Högerklicka **sedan** på *programmet Functions.Test* och välj **Lägg till > klass**, ge den namnet **ListLogger.cs** och ange följande kod:

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

Klassen `ListLogger` implementerar följande medlemmar enligt `ILogger` gränssnittets kontrakt:

- **BeginScope**: Scopes lägga sammanhang till din loggning. I det här fallet pekar testet bara `NullScope` på den statiska instansen på klassen så att testet kan fungera.

- **IsEnabled**: Ett `false` standardvärde för tillhandahålls.

- **Logg:** Den här `formatter` metoden använder den medföljande funktionen för `Logs` att formatera meddelandet och sedan läggs den resulterande texten till i samlingen.

Samlingen `Logs` är en `List<string>` instans av och initieras i konstruktorn.

Högerklicka **sedan** på *programmet Functions.Test* och välj **Lägg till > klass**, ge den namnet **LoggerTypes.cs** och ange följande kod:

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
Den här uppräkningen anger vilken typ av logger som används av testerna. 

Högerklicka **sedan** på *programmet Functions.Test* och välj **Lägg till > klass**, ge den namnet **TestFactory.cs** och ange följande kod:

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
Klassen `TestFactory` implementerar följande medlemmar:

- **Data**: Den här [egenskapen returnerar en IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) samling av exempeldata. Nyckelvärdesparen representerar värden som skickas till en frågesträng.

- **CreateDictionary**: Den här metoden accepterar ett nyckel-/värdepar som argument och returnerar en ny `Dictionary` som används för att skapa `QueryCollection` för att representera frågesträngvärden.

- **SkapaHttpRequest**: Den här metoden skapar en HTTP-begäran som initierats med de angivna frågesträngparametrarna.

- **CreateLogger**: Baserat på loggertypen returnerar den här metoden en loggerklass som används för testning. Håller `ListLogger` reda på loggade meddelanden tillgängliga för utvärdering i tester.

Högerklicka **sedan** på *programmet Functions.Test* och välj **Lägg till > klass**, ge den namnet **FunctionsTests.cs** och ange följande kod:

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
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
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

- **Http_trigger_should_return_known_string**: Det här testet skapar en begäran med `name=Bill` frågesträngvärdena för till en HTTP-funktion och kontrollerar att det förväntade svaret returneras.

- **Http_trigger_should_return_string_from_member_data**: I det här testet används xUnit-attribut för att tillhandahålla exempeldata till HTTP-funktionen.

- **Timer_should_log_message**: Det här testet skapar `ListLogger` en instans av och skickar den till en timerfunktioner. När funktionen har körts kontrolleras loggen för att säkerställa att det förväntade meddelandet finns.

Om du vill komma åt programinställningarna i dina tester kan du använda [System.Environment.GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables).

### <a name="run-tests"></a>Kör tester

Om du vill köra testerna navigerar du till **Test explorer** och klickar på **Kör alla**.

![Testa Azure-funktioner med C# i Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Felsöka tester

Om du vill felsöka testerna anger du en brytpunkt för ett test, navigerar till **Test Explorer** och klickar på Kör **> Debug Last Run**.

## <a name="javascript-in-vs-code"></a>JavaScript i VS-kod

I följande exempel beskrivs hur du skapar en JavaScript-funktionsapp i VS-kod och kör och testar med [Jest](https://jestjs.io). Den här proceduren använder [tillägget VS-kodfunktioner](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för att skapa Azure-funktioner.

![Testa Azure-funktioner med JavaScript i VS-kod](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Installation

Om du vill konfigurera miljön initierar du en ny Node.js-app i en tom mapp genom att köra `npm init`.

```bash
npm init -y
```
Installera sedan Jest genom att köra följande kommando:

```bash
npm i jest
```
Uppdatera _nu package.json_ för att ersätta det befintliga testkommandot med följande kommando:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Skapa testmoduler
Med projektet initierat kan du skapa de moduler som används för att köra de automatiserade testerna. Börja med att skapa en ny mapp med namnet *testning* för att hålla stödmodulerna.

I *testmappen* lägger du till en ny fil, namnger den **som standardContext.js**och lägger till följande kod:

```javascript
module.exports = {
    log: jest.fn()
};
```
Den här modulen hånar *loggfunktionen* för att representera standardkörningskontexten.

Lägg sedan till en ny fil, namnge den **defaultTimer.js**och lägg till följande kod:

```javascript
module.exports = {
    IsPastDue: false
};
```

Denna modul `IsPastDue` implementerar egenskapen att stå är som en falsk timer instans. Timerkonfigurationer som NCRONTAB-uttryck krävs inte här eftersom testselen helt enkelt anropar funktionen direkt för att testa resultatet.

Använd sedan tillägget VS-kodfunktioner för att [skapa en ny JavaScript HTTP-funktion](/azure/javascript/tutorial-vscode-serverless-node-01) och namnge den *httptrigger*. När funktionen har skapats lägger du till en ny fil i samma mapp med namnet **index.test.js**och lägger till följande kod:

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
HTTP-funktionen från mallen returnerar en sträng med "Hello" som sammanfogats med namnet i frågesträngen. Det här testet skapar en falsk instans av en begäran och skickar den till HTTP-funktionen. Testet kontrollerar att *loggmetoden* anropas en gång och den returnerade texten är lika med "Hello Bill".

Använd sedan tillägget VS-kodfunktioner för att skapa en ny JavaScript-timerfunktion och namnge den *TimerTrigger*. När funktionen har skapats lägger du till en ny fil i samma mapp med namnet **index.test.js**och lägger till följande kod:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
Timerfunktionen från mallen loggar ett meddelande i slutet av brödtexten i funktionen. Detta test säkerställer att *loggfunktionen* anropas en gång.

### <a name="run-tests"></a>Kör tester
Om du vill köra testerna trycker du på **CTRL + ~** för att öppna kommandofönstret och kör: `npm test`

```bash
npm test
```

![Testa Azure-funktioner med JavaScript i VS-kod](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Felsöka tester

Om du vill felsöka dina tester lägger du till följande konfiguration i *filen launch.json:*

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

Ange sedan en brytpunkt i testet och tryck på **F5**.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig att skriva automatiserade tester för dina funktioner fortsätter du med följande resurser:
- [Köra en funktion som inte utlösts av HTTP manuellt](./functions-manually-run-non-http.md)
- [Felhantering av Azure Functions](./functions-bindings-error-pages.md)
- [Lokal felsökning av Event Grid-utlösare i Azure Functions](./functions-debug-event-grid-trigger-local.md)
