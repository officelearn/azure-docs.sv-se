---
title: Snabb start – utforska ett exempel scenario
titleSuffix: Azure Digital Twins
description: Snabb start – Använd ADT Explorer-exemplet för att visualisera och utforska ett i förväg skapat scenario.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 466129e8435ef694821b078592a100a111a43f3a
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242287"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-adt-explorer"></a>Snabb start – utforska ett exempel på Azure Digitals dubbla scenarier med ADT Explorer

Med Azure Digitals dubbla, kan du skapa och interagera med Live-modeller i dina verkliga miljöer. Det gör du genom att utforma enskilda element som **digitala** delar och sedan ansluta dem till ett kunskaps **diagram** som kan svara på direktsända händelser och frågas efter information.

I den här snabb starten ska du utforska ett förbyggt Azure Digital-diagram med hjälp av ett exempel program som kallas [**Azure Digitals dubblare (ADT) Explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Med ADT Explorer kan du ladda upp en digital representation av en miljö, Visa visuella bilder av de dubbla och grafer som skapas för att representera miljön i Azure Digitals, och utföra andra hanterings aktiviteter via en webbläsarbaserad, visuell upplevelse.

Snabb starten innehåller följande viktiga steg:

1. Konfigurera en digital Azure-instans och ADT Explorer
1. Ladda upp färdiga modeller och diagram data för att skapa exempel scenariot
1. Utforska scenario diagrammet som skapas
1. Gör ändringar i grafen

Det exempel diagram som du kommer att arbeta med representerar en byggnad med två golv och två rum. Diagrammet kommer att se ut så här:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna.":::

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration för att slutföra den här snabb starten. Om du inte redan har en, kan du **[skapa ett kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** nu.

Du behöver också **Node.js** på din dator. Du kan hämta den senaste versionen på den här länken: [Node.js](https://nodejs.org/).

Slutligen måste du hämta exemplet som ska användas under snabb starten: exempel programmet för **ADT Explorer** . Det här exemplet innehåller den app som du använder i snabb starten för att läsa in och utforska ett Azure Digital-scenario, samt exempel på scenario-filer. Hämta exemplet genom att navigera här: [Azure Digitals flätar (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Tryck på *hämtnings zip* -knappen för att ladda ned en *. ZIP* -fil för den här exempel koden på din dator. Då hämtas en. ZIP-mapp på din dator som _**Azure_Digital_Twins__ADT__explorer.zip**_. Zippa upp mappen och extrahera filerna.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Konfigurera Azure Digital-och ADT Explorer

Det första steget när du arbetar med Azure Digitals dubbla är att **Konfigurera en digital Azure-instans**. När du har skapat en instans av tjänsten och **konfigurerat dina autentiseringsuppgifter** för att AUTENTISERA med ADT Explorer kan du **ansluta till instansen i ADT Explorer** och fylla den med exempel data senare i snabb starten.

Resten av det här avsnittet vägleder dig genom de här stegen.

### <a name="set-up-azure-digital-twins-instance"></a>Konfigurera Azure Digitals dubbla instanser

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Konfigurera lokala autentiseringsuppgifter för Azure

ADT Explorer-programmet använder [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (del av `Azure.Identity` biblioteket) för att autentisera användare med Azure Digitals-instansen när du kör den på den lokala datorn. Mer information om olika sätt som en klient-app kan autentisera med Azure Digitals dubbla finns i [*How-to: Write app authentication code*](how-to-authenticate-client.md).

Med den här typen av autentisering söker ADT Explorer efter autentiseringsuppgifter i din lokala miljö, till exempel en Azure-inloggning i en lokal [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) eller Visual Studio/Visual Studio Code. Det innebär att du bör **Logga in på Azure lokalt** via någon av dessa metoder för att ställa in AUTENTISERINGSUPPGIFTER för ADT Explorer-appen.

Om du redan är inloggad på Azure på något av följande sätt kan du gå vidare till [Nästa avsnitt](#run-and-configure-adt-explorer).

Annars kan du installera den lokala **Azure CLI** med följande steg:
1. Följ processen på [**den här installations länken**](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) för att slutföra installationen som matchar ditt operativ system.
2. Öppna ett konsol fönster på din dator.
3. Kör `az login` och följ autentiserings-prompterna för att logga in på ditt Azure-konto.

När du har gjort detta ska ADT Explorer hämta dina Azure-autentiseringsuppgifter automatiskt när du kör det i nästa avsnitt.

Du kan stänga fönstret för verifierings konsolen om du vill, eller låta det vara öppet att använda i nästa steg.

### <a name="run-and-configure-adt-explorer"></a>Köra och konfigurera ADT Explorer

Kör sedan ADT Explorer-programmet och konfigurera det för din Azure Digital-instansen.

Navigera till mappen hämtade och zippade _**Azure_Digital_Twins__ADT__explorer**_ . Öppna ett konsol fönster till mappens plats *Azure_Digital_Twins__ADT__explorer/client/src*.

Kör `npm install` för att ladda ned alla nödvändiga beroenden.

Starta sedan appen genom att köra `npm run start` .

Efter några sekunder öppnas ett webbläsarfönster och appen visas i webbläsaren.

:::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Webbläsarfönster som visar en app som körs på localhost: 3000. Appen kallas ADT Explorer och innehåller rutor för en Query Explorer, modell läge, diagramvy och egenskaps Utforskaren. Det finns inga skärm data ännu." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

Tryck på knappen *Logga in* överst i fönstret (visas i bilden nedan) för att konfigurera ADT Explorer att fungera med den instans som du har konfigurerat. 

:::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="ADT Explorer markerar ikonen Logga in längst upp i fönstret. Ikonen visar en enkel Silhouette av en person som har en Silhouette av en nyckel." lightbox="media/quickstart-adt-explorer/sign-in.png":::

Ange den URL som du har samlat in tidigare i avsnittet [krav](#prerequisites) , i formatet *https://{instans värd namn}* för *Azure Digital* .

>[!NOTE]
> Du kan gå tillbaka och redigera informationen när som helst genom att välja samma ikon för att hämta inloggnings rutan igen. Den behåller de värden som du har skickat.

> [!TIP]
> Om ett `SignalRService.subscribe` fel meddelande visas när du ansluter, se till att din Azure Digital-URL börjar med *https://*.

Om du ser en *behörighet som begärs* i popup-fönstret från Microsoft, bevilja du medgivande för det här programmet och accepterar att fortsätta.

## <a name="add-the-sample-data"></a>Lägg till exempel data

Därefter ska du importera exempel scenariot och grafen till ADT Explorer. Exempel scenariot finns också i mappen **Azure_Digital_Twins__ADT__explorer** som du laddade ned tidigare.

### <a name="models"></a>Modeller

Det första steget i en Azure digital-lösning med dubbla lösningar är att definiera ord listan för din miljö. Detta görs genom att skapa anpassade [**modeller**](concepts-models.md)som beskriver vilka typer av entiteter som finns i din miljö. 

Varje modell skrivs i ett JSON-LD-liknande språk som kallas **digital DTDL (Digital Definition Language)** och beskriver en enskild typ av entitet med avseende på dess *Egenskaper* , *telemetri* , *relationer* och *komponenter*. Senare kommer du att använda dessa modeller som grund för digitala dubbla, som representerar vissa instanser av dessa typer.

När du skapar en modell kommer du vanligt vis att utföra tre steg:
1. Skriv modell definitionen (i snabb starten som redan utförts som en del av exempel lösningen)
2. Verifiera den för att se till att syntaxen är korrekt (i snabb starten som redan har utförts som en del av exempel lösningen)
3. Ladda upp den till din Azure Digital-instansen
 
I den här snabb starten har modell filen redan skrivits och godkänts för dig och ingår i lösningen som du laddade ned. I det här avsnittet ska du ladda upp två fördefinierade modeller till din instans för att definiera dessa komponenter i en byggnads miljö:
* Floor
* Rummet

#### <a name="upload-models"></a>Ladda upp modeller

I rutan *modell* visas trycker du på ikonen *Ladda upp en modell* .

:::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="I rutan modell visas den mittersta ikonen. Den visar en pil som pekar på ett moln." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. I rutan fil väljare som visas navigerar du till mappen *Azure_Digital_Twins__ADT__explorer/client/examples* i den hämtade lagrings platsen.
2. Välj *Room.jspå* och *Floor.jspå* och tryck på OK. (Du kan ladda upp ytterligare modeller om du vill, men de används inte i den här snabb starten.)
3. Följ dialog rutan för att be dig att logga in på ditt Azure-konto.

>[!NOTE]
>Om du ser följande fel meddelande: ett popup-meddelande visas: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="fel när modeller hämtades: ClientAuthError: det gick inte att öppna popup-fönstret. Detta kan inträffa om du använder IE eller om popup-fönster blockeras i webbläsaren. &quot;med knappen Stäng längst ned" border="false"::: 
> Försök inaktivera blockering av popup-fönster eller använda en annan webbläsare.

ADT Explorer kommer nu att ladda upp de här projektfilerna till din Azure Digital-instansen. De bör visas i rutan *modell* och visar sina egna namn och fullständiga modell-ID: n. Du kan klicka på *Visa modell* informations bubblor för att se DTDL-koden bakom dem.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="En vy av rutan modell visning med två modell definitioner som anges inuti, våning (dtmi: exempel: golv; 1) och rum (dtmi: exempel: Room; 1). Ikonen Visa modell som visar en bokstav i en cirkel är markerad för varje modell." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Dubbla och det dubbla diagrammet

Nu när vissa modeller har laddats upp till din Azure Digital-instansen, kan du lägga till [**digitala dubbla**](concepts-twins-graph.md) , som följer modell definitionerna. 

Digitala delar representerar faktiska entiteter i din affärs miljö: saker som sensorer i en grupp, lampor i en bil eller – i den här snabb starten – rum på en byggnad av golv. Du kan skapa många multiplar av alla typer av modeller (t. ex. flera rum som alla använder *rums* modellen) och koppla dem till relationer i ett **dubbel diagram** som representerar hela miljön.

I det här avsnittet kommer du att överföra färdiga, dubbla, som är anslutna till ett diagram som skapats i förväg. Grafen innehåller två golv och två rum, anslutna i följande layout:
* *Floor0*
    - innehåller *Room0*
* *Floor1*
    - innehåller *Room1*

#### <a name="import-the-graph"></a>Importera grafen

Tryck på ikonen *Importera diagram* i rutan *diagram visning* .

:::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="I rutan diagram visas en ikon. Den visar en pil som pekar på ett moln." lightbox="media/quickstart-adt-explorer/import-graph.png":::

I rutan fil väljare navigerar du till mappen *Azure_Digital_Twins__ADT__explorer/client/examples* och väljer kalkyl blads filen _**buildingScenario.xlsx**_ . Den här filen innehåller en beskrivning av exempel diagrammet. Tryck på OK.

Efter några sekunder öppnar ADT Explorer en *importerad* vy som visar en för hands version av grafen som ska läsas in.

Tryck på ikonen *Spara* i det övre högra hörnet i *diagramvyn* för att bekräfta att diagrammet laddas upp:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Markera ikonen Spara i förhands gransknings fönstret för diagrammet" lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT Explorer kommer nu att använda den överförda filen för att skapa de begärda delningarna och relationerna mellan dem. En dialog ruta visas som visar att den är färdig. Tryck på *Stäng*.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Dialog rutan visar att grafen har importer klarat. Den läser importen lyckades. 4 dubbla importer. 2 relationer har importer ATS. &quot;" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Grafen har nu laddats upp till ADT Explorer. Om du vill se diagrammet trycker du på knappen *Kör fråga* i rutan *diagram Utforskaren* , längst upp i fönstret ADT Explorer. 

:::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="En knapp som läser kör fråga nära överst i fönstret är markerad" lightbox="media/quickstart-adt-explorer/run-query.png":::

Det här kör standard frågan för att välja och Visa alla digitala dubbla. ADT Explorer hämtar alla dubbla och relationer från tjänsten och ritar diagrammet som definieras av dem i rutan *diagram* .

## <a name="explore-the-graph"></a>Utforska grafen

Nu kan du se det överförda diagrammet i exempel scenariot:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Vy av rutan diagramvy med en dubbel graf inuti. En cirkel med namnet &quot;floor1&quot; är kopplad till en cirkel med etiketten &quot;room1&quot;. en cirkel med etiketten &quot;floor0&quot; är kopplad till en cirkel med etiketten &quot;room0&quot;.":::

Cirklarna (graf "Nodes") representerar digitala dubbla och linjerna representerar relationer. Du kommer att se att *Floor0* -den dubbla innehåller *Room0* och att *Floor1* -filen innehåller *Room1*.

Om du använder en mus kan du klicka och dra delar av diagrammet för att flytta runt dem.

### <a name="view-twin-properties"></a>Visa dubbla egenskaper 

Du kan välja en fläta om du vill se en lista över dess egenskaper och deras värden i rutan *egenskaps Utforskaren* . 

Här följer egenskaperna för *Room0* :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Markera i rutan &quot;egenskaps Utforskaren&quot; som visar egenskaper för Room0, inklusive (bland annat) ett $dtId fält för &quot;Room0&quot;, ett temperatur fält på 70 och ett fuktighets fält på 30." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Observera att *Room0* har en temperatur på **70**.

Här följer egenskaperna för *Room1* :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Markera i rutan &quot;egenskaps Utforskaren&quot; som visar egenskaper för Room1, inklusive (bland annat) ett $dtId fält för &quot;Room1&quot;, ett temperatur fält på 80 och ett fuktighets fält på 60." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Observera att *Room1* har en temperatur på **80**.

### <a name="query-the-graph"></a>Fråga i grafen

En huvud funktion i Azure Digitals flätas är möjligheten att [fråga](concepts-query-language.md) ditt dubbla diagram enkelt och effektivt att besvara frågor om din miljö. 

Ett sätt att fråga de dubblarna i diagrammet är av deras *Egenskaper*. Frågor som baseras på egenskaper kan hjälpa dig att besvara en rad olika frågor, inklusive att hitta avvikande Mät värden i din miljö som kan behöva åtgärdas.

I det här avsnittet ska du köra en fråga för att besvara följande fråga: _**Vad är alla skärnings punkter i min miljö med en temperatur på 75?**_

Om du vill se svaret kör du följande fråga i rutan *query Explorer* :

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Kom ihåg att visa de dubbla egenskaperna tidigare att *Room0* har en temperatur på **70** och *Room1* har en temperatur på **80**. Resultatet blir att endast _**Room1**_ visas i resultatet här.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Resultat för egenskaps fråga, som endast visar Room1" lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Andra jämförelse operatorer ( *<* ,, *>* *=* eller *! =* ) stöds också i frågan ovan. Du kan prova att koppla dessa, olika värden eller andra dubbla egenskaper till frågan för att testa dina egna frågor.

## <a name="edit-data-in-the-graph"></a>Redigera data i grafen

Du kan använda ADT Explorer för att redigera egenskaperna för de dubbla som representeras i grafen. I det här avsnittet kommer vi att **_öka temperaturen på_ Room0 till 76**.

Det gör du genom att välja *Room0* , så visas dess egenskaps lista i rutan *egenskaps Utforskaren* .

Egenskaperna i den här listan kan redige ras. Välj temperatur svärdet **70** för att aktivera ett nytt värde. Ange **76** och tryck på ikonen *Spara* för att uppdatera temperaturen till **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Rutan &quot;egenskaps Utforskaren&quot; som visar egenskaper för Room0. Temperatur svärdet är en redigerings bar ruta som visar 76 och det finns en markering runt ikonen Spara." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

När du har sparat filen visas en *korrigerings informations* fönster som visar korrigerings koden som användes bakom kulisserna med Azure Digitals dubbla [API: er](how-to-use-apis-sdks.md) för att göra uppdateringen. Tryck på *Stäng*.

### <a name="query-to-see-the-result"></a>Fråga för att se resultatet

Kontrol lera att grafen har registrerat din uppdatering på *Room0* -temperatur genom att köra frågan igen från tidigare för att **Hämta alla dubbla i miljön med en temperatur över 75** :

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Nu när temperaturen för *Room0* har ändrats från **70** till **76** , ska båda delarna visas i resultatet.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Resultat för egenskaps fråga, som visar både Room0 och Room1" lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Granska och sätta-information

I den här snabb starten skapade du en Azure Digital-instansen, anslöt den till ADT Explorer och fyllde den med ett exempel scenario. 

Du utforskar sedan grafen, efter...
1. Använda en fråga för att besvara en fråga om scenariot.
2. Redigera en egenskap på en digital, dubbel.
3. Kör frågan igen för att se hur svaret ändrades till följd av din uppdatering.

Syftet med den här övningen är att demonstrera hur du kan använda Azure Digitals grafer för att besvara frågor om din miljö, även om miljön fortsätter att ändras. 

I den här snabb starten gjorde du temperatur uppdateringen manuellt, men det är vanligt i Azure Digital-enheter för att ansluta digitala enheter till riktiga IoT-enheter så att de får uppdateringar automatiskt, baserat på telemetridata. På så sätt kan du skapa en Live-graf som alltid återspeglar den faktiska situationen i din miljö och använda frågor för att få information om vad som händer i din miljö i real tid.

## <a name="clean-up-resources"></a>Rensa resurser

För att packa upp arbetet för den här snabb starten ska du först avsluta den aktiva konsolen. Då stängs anslutningen till ADT Explorer-appen i webbläsaren och du kommer inte längre att kunna visa real tids data i webbläsaren. Du kan stänga fliken webbläsare.

Om du planerar att fortsätta med självstudierna för Azure Digitals dubbla, kan den instans som används i den här snabb starten återanvändas för dessa artiklar och du behöver inte ta bort den.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Slutligen tar du bort exempel mappen Project som du laddade ned till din lokala dator ( _**Azure_Digital_Twins__ADT__explorer**_ ). Du kanske måste ta bort både zippade och zippade versioner.

## <a name="next-steps"></a>Nästa steg 

Fortsätt sedan till Azure Digitals-självstudierna för att bygga upp dina egna Azure Digital-dubblare scenario-och interaktions verktyg.

> [!div class="nextstepaction"]
> [*Självstudie: koda en klient app*](tutorial-code.md)