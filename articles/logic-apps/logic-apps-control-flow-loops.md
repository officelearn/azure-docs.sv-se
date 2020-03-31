---
title: Lägga till loopar för att upprepa åtgärder
description: Skapa loopar som upprepar arbetsflödesåtgärder eller processmatriser i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: 5f6c04c9a57dc8c250d99f2fa944203d2d73c404
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270581"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Skapa loopar som upprepar arbetsflödesåtgärder eller processmatriser i Azure Logic Apps

Om du vill bearbeta en matris i logikappen kan du skapa en ["Foreach"-loop](#foreach-loop). Den här loopen upprepar en eller flera åtgärder för varje objekt i matrisen. Begränsningar för antalet matrisobjekt som "Foreach"-loopar kan bearbeta finns [i Gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md). 

Om du vill upprepa åtgärder tills ett villkor uppfylls eller ett tillstånd ändras kan du skapa en ["Till"-loop](#until-loop). Logikappen kör först alla åtgärder i loopen och kontrollerar sedan villkoret eller tillståndet. Om villkoret är uppfyllt stannar slingan. Annars upprepas slingan. Begränsningar för antalet tills-loopar i en logikappkörning finns i [Gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP]
> Om du har en utlösare som tar emot en matris och vill köra ett arbetsflöde för varje matrisobjekt kan *dubatchera* matrisen med [egenskapen **SplitOn-utlösaren** ](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>"Foreach" slinga

En "Foreach-loop" upprepar en eller flera åtgärder på varje matrisobjekt och fungerar bara på matriser. Iterationer i en "Foreach"-loop körs parallellt. Du kan dock köra iterationer en i taget genom att ställa in en [sekventiell "Foreach"-loop](#sequential-foreach-loop). 

Här är några funderingar när du använder "Foreach"-loopar:

* I kapslade slingor körs iterationer alltid sekventiellt, inte parallellt. Om du vill köra åtgärder parallellt för objekt i en kapslad loop skapar och [anropar du en underordnad logikapp](../logic-apps/logic-apps-http-endpoint.md).

* Om du vill få förutsägbara resultat från åtgärder på variabler under varje loopiteration kör du dessa loopar sekventiellt. Till exempel när en samtidig körning slinga slutar, öka, minskning och lägga till variabla operationer returnera förutsägbara resultat. Men under varje iteration i den samtidiga kör loopen, kan dessa åtgärder returnera oförutsägbara resultat. 

* Åtgärder i en "Foreach"-slinga använder[`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
för att referera till och bearbeta varje objekt i matrisen. Om du anger data som inte finns i en matris misslyckas arbetsflödet för logikappen. 

Det här exemplet logik app skickar en daglig sammanfattning för en webbplats RSS-flöde. Appen använder en "Foreach"-loop som skickar ett e-postmeddelande för varje nytt objekt.

1. [Skapa den här exempellogiken med](../logic-apps/quickstart-create-first-logic-app-workflow.md) ett Outlook.com- eller Office 365 Outlook-konto.

2. Mellan RSS-utlösaren och skicka e-poståtgärd, lägg till en "Foreach" loop. 

   1. Om du vill lägga till en slinga mellan stegen flyttar du pekaren över pilen mellan dessa steg. 
   Välj **plustecknet** **+**( ) som visas och välj sedan **Lägg till en åtgärd**.

      ![Välj "Lägg till en åtgärd"](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. Under sökrutan väljer du **Alla**. Skriv "för varje" som filter i sökrutan. Välj den här åtgärden i åtgärdslistan: **För varje - Kontroll**

      ![Lägg till "För varje" loop](media/logic-apps-control-flow-loops/select-for-each.png)

3. Nu bygga slingan. Under **Välj en utdata från föregående steg** efter att listan Lägg till **dynamiskt innehåll** visas väljer du matrisen Feed **links,** som matas ut från RSS-utlösaren. 

   ![Välj från listan med dynamiskt innehåll](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Du kan *bara* välja matrisutdata från föregående steg.

   Den valda matrisen visas nu här:

   ![Välj matris](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Om du vill köra en åtgärd för varje matrisobjekt drar du åtgärden **Skicka ett e-postmeddelande** till loopen. 

   Logikappen kan se ut ungefär så här:

   ![Lägg till steg i "Foreach"-loopen](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Spara din logikapp. Om du vill testa logikappen manuellt väljer du **Kör**i designerverktygsfältet .

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>"Foreach" loop definition (JSON)

Om du arbetar i kodvyn för logikappen `Foreach` kan du definiera loopen i logikappens JSON-definition i stället, till exempel:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>"Foreach" slinga: Sekventiell

Som standard körs cykler i en "Foreach"-loop parallellt. Om du vill köra varje cykel sekventiellt ställer du in loopens **sekventiella** alternativ. "Foreach" loopar måste köras sekventiellt när du har kapslade loopar eller variabler inuti slingor där du förväntar dig förutsägbara resultat. 

1. I slingans övre högra hörn väljer du **ellipser** (**...**) > **Inställningar**.

   ![I "Foreach"-loopen väljer du "..." > "Inställningar"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. Under **Samtidighetskontroll**vrider du inställningen **Samtidighetskontroll** till **På**. Flytta reglaget **Parallellismgrad** till **1**och välj **Klar**.

   ![Aktivera samtidighetskontroll](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Om du arbetar med logikappens JSON-definition kan `Sequential` du använda `operationOptions` alternativet genom att lägga till parametern, till exempel:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>"Tills" slinga
  
Om du vill köra och upprepa åtgärder tills ett villkor uppfylls eller ett tillstånd ändras placerar du dessa åtgärder i loopen "Till". Logikappen kör först alla åtgärder i loopen och kontrollerar sedan villkoret eller tillståndet. Om villkoret är uppfyllt stannar slingan. Annars upprepas slingan.

Här är några vanliga scenarier där du kan använda en "Tills"-loop:

* Anropa en slutpunkt tills du får det svar du vill ha.

* Skapa en post i en databas. Vänta tills ett visst fält i posten godkänns. Fortsätt bearbeta. 

Från och med 8:00 varje dag ökar det här exemplet logikappen en variabel tills variabelns värde är lika med 10. Logikappen skickar sedan ett e-postmeddelande som bekräftar det aktuella värdet. 

> [!NOTE]
> De här stegen använder Office 365 Outlook, men du kan använda alla e-postleverantörer som Logic Apps stöder. 
> [Kontrollera kopplingslistan här](https://docs.microsoft.com/connectors/). Om du använder ett annat e-postkonto förblir de allmänna stegen desamma, men användargränssnittet kan se lite annorlunda ut. 

1. Skapa en tom logikapp. Välj **Alla**under sökrutan i Logic App Designer . Sök efter "återkommande". 
   Välj den här utlösaren i listan utlösare: **Återkommande - Schema**

   ![Lägga till utlösaren "Återkommande - schema"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Ange när utlösaren utlöses genom att ställa in intervallet, frekvensen och timmen för dagen. Om du vill ställa in timmen väljer du **Visa avancerade alternativ**.

   ![Ställ in schema för återkommande](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Egenskap | Värde |
   | -------- | ----- |
   | **Intervall** | 1 | 
   | **Frekvens** | Day |
   | **Vid dessa timmar** | 8 |
   ||| 

1. Under utlösaren väljer du **Nytt steg**. 
   Sök efter "variabler" och välj den här åtgärden: **Initiera variabel - Variabler**

   ![Lägg till åtgärden "Initiera variabel - variabler"](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Ställ in variabeln med följande värden:

   ![Ange variabelegenskaper](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Egenskap | Värde | Beskrivning |
   | -------- | ----- | ----------- |
   | **Namn** | Gräns | Variabelns namn | 
   | **Typ** | Integer | Variabelns datatyp | 
   | **Värde** | 0 | Variabelns startvärde | 
   |||| 

1. Välj **Nytt steg**under variabeln **Initialize.** 

1. Under sökrutan väljer du **Alla**. Sök efter "tills", och välj denna åtgärd: **Tills - Kontroll**

   ![Lägg till "tills"-loop](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Skapa loopens utgångsvillkor genom att välja variabeln **Begränsa** och operatorn **är lika.** 
   Ange **10** som jämförelsevärde.

   ![Skapa utgångstillstånd för stoppslinga](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. I loopen väljer du **Lägg till en åtgärd**. 

1. Under sökrutan väljer du **Alla**. Sök efter "variabler" och välj den här åtgärden: **Öka variabeln - Variabler**

   ![Lägg till åtgärd för att öka variabeln](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. För **Namn**väljer du variabeln **Begränsa.** För **värde**anger du "1". 

     ![Öka "Gräns" med 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Utanför och under slingan väljer du **Nytt steg**. 

1. Under sökrutan väljer du **Alla**. 
     Hitta och lägg till en åtgärd som skickar e-post, till exempel: 

     ![Lägga till åtgärd som skickar e-post](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Logga in på ditt e-postkonto om du uppmanas att göra det.

1. Ange egenskaperna för e-poståtgärden. Lägg till **variabeln Begränsa** i ämnet. På så sätt kan du bekräfta att variabelns aktuella värde uppfyller ditt angivna villkor, till exempel:

      ![Ställ in e-postegenskaper](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Egenskap | Värde | Beskrivning |
      | -------- | ----- | ----------- | 
      | **Att** | *\<domän för\@e-postadress>* | Mottagarens e-postadress. För testning använder du din egen e-postadress. | 
      | **Subjekt** | Aktuellt värde för "Gräns" är **Gräns** | Ange e-postämnet. I det här exemplet kontrollerar du att du inkluderar variabeln **Begränsa.** | 
      | **Brödtext** | <*e-postinnehåll*> | Ange det e-postmeddelandesinnehåll som du vill skicka. I det här exemplet anger du vilken text du vill. | 
      |||| 

1. Spara din logikapp. Om du vill testa logikappen manuellt väljer du **Kör**i designerverktygsfältet .

      När logiken börjar köras får du ett e-postmeddelande med det innehåll som du har angett:

      ![Mottaget e-postmeddelande](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Förhindra oändliga loopar

Loopen "Tills" har standardgränser som stoppar körningen om något av dessa villkor inträffar:

| Egenskap | Standardvärde | Beskrivning | 
| -------- | ------------- | ----------- | 
| **Räkna** | 60 | Det högsta antalet slingor som körs innan slingan avslutas. Standard är 60 cykler. | 
| **Timeout** | PT1H (PÅ) | Den minsta tid att köra en slinga innan loopen avslutas. Standardvärdet är en timme och anges i ISO 8601-format. <p>Timeout-värdet utvärderas för varje loopcykel. Om någon åtgärd i loopen tar längre tid än tidsgränsen stoppas inte den aktuella cykeln. Nästa cykel startar dock inte eftersom gränsvillkoret inte uppfylls. | 
|||| 

Om du vill ändra dessa standardgränser väljer du **Visa avancerade alternativ** i loopåtgärdsformen.

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Tills"-definition (JSON)

Om du arbetar i kodvyn för logikappen `Until` kan du definiera en loop i logikappens JSON-definition i stället, till exempel:

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

I det här exemplet "Tills" anropas en HTTP-slutpunkt, som skapar en resurs. Loopen stoppas när HTTP-svarstexten returneras med `Completed` status. För att förhindra oändliga slingor stannar slingan också om något av dessa villkor inträffar:

* Loopen kördes 10 gånger `count` som anges av attributet. Standardvärdet är 60 gånger. 

* Loopen kördes i två timmar `timeout` enligt attributet i ISO 8601-format. Standardär en timme.
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta om funktioner och förslag kan [du på webbplatsen för Azure Logic Apps-användares feedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på ett villkor (villkorssatser)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör steg baserat på olika värden (växelsatser)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Kör eller sammanfoga parallella steg (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperad åtgärdsstatus (scope)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
