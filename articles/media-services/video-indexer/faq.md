---
title: Vanliga frågor och svar om Video Indexer - Azure
titlesuffix: Azure Media Services
description: Få svar på vanliga frågor om Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 12/19/2018
ms.author: juliako
ms.openlocfilehash: 972858b4bae995b6e9073cf7ee451a317e9f3909
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731577"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Den här artikeln får du svar på vanliga frågor om Video Indexer.

## <a name="general-questions"></a>Allmänna frågor

### <a name="what-is-video-indexer"></a>Vad är Video Indexer?

Video Indexer är en tjänst för extrahering av metadata för ljud- och mediefiler från Azure Media Services. Den använder ett stort utbud av machine learning-algoritmer för att göra det lättare att klassificera, analysera och få insikter om video och ljud innehåll med hjälp av [fördefinierade modeller](video-indexer-overview.md). Du kan använda dessa insikter på många sätt som förbättrar innehåll identifieringsmöjlighet och tillgänglighet, skapa nya möjligheter för intäkter eller skapa nya upplevelser som drar nytta av insikterna.

Video Indexer tillhandahåller en webbaserat gränssnitt för testning, konfiguration och anpassning och ett REST-baserat API för utvecklare som vill integrera i produktionssystemet.

### <a name="what-can-i-do-with-video-indexer"></a>Vad kan jag göra med Video Indexer?

Video Indexer kan utföra följande typer av åtgärder på mediefiler:

* Identifiera och extrahera tal och identifiera talare.
* Identifiera och extrahera anvisningarna på skärmen text i en video.
* Identifiera och etikettera objekt i en videofil.
* Identifiera text i en video för varumärken, t.ex. Microsoft från ljudspår och anvisningarna på skärmen.
* Identifiera och känna igen ansikten från en databas med kändisar och en användardefinierad databas med ansikten.
* Extrahera nyckelord från ljud- och innehåll utifrån talat och visuella text.
* Extrahera avsnitt diskuteras men inte nödvändigtvis uttryckligen anges i ljud- och innehåll utifrån talat och visuella text.
* Skapa undertexter eller undertexter från ljudspåret.

