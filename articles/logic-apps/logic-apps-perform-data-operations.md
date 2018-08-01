---
title: Utföra åtgärder på data – Azure Logic Apps | Microsoft Docs
description: Konvertera, hantera och manipulera data utdata och format i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 7e62986569888ebbcd9f17b4eb4cfb2c70411d4a
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392084"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Utföra åtgärder i Azure Logic Apps

Den här artikeln visar hur du kan arbeta med data i dina logic apps genom att lägga till åtgärder för dessa uppgifter och mer:

* Skapa tabeller från matriser.
* Skapa matriser från andra matriser baserat på ett villkor.
* Skapa användarvänliga token från JavaScript Object Notation (JSON) objektegenskaper så att du lätt kan använda dessa egenskaper i arbetsflödet.

Om du inte hittar den åtgärd som du vill ha här, försök att bläddra i många olika [datahanteringsfunktioner](../logic-apps/workflow-definition-language-functions-reference.md) som Logic Apps tillhandahåller. 

Dessa tabeller sammanfattar data åtgärder du kan använda och ordnas baserat på käll-datatyper som de fungerar på, men varje beskrivningen visas i alfabetisk ordning.

**Matris åtgärder** 

De här åtgärderna hjälper dig att arbeta med data i matriser.

| Åtgärd | Beskrivning | 
|--------|-------------| 
| [**Skapa CSV-tabell**](#create-csv-table-action) | Skapa en fil med kommaavgränsade värden (CSV) från en matris. | 
| [**Skapa HTML-tabell**](#create-html-table-action) | Skapa en HTML-tabell från en matris. | 
| [**Filtrera matris**](#filter-array-action) | Skapa en matris delmängd från en matris som baseras på det angivna filtret eller ett villkor. | 
| [**Anslut till**](#join-action) | Skapa en sträng från alla objekt i en matris och avgränsa varje objekt med det angivna tecknet. | 
| [**Välj**](#select-action) | Skapa en matris från de angivna egenskaperna för alla objekt i en annan matris. | 
||| 

**JSON-åtgärder**

De här åtgärderna hjälper dig att arbeta med data i JavaScript Object Notation (JSON)-format.

| Åtgärd | Beskrivning | 
|--------|-------------| 
| [**Compose**](#compose-action) | Skapa ett meddelande eller en sträng, från flera inmatningar som kan ha olika datatyper. Du kan sedan använda den här strängen som en enda indata i stället för flera gånger att ange samma indata. Du kan till exempel skapa ett enda JSON-meddelande från olika indata. | 
| [**Parsa JSON**](#parse-json-action) | Skapa användarvänliga data-token för egenskaper i JSON innehåll så att du kan enkelt använda egenskaperna i dina logic apps. | 
||| 

Om du vill skapa mer komplexa JSON-transformationer [utför avancerad JSON-transformationer med en flytande mallar](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa exemplen i den här artikeln behöver du följande objekt:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration än, <a href="https://azure.microsoft.com/free/" target="_blank">registrera dig för ett kostnadsfritt konto</a>.

* Logikappen där du behöver igen för att arbeta med data 

  Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Snabbstart: skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* En [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts) som det första steget i din logikapp 

  Dataåtgärder finns endast som åtgärder, så innan du kan använda de här åtgärderna börja din logikapp med en utlösare och innehålla andra åtgärder som krävs för att skapa utdata som du vill ha.

<a name="compose-action"></a>

## <a name="compose-action"></a>Åtgärden Skriv

Du kan använda för att konstruera ett enda utflöde, till exempel ett JSON-objekt från flera inmatningar den **dataåtgärder - Skriv** åtgärd. Dina indata kan ha olika typer som heltal, booleska värden, matriser, JSON-objekt och andra interna skriver som stöd för Azure Logic Apps, till exempel binära och XML. Du kan sedan använda utdata i åtgärder som följer efter den **Compose** åtgärd. Den **Compose** åtgärd kan också spara du upprepade gånger använder samma indata när du skapar logikappens arbetsflöde. 

Exempelvis kan du skapa en JSON-meddelande från flera variabler, till exempel strängvariabler som lagrar människors förnamn och efternamn och en heltalsvariabel som lagrar människors åldrar. Här är den **Compose** åtgärd accepterar dessa indata:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

och skapar dessa utdata:

`{"age":35,"fullName":"Owens,Sophie"}`

Följ dessa steg om du vill prova ett exempel med hjälp av Logic App Designer. Eller, om du vill arbeta i kodredigeraren vy kan du kopiera exemplet **Compose** och **initieras variabeln** åtgärd definitionerna från den här artikeln i din egen logikapp underliggande arbetsflöde definition: [Data åtgärden kodexempel - Compose](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> eller Visual Studio, öppna logikappen i Logic App Designer. 

   Det här exemplet används Azure portal och en logikapp med en **upprepning** utlösare och flera **initieras variabeln** åtgärder. 
   De här åtgärderna ställs in för att skapa två strängvariabler och en heltalsvariabel. När du testar din logikapp senare, kan du manuellt köra din app utan att behöva vänta utlösare utlöses.

   ![Från exempellogikappen](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

2. I logikappen där du vill skapa utdata, gör du något av följande: 

   * Om du vill lägga till en åtgärd under det sista steget, Välj **nytt steg** > **Lägg till en åtgärd**.

     ![Lägg till åtgärd](./media/logic-apps-perform-data-operations/add-compose-action.png)

   * Om du vill lägga till en åtgärd mellan stegen, musen över den anslutande pilen så visas på plustecknet (+). 
   Välj på plustecknet och välj sedan **Lägg till en åtgärd**.

3. I sökrutan anger du ”skapa” som filter. Välj den här åtgärden från åtgärdslistan över: **Compose**

   ![Välj åtgärden ”Skriv”](./media/logic-apps-perform-data-operations/select-compose-action.png)

4. I den **indata** ange indata som du vill använda för att skapa utdata. 

   I det här exemplet när du klickar i den **indata** den dynamiska innehållslistan visas rutan, så du kan välja de tidigare skapade variablerna:

   ![Välj indata till att skapa](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Här är det klar exemplet **Compose** åtgärd: 

   ![Åtgärden slutfördes ”Skriv”](./media/logic-apps-perform-data-operations/finished-compose-action.png)

5. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

Mer information om den här åtgärden i dina underliggande arbetsflödesdefinitionen finns i den [åtgärden Skriv](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action). 

### <a name="test-your-logic-app"></a>Testa din logikapp

Bekräfta om de **Compose** åtgärden skapar de förväntade resultaten, skicka dig själv ett meddelande som innehåller utdata från den **Compose** åtgärd.

1. I din logikapp, lägger du till en åtgärd som kan skicka resultaten från den **Compose** åtgärd.

2. Klicka på var du vill att resultatet ska visas i den åtgärden. När den dynamiska innehållslistan öppnas, under den **Compose** väljer **utdata**. 

   Det här exemplet används den **Office 365 Outlook – skicka ett e-postmeddelande** åtgärd och innehåller de **utdata** fält i e-postmeddelandets brödtext och ämne:

   ![”Utdata” fälten i åtgärden ”Skicka ett e-postmeddelande”](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

3. Nu kan köra din logikapp manuellt. I verktygsfältet för appdesignern väljer **kör**. 

   Baserat på den e-anslutningen som du använde, är här de resultat du får:

   ![E-post med resultat för ”skriva”-åtgärd](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Skapa CSV Tabellåtgärd

Du kan skapa en fil med kommaavgränsade värden (CSV)-tabell som har egenskaper och värden från JavaScript Object Notation (JSON) objekt i en matris med de **dataåtgärder - skapa CSV tabell** åtgärd. Du kan sedan använda den resulterande tabellen i åtgärder som följer den **skapa CSV tabell** åtgärd. 

Om du vill arbeta i kodredigeraren vy kan du kopiera exemplet **skapa CSV tabell** och **initieras variabeln** åtgärd definitionerna från den här artikeln i din egen logikapp underliggande arbetsflöde definition: [Data åtgärden kodexempel - skapa CSV tabell](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example) 

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> eller Visual Studio, öppna logikappen i Logic App Designer. 

   Det här exemplet används Azure portal och en logikapp med en **upprepning** utlösare och en **initieras variabeln** åtgärd. 
   Åtgärden har ställts in för att skapa en variabel vars inledande värde är en matris som har vissa egenskaper och värden i JSON-format. 
   När du testar din logikapp senare, kan du manuellt köra din app utan att behöva vänta utlösare utlöses.

   ![Från exempellogikappen](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. I logikappen där du vill skapa CSV-tabell, gör du något av följande: 

   * Om du vill lägga till en åtgärd under det sista steget, Välj **nytt steg** > **Lägg till en åtgärd**.

     ![Lägg till åtgärd](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Om du vill lägga till en åtgärd mellan stegen, musen över den anslutande pilen så visas på plustecknet (+). 
   Välj på plustecknet och välj sedan **Lägg till en åtgärd**.

3. I sökrutan anger du ”skapa csv tabell” som filter. Välj den här åtgärden från åtgärdslistan över: **skapa CSV tabell**

   ![Välj ”Skapa CSV tabell” åtgärd](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

4. I den **från** anger den matris eller ett uttryck som du vill använda för att skapa tabellen. 

   I det här exemplet när du klickar i den **från** den dynamiska innehållslistan visas rutan, så du kan välja den tidigare skapade variabeln:

   ![Välj matrisutdata för att skapa CSV tabell](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   Här är det klar exemplet **skapa CSV tabell** åtgärd: 

   ![Klar ”skapa CSV tabell” åtgärd](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

   Som standard skapar den här åtgärden automatiskt kolumner baserat på matrisobjekt. 
   För att manuellt skapa kolumnrubriker och värden, Välj **visa avancerade alternativ**. 
   För att ge endast anpassade värden, ändra **kolumner** till **anpassade**. 
   För att tillhandahålla anpassade kolumnrubrikerna för att ändra **inkludera rubriker** till **Ja**. 

   > [!TIP]
   > Du kan skapa användarvänliga token för egenskaper i JSON-objekt så att du kan välja dessa egenskaper som indata med de [parsa JSON](#parse-json-action) innan du anropar den **skapa CSV tabell** åtgärd.

5. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

Mer information om den här åtgärden i dina underliggande arbetsflödesdefinitionen finns i den [tabellen åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testa din logikapp

Bekräfta om de **skapa CSV tabell** åtgärden skapar de förväntade resultaten, skicka dig själv ett meddelande som innehåller utdata från den **skapa CSV tabell** åtgärd.

1. I din logikapp, lägger du till en åtgärd som kan skicka resultaten från den **skapa CSV tabell** åtgärd.

2. Klicka på var du vill att resultatet ska visas i den åtgärden. När den dynamiska innehållslistan öppnas, under den **skapa CSV tabell** väljer **utdata**. 

   Det här exemplet används den **Office 365 Outlook – skicka ett e-postmeddelande** åtgärd och innehåller de **utdata** i e-postmeddelandets brödtext:

   ![”Utdata” fälten i åtgärden ”Skicka ett e-postmeddelande”](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

3. Nu kan köra din logikapp manuellt. I verktygsfältet för appdesignern väljer **kör**. 

   Baserat på den e-anslutningen som du använde, är här de resultat du får:

   ![E-post med ”skapa CSV tabell” åtgärd resultat](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Skapa HTML-Tabellåtgärd

Du kan skapa en HTML-tabell som har egenskaper och värden från JavaScript Object Notation (JSON) objekt i en matris med de **dataåtgärder - skapa HTML-tabell** åtgärd. Du kan sedan använda den resulterande tabellen i åtgärder som följer den **skapa HTML-tabell** åtgärd.

Om du vill arbeta i kodredigeraren vy kan du kopiera exemplet **skapa HTML-tabell** och **initieras variabeln** åtgärd definitionerna från den här artikeln i din egen logikapp underliggande arbetsflöde definition: [Data åtgärden kodexempel - skapa HTML-tabell](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> eller Visual Studio, öppna logikappen i Logic App Designer. 

   Det här exemplet används Azure portal och en logikapp med en **upprepning** utlösare och en **initieras variabeln** åtgärd. 
   Åtgärden har ställts in för att skapa en variabel vars inledande värde är en matris som har vissa egenskaper och värden i JSON-format. 
   När du testar din logikapp senare, kan du manuellt köra din app utan att behöva vänta utlösare utlöses.

   ![Från exempellogikappen](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. I logikappen där du vill skapa en HTML-tabell, gör du något av följande: 

   * Om du vill lägga till en åtgärd under det sista steget, Välj **nytt steg** > **Lägg till en åtgärd**.

     ![Lägg till åtgärd](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Om du vill lägga till en åtgärd mellan stegen, musen över den anslutande pilen så visas på plustecknet (+). 
   Välj på plustecknet och välj sedan **Lägg till en åtgärd**.

3. I sökrutan anger du ”skapa html-tabell” som filter. Välj den här åtgärden från åtgärdslistan över: **skapa HTML-tabell**

   ![Välj ”skapa HTML-”-Tabellåtgärd](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

4. I den **från** anger den matris eller ett uttryck som du vill använda för att skapa tabellen. 

   I det här exemplet när du klickar i den **från** den dynamiska innehållslistan visas rutan, så du kan välja den tidigare skapade variabeln:

   ![Välj matrisutdata för att skapa HTML-tabell](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   Här är det klar exemplet **skapa HTML-tabell** åtgärd: 

   ![Klar ”skapa HTML-”-Tabellåtgärd](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

   Som standard skapar den här åtgärden automatiskt kolumner baserat på matrisobjekt. 
   För att manuellt skapa kolumnrubriker och värden, Välj **visa avancerade alternativ**. 
   För att ge endast anpassade värden, ändra **kolumner** till **anpassade**. 
   För att tillhandahålla anpassade kolumnrubrikerna för att ändra **inkludera rubriker** till **Ja**. 

   > [!TIP]
   > Du kan skapa användarvänliga token för egenskaper i JSON-objekt så att du kan välja dessa egenskaper som indata med de [parsa JSON](#parse-json-action) innan du anropar den **skapa HTML-tabell** åtgärd.

5. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

Mer information om den här åtgärden i dina underliggande arbetsflödesdefinitionen finns i den [tabellen åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testa din logikapp

Bekräfta om de **skapa HTML-tabell** åtgärden skapar de förväntade resultaten, skicka dig själv ett meddelande som innehåller utdata från den **skapa HTML-tabell** åtgärd.

1. I din logikapp, lägger du till en åtgärd som kan skicka resultaten från den **skapa HTML-tabell** åtgärd.

2. Klicka på var du vill att resultatet ska visas i den åtgärden. När den dynamiska innehållslistan öppnas, under den **skapa HTML-tabell** väljer **utdata**. 

   Det här exemplet används den **Office 365 Outlook – skicka ett e-postmeddelande** åtgärd och innehåller de **utdata** i e-postmeddelandets brödtext:

   ![”Utdata” fälten i åtgärden ”Skicka ett e-postmeddelande”](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)
   
   > [!NOTE]
   > När HTML-tabell som utdata i en e poståtgärd och se till att du ställer in den **är HTML** egenskap **Ja** i e-åtgärd avancerade alternativ. På så sätt kan e-poståtgärden formaterar korrekt HTML-tabell.

3. Nu kan köra din logikapp manuellt. I verktygsfältet för appdesignern väljer **kör**. 

   Baserat på den e-anslutningen som du använde, är här de resultat du får:

   ![E-post med resultat för ”skapa HTML-tabell”-åtgärd](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Filtermatrisåtgärd

Om du vill skapa en mindre matris med objekt som uppfyller specifika villkor, från en befintlig matris, använda den **dataåtgärder - filtermatris** åtgärd. Du kan sedan använda filtrerade matrisen i åtgärder som följer efter den **filtermatris** åtgärd. 

> [!NOTE]
> Filtret text som du använder i villkoret är skiftlägeskänsligt. Den här åtgärden kan även ändra format eller komponenter i objekt i matrisen. 
> 
> För åtgärder för att använda matrisutdata från den **filtermatris** åtgärd, antingen dessa åtgärder måste acceptera matriser som indata, eller du kanske omvandla utdata-matris till en annan kompatibelt format. 

Om du vill arbeta i kodredigeraren vy kan du kopiera exemplet **filtermatris** och **initieras variabeln** åtgärd definitionerna från den här artikeln i din egen logikapp underliggande arbetsflöde definition: [Data åtgärden kodexempel - filtermatris](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example) 

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> eller Visual Studio, öppna logikappen i Logic App Designer. 

   Det här exemplet används Azure portal och en logikapp med en **upprepning** utlösare och en **initieras variabeln** åtgärd. 
   Åtgärden har ställts in för att skapa en variabel vars inledande värde är en matris som har vissa exempel heltal. När du testar din logikapp senare, kan du manuellt köra din app utan att behöva vänta utlösare utlöses.

   > [!NOTE]
   > Även om det här exemplet använder en enkel heltalsmatris kan är den här åtgärden särskilt användbart för JSON-objekt som matriser där du kan filtrera baserat på objektens egenskaper och värden.

   ![Från exempellogikappen](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

2. I logikappen där du vill skapa den filtrera matrisen, gör du något av följande: 

   * Om du vill lägga till en åtgärd under det sista steget, Välj **nytt steg** > **Lägg till en åtgärd**.

     ![Lägg till åtgärd](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Om du vill lägga till en åtgärd mellan stegen, musen över den anslutande pilen så visas på plustecknet (+). 
   Välj på plustecknet och välj sedan **Lägg till en åtgärd**.

3. I sökrutan anger du ”filtermatris” som filter. Välj den här åtgärden från åtgärdslistan över: **filtermatris**

   ![Välj ”filtrera matris” åtgärd](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

4. I den **från** anger den matris eller ett uttryck som du vill filtrera. 

   I det här exemplet när du klickar i den **från** den dynamiska innehållslistan visas rutan, så du kan välja den tidigare skapade variabeln:

   ![Välj matrisutdata för att skapa filtrerad matris](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

5. Ange matrisobjekt om du vill jämföra väljer jämförelseoperatorn för villkoret och ange värdet för jämförelse.

   Det här exemplet används den **item()** funktion för att komma åt varje objekt i matrisen när den **filtermatris** åtgärd sökningar för matris objekt vars värde är större än 1:
   
   ![Klar ”filtermatris”-åtgärd](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

6. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

Läs mer om den här åtgärden i dina underliggande arbetsflödesdefinitionen [frågeåtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Testa din logikapp

Bekräfta om **filtermatris** åtgärden skapar de förväntade resultaten, skicka dig själv ett meddelande som innehåller utdata från den **filtermatris** åtgärd.

1. I din logikapp, lägger du till en åtgärd som kan skicka resultaten från den **filtermatris** åtgärd.

2. Klicka på var du vill att resultatet ska visas i den åtgärden. När du öppnar listan med dynamiskt innehåll väljer **uttryck**. Att få matrisutdata från den **filtermatris** åtgärd, ange det här uttrycket som innehåller den **filtermatris** åtgärdens namn:

   ```
   @actionBody('Filter_array')
   ```

   Det här exemplet används den **Office 365 Outlook – skicka ett e-postmeddelande** åtgärd och innehåller utdata från den **actionBody('Filter_array')** uttryck i e-postmeddelandets brödtext:

   ![Åtgärdens utdata i åtgärden ”Skicka ett e-postmeddelande”](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

3. Nu kan köra din logikapp manuellt. I verktygsfältet för appdesignern väljer **kör**. 

   Baserat på den e-anslutningen som du använde, är här de resultat du får:

   ![E-post med ”filtermatris” åtgärd resultat](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Ansluta till åtgärd

Om du vill skapa en sträng som innehåller alla element från en matris och avgränsa de objekt med en specifik avgränsningstecken, använda den **dataåtgärder - koppla** åtgärd. Du kan sedan använda strängen i åtgärder som följer efter den **ansluta** åtgärd.

Om du vill arbeta i kodredigeraren vy kan du kopiera exemplet **ansluta** och **initieras variabeln** åtgärd definitionerna från den här artikeln i din egen logikapp underliggande arbetsflödesdefinitionen: [ Data åtgärden kodexempel - koppling](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example) 

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> eller Visual Studio, öppna logikappen i Logic App Designer. 

   Det här exemplet används Azure portal och en logikapp med en **upprepning** utlösare och en **initieras variabeln** åtgärd. 
   Den här åtgärden har ställts in för att skapa en variabel vars inledande värde är en matris som har vissa exempel heltal. 
   Du kan manuellt köra din app utan att behöva vänta utlösare utlöses när du testar din logikapp senare.

   ![Från exempellogikappen](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

2. I logikappen där du vill skapa strängen från en matris, gör du något av följande: 

   * Om du vill lägga till en åtgärd under det sista steget, Välj **nytt steg** > **Lägg till en åtgärd**.

     ![Lägg till åtgärd](./media/logic-apps-perform-data-operations/add-join-action.png)

   * Om du vill lägga till en åtgärd mellan stegen, musen över den anslutande pilen så visas på plustecknet (+). 
   Välj på plustecknet och välj sedan **Lägg till en åtgärd**.

3. I sökrutan anger du ”Anslut” som filter. Välj den här åtgärden från åtgärdslistan över: **delta**

   ![Välj ”Data Operations--anslutning till”-åtgärd](./media/logic-apps-perform-data-operations/select-join-action.png)

4. I den **från** anger matris som innehåller de element som du vill ansluta till som en sträng. 

   I det här exemplet när du klickar i den **från** rutan den dynamiska innehållslistan som visas så att du kan välja den tidigare skapade variabeln:  

   ![Välj matrisutdata för att skapa strängen](./media/logic-apps-perform-data-operations/configure-join-action.png)

5. I den **träffa** anger tecknet som du vill använda för att avgränsa varje objekt i matrisen. 

   Det här exemplet används ett kolon (:) som avgränsare.

   ![Ange avgränsningstecken](./media/logic-apps-perform-data-operations/finished-join-action.png)

6. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

Mer information om den här åtgärden i dina underliggande arbetsflödesdefinitionen finns i den [delta åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Testa din logikapp

Bekräfta om den **ansluta** åtgärden skapar de förväntade resultaten, skicka dig själv ett meddelande som innehåller utdata från den **ansluta** åtgärd. 

1. I din logikapp, lägger du till en åtgärd som kan skicka resultaten från den **ansluta** åtgärd.

2. Klicka på var du vill att resultatet ska visas i den åtgärden. När den dynamiska innehållslistan öppnas, under den **ansluta** väljer **utdata**. 

   Det här exemplet används den **Office 365 Outlook – skicka ett e-postmeddelande** åtgärd och innehåller de **utdata** i e-postmeddelandets brödtext:

   ![”Utdata” fälten i åtgärden ”Skicka ett e-postmeddelande”](./media/logic-apps-perform-data-operations/send-email-join-action.png)

3. Nu kan köra din logikapp manuellt. I verktygsfältet för appdesignern väljer **kör**. 

   Baserat på den e-anslutningen som du använde, är här de resultat du får:

   ![E-post med ”anslutning till” åtgärd resultat](./media/logic-apps-perform-data-operations/join-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Parsa JSON-åtgärd

Referens eller åtkomst egenskaper i JavaScript Object Notation (JSON) innehåll, du kan skapa användarvänliga fält eller token för dessa egenskaper med hjälp av den **dataåtgärder – parsa JSON** åtgärd.
På så sätt kan du kan välja dessa egenskaper från listan med dynamiskt innehåll när du anger indata för din logikapp. För den här åtgärden kan du ange JSON-scheman eller generera en JSON-schema från exemplet JSON-innehåll eller nyttolast.

Om du vill arbeta i kodredigeraren vy kan du kopiera exemplet **parsa JSON** och **initieras variabeln** åtgärd definitionerna från den här artikeln i din egen logikapp underliggande arbetsflödesdefinition : [Data åtgärden kodexempel – parsa JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example) 

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> eller Visual Studio, öppna logikappen i Logic App Designer. 

   Det här exemplet används Azure portal och en logikapp med en **upprepning** utlösare och en **initieras variabeln** åtgärd. 
   Åtgärden har ställts in för att skapa en variabel vars inledande värde är ett JSON-objekt med egenskaper och värden. 
   När du testar din logikapp senare, kan du manuellt köra din app utan att behöva vänta utlösare utlöses.

   ![Från exempellogikappen](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

2. I logikappen där du vill parsa JSON-innehåll, gör du något av följande: 

   * Om du vill lägga till en åtgärd under det sista steget, Välj **nytt steg** > **Lägg till en åtgärd**.

     ![Lägg till åtgärd](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Om du vill lägga till en åtgärd mellan stegen, musen över den anslutande pilen så visas på plustecknet (+). 
   Välj på plustecknet och välj sedan **Lägg till en åtgärd**.

3. I sökrutan anger du ”parsa json” som filter. Välj den här åtgärden från åtgärdslistan över: **parsa JSON**

   ![Välj ”parsa JSON”-åtgärd](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

4. I den **innehåll** ange JSON-innehåll som du vill parsa. 

   I det här exemplet när du klickar i den **innehåll** den dynamiska innehållslistan visas rutan, så du kan välja den tidigare skapade variabeln:

   ![Välj JSON-objekt för åtgärden parsa JSON](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

5. Ange JSON-schema som beskriver JSON-innehåll som du parsning. 

   Här är JSON-schemat för det här exemplet:

   ![Ange JSON-schemat för JSON-objekt som du vill parsa](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Om du inte har schemat kan du generera schemat från JSON-innehåll eller *nyttolast*, du parsning. 
   
   1. I den **parsa JSON** väljer **Använd exempel för att generera schemat**.

   2. Under **Skriv eller klistra in en JSON-exempelnyttolast**, ange JSON-innehåll och välj sedan **klar**.

      ![Ange JSON-innehåll för att generera schemat](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

6. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

Läs mer om den här åtgärden i dina underliggande arbetsflödesdefinitionen [åtgärd för att parsa JSON](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testa din logikapp

Bekräfta om de **parsa JSON** åtgärden skapar de förväntade resultaten, skicka dig själv ett meddelande som innehåller utdata från den **parsa JSON** åtgärd.

1. I din logikapp, lägger du till en åtgärd som kan skicka resultaten från den **parsa JSON** åtgärd.

2. Klicka på var du vill att resultatet ska visas i den åtgärden. När den dynamiska innehållslistan öppnas, under den **parsa JSON** åtgärd, du kan nu välja egenskaperna från det parsade JSON-innehållet.

   Det här exemplet används den **Office 365 Outlook – skicka ett e-postmeddelande** åtgärd och innehåller de **FirstName**, **LastName**, och **e-post** fält i den e-postmeddelandets brödtext:

   ![JSON-egenskaperna i åtgärden ”Skicka ett e-postmeddelande”](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Här är klar e-poståtgärden:

   ![Färdiga e-poståtgärd](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

3. Nu kan köra din logikapp manuellt. I verktygsfältet för appdesignern väljer **kör**. 

   Baserat på den e-anslutningen som du använde, är här de resultat du får:

   ![E-post med ”anslutning till” åtgärd resultat](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Välj åtgärd

Du kan skapa en matris med JSON-objekt som bygger på värdena i en befintlig matris med de **dataåtgärder – Välj** åtgärd. Du kan till exempel skapa en JSON-objekt för varje värde i en heltalsmatris genom att ange de egenskaper som varje JSON-objekt måste ha och hur du mappar värden i matrisen källa till dessa egenskaper. Och men du kan ändra komponenterna i dessa JSON-objekt, matrisen utdata har alltid samma antal objekt som källmatris.

> [!NOTE]
> För åtgärder för att använda matrisutdata från den **Välj** åtgärden, antingen dessa åtgärder måste acceptera matriser som indata, eller du kanske omvandla utdata-matris till en annan kompatibelt format. 

Om du vill arbeta i kodredigeraren vy kan du kopiera exemplet **Välj** och **initieras variabeln** åtgärd definitionerna från den här artikeln i din egen logikapp underliggande arbetsflödesdefinitionen: [Data åtgärden kodexempel - Välj](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> eller Visual Studio, öppna logikappen i Logic App Designer. 

   Det här exemplet används Azure portal och en logikapp med en **upprepning** utlösare och en **initieras variabeln** åtgärd. 
   Åtgärden har ställts in för att skapa en variabel vars inledande värde är en matris som har vissa exempel heltal. 
   När du testar din logikapp senare, kan du manuellt köra din app utan att behöva vänta utlösare utlöses.

   ![Från exempellogikappen](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

2. I logikappen där du vill skapa matrisen, gör du något av följande: 

   * Om du vill lägga till en åtgärd under det sista steget, Välj **nytt steg** > **Lägg till en åtgärd**.

     ![Lägg till åtgärd](./media/logic-apps-perform-data-operations/add-select-action.png)

   * Om du vill lägga till en åtgärd mellan stegen, musen över den anslutande pilen så visas på plustecknet (+). 
   Välj på plustecknet och välj sedan **Lägg till en åtgärd**.

3. I sökrutan anger du ”Välj” som filter. Välj den här åtgärden från åtgärdslistan över: **Välj**

   ![Välj åtgärden ”Välj”](./media/logic-apps-perform-data-operations/select-select-action.png)

4. I den **från** anger källmatris som du vill.

   I det här exemplet när du klickar i den **från** den dynamiska innehållslistan visas rutan, så du kan välja den tidigare skapade variabeln:

   ![Välj källa matris för åtgärden Välj](./media/logic-apps-perform-data-operations/configure-select-action.png)

5. I den **kartan** vänster kolumn, ange egenskapens namn som du vill tilldela varje värde i matrisen källa. Ange ett uttryck som representerar det värde som du vill tilldela egenskapen i den högra kolumnen.

   Det här exemplet anger ”Product_ID” som egenskapsnamn för att tilldela varje värde i heltalsmatrisen med hjälp av den **item()** funktion i ett uttryck som har åtkomst till varje objekt i matrisen. 

   ![Ange JSON-objektegenskapen och värden för matrisen som du vill skapa](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Här är den klara åtgärden:

   ![Klar med åtgärden Välj](./media/logic-apps-perform-data-operations/finished-select-action.png)

6. Spara din logikapp. Välj **Spara** i designerverktygsfältet.

Läs mer om den här åtgärden i dina underliggande arbetsflödesdefinitionen [Välj åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testa din logikapp

Bekräfta om de **Välj** åtgärden skapar de förväntade resultaten, skicka dig själv ett meddelande som innehåller utdata från den **Välj** åtgärd.

1. I din logikapp, lägger du till en åtgärd som kan skicka resultaten från den **Välj** åtgärd.

2. Klicka på var du vill att resultatet ska visas i den åtgärden. När du öppnar listan med dynamiskt innehåll väljer **uttryck**. Att få matrisutdata från den **Välj** åtgärd, ange det här uttrycket som innehåller den **Välj** åtgärdens namn:

   ```
   @actionBody('Select')
   ```

   Det här exemplet används den **Office 365 Outlook – skicka ett e-postmeddelande** åtgärd och innehåller utdata från den **actionBody('Select')** uttryck i e-postmeddelandets brödtext:

   ![Åtgärdens utdata i åtgärden ”Skicka ett e-postmeddelande”](./media/logic-apps-perform-data-operations/send-email-select-action.png)

3. Nu kan köra din logikapp manuellt. I verktygsfältet för appdesignern väljer **kör**. 

   Baserat på den e-anslutningen som du använde, är här de resultat du får:

   ![E-post med åtgärden ”Välj” resultat](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
