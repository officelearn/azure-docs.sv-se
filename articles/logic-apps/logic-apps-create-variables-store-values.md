---
title: Skapa variabler för att spara värden – Azure Logic Apps | Microsoft Docs
description: Hur du sparar och hantera värden genom att skapa variabler i Azure Logic Apps
services: logic-apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: bb84c7d5e483b0a2abc3b7d1a37de8760513d203
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063224"
---
# <a name="create-variables-for-saving-and-managing-values-in-azure-logic-apps"></a>Skapa variabler för att spara och hantera värden i Azure Logic Apps

Den här artikeln visar hur du kan lagra och arbeta med värden i hela din logikapp genom att skapa variabler. Till exempel variabler kan hjälpa dig att räkna antalet gånger som körs i en loop. När iterera över en matris eller kontrollerar en matris för ett specifikt objekt, kan du använda en variabel för att referera till Indextalet för varje objekt i matrisen. 

Du kan skapa variabler för datatyper som heltal, flyttal, booleskt, sträng, matris och objekt. När du skapar en variabel kan utföra du andra uppgifter, till exempel:

* Hämta eller referera till variabelns värde.
* Öka eller minska variabeln med ett konstant värde, även kallat *ökningen* och *minska*.
* Tilldela variabeln ett annat värde.
* Infoga eller *lägga till* variabelns värde som senast i en sträng eller en matris.

Variabler finns och är globala endast inom den logic app-instansen där de skapas. De behåller dessutom, över alla loop iterationer inuti en logikappinstans. När du refererar till en variabel, kan du använda variabelns namn som token inte åtgärdens namn, vilket är vanligt att referera till en åtgärd utdata. 

> [!IMPORTANT]
> Som standard köras cykler i en ”Foreach”-loop parallellt. När du använder variabler i slingor kör loopen [sekventiellt](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) därför variabler returnerar förutsägbara resultat. 

Om du inte har en Azure-prenumeration än, <a href="https://azure.microsoft.com/free/" target="_blank">registrera dig för ett kostnadsfritt konto</a>. 

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här artikeln, är här de objekt som du behöver:

* Logikappen där du vill skapa en variabel 

  Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabbstarten: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* En [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts) som det första steget i din logikapp 

  Innan du kan lägga till åtgärder för att skapa och arbeta med variabler, måste din logikapp börja med en utlösare.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Initiera variabel

Du kan skapa en variabel och deklarera dess datatyp och ursprungsvärdet – allt inom en åtgärd i din logikapp. Du kan endast deklarera variabler på global nivå inte inom scope, villkor och loopar. 

1. I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> eller Visual Studio, öppna logikappen i Logic App Designer. 

   Det här exemplet används Azure portal och en logikapp med en befintlig utlösare.

2. I din logikapp under steget där du vill lägga till en variabel, gör du något av följande: 

   * Om du vill lägga till en åtgärd under det sista steget, Välj **nytt steg** > **Lägg till en åtgärd**.

     ![Lägg till åtgärd](./media/logic-apps-create-variables-store-values/add-action.png)

   * Om du vill lägga till en åtgärd mellan stegen, musen över den anslutande pilen så visas på plustecknet (+). 
   Välj på plustecknet och välj sedan **Lägg till en åtgärd**.

