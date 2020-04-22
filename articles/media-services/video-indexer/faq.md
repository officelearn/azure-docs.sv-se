---
title: Vanliga frågor och svar om Video Indexer - Azure
titleSuffix: Azure Media Services
description: Den här artikeln innehåller svar på vanliga frågor om Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: dc57978dd881532cab59150dec921df9ffa958c3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767213"
---
# <a name="video-indexer-frequently-asked-questions"></a>Video Indexer ofta ställde frågor

Den här artikeln besvarar vanliga frågor om Video Indexer.

## <a name="general-questions"></a>Allmänna frågor

### <a name="what-is-video-indexer"></a>Vad är Video Indexer?

Video Indexer är en artificiell underrättelsetjänst som är en del av Microsoft Azure Media Services. Video Indexer tillhandahåller en orkestrering av flera maskininlärningsmodeller som gör att du enkelt kan extrahera djup insikt från en video. För att ge avancerade och korrekta insikter använder Video Indexer flera kanaler i videon: ljud, tal och visuellt. Video Indexerers insikter kan användas på många sätt, till exempel förbättra innehållsupptäcktbarhet och tillgänglighet, skapa nya möjligheter till intäktsgenerering eller skapa nya upplevelser som använder insikterna. Video Indexer tillhandahåller ett webbaserat gränssnitt för testning, konfiguration och anpassning av modeller i ditt konto. Utvecklare kan använda ett REST-baserat API för att integrera Video Indexer i produktionssystemet. 

### <a name="what-can-i-do-with-video-indexer"></a>Vad kan jag göra med Video Indexer?

Några av de åtgärder som Video Indexer kan utföra på mediefiler är:

* Identifiera och extrahera tal och identifiera talare.
* Identifiera och extrahera text på skärmen i en video.
* Identifiera objekt i en videofil.
* Identifiera varumärken (till exempel Microsoft) från ljudspår och skärmtext i en video.
* Upptäcka och känna igen ansikten från en databas med kändisar och en användardefinierad databas med ansikten.
* Extrahera ämnen som diskuteras men inte nödvändigtvis nämns i ljud- och videoinnehåll.
* Skapa dold textning eller undertexter från ljudspåret.

