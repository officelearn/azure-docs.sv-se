---
title: Azure-resurser - QnA Maker
description: QnA Maker använder flera Azure-källor, var och en med ett annat syfte. Om du förstår hur de används individuellt kan du planera för och välja rätt prisnivå eller veta när du ska ändra din prisnivå. Förstå hur de används i kombination kan du hitta och åtgärda problem när de uppstår.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 581029d2372f7a2ef704dcf02f266b66440aa246
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873913"
---
# <a name="azure-resources-for-qna-maker"></a>Azure-resurser för QnA Maker

QnA Maker använder flera Azure-källor, var och en med ett annat syfte. Om du förstår hur de används individuellt kan du planera för och välja rätt prisnivå eller veta när du ska ändra din prisnivå. Förstå hur de används _i kombination_ kan du hitta och åtgärda problem när de uppstår.

## <a name="resource-planning"></a>Resursplanering

När du först utvecklar en QnA Maker-kunskapsbas, i prototypfasen, är det vanligt att ha en enda QnA Maker-resurs för både testning och produktion.

När du går in i projektets utvecklingsfas bör du tänka på följande:

* hur många språk ditt kunskapsbassystem kommer att innehålla
* hur många regioner du behöver din kunskapsbas för att vara tillgänglig i/från
* hur många dokument i varje domän som ditt system kommer att innehålla

Planera att ha en enda QnA Maker-resurs som innehåller alla kunskapsbaser som har samma språk, samma region och samma ämnesdomänkombination.

## <a name="pricing-tier-considerations"></a>Prisnivåöverväganden

Vanligtvis finns det tre parametrar som du måste tänka på:

* **Dataflödet du behöver från tjänsten:**
    * Välj lämplig [appplan](https://azure.microsoft.com/pricing/details/app-service/plans/) för din App-tjänst baserat på dina behov. Du kan [skala upp](https://docs.microsoft.com/azure/app-service/manage-scale-up) eller ned appen.
    * Detta bör också påverka ditt Azure **Cognitive Search** SKU-val, se mer information [här](https://docs.microsoft.com/azure/search/search-sku-tier). Dessutom kan du behöva justera kognitiv sökning [kapacitet](../../../search/search-capacity-planning.md) med repliker.

* **Storlek och antal kunskapsbaser**: Välj lämplig [Azure-sökning SKU](https://azure.microsoft.com/pricing/details/search/) för ditt scenario. Vanligtvis bestämmer du antalet kunskapsbaser som du behöver baserat på antalet olika ämnesområden. När ämnesdomänen (för ett enda språk) ska finnas i en kunskapsbas.

    Du kan publicera N-1-kunskapsbaser på en viss nivå, där N är de högsta index som tillåts på nivån. Kontrollera också den maximala storleken och antalet dokument som tillåts per nivå.

    Om din nivå till exempel har 15 tillåtna index kan du publicera 14 kunskapsbaser (1 index per publicerad kunskapsbas). Det femtonde indexet används för alla kunskapsbaser för redigering och testning.

* **Antal dokument som källor:** Den kostnadsfria SKU:n för QnA Maker-hanteringstjänsten begränsar antalet dokument som du kan hantera via portalen och API:erna till 3 (av 1 MB storlek vardera). Standard-SKU har inga gränser för hur många dokument du kan hantera. Se mer information [här](https://aka.ms/qnamaker-pricing).

Följande tabell ger dig några riktlinjer på hög nivå.

|                        | QnA Maker Förvaltning | App Service | Azure Cognitive Search | Begränsningar                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentering        | Gratis SKU             | Gratis nivå   | Gratis nivå    | Publicera upp till 2 KBs, 50 MB storlek  |
| Utvecklings-/testmiljö   | Standard-SKU         | Delad      | Basic        | Publicera upp till 14 KBs, 2 GB storlek    |
| Produktionsmiljö | Standard-SKU         | Basic       | Standard     | Publicera upp till 49 KBs, 25 GB storlek |

## <a name="recommended-settings"></a>Rekommenderade inställningar

|Mål QPS | App Service | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 Instans   | S1, 1 Instans    |
| 50         | S3, 10 instanser       | S1, 12 instanser         |
| 80         | S3, 10 instanser      |  S3, 12 instanser  |
| 100         | P3V2, 10 instanser  | S3, 12 instanser, 3 partitioner   |
| 200 till 250         | P3V2, 20 instanser | S3, 12 instanser, 3 partitioner    |

## <a name="when-to-change-a-pricing-tier"></a>När du ska ändra en prisnivå

|Uppgradera|Orsak|
|--|--|
|[Uppgradera](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) QnA Maker förvaltning SKU|Du vill ha fler QnA-par eller dokumentkällor i kunskapsbasen.|
|[Uppgradera](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) App Service SKU och kontrollera kognitiv söknivå och [skapa kognitiva sökrepliker](../../../search/search-capacity-planning.md)|Din kunskapsbas måste visa fler förfrågningar från din klientapp, till exempel en chattrobot.|
|[Uppgradera](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure Cognitive Search-tjänst|Du planerar att ha många kunskapsbaser.|

Hämta de senaste körningsuppdateringarna genom [att uppdatera din App-tjänst i Azure-portalen](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="resource-naming-considerations"></a>Resursnamngivningsöverväganden

Resursnamnet för QnA Maker-resursen, till exempel `qna-westus-f0-b`, används också för att namnge de andra resurserna.

Med Azure-portalens skapa fönster kan du skapa en QnA Maker-resurs och välja prisnivåer för de andra resurserna.

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure-portal för QnA Maker-resursskapande](../media/concept-azure-resource/create-blade.png)

När resurserna har skapats har de samma namn, förutom den valfria Application Insights-resursen, som efterdämnar tecken till namnet.

> [!div class="mx-imgBorder"]
> ![Skärmbild av Azure Portal-resurslista](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Skapa en ny resursgrupp när du skapar en QnA Maker-resurs. På så sätt kan du se alla resurser som är associerade med QnA Maker-resursen när du söker efter resursgrupp.

> [!TIP]
> Använd en namngivningskonvention för att ange prisnivåer i resursens eller resursgruppens namn. När du får felmeddelanden från att skapa en ny kunskapsbas eller lägga till nya dokument är prisnivån för kognitiv sökning ett vanligt problem.

## <a name="resource-purposes"></a>Resursändamål

Varje Azure-resurs som skapas med QnA Maker har ett specifikt syfte:

* QnA Maker-resurs
* Resurs för kognitiv sökning
* App Service
* Tjänsten App Plan
* Tjänsten Application Insights


### <a name="cognitive-search-resource"></a>Resurs för kognitiv sökning

Cognitive [Search-resursen](../../../search/index.yml) används för att:

* Lagra QnA-paren
* Ange den ursprungliga rankningen (ranker #1) för QnA-paren vid körning

#### <a name="index-usage"></a>Indexanvändning

Resursen håller ett index för att fungera som testindex och de återstående indexen korrelerar till en publicerad kunskapsbas vardera.

En resurs prissatt till 15 index, kommer att innehålla 14 publicerade kunskapsbaser, och ett index används för att testa alla kunskapsbaser. Det här testindexet partitioneras efter kunskapsbas så att en fråga som använder den interaktiva testfönstret använder testindexet men returnerar endast resultat från den specifika partition som är associerad med den specifika kunskapsbasen.

#### <a name="language-usage"></a>Språkanvändning

Den första kunskapsbasen som skapas i QnA Maker-resursen används för att bestämma den _enda_ språkuppsättningen för cognitive search-resursen och alla dess index. Du kan bara ha _ett språk inställt_ för en QnA Maker-tjänst.

### <a name="qna-maker-resource"></a>QnA Maker-resurs

QnA Maker-resursen ger tillgång till API:er för redigering och publicering samt NLP-baserat andra rankningslager (ranker #2) för QnA-paren vid körning.

Den andra rankningen gäller intelligenta filter som kan innehålla metadata och uppföljningsansaner.

#### <a name="qna-maker-resource-configuration-settings"></a>Konfigurationsinställningar för QnA Maker-resurs

När du skapar en ny kunskapsbas i [QnA Maker-portalen](https://qnamaker.ai)är **språkinställningen** den enda inställningen som tillämpas på resursnivå. Du väljer språk när du skapar resursens första kunskapsbas.

### <a name="app-service-and-app-service-plan"></a>Apptjänst och apptjänstplan

[App-tjänsten](../../../app-service/index.yml) används av klientprogrammet för att komma åt de publicerade kunskapsbaserna via slutpunkten för körning.

Om du vill fråga efter den publicerade kunskapsbasen använder alla publicerade kunskapsbaser samma URL-slutpunkt, men anger **kunskapsbas-ID:n** i flödet.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) används för att samla in chattloggar och telemetri. Gå igenom de vanliga [Kusto-frågorna](../how-to/get-analytics-knowledge-base.md) om du vill ha information om din tjänst.

## <a name="share-services-with-qna-maker"></a>Dela tjänster med QnA Maker

QnA Maker skapar flera Azure-resurser. Om du vill minska hanteringen och dra nytta av kostnadsdelning använder du följande tabell för att förstå vad du kan och inte kan dela:

|Tjänst|Dela|Orsak|
|--|--|--|
|Cognitive Services|X|Inte möjligt avsiktligt|
|App Service-plan|✔|Fast diskutrymme som allokerats för en App Service-plan. Om andra appar som delar samma App Service-plan använder betydande diskutrymme uppstår problem med QnAMaker App Service-instansen.|
|App Service|X|Inte möjligt avsiktligt|
|Application Insights|✔|Kan delas|
|Söktjänst|✔|1. `testkb` är ett reserverat namn för QnAMaker-tjänsten; det kan inte användas av andra.<br>2. Synonymkarta med `synonym-map` namnet är reserverad för QnAMaker-tjänsten.<br>3. Antalet publicerade kunskapsbaser begränsas av söktjänstnivå. Om det finns gratis index tillgängliga kan andra tjänster använda dem.|

### <a name="using-a-single-cognitive-search-service"></a>Använda en enda cognitive search-tjänst

Om du skapar en QnA-tjänst och dess beroenden (till exempel Sök) via portalen skapas en söktjänst åt dig och länkas till QnA Maker-tjänsten. När dessa resurser har skapats kan du uppdatera apptjänstinställningen för att använda en tidigare befintlig söktjänst och ta bort den du just skapade.

Lär dig hur du [konfigurerar](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA Maker för att använda en annan Cognitive Service-resurs än den som skapats som en del av QnA Maker-resursskapandeprocessen.

## <a name="management-service-region"></a>Region för hanteringstjänster

QnA Maker hanteringstjänst används endast för QnA Maker-portalen och för inledande databehandling. Den här tjänsten är endast tillgänglig i regionen **västra USA.** Inga kunddata lagras i den här tjänsten i västra USA.

## <a name="keys-in-qna-maker"></a>Nycklar i QnA Maker

Din QnA Maker-tjänst behandlar två typer av nycklar: redigering av **nycklar** och **frågeslutpunktsnycklar** som används med körningen som finns i App-tjänsten.

Om du letar efter **din prenumerationsnyckel** [har terminologin ändrats](#subscription-keys).

Använd dessa nycklar när du gör förfrågningar till tjänsten via API:er.

![Nyckelhantering](../media/qnamaker-how-to-key-management/key-management.png)

|Namn|Location|Syfte|
|--|--|--|
|Skapa nyckel|[Azure-portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Dessa nycklar används för att komma åt [QnA Maker-hanteringstjänsten API:er](https://go.microsoft.com/fwlink/?linkid=2092179). Med de här API:erna kan du redigera frågorna och svaren i din kunskapsbas och publicera kunskapsbasen. Dessa nycklar skapas när du skapar en ny QnA Maker-tjänst.<br><br>Hitta dessa nycklar på **cognitive services-resursen** på sidan **Nycklar.**|
|Frågeslutpunktsnyckel|[QnA Maker-portalen](https://www.qnamaker.ai)|Dessa nycklar används för att fråga den publicerade kunskapsbasens slutpunkt för att få ett svar på en användarfråga. Du använder vanligtvis den här frågeslutpunkten i chattroboten eller i klientprogramkoden som ansluter till QnA Maker-tjänsten. Dessa nycklar skapas när du publicerar din QnA Maker-kunskapsbas.<br><br>Leta reda på dessa nycklar på sidan **Tjänstinställningar.** Hitta den här sidan från användarens meny längst upp till höger på sidan på rullgardinsmenyn.|

### <a name="subscription-keys"></a>Prenumerationsnycklar

Termerna författande och frågeslutpunktsnyckel är korrigerande termer. Föregående term var **prenumerationsnyckel**. Om du ser annan dokumentation som refererar till prenumerationsnycklar motsvarar dessa författar- och frågeslutpunktsnycklar (används i körningen).

Du måste veta vad nyckeln är åtkomst till, kunskapsbashantering eller kunskapsbasfråga, för att veta vilken nyckel du behöver hitta.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [QnA Maker-kunskapsbasen](knowledge-base.md)
* Förstå en [kunskapsbas livscykel](development-lifecycle-knowledge-base.md)
* Granska [gränser](../limits.md) för service och kunskapsbas