Mer information finns i [Översikt](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Hur kommer jag igång med Video Indexer?

Video Indexer är kostnadsfritt att testa. Den kostnadsfria utvärderingsversionen ger dig 600 minuter i webbaserat gränssnitt och 2 400 minuter via API: et.

Index videor och ljud sig i skala, kan du ansluta Video Indexer till en betald Microsoft Azure-prenumeration. Du hittar mer information om priser på den [priser](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) sidan.

Du hittar mer information om att komma igång [börjar](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Behöver jag kodning för att använda Video Indexer?

Du kan integrera API: er för Video Indexer i din pipeline eller du kan använda Video Indexer-portalen och inte behöver skriva någon kod alls. 

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Behöver jag machine learning-kunskaper för att använda Video Indexer?

Nej, du kan lyfta ut kunskaper från ditt innehåll med video- och ljudströmmar helt utan någon machine learning-kunskaper eller kunskap om i algoritmer. 

### <a name="what-media-formats-does-video-indexer-support"></a>Vilka media-format stöder Video Indexer?

Video Indexer har stöd för de vanligaste medieformat. Läs mer i listan med Azure Media Encoder standardformat för mer information.

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Hur gör jag för att överföra en media till Video Indexer?

I Video Indexer webbaserad portal kan överför en mediefil med hjälp av dialogrutan för filöverföring eller genom att peka till en URL som direkt anger du filen. Till exempel `http://contoso.cloudapp.net/videos/example.mp4`. En länk till en sida med en videospelare som `http://contoso.cloudapp.net/videos` fungerar inte. Video Indexer API måste du ange indatafilen via en URL eller en bytematris. Observera att överför via en URL är begränsade till 10 GB, men har inte en tidsgräns för varaktighet. Mer information finns i denna [instruktionsguide](upload-index-videos.md).

### <a name="how-long-does-it-take-visual-indexer-to-extract-insights-from-media"></a>Hur lång tid tar det visuella indexeraren extrahera insikter från media?

Hur lång tid det tar för att indexera en video- eller ljudinnehåll fil, båda med Video Indexer API: et och Video Indexer webbaserat gränssnitt är beroende av flera parametrar, t.ex längden för filen och kvalitet, antal insikter i filen, antalet beräkning unites tillgängliga, och om den strömmande slutpunkten är aktiverad eller inte. Om vi antar att 10 mediereserverade S3-enheter är aktiverade för de flesta typer förutsäga vi att indexering tar från 1/3 till ½ av videons längd.  Vi rekommenderar dock att du kör några testfiler med ditt eget innehåll och dra ett medelvärde för att få en bättre uppfattning. 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>I vilka Azure-regioner finns Video indexer?

Du kan se vilka Azure-regioner Video Indexer är tillgänglig på den [regioner](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) sidan.

### <a name="what-is-the-sla-for-video-indexer"></a>Vad är serviceavtalet för Video Indexer?

Den kostnadsfria utvärderingsversionen för Video Indexer har inget serviceavtal. Azure Media Services SLA täcker Video Indexer.

Du hittar informationen på den [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_0/) sidan.

## <a name="billing-questions"></a>Frågor om fakturering

### <a name="how-much-does-video-indexer-cost"></a>Hur mycket kostar Video Indexer?

Video Indexer använder en enkel betala för det du använder prismodell som baseras på inkommande varaktigheten för innehåll. Ytterligare kostnader kan tillkomma för kodning, strömning, lagring, nätverksanvändning och media reserverade enheter. Aktuell prissättning finns i den [priser](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) sidan.

### <a name="does-video-indexer-offer-a-free-trial"></a>Erbjuder Video Indexer en kostnadsfri utvärderingsversion?

Ja, Video Indexer erbjuder en kostnadsfri utvärderingsversion som är värd för regionen östra USA Azure och ger fullständig tjänst och API-funktioner. Det finns en kvot på tio timmars värt videor för webbplatsen användare och 40 timmar för API-användare. 

## <a name="security-questions"></a>Säkerhetsfrågor

### <a name="are-audio-and-video-files-indexed-by-video-indexer-stored"></a>Indexeras ljud- och bildfiler av Video Indexer lagras?

Ja, om du tar bort filen från Video Indexer antingen med hjälp av Video Indexer-webbplats eller API: et, video och ljud filer lagras. För den kostnadsfria utvärderingsversionen, video och ljud filer som du kommer att lagras index i Azure-regionen östra USA. I annat fall lagras din video och ljud filer i lagringskontot för din Azure-prenumeration.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Kan jag ta bort min filer som lagras i Video Indexer-portalen?

Ja, du kan alltid ta bort din video och ljud filer samt sina insikter från Video Indexer. När du tar bort filer, är det permanent borta från Video Indexer och var Video Indexer sparat filen (Azure-regionen östra USA för utvärderingskonton och lagringskontot för din Azure-prenumeration på annat sätt).

### <a name="who-has-access-to-my-audio-and-video-files-and-that-have-been-indexed-andor-stored-by-video-indexer"></a>Vem som har åtkomst till min ljud- och bildfiler och som har blivit indexerade och/eller lagras av Video Indexer?

Ja, du kan alltid ta bort din video och ljud filer samt sina insikter från Video Indexer. När du tar bort filer, är det permanent borta från Video Indexer och var Video Indexer sparat filen (Azure-regionen östra USA för utvärderingskonton och lagringskontot för din Azure-prenumeration på annat sätt).

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Kan jag styra användarnas åtkomst till min Video Indexer-konto?

Du väljer Ja, som bjuds in till kontot.

### <a name="who-has-access-to-the-insights-that-were-extracted-from-my-audio-and-video-files-by-video-indexer"></a>Vem har tillgång till de insikter som extraherades från min ljud- och bildfiler av Video Indexer?

Dina videor som har offentliga som dess sekretessinställningarna kan användas av alla som har en länk till din video och dess insikter. Dina videor som är privata som dess sekretessinställningarna kan bara användas av användare som har bjudits in till kontot för videon.

### <a name="do-i-still-own-my-content-that-have-been-indexed-and-stored-by-video-indexer"></a>Jag fortfarande äger mitt innehåll som har indexerats och lagras av Video Indexer?

Ja, per den [Azure Online Services-villkoren](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), du äger ditt innehåll.

### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Innehållet som indexeras av Video Indexer hålls inom Azure-regionen där jag använder Video Indexer?

Ja, innehållet och dess insikter är kvar i Azure-region om du inte har en manuell konfiguration i Azure-prenumerationen som använder flera Azure-regioner. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Vad är sekretesspolicyn för Video Indexer?

Video Indexer omfattas av den [Microsoft Privacy Statement](https://privacy.microsoft.com/privacystatement).

## <a name="api-questions"></a>API-frågor

### <a name="what-are-the-differences-in-capability-on-the-video-indexer-website-versus-the-video-indexer-api"></a>Vad är skillnaderna i funktion på webbplatsen Video Indexer jämfört med Video Indexer API?

Med den [Video Indexer](https://www.videoindexer.com) webbplats, du kan söka efter ditt videobibliotek, analysera insikter, anpassa modeller, konfigurera ditt konto och redigera dina videor enkelt. Med den robusta [Video Indexer REST API](https://api-portal.videoindexer.ai/), du kan integrera med all infrastruktur eller bädda in widgetar direkt i din egen webbplats eller program.

### <a name="what-apis-does-video-indexer-offer"></a>Vilka API: er finns i Video Indexer?

Du hittar information om hur du använder API: er för Video Indexer på den [Video Indexer Developer-portalen](https://api-portal.videoindexer.ai/)

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Hur kommer jag igång med Video Indexer API?

Följ [Självstudier: Kom igång med Video Indexer API](video-indexer-use-apis.md).

### <a name="what-is-an-access-token"></a>Vad är en åtkomst-token?

Video Indexer API innehåller ett API för auktorisering och ett Operations-API. Auktoriseringar API: et innehåller anrop som ger dig åtkomst-token. Varje anrop till åtgärds-API:t ska associeras med en åtkomsttoken, som matchar auktoriseringsområdet för anropet.

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Vad är skillnaden mellan-kontots åtkomsttoken, åtkomsttoken för användaren och Video åtkomst-token?

Dessa representerar auktorisering omfånget för ett anrop.

* Användarnivå - administratörsnivå användartoken kan du utföra åtgärder på användarnivå. Till exempel hämta associerade konton.
* Kontonivå – med hjälp av kontot på åtkomsttoken kan du utföra åtgärder på kontonivå eller videonivå. Till exempel ladda upp video, visa alla videor, få videoinsikter osv.
* Videonivå – med hjälp av video på åtkomsttoken kan du utföra åtgärder på en specifik video. Till exempel få videoinsikter, ladda ned textning, få widgetar osv.

Du kan styra om dessa token är skrivskyddade och de gör att redigera genom att ange allowEdit = SANT/FALSKT.

### <a name="why-do-i-need-access-tokens-when-using-the-video-indexer-apis"></a>Varför behöver jag åtkomsttoken när du använder API: er för Video Indexer?

Åtkomst-token behövs för att använda API: er för Video Indexer av säkerhetsskäl. Detta säkerställer att alla anrop kommer från dig eller de som har behörigheter för åtkomst till ditt konto. 

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Hur ofta behöver jag hämta en ny åtkomsttoken? När åtkomsttoken upphör att gälla?

Åtkomsttoken går ut varje timme, så måste du generera en ny åtkomsttoken varje timme. 

## <a name="next-steps"></a>Nästa steg

[Översikt](video-indexer-overview.md)
