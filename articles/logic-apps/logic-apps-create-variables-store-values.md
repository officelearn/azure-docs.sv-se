---
title: Skapa och hantera variabler för lagring och överföring av värden
description: Lär dig hur du lagrar, hanterar, använder och skickar värden med hjälp av variabler i dina automatiserade uppgifter och arbetsflöden som du skapar med Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 55984082a6b287e9f7cdca005a24ef3c18032491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456689"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>Lagra och hantera värden med hjälp av variabler i Azure Logic Apps

Den här artikeln visar hur du skapar och arbetar med variabler som du använder för att lagra värden i logikappen. Variabler kan till exempel hjälpa dig att spåra hur många gånger en loop körs. Om du vill iterera över en matris eller söka efter en matris efter en viss artikel kan du använda en variabel för att referera till indexnumret för varje matrisobjekt.

Du kan skapa variabler för datatyper som heltal, float, boolean, string, array och object. När du har skapat en variabel kan du utföra andra uppgifter, till exempel:

* Hämta eller referera till variabelns värde.
* Öka eller minska variabeln med ett konstant värde, även kallat *ökning* och *minskning*.
* Tilldela variabeln ett annat värde.
* Infoga eller *lägg till* variabelns värde som sista gången i en sträng eller matris.

Variabler finns och är globala endast inom logikappinstansen som skapar dem. Dessutom finns de kvar över alla loopiterationer inuti en logikappinstans. När du refererar till en variabel använder du variabelns namn som token, inte åtgärdens namn, vilket är det vanliga sättet att referera till en åtgärds utdata.

