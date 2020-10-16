---
title: Förstå koncepten för enhets modellens lagrings plats | Microsoft Docs
description: Som en lösnings utvecklare eller IT-proffs kan du läsa om de grundläggande begreppen i enhets modellens lagrings plats.
author: rido-min
ms.author: rmpablos
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4e15ef5256c1552fc8ab7fb9bd84f15bb3433834
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131368"
---
# <a name="device-model-repository"></a>Enhets modellens lagrings plats

Enhets modellens lagrings plats (DMR) gör det möjligt för enhets byggare att hantera och dela IoT Plug and Play enhets modeller. Enhets modellerna är JSON LD-dokument som definierats med hjälp av [DTDL (Digital Garns Modeling Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

DMR definierar ett mönster för att lagra DTDL-gränssnitt i en mappstruktur baserat på enhetens dubbla modell identifierare (DTMI). Du kan hitta ett gränssnitt i DMR genom att konvertera DTMI till en relativ sökväg. Till exempel `dtmi:com:example:Thermostat;1` översätts DTMI till `/dtmi/com/example/thermostat-1.json` .

## <a name="public-device-model-repository"></a>Lagrings plats för offentlig enhets modell

Microsoft är värd för en offentlig DMR med följande egenskaper:

- Granskade modeller. Microsoft granskar och godkänner alla tillgängliga gränssnitt med ett GitHub-validerings arbets flöde för pull-begäran (PR).
- Oföränderlighets.  När den har publicerats går det inte att uppdatera ett gränssnitt.
- Hyper-Scale. Microsoft tillhandahåller den infrastruktur som krävs för att skapa en säker, skalbar slut punkt där du kan publicera och använda enhets modeller.

## <a name="custom-device-model-repository"></a>Anpassad lagrings plats för enhets modell

Du kan använda samma DMR-mönster i valfritt lagrings medium, t. ex. lokalt fil system eller anpassade HTTP-webbservrar, för att skapa en anpassad DMR. Du kan hämta enhets modeller från den anpassade DMR på samma sätt som från offentliga DMR genom att ändra bas-URL: en som används för att få åtkomst till DMR.

> [!NOTE]
> Microsoft tillhandahåller verktyg för att validera enhets modeller i den offentliga DMR. Du kan återanvända dessa verktyg i anpassade lagrings platser.

## <a name="public-models"></a>Offentliga modeller

De offentliga enhets modeller som lagras i modell databasen är tillgängliga för alla att använda och integrera i sina program. Med offentliga enhets modeller kan du använda ett öppet eko system för enhets byggare och-utvecklare för att dela och återanvända sina IoT Plug and Play enhets modeller.

I avsnittet [publicera en modell](#publish-a-model) hittar du instruktioner om hur du publicerar en modell i modell databasen för att göra den offentlig.

Användare kan söka efter, söka i och Visa offentliga gränssnitt från den officiella [GitHub-lagringsplatsen](https://github.com/Azure/iot-plugandplay-models).

Alla gränssnitt i `dtmi` mapparna är också tillgängliga från den offentliga slut punkten [https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>Lösa modeller

För att program mässigt komma åt dessa gränssnitt måste du konvertera en DTMI till en relativ sökväg som du kan använda för att fråga den offentliga slut punkten. Följande kod exempel visar hur du gör detta:

För att konvertera en DTMI till en absolut sökväg använder vi `DtmiToPath` funktionen, med `IsValidDtmi` :

```cs
static string DtmiToPath(string dtmi)
{
    if (!IsValidDtmi(dtmi))
    {
        return null;
    }
    // dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
    return $"/{dtmi.ToLowerInvariant().Replace(":", "/").Replace(";", "-")}.json";
}

static bool IsValidDtmi(string dtmi)
{
    // Regex defined at https://github.com/Azure/digital-twin-model-identifier#validation-regular-expressions
    Regex rx = new Regex(@"^dtmi:[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?(?::[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?)*;[1-9][0-9]{0,8}$");
    return rx.IsMatch(dtmi);
}
```

Med den resulterande sökvägen och bas-URL: en för databasen kan vi hämta gränssnittet:

```cs
const string _repositoryEndpoint = "https://devicemodels.azure.com";

string dtmiPath = DtmiToPath(dtmi.ToString());
string fullyQualifiedPath = $"{_repositoryEndpoint}{dtmiPath}";
string modelContent = await _httpClient.GetStringAsync(fullyQualifiedPath);
```

## <a name="publish-a-model"></a>Publicera en modell

> [!Important]
> Du måste ha ett GitHub-konto för att kunna skicka modeller till den offentliga DMR.

1. Förgrena över den offentliga GitHub-lagrings platsen: [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) .
1. Klona den förgrenade lagrings platsen. Du kan också skapa en ny gren för att hålla dina ändringar isolerade från `main` grenen.
1. Lägg till de nya gränssnitten i `dtmi` mappen med hjälp av katalogen/fil namns konventionen. Se verktyget [Lägg till modell](#add-model) .
1. Validera enhets modellerna lokalt med hjälp av [skripten för att validera ändringar](#validate-files) .
1. Genomför ändringarna lokalt och skicka dem till din förgrening.
1. Från din förgrening skapar du en pull-begäran som är riktad mot `main` grenen. Se [skapa ett ärende-eller pull-begäranden](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) .
1. Granska [kraven för pull-begäran](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md).

Pull-begäran utlöser en serie GitHub-åtgärder som verifierar de nya skickade gränssnitten och ser till att pull-begäran uppfyller alla kontroller.

Microsoft kommer att svara på en pull-begäran med alla kontroller inom tre arbets dagar.

### <a name="add-model"></a>Lägg till-modell

Följande steg visar hur du kan använda add-model.js-skriptet för att lägga till ett nytt gränssnitt. Det här skriptet kräver Node.js att köras:

1. Från en kommando tolk navigerar du till den lokala git-lagrings platsen
1. Kör `npm install`
1. Kör `npm run add-model <path-to-file-to-add>`

Titta på konsolens utdata för eventuella fel meddelanden.

### <a name="local-validation"></a>Lokal verifiering

Du kan köra samma verifierings kontroller lokalt innan du skickar pull-begäran för att hjälpa till att diagnostisera problem i förväg.

#### <a name="validate-files"></a>Validera – filer

`npm run validate-files <file1.json> <file2.json>` kontrollerar att fil Sök vägen matchar den förväntade mappen och fil namnen.

#### <a name="validate-ids"></a>Validera-ID: n

`npm run validate-ids <file1.json> <file2.json>` kontrollerar att alla ID: n som definierats i dokumentet använder samma rot som huvud-ID: t.

#### <a name="validate-deps"></a>Validera-deps

`npm run validate-deps <file1.json> <file2.json>` kontrollerar att alla beroenden är tillgängliga i `dtmi` mappen.

#### <a name="validate-models"></a>Validera-modeller

Du kan köra [DTDL verifierings exempel](https://github.com/Azure-Samples/DTDL-Validator) för att validera dina enhets modeller lokalt.

## <a name="next-steps"></a>Nästa steg

Det föreslagna nästa steg är att granska [IoT plug and Play-arkitekturen](concepts-architecture.md).
