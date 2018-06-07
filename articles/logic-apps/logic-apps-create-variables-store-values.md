---
title: Skapa variabler för att spara värden - Azure Logic Apps | Microsoft Docs
description: Spara och hantera värden genom att skapa variabler i Azure Logic Apps
services: logic-apps
author: ecfan
manager: cfowler
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: cc464edf416a2b036d84e65e05810104d2706041
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655599"
---
# <a name="create-variables-for-saving-and-managing-values-in-azure-logic-apps"></a>Skapa variabler för att spara och hantera värden i Azure Logic Apps

Den här artikeln visar hur du kan lagra och arbeta med värden i hela din logikapp genom att skapa variabler. Till exempel hjälper variabler dig att räkna antalet gånger som en loop körs. När iterera över en matris eller kontrollerar en matris för ett specifikt objekt, kan du använda en variabel för att referera till Indextalet för varje element i matrisen. 

Du kan skapa variabler för-datatyper, till exempel heltal, flyttal, boolean, string, matris och objekt. När du har skapat en variabel kan du utföra andra uppgifter, till exempel:

* Hämta eller referera variabelns värde.
* Öka eller minska variabeln med ett konstant värde, även kallat *ökning* och *minska*.
* Tilldela variabeln ett annat värde.
* Infoga eller *bifoga* variabelns värde som senast i en sträng eller en matris.

Variabler finns och är globala endast inom den logik app-instansen där de. Dessutom de finns kvar i alla Repetera iterationer inuti en logik app-instansen. När du refererar till en variabel, kan du använda variabelns namn som token inte åtgärdens namn, vilket är vanligt att referera till en åtgärd utdata.

Om du inte har en Azure-prenumeration ännu, <a href="https://azure.microsoft.com/free/" target="_blank">registrera dig för ett kostnadsfritt Azure-konto</a>. 

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här artikeln, är här de objekt som du behöver:

* Logikappen där du vill skapa en variabel 

  Om du har använt logikappar granska [vad är Azure Logikappar](../logic-apps/logic-apps-overview.md) och [Snabbstart: skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* En [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts) som det första steget i din logikapp 

  Innan du kan lägga till åtgärder för att skapa och arbeta med variabler måste logikappen börja med en utlösare.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Initiera variabel

Du kan skapa en variabel och deklarera dess datatyp och startvärde - alla inom en åtgärd i din logikapp. Du kan endast deklarera variabler på global nivå inte inom scope, villkor och slingor. 

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> eller Visual Studio, öppna logikappen i logik App Designer. 

   Det här exemplet använder Azure-portalen och en logikapp med en befintlig utlösare.

2. I din logikapp under steget där du vill lägga till en variabel, gör du något av följande: 

   * Om du vill lägga till en åtgärd under det sista steget, Välj **nytt steg** > **lägga till en åtgärd**.

     ![Lägg till åtgärd](./media/logic-apps-create-variables-store-values/add-action.png)

   * Om du vill lägga till en åtgärd mellan stegen, håller du muspekaren över anslutande pilen så plustecken (+) visas. 
   Välj på plustecknet och välj sedan **lägga till en åtgärd**.

3. I sökrutan anger du ”variabler” som filter. Välj i listan åtgärder **variabler - initiera variabeln**.

   ![Välj åtgärd](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

4. Ange den här informationen för variabeln:

   | Egenskap  | Krävs | Värde |  Beskrivning |
   |----------|----------|-------|--------------|
   | Namn | Ja | <*variabeln namn*> | Namnet på variabeln för att öka | 
   | Typ | Ja | <*variabeln-typ*> | Datatypen för variabeln | 
   | Värde | Nej | <*Start-värde*> | Det inledande värdet för variabeln <p><p>**Tips**: även om det är valfritt, ange ett värde som bästa praxis så att du alltid vet startvärdet för variabeln. | 
   ||||| 

   ![Initiera variabel](./media/logic-apps-create-variables-store-values/initialize-variable.png)

5. Fortsätt nu lägga till de åtgärder som du vill använda. När du är klar i verktygsfältet designer väljer **spara**.

Om du växlar från designer i koden visa redigeringsprogrammet här är hur de **initiera variabeln** åtgärden visas i din logik app definition som är i JavaScript Object Notation (JSON)-format:

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

Här följer exempel på några andra typer:

*String-variabel*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*Boolesk variabel*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*Matris med heltal*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*Matris med strängar*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>Hämta variabelns värde

För att hämta eller referera till en variabel innehållet, du kan också använda den [variables() funktionen](../logic-apps/workflow-definition-language-functions-reference.md#variables) i App-Designer logik och kod visa redigeraren.
När du refererar till en variabel, kan du använda variabelns namn som token inte åtgärdens namn, vilket är vanligt att referera till en åtgärd utdata. 

Till exempel uttrycket hämtar objekt från matrisen variabeln [skapade tidigare i den här artikeln](#append-value) med hjälp av den **variables()** funktion. Den **string()** funktionen returnerar variabelns innehållet i Anslutningssträngens format: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Inkrementera variabeln 

Öka eller *ökning* lägga till en variabel med ett konstant värde i **variabler - öka variabeln** åtgärder för att din logikapp. Den här åtgärden fungerar endast med heltal och flyttal variabler.

1. I logik App Designer under steget där du vill öka en befintlig variabel väljer **nytt steg** > **lägga till en åtgärd**. 

   Till exempel redan den här logikapp en utlösare och en åtgärd som skapat en variabel. Så lägger du till en ny åtgärd under de här stegen:

   ![Lägg till åtgärd](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Om du vill lägga till en åtgärd mellan befintliga steg, håller du muspekaren över anslutande pilen så att plustecken (+) visas. Välj på plustecknet och välj sedan **lägga till en åtgärd**.

2. I sökrutan anger du ”öka variabeln” som filter. Välj i listan åtgärder **variabler - öka variabeln**.

   ![Välj ”öka variabeln”-åtgärd](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

3. Ange den här informationen för att öka variabeln:

   | Egenskap  | Krävs | Värde |  Beskrivning |
   |----------|----------|-------|--------------|
   | Namn | Ja | <*variabeln namn*> | Namnet på variabeln för att öka | 
   | Värde | Nej | <*Increment-värde*> | Det värde som används för att öka variabeln. Standardvärdet är en. <p><p>**Tips**: även om det är valfritt, ange ett värde som bästa praxis så att du alltid vet det specifika värdet för att öka variabeln. | 
   |||| 

   Exempel: 
   
   ![Öka Värdeexempel](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

4. När du är klar i verktygsfältet designer väljer **spara**. 

Om du växlar från designer i koden visa redigeringsprogrammet här är hur de **öka variabeln** åtgärden visas i din logik app definition som är i JSON-format:

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>Exempel: Skapa en loop-räknare

Variabler används ofta för att kunna räkna antalet gånger som en loop körs. Det här exemplet visar hur du skapar och använda variabler för den här uppgiften genom att skapa en loop som räknar bifogade filer i ett e-postmeddelande.

1. Skapa en tom logikapp i Azure-portalen. Lägg till en utlösare som söker efter ny e-post och bifogade filer. 

   Det här exemplet används Outlook för Office 365-utlösare för **när en ny e-postmeddelandet**. 
   Du kan ställa in den här utlösaren eller endast när e-postmeddelandet har bifogade filer.
   Du kan dock använda alla anslutningar som kontrollerar för nya e-postmeddelanden med bifogade filer, till exempel Outlook.com-kopplingen.

2. I utlösaren, Välj **visa avancerade alternativ**. Om du vill att utlösaren Kontrollera för bifogade filer och skicka dessa bifogade filer i din logikapp arbetsflöde, Välj **Ja** för dessa egenskaper:
   
   * **Has Attachment** (Innehåller bifogad fil) 
   * **Inkludera bifogade filer** 

   ![Sök efter och inkludera bifogade filer](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

3. Lägg till den [ **initiera variabeln** åtgärd](#create-variable). Skapa en heltalsvariabel som heter **antal** starta värdet noll.

   ![Lägg till åtgärd för ”initiera variabeln”](./media/logic-apps-create-variables-store-values/initialize-variable.png)

4. Om du vill gå igenom bifogad fil, lägger du till en *för varje* loop genom att välja **nytt steg** > **mer** > **Lägg till ett för varje**.

   ![Lägg till en ”för var och en” loop](./media/logic-apps-create-variables-store-values/add-loop.png)

5. I en slinga Klicka i den **Välj utdata från föregående steg** rutan. När listan över dynamiskt innehåll visas väljer du **bilagor**. 

   ![Välj ”Bifogade filer”](./media/logic-apps-create-variables-store-values/select-attachments.png)

   Den **bilagor** fältet skickar en matris med e-postbilagor från utlösarens utdata till en loop.

6. Välj i ”för var och en” loop **lägga till en åtgärd**. 

   ![Välj ”Lägg till en åtgärd”](./media/logic-apps-create-variables-store-values/add-action-2.png)

7. I sökrutan anger du ”öka variabeln” som filter. Välj i listan åtgärder **variabler - öka variabeln**.

   > [!NOTE]
   > Kontrollera att den **öka variabeln** åtgärden visas i den här slingan. Om åtgärden visas utanför loopen, drar du åtgärden till loopen.

8. I den **öka variabeln** åtgärd, från den **namn** väljer den **antal** variabeln. 

   ![Välj ”antal” variabel](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

9. Lägg till alla åtgärder som skickar antal bifogade filer under loopen. Inkludera i din åtgärd värdet från den **antal** variabel, till exempel: 

   ![Lägg till en åtgärd som skickar resultaten](./media/logic-apps-create-variables-store-values/send-email-results.png)

10. Spara din logikapp. Välj **Spara** i designerverktygsfältet. 

### <a name="test-your-logic-app"></a>Testa logikappen

1. Om din logikapp inte är aktiverat på logiken app-menyn väljer du **översikt**. I verktygsfältet, välja **aktivera**. 

2. Välj verktygsfältet logik App Designer **kör**. Det här steget startar manuellt logikappen.

3. Skicka ett e-postmeddelande med en eller flera bilagor till e-postkontot som du använde i det här exemplet.

   Det här steget utlöses den logikapp utlösare som skapar och kör en instans för din logikapp arbetsflöde.
   Därför skickar logikappen du ett meddelande eller e-post som visar antalet bifogade filer i e-postmeddelandet skickades.

Om du växlar från designer i koden visa redigeringsprogrammet här är hur slingan ”för varje” visas med den **öka variabeln** åtgärd i dina logic app definition som är i JSON-format.

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>Minska variabel

Att minska eller *minska* en variabel med ett konstant värde följer du stegen för [öka en variabel](#increment-value) förutom att du kan hitta och välja den **variabler - minska variabeln**åtgärd i stället. Den här åtgärden fungerar endast med heltal och flyttal variabler.

Här följer egenskaperna för den **minska variabeln** åtgärd:

| Egenskap  | Krävs | Värde |  Beskrivning |
|----------|----------|-------|--------------|
| Namn | Ja | <*variabeln namn*> | Namnet på variabeln för att minska | 
| Värde | Nej | <*Increment-värde*> | Värdet för minska variabeln. Standardvärdet är en. <p><p>**Tips**: även om det är valfritt, ange ett värde som bästa praxis så att du alltid vet det specifika värdet för minska variabeln. | 
||||| 

Om du växlar från designer i koden visa redigeringsprogrammet här är hur de **minska variabeln** åtgärden visas i din logik app definition som är i JSON-format.

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```


<a name="assign-value"></a>

## <a name="set-variable"></a>Ange variabel

Om du vill tilldela ett annat värde till en befintlig variabel, följer du stegen för [öka en variabel](#increment-value) förutom att du: 

1. Leta upp och markera den **variabler - ange variabel** åtgärd i stället. 

2. Ange namn och värde som du vill tilldela. Både det nya värdet och variabeln måste ha samma datatyp.
Värdet är obligatoriskt eftersom åtgärden inte har ett standardvärde. 

Här följer egenskaperna för den **ange variabel** åtgärd:

| Egenskap  | Krävs | Värde |  Beskrivning | 
|----------|----------|-------|--------------| 
| Namn | Ja | <*variabeln namn*> | Namnet på variabeln för att ändra | 
| Värde | Ja | <*nytt värde*> | Värdet som du vill tilldela variabeln. Båda måste ha samma datatyp. | 
||||| 

> [!NOTE]
> Om du inte är öka eller minska variabler, ändra variabler i slingor *kan* skapa oväntade resultat eftersom slingor körs parallellt eller parallellt, som standard. I dessa fall försök med din slingan körs sekventiellt. När du vill referera till variabelns värde i den här slingan och räknar med samma värde i början och slutet av loop instansen följer du dessa steg om du vill ändra hur slingan körs: 
>
> 1. Välj knappen med ellipstecknet (...) i din loop övre högra hörnet och välj **inställningar**.
> 
> 2. Under **samtidighetskontroll**, ändra den **åsidosätta standard** till **på**.
>
> 3. Dra den **grad av parallellitet** skjutreglaget till **1**.

Om du växlar från designer i koden visa redigeringsprogrammet här är hur de **ange variabel** åtgärden visas i din logik app definition som är i JSON-format. Det här exemplet ändras aktuellt värde för ”antal” variabeln till ett annat värde. 

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>Lägg till variabel

Du kan infoga för variabler som lagrar strängar eller matriser eller *lägga till* värde för en variabel som det sista objektet i dessa strängar eller matriser. Du kan följa stegen för [öka en variabel](#increment-value) förutom att du kan följa stegen i stället: 

1. Sök efter och välj någon av följande åtgärder baserat på om variabeln är en sträng eller en matris. 

  * **Variabler - lägga till string-variabel**
  * **Variabler - Lägg till i matrisvariabel** 

2. Ange ett värde att lägga till som det sista objektet i strängen eller matrisindexet. Det här värdet är obligatoriskt. 

Här följer egenskaperna för den **lägga till...**  åtgärder:

| Egenskap  | Krävs | Värde |  Beskrivning | 
|----------|----------|-------|--------------| 
| Namn | Ja | <*variabeln namn*> | Namnet på variabeln för att ändra | 
| Värde | Ja | <*Lägg till värdet*> | Det värde som du vill lägga till, vilket kan ha någon typ | 
|||||  

Om du växlar från designer i koden visa redigeringsprogrammet här är hur de **Lägg till matrisvariabel** åtgärden visas i din logik app definition som är i JSON-format.
Det här exemplet skapar en matrisvariabel och lägger till ett annat värde som det sista elementet i matrisen. Resultatet är en uppdaterad variabel som innehåller denna matris: `[1,2,3,"red"]` 

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Logic Apps kopplingar](../connectors/apis-list.md)