> [!IMPORTANT]
> Som standard körs cykler i en "För varje" slinga parallellt. När du använder variabler i loopar kör du loopen [sekventiellt](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) så att variablerna returnerar förutsägbara resultat.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en prenumeration [registrerar du dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Logikappen där du vill skapa variabeln

  Om du inte har tidigare i logikappar läser [Quickstart: Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* En [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som det första steget i logikappen

  Innan du kan lägga till åtgärder för att skapa och arbeta med variabler måste logikappen börja med en utlösare.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Initialisera variabel

Du kan skapa en variabel och deklarera dess datatyp och ursprungliga värde – allt inom en åtgärd i logikappen. Du kan bara deklarera variabler på global nivå, inte inom scope, villkor och loopar.

1. Öppna logikappen i Logic App Designer i [Azure-portalen](https://portal.azure.com) eller Visual Studio.

   I det här exemplet används Azure-portalen och en logikapp med en befintlig utlösare.

1. I logikappen följer du något av följande steg under det steg där du vill lägga till en variabel: 

   * Om du vill lägga till en åtgärd under det sista steget väljer du **Nytt steg**.

     ![Lägg till åtgärd](./media/logic-apps-create-variables-store-values/add-action.png)

   * Om du vill lägga till en åtgärd mellan stegen flyttar du**+** musen över kopplingspilen så att plustecknet ( ) visas. Markera plustecknet och välj sedan **Lägg till en åtgärd**.

1. Ange `variables` som filter i sökrutan under **Välj en åtgärd.** Välj **Initialize-variabel**i åtgärdslistan .

   ![Välj åtgärd](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. Ange den här informationen om variabeln enligt beskrivningen nedan:

   | Egenskap | Krävs | Värde |  Beskrivning |
   |----------|----------|-------|--------------|
   | **Namn** | Ja | <*variabel-namn*> | Namnet på variabeln som ska öka |
   | **Typ** | Ja | <*variabeltyp*> | Datatypen för variabeln |
   | **Värde** | Inga | <*start-värde*> | Det inledande värdet för variabeln <p><p>**Tips:** Ange det här värdet som bästa praxis även om det är valfritt så att du alltid vet startvärdet för variabeln. |
   |||||

   Ett exempel:

   ![Initialisera variabel](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Fortsätt nu att lägga till de åtgärder du vill ha. När du är klar väljer du **Spara**i designerverktygsfältet .

Om du växlar från designern till kodvyredigeraren visas följande sätt att **den initialisera variabeln** visas i logikappdefinitionen, som är i JSON-format (JavaScript Object Notation):

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

> [!NOTE]
> Även om **variabeln Initialize** har ett `variables` avsnitt som är strukturerat som en matris, kan åtgärden bara skapa en variabel i taget. Varje ny variabel kräver en **enskild variabelåtgärd för initierad** variabel.

Här är exempel för några andra variabla typer:

*Strängvariabel*

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

Om du vill hämta eller referera till en variabels innehåll kan du också använda [funktionen variables()](../logic-apps/workflow-definition-language-functions-reference.md#variables) i Logic App Designer och kodvyredigeraren. När du refererar till en variabel använder du variabelns namn som token, inte åtgärdens namn, vilket är det vanliga sättet att referera till en åtgärds utdata.

Det här uttrycket hämtar till exempel objekten från matrisvariabeln [som skapats tidigare i](#append-value) den `variables()` här artikeln med hjälp av funktionen. Funktionen `string()` returnerar variabelns innehåll i strängformat:`"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Variabel för ökning 

Om du vill öka eller *öka* en variabel med ett konstant värde lägger du till variabelåtgärden **Öka** i logikappen. Den här åtgärden fungerar bara med heltals- och floatvariabler.

1. Välj **Nytt steg**under det steg där du vill öka en befintlig variabel i Logic App Designer. 

   Den här logikappen har till exempel redan en utlösare och en åtgärd som skapade en variabel. Lägg därför till en ny åtgärd under följande steg:

   ![Lägg till åtgärd](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du musen över kopplingspilen så att plustecknet (+) visas. Markera plustecknet och välj sedan **Lägg till en åtgärd**.

1. I sökrutan anger du "stegvariabel" som filter. Välj **Öka variabel**i åtgärdslistan .

   ![Välj åtgärden "Öka variabel"](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. Ange den här informationen för att öka variabeln:

   | Egenskap | Krävs | Värde |  Beskrivning |
   |----------|----------|-------|--------------|
   | **Namn** | Ja | <*variabel-namn*> | Namnet på variabeln som ska öka |
   | **Värde** | Inga | <*öka värdet*> | Det värde som används för att öka variabeln. Standardvärdet är ett. <p><p>**Tips:** Ange det här värdet som bästa praxis även om det är valfritt så att du alltid känner till det specifika värdet för att öka variabeln. |
   ||||

   Ett exempel:

   ![Exempel på öka värde](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. När du är klar väljer du **Spara**i designerverktygsfältet .

Om du växlar från designern till kodvyredigeraren är det så här **variabeln Öka** i logikappdefinitionen, som är i JSON-format:

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

## <a name="example-create-loop-counter"></a>Exempel: Skapa loopräknare

Variabler används ofta för att räkna antalet gånger som en loop körs. Det här exemplet visar hur du skapar och använder variabler för den här uppgiften genom att skapa en loop som räknar bilagorna i ett e-postmeddelande.

1. Skapa en tom logikapp i Azure-portalen. Lägg till en utlösare som söker efter ny e-post och eventuella bilagor.

   I det här exemplet används utlösaren Office 365 Outlook för **När ett nytt e-postmeddelande anländer**. Du kan ställa in den här utlösaren så att den utlöses endast när e-postmeddelandet har bifogade filer. Du kan dock använda alla kopplingar som söker efter nya e-postmeddelanden med bilagor, till exempel Outlook.com-anslutningsappen.

1. Om du vill söka efter bifogade filer och skicka dessa bilagor till logikappens arbetsflöde i utlösaren väljer du **Ja** för följande egenskaper:

   * **Has Attachment** (Innehåller bifogad fil)
   * **Inkludera bifogade filer**

   ![Sök efter och inkludera bilagor](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. Lägg till [ **variabelåtgärden Initialize** ](#create-variable). Skapa en heltalsvariabel med namnet `Count` som har ett nollstartvärde.

   ![Lägg till åtgärd för "Initialize-variabel"](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Om du vill bläddra igenom varje bifogad fil lägger du till en *för varje* slinga.

   1. Välj **Nytt steg**under variabeln **Initiera variabel.**

   1. Under **Välj en åtgärd**väljer du **Inbyggd**. I sökrutan anger `for each` du som sökfilter och väljer **För varje**.

      ![Lägg till en "för varje" loop](./media/logic-apps-create-variables-store-values/add-loop.png)

1. I loopen klickar du i rutan **Välj en utdata från föregående steg.** När listan med dynamiskt innehåll visas väljer du **Bifogade filer**.

   ![Välj ”Bifogade filer”](./media/logic-apps-create-variables-store-values/select-attachments.png)

   Egenskapen **Bifogade filer** skickar en matris, som har e-postbilagorna från utlösarens utdata, till loopen.

1. Välj **Lägg** till en **åtgärd**i för varje slinga .

   ![Välj "Lägg till en åtgärd"](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. I sökrutan anger du "stegvariabel" som filter. Välj **Öka variabel**i åtgärdslistan .

   > [!NOTE]
   > Kontrollera att variabeln **Öka** visas i loopen. Om åtgärden visas utanför loopen drar du åtgärden till loopen.

1. Välj variabeln Antal **i** variabeln Öka i variabeln **Öka.** **Count**

   ![Välj variabel "Antal"](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. Lägg till alla åtgärder som skickar antalet bifogade filer under loopen. I åtgärden tar du med värdet från variabeln **Antal,** till exempel:

   ![Lägga till en åtgärd som skickar resultat](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. Spara din logikapp. Välj **Spara**i designerverktygsfältet .

### <a name="test-your-logic-app"></a>Testa logikappen

1. Om logikappen inte är aktiverad väljer du **Översikt**på logikappmenyn . Välj **Aktivera**i verktygsfältet .

1. Välj **Kör**i verktygsfältet Logikappdesigner . Det här steget startar logikappen manuellt.

1. Skicka ett e-postmeddelande med en eller flera bilagor till det e-postkonto som du använde i det här exemplet.

   Det här steget utlöser logikappens utlösare, som skapar och kör en instans för logikappens arbetsflöde. Logikappen skickar därför ett meddelande eller e-postmeddelande som visar antalet bifogade filer i e-postmeddelandet du skickade.

Om du växlar från designern till kodvyredigeraren är det så här för **varje** loop som visas tillsammans med åtgärden **Öka variabel** i logikappdefinitionen, som är i JSON-format.

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

## <a name="decrement-variable"></a>Variabel för minskning av deskap

Om du vill minska eller *minska* en variabel med ett konstant värde följer du stegen för att [öka en variabel](#increment-value) förutom att du hittar och väljer variabelåtgärden **Minska** i stället. Den här åtgärden fungerar bara med heltals- och floatvariabler.

Här är egenskaperna för **variabeln Decrement-variabel:**

| Egenskap | Krävs | Värde |  Beskrivning |
|----------|----------|-------|--------------|
| **Namn** | Ja | <*variabel-namn*> | Namnet på variabeln till decrement | 
| **Värde** | Inga | <*öka värdet*> | Värdet för att återskapa variabeln. Standardvärdet är ett. <p><p>**Tips:** Ange det här värdet som bästa praxis även om det är valfritt så att du alltid vet det specifika värdet för att återskapa variabeln. |
||||| 

Om du växlar från designern till kodvyredigeraren är det så här variabeln **Decrement-variabeln** visas i logikappdefinitionen, som är i JSON-format.

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

Om du vill tilldela ett annat värde till en befintlig variabel följer du stegen för att [öka en variabel](#increment-value) förutom att du:

1. Sök efter och välj **variabelåtgärden Ange** i stället.

1. Ange det variabelnamn och det värde som du vill tilldela. Både det nya värdet och variabeln måste ha samma datatyp. Värdet krävs eftersom den här åtgärden inte har något standardvärde.

Här är egenskaperna för åtgärden **Ange variabel:**

| Egenskap | Krävs | Värde |  Beskrivning |
|----------|----------|-------|--------------|
| **Namn** | Ja | <*variabel-namn*> | Namnet på variabeln som ska ändras |
| **Värde** | Ja | <*nytt värde*> | Det värde som du vill tilldela variabeln. Båda måste ha samma datatyp. |
||||| 

> [!NOTE]
> Om du inte ökar eller minskar variablerna *kan* det skapa oväntade resultat genom att ändra variabler inuti loopar eftersom loopar körs parallellt eller samtidigt. I dessa fall kan du prova att ställa in loopen så att den körs sekventiellt. När du till exempel vill referera till variabelvärdet i loopen och förvänta dig samma värde i början och slutet av loopinstansen, gör du så här för att ändra hur loopen körs: 
>
> 1. I slingans övre högra hörn väljer du ellipsknappen (**...**) och väljer sedan **Inställningar**.
> 
> 2. Under **Samtidighetskontroll**ändrar du inställningen **Åsidosätt standard** till **På**.
>
> 3. Dra reglaget **Parallellism till** **1**.

Om du växlar från designern till kodvyredigeraren är det så här **variabeln Ange** i logikappdefinitionen, som är i JSON-format. I det `Count` här exemplet ändras variabelns aktuella värde till ett annat värde.

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

## <a name="append-to-variable"></a>Lägg till i variabeln

För variabler som lagrar strängar eller matriser kan du infoga eller *lägga till* en variabels värde som det sista objektet i dessa strängar eller matriser. Du kan följa stegen för att [öka en variabel](#increment-value) förutom att du följer dessa steg i stället: 

1. Sök efter och välj en av dessa åtgärder baserat på om variabeln är en sträng eller en matris: 

   * **Lägg till i strängvariabel**
   * **Lägg till i matrisvariabel** 

1. Ange värdet som ska läggas till som det sista objektet i strängen eller matrisen. Det här värdet är obligatoriskt.

Här är egenskaperna för **åtgärderna Lägg till i...** :

| Egenskap | Krävs | Värde |  Beskrivning |
|----------|----------|-------|--------------|
| **Namn** | Ja | <*variabel-namn*> | Namnet på variabeln som ska ändras |
| **Värde** | Ja | <*bifoga värde*> | Det värde som du vill lägga till, som kan ha vilken typ som helst |
|||||

Om du växlar från designern till kodvyredigeraren visas följande sätt att åtgärden **Lägg till i matrisvariabeln** visas i logikappdefinitionen, som är i JSON-format. I det här exemplet skapas en matrisvariabel och ett annat värde läggs till som det sista objektet i matrisen. Resultatet är en uppdaterad variabel som innehåller den här matrisen:`[1,2,3,"red"]`

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

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Logic Apps-kopplingar](../connectors/apis-list.md)
