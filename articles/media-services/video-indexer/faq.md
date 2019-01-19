---
title: Vanliga frågor och svar om Video Indexer - Azure
titlesuffix: Azure Media Services
description: Få svar på vanliga frågor om Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 01/09/2019
ms.author: juliako
ms.openlocfilehash: 205a4f2089ec5f415932f98eefe2f3d5f614f0ba
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402055"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Den här artikeln får du svar på vanliga frågor om Video Indexer.

## <a name="general-questions"></a>Allmänna frågor

### <a name="what-is-video-indexer"></a>Vad är Video Indexer?

Video Indexer är en tjänst för artificiell intelligens som ingår i Microsoft Azure Media Services. Video Indexer ger en orkestrering av flera machine learning-modeller som hjälper dig att enkelt extrahera djupa insikter från en video. För att tillhandahålla avancerade och korrekt information Video Indexer gör användning av flera kanaler videon: ljud-, tal- och visuella objekt. Video Indexer insikter som kan användas på många sätt, som förbättrar innehåll identifieringsmöjlighet och tillgänglighet, skapa nya möjligheter för intäkter, eller skapa nya upplevelser som använder insikterna. Video Indexer tillhandahåller en webbaserat gränssnitt för testning, konfiguration och anpassning av modeller i ditt konto. Utvecklare kan använda ett REST-baserat API för att integrera Video Indexer i produktionssystemet. 

### <a name="what-can-i-do-with-video-indexer"></a>Vad kan jag göra med Video Indexer?

Några av de åtgärder som Video Indexer kan utföra på mediefiler är:

* Identifiera och extrahera tal och identifiera talare.
* Identifiera och extrahera anvisningarna på skärmen text i en video.
* Identifierar objekt i en videofil.
* Identifiera varumärken (till exempel: Microsoft) från ljudspår och anvisningarna på skärmen text i en video.
* Identifiera och känna igen ansikten från en databas med kändisar och en användardefinierad databas med ansikten.
* Extrahera avsnitt diskuteras men inte nödvändigtvis nämns i ljud-och videoinnehåll.
* Skapa undertexter eller undertexter ljudspåret.

