---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 3d92d3f959e2ad44daa82d6b609b9357cee969c9
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906888"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Skapa ett .NET Core-projekt

Öppna en ny kommandotolk (eller en terminalsession) och kör följande kommandon:

```console
dotnet new console -o sentences-sample
cd sentences-sample
```

Det första kommandot gör två saker. Det skapar ett nytt .NET-konsolprogram och en katalog med namnet `sentences-sample`. Det andra kommandot ändrar till katalogen för ditt projekt.

Därefter behöver du installera Json.Net. Från projektkatalogen kör du:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Välj den C# språkversion

Den här snabbstarten kräver C# 7.1 eller senare. Det finns ett antal sätt att ändra den C# version för ditt projekt. I den här guiden får du lära dig hur du ändrar den `sentences-sample.csproj` filen. Alla tillgängliga alternativ, till exempel ändra språk i Visual Studio finns i [väljer den C# språkversion](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Öppna projektet och öppna sedan `sentences-sample.csproj`. Se till att `LangVersion` är inställt på 7.1 eller senare. Om det inte finns en egenskapsgrupp med språkversion, lägger du till följande rader:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Lägg till nödvändiga namnrymder i projektet

Kommandot `dotnet new console` som du körde tidigare skapade ett nytt projekt, inklusive `Program.cs`. Den här filen är där du lägger programkoden. Öppna `Program.cs` och ersätt de befintliga using-instruktionerna. De här instruktionerna ser till att du har åtkomst till alla typer som krävs för att skapa och köra exempelappen.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Skapa klasser för JSON-svaret

Nu ska vi skapa en klass som används för att avserialisera JSON-svaret som returneras av Translator Text API.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class BreakSentenceResult
{
    public int[] SentLen { get; set; }
    public DetectedLanguage DetectedLanguage { get; set; }
}

public class DetectedLanguage
{
    public string Language { get; set; }
    public float Score { get; set; }
}
```

## <a name="get-subscription-information-from-environment-variables"></a>Hämta prenumerations information från miljövariabler

Lägg till följande rader i `Program` -klassen. Dessa rader läser din prenumerations nyckel och slut punkt från miljövariabler och genererar ett fel om du stöter på problem.

```csharp
private const string key_var = "TRANSLATOR_TEXT_SUBSCRIPTION_KEY";
private static readonly string subscriptionKey = Environment.GetEnvironmentVariable(key_var);

private const string endpoint_var = "TRANSLATOR_TEXT_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == subscriptionKey)
    {
        throw new Exception("Please set/export the environment variable: " + key_var);
    }
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
// The code in the next section goes here.
```

## <a name="create-a-function-to-determine-sentence-length"></a>Skapa en funktion för att fastställa meningslängd

I- `BreakSentenceRequest()`klassen skapar du en ny funktion som heter. `Program` Den här funktionen tar fyra argument `subscriptionKey`: `endpoint`, `route`, och `inputText`.

```csharp
static public async Task BreakSentenceRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-break-sentence-request"></a>Serialisera begäran om Break-meningen

Sedan behöver du skapa och serialisera det JSON-objekt som innehåller texten. Du kan skicka fler än ett objekt i `body`-matrisen.

```csharp
object[] body = new object[] { new { Text = inputText } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Instansiera klienten och göra en begäran

Dessa rader instansierar `HttpClient` och `HttpRequestMessage`:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Konstruera begäran och skriva ut svaret

I `HttpRequestMessage` gör du följande:

* Deklarera HTTP-metoden
* Konstruera begärande-URI
* Infoga begärandetexten (serialiserat JSON-objekt)
* Lägga huvuden som krävs
* Göra en asynkron begäran
* Skriva ut svaret

Lägg till den här koden i `HttpRequestMessage`:

```csharp
// Build the request.
// Set the method to POST
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(endpoint + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
BreakSentenceResult[] deserializedOutput = JsonConvert.DeserializeObject<BreakSentenceResult[]>(result);
foreach (BreakSentenceResult o in deserializedOutput)
{
    Console.WriteLine("The detected language is '{0}'. Confidence is: {1}.", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    Console.WriteLine("The first sentence length is: {0}", o.SentLen[0]);
}
```

Om du använder en Cognitive Services-prenumeration med flera tjänster måste du också ta `Ocp-Apim-Subscription-Region` med i parametrarna för begäran. [Lär dig mer om att autentisera med multi-service](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)-prenumerationen.

## <a name="put-it-all-together"></a>Färdigställa allt

Det sista steget är att anropa `BreakSentenceRequest()` i funktionen `Main`. Leta `static void Main(string[] args)` upp och ersätt den med den här koden:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    string route = "/breaksentence?api-version=3.0";
    // Feel free to use any string.
    string breakSentenceText = @"How are you doing today? The weather is pretty pleasant. Have you been to the movies lately?";
    await BreakSentenceRequest(subscriptionKey, endpoint, route, breakSentenceText);
    Console.WriteLine("Press any key to continue.");
    Console.ReadKey();
}
```

Du kommer att märka att `Main`i, du har `subscriptionKey`deklarerat `endpoint`, `route`, och den text som ska `breakSentenceText`utvärderas.

## <a name="run-the-sample-app"></a>Kör exempelappen

Det var allt. Nu är du redo att köra exempelappen. Från kommandoraden (eller en terminalsession) går du till projektkatalogen och kör:

```console
dotnet run
```

## <a name="sample-response"></a>Exempelsvar

När du har kört exemplet bör du se följande utskrivna till Terminal:

```bash
The detected language is \'en\'. Confidence is: 1.
The first sentence length is: 25
```

Det här meddelandet skapas från RAW JSON, som ser ut så här:

```json
[
    {
        "detectedLanguage":
        {
            "language":"en",
            "score":1.0
        },
        "sentLen":[25,32,35]
    }
]
```

## <a name="clean-up-resources"></a>Rensa resurser

Se till att ta bort all konfidentiell information från exempelappens källkod, till exempel prenumerationsnycklar.

## <a name="next-steps"></a>Nästa steg

Ta en titt på API-referensen för att förstå allt du kan göra med Translator Text API.

> [!div class="nextstepaction"]
> [API-referens](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
