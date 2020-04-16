---
title: IntelliSense i Azure Stream Analytics-verktyg för Visual Studio-kod
description: I den här artikeln beskrivs hur du använder IntelliSense-funktioner i Azure Stream Analytics-verktyg för Visual Studio-kod.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: conceptual
ms.openlocfilehash: 25ece3174cd65df9c665888d913cf6b9029904b1
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394402"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>IntelliSense i Azure Stream Analytics-verktyg för Visual Studio-kod

IntelliSense är tillgängligt för [Query Language Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json) i Azure Stream [Analytics-verktyg för VS-kod](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview). IntelliSense är ett stöd för kodkomplettering som innehåller ett antal funktioner: Listmedlemmar, Parameterinformation, Snabbinformation och Fullständigt Word. IntelliSense-funktioner kallas ibland av andra namn som "kodkomplettering", "innehållsassistent" och "kodtips".

![IntelliSense demo](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>IntelliSense-funktioner

IntelliSense-funktionerna i Stream Analytics-verktyg för VS-kod drivs av en språktjänst. En språktjänst analyserar källkoden och ger intelligenta kodkompletter baserade på språksenmantik. Om en språktjänst vet möjliga kompletteringar dyker IntelliSense-förslag upp medan du skriver. Om du fortsätter att skriva filtreras en lista med medlemmar, till exempel variabler och metoder, så att endast medlemmar som innehåller de tecken som du har skrivit. När du `Tab` trycker `Enter` på tangenterna infogas intelliSense den medlem du valde.

Du kan utlösa IntelliSense i ett redigeringsfönster genom att `.`skriva ett utlösartecken, till exempel punkttecknet .

![intellisense automatisk komplettering](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> Den förslag widget stöttar CamelCase filtrering. Du kan skriva bokstäverna som är uppercased i ett metodnamn för att begränsa förslagen. Till exempel kommer "cra" snabbt att ta upp "createApplication".

### <a name="types-of-completions"></a>Typer av kompletteringar

Stream Analytics-verktyg för VS-kod IntelliSense erbjuder olika typer av kompletteringar, inklusive språkserverförslag, utdrag och enkla ordbaserade textkompletter.

|       |         |       |
| ----- | ------- | ----- |
| Nyckelord | `keyword`
| Functions | `build-in function`, `user defined function`  |
| Namn på datauppsättning| `input`, `output`, `intermediate result set`|
| Kolumnnamn för datauppsättning|`input`, `intermediate result set`|
|

#### <a name="name-completion"></a>Namnkomplettering

Förutom automatisk komplettering av sökord läser Stream Analytics-verktyg för VS-kod listan över jobbinmatnings- och utdatanamn samt namnen på kolumnerna i dina datakällor när de konfigureras. Tillägget kommer ihåg den här informationen för att ge funktioner för namnkomplettering som är användbara för att skriva in satser med få tangenttryckningar:

När du kodar behöver du inte låta redigeraren utföra sökningar på jobbinmatningsnamn, utdatanamn och kolumnnamn. Du kan behålla ditt sammanhang, hitta den information du behöver, infoga element direkt i koden och låta IntelliSense slutföra din maskinskrivning åt dig.

Observera att du måste konfigurera antingen lokal indata eller live-indata och spara konfigurationsfilen för att kunna använda namnkomplettering.

![namnkomplettering](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Information om parameter

Alternativet IntelliSense **Parameter Info** öppnar en parameterlista som innehåller information om antalet, namnen och typerna av de parametrar som krävs av en funktion. Parametern i fetstil anger nästa parameter som krävs när du skriver en funktion.

Parameterlistan visas också för kapslade funktioner. Om du skriver en funktion som parameter till en annan funktion visas parametrarna för den inre funktionen i parameterlistan. När parameterlistan för den inre funktionen är klar återgår parameterlistan till att visa de yttre funktionsparametrarna.

![parameterinfo](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Snabbinfo

I enlighet med språktjänsten kan du se **Snabbinformation** för varje identifierare i koden. Några exempel på identifierare är indata, utdata, en mellanliggande resultatuppsättning eller funktion. När du flyttar muspekaren över en identifierare visas deklarationen i ett popup-fönster. Egenskaper och datascheman för indata, om de är konfigurerade, och mellanliggande datauppsättning visas.

![snabb information](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Felsöka IntelliSense

Det här problemet orsakas av att indatakonfiguration saknas som tillhandahåller data. Du kan kontrollera om en [lokal indata](visual-studio-code-local-run.md#define-a-local-input) eller [live-indata](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) har konfigurerats korrekt.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett Azure Stream Analytics-jobb i Visual Studio-kod](quick-create-vs-code.md)
* [Testa Stream Analytics-frågor lokalt med exempeldata med Visual Studio-kod](visual-studio-code-local-run.md)
* [Testa Stream Analytics-frågor lokalt mot livestreamindata med hjälp av Visual Studio-kod](visual-studio-code-local-run-live-input.md)