3. I sökrutan anger du ”variabler” som filter. Välj i åtgärdslistan **variabler – initiera variabel**.

   ![Välj åtgärd](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

4. Ange den här informationen för variabeln:

   | Egenskap  | Krävs | Värde |  Beskrivning |
   |----------|----------|-------|--------------|
   | Namn | Ja | <*variabeln-name*> | Namnet på variabeln för att öka | 
   | Typ | Ja | <*typ av variabel*> | Datatypen för variabeln | 
   | Värde | Nej | <*Start-värde*> | Det inledande värdet för variabeln <p><p>**Tips!** Även om det är valfritt, ange ett värde som bästa praxis så att du alltid vet startvärdet för variabeln. | 
   ||||| 

   ![Initiera variabel](./media/logic-apps-create-variables-store-values/initialize-variable.png)

5. Fortsätt nu lägga till åtgärder som du vill. När du är klar på verktygsfältet för appdesignern väljer **spara**.

Om du växlar från designer till vyn Kodredigeraren här är sätt den **initieras variabeln** åtgärden visas i dina logic app-definition som är i JavaScript Object Notation (JSON)-format:

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

Här följer exempel på några andra variabeltyper:

*Strängvariabeln*

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

För att hämta eller referera till en variabel innehållet, du kan också använda den [variables() funktionen](../logic-apps/workflow-definition-language-functions-reference.md#variables) i Logic App Designer och visa Kodredigeraren.
När du refererar till en variabel, kan du använda variabelns namn som token inte åtgärdens namn, vilket är vanligt att referera till en åtgärd utdata. 

Till exempel det här uttrycket hämtar objekt från matrisvariabel [skapade tidigare i den här artikeln](#append-value) med hjälp av den **variables()** funktion. Den **string()** funktionen returnerar variabelns innehållet i strängformat: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Inkrementera variabeln 

Att öka eller *ökningen* en variabel med ett konstant värde, lägga till den **variabler - inkrementera variabeln** åtgärd i logikappen. Den här åtgärden fungerar endast med heltal-och flyttalsvariabler.

1. I Logic App Designer under steget där du vill öka en befintlig variabel väljer **nytt steg** > **Lägg till en åtgärd**. 

   Den här logikappen har till exempel redan en utlösare och en åtgärd som skapade en variabel. Så lägger du till en ny åtgärd under de här stegen:

   ![Lägg till åtgärd](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Om du vill lägga till en åtgärd mellan befintliga steg, musen över den anslutande pilen så att visas på plustecknet (+). Välj på plustecknet och välj sedan **Lägg till en åtgärd**.

2. I sökrutan anger du ”inkrementera variabeln” som filter. Välj i åtgärdslistan **variabler - inkrementera variabeln**.

   ![Välj ”inkrementera variabeln”-åtgärd](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

3. Ange den här informationen för att öka variabeln:

   | Egenskap  | Krävs | Värde |  Beskrivning |
   |----------|----------|-------|--------------|
   | Namn | Ja | <*variabeln-name*> | Namnet på variabeln för att öka | 
   | Värde | Nej | <*öka värdet*> | Det värde som används för att öka variabeln. Standardvärdet är en. <p><p>**Tips!** Även om det är valfritt, ange ett värde som bästa praxis så att du alltid vet det specifika värdet för variabeln som ökar. | 
   |||| 

   Exempel: 
   
   ![Öka Värdeexempel](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

4. När du är klar på verktygsfältet för appdesignern väljer **spara**. 

Om du växlar från designer till vyn Kodredigeraren här är sätt den **inkrementera variabeln** åtgärden visas i dina logic app-definition som är i JSON-format:

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

## <a name="example-create-loop-counter"></a>Exempel: Skapa loop räknare

Variabler används ofta för att kunna räkna hur många gånger som körs i en loop. Det här exemplet visar hur du skapar och använda variabler för den här uppgiften genom att skapa en loop som räknar de bifogade filerna i ett e-postmeddelande.

1. Skapa en tom logikapp i Azure-portalen. Lägg till en utlösare som söker efter nya e-post och eventuella bifogade filer. 

   Det här exemplet används Office 365 Outlook-utlösare för **när ett nytt e-postmeddelande**. 
   Du kan ställa in den här utlösaren utlöses endast när e-postmeddelandet har bilagor.
   Du kan dock använda alla anslutningar som söker efter nya e-postmeddelanden med bifogade filer, till exempel Outlook.com-anslutning.

2. I utlösaren väljer **visa avancerade alternativ**. Om du vill att utlösaren söka efter bilagor och skicka de bifogade filerna i logikappens arbetsflöde, Välj **Ja** för dessa egenskaper:
   
   * **Has Attachment** (Innehåller bifogad fil) 
   * **Inkludera bifogade filer** 

   ![Söka efter och inkludera bifogade filer](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

3. Lägg till den [ **initieras variabeln** åtgärd](#create-variable). Skapa en heltalsvariabel som heter **antal** noll startvärde.

   ![Lägg till åtgärd för ”initiera variabel”](./media/logic-apps-create-variables-store-values/initialize-variable.png)

4. Om du vill gå igenom varje bifogad fil, lägger du till en *för var och en* loop genom att välja **nytt steg** > **mer** > **Lägg till en för varje**.

   ![Lägg till en ”för var och en” loop](./media/logic-apps-create-variables-store-values/add-loop.png)

5. I loopen, klickar du i den **Välj utdata från föregående steg** box. När den dynamiska innehållslistan visas väljer du **bilagor**. 

   ![Välj ”Bifogade filer”](./media/logic-apps-create-variables-store-values/select-attachments.png)

   Den **bilagor** fältet skickar en matris med e-postbilagor från utlösarens utdata i loopen.

6. I ”för var och en”-loop väljer **Lägg till en åtgärd**. 

   ![Välj ”Lägg till en åtgärd”](./media/logic-apps-create-variables-store-values/add-action-2.png)

7. I sökrutan anger du ”inkrementera variabeln” som filter. Välj i åtgärdslistan **variabler - inkrementera variabeln**.

   > [!NOTE]
   > Kontrollera att den **inkrementera variabeln** åtgärden visas i den här slingan. Om åtgärden visas utanför loopen, dra åtgärden till loopen.

8. I den **inkrementera variabeln** åtgärd, från den **namn** väljer den **antal** variabeln. 

   ![Välj ”antal” variabel](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

9. Under loopen, lägger du till en åtgärd som skickar du antalet bifogade filer. I åtgärden, inkluderar du värdet från den **antal** variabel, till exempel: 

   ![Lägg till en åtgärd som skickar resultaten](./media/logic-apps-create-variables-store-values/send-email-results.png)

10. Spara din logikapp. Välj **Spara** i designerverktygsfältet. 

### <a name="test-your-logic-app"></a>Testa din logikapp

1. Om logikappen inte är aktiverad på logikappmenyn, välja **översikt**. I verktygsfältet, välja **aktivera**. 

2. Välj Logic App Designer-verktygsfältet **kör**. Det här steget startar logikappen manuellt.

3. Skicka ett e-postmeddelande med en eller flera bilagor till e-postkontot som du använde i det här exemplet.

   Det här steget utlöser logic app-utlösare, som skapar och kör en instans för logikappens arbetsflöde.
   Därför kan skickar logikappen du ett meddelande eller e-postmeddelande som visar antalet bifogade filer i e-post som du har skickat.

Om du växlar från designer till vyn Kodredigeraren här är hur ”för var och en”-loop visas med den **inkrementera variabeln** åtgärd i dina logic app-definition som är i JSON-format.

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

Att minska eller *minska* en variabel med ett konstant värde, följer du stegen för [ökar en variabel](#increment-value) förutom att du hitta och välja den **variabler – minska variabel**åtgärd i stället. Den här åtgärden fungerar endast med heltal-och flyttalsvariabler.

Här följer egenskaperna för den **minska variabel** åtgärd:

| Egenskap  | Krävs | Värde |  Beskrivning |
|----------|----------|-------|--------------|
| Namn | Ja | <*variabeln-name*> | Namnet på variabeln för att minska | 
| Värde | Nej | <*öka värdet*> | Värdet för minska variabeln. Standardvärdet är en. <p><p>**Tips!** Även om det är valfritt, ange ett värde som bästa praxis så att du alltid vet det specifika värdet för minska variabeln. | 
||||| 

Om du växlar från designer till vyn Kodredigeraren här är sätt den **minska variabel** åtgärden visas i dina logic app-definition som är i JSON-format.

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

Om du vill tilldela ett annat värde till en befintlig variabel, följer du stegen för [ökar en variabel](#increment-value) förutom att du: 

1. Sök efter och välj den **variabler – ange variabel** åtgärd i stället. 

2. Ange variabelnamnet och värdet som du vill tilldela. Både det nya värdet och variabeln måste ha samma datatyp.
Värdet är obligatoriskt eftersom åtgärden inte har ett standardvärde. 

Här följer egenskaperna för den **ange variabel** åtgärd:

| Egenskap  | Krävs | Värde |  Beskrivning | 
|----------|----------|-------|--------------| 
| Namn | Ja | <*variabeln-name*> | Namnet på variabeln att ändra | 
| Värde | Ja | <*nytt värde*> | Det värde som du vill tilldela variabeln. Båda måste ha samma datatyp. | 
||||| 

> [!NOTE]
> Om du inte öka eller minska variabler, ändra variabler i slingor *kan* skapa oväntade resultat eftersom slingor körs parallellt eller samtidigt, som standard. De här fallen, försök med din slingan körs sekventiellt. Till exempel när du vill referera till variabelvärdet i Loopens och förväntar sig samma värde i början och slutet av den loop-instansen, Följ dessa steg om du vill ändra hur slingan körs: 
>
> 1. Välj knappen med tre punkter (...) i din Loopens övre högra hörnet och väljer sedan **inställningar**.
> 
> 2. Under **samtidighetskontroll**, ändra den **åsidosätta standard** att ställa in **på**.
>
> 3. Dra den **grad av parallellitet** skjutreglaget till **1**.

Om du växlar från designer till vyn Kodredigeraren här är sätt den **ange variabel** åtgärden visas i dina logic app-definition som är i JSON-format. Det här exemplet ändras ”antal” variabelns aktuella värde till ett annat värde. 

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

## <a name="append-to-variable"></a>Lägga till i variabeln

För variabler som lagrar strängar eller matriser, kan du infoga eller *lägga till* en variabelns värde är den sista posten i dessa strängar eller matriser. Du kan följa stegen för att [ökar en variabel](#increment-value) förutom att du följer anvisningarna i stället: 

1. Sök efter och välj någon av följande åtgärder baserat på om variabeln är en sträng eller en matris. 

  * **Variabler – lägga till strängvariabeln**
  * **Variabler – lägga till en matrisvariabel** 

2. Ange värde att lägga till som det sista objektet i den sträng eller en matris. Det här värdet är obligatoriskt. 

Här följer egenskaperna för den **läggas till...**  åtgärder:

| Egenskap  | Krävs | Värde |  Beskrivning | 
|----------|----------|-------|--------------| 
| Namn | Ja | <*variabeln-name*> | Namnet på variabeln att ändra | 
| Värde | Ja | <*Lägg till värde*> | Det värde som du vill lägga till, vilket kan ha valfri typ | 
|||||  

Om du växlar från designer till vyn Kodredigeraren här är sätt den **Lägg till matrisvariabel** åtgärden visas i dina logic app-definition som är i JSON-format.
Det här exemplet skapar en matrisvariabel och lägger till ett annat värde som det sista objektet i matrisen. Resultatet är en uppdaterad variabeln som innehåller den här matrisen: `[1,2,3,"red"]` 

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
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
