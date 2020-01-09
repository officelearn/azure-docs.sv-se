---
title: 'Snabb start: testa kunskaps bas med batch-frågor'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/19/2019
ms.author: diberry
ms.openlocfilehash: 9483db2187c05fe8e0f4fa2d41c17b8748ba3db7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474041"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>Snabb start: testa kunskaps bas med batch-frågor och förväntade svar

Använd batch-testverktyget QnA Maker för att testa kunskaps baserna i din QnA Maker-resurs för förväntade svar, förtroende poäng och frågor om flera inaktivitet.

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Skapa antingen en QNA Maker tjänst](create-publish-knowledge-base.md#create-a-new-qna-maker-knowledge-base) eller Använd en befintlig tjänst som använder det engelska språket för det exempel dokument som används i den här snabb starten.
* Ladda ned [`.docx`-filen med flera turn-exempel](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Hämta [batch-testverktyget](https://aka.ms/qnamakerbatchtestingtool), extrahera den körbara filen från `.zip`-filen.

## <a name="sign-into-qna-maker-portal"></a>Logga in på QnA Maker Portal

[Logga](https://www.qnamaker.ai/) in på QNA Maker Portal.

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Skapa en ny kunskaps bas från filen multi-turn Sample. docx

1. Välj **skapa en kunskaps bas** i verktygsfältet.
1. Hoppa över **steg 1** eftersom du redan bör ha en QNA Maker resurs, gå vidare till **steg 2** för att välja din befintliga resursinformation:
    * Azure Active Directory ID
    * Namn på Azure-prenumeration
    * Namn på Azure QnA-tjänsten
    * Språk – det engelska språket
1. Ange namnet `Multi-turn batch test quickstart` som namn på din kunskaps bas.
1. I **steg 4**markerar du **Aktivera extrahering av flera turn från URL: er,. PDF eller. docx-filer**.
1. Ange **standard svars texten** för `Quickstart - can't find answer`. I en produktions kunskaps bas bör den här informationen vara mer information till användaren, men för den här snabb starten fungerar ett enkelt svar.
1. I **steg 4**väljer du **+ Lägg till fil** och väljer sedan den nedladdade `.docx` fil listan i kraven.
1. I **steg 5**väljer **du skapa din KB**.

    När processen har slutförts visas den redigerbara kunskaps basen i portalen.

## <a name="save-train-and-publish-knowledge-base"></a>Spara, träna och publicera kunskaps bas

1. Spara kunskaps basen genom att välja **Spara och träna** från verktygsfältet.
1. Välj **publicera** från verktygsfältet och välj sedan **publicera** igen för att publicera kunskaps basen. Publicering gör kunskaps basen tillgänglig för frågor från en offentlig URL-slutpunkt. När publiceringen är klar sparar du värd-URL och information om slut punkts nyckel som visas på **publicerings** sidan.

    |Obligatoriska data| Exempel|
    |--|--|
    |Publicerad värd|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Publicerad nyckel|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` (32 tecken sträng som visas efter `Endpoint`)|
    |App-ID|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (36 tecken sträng visas som en del av `POST`) |

## <a name="create-batch-test-file-with-question-ids"></a>Skapa batch-testfilen med fråga-ID

För att kunna använda batch-testverktyget, skapar du en fil med namnet `batch-test-data-1.tsv` med en text redigerare. Filen måste ha följande kolumner avgränsade med en flik.

|Fält för TSV-indatafiler|Anteckningar|Exempel|
|--|--|--|
|Kunskaps bas-ID|Ditt kunskaps bas-ID finns på publicerings sidan. Testa flera kunskaps banker i samma tjänst samtidigt i en enda fil med hjälp av olika kunskaps bas-ID: n i en enda fil.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (36 tecken sträng visas som en del av `POST`) |
|Fråga|Den frågetext som en användare skulle ange. 1 000-Character max.|`How do I sign out?`|
|Metadatataggar|valfri|`topic:power` använder _nyckel: värde_ formatet|
|Övre parameter|valfri|`25`|
|Förväntat svars-ID|valfri|`13`|

I den här kunskaps basen lägger du till 3 rader av bara de 2 obligatoriska kolumnerna i filen. Den första kolumnen är ditt kunskaps bas-ID och den andra kolumnen bör vara följande lista över frågor:

|Kolumn 2 – frågor|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Dessa frågor är den exakta orda lydelsen från kunskaps basen och ska returnera 100 som konfidens resultat.

Lägg sedan till några frågor, liknande dessa frågor, men inte exakt samma på tre fler rader med samma kunskaps bas-ID:

|Kolumn 2 – frågor|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Se till att varje kolumn är avgränsad med en tabb-avgränsare. Inledande eller avslutande blank steg läggs till i kolumn data och gör att programmet returnerar undantag när typen eller storleken är felaktig.

Batch-testfilen när den öppnas i Excel ser ut som följande bild. Kunskaps bas-ID: t har ersatts med `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` för säkerhet. Se till att kolumnen visar ditt kunskaps bas-ID för ditt eget batch-test.

> [!div class="mx-imgBorder"]
> ![indata från första versionen av. tsv-filen från batch-test](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>Testa kommando filen

Kör batch testing-programmet med följande CLI-format på kommando raden.

Ersätt `YOUR-RESOURCE-NAME` och `ENDPOINT-KEY` med dina egna värden för tjänst namn och slut punkts nyckel. Dessa värden finns på sidan **Inställningar** på QNA Maker-portalen.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
Testet slutförs och genererar `out.tsv`-filen:

> [!div class="mx-imgBorder"]
> ![ut den första versionen av. tsv-filen från batch-test](../media/batch-test/batch-test-1-output.png)

Kunskaps bas-ID: t har ersatts med `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` för säkerhet. För ditt eget batch-test visar kolumnen ditt kunskaps bas-ID.

Test resultatet av förtroende poängen, i den fjärde kolumnen, visar de tre främsta frågorna returnerade en poäng på 100 som förväntat eftersom varje fråga är exakt samma som den visas i kunskaps basen. De senaste tre frågorna, med en ny formulering av frågan, returnerar inte 100 som säkerhets poäng. För att öka poängen både för testet och användarna måste du lägga till fler alternativa frågor i kunskaps basen.

## <a name="testing-with-the-optional-fields"></a>Testa med valfria fält

När du förstår formatet och processen kan du generera en testfil för att köra mot din kunskaps bas från en data källa, t. ex. från Chat-loggar.

Eftersom data källan och processen automatiseras, kan test filen köras flera gånger med olika inställningar för att fastställa korrekta värden.

Om du till exempel har en chatt-logg och vill bestämma vilken Chat-text som ska användas för vilka metadatafält, skapar du en test fil och anger metadata-fälten för varje rad. Kör testet och granska sedan de rader som matchar metadata. I allmänhet ska matchningarna vara positiva, men du bör granska resultaten för falska positiva identifieringar. Ett falskt positivt värde är en rad som matchar metadata, men som baseras på texten, den ska inte matcha.

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>Använda valfria fält i test filen för indata-batch

Använd följande diagram för att förstå hur du hittar fältvärdena för valfria data.

|Kolumn nummer|Valfri kolumn|Dataplats|
|--|--|--|
|3|metadata|Exportera befintlig kunskaps bas för befintlig _nyckel:_ värdepar.|
|4|överst|Standardvärdet för `25` rekommenderas.|
|5|ID för fråga och svars uppsättning|Exportera befintlig kunskaps bas för ID-värden. Observera också att ID: n returnerades i utdatafilen.|

## <a name="add-metadata-to-the-knowledge-base"></a>Lägg till metadata i kunskaps basen

1. På sidan **redigera** i QNA-portalen lägger du till metadata för `topic:power` på följande frågor:

    |Frågor|
    |--|
    |Debitera din yta Pro 4|
    |Kontrol lera batteri nivån|

    Två QnA uppsättningar har metadata uppsättningen.

    > [!TIP]
    > Exportera kunskaps basen om du vill se metadata och QnA-ID för varje uppsättning. Välj sidan **Inställningar** och välj sedan **exportera** som en `.xls`-fil. Hitta den här hämtade filen och öppna med Excel-granska för metadata och ID.

1. Välj **Spara och träna**, välj sedan **publicerings** sidan och välj sedan knappen **publicera** . De här åtgärderna gör ändringen tillgänglig för batch-testet. Hämta kunskaps basen från sidan **Inställningar** .

    Den hämtade filen har rätt format för metadata och korrekt fråge-och svars uppsättnings-ID. Använd de här fälten i nästa avsnitt

    > [!div class="mx-imgBorder"]
    > ![exporterad kunskaps bas med metadata](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>Skapa ett andra batch-test

Det finns två huvud scenarier för batch-testning:
* **Bearbeta chatt-loggfiler** – Bestäm det främsta svaret för en tidigare osett-fråga – den vanligaste situationen är när du behöver bearbeta logg filen för frågor, till exempel en chatts användar frågor. Skapa ett test av en batch-fil med endast de kolumner som krävs. Testet returnerar det främsta svaret för varje fråga. Det innebär inte att det är det högsta svaret är rätt svar. När du har slutfört testet går du vidare till verifierings testet.
* **Verifierings test** – verifiera förväntat svar. Det här testet kräver att alla frågor och matchar förväntade svar i batch-testet har verifierats. Detta kan kräva viss manuell process.

Följande procedur förutsätter att scenariot är att bearbeta chatt loggar med 

1. Skapa en ny batch-testfil för att inkludera valfria data, `batch-test-data-2.tsv`. Lägg till de 6 raderna från den ursprungliga batch-testets indatafil och Lägg sedan till ID för metadata, topp och QnA för varje rad.

    För att simulera den automatiserade processen med att kontrol lera ny text från Chat-loggar mot kunskaps basen ställer du in metadata för varje kolumn till samma värde: `topic:power`.

    > [!div class="mx-imgBorder"]
    > ![andra indata-versionen av. tsv-filen från batch-test](../media/batch-test/batch-test-2-input.png)

1. Kör testet igen och ändra namnen på indata-och utdatafiler för att ange att det är det andra testet.

    > [!div class="mx-imgBorder"]
    > ![ut den andra versionen av. tsv-filen från batch-test](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>Test resultat och ett automatiserat test system

Den här test utdatafilen kan parsas som en del av en automatiserad kontinuerlig test-pipeline.

Den här test utmatningen bör läsas som: varje rad filtreras med metadata och eftersom varje rad inte matchar metadata i kunskaps basen returnerades standard svaret för de icke-matchande raderna ("ingen lämplig matchning hittades i KB"). För de rader som var identiska returnerades QnA-ID och poängen.

Alla rader returnerade etiketten fel eftersom ingen rad matchade svars-ID: t förväntades.

Du bör kunna se med de här resultaten att du kan ta en chatt-logg och använda texten som fråga för varje rad. Om du inte vet något om data kan du få mer information om de data som du sedan kan använda för att flytta framåt:

* Meta-data
* QnA-ID
* poäng

Var det en bra idé att filtrera med meta-data för testet? Ja och nej. Test systemet bör skapa testfiler för varje Meta-Data-par samt ett test utan meta-datapar.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att testa kunskaps basen tar du bort batch-filverktyget och testfilerna.

Om du inte kommer att fortsätta att använda den här kunskaps basen tar du bort kunskaps basen med följande steg:

1. I QnA Maker-portalen väljer du **Mina kunskaps banker** på den översta menyn.
1. I listan över kunskaps baser väljer du ikonen **ta bort** på raden i kunskaps basen för den här snabb starten.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)
