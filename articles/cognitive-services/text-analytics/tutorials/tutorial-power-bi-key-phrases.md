---
title: Textanalys med Power BI – Azure Cognitive Services | Microsoft Docs
description: Lär dig hur du extraherar nyckelfraser i text som lagras i Power BI med hjälp av textanalys.
services: cognitive-services
author: luiscabrer
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: tutorial
ms.date: 3/07/2018
ms.author: luisca
ms.openlocfilehash: 2cdb93d44218627efdcb0360d8cf4a4eeeca177a
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "42889282"
---
# <a name="text-analytics-with-power-bi"></a>Textanalys med Power BI

Microsoft Power BI renodlar organisationsdata till snygga rapporter som du kan distribuera i organisationen för snabbare, djupare insikter. Textanalystjänsten som ingår i Cognitive Services i Microsoft Azure kan extrahera de viktigaste fraserna från text via nyckelfras-API:et. Tillsammans kan dessa verktyg hjälpa dig att snabbt se vad kunderna talar om och vad de anser om det.

I den här självstudien ser du hur du kan integrera Power BI Desktop och nyckelfras-API:et och extrahera de viktigaste fraserna från feedback från kunder med hjälp av en anpassad Power Query-funktion. Vi kan också skapa ett Word-moln av de här fraserna.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

