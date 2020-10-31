---
title: Snabb start – utforska ett exempel scenario
titleSuffix: Azure Digital Twins
description: Snabb start – Använd ADT Explorer-exemplet för att visualisera och utforska ett i förväg skapat scenario.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 9d3c9d03c4297af0b9155c2d528e27221b42bc9e
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124847"
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

Slutligen måste du hämta exemplet som ska användas under snabb starten: exempel programmet för **ADT Explorer** . Det här exemplet innehåller den app som du använder i snabb starten för att läsa in och utforska ett Azure Digital-scenario, samt exempel på scenario-filer. Hämta exemplet genom att navigera här: [Azure Digitals flätar (ADT) Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Tryck på *hämtnings zip* -knappen för att ladda ned en *. ZIP* -fil för den här exempel koden på din dator. Då hämtas en. ZIP-mapp på din dator som _**Azure_Digital_Twins__ADT__explorer.zip**_ . Zippa upp mappen och extrahera filerna.

## <a name="set-up-azure-digital-twins-and-adt-explorer"></a>Konfigurera Azure Digital-och ADT Explorer

Det första steget när du arbetar med Azure Digitals dubbla är att konfigurera en **digital Azure-instans** . När du har skapat en instans av tjänsten kan du fylla den med exempel data senare i snabb starten.

Du kan också konfigurera behörigheter för ADT Explorer att köras på din dator och få åtkomst till din Azure Digital-instansen, till exempel konfigurera en Azure Active Directory (Azure AD) för IT- **registrering** . Därefter kan du använda exempel appen för att utforska din instans och dess data.

### <a name="set-up-azure-digital-twins-instance-and-app-registration"></a>Konfigurera Digital Azure Digitals-instans och registrera appar

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [digital-twins-prereq-registration.md](../../includes/digital-twins-prereq-registration.md)]

### <a name="set-adt-explorer-permissions"></a>Ange behörigheter för ADT Explorer

Förbered sedan Azure Digital-instansen som du skapade för att arbeta med ADT Explorer, som är ett lokalt värdbaserade webb program. Besök sidan [Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) i Azure Portal och välj namnet på din **app-registrering** som du skapade i föregående avsnitt i listan.

Välj *autentisering* på registrerings menyn och tryck på *+ Lägg till en plattform* .

:::image type="content" source="media/quickstart-adt-explorer/authentication-pre.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna." lightbox="media/quickstart-adt-explorer/authentication-pre.png":::

På sidan *Konfigurera plattformar* som följer väljer du *webb* .
Fyll i konfigurations informationen på följande sätt:
* **Omdirigera URI: er** : Lägg till en omdirigerings-URI för *http://localhost:3000* .
* **Implicit beviljande** : Markera kryss *rutan för åtkomsttoken* .

Tryck på *Konfigurera* för att avsluta.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/authentication-configure-web.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Nu har du en webb konfiguration som är konfigurerad som ADT Explorer kommer att använda. Fliken autentisering i Azure Portal bör avspegla detta. När du har verifierat avsnitten nedan trycker du på *Spara* .

:::image type="content" source="media/quickstart-adt-explorer/authentication-post.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna.":::

### <a name="run-and-configure-adt-explorer"></a>Köra och konfigurera ADT Explorer

Kör sedan ADT Explorer-programmet och konfigurera det för din Azure Digital-instansen.

Navigera till mappen hämtade och zippade _**Azure_Digital_Twins__ADT__explorer**_ . Öppna en kommando tolk på mappens plats *Azure_Digital_Twins__ADT__explorer/client/src* .

Kör `npm install` för att ladda ned alla nödvändiga beroenden.

Starta sedan appen genom att köra `npm run start` .

Efter några sekunder öppnas ett webbläsarfönster och appen visas i webbläsaren.

:::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

Tryck på knappen *Logga in* överst i fönstret (visas i bilden nedan) för att konfigurera ADT Explorer att fungera med den instans som du har konfigurerat. 

:::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna." lightbox="media/quickstart-adt-explorer/sign-in.png":::

