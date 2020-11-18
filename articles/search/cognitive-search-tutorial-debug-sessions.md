---
title: 'Självstudie: Använd debug-sessioner för att diagnostisera, åtgärda och genomföra ändringar i din färdigheter'
titleSuffix: Azure Cognitive Search
description: Fel söknings sessioner (för hands version) tillhandahåller ett Portal-baserat gränssnitt för att utvärdera och reparera problem/fel i din färdighetsuppsättningar
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 8ec39c4616f5a34f8326b56d4f0ba6e15cdad91c
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94699125"
---
# <a name="tutorial-diagnose-repair-and-commit-changes-to-your-skillset"></a>Självstudie: diagnostisera, reparera och genomför ändringar i din färdigheter

I den här artikeln ska du använda Azure Portal för att få åtkomst till fel söknings-sessioner för att åtgärda problem med den angivna färdigheter. Färdigheter innehåller några fel som behöver åtgärdas. Den här självstudien tar dig igenom en felsökningssession för att identifiera och lösa problem med indata och utdata från färdigheter.

> [!Important]
> Debug-sessioner är en förhands gransknings funktion utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

> [!div class="checklist"]
> * En Azure-prenumeration. Skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) eller Använd din nuvarande prenumeration
> * En Azure Kognitiv sökning-tjänstinstans
> * Ett Azure Storage konto
> * [Skrivbordsappen Postman](https://www.getpostman.com/)

## <a name="create-services-and-load-data"></a>Skapa tjänster och läsa in data

I den här självstudien används Azure Kognitiv sökning-och Azure Storage-tjänster.

* [Hämta exempel data](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19) som består av 19 filer.

* [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal) eller [hitta ett befintligt konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

   Välj samma region som Azure Kognitiv sökning för att undvika avgifter för bandbredd.
   
   Välj konto typen StorageV2 (General Purpose v2).

* Öppna sidan Storage Services och skapa en behållare. Bästa praxis är att ange åtkomst nivån "privat". Namnge din behållare `clinicaltrialdataset` .

* I behållare klickar du på **överför** för att ladda upp exempelfilerna som du laddade ned och zippa upp i det första steget.

* [Skapa en Azure kognitiv sökning-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Du kan använda en kostnads fri tjänst för den här snabb starten.

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

:::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Hämta en HTTP-slutpunkt och åtkomst nyckel" border="false":::

Alla begär Anden kräver en API-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="create-data-source-skillset-index-and-indexer"></a>Skapa data källa, färdigheter, index och indexerare

I det här avsnittet används Postman och en angiven samling för att skapa Sök tjänstens data källa, färdigheter, index och indexerare.

1. Om du inte har Postman kan du [Hämta appen Postman Desktop här](https://www.getpostman.com/).
1. [Ladda ned Postman-samlingen för debug-sessioner](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)
1. Starta Postman
1. Under **filer**  >  **nya** väljer du den samling som ska importeras.
1. När samlingen har importer ATS expanderar du åtgärds listan (...).
1. Klicka på **Redigera**.
1. Ange namnet på din searchService (till exempel om slut punkten är `https://mydemo.search.windows.net` , så är tjänstens namn " `mydemo` ").
1. Ange apiKey med antingen den primära eller sekundära nyckeln för Sök tjänsten.
1. Ange storageConnectionString på sidan nycklar i ditt Azure Storage-konto.
1. Ange containerName för den behållare som du skapade i lagrings kontot.

> :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="redigera variabler i Postman":::

Samlingen innehåller fyra olika REST-anrop som används för att slutföra det här avsnittet.

Det första anropet skapar data källan. `clinical-trials-ds`. Det andra anropet skapar färdigheter `clinical-trials-ss` . Det tredje anropet skapar indexet `clinical-trials` . Det fjärde och sista anropet skapar indexeraren `clinical-trials-idxr` . När alla anrop i samlingen har slutförts stänger du Postman och återgår till Azure Portal.

> :::image type="content" source="media/cognitive-search-debug/postman-create-data-source.png" alt-text="använda Postman för att skapa data Källa":::

## <a name="check-the-results"></a>Kontrollera resultaten

Färdigheter innehåller några vanliga fel. I det här avsnittet, som kör en tom fråga för att returnera alla dokument, visas flera fel. I efterföljande steg kommer problemen att lösas med en felsökningssession.

1. Gå till din Sök tjänst i Azure Portal. 
1. Välj fliken **index** . 
1. Välj `clinical-trials` index
1. Klicka på **Sök** för att köra en tom fråga. 

När sökningen är färdig visas två fält utan data i listan. "organisationer" och "platser" visas i fönstret. Följ stegen för att identifiera alla problem som skapats av färdigheter.

1. Gå tillbaka till sidan Sök tjänst översikt.
1. Välj fliken **indexerare** . 
1. Klicka på `clinical-trials-idxr` och välj varnings meddelandet. 

Det finns många problem med mappningar för utmatnings fält för projektion och på sidan tre varningar eftersom en eller flera kompetens inmatningar är ogiltiga.

Gå tillbaka till översikts fönstret för Search-tjänsten.

## <a name="start-your-debug-session"></a>Starta felsökningssessionen

> :::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="Starta en ny felsökningssession":::

1. Klicka på fliken Felsök sessioner (för hands version).
1. Välj + NewDebugSession
1. Ge sessionen ett namn. 
1. Anslut sessionen till ditt lagrings konto. 
1. Ange namn på indexeraren. Indexeraren har referenser till data källan, färdigheter och indexet.
1. Godkänn standard dokument valet för det första dokumentet i samlingen. 
1. **Spara** sessionen. Om du sparar sessionen inaktive ras pipelinen för AI-anrikning som definieras av färdigheter.

> [!Important]
> En Felsök-session fungerar bara med ett enda dokument. Ett särskilt dokument i data uppsättningen kan > väljas eller så kommer sessionen att standardvärdet för det första dokumentet.

> :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="En ny felsökningssession har startats":::

När felsökningssessionen har körts, använder sessionen standardvärdet för AI-anrikningen och markerar diagrammet färdighet.

+ Kunskaps diagrammet innehåller en visuell hierarki av färdigheter och dess ordning för körning uppifrån och ned. Färdigheter som är sida vid sida i grafen körs parallellt. Färg kodning av färdigheter i grafen visar vilka typer av färdigheter som körs i färdigheter. I exemplet är de gröna färdigheterna text och den röda kunskapen är vision. Om du klickar på en enskild färdighet i diagrammet visas information om den instansen av kompetensen i den högra rutan i fönstret session. Kunskaps inställningarna, en JSON-redigerare, körnings information och fel/varningar är tillgängliga för granskning och redigering.
+ Den förrikade data strukturen innehåller information om noderna i det berikande trädet som genereras av färdigheterna från käll dokumentets innehåll.

På fliken fel/varningar får du en mycket mindre lista än den som visas tidigare, eftersom den här listan endast innehåller fel för ett enskilt dokument. Precis som i listan som visas av indexeraren kan du klicka på ett varnings meddelande och se information om den här varningen.

## <a name="fix-missing-skill-input-value"></a>Korrigera inmatat värde för kompetens saknas

På fliken fel/varningar finns det ett fel för en åtgärd med etiketten `Enrichment.NerSkillV2.#1` . Informationen i det här felet förklarar att det är problem med värdet "/document/languageCode" för kompetensen. 

1. Gå tillbaka till fliken AI-anrikninger.
1. Klicka på **kunskaps diagrammet**.
1. Klicka på kunskaps etiketten #1 för att visa information i den högra rutan.
1. Leta upp InInformationen för "languageCode".
1. Välj **</>** symbolen i början av raden och öppna uttrycks utvärderaren.
1. Klicka på knappen **utvärdera** för att bekräfta att uttrycket resulterar i ett fel. Det bekräftar att egenskapen "languageCode" inte är en giltig Indatatyp.

> :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="Uttrycks utvärderare":::

Det finns två sätt att undersöka det här felet i sessionen. Det första är att titta på var indatamängden kommer från – vilken kunskap i hierarkin som ska producera det här resultatet? På fliken körningar i fönstret kunskaps information visas källan för indata. Om det inte finns någon källa, indikerar detta ett fel vid fält mappning.

1. Klicka på fliken **körningar** .
1. Titta på indata och hitta "languageCode". Det finns ingen källa för de här inmatade objekten. 
1. Växla till den vänstra rutan för att visa den omfattande data strukturen. Det finns ingen mappad sökväg som motsvarar "languageCode".

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Omfattande data struktur":::

Det finns en mappad sökväg för "språk". Därför finns det ett stavfel i färdighets inställningarna. För att åtgärda detta uttryck i #1-kunskaper med uttrycket '/Document/Language ' måste uppdateras.

1. Öppna uttrycks utvärderaren **</>** för sökvägen "språk".
1. Kopiera uttrycket. Stäng fönstret.
1. Gå till kunskaps inställningarna för #1s kompetensen och öppna uttrycks utvärderaren **</>** för inmatade "languageCode".
1. Klistra in det nya värdet "/Document/Language" i resultat rutan och klicka på **utvärdera**.
1. Den ska visa rätt Indatatyp "sv". Klicka på Använd för att uppdatera uttrycket.
1. Klicka på **Spara** i rutan till höger, kunskaps information.
1. Klicka på **Kör** på sessionens fönster-meny. Detta startar en annan körning av färdigheter med hjälp av dokumentet. 

När debug-sessionen har körts klart klickar du på fliken fel/varningar så visas att det fel som har etiketten "berikning. NerSkillV2. #1" är borta. Det finns dock fortfarande två varningar om att tjänsten inte kunde mappa utmatnings fält för organisationer och platser till Sök indexet. Det saknas värden: "/Document/merged_content/ORGANIZATIONS" och "/Document/merged_content/locations".

## <a name="fix-missing-skill-output-values"></a>Åtgärda saknade värden för kunskaps utdata

> :::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Fel och varningar":::

Det saknas värden för utdata från en färdighet. Om du vill identifiera kunskapen med felet går du till den fördefinierade data strukturen och letar reda på värde namnet och tittar på den ursprungliga källan. När det gäller de saknade organisationer och plats värden, är de utdata från färdighets #1. Om du öppnar uttrycks utvärderaren </> för varje sökväg visas uttrycken som anges som "/Document/Content/organizations" respektive "/Document/Content/locations".

> :::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="Organisations enhet för uttrycks utvärderare":::

Utdata för dessa entiteter är tom och får inte vara tom. Vad är indata som producerar det här resultatet?

1. Gå till **färdighets diagram** och välj kunskaps #1.
1. Välj fliken **körningar** i den högra kunskaps informations rutan.
1. Öppna uttrycks utvärderaren **</>** för INmatad text.

> :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Inmatade för text kunskaper":::

Det visade resultatet för den här indatamängden ser inte ut som ett text flöde. Det ser ut som en bild som är omgiven av nya rader. Bristen på text innebär att inga entiteter kan identifieras. När du tittar på hierarkin för färdigheter visas innehållet först när du bearbetas av #6 (OCR)-kompetensen och sedan skickas till #5 (slå samman) kunskap. 

1. Välj #5 (slå samman) kompetensen i **färdighets diagrammet**.
1. Välj fliken **körningar** i den högra kunskaps informations rutan och öppna uttrycks utvärderaren **</>** för utmatningarna "mergedText".

> :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Utdata för kopplings kunskaper":::

Här är texten länkad till bilden. Det går inte att titta på uttrycket "/Document/merged_content" i sökvägen "organisationer" och "platser" för #1-kunskapen. I stället för att använda '/Document/Content ' bör det använda/Document/merged_content för text inmatningar.

1. Kopiera uttrycket för "mergedText"-utdata och Stäng fönstret uttrycks utvärderare.
1. Välj färdighets #1 i **färdighets diagrammet**.
1. Välj fliken **kunskaps inställningar** i rutan höger kunskaps information.
1. Öppna uttrycks utvärderaren **</>** för texten "text".
1. Klistra in det nya uttrycket i rutan. Klicka på **utvärdera**.
1. Rätt inmatare med den tillagda texten ska visas. Klicka på **Använd** för att uppdatera kunskaps inställningarna.
1. Klicka på **Spara** i rutan till höger, kunskaps information.
1. Klicka på **Kör** på menyn sessions-fönster. Detta startar en annan körning av färdigheter med hjälp av dokumentet.

När indexeraren har körts är felen fortfarande där. Gå tillbaka till färdighets #1 och undersök. Indatamängden till färdigheten korrigerades till merged_content från Content. Vilka är utmatningarna för dessa entiteter i kompetensen?

1. Välj fliken **AI-anrikninger** .
1. Välj **färdighets diagram** och klicka på kompetens #1.
1. Navigera bland **kunskaps inställningarna** för att hitta "utdata".
1. Öppna uttrycks utvärderaren **</>** för entiteten organisationer.

> :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="Utdata för organisationer-entitet":::

Utvärdering av resultatet av uttrycket ger rätt resultat. Kunskapen fungerar för att identifiera rätt värde för entiteten, "organisationer". Men utdata-mappningen i entitetens sökväg ger fortfarande ett fel. I jämförelsen av sökvägen för utdata i kunskapen till utdatafilen i fel meddelandet är det den kunskap som översätter utmatningarna, organisationer och platser under/Document/Content-noden. Även om mappningen av utdatakolumner förväntar sig att resultaten ska överordnas under noden/Document/merged_content. I föregående steg ändrades indatatypen från '/Document/Content ' till '/Document/merged_content '. Du måste ändra sammanhanget i kunskaps inställningarna för att se till att utdata genereras med rätt kontext.

1. Välj fliken **AI-anrikninger** .
1. Välj **färdighets diagram** och klicka på kompetens #1.
1. Navigera bland **kunskaps inställningarna** för att hitta "context".
1. Dubbelklicka på inställningen för "context" och redigera den för att läsa "/Document/merged_content".
1. Klicka på **Spara** i rutan till höger, kunskaps information.
1. Klicka på **Kör** på menyn sessions-fönster. Detta startar en annan körning av färdigheter med hjälp av dokumentet.

> :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Kontext korrigering i kompetens inställning":::

Alla fel har åtgärd ATS.

## <a name="commit-changes-to-the-skillset"></a>Genomför ändringar i färdigheter

När felsökningssessionen initierades skapade Sök tjänsten en kopia av färdigheter. Detta skedde så att ändringar som gjorts inte påverkar produktions systemet. Nu när du har slutfört fel sökningen av färdigheter kan du utföra korrigeringarna (skriva över den ursprungliga färdigheter) till produktions systemet. Om du vill fortsätta att göra ändringar i färdigheter utan att påverka produktions systemet, kan felsökningssessionen sparas och öppnas igen senare.

1. Klicka på **genomför ändringar** i huvud menyn för fel söknings sessioner.
1. Klicka på **OK** för att bekräfta att du vill uppdatera din färdigheter.
1. Stäng felsökningssessionen och välj fliken **indexerare** .
1. Öppna din "kliniska-tests-idxr".
1. Klicka på **Återställ**.
1. Klicka på **Kör**. Bekräfta genom att klicka på **OK** .

När indexeraren har slutförts bör det vara en grön bock och ordet lyckades bredvid tidsstämpeln för den senaste körningen på fliken körnings historik. Så här kontrollerar du att ändringarna har tillämpats:

1. Avsluta **indexeraren** och välj fliken **index** .
1. Öppna indexet "kliniska-tests" och klicka på **Sök** på fliken Sök Utforskaren.
1. Resultat fönstret bör visa att entiteter organisationer och platser nu fylls med förväntade värden.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om färdighetsuppsättningar](./cognitive-search-working-with-skillsets.md) 
>  [Läs mer om stegvis anrikning och cachelagring](./cognitive-search-incremental-indexing-conceptual.md)