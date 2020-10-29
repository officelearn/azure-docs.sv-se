---
title: Flytta instansen till en annan Azure-region
titleSuffix: Azure Digital Twins
description: Se hur du flyttar en digital Azure-instans från en Azure-region till en annan.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: e586e9acc9510dc1aaae511fa51e5a0c3255bd8f
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026504"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Flytta en digital Azure-instans till en annan Azure-region

Om du behöver flytta din Azure Digital-instansen från en region till en annan, är den aktuella processen att **återskapa dina resurser i den nya regionen** och sedan ta bort de ursprungliga resurserna. I slutet av den här processen kommer du att arbeta med en ny Azure Digital-instans som är identisk med den första, förutom den uppdaterade platsen.

Den här artikeln innehåller rikt linjer för hur du utför en fullständig flyttning och kopierar över allt du behöver för att göra den nya instansen att matcha originalet.

Den här processen omfattar följande steg:
1. Förbered: Ladda ned dina ursprungliga modeller, garn och diagram.
2. Flytta: skapa en ny Azure Digitals-instans i en ny region.
3. Flytta: Fyll i den nya Azure Digital-instansen.
    - Ladda upp ursprungliga modeller, dubbla och grafer.
    - Återskapa slut punkter och vägar.
    - Länka anslutna resurser på nytt.
4. Rensa käll resurser: ta bort ursprunglig instans.

## <a name="prerequisites"></a>Förutsättningar

Innan du försöker återskapa din Azure Digital-instansen är det en bra idé att gå igenom komponenterna i den ursprungliga instansen och få en tydlig uppfattning om alla delar som måste återskapas.

Här är några frågor som du kanske vill tänka på:
* Vilka **modeller** överförs till min instans? Hur många finns det?
* Vilka är de **dubbla** i min instans? Hur många finns det?
* Vad är **diagrammets** allmänna form i min instans? Hur många relationer finns det?
* Vilka **slut punkter** finns i min instans?
* Vilka **vägar** finns i min instans? Har de filter?
* Var ansluter min instans **till andra Azure-tjänster** ? Några vanliga integrerings punkter är...
    - Event Grid, Händelsehubben eller Service Bus
    - Azure Functions
    - Logic Apps
    - Time Series Insights
    - Azure Maps
    - Enhets etablerings tjänst (DPS)
* Vilka andra **personliga appar eller företags program** har jag som ansluter till min instans?

