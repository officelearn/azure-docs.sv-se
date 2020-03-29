---
title: Extrahera information i Excel med textanalys och Power Automate
titleSuffix: Azure Cognitive Services
description: Lär dig hur du extraherar Excel-text utan att behöva skriva kod med hjälp av TextAnalys och Power Automate.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: fd70fe14d3765fb7c21b92f62b4d73564176baa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78201192"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Extrahera information i Excel med textanalys och Power Automate 

I den här självstudien skapar du ett Power Automate-flöde för att extrahera text i ett Excel-kalkylblad utan att behöva skriva kod. 

Detta flöde kommer att ta ett kalkylblad av frågor som rapporterats om ett lägenhetskomplex, och klassificera dem i två kategorier: VVS och andra. Det kommer också att extrahera namn och telefonnummer till de hyresgäster som skickade dem. Slutligen kommer flödet att lägga till den här informationen i Excel-bladet. 

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Använd Power Automate för att skapa ett flöde
> * Ladda upp Excel-data från OneDrive för företag
> * Extrahera text från Excel och skicka den till API:et för textanalys 
> * Använd informationen från API:et för att uppdatera ett Excel-blad.

## <a name="prerequisites"></a>Krav

- Ett Microsoft Azure-konto. [Starta en kostnadsfri utvärdering](https://azure.microsoft.com/free/) eller [Logga in](https://portal.azure.com/).
- En textanalysresurs. Om du inte har en kan du [skapa en i Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) och använda den kostnadsfria nivån för att slutföra den här självstudien.
- [Nyckeln och slutpunkten](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som genererades för dig under registreringen.
- Ett kalkylblad som innehåller klientproblem. Exempeldata finns på GitHub
- Office 365 med OneDrive för företag.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Lägga till Excel-filen i OneDrive för företag

Hämta exempelfilen excel från [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx). Den här filen måste lagras i ditt OneDrive för företag-konto.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Exempel från Excel-filen.":::

Problemen rapporteras i rå text. Vi använder textanalys-API:et med namnet Entity Recognition för att extrahera personens namn och telefonnummer. Då flödet kommer att leta efter ordet "VVS" i beskrivningen för att kategorisera frågorna. 

## <a name="create-a-new-power-automate-workflow"></a>Skapa ett nytt Power Automate-arbetsflöde

Gå till [power automate-webbplatsen](https://preview.flow.microsoft.com/)och logga in. Klicka sedan på **Skapa** och **schemalagt flöde**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="Skärmen för att skapa flödet.":::


Initiera flödet med följande fält på sidan **Skapa ett schemalagt flöde:**

|Field |Värde  |
|---------|---------|
|**Flödesnamn**     | **Schemalagd granskning** eller ett annat namn.         |
|**Start**     |  Ange aktuellt datum och aktuell tid.       |
|**Upprepa varje**     | **1 timme**        |

## <a name="add-variables-to-the-flow"></a>Lägga till variabler i flödet

> [!NOTE]
> Om du vill se en bild av det slutförda flödet kan du hämta den från [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams). 

Skapa variabler som representerar den information som ska läggas till i Excel-filen. Klicka på **Nytt steg** och sök efter **variabeln Initialisera**. Gör detta fyra gånger för att skapa fyra variabler.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Initiera variabler.":::

Lägg till följande information i de variabler som du skapade. De representerar kolumnerna i Excel-filen. Om några variabler är komprimerade kan du klicka på dem för att expandera dem.

| Åtgärd |Namn   | Typ | Värde |
|---------|---------|---|---|
| Initialisera variabel | var_person | String | Person |
| Initiera variabel 2 | var_phone | String | Phone_Number |
| Initiera variabel 3 | var_plumbing | String | Vvs |
| Initiera variabel 4 | var_other | String | övrigt | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="information i flödesvariablerna":::

## <a name="read-the-excel-file"></a>Läs Excel-filen

Klicka på **Nytt steg** och skriv **Excel**och välj sedan Lista rader som finns i en **tabell i** listan över åtgärder.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="lägga till excel-rader.":::

Lägg till Excel-filen i flödet genom att fylla i fälten i den här åtgärden. Den här självstudien kräver att filen har överförts till OneDrive för företag.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="lägga till excel-rader.":::

Klicka på **Nytt steg** och lägg till en Använd **på varje** åtgärd.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="lägga till ett kommandot apply.":::

Klicka på **Välj en utdata från föregående steg**. Välj **värde**i rutan Dynamiskt innehåll som visas .

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Välj utdata från Excel-filen.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Skicka en begäran till API:et för textanalys

Om du inte redan har gjort det måste du skapa en [Text Analytics-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) i Azure-portalen.

### <a name="create-a-text-analytics-connection"></a>Skapa en textanalysanslutning

Klicka på Lägg till **en åtgärd**i sidan Använd på **varje**. Gå till din Text Analytics-resurs **nyckel- och slutpunktssida** i Azure-portalen och hämta nyckeln och slutpunkten för din Text Analytics-resurs.

I flödet anger du följande information för att skapa en ny Text Analytics-anslutning.

> [!NOTE]
> Om du redan har skapat en Text Analytics-anslutning och vill ändra anslutningsinformationen klickar du på ellipsen längst upp till höger och klickar på **+ Lägg till ny anslutning**.

| Field           | Värde                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Anslutningsnamn | Ett namn på anslutningen till textanalysresursen. Till exempel `TAforPowerAutomate`. |
| Kontonyckel     | Nyckeln för din Text Analytics-resurs.                                                                                   |
| Url till webbplats        | Slutpunkten för din Text Analytics-resurs.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Lägg till autentiseringsuppgifter för Textanalys i flödet.":::

## <a name="extract-the-excel-content"></a>Extrahera excel-innehållet 

När anslutningen har skapats söker du efter **Textanalys** och väljer **Entiteter**. Detta extraherar information från beskrivningskolumnen för problemet.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Lägg till autentiseringsuppgifter för Textanalys i flödet.":::

Klicka i fältet **Text** och välj **Beskrivning** i fönstren Dynamiskt innehåll som visas. Ange `en` för språk. (Klicka på Visa avancerade alternativ om du inte ser Språk)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Lägg till autentiseringsuppgifter för Textanalys i flödet.":::


## <a name="extract-the-person-name"></a>Extrahera personens namn

Därefter hittar vi personentitetstypen i textanalysutdata. Klicka på Lägg till **en åtgärd**i tillämpa på **varje**åtgärd och skapa en annan Använd **på varje** åtgärd. Klicka i textrutan och välj **Entiteter** i fönstret Dynamiskt innehåll som visas.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Lägg till autentiseringsuppgifter för Textanalys i flödet.":::

I den nyligen skapade **Använd på varje 2-åtgärd** klickar du på **Lägg till en åtgärd**och lägger till en **villkorskontroll.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Lägg till autentiseringsuppgifter för Textanalys i flödet.":::

Klicka på den första textrutan i fönstret Villkor. Sök efter **entitetstyp** i fönstret Dynamiskt innehåll och markera det.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Lägg till autentiseringsuppgifter för Textanalys i flödet.":::

Kontrollera att den andra rutan är inställd **på är lika med**. Markera sedan den tredje rutan `var_person` och sök efter i fönstret Dynamiskt innehåll. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Lägg till autentiseringsuppgifter för Textanalys i flödet.":::

Skriv i Excel i villkoret **Om ja** och välj sedan Uppdatera **en rad**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Lägg till autentiseringsuppgifter för Textanalys i flödet.":::

Ange Excel-informationen och uppdatera fälten **Nyckelkolumn,** **Nyckelvärde** och **PersonNamn.** Detta kommer att lägga till namnet som identifieras av API:et i Excel-bladet. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Lägg till autentiseringsuppgifter för Textanalys i flödet.":::

## <a name="get-the-phone-number"></a>Hämta telefonnumret

Minimera åtgärden Använd på **varje 2** genom att klicka på namnet. Lägg sedan till ytterligare **en Tillämpa på varje** åtgärd, som tidigare. Det kommer att heta **Gäller för varje 3**. Markera textrutan och lägg till **entiteter** som utdata för den här åtgärden. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Lägg till autentiseringsuppgifter för Textanalys i flödet.":::

Lägg till en **villkorskontroll** inom **Använd på varje 3.** Det kommer att heta **Villkor 2**. Sök efter i den första textrutan och lägg till **entitetstyp** från fönstret Dynamiskt innehåll. Se till att mittrutan är inställd **på är lika med**. Ange sedan `var_phone`i den högra textrutan . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Lägg till autentiseringsuppgifter för Textanalys i flödet.":::

Lägg till en uppdatering **av en rad** i villkoret **Om ja.** Ange sedan informationen som vi gjorde ovan, för telefonnummerkolumnen i Excel-arket. Detta kommer att lägga till telefonnumret som identifieras av API:et i Excel-arket. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Lägg till autentiseringsuppgifter för Textanalys i flödet.":::


## <a name="get-the-plumbing-issues"></a>Få VVS frågor

Minimera **Använd på varje 3** genom att klicka på namnet. Skapa sedan en annan **Tillämpa på var och en** i den överordnade åtgärden. Markera textrutan och lägg till **entiteter** som utdata för den här åtgärden i fönstret Dynamiskt innehåll. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Lägg till autentiseringsuppgifter för Textanalys i flödet.":::


Därefter kommer flödet att kontrollera om ärendebeskrivningen från Excel-tabellraden innehåller ordet "VVS". Om ja, kommer det att lägga till "VVS" i IssueType kolumnen. Om inte, kommer vi att ange "andra".

Lägg till en **villkorskontroll** i åtgärden Använd på **varje 4.** Det kommer att heta **Villkor 3**. Sök efter och lägg till **Beskrivning** från Excel-filen i den första textrutan med hjälp av fönstret Dynamiskt innehåll. Se till att mitten rutan säger **innehåller**. Leta sedan upp och markera `var_plumbing`i den högra textrutan . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Lägg till autentiseringsuppgifter för Textanalys i flödet.":::


Klicka på **Lägg till en åtgärd**i villkoret Om **ja** och välj Uppdatera **en rad**. Ange sedan informationen som tidigare. Välj `var_plumbing`i kolumnen IssueType . Detta kommer att tillämpa en "VVS" etikett på raden.

Klicka på **Lägg till en åtgärd**i **inget** villkor och välj Uppdatera **en rad**. Ange sedan informationen som tidigare. Välj `var_other`i kolumnen IssueType . Detta kommer att tillämpa en "annan" etikett på raden.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Lägg till autentiseringsuppgifter för Textanalys i flödet.":::

## <a name="test-the-workflow"></a>Testa arbetsflödet

Klicka på **Spara**längst upp till höger på skärmen och sedan **Testa**. Välj **Jag ska utföra utlösaråtgärden**. Klicka på **Spara & Test**, Kör **flöde**och sedan **gjort**.

Excel-filen uppdateras i ditt OneDrive-konto. Det kommer att se ut som nedan.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="Det uppdaterade Excel-kalkylbladet.":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska fler lösningar](../text-analytics-user-scenarios.md)