Mer information och fler videoindexeringsfunktioner finns i [Översikt](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Hur kommer jag igång med Video Indexer?

Video Indexer innehåller en gratis testperiod erbjudande som ger dig 600 minuter i det webbaserade gränssnittet och 2.400 minuter via API. Du kan [logga in på det webbaserade gränssnittet Video Indexer](https://www.videoindexer.ai/) och prova själv med hjälp av vilken webbidentitet som helst och utan att behöva konfigurera en Azure-prenumeration. Följ [detta enkla introduktionslabb](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md) för att få bättre uppfattning om hur du använder Video Indexer.

Om du vill indexera videor och ljudflugor i stor skala kan du ansluta Video Indexer till en betald Microsoft Azure-prenumeration. Du hittar mer information om priser på [prissidan.](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)

Du hittar mer information om hur du kommer igång i [Kom igång](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Behöver jag kodningskunskaper för att använda Video Indexer?

Du kan använda det webbaserade gränssnittet Video Indexer för att utvärdera, konfigurera och hantera ditt konto **utan att kodning krävs**.  När du är redo att utveckla mer komplexa program kan du använda [API:et för videoindexer](https://api-portal.videoindexer.ai/) för att integrera videoindexer i dina egna program, webbplatser eller [anpassade arbetsflöden med hjälp av serverlösa tekniker som Azure Logic Apps](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) eller Azure Functions.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Behöver jag maskininlärningsfärdigheter för att använda Video Indexer?

Nej, Video Indexer tillhandahåller integrering av flera maskininlärningsmodeller i en pipeline. Indexering av en video- eller ljudfil via Video Indexer hämtar en fullständig uppsättning insikter som extraherats på en delad tidslinje utan maskininlärningsfärdigheter eller kunskaper om algoritmer som behövs från kundens sida.

### <a name="what-media-formats-does-video-indexer-support"></a>Vilka medieformat stöder Video Indexer?

Video Indexer stöder de vanligaste medieformaten. Mer information finns i [standardformatlistan för Azure Media Encoder.](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats)

### <a name="how-do-i-upload-a-media-file-into-video-indexer-and-what-are-the-limitations"></a>Hur laddar jag upp en mediefil till Video Indexer och vilka är begränsningarna?

I den webbaserade portalen Video Indexer kan du ladda upp en mediefil med hjälp av dialogrutan filöverföring eller genom att peka på en URL som är direkt värd för källfilen (se [exempel](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). Alla webbadresser som är värdar för medieinnehållet med hjälp av en iFrame- eller inbäddningskod fungerar inte (se [exempel](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)). 

För mer information, se denna [hur-till guide](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos).

#### <a name="limitations"></a>Begränsningar

* Namnet på videon får inte överstiga 80 tecken.
* Om du laddar upp en video med byte array är videostorleken begränsad till 2 GB (och 30 GB när du använder URL). 

Den omfattande listan finns i [Ladda upp överväganden och begränsningar](upload-index-videos.md#uploading-considerations-and-limitations).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Hur lång tid tar det videoindexer att extrahera insikter från media?

Hur lång tid det tar att indexera en video- eller ljudfil, både med hjälp av VIDEO Indexer API och det webbaserade gränssnittet Video Indexer, beror på flera parametrar, till exempel filens längd och kvalitet, antalet insikter som finns i filen, antalet [tillgängliga reserverade enheter](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) och om slutpunkten för [direktuppspelning](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview) är aktiverad eller inte. Vi rekommenderar att du kör några testfiler med ditt eget innehåll och tar ett genomsnitt för att få en bättre uppfattning.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Kan jag skapa anpassade arbetsflöden för att automatisera processer med Video Indexer?

Ja, du kan integrera Video Indexer i serverlösa tekniker som Logic Apps, Flow och [Azure Functions](https://azure.microsoft.com/services/functions/). Du hittar mer information om [Logic App-](https://azure.microsoft.com/services/logic-apps/) och [Flow-kopplingarna](https://flow.microsoft.com/en-us/) för Video Indexer [här](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/). Du kan se vissa automatiseringsprojekt som utförs av partner i [repoeringen för videoindexeringsprover.](https://github.com/Azure-Samples/media-services-video-indexer)

### <a name="in-which-azure-regions-is-video-indexer-available"></a>I vilka Azure-regioner är Video indexerare tillgänglig?

Du kan se vilka Azure-regioner Video Indexer är tillgänglig på [regionssidan.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)

### <a name="can-i-customize-video-indexer-models-for-my-specific-use-case"></a>Kan jag anpassa videoindexermodeller för mitt specifika användningsfall? 

Ja. I Video Indexer kan du anpassa några av de tillgängliga modellerna för att bättre passa dina behov. 

Vår personmodell stöder till exempel out-of-the-box 1.000.000 ansikten kändis erkännande, men du kan också träna den att känna igen andra ansikten som inte finns i databasen. 

Mer information finns i artiklar om hur du anpassar [modeller för person,](customize-person-model-overview.md) [varumärken](customize-brands-model-overview.md)och [språk.](customize-language-model-overview.md) 

###  <a name="can-i-edit-the-videos-in-my-library"></a>Kan jag redigera videorna i mitt bibliotek?

Ja. Tryck på **knappen redigera video** från biblioteksvisningen eller knappen öppna i **redigeraren** från spelarvisningen för att komma till fliken **Projekt.** Du kan skapa ett nytt projekt och lägga till fler videor från biblioteket för att redigera dem tillsammans, när du är klar kan du återge din video och ladda ner. 

Om du vill få insikter om din nya video indexerar du den med Video Indexer så visas den i biblioteket med dess insikter.

### <a name="what-is-the-sla-for-video-indexer"></a>Vad är SLA för Video Indexer?

Azure Media Service SLA täcker Video Indexer och finns på [SLA-sidan.](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) Serviceavtalet gäller endast betalkonton för videoindexer och gäller inte för den kostnadsfria provperioden.

## <a name="privacy-questions"></a>Sekretessfrågor

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Lagras video- och ljudfiler av Video Indexer?

Ja, om du inte tar bort filen från Video Indexer, antingen med videoindexeringswebbplatsen eller API:et, lagras dina video- och ljudfiler. För den kostnadsfria utvärderingsversionen lagras de video- och ljudfiler som du indexerar i Azure-regionen östra USA. Annars lagras dina video- och ljudfiler i lagringskontot för din Azure-prenumeration.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Kan jag ta bort mina filer som lagras i Video Indexer Portal?

Ja, du kan alltid ta bort dina video- och ljudfiler samt alla metadata och insikter som extraherats från dem av Video Indexer. När du har tagit bort en fil från Video Indexer tas filen och dess metadata och insikter bort permanent från Video Indexer. Men om du har implementerat din egen säkerhetskopieringslösning i Azure-lagring finns filen kvar i din Azure-lagring.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Kan jag styra användarnas åtkomst till mitt Video Indexer-konto?

Ja, bara kontoadministratörer kan bjuda in och ta bort inbjudan till sina konton, samt tilldela vem som har redigeringsbehörighet och vem som har skrivskyddad åtkomst.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Vem har tillgång till mina video- och ljudfiler som har indexerats och/eller lagrats av Video Indexer och de metadata och insikter som extraherades?

Ditt video- eller ljudinnehåll som är offentligt som sekretessinställning kan nås av alla som har länken till ditt video- eller ljudinnehåll och dess insikter. Video- eller ljudinnehåll som har privat som sekretessinställning kan endast nås av användare som har bjudits in till kontot för video- eller ljudinnehållet. Sekretessinställningen för ditt innehåll gäller även för metadata och insikter som Video Indexer extraherar. Du tilldelar sekretessinställningen när du laddar upp video- eller ljudfilen. Du kan också ändra sekretessinställningen efter indexering.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Vilken åtkomst har Microsoft till mina video- eller ljudfiler som har indexerats och/eller lagrats av Video Indexer och de metadata och insikter som extraherades?

Enligt [Villkoren](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) för Azure Online Services (OST) äger du helt ditt innehåll, och Microsoft kommer bara att komma åt ditt innehåll och de metadata och insikter som Video Indexer extraherar från ditt innehåll enligt OST och Microsofts sekretesspolicy.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Är de anpassade modeller som jag bygger i mitt Video Indexer-konto tillgängliga för andra konton?

 Nej, de anpassade modeller som du skapar i ditt konto är inte tillgängliga för något annat konto. Video Indexer kan du för närvarande bygga anpassade [varumärken,](customize-brands-model-overview.md) [språk](customize-language-model-overview.md)och [personmodeller](customize-person-model-overview.md) i ditt konto. Dessa modeller är endast tillgängliga i kontot där du skapade modellerna.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Finns innehållet indexerat av Video Indexer inom Azure-regionen där jag använder Video Indexer?

Ja, innehållet och dess insikter sparas inom Azure-regionen om du inte har en manuell konfiguration i din Azure-prenumeration som använder flera Azure-regioner. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Vad är sekretesspolicyn för Video Indexer?

Video Indexer omfattas av [Microsofts sekretesspolicy](https://privacy.microsoft.com/privacystatement). Sekretesspolicyn förklarar de personuppgifter som Microsoft behandlar, hur Microsoft behandlar dem och för vilka ändamål Microsoft behandlar dem. Mer information om sekretess finns i [Microsoft Trust Center](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Vilka certifieringar har Video Indexer?

Video Indexer har för närvarande SOC-certifieringen. Om du vill granska Video Indexers certifiering läser du [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

### <a name="what-is-the-difference-between-private-and-public-videos"></a>Vad är skillnaden mellan privata och offentliga videor? 

När videor laddas upp till Video Indexer kan du välja mellan två sekretessinställningar: privata och offentliga. Offentliga videor är tillgängliga för alla, inklusive anonyma och oidentifierade användare. Privata begränsas endast till kontomedlemmarna. 

### <a name="i-tried-to-upload-a-video-as-public-and-it-was-flagged-for-inappropriate-or-offensive-content-what-does-that-mean"></a>Jag försökte ladda upp en video som offentlig och det flaggades för olämpligt eller stötande innehåll, vad betyder det? 

När du laddar upp en video till Video Indexer görs en automatisk innehållsanalys av algoritmer och modeller för att se till att inget olämpligt innehåll presenteras offentligt. Om det visar sig att en video är misstänkt som innehåller explicit innehåll är det inte möjligt att ange den som offentlig. Kontomedlemmarna kan dock fortfarande komma åt den som en privat video (visa den, hämta insikter och extraherade artefakter och utföra andra åtgärder som är tillgängliga för kontomedlemmar).   

För att ställa in videon för allmänheten kan du antingen: 

* Skapa ett eget gränssnittslagret (till exempel app eller webbplats) och använd det för att interagera med videoindexeringstjänsten. På så sätt förblir videon privat i vår portal och dina användare kan interagera med den via ditt gränssnitt. Du kan till exempel fortfarande få insikter eller tillåta visning av videon i ditt eget gränssnitt. 
* Begär en mänsklig granskning av innehållet, vilket skulle leda till att begränsningen tas bort förutsatt att innehållet inte är explicit. 

    Det här alternativet kan utforskas om videoindexerarens webbplats används direkt av användarna som gränssnittslagret och för offentlig (oautentiserade) visning. 

## <a name="api-questions"></a>API-frågor

### <a name="what-apis-does-video-indexer-offer"></a>Vilka API:er erbjuder Video Indexer?

Video Indexeres API:er gör det möjligt att indexera, extrahera metadata, kapitalförvaltning, översättning, inbäddning, anpassning av modeller med mera. Mer detaljerad information om hur du använder API:et för videoindexerare finns i [utvecklarportalen för videoindexeringsindexer](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Vilka klient-SDK-filer erbjuder Video Indexer?

Det finns för närvarande inga klient-SDK:er som erbjuds. Video Indexer-teamet arbetar på SDK:erna och planerar att leverera dem snart.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Hur kommer jag igång med Video Indexers API?

Följ [självstudiekurs: kom igång med API:et för videoindexerare](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Vad är skillnaden mellan API:et för videoindexer och Azure Media Service v3 API?

För närvarande finns det vissa överlappningar i funktioner som erbjuds av Api för videoindexer och Azure Media Service v3 API. Du hittar mer information om hur du jämför båda tjänsterna [här](compare-video-indexer-with-media-services-presets.md).

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>Vad är en API-åtkomsttoken och varför behöver jag den?

Api:et för videoindexerare innehåller ett Auktoriserings-API och ett operations-API. API:et för auktoriseringar innehåller anrop som ger dig åtkomsttoken. Varje anrop till åtgärds-API:t ska associeras med en åtkomsttoken, som matchar auktoriseringsområdet för anropet.

Åtkomsttoken behövs för att använda VIDEO Indexer API:er av säkerhetsskäl. Detta säkerställer att alla samtal kommer från dig eller de som har åtkomstbehörighet till ditt konto. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Vad är skillnaden mellan kontoåtkomsttoken, användaråtkomsttoken och videoåtkomsttoken?

* Kontonivå – med åtkomsttoken på kontonivå kan du utföra åtgärder på kontonivån eller videonivån. Ladda till exempel upp en video, lista alla videor, få videoinsikter.
* Användarnivå - åtkomsttoken på användarnivå låter dig utföra åtgärder på användarnivå. Till exempel hämta associerade konton.
* Videonivå – med åtkomsttoken på videonivå kan du utföra åtgärder på en specifik video. Till exempel få videoinsikter, ladda ned textning, få widgetar osv.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Hur ofta behöver jag skaffa en ny åtkomsttoken? När upphör åtkomsttoken?

Åtkomsttoken upphör att gälla varje timme, så du måste generera en ny åtkomsttoken varje timme. 

### <a name="what-are-the-login-options-to-video-indexer-developer-portal"></a>Vilka är inloggningsalternativen till Video Indexer Developer portal?

Du kan logga in med Azure AD, Microsoft-konto, Google-konto eller Facebook-konto. 

När du har registrerat ditt e-postkonto med en identitetsleverantör kan du inte använda det här e-postkontot hos en annan identitetsleverantör.

## <a name="billing-questions"></a>Frågor om fakturering

### <a name="how-much-does-video-indexer-cost"></a>Hur mycket kostar Video Indexer?

Video Indexer använder en enkel prismodell för användningsbaserad betalning baserat på varaktigheten av den innehållsinmatning som du indexerar. Ytterligare avgifter kan tillkomma för kodning, direktuppspelning, lagring, nätverksanvändning och mediereserverade enheter. Mer information finns på [prissidan.](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)

### <a name="when-am-i-billed-for-using-video-indexer"></a>När faktureras jag för att använda Video Indexer?

När du skickar en video som ska indexeras, kommer användaren att definiera indexering vara videoanalys, ljudanalys eller båda. Detta avgör vilka SKU:er som ska debiteras. Om det finns ett kritiskt nivåfel under bearbetningen returneras en felkod som svar. I sådana fall sker ingen fakturering.  Ett kritiskt fel kan orsakas av ett fel i vår kod eller ett kritiskt fel i ett internt beroende som tjänsten har. Fel som felaktig identifiering eller insiktsextrahering betraktas inte som kritiska och ett svar returneras. I alla fall där ett giltigt (icke-felkod) svar returneras, fakturering sker.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>Erbjuder Video Indexer en kostnadsfri provperiod?

Ja, Video Indexer erbjuder en gratis testperiod som ger full service och API-funktionalitet. Det finns en kvot på 600 minuter till ett värde av videor för webbaserade gränssnittsanvändare och 2 400 minuter för API-användare. 

## <a name="next-steps"></a>Nästa steg

[Översikt](video-indexer-overview.md)
