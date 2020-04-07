---
title: 'Snabbstart: Testa kunskapsbas med batchfrågor'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 3bc095d8949f177ccb6c4cc111ba4b272027904e
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756709"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>Snabbstart: Testa kunskapsbas med batchfrågor och förväntade svar

Använd batchtestverktyget QnA Maker för att testa kunskapsbaserna i QnA Maker-resursen för förväntade svar, konfidenspoäng och flervarvsprompter.

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Skapa [antingen en QnA Maker-tjänst](create-publish-knowledge-base.md) eller använd en befintlig tjänst som använder det engelska språket.
* Ladda ner [exempelfilen `.docx` för flera varv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Hämta [batchtestverktyget](https://aka.ms/qnamakerbatchtestingtool)och extrahera den körbara filen från `.zip` filen.

## <a name="sign-into-qna-maker-portal"></a>Logga in på QnA Maker-portalen

[Logga in](https://www.qnamaker.ai/) på QnA Maker-portalen.

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Skapa en ny kunskapsbas från filen sample.docx med flera svängar

1. Välj **Skapa en kunskapsbas** i verktygsfältet.
1. Hoppa över **steg 1** eftersom du redan bör ha en QnA Maker-resurs och gå vidare till **steg 2** för att välja din befintliga resursinformation:
    * Azure Active Directory-ID
    * Namn på Azure-prenumeration
    * Azure QnA-tjänstnamn
    * Språk - det engelska språket
1. Ange namnet `Multi-turn batch test quickstart` som namnet på kunskapsbasen.

1. Konfigurera inställningarna med följande tabell i **steg 4:**

    |Inställning|Värde|
    |--|--|
    |**Aktivera extrahering med flera svängar från webbadresser, PDF- eller .docx-filer.**|Markerad|
    |**Standardsvarstext**| `Batch test - default answer not found.`|
    |**+ Lägg till fil**|Välj den `.docx` hämtade fillistan i förutsättningarna.|
    |**Chit-chatt**|Välj **Professional**|

1. I **steg 5**väljer du **Skapa kb**.

    När skapandeprocessen är klar visar portalen den redigerbara kunskapsbasen.

## <a name="save-train-and-publish-knowledge-base"></a>Spara, träna och publicera kunskapsbas

1. Välj **Spara och träna** i verktygsfältet för att spara kunskapsbasen.
1. Välj **Publicera** i verktygsfältet och välj sedan **Publicera** igen om du vill publicera kunskapsbasen. Publicering gör kunskapsbasen tillgänglig för frågor från en offentlig URL-slutpunkt. När publiceringen är klar sparar du värd-URL:en och slutpunktsnyckelinformationen som visas på sidan **Publicera.**

    |Nödvändiga data| Exempel|
    |--|--|
    |Publicerad värd|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Publicerad nyckel|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`(32 teckensträng `Endpoint` visas efter )|
    |App-ID|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(36 teckensträng som `POST`visas som en del av ) |

## <a name="create-batch-test-file-with-question-ids"></a>Skapa batchtestfil med fråge-ID

Skapa en fil med namnet `batch-test-data-1.tsv` med en textredigerare för att använda batchtestverktyget. Filen måste ha följande kolumner åtskilda av en flik.

|TSV-inmatningsfilfält|Anteckningar|Exempel|
|--|--|--|
|Id för kunskapsbas|Ditt kunskapsbas-ID finns på sidan Publicera. Testa flera kunskapsbaser i samma tjänst samtidigt i en enda fil med hjälp av olika kunskapsbas-ID:er i en enda fil.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`(36 teckensträng som `POST`visas som en del av ) |
|Fråga|Den frågetext en användare skulle skriva in. 1000 tecken max.|`How do I sign out?`|
|Metadatataggar|valfri|`topic:power`använder _nyckeln:värdeformat_|
|Parametern Översta|valfri|`25`|
|Förväntat svars-ID|valfri|`13`|

För den här kunskapsbasen lägger du till 3 rader med bara de 2 kolumner som krävs i filen. Den första kolumnen är ditt kunskapsbas-ID och den andra kolumnen ska vara följande lista med frågor:

|Kolumn 2 - frågor|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Dessa frågor är den exakta ordalydelsen från kunskapsbasen och bör returnera 100 som förtroendepoäng.

Lägg sedan till några frågor, liknande dessa frågor men inte exakt samma på 3 rader, med samma kunskapsbas-ID:

|Kolumn 2 - frågor|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Kontrollera att varje kolumn endast avgränsas med en flikavgränsare. Inledande eller avslutande blanksteg läggs till i kolumndata och gör att programmet kastar undantag när typen eller storleken är felaktig.

Batchtestfilen, när den öppnas i Excel, ser ut som följande bild. Kunskapsbas-ID har `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ersatts med för säkerhet. För ditt eget batchtest kontrollerar du att kolumnen visar ditt kunskapsbas-ID.

> [!div class="mx-imgBorder"]
> ![Mata in första versionen av TSV-filen från batchtest](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>Testa batchfilen

Kör batchtestprogrammet med följande CLI-format på kommandoraden.

Ersätt `YOUR-RESOURCE-NAME` `ENDPOINT-KEY` och med dina egna värden för tjänstnamn och slutpunktsnyckel. Dessa värden finns på sidan **Inställningar** i QnA Maker-portalen.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
Testet slutförs och genererar `out.tsv` filen:

> [!div class="mx-imgBorder"]
> ![Utdata första versionen av .tsv-fil från batchtest](../media/batch-test/batch-test-1-output.png)

Kunskapsbas-ID har `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ersatts med för säkerhet. För ditt eget batchtest visar kolumnen ditt kunskapsbas-ID.

Testutgången av konfidenspoäng, i den 4: e kolumnen, visar de 3 vanligaste frågorna returnerade en poäng på 100 som förväntat eftersom varje fråga är exakt samma som den visas i kunskapsbasen. De tre sista frågorna, med ny formulering av frågan, returnerar inte 100 som förtroendepoäng. För att öka poängen både för testet och användarna måste du lägga till fler alternativa frågor till kunskapsbasen.

## <a name="testing-with-the-optional-fields"></a>Testa med valfria fält

När du förstår formatet och processen kan du generera en testfil, för att köra mot din kunskapsbas från en datakälla, till exempel från chattloggar.

Eftersom datakällan och processen är automatiserade kan testfilen köras många gånger med olika inställningar för att fastställa rätt värden.

Om du till exempel har en chattlogg och vill ta reda på vilken chattloggtext som gäller för vilka metadatafält, skapar du en testfil och anger metadatafälten för varje rad. Kör testet och granska sedan raderna som matchar metadata. I allmänhet bör matchningarna vara positiva, men du bör granska resultaten för falska positiva identifieringar. Ett falskt positivt är en rad som matchar metadata men baserat på texten bör den inte matcha.

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>Använda valfria fält i testfilen för indatabatch

Använd följande diagram för att förstå hur du hittar fältvärdena för valfria data.

|Kolumnnummer|Valfri kolumn|Dataplats|
|--|--|--|
|3|metadata|Exportera befintlig kunskapsbas för befintliga _nyckel:värdepar._|
|4|överst|Standardvärdet `25` för rekommenderas.|
|5|Id för fråga och svar|Exportera befintlig kunskapsbas för ID-värden. Lägg också märke till att ID:na returnerades i utdatafilen.|

## <a name="add-metadata-to-the-knowledge-base"></a>Lägga till metadata i kunskapsbasen

1. Lägg till metadata `topic:power` för följande frågor på sidan **Redigera** på QnA-portalen:

    |Frågor|
    |--|
    |Ladda din Surface Pro 4|
    |Kontrollera batterinivån|

    Två QnA-par har metadatauppsättningen.

    > [!TIP]
    > Om du vill visa metadata och QnA-ID:er för varje uppsättning exporterar du kunskapsbasen. Välj sidan **Inställningar** och välj `.xls` sedan **Exportera** som en fil. Hitta den nedladdade filen och öppna med Excel granska för metadata och ID.

1. Välj **Spara och träna**och välj sedan sidan **Publicera** och välj sedan knappen **Publicera.** Dessa åtgärder gör ändringen tillgänglig för batchtestet. Hämta kunskapsbasen från sidan **Inställningar.**

    Den nedladdade filen har rätt format för metadata och rätt fråge- och svarsuppsättnings-ID. Använda dessa fält i nästa avsnitt

    > [!div class="mx-imgBorder"]
    > ![Exporterad kunskapsbas med metadata](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>Skapa ett andra batchtest

Det finns två huvudscenarier för batchtestning:
* **Bearbeta chattloggfiler** - Bestäm det översta svaret för en tidigare osedda fråga - den vanligaste situationen är när du behöver bearbeta är loggfil med frågor, till exempel en chattrobot användarfrågor. Skapa ett batchfilstest med endast nödvändiga kolumner. Testet returnerar det översta svaret för varje fråga. Det betyder inte att det översta svaret är det rätta svaret. När du har slutfört det här testet går du vidare till valideringstestet.
* **Valideringstest** - Validera det förväntade svaret. Det här testet kräver att alla frågor och matchande förväntade svar i batchtestet har validerats. Detta kan kräva viss manuell process.

Följande procedur förutsätter att scenariot är att bearbeta chattloggar med

1. Skapa en ny batchtestfil för `batch-test-data-2.tsv`att inkludera valfria data. Lägg till de 6 raderna från den ursprungliga batchtestinmatningsfilen och lägg sedan till metadata, övre och QnA-par-ID för varje rad.

    Om du vill simulera den automatiska processen att kontrollera ny text från chattloggar mot `topic:power`kunskapsbasen anger du metadata för varje kolumn till samma värde: .

    > [!div class="mx-imgBorder"]
    > ![Indata andra versionen av .tsv-fil från batchtest](../media/batch-test/batch-test-2-input.png)

1. Kör testet igen och ändra indata- och utdatafilnamnen för att visa att det är det andra testet.

    > [!div class="mx-imgBorder"]
    > ![Utdata andra versionen av .tsv-fil från batchtest](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>Testresultat och ett automatiserat testsystem

Den här testutdatafilen kan tolkas som en del av en automatiserad kontinuerlig testpipeline.

Den här specifika testutdata bör läsas som: varje rad filtrerades med metadata, och eftersom varje rad inte matchade metadata i kunskapsbasen, standardsvaret för de rader som inte matchar returneras ("ingen bra matchning finns i kb"). Av de rader som matchade returnerades QnA ID och poäng.

Alla rader returnerade etiketten på felaktig eftersom ingen rad matchade det förväntade svars-ID:t.

Du bör kunna se med dessa resultat att du kan ta en chattlogg och använda texten som fråga för varje rad. Utan att veta något om data, resultaten berätta mycket om de data som du sedan kan använda framåt:

* metadata
* QnA-ID
* poäng

Var filtrering med metadata en bra idé för testet? Ja och nej. Testsystemet bör skapa testfiler för varje metadatapar samt ett test utan metadatapar.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta testa kunskapsbasen tar du bort batchfilsverktyget och testfilerna.

Om du inte ska fortsätta att använda den här kunskapsbasen tar du bort kunskapsbasen med följande steg:

1. I QnA Maker-portalen väljer du **Mina kunskapsbaser** på den övre menyn.
1. I listan över kunskapsbaser väljer du ikonen **Ta bort** på raden i kunskapsbasen för den här snabbstarten.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)