Mer information och fler Video Indexer-funktioner finns i [översikt](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Hur kommer jag igång med Video Indexer?

Video Indexer omfattar en kostnadsfri utvärderingsversion som ger dig 600 minuter i webbaserat gränssnitt och 2 400 minuter via API: et. Du kan [logga in på Video Indexer webbaserat gränssnitt](https://www.videoindexer.ai/) och prova själv med alla webbidentitet och utan att behöva konfigurera en Azure-prenumeration. 

Index videor och ljud sig i skala, kan du ansluta Video Indexer till en betald Microsoft Azure-prenumeration. Du hittar mer information om priser på den [priser](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) sidan.

Du hittar mer information om att komma igång [börjar](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Behöver jag kodning för att använda Video Indexer?

Du kan använda Video Indexer webbaserat gränssnitt för att utvärdera, konfigurera och hantera ditt konto med **ingen kodning krävs**.  När du är redo att utveckla mer komplexa program kan du använda den [Video Indexer API](https://api-portal.videoindexer.ai/) att integrera Video Indexer i dina program, webbplatser, eller [anpassade arbetsflöden med serverlös teknik som Azure Logic Apps](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) eller Azure Functions.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Behöver jag machine learning-kunskaper för att använda Video Indexer?

Nej, ger Video Indexer integrering av flera machine learning-modeller i en pipeline. Indexera en video- eller ljudinnehåll filen via Video Indexer hämtar en fullständig uppsättning insights har extraherat på en delad tidslinje utan någon machine learning-kunskaper eller kunskap om i algoritmer som behövs för kundens del.

### <a name="what-media-formats-does-video-indexer-support"></a>Vilka media-format stöder Video Indexer?

Video Indexer har stöd för de vanligaste medieformat. Referera till den [Azure Media Encoder standard format](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats) listan och visa mer information.

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Hur gör jag för att överföra en media till Video Indexer?

I Video Indexer webbaserad portal du kan ladda upp en mediefil med hjälp av dialogrutan för filöverföring eller genom att peka till en URL som direkt är värd för källfilen (se [exempel](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). Alla värdar mediet innehåll med en iFrame eller bädda in kod inte fungerar URL: er (se [exempel](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)). Video Indexer API måste du ange indatafilen via en URL eller en bytematris. Överföringar via en URL med hjälp av API är begränsade till 10 GB, men har inte en tidsgräns för varaktighet. Mer information finns i denna [instruktionsguide](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Hur lång tid tar det Video-indexeraren extrahera insikter från media?

Hur lång tid det tar för att indexera en video- eller ljudinnehåll fil, båda med Video Indexer API: et och Video Indexer webbaserat gränssnitt som beror på flera parametrar, t.ex filen längd och kvalitet, antal insikter hittades i filen, antalet [reserverade enheter för](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) tillgängliga, och om den [slutpunkt för direktuppspelning](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview) har aktiverats eller inte. Om vi antar att 10 reserverade enheter av typen S3 är aktiverade för de flesta typer förutsäga vi att indexering tar från 1/3 ½ av varaktigheten för filen ljud eller video (till exempel en 10: e minut källa video tar 3.5 till 5 minuter). Vi rekommenderar dock att du kör några testfiler med ditt eget innehåll och dra ett medelvärde för att få en bättre uppfattning.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Kan jag skapa anpassade arbetsflöden för att automatisera processer med Video Indexer?

Ja, du kan integrera Video Indexer i serverlös teknik som Logic Apps, Flow och [Azure Functions](https://azure.microsoft.com/services/functions/). Du hittar mer information på den [Logikapp](https://azure.microsoft.com/services/logic-apps/) och [Flow](https://flow.microsoft.com/en-us/) anslutningsappar för Video Indexer [här](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/). 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>I vilka Azure-regioner finns Video indexer?

Du kan se vilka Azure-regioner Video Indexer är tillgänglig på den [regioner](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) sidan.

### <a name="what-is-the-sla-for-video-indexer"></a>Vad är serviceavtalet för Video Indexer?

Azure Media Service-serviceavtalet täcker Video Indexer och finns på den [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) sidan. SERVICEAVTALET endast gäller för Video Indexer betald konton och gäller inte för den kostnadsfria utvärderingsversionen.

## <a name="privacy-questions"></a>Integritetsfrågor

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Indexeras video-och ljudfiler av Video Indexer lagras?

Ja, om du tar bort filen från Video Indexer kan antingen använda Video Indexer-webbplatsen eller API: et, video och ljud filerna lagras. För den kostnadsfria utvärderingsversionen, video och ljud filer som du lagras index i Azure-regionen östra USA. I annat fall lagras din video och ljud filer i lagringskontot för din Azure-prenumeration.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Kan jag ta bort min filer som lagras i Video Indexer-portalen?

Ja, du kan alltid ta bort videon och ljudfiler samt alla metadata och information som extraherats från dem med hjälp av Video Indexer. När du tar bort en fil från Video Indexer filen och dess metadata och information tas bort permanent från Video Indexer. Om du har implementerat en egen lösning för säkerhetskopiering i Azure storage, förblir filen i din Azure-lagring.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Kan jag styra användarnas åtkomst till min Video Indexer-konto?

Ja, kontoadministratörer kan bjuda in och dra tillbaka inbjudan personer på sina konton, samt tilldela vem som har redigeringen av behörigheter och vem som har skrivskyddad åtkomst.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Vem har tillgång till min video och ljud-filer som har blivit indexerade och/eller lagras av Video Indexer och metadata och information som extraherades?

Din video- eller ljudinnehåll innehåll som har offentliga som dess sekretessinställningarna kan användas av alla som har en länk till video- eller ljudinnehåll innehållet och dess insikter. Din video- eller ljudinnehåll innehåll som har privata som dess sekretessinställningarna kan bara användas av användare som har bjudits in till kontot för video- eller ljudinnehåll innehållet. Sekretessinställningarna för ditt innehåll gäller även för metadata och insikter som extraherar Video Indexer. Du tilldelar sekretessinställningen när du laddar upp din video- eller ljudinnehåll-fil. Du kan också ändra sekretessinställningarna efter indexering.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Vilken åtkomst har Microsoft att min video- eller ljudinnehåll filer som har blivit indexerade och/eller lagras av Video Indexer och metadata och information som extraherades?

Per den [Azure Online Services-villkoren](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST) du helt äger innehållet och Microsoft har endast åtkomst till ditt innehåll och metadata och insikter som Video Indexer extraherar från ditt innehåll enligt OST och Microsoft Sekretesspolicy.

### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Innehållet som indexeras av Video Indexer hålls inom Azure-regionen där jag använder Video Indexer?

Ja, innehållet och dess insikter hålls inom Azure-region om du inte har en manuell konfiguration i Azure-prenumerationen som använder flera Azure-regioner. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Vad är sekretesspolicyn för Video Indexer?

Video Indexer omfattas av den [Microsoft Privacy Statement](https://privacy.microsoft.com/privacystatement). Sekretesspolicyn förklarar de personuppgifter som Microsoft bearbetar, hur Microsoft behandlar det och i vilket syfte Microsoft bearbetar den. Läs mer om sekretess i [Microsoft Trust Center](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Vilka certifieringar har Video Indexer?

Video Indexer har för närvarande SOC-certifiering. Om du vill granska Video Indexer-certifiering, finns det [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

## <a name="api-questions"></a>API-frågor

### <a name="what-apis-does-video-indexer-offer"></a>Vilka API: er finns i Video Indexer?

Video Indexer API: er kan indexera, extraherar metadata, tillgångshantering, översättning, bädda in, anpassning av modeller och mycket mer. Du hittar mer detaljerad information om hur du använder Video Indexer API genom att referera till den [Utvecklarportalen för Video Indexer](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Vilka klient-SDK: er finns i Video Indexer?

Det finns ingen klient som erbjuds av SDK: er. Video Indexer-teamet arbetar med SDK: er och planer för att leverera dem snart.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Hur kommer jag igång med Video Indexer API?

Följ [Självstudier: Kom igång med Video Indexer API](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Vad är skillnaden mellan Video Indexer API och API: et för Azure Media Services v3?

Det finns för närvarande vissa överlappningar i funktioner som erbjuds av Video Indexer API och API: et för Azure Media Services v3. Du hittar mer information om hur du kan jämföra båda tjänsterna [här](compare-video-indexer-with-media-services-presets.md).

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>Vad är en API-åtkomsttoken och varför behöver jag det?

Video Indexer API innehåller ett API för auktorisering och ett Operations-API. Auktoriseringar API: et innehåller anrop som ger dig åtkomst-token. Varje anrop till åtgärds-API:t ska associeras med en åtkomsttoken, som matchar auktoriseringsområdet för anropet.

Åtkomst-token behövs för att använda API: er för Video Indexer av säkerhetsskäl. Detta säkerställer att alla anrop kommer från dig eller de som har behörigheter för åtkomst till ditt konto. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Vad är skillnaden mellan-kontots åtkomsttoken, åtkomsttoken för användaren och Video åtkomst-token?

* Kontonivå – med hjälp av kontot på åtkomsttoken kan du utföra åtgärder på kontonivå eller videonivå. Till exempel ladda upp en video, lista alla videor, hämta videoinsikter.
* Användarnivå - administratörsnivå användartoken kan du utföra åtgärder på användarnivå. Till exempel hämta associerade konton.
* Videonivå – med hjälp av video på åtkomsttoken kan du utföra åtgärder på en specifik video. Till exempel få videoinsikter, ladda ned textning, få widgetar osv.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Hur ofta behöver jag hämta en ny åtkomsttoken? När åtkomsttoken upphör att gälla?

Åtkomsttoken går ut varje timme, så måste du generera en ny åtkomsttoken varje timme. 

## <a name="billing-questions"></a>Frågor om fakturering

### <a name="how-much-does-video-indexer-cost"></a>Hur mycket kostar Video Indexer?

Video Indexer använder en enkel användningsbaserad prismodell som baseras på varaktigheten för innehåll indata som du indexera. Ytterligare kostnader kan tillkomma för kodning, strömning, lagring, nätverksanvändning och media reserverade enheter. Mer information finns i den [priser](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) sidan.

### <a name="does-video-indexer-offer-a-free-trial"></a>Erbjuder Video Indexer en kostnadsfri utvärderingsversion?

Ja, Video Indexer erbjuder en kostnadsfri utvärderingsversion som ger fullständig tjänst och API-funktioner. Det finns en kvot på 600 minuter värt av videor för webbaserat gränssnitt användare och 2 400 minuter för API-användare. 

## <a name="next-steps"></a>Nästa steg

[Översikt](video-indexer-overview.md)