> [!div class="checklist"]
> * Microsoft Power BI Desktop. [Hämta utan kostnad](https://powerbi.microsoft.com/get-started/).
> * Ett Microsoft Azure-konto. [Starta en kostnadsfri utvärdering](https://azure.microsoft.com/free/) eller [Logga in](https://portal.azure.com/).
> * En åtkomstnyckel för textanalys. [Registrera dig](../../cognitive-services-apis-create-account.md) och få sedan [en nyckel](../how-tos/text-analytics-how-to-access-key.md).
> * Kundkommentarer. [Hämta våra exempeldata](https://aka.ms/cogsvc/ta) eller använda egna.

## <a name="loading-customer-data"></a>Läsa in kundinformation

Börja med att öppna Power BI Desktop och läsa in den kommaavgränsade filen (CSV-filen)**FabrikamComments.csv**. Den här filen representerar en dags hypotetisk aktivitet i ett supportforum hos ett fiktivt småföretag.

> [!NOTE]
> Power BI kan använda data från en mängd olika källor, till exempel Facebook eller en SQL-databas. Läs mer på [Facebook-integrering med Power BI](https://powerbi.microsoft.com/integrations/facebook/) och [SQL Server-integrering med Power BI](https://powerbi.microsoft.com/integrations/sql-server/).

I huvudfönstret i Power BI Desktop identifierar du gruppen med externa data på startmenyfliken. Välj **Text/CSV** i den nedrullningsbara menyn **Hämta data** i den här gruppen.

![[Knappen Hämta data]](../media/tutorials/power-bi/get-data-button.png)

Dialogrutan Öppna visas. Navigera till mappen för nedladdade filer eller den mapp som innehåller exempeldatafilen. Klicka på `FabrikamComments.csv` och sedan knappen **Öppna**. Dialogrutan för CSV-import visas.

![[Dialogrutan för CSV-import]](../media/tutorials/power-bi/csv-import.png)

Via dialogrutan för .csv-import kan du kontrollera att Power BI Desktop har identifierat rätt teckenuppsättning, avgränsare, rubrikrader och kolumntyper. Den här informationen stämmer. Vi klickar på **Läs in**.

Visa inlästa data genom att växla till datavyn med hjälp av datavyknappen längs till vänster på Power BI-arbetsytan. En tabell med våra data öppnas, t.ex. i Microsoft Excel.

![[Startvy för importerade data]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="preparing-the-data"></a>Förbereda data

Du kan behöva transformera data i Power BI Desktop innan de är klara att bearbetas med nyckelfrastjänsten.

Våra data innehåller till exempel både ett `subject`-fält och ett `comment`-fält. När vi extraherar nyckelfraser ska vi titta på texten i båda de här fälten, inte bara `comment`. Funktionen för slå ihop kolumner i Power BI Desktop underlättar den här uppgiften.

Öppna frågeredigeraren i huvudfönstret för Power BI Desktop genom att klicka på **Redigera frågor** i gruppen med externa data på startmenyfliken. 

![[Gruppen med externa data på startmenyfliken]](../media/tutorials/power-bi/edit-queries.png)

Välj FabrikamComments i frågelistan på vänster sida av fönstret om det inte redan är valt.

Välj nu båda kolumnerna, `subject` och `comment`, i tabellen. Du kan behöva bläddra vågrätt för att se de här kolumnerna. Klicka först på kolumnrubriken `subject`. Håll sedan ned Ctrl-tangenten och klicka sedan på kolumnrubriken `comment`.

![[Markera fält som ska slås samman]](../media/tutorials/power-bi/select-columns.png)

I gruppen textkolumner i menyfliksområdet för transformering klickar du på **Slå ihop kolumner**. Dialogrutan Slå ihop kolumner visas.

![[Slå samman fält med dialogrutan Slå ihop kolumner]](../media/tutorials/power-bi/merge-columns.png)

I dialogrutan Slå ihop kolumner väljer du Tab som avgränsare. Klicka sedan på **OK.** Klart!

Du kan också överväga att filtrera bort tomma meddelanden med filtret Ta bort tom och att ta bort icke utskrivbara tecken med Rensa-transformeringen. Om det i dina data finns en kolumn som kolumnen `spamscore` i vår exempelfil kan du hoppa över skräpkommentarer med hjälp av ett nummerfilter.

## <a name="understanding-the-api"></a>Förstå API:et

Nyckelfras-API:et kan bearbeta upp till tusen textdokument per HTTP-begäran. Power BI föredrar dock att hantera poster en i taget, så att våra anrop till API:et innehåller endast ett enskilt dokument. [API: et](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) kräver följande fält för varje dokument som bearbetas.

| | |
| - | - |
| `id`  | En unik identifierare för det här dokumentet inom begäran. Svaret innehåller även det här fältet. På så sätt kan du enkelt koppla de extraherade nyckelfraserna med det dokument de kom från, om du bearbetar flera dokument. Vi bearbetar ett dokument per begäran, så vi vet redan vilket dokument svaret är kopplat till och `id` kan vara samma i varje begäran.|
| `text`  | Texten som ska bearbetas. Vi hämta från kolumnen `Merged` som vi har skapat och som innehåller den kombinerade ämnesraden och kommentarstexten. Enligt nyckelfras-API:et får dessa data inte vara längre än cirka 5 000 tecken.|
| `language` | Koden som representerar språket som dokumentet är skrivet i. Alla våra meddelanden är på engelska, så vi kan hårdkoda språket `en` i frågan.|

Vi behöver kombinera de här fälten i ett JSON-dokument (JavaScript Object Notation) för överföring till nyckelfraser-API:et. Svaret från den här begäran är också ett JSON-dokument. Vi måste tolka det och fiska ut nyckelfraserna.

## <a name="creating-a-custom-function"></a>Skapa en anpassad funktion

Nu är vi redo att skapa den anpassade funktionen som integrerar Power BI och textanalysen. Power BI Desktop anropar funktionen för varje rad i tabellen och skapar en ny kolumn med resultaten.

Funktionen tar emot texten som ska behandlas som parameter. Den konverterar data till och från JSON som krävs och gör en HTTP-begäran till nyckelfras-API-slutpunkten. När vi har tolkat svaret returnerar funktionen en sträng som innehåller en kommaavgränsad lista över extraherade nyckelfraser.

> [!NOTE]
> De anpassade funktionerna i Power BI Desktop är skrivna med [Power Query M-formelspråket](https://msdn.microsoft.com/library/mt211003.aspx), även kallat M. M är ett funktionellt programmeringsspråk som baseras på [F#](https://docs.microsoft.com/dotnet/fsharp/). Du behöver inte vara programmerare för att genomföra den här självstudien. Den kod som krävs finns nedan.

Du bör fortfarande vara i Query Editor-fönstret. Navigera till startmenyfliken. Klicka på **Ny källa** (på fliken Ny fråga) och välj **Tom fråga** i den nedrullningsbara menyn. 

En ny fråga, inledningsvis med namnet Query1, visas i listan med frågor. Dubbelklicka på den här posten och ge den namnet `KeyPhrases`.

Öppna nu fönstret Avancerad redigerare genom att klicka på **Avancerad redigerare** i frågegruppen på startmenyfliken. Ta bort den kod som redan finns i fönstret och klistra in följande kod. 

> [!NOTE]
> I exemplen nedan förutsätter vi att slutpunkten finns vid https://westus.api.cognitive.microsoft.com.  I textanalysen finns funktioner för prenumeration i 13 olika regioner. Om du har registrerat dig för tjänsten i en annan region ska du se till att använda slutpunkten för den region du har valt. Den bör visas på översiktssidan i Azure-portalen när du väljer textanalysprenumeration.

```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

Klistra också in textanalys-API-nyckeln, från Microsoft Azure-instrumentpanelen, i den rad som börjar med `apikey`. (Kom ihåg att lämna de dubbla citattecknen före och efter nyckeln.) Klicka sedan på **Klar**.

## <a name="using-the-function"></a>Använda funktionen

Vi kan nu hämta nyckelfraser i var och en av våra kundkommentarer med den anpassade funktionen. Vi lagrar dem i en ny kolumn i tabellen. 

Du är fortfarande i frågeredigeraren. Byt tillbaka till frågan FabrikamComments, byt till menyfliksområdet Lägg till kolumn och klicka på knappen **Anropa anpassad funktion** i den allmänna gruppen.

![[Knappen Anropa anpassad funktion]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

I dialogrutan Anropa anpassad funktion anger du `keyphrases` som namn på den nya kolumnen. Välj vår anpassade funktion `KeyPhrases`, som funktionsfrågan. 

Ett nytt fält visas i dialogrutan. Vi får frågan vilket fält vi vill skicka till vår funktion som parametern `text`. Välj `Merged` (den kolumn vi skapade tidigare genom att slå samman ämnes- och meddelandefälten) i den nedrullningsbara menyn.

![[Anropa anpassad funktion]](../media/tutorials/power-bi/invoke-custom-function.png)

Klicka slutligen på **OK**.

Om allt är klart anropar Power BI vår funktion en gång för varje rad i tabellen. Nyckelfrasfrågor utförs och den nya kolumnen läggs till i tabellen. Innan det händer kan du behöva ange inställningar för autentisering och sekretess.

## <a name="authentication-and-privacy"></a>Autentisering och sekretess

När du stänger dialogrutan Anropa anpassad funktion visas en banderoll där du uppmanas att ange hur du vill ansluta till nyckelfrasslutpunkten.

![[meddelande om autentiseringsuppgifter]](../media/tutorials/power-bi/credentials-banner.png)

Klicka på **Redigera autentiseringsuppgifter**. Se till att Anonym är vald i dialogrutan. Klicka sedan på **Anslut.** 

> [!NOTE]
> Varför anonym? Textanalystjänsten autentiserar dig med API-nyckeln. Power BI behöver inte ange autentiseringsuppgifter för HTTP-begäran.

![[ange autentisering som anonym]](../media/tutorials/power-bi/access-web-content.png)

Om du ser meddelandet för att redigera autentiseringsuppgifter även när du har valt anonym åtkomst kan du ha glömt att klistra in API-nyckeln. Kontrollera den anpassade funktionen `KeyPhrases` i den avancerade redigeraren, så att den verkligen finns där.

Nu kan ett meddelande visas där du uppmanas att ange information om datakällornas sekretess. 

![[sekretessmeddelande]](../media/tutorials/power-bi/privacy-banner.png)

Klicka på **Fortsätt** och välj Offentlig för var och en av datakällorna i dialogrutan. Klicka sedan på **Spara**.

![[ange datakällas sekretess]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="creating-the-word-cloud"></a>Skapa ordmoln

När du har åtgärdat eventuella banderoller som visas klickar du på **Stäng och tillämpa** i startmenyfliken för att stänga frågeredigeraren.

Det tar en stund innan Power BI Desktop har slutfört nödvändiga HTTP-begäranden. För varje rad i tabellen innehåller den nya `keyphrases`-kolumnen nyckelfraser som identifieras i texten av nyckelfras-API:et. 

Vi genererar ett ordmoln med den här kolumnen. Börja med att klicka på knappen Rapport i Power BI Desktop-huvudfönstret, till vänster om arbetsytan.

> [!NOTE]
> Varför generera ett ordmoln med extraherade nyckelfraser i stället för hela texten i varje kommentar? Nyckelfraser förser oss med de *viktiga* orden från kundkommentarerna, inte bara de *vanligaste* orden. Ordstorleken i det resulterande molnet påverkas dessutom inte av ord som används ofta i ett relativt litet antal kommentarer.

Installera den anpassade ordmolnsvyn om du inte redan har den. Navigera till panelen Visualiseringar till höger om arbetsytan. Klicka på de tre punkterna (**...**) och välj **Importera från lagret**. Sök sedan efter moln och klicka på knappen **Lägg till** bredvid ordmolnsvyn. Power BI installerar ordmolnsvyn och bekräftar att installationen slutfördes.

![[lägga till anpassad visuell vy]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

Nu ska vi skapa vårt ordmoln!

![[ordmolnsikon i visualiseringspanel]](../media/tutorials/power-bi/visualizations-panel.png)

Klicka först på ikonen Ordmoln på panelen visualiseringar. En ny rapport visas i arbetsytan. Dra fältet `keyphrases` från fältpanelen till kategorifältet på panelen Visualiseringar. Ordmolnet visas i rapporten.

Växla nu till sidan Format på panelen Visualiseringar. I kategorin Stoppord slår du på **Standard-stoppord** för att slippa korta och vanliga ord som ”och” i molnet. 

![[aktivera standard-stoppord]](../media/tutorials/power-bi/default-stop-words.png)

Lite längre ned på panelen slår du av **Rotera texten** och **Title** (rubrik/titel).

![[aktivera fokusläge]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

Klicka på verktyget Fokusläge i rapporten för att titta närmare på ordmolnet. Verktyget expanderar ordmolnet så att det fyller hela arbetsytan. Se nedan.

![[ett ordmoln]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>Fler textanalystjänster

Textanalyssjänsten, som ingår i Cognitive Services i Microsoft Azure, omfattar också attitydanalysen och språkidentifiering. Språkidentifieringen är särskilt användbart om du får kundfeedback på andra språk än engelska.

Båda dessa andra API:er är mycket lika nyckelfras-API:et. Nästan identiska anpassade funktioner kan därför användas till att integrera dem med Power BI Desktop. Skapa helt enkelt en tom fråga och klistra in lämplig kod nedan i den avancerade redigeraren, som du gjorde tidigare. (Glöm inte din åtkomstnyckel!) Lägg sedan, som tidigare, till funktionen för att lägga till en ny kolumn i tabellen.

Attitydanalysen returnerar ett värde som anger hur positiva åsikterna som uttrycks i texten är.

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[score]
in  sentiment
```

Här följer två typer av språkidentifiering. Den första returnerar ISO-språkkoden (t.ex. `en` för engelska), och den andra returnerar ett smeknamn (t.ex. `English`). Du kanske märker att bara den sista raden i texten skiljer sig åt mellan de två versionerna.

```fsharp
// Returns the two-letter language code (e.g. en for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (e.g. English) of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

Här är en variant av nyckelfrasfunktionen som redan visas. Den returnerar fraserna som listobjekt snarare än som en sträng med kommaseparerade fraser. 

> [!NOTE]
> Returnerar en enkel sträng, förenklad, vårt ordmolnsexempel. En lista är å andra sidan ett mer flexibelt format för att arbeta med returnerade fraser i Power BI. Du kan ändra listobjekten i Power BI Desktop. Använd gruppen Strukturerad kolumn på frågeredigerarens transformeringsmenyflik.

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>Nästa steg

Läs mer om textanalystjänsten, Power Query M-formelspråket och Power BI.

> [!div class="nextstepaction"]
> [API för textanalys – referens](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

> [!div class="nextstepaction"]
> [Power Query M – referens](//msdn.microsoft.com/library/mt211003.aspx)

> [!div class="nextstepaction"]
> [Power BI-dokumentation](//powerbi.microsoft.com/documentation/powerbi-landing-page/)