Du kan samla in den här informationen med hjälp av [Azure Portal](https://portal.azure.com), [Azure Digitals dubbla API: er och SDK: er](how-to-use-apis-sdks.md), [Azure Digitals CLI-kommandon](how-to-use-cli.md)eller [Azure Digitals-Utforskaren (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) .

## <a name="prepare"></a>Förbereda

I det här avsnittet ska du förbereda för att återskapa instansen genom att **Ladda ned dina ursprungliga modeller, dubbla och grafer** från den ursprungliga instansen. Den här artikeln gör detta med hjälp av exempel på [Azure Digitals ADT-Utforskaren](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) .

>[!NOTE]
>Du kanske redan har filer som innehåller modellerna och/eller grafen i din instans. I så fall behöver du inte hämta allting igen, bara de delar som du saknar eller saker som kan ha ändrats sedan du ursprungligen laddade upp dessa filer (till exempel dubbla objekt som kan ha uppdaterats med nya data).

### <a name="limitations-of-adt-explorer"></a>Begränsningar för ADT Explorer

[ADT-Utforskaren (Azure Digitals)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) är ett exempel på en app för klient program som har stöd för visuell representation av grafen och ger visuell interaktion med din instans. Den här artikeln visar hur du kan använda den för att ladda ned och senare Ladda upp igen, modeller, dubbla och grafer.

Observera dock att det här är ett **exempel** och inte ett komplett verktyg. Den har inte testats och har inte skapats för att hantera diagram av stor storlek. Tänk därför på följande exempel begränsningar som är färdiga:
* Exemplet har för närvarande endast testats i diagram storlekar upp till 1000 noder och 2000 relationer
* Exemplet stöder inte återförsök vid tillfälliga haverier
* Exemplet meddelar inte nödvändigt vis användaren om data som laddas upp är ofullständiga
* Exemplet hanterar inte fel som beror på mycket stora diagram som överskrider tillgängliga resurser som minne

Om exemplet inte kan hantera diagrammets storlek, kan du exportera och importera grafen med andra verktyg för Azure Digitals sammanflätade utvecklare:
* [Azure Digitals flätade CLI-kommandon](how-to-use-cli.md)
* [Azure Digitals dubbla API: er och SDK: er](how-to-use-apis-sdks.md)

### <a name="set-up-adt-explorer-application"></a>Konfigurera ADT Explorer-program

Om du vill fortsätta med ADT Explorer laddar du först ned exempel koden och konfigurerar den så att den körs på din dator. 

Navigera till exemplet här: [Azure Digitals flätar (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Tryck på *hämtnings zip* -knappen för att ladda ned en *. ZIP* -fil för den här exempel koden till din dator som _**Azure_Digital_Twins__ADT__explorer.zip**_ . Zippa upp filen.

Konfigurera och konfigurera sedan behörigheter för ADT Explorer. Det gör du genom att följa anvisningarna i avsnittet [*Konfigurera Azure Digital-och ADT-Utforskare*](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-adt-explorer) i snabb starten för Azure Digital. Det här avsnittet vägleder dig genom följande steg:
1. Konfigurera en digital Azure-instans (du kan hoppa över den här delen eftersom du redan har en instans)
2. Konfigurera en **Azure AD App-registrering** för att ge åtkomst till din instans
3. Konfigurera behörigheter för ADT Explorer att köras på din dator
4. Kör ADT Explorer och konfigurera den för att ansluta till din instans. Du kommer att använda **värd namnet** för den ursprungliga Azure Digital-instansen som du flyttar och **klient-ID och klient** **-ID** från appens registrering.

Nu ska du ha ADT Explorer-exempelprogrammet som körs i en webbläsare på din dator. Exemplet bör vara anslutet till den ursprungliga Azure Digital-instansen.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Webbläsarfönster som visar en app som körs på localhost: 3000. Appen kallas ADT Explorer och innehåller rutor för en Query Explorer, modell läge, diagramvy och egenskaps Utforskaren. Det finns inga skärm data ännu." lightbox="media/how-to-move-regions/explorer-blank.png":::

För att verifiera anslutningen kan du trycka på knappen *Kör fråga* för att köra standard frågan som visar alla dubbla och relationer i grafen i rutan *diagram Utforskaren* .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Webbläsarfönster som visar en app som körs på localhost: 3000. Appen kallas ADT Explorer och innehåller rutor för en Query Explorer, modell läge, diagramvy och egenskaps Utforskaren. Det finns inga skärm data ännu." lightbox="media/how-to-move-regions/run-query.png":::

Du kan lämna ADT Explorer igång eftersom du kommer att använda den igen senare i den här artikeln för att ladda upp objekten på nytt till din nya instans i mål regionen.

### <a name="download-models-twins-and-graph"></a>Ladda ned modeller, dubbla och diagram

Sedan laddar du ned modeller, dubbla och grafer i din lösning till din dator.

Om du vill ladda ned alla dessa samtidigt måste du först se till att hela grafen visas i *diagram* rutan (du kan göra detta genom att köra standard frågan i `SELECT * FROM digitaltwins` rutan fråga i *Utforskaren* ).
 
Tryck sedan på *Exportera diagram* -ikonen i rutan *diagram* .

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="Webbläsarfönster som visar en app som körs på localhost: 3000. Appen kallas ADT Explorer och innehåller rutor för en Query Explorer, modell läge, diagramvy och egenskaps Utforskaren. Det finns inga skärm data ännu." lightbox="media/how-to-move-regions/export-graph.png":::

När du gör det aktive ras en *nedladdnings* länk i *diagramvyn* . Välj den för att ladda ned en JSON-baserad representation av frågeresultatet, inklusive modeller, dubbla och relationer. Detta bör ladda ned en *. JSON* -fil till din dator.

>[!NOTE]
>Om den hämtade filen verkar ha ett annat fil namns tillägg kan du försöka redigera tillägget direkt och ändra det till *. JSON* .

## <a name="move"></a>Flytta

Sedan slutför du "flytta"-instansen genom att skapa en ny instans i mål regionen och fylla den med data och komponenter från den ursprungliga instansen.

### <a name="create-a-new-instance"></a>Skapa en ny instans

Börja **med att skapa en ny instans av Azure Digitals dubbla i mål regionen** . Det gör du genom att följa stegen i [*anvisningar: Konfigurera en instans och autentisering*](how-to-set-up-instance-portal.md), och hålla dessa pekare i åtanke:
* Du kan behålla samma namn för den nya instansen **om** den finns i en annan resurs grupp. Om du behöver använda samma resurs grupp som innehåller den ursprungliga instansen måste den nya instansen ha sitt eget distinkta namn.
* Ange den nya mål regionen när du uppmanas att ange en plats.

När detta är klart behöver du **värd namnet** för den nya instansen för att fortsätta att konfigurera den med dina data. Om du inte antecknade detta under installationen kan du följa [dessa instruktioner](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) för att hämta det nu från Azure Portal.

### <a name="repopulate-old-instance"></a>Uppdatera gammal instans

Härnäst ska du ställa in den nya instansen så att den är en kopia av originalet.

#### <a name="upload-original-models-twins-and-graph-using-adt-explorer"></a>Ladda upp original modeller, dubbla och Graf med ADT Explorer

I det här avsnittet kan du överföra modeller, dubbla och grafer till den nya instansen. Om du inte har några modeller, dubbla eller grafer i din ursprungliga instans eller om du inte vill flytta dem till den nya instansen kan du gå vidare till [Nästa avsnitt](#recreate-endpoints-and-routes).

Annars går du tillbaka till webbläsarfönstret som kör **ADT Explorer** och följer stegen nedan.

##### <a name="connect-to-the-new-instance"></a>Anslut till den nya instansen

ADT Explorer är för närvarande ansluten till den ursprungliga Azure Digital-instansen. Växla anslutningen så att den pekar på den nya instansen genom att trycka på knappen *Logga in* överst i fönstret. 

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Webbläsarfönster som visar en app som körs på localhost: 3000. Appen kallas ADT Explorer och innehåller rutor för en Query Explorer, modell läge, diagramvy och egenskaps Utforskaren. Det finns inga skärm data ännu." lightbox="media/how-to-move-regions/sign-in.png":::

Du kan återanvända samma app-registrering, så du behöver bara ersätta *ADT-URL: en* för att avspegla din nya instans. Ändra det här värdet till det läser *https://{ny instans-värdnamn}* .

Tryck på *Anslut* . Du kan uppmanas att logga in igen med dina Azure-autentiseringsuppgifter och/eller bevilja detta program tillstånd för din instans.

##### <a name="upload-models-twins-and-graph"></a>Ladda upp modeller, garn och diagram

Ladda sedan upp lösnings komponenterna som du laddade ned tidigare till din nya instans.

Om du vill överföra **modeller, dubbla och grafer** , trycker du på ikonen *Importera diagram* i rutan *diagram* . Med det här alternativet överförs alla tre komponenterna på samma gång (även modeller som inte används i diagrammet).

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="Webbläsarfönster som visar en app som körs på localhost: 3000. Appen kallas ADT Explorer och innehåller rutor för en Query Explorer, modell läge, diagramvy och egenskaps Utforskaren. Det finns inga skärm data ännu." lightbox="media/how-to-move-regions/import-graph.png":::

I rutan fil väljare navigerar du till den hämtade grafen. Välj filen Graph *. JSON* och tryck på *Öppna* .

Efter några sekunder öppnar ADT Explorer en *importerad* vy som visar en för hands version av grafen som ska läsas in.

Tryck på ikonen *Spara* i det övre högra hörnet i *diagramvyn* för att bekräfta att diagrammet laddas upp:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Webbläsarfönster som visar en app som körs på localhost: 3000. Appen kallas ADT Explorer och innehåller rutor för en Query Explorer, modell läge, diagramvy och egenskaps Utforskaren. Det finns inga skärm data ännu." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT Explorer laddar nu upp dina modeller och diagram (inklusive de dubbla och relationerna) till den nya Azure Digital-instansen. Du bör se ett meddelande som visar hur många modeller, dubbla och relationer som har överförts:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Webbläsarfönster som visar en app som körs på localhost: 3000. Appen kallas ADT Explorer och innehåller rutor för en Query Explorer, modell läge, diagramvy och egenskaps Utforskaren. Det finns inga skärm data ännu." lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Om du vill kontrol lera att allt har överförts trycker du på knappen *Kör fråga* i *Graph Explorer* -rutan för att köra standard frågan som visar alla dubbla och relationer i grafen. Detta kommer också att uppdatera listan över modeller i *vyn modell* .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Webbläsarfönster som visar en app som körs på localhost: 3000. Appen kallas ADT Explorer och innehåller rutor för en Query Explorer, modell läge, diagramvy och egenskaps Utforskaren. Det finns inga skärm data ännu." lightbox="media/how-to-move-regions/run-query.png":::

Du bör se grafen med alla dess sammanflätade och relationer i rutan *Graph Explorer* . Du bör också se vilka modeller som visas i rutan *modell* .

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Webbläsarfönster som visar en app som körs på localhost: 3000. Appen kallas ADT Explorer och innehåller rutor för en Query Explorer, modell läge, diagramvy och egenskaps Utforskaren. Det finns inga skärm data ännu." lightbox="media/how-to-move-regions/post-upload.png":::

Detta bekräftar att dina modeller, dubbla och grafer har laddats upp igen till den nya instansen i mål regionen.

#### <a name="recreate-endpoints-and-routes"></a>Återskapa slut punkter och vägar

Om du har **slut punkter och/eller vägar** i din ursprungliga instans måste du återskapa dem i den nya instansen. Om du inte har några slut punkter eller vägar i den ursprungliga instansen eller om du inte vill flytta dem till den nya instansen kan du gå vidare till [Nästa avsnitt](#re-link-connected-resources).

Fortsätt annars genom att följa stegen i [*instruktion: hantera slut punkter och vägar*](how-to-manage-routes-portal.md) med hjälp av den nya instansen och hålla dessa pekare i åtanke: 
* Du **behöver inte** återskapa Event Grid, händelsehubben eller Service Bus resurs som du använder för slut punkten ( *nödvändiga* avsnitt i slut punkts anvisningarna). Du behöver bara återskapa slut punkten på Azure Digitals-instansen.
* Du kan återanvända slut punkts-och Dirigerings **namn** eftersom de är begränsade till en annan instans.
* Kom ihåg att lägga till eventuella nödvändiga **filter** till de vägar som du skapar.

#### <a name="re-link-connected-resources"></a>Länka anslutna resurser på nytt

Om du har andra appar eller Azure-resurser som är anslutna till den ursprungliga Azure Digital-instansen måste du redigera anslutningen så att de når den nya instansen i stället. Detta kan omfatta andra Azure-tjänster eller personliga eller företags-appar som du har konfigurerat för att arbeta med Azure Digitals dubbla.

Om du inte har några andra resurser som är anslutna till den ursprungliga instansen eller om du inte vill flytta dem till den nya instansen kan du gå vidare till [Nästa avsnitt](#verify).

Annars kan du gå vidare till de anslutna resurserna i ditt scenario. Du behöver inte ta bort och återskapa alla anslutna resurser. i stället behöver du bara redigera de punkter där de ansluter till en digital Azure-instans via sitt **värdnamn** och uppdatera den för att använda värd namnet för den nya instansen i stället för den ursprungliga.

De exakta resurser som du behöver redigera beror på ditt scenario, men här är några vanliga integrerings punkter:
* Azure Functions. Om du har en Azure-funktion vars kod innehåller värd namnet för den ursprungliga instansen, bör du uppdatera värdet till den nya instansens värdnamn och publicera funktionen på nytt.
* Event Grid, Event Hubs eller Service Bus
* Logic Apps
* Time Series Insights
* Azure Maps
* Enhets etablerings tjänst (DPS)
* Personliga appar eller företags program utanför Azure, t. ex. **klient programmet** som skapades i [*Självstudier: kod a klient program*](tutorial-code.md), som ansluter till instansen och anropar Azure Digitals dubbla API: er

När du har slutfört det här steget ska den nya instansen i mål regionen vara en kopia av den ursprungliga instansen.

## <a name="verify"></a>Verifiera

Du kan använda följande verktyg för att kontrol lera att den nya instansen har kon figurer ATS korrekt:
* [**Azure Portal**](https://portal.azure.com) (passar för att verifiera att den nya instansen finns och är i rätt mål region, även för att verifiera slut punkter och vägar, och anslutningar till andra Azure-tjänster)
* [Azure Digitals flätat **CLI-kommandon**](how-to-use-cli.md) (passar för att verifiera att din nya instans finns och är i rätt mål region, kan också användas för att verifiera instans data)
* [**ADT Explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) (passar för att verifiera instans data som modeller, dubbla och grafer)
* [Azure Digitals dubbla API: er och SDK: er](how-to-use-apis-sdks.md) (passar för att verifiera instans data, t. ex. modeller, dubbla och grafer, också lämpligt för att verifiera slut punkter och vägar)

Du kan också prova att köra anpassade appar eller slut punkt till slut punkt som du har kört med din ursprungliga instans, för att hjälpa dig att kontrol lera att de arbetar med den nya instansen korrekt.

## <a name="clean-up-source-resources"></a>Rensa käll resurser

Nu när den nya instansen har kon figurer ATS i mål regionen med en kopia av den ursprungliga instansens data och anslutningar kan du **ta bort den ursprungliga instansen** .

Du kan göra detta i [Azure Portal](https://portal.azure.com), med [CLI](how-to-use-cli.md)eller med [kontroll Plans-API: erna](how-to-use-apis-sdks.md#overview-control-plane-apis).

Om du vill ta bort instansen med Azure Portal [öppnar du portalen](https://portal.azure.com) i ett webbläsarfönster och navigerar till den ursprungliga Azure Digital-instansen genom att söka efter dess namn i portalens Sök fält.

Tryck på knappen *ta bort* och följ anvisningarna för att slutföra borttagningen.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Webbläsarfönster som visar en app som körs på localhost: 3000. Appen kallas ADT Explorer och innehåller rutor för en Query Explorer, modell läge, diagramvy och egenskaps Utforskaren. Det finns inga skärm data ännu.":::