Ange viktig information som du samlade in tidigare i avsnittet [krav](#prerequisites) :
* Program-ID (klient)
* Katalog-ID (klient)
* Azure Digitals sammanflätade instans-URL, i formatet *https://{instans värd namn}*

>[!NOTE]
> Du kan gå tillbaka och redigera informationen när som helst genom att välja samma ikon för att hämta inloggnings rutan igen. Den behåller de värden som du har skickat.

> [!TIP]
> Om ett `SignalRService.subscribe` fel meddelande visas när du ansluter, se till att din Azure Digital-URL börjar med *https://* .

Om du ser en *behörighet som begärs* i popup-fönstret från Microsoft, bevilja du medgivande för det här programmet och accepterar att fortsätta.

## <a name="add-the-sample-data"></a>Lägg till exempel data

Därefter ska du importera exempel scenariot och grafen till ADT Explorer. Exempel scenariot finns också i mappen **Azure_Digital_Twins__ADT__explorer** som du laddade ned tidigare.

### <a name="models"></a>Modeller

Det första steget i en Azure digital-lösning med dubbla lösningar är att definiera ord listan för din miljö. Detta görs genom att skapa anpassade [**modeller**](concepts-models.md)som beskriver vilka typer av entiteter som finns i din miljö. 

Varje modell skrivs i ett JSON-LD-liknande språk som kallas **digital DTDL (Digital Definition Language)** och beskriver en enskild typ av entitet med avseende på dess *Egenskaper* , *telemetri* , *relationer* och *komponenter* . Senare kommer du att använda dessa modeller som grund för digitala dubbla, som representerar vissa instanser av dessa typer.

När du skapar en modell kommer du vanligt vis att utföra tre steg:
1. Skriv modell definitionen (i snabb starten som redan utförts som en del av exempel lösningen)
2. Verifiera den för att se till att syntaxen är korrekt (i snabb starten som redan har utförts som en del av exempel lösningen)
3. Ladda upp den till din Azure Digital-instansen
 
I den här snabb starten har modell filen redan skrivits och godkänts för dig och ingår i lösningen som du laddade ned. I det här avsnittet ska du ladda upp två fördefinierade modeller till din instans för att definiera dessa komponenter i en byggnads miljö:
* Floor
* Rummet

#### <a name="upload-models"></a>Ladda upp modeller

I rutan *modell* visas trycker du på ikonen *Ladda upp en modell* .

:::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. I rutan fil väljare som visas navigerar du till mappen *Azure_Digital_Twins__ADT__explorer/client/examples* i den hämtade lagrings platsen.
2. Välj *Room.jspå* och *Floor.jspå* och tryck på OK. (Du kan ladda upp ytterligare modeller om du vill, men de används inte i den här snabb starten.)
3. Följ dialog rutan för att be dig att logga in på ditt Azure-konto.

>[!NOTE]
>Om du ser följande fel meddelande: ett popup-meddelande visas: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna." border="false"::: 
> Försök inaktivera blockering av popup-fönster eller använda en annan webbläsare.

ADT Explorer kommer nu att ladda upp de här projektfilerna till din Azure Digital-instansen. De bör visas i rutan *modell* och visar sina egna namn och fullständiga modell-ID: n. Du kan klicka på *Visa modell* informations bubblor för att se DTDL-koden bakom dem.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna." lightbox="media/quickstart-adt-explorer/model-info.png":::
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

:::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna." lightbox="media/quickstart-adt-explorer/import-graph.png":::

I rutan fil väljare navigerar du till mappen *Azure_Digital_Twins__ADT__explorer/client/examples* och väljer kalkyl blads filen _**buildingScenario.xlsx**_ . Den här filen innehåller en beskrivning av exempel diagrammet. Tryck på OK.

Efter några sekunder öppnar ADT Explorer en *importerad* vy som visar en för hands version av grafen som ska läsas in.

Tryck på ikonen *Spara* i det övre högra hörnet i *diagramvyn* för att bekräfta att diagrammet laddas upp:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna." lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT Explorer kommer nu att använda den överförda filen för att skapa de begärda delningarna och relationerna mellan dem. En dialog ruta visas som visar att den är färdig. Tryck på *Stäng* .

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna." lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Grafen har nu laddats upp till ADT Explorer. Om du vill se diagrammet trycker du på knappen *Kör fråga* i rutan *diagram Utforskaren* , längst upp i fönstret ADT Explorer. 

:::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna." lightbox="media/quickstart-adt-explorer/run-query.png":::

Det här kör standard frågan för att välja och Visa alla digitala dubbla. ADT Explorer hämtar alla dubbla och relationer från tjänsten och ritar diagrammet som definieras av dem i rutan *diagram* .

## <a name="explore-the-graph"></a>Utforska grafen

Nu kan du se det överförda diagrammet i exempel scenariot:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna.":::

Cirklarna (graf "Nodes") representerar digitala dubbla och linjerna representerar relationer. Du kommer att se att *Floor0* -den dubbla innehåller *Room0* och att *Floor1* -filen innehåller *Room1* .

Om du använder en mus kan du klicka och dra delar av diagrammet för att flytta runt dem.

### <a name="view-twin-properties"></a>Visa dubbla egenskaper 

Du kan välja en fläta om du vill se en lista över dess egenskaper och deras värden i rutan *egenskaps Utforskaren* . 

Här följer egenskaperna för *Room0* :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Observera att *Room0* har en temperatur på **70** .

Här följer egenskaperna för *Room1* :

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Observera att *Room1* har en temperatur på **80** .

### <a name="query-the-graph"></a>Fråga i grafen

En huvud funktion i Azure Digitals flätas är möjligheten att [fråga](concepts-query-language.md) ditt dubbla diagram enkelt och effektivt att besvara frågor om din miljö. 

Ett sätt att fråga de dubblarna i diagrammet är av deras *Egenskaper* . Frågor som baseras på egenskaper kan hjälpa dig att besvara en rad olika frågor, inklusive att hitta avvikande Mät värden i din miljö som kan behöva åtgärdas.

I det här avsnittet ska du köra en fråga för att besvara följande fråga: _**Vad är alla skärnings punkter i min miljö med en temperatur på 75?**_

Om du vill se svaret kör du följande fråga i rutan *query Explorer* :

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Kom ihåg att visa de dubbla egenskaperna tidigare att *Room0* har en temperatur på **70** och *Room1* har en temperatur på **80** . Resultatet blir att endast _**Room1**_ visas i resultatet här.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna." lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Andra jämförelse operatorer ( *<* ,, *>* *=* eller *! =* ) stöds också i frågan ovan. Du kan prova att koppla dessa, olika värden eller andra dubbla egenskaper till frågan för att testa dina egna frågor.

## <a name="edit-data-in-the-graph"></a>Redigera data i grafen

Du kan använda ADT Explorer för att redigera egenskaperna för de dubbla som representeras i grafen. I det här avsnittet kommer vi att **_öka temperaturen på_ Room0 till 76** .

Det gör du genom att välja *Room0* , så visas dess egenskaps lista i rutan *egenskaps Utforskaren* .

Egenskaperna i den här listan kan redige ras. Välj temperatur svärdet **70** för att aktivera ett nytt värde. Ange **76** och tryck på ikonen *Spara* för att uppdatera temperaturen till **76** .

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

När du har sparat filen visas en *korrigerings informations* fönster som visar korrigerings koden som användes bakom kulisserna med Azure Digitals dubbla [API: er](how-to-use-apis-sdks.md) för att göra uppdateringen. Tryck på *Stäng* .

### <a name="query-to-see-the-result"></a>Fråga för att se resultatet

Kontrol lera att grafen har registrerat din uppdatering på *Room0* -temperatur genom att köra frågan igen från tidigare för att **Hämta alla dubbla i miljön med en temperatur över 75** :

```SQL
SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
```

Nu när temperaturen för *Room0* har ändrats från **70** till **76** , ska båda delarna visas i resultatet.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Vy av ett diagram av 4 cirkelformade noder som är anslutna via pilar. En cirkel med namnet &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. en cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna." lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

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

Ta sedan bort den Azure Active Directory app-registrering som du skapade för din klient app med det här kommandot:

```azurecli-interactive
az ad app delete --id <your-application-ID>
```

Slutligen tar du bort exempel mappen Project som du laddade ned till din lokala dator ( _**Azure_Digital_Twins__ADT__explorer**_ ). Du kanske måste ta bort både zippade och zippade versioner.

## <a name="next-steps"></a>Nästa steg 

Fortsätt sedan till Azure Digitals-självstudierna för att bygga upp dina egna Azure Digital-dubblare scenario-och interaktions verktyg.

> [!div class="nextstepaction"]
> [*Självstudie: koda en klient app*](tutorial-code.md)