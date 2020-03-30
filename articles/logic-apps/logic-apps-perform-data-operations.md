---
title: Utföra åtgärder på data
description: Konvertera, hantera och ändra datautdata och format i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283945"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Utföra dataåtgärder i Azure Logic Apps

Den här artikeln visar hur du kan arbeta med data i logikapparna genom att lägga till åtgärder för dessa uppgifter med mera:

* Skapa tabeller från matriser.
* Skapa matriser från andra matriser baserat på ett villkor.
* Skapa användarvänliga token från JSON-objektegenskaper (JavaScript Object Notation) så att du enkelt kan använda dessa egenskaper i arbetsflödet.

Om du inte hittar den åtgärd du vill ha här kan du prova att bläddra bland de många olika [datamanipuleringsfunktioner](../logic-apps/workflow-definition-language-functions-reference.md) som Azure Logic Apps tillhandahåller.

Dessa tabeller sammanfattar de dataåtgärder som du kan använda och är ordnade baserat på de källdatatyper som operationerna arbetar med, men varje beskrivning visas i alfabetisk ordning.

**Matrisåtgärder** 

Dessa åtgärder hjälper dig att arbeta med data i matriser.

| Åtgärd | Beskrivning |
|--------|-------------|
| [**Skapa CSV-tabell**](#create-csv-table-action) | Skapa en CSV-tabell (Comma-separated value) från en matris. |
| [**Skapa HTML-tabell**](#create-html-table-action) | Skapa en HTML-tabell från en matris. |
| [**Filtermatris**](#filter-array-action) | Skapa en matrisundergrupp från en matris baserat på det angivna filtret eller villkoret. |
| [**Ansluta sig till**](#join-action) | Skapa en sträng från alla objekt i en matris och separera varje objekt med det angivna tecknet. |
| [**Välj**](#select-action) | Skapa en matris från de angivna egenskaperna för alla objekt i en annan matris. |
||| 

**JSON åtgärder**

Dessa åtgärder hjälper dig att arbeta med data i JavaScript Object Notation (JSON) format.

| Åtgärd | Beskrivning |
|--------|-------------|
| [**Komponera**](#compose-action) | Skapa ett meddelande, eller en sträng, från flera indata som kan ha olika datatyper. Du kan sedan använda den här strängen som en enda ingång, i stället för att upprepade gånger ange samma ingångar. Du kan till exempel skapa ett enda JSON-meddelande från olika indata. |
| [**Mer från Tolka JSON**](#parse-json-action) | Skapa användarvänliga datatokens för egenskaper i JSON-innehåll så att du lättare kan använda egenskaperna i logikapparna. |
|||

Information om hur du skapar mer komplexa JSON-omvandlingar finns i [Utföra avancerade JSON-omvandlingar med flytande mallar](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Logikappen där du behöver åtgärden för att arbeta med data

  Om du inte har tidigare i logikappar läser [Quickstart: Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* En [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som det första steget i logikappen 

  Dataåtgärder är endast tillgängliga som åtgärder, så innan du kan använda dessa åtgärder startar du logikappen med en utlösare och inkluderar alla andra åtgärder som krävs för att skapa de utdata du vill ha.

<a name="compose-action"></a>

## <a name="compose-action"></a>Komponera åtgärd

Om du vill skapa en enda utdata, till exempel ett JSON-objekt från flera indata, kan du använda åtgärden **Komponera.** Dina indata kan ha olika typer, till exempel heltal, booleans, matriser, JSON-objekt och alla andra inbyggda typer som Azure Logic Apps stöder, till exempel binära och XML. Du kan sedan använda utdata i åtgärder som följer efter åtgärden **Komponera.** Åtgärden **Skriv** kan också spara dig från att upprepade gånger ange samma indata medan du skapar logikappens arbetsflöde.

Du kan till exempel skapa ett JSON-meddelande från flera variabler, till exempel strängvariabler som lagrar personers förnamn och efternamn, och en heltalsvariabel som lagrar personers åldrar. Här accepterar åtgärden **Komponera** dessa indata:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

och skapar den här utdata:

`{"age":35,"fullName":"Owens,Sophie"}`

Om du vill prova ett exempel följer du dessa steg med hjälp av Logic App Designer. Om du föredrar att arbeta i kodvyredigeraren kan du kopiera exemplet **Skriv** och **initiera variabla** åtgärdsdefinitioner från den här artikeln till den egna logikappens underliggande arbetsflödesdefinition: [Exempel på dataåtgärdskod - Skriv skriv](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. Öppna logikappen i Logic App Designer i [Azure-portalen](https://portal.azure.com) eller Visual Studio.

   I det här exemplet används Azure-portalen och en logikapp med en **återkommande** utlösare och flera **initialisera variabelåtgärder.** Dessa åtgärder har ställts in för att skapa två strängvariabler och en heltalsvariabel. När du senare testar logikappen kan du köra appen manuellt utan att vänta på att utlösaren ska aktiveras.

   ![Starta exempellogikapp för åtgärden "Komponera"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. I logikappen där du vill skapa utdata följer du något av följande steg: 

   * Om du vill lägga till en åtgärd under det sista steget väljer du **Nytt steg**.

     ![Välj "Nytt steg" för åtgärden "Komponera"](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * Om du vill lägga till en åtgärd mellan stegen flyttar**+** du musen över kopplingspilen så att plustecknet ( ) visas. Markera plustecknet och välj sedan **Lägg till en åtgärd**.

1. Ange `compose` som filter i sökrutan under **Välj en åtgärd.** Välj åtgärden **Komponera** i åtgärdslistan.

   ![Välj åtgärden "Komponera"](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. Ange de indata du vill använda för att skapa utdata i rutan **Ingångar.**

   I det här exemplet visas listan med dynamiskt innehåll när du klickar i rutan **Ingångar** så att du kan välja de tidigare skapade variablerna:

   ![Välj indata som ska användas för åtgärden "Komponera"](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Här är det färdiga exemplet **Komponera** åtgärd: 

   ![Avslutat exempel för åtgärden "Komponera"](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet .

Mer information om den här åtgärden i den underliggande arbetsflödesdefinitionen finns i [åtgärden Komponera](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action).

### <a name="test-your-logic-app"></a>Testa logikappen

Om du vill bekräfta om åtgärden **Komponera** skapar förväntade resultat skickar du ett meddelande som innehåller utdata från åtgärden **Komponera.**

1. Lägg till en åtgärd som kan skicka resultaten från åtgärden **Komponera** i logikappen.

1. I den åtgärden klickar du var som helst där du vill att resultaten ska visas. När listan med dynamiskt innehåll öppnas väljer du **Utdata**under åtgärden **Komponera** .

   I det här exemplet används åtgärden **Skicka ett e-postmeddelande** och **indatafälten** i e-postmeddelandets brödtext och ämne:

   !["Utdata" fält för "Komponera" åtgärd](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. Kör nu logikappen manuellt. Välj **Kör**i designerverktygsfältet .

   Baserat på e-postkontakten du använde, här är de resultat du får:

   ![E-post med åtgärdsresultat för "Skriv"](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Skapa csv-tabellåtgärd

Om du vill skapa en CSV-tabell (kommaavgränsad värde) som har egenskaper och värden från JSON-objekt (JavaScript Object Notation) i en matris använder du åtgärden **Skapa CSV-tabell.** Du kan sedan använda den resulterande tabellen i åtgärder som följer åtgärden **Skapa CSV-tabell.**

Om du föredrar att arbeta i kodvyredigeraren kan du kopiera exemplet **Skapa CSV-tabell** och **initiera definitioner av variabelåtgärd** från den här artikeln till din egen logikapps underliggande arbetsflödesdefinition: [Exempel på dataåtgärdskod - Skapa CSV-tabell](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. Öppna logikappen i Logic App Designer i [Azure-portalen](https://portal.azure.com) eller Visual Studio.

   I det här exemplet används Azure-portalen och en logikapp med en **återkommande** utlösare och en **variabel initieringsåtgärd.** Åtgärden har ställts in för att skapa en variabel vars ursprungliga värde är en matris som har vissa egenskaper och värden i JSON-format. När du senare testar logikappen kan du köra appen manuellt utan att vänta på att utlösaren ska aktiveras.

   ![Starta exempellogikapp för åtgärden Skapa CSV-tabell](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. I logikappen där du vill skapa CSV-tabellen följer du något av följande steg: 

   * Om du vill lägga till en åtgärd under det sista steget väljer du **Nytt steg**.

     ![Välj åtgärden "Nytt steg" för åtgärden "Skapa CSV-tabell"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Om du vill lägga till en åtgärd mellan stegen flyttar**+** du musen över kopplingspilen så att plustecknet ( ) visas. Markera plustecknet och välj sedan **Lägg till en åtgärd**.

1. Ange `create csv table` som filter i sökrutan under **Välj en åtgärd.** Välj åtgärden **Skapa CSV-tabell** i listan Åtgärder.

   ![Välj åtgärden Skapa CSV-tabell](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. Ange den matris eller det uttryck som du vill använda för att skapa tabellen i rutan **Från.**

   I det här exemplet visas listan med dynamiskt innehåll när du klickar i rutan **Från** så att du kan välja den tidigare skapade variabeln:

   ![Välj matrisutdata för att skapa CSV-tabell](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > Om du vill skapa användarvänliga token för egenskaperna i JSON-objekt så att du kan markera dessa egenskaper som indata använder [du Parse JSON](#parse-json-action) innan du anropar åtgärden **Skapa CSV-tabell.**

   Här är det färdiga exemplet **Skapa CSV-tabellåtgärd:** 

   ![Föregående exempel för åtgärden "Skapa CSV-tabell"](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet .

### <a name="customize-table-format"></a>Anpassa tabellformat

Egenskapen **Kolumner** är som standard inställd på att automatiskt skapa tabellkolumnerna baserat på matrisobjekten. Så här anger du anpassade rubriker och värden:

1. Öppna listan **Kolumner** och välj **Anpassad**.

1. Ange den anpassade rubriktext som ska användas i stället i egenskapen **Rubrik.**

1. Ange det anpassade värde som ska användas i stället i egenskapen **Value.**

Om du vill returnera värden från matrisen kan du använda [ `item()` funktionen](../logic-apps/workflow-definition-language-functions-reference.md#item) med åtgärden **Skapa CSV-tabell.** I `For_each` en slinga kan du använda [ `items()` funktionen](../logic-apps/workflow-definition-language-functions-reference.md#items).

Anta till exempel att du vill ha tabellkolumner som bara har egenskapsvärdena och inte egenskapsnamnen från en matris. Om du bara vill returnera dessa värden följer du dessa steg för att arbeta i designervyn eller i kodvyn. Här är resultatet som det här exemplet returnerar:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Arbeta i designervy

Håll kolumnen **Rubrik** tom i åtgärden. På varje rad i kolumnen **Värde** avrefererar du varje matrisegenskap som du vill använda. Varje rad under **Värde** returnerar alla värden för den angivna matrisegenskapen och blir en kolumn i tabellen.

1. Klicka i redigeringsrutan under **Värde**på varje rad som du vill använda så att listan med dynamiskt innehåll visas.

1. Välj **Uttryck**i listan med dynamiskt innehåll .

1. I uttrycksredigeraren anger du det här uttrycket som anger önskat matrisegenskapsvärde och väljer **OK**.

   `item()?['<array-property-name>']`

   Ett exempel:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Avreference "Beskrivning" för "Skapa CSV-tabell"](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. Upprepa föregående steg för varje matrisegenskap som du vill använda. När du är klar ser åtgärden ut så här:

   !["item()" i "Skapa CSV-tabell"](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. Om du vill lösa uttryck i mer beskrivande versioner växlar du till kodvyn och tillbaka till designervyn och öppnar sedan den komprimerade åtgärden igen:

   Åtgärden **Skapa CSV-tabell** visas nu så här:

   !["Skapa CSV-tabell" - lösta uttryck, inga rubriker](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>Arbeta i kodvyn

I åtgärdens JSON-definition anger `columns` du egenskapen `header` till en tom sträng i åtgärdens JSON-definition. För `value` varje egenskap avrefererar du varje matrisegenskap som du vill använda.

1. Välj **Kodvy**i designerverktygsfältet .

1. I kodredigeraren i `columns` åtgärdens matris `header` lägger `value` du till den tomma egenskapen och det här uttrycket för varje kolumn med matrisvärden som du vill ha:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Ett exempel:

   ```json
   "Create_CSV_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Växla tillbaka till designervyn och öppna den komprimerade åtgärden igen.

   **Åtgärden Skapa CSV-tabell** visas nu som det här exemplet och uttrycken har lösts till mer beskrivande versioner:

   !["Skapa CSV-tabell" - lösta uttryck och inga rubriker](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

Mer information om den här åtgärden i den underliggande arbetsflödesdefinitionen finns i [tabellåtgärden](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testa logikappen

Om du vill bekräfta om åtgärden **Skapa CSV-tabell** skapar förväntade resultat skickar du ett meddelande som innehåller utdata från åtgärden **Skapa CSV-tabell.**

1. Lägg till en åtgärd som kan skicka resultaten från åtgärden **Skapa CSV** i logikappen.

1. I den åtgärden klickar du var som helst där du vill att resultaten ska visas. När listan med dynamiskt innehåll öppnas väljer du **Utdata**under åtgärden **Skapa CSV-tabell.** 

   I det här exemplet används åtgärden **Skicka ett e-postmeddelande** till Office 365 Outlook och fältet **Utdata** i e-postmeddelandets brödtext:

   !["Utdata" fält för åtgärden "Skapa CSV-tabell"](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. Kör nu logikappen manuellt. Välj **Kör**i designerverktygsfältet .

   Baserat på e-postkontakten du använde, här är de resultat du får:

   ![E-post med åtgärdsresultaten "Skapa CSV-tabell"](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Åtgärden Skapa HTML-tabell

Om du vill skapa en HTML-tabell med egenskaper och värden från JSON-objekt (JavaScript Object Notation) i en matris använder du åtgärden **Skapa HTML-tabell.** Du kan sedan använda den resulterande tabellen i åtgärder som följer åtgärden **Skapa HTML-tabell.**

Om du föredrar att arbeta i kodvyredigeraren kan du kopiera exemplet **Skapa HTML-tabell** och **initiera definitioner** av variabelåtgärder från den här artikeln till din egen logikapps underliggande arbetsflödesdefinition: [Exempel på dataåtgärdskod - Skapa HTML-tabell](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. Öppna logikappen i Logic App Designer i [Azure-portalen](https://portal.azure.com) eller Visual Studio.

   I det här exemplet används Azure-portalen och en logikapp med en **återkommande** utlösare och en **variabel initieringsåtgärd.** Åtgärden har ställts in för att skapa en variabel vars ursprungliga värde är en matris som har vissa egenskaper och värden i JSON-format. När du senare testar logikappen kan du köra appen manuellt utan att vänta på att utlösaren ska aktiveras.

   ![Starta exempellogikapp för "Skapa HTML-tabell"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. I logikappen där du vill skapa en HTML-tabell följer du något av följande:

   * Om du vill lägga till en åtgärd under det sista steget väljer du **Nytt steg**.

     ![Välj åtgärden "Nytt steg" för åtgärden "Skapa HTML-tabell"](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Om du vill lägga till en åtgärd mellan stegen flyttar**+** du musen över kopplingspilen så att plustecknet ( ) visas. Markera plustecknet och välj sedan **Lägg till en åtgärd**.

1. Ange `create html table` som filter i sökrutan under **Välj en åtgärd.** Välj åtgärden **Skapa HTML-tabell** i listan Åtgärder.

   ![Välj åtgärden Skapa HTML-tabell](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. Ange den matris eller det uttryck som du vill använda för att skapa tabellen i rutan **Från.**

   I det här exemplet visas listan med dynamiskt innehåll när du klickar i rutan **Från** så att du kan välja den tidigare skapade variabeln:

   ![Välj matrisutdata för att skapa HTML-tabell](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > Om du vill skapa användarvänliga token för egenskaperna i JSON-objekt så att du kan markera dessa egenskaper som indata använder du [Parse JSON](#parse-json-action) innan du anropar åtgärden **Skapa HTML-tabell.**

   Här är det färdiga exemplet **Skapa HTML-tabellåtgärd:**

   ![Exempel på färdigt för "Skapa HTML-tabell"](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet .

### <a name="customize-table-format"></a>Anpassa tabellformat

Egenskapen **Kolumner** är som standard inställd på att automatiskt skapa tabellkolumnerna baserat på matrisobjekten. Så här anger du anpassade rubriker och värden:

1. Öppna listan **Kolumner** och välj **Anpassad**.

1. Ange den anpassade rubriktext som ska användas i stället i egenskapen **Rubrik.**

1. Ange det anpassade värde som ska användas i stället i egenskapen **Value.**

Om du vill returnera värden från matrisen kan du använda [ `item()` funktionen](../logic-apps/workflow-definition-language-functions-reference.md#item) med åtgärden **Skapa HTML-tabell.** I `For_each` en slinga kan du använda [ `items()` funktionen](../logic-apps/workflow-definition-language-functions-reference.md#items).

Anta till exempel att du vill ha tabellkolumner som bara har egenskapsvärdena och inte egenskapsnamnen från en matris. Om du bara vill returnera dessa värden följer du dessa steg för att arbeta i designervyn eller i kodvyn. Här är resultatet som det här exemplet returnerar:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Arbeta i designervy

Håll kolumnen **Rubrik** tom i åtgärden. På varje rad i kolumnen **Värde** avrefererar du varje matrisegenskap som du vill använda. Varje rad under **Värde** returnerar alla värden för den angivna egenskapen och blir en kolumn i tabellen.

1. Klicka i redigeringsrutan under **Värde**på varje rad som du vill använda så att listan med dynamiskt innehåll visas.

1. Välj **Uttryck**i listan med dynamiskt innehåll .

1. I uttrycksredigeraren anger du det här uttrycket som anger önskat matrisegenskapsvärde och väljer **OK**.

   `item()?['<array-property-name>']`

   Ett exempel:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Egenskapen Dereference i åtgärden "Skapa HTML-tabell"](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. Upprepa föregående steg för varje matrisegenskap som du vill använda. När du är klar ser åtgärden ut så här:

   !["item()" i "Skapa HTML-tabell"](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. Om du vill lösa uttryck i mer beskrivande versioner växlar du till kodvyn och tillbaka till designervyn och öppnar sedan den komprimerade åtgärden igen:

   Åtgärden **Skapa HTML-tabell** visas nu så här:

   !["Skapa HTML-tabell" - lösta uttryck, inga rubriker](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>Arbeta i kodvyn

I åtgärdens JSON-definition anger `columns` du egenskapen `header` till en tom sträng i åtgärdens JSON-definition. För `value` varje egenskap avrefererar du varje matrisegenskap som du vill använda.

1. Välj **Kodvy**i designerverktygsfältet .

1. I kodredigeraren i `columns` åtgärdens matris `header` lägger `value` du till den tomma egenskapen och det här uttrycket för varje kolumn med matrisvärden som du vill ha:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Ett exempel:

   ```json
   "Create_HTML_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Växla tillbaka till designervyn och öppna den komprimerade åtgärden igen.

   Åtgärden **Skapa HTML-tabell** visas nu som det här exemplet och uttrycken har lösts till mer beskrivande versioner:

   !["Skapa HTML-tabell" - lösta uttryck och inga rubriker](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

Mer information om den här åtgärden i den underliggande arbetsflödesdefinitionen finns i [tabellåtgärden](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testa logikappen

Om du vill bekräfta om åtgärden **Skapa HTML-tabell** skapar förväntade resultat skickar du ett meddelande som innehåller utdata från åtgärden **Skapa HTML-tabell.**

1. Lägg till en åtgärd som kan skicka resultaten från åtgärden **Skapa HTML-tabell** i logikappen.

1. I den åtgärden klickar du var som helst där du vill att resultaten ska visas. När listan med dynamiskt innehåll öppnas väljer du **Utdata**under åtgärden **Skapa HTML-tabell** . 

   I det här exemplet används åtgärden **Skicka ett e-postmeddelande** till Office 365 Outlook och fältet **Utdata** i e-postmeddelandets brödtext:

   ![Utdatafält för "Skapa HTML-tabell"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > När du inkluderar HTML-tabellutdata i en e-poståtgärd ska du se till att egenskapen **Är HTML** anges till **Ja** i avancerade alternativ för e-poståtgärden. På så sätt formaterar e-poståtgärden HTML-tabellen på rätt sätt.

1. Kör nu logikappen manuellt. Välj **Kör**i designerverktygsfältet .

   Baserat på e-postkontakten du använde, här är de resultat du får:

   ![E-post med resultat i "Skapa HTML-tabell"](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Åtgärden Filtrera matris

Om du vill skapa en mindre matris som har objekt, som uppfyller specifika villkor, använder du åtgärden **Filtermatris** från en befintlig matris. Du kan sedan använda den filtrerade matrisen i åtgärder som följer efter åtgärden **Filtermatris.**

> [!NOTE]
> All filtertext som du använder i ditt tillstånd är skiftlägeskänslig. Den här åtgärden kan inte heller ändra format eller komponenter för objekt i matrisen. 
> 
> För att åtgärder ska kunna använda matrisutdata från åtgärden **Filtermatris** måste antingen dessa åtgärder acceptera matriser som indata eller så måste du omvandla utdatamatrisen till ett annat kompatibelt format.
> 
> Om du anropar en HTTP-slutpunkt och får ett JSON-svar använder du åtgärden **Parse JSON** för att bearbeta JSON-svaret. 
> Annars kan åtgärden **Filtermatris** bara läsa svarstexten och inte strukturen för JSON-nyttolasten.

Om du föredrar att arbeta i kodvyredigeraren kan du kopiera exemplet **Filtermatris** och **initiera variableactiondefinitioner** från den här artikeln till din egen logikapps underliggande arbetsflödesdefinition: [Exempel på dataåtgärdskod - filtermatris](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. Öppna logikappen i Logic App Designer i [Azure-portalen](https://portal.azure.com) eller Visual Studio.

   I det här exemplet används Azure-portalen och en logikapp med en **återkommande** utlösare och en **variabel initieringsåtgärd.** Åtgärden har ställts in för att skapa en variabel vars ursprungliga värde är en matris som har några exempel heltal. När du senare testar logikappen kan du köra appen manuellt utan att vänta på att utlösaren ska aktiveras.

   > [!NOTE]
   > Även om det här exemplet använder en enkel heltalsmatris är den här åtgärden särskilt användbar för JSON-objektmatriser där du kan filtrera baserat på objektens egenskaper och värden.

   ![Starta exempellogikapp för åtgärden Filtermatris](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. I logikappen där du vill skapa den filtrerade matrisen följer du något av följande: 

   * Om du vill lägga till en åtgärd under det sista steget väljer du **Nytt steg**.

     ![Välj "Nytt steg" för åtgärden "Filtermatris"](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Om du vill lägga till en åtgärd mellan stegen flyttar**+** du musen över kopplingspilen så att plustecknet ( ) visas. Markera plustecknet och välj sedan **Lägg till en åtgärd**.

1. Ange som filter `filter array` i sökrutan. Välj åtgärden **Filtermatris** i åtgärdslistan.

   ![Välj åtgärd "Filtermatris"](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. Ange den matris eller det uttryck som du vill filtrera i rutan **Från.**

   I det här exemplet visas listan med dynamiskt innehåll när du klickar i rutan **Från** så att du kan välja den tidigare skapade variabeln:

   ![Välj matrisutdata för att skapa filtrerad matris](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. För villkoret anger du vilka matrisobjekt som ska jämföras, väljer jämförelseoperatorn och anger jämförelsevärdet.

   I det `item()` här exemplet används funktionen för att komma åt varje objekt i matrisen medan åtgärden **Filtermaterray** söker efter matrisobjekt vars värde är större än ett:

   ![Exempel på färdigt för åtgärden "Filtermatris"](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet .

Mer information om den här åtgärden i den underliggande arbetsflödesdefinitionen finns i [Frågeåtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Testa logikappen

Om du vill bekräfta om **filtermatrisåtgärden** skapar de förväntade resultaten skickar du ett meddelande som innehåller utdata från åtgärden **Filtermatris.**

1. Lägg till en åtgärd som kan skicka resultaten från åtgärden **Filtermatris** i logikappen.

1. I den åtgärden klickar du var som helst där du vill att resultaten ska visas. När listan med dynamiskt innehåll öppnas väljer du **Uttryck**. Om du vill hämta matrisutdata från åtgärden **Filtermatris** anger du det här uttrycket som innehåller namnet **på filtermatrisen:**

   `@actionBody('Filter_array')`

   I det här exemplet används office 365 Outlook **Skicka en e-poståtgärd** och de utdata från **uttrycket actionBody('Filter_array')** i e-postmeddelandets brödtext:

   ![Åtgärdsutdata från åtgärden Filtermatris](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. Kör nu logikappen manuellt. Välj **Kör**i designerverktygsfältet .

   Baserat på e-postkontakten du använde, här är de resultat du får:

   ![E-post med åtgärdsresultat för "Filtermatris"](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Delta i åtgärd

Om du vill skapa en sträng som har alla objekt från en matris och avgränsar objekten med ett visst avgränsare använder du åtgärden **Koppla.** Du kan sedan använda strängen i åtgärder som följer efter åtgärden **Anslut.**

Om du föredrar att arbeta i kodvyredigeraren kan du kopiera exemplet **Join** och **Initiera variabla** åtgärdsdefinitioner från den här artikeln till din egen logikapps underliggande arbetsflödesdefinition: [Exempel på dataåtgärdskod - Gå med](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. Öppna logikappen i Logic App Designer i [Azure-portalen](https://portal.azure.com) eller Visual Studio.

   I det här exemplet används Azure-portalen och en logikapp med en **återkommande** utlösare och en **variabel initieringsåtgärd.** Den här åtgärden har ställts in för att skapa en variabel vars ursprungliga värde är en matris som har några exempel heltal. När du testar logikappen senare kan du köra appen manuellt utan att vänta på att utlösaren ska aktiveras.

   ![Starta exempellogikapp för åtgärden "Gå med"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. I logikappen där du vill skapa strängen från en matris följer du något av följande steg:

   * Om du vill lägga till en åtgärd under det sista steget väljer du **Nytt steg**.

     ![Avmarkera "Nytt steg" för åtgärden "Join"](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * Om du vill lägga till en åtgärd mellan stegen flyttar**+** du musen över kopplingspilen så att plustecknet ( ) visas. Markera plustecknet och välj sedan **Lägg till en åtgärd**.

1. Ange som filter `join` i sökrutan. Välj den här åtgärden i åtgärdslistan: **Gå med i**

   ![Välj åtgärden "Gå med"](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. Ange den matris som har de objekt som du vill koppla till som en sträng i rutan **Från.**

   I det här exemplet när du klickar i rutan **Från** kan du välja den lista över dynamiskt innehåll som visas så att du kan välja den tidigare skapade variabeln:  

   ![Välj matrisutdata för att skapa strängen](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. I rutan **Anslut med** anger du önskat tecken för att separera varje matrisobjekt. 

   I det här exemplet används ett kolon (:) som separator.

   ![Ange avgränsare](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet .

Mer information om den här åtgärden i den underliggande arbetsflödesdefinitionen finns i [åtgärden Anslut](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Testa logikappen

Om du vill bekräfta om åtgärden **Anslut** skapar de förväntade resultaten skickar du ett meddelande som innehåller utdata från åtgärden **Anslut.**

1. Lägg till en åtgärd som kan skicka resultaten från åtgärden **Anslut** i logikappen.

1. I den åtgärden klickar du var som helst där du vill att resultaten ska visas. När listan med dynamiskt innehåll öppnas väljer du **Utdata**under åtgärden **Koppla** . 

   I det här exemplet används åtgärden **Skicka ett e-postmeddelande** till Office 365 Outlook och fältet **Utdata** i e-postmeddelandets brödtext:

   !["Utdata" fält för åtgärden "Gå med"](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. Kör nu logikappen manuellt. Välj **Kör**i designerverktygsfältet .

   Baserat på e-postkontakten du använde, här är de resultat du får:

   ![E-post med åtgärdsresultat för "Gå med"](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Tolka JSON-åtgärd

Om du vill referera till eller komma åt egenskaper i JSON-innehåll (JavaScript Object Notation) kan du skapa användarvänliga fält eller token för dessa egenskaper med hjälp av åtgärden **Parse JSON.** På så sätt kan du välja dessa egenskaper i listan över dynamiskt innehåll när du anger indata för logikappen. För den här åtgärden kan du antingen ange ett JSON-schema eller generera ett JSON-schema från ditt exempel på JSON-innehåll eller nyttolast.

Om du föredrar att arbeta i kodvyredigeraren kan du kopiera exemplet **Parse JSON** och **initiera variabla** åtgärdsdefinitioner från den här artikeln till din egen logikapps underliggande arbetsflödesdefinition: [Exempel på dataåtgärdskod - Parse JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. Öppna logikappen i Logic App Designer i [Azure-portalen](https://portal.azure.com) eller Visual Studio.

   I det här exemplet används Azure-portalen och en logikapp med en **återkommande** utlösare och en **variabel initieringsåtgärd.** Åtgärden ställs in för att skapa en variabel vars ursprungliga värde är ett JSON-objekt som har egenskaper och värden. När du senare testar logikappen kan du köra appen manuellt utan att vänta på att utlösaren ska aktiveras.

   ![Starta exempellogikapp för åtgärden "Parse JSON"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. I logikappen där du vill tolka JSON-innehållet följer du något av följande steg:

   * Om du vill lägga till en åtgärd under det sista steget väljer du **Nytt steg**.

     ![Välj "Nytt steg" för åtgärden "Parse JSON"](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Om du vill lägga till en åtgärd mellan stegen flyttar**+** du musen över kopplingspilen så att plustecknet ( ) visas. Markera plustecknet och välj sedan **Lägg till en åtgärd**.

1. Ange som filter `parse json` i sökrutan. Välj åtgärden **Parse JSON** i åtgärdslistan.

   ![Välj åtgärden "Parse JSON"](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. Ange det JSON-innehåll som du vill tolka i rutan **Innehåll.**

   I det här exemplet visas listan med dynamiskt innehåll när du klickar i rutan **Innehåll** så att du kan välja den tidigare skapade variabeln:

   ![Välj JSON-objekt för parsa JSON-åtgärd](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. Ange JSON-schemat som beskriver det JSON-innehåll som du tolkar.

   I det här exemplet är här JSON-schemat:

   ![Ange JSON-schema för det JSON-objekt som du vill tolka](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Om du inte har schemat kan du generera schemat från JSON-innehållet eller *nyttolasten,* du tolkar. 
   
   1. I åtgärden **Parse JSON** väljer du **Använd exempelnyttolaster för att generera schema**.

   1. Under **Ange eller klistra in ett exempel på JSON-nyttolast**anger du JSON-innehållet och väljer sedan **Klar**.

      ![Ange JSON-innehållet för att generera schemat](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet .

Mer information om den här åtgärden i den underliggande arbetsflödesdefinitionen finns i [Åtgärd Parse JSON](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testa logikappen

Om du vill bekräfta om **åtgärden Parse JSON** skapar förväntade resultat skickar du ett meddelande som innehåller utdata från åtgärden **Parse JSON.**

1. Lägg till en åtgärd som kan skicka resultaten från **parse JSON-åtgärden** i logikappen.

1. I den åtgärden klickar du var som helst där du vill att resultaten ska visas. När listan med dynamiskt innehåll öppnas under åtgärden **Parse JSON** kan du nu välja egenskaper från det tolkade JSON-innehållet.

   I det här exemplet används office 365 Outlook **Skicka en e-poståtgärd** och fälten **Förnamn,** **Efternamn**och **E-post** i e-postmeddelandets brödtext:

   ![JSON-egenskaper i åtgärden "Skicka ett e-postmeddelande"](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Här är den färdiga e-åtgärden:

   ![Exempel på e-poståtgärd har slutförts](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. Kör nu logikappen manuellt. Välj **Kör**i designerverktygsfältet . 

   Baserat på e-postkontakten du använde, här är de resultat du får:

   ![E-post med åtgärdsresultat för "Parse JSON"](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Välj åtgärd

Om du vill skapa en matris med JSON-objekt byggda av värden i en befintlig matris använder du åtgärden **Välj.** Du kan till exempel skapa ett JSON-objekt för varje värde i en heltalsmatris genom att ange de egenskaper som varje JSON-objekt måste ha och hur värdena i källmatrisen ska mappas till dessa egenskaper. Och även om du kan ändra komponenterna i dessa JSON-objekt, har utdatamatrisen alltid samma antal objekt som källmatrisen.

> [!NOTE]
> För att åtgärder ska kunna använda matrisutdata från åtgärden **Välj** måste antingen dessa åtgärder acceptera matriser som indata eller så måste du omvandla utdatamatrisen till ett annat kompatibelt format. 

Om du föredrar att arbeta i kodvyredigeraren kan du kopiera exemplet **Välj** och **initiera variabla** åtgärdsdefinitioner från den här artikeln till din egen logikapps underliggande arbetsflödesdefinition: [Exempel på dataoperationskod - Välj Välj](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. Öppna logikappen i Logic App Designer i [Azure-portalen](https://portal.azure.com) eller Visual Studio.

   I det här exemplet används Azure-portalen och en logikapp med en **återkommande** utlösare och en **variabel initieringsåtgärd.** Åtgärden har ställts in för att skapa en variabel vars ursprungliga värde är en matris som har några exempel heltal. När du senare testar logikappen kan du köra appen manuellt utan att vänta på att utlösaren ska aktiveras.

   ![Starta exempellogikapp för åtgärden "Välj"](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. I logikappen där du vill skapa matrisen följer du något av följande steg: 

   * Om du vill lägga till en åtgärd under det sista steget väljer du **Nytt steg**.

     ![Välj "Nytt steg" för åtgärden "Välj"](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * Om du vill lägga till en åtgärd mellan stegen flyttar**+** du musen över kopplingspilen så att plustecknet ( ) visas. Markera plustecknet och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd**väljer du **Inbyggd**. Ange som filter `select` i sökrutan. Välj åtgärden Välj åtgärden **Välj** i åtgärdslistan.

   ![Markera åtgärden "Välj"](./media/logic-apps-perform-data-operations/select-select-action.png)

1. Ange den källmatris du vill använda i rutan **Från.**

   I det här exemplet visas listan med dynamiskt innehåll när du klickar i rutan **Från** så att du kan välja den tidigare skapade variabeln:

   ![Välj källmatris för välj åtgärd](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. Ange **Map** det egenskapsnamn som du vill tilldela varje värde i källmatrisen i rutan Mappas. I den högra kolumnen anger du ett uttryck som representerar det värde som du vill tilldela egenskapen.

   I det här exemplet anges "Product_ID" som egenskapsnamn för att tilldela `item()` varje värde i heltalsmatrisen med hjälp av funktionen i ett uttryck som kommer åt varje matrisobjekt. 

   ![Ange JSON-objektegenskap och -värden som ska skapas](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Här är den färdiga åtgärden:

   ![Exempel på färdigt för åtgärden "Välj"](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet .

Mer information om den här åtgärden i den underliggande arbetsflödesdefinitionen finns i [Välj åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testa logikappen

Om du vill bekräfta om åtgärden **Välj** skapar förväntade resultat skickar du ett meddelande som innehåller utdata från åtgärden **Välj.**

1. Lägg till en åtgärd som kan skicka resultaten från åtgärden **Välj** i logikappen.

1. I den åtgärden klickar du var som helst där du vill att resultaten ska visas. När listan med dynamiskt innehåll öppnas väljer du **Uttryck**. Om du vill hämta matrisutdata från åtgärden **Välj** anger du det här uttrycket som innehåller namnet **Välj:**

   `@actionBody('Select')`

   I det här exemplet används åtgärden **Skicka ett e-postmeddelande** till `@actionBody('Select')` Office 365 Outlook och de utdata som skickas från uttrycket i e-postmeddelandets brödtext:

   ![Åtgärdsutdata från åtgärden "Välj"](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. Kör nu logikappen manuellt. Välj **Kör**i designerverktygsfältet .

   Baserat på e-postkontakten du använde, här är de resultat du får:

   ![E-post med åtgärdsresultat för "Välj"](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Logic Apps-kopplingar](../connectors/apis-list.md)
