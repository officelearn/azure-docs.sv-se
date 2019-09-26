---
title: Migrera från Azure Scheduler till Azure Logic Apps
description: Lär dig hur du kan ersätta jobb i Azure Scheduler, som dras tillbaka, med Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/23/2019
ms.openlocfilehash: 6b80cbd16ac78f7f347bef9ab8e22c4d67d31058
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301031"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrera Azure Scheduler-jobb till Azure Logic Apps

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras [tillbaka](#retire-date). Om du vill fortsätta arbeta med de jobb som du har konfigurerat i Scheduler kan du gå vidare till Azure Logic Apps så snart som möjligt genom att följa den här artikeln.

Den här artikeln visar hur du kan schemalägga engångs-och återkommande jobb genom att skapa automatiserade arbets flöden med Azure Logic Apps, i stället för med Azure Scheduler. När du skapar schemalagda jobb med Logic Apps får du följande fördelar:

* Bygg jobbet med hjälp av en Visual designer och [färdiga anslutningar](../connectors/apis-list.md) från hundratals tjänster, till exempel Azure Blob Storage, Azure Service Bus, Office 365 Outlook och SAP.

* Hantera alla schemalagda arbets flöden som en första klass Azure-resurs. Du behöver inte oroa dig för begreppet *jobb samling* eftersom varje Logic app är en enskild Azure-resurs.

* Köra flera engångs jobb genom att använda en enda Logic-app.

* Ange scheman som stöder tids zoner och justera automatiskt till sommar tid (sommar tid).

Mer information finns i [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md) eller prova att skapa din första Logic-app i den här snabb starten: [Skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Om du vill utlösa din Logi Kap par genom att skicka HTTP-begäranden använder du ett verktyg som t. ex. [Postman Desktop-appen](https://www.getpostman.com/apps).

## <a name="schedule-one-time-jobs"></a>Schemalägga engångs jobb

Du kan köra flera engångs jobb genom att skapa bara en enda Logic-app. 

1. I [Azure Portal](https://portal.azure.com)skapar du en tom Logic-app i Logic App Designer. 

   De grundläggande stegen följer [snabb start: Skapa din första Logic-](../logic-apps/quickstart-create-first-logic-app-workflow.md)app.

1. I rutan Sök anger du "när en http-begäran" som filter. Välj den här utlösaren i listan utlösare: **När en HTTP-begäran tas emot** 

   ![Lägg till utlösare för "begäran"](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. För begär ande utlösare kan du välja att ange ett JSON-schema som hjälper Logic App Designer att förstå strukturen för indatan från den inkommande begäran och gör utmatningarna enklare att välja senare i arbets flödet.

   Om du vill ange ett schema anger du schemat i rutan **JSON-schema för begär ande text** , till exempel: 

   ![Begär schema](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Om du inte har ett schema, men du har ett exempel på en nytto Last i JSON-format, kan du generera ett schema från den nytto lasten.

   1. I utlösaren för begäran väljer **du Använd exempel nytto last för att generera schemat**.

   1. Under **Ange eller klistra in en exempel-JSON-nyttolast**, anger du exempel nytto lasten och väljer sedan **färdig**, till exempel:

      ![Exempel på nytto Last](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. Under utlösaren väljer du **Nästa steg**. 

1. Skriv "fördröjning till" som filter i sökrutan. Under listan åtgärder väljer du den här åtgärden: **Fördröjning till**

   Den här åtgärden pausar ditt Logic app-arbetsflöde fram till angivet datum och tid.

   ![Lägg till åtgärden fördröj tills](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Ange tidsstämpeln för när du vill starta arbets flödet för Logic Apps. 

   När du klickar i rutan **tidsstämpel** visas den dynamiska innehålls listan så att du kan välja att välja utdata från utlösaren.

   ![Ange information om fördröjning till](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Lägg till andra åtgärder som du vill köra genom att välja mellan [hundratals färdiga kopplingar som är färdiga att använda](../connectors/apis-list.md). 

   Du kan till exempel inkludera en HTTP-åtgärd som skickar en begäran till en URL eller åtgärder som fungerar med lagrings köer, Service Bus köer eller Service Bus ämnen: 

   ![HTTP-åtgärd](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. När du är klar sparar du din Logic app.

   ![Spara din logikapp](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   När du sparar din Logic app för första gången visas slut punkts-URL: en för din Logic Apps-utlösare i rutan **http post-URL** . 
   När du vill anropa din Logi Kap par och skicka indata till din Logic app för bearbetning använder du denna URL som anrops mål.

   ![Spara slut punkts-URL för begäran](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Kopiera och spara slut punkts-URL: en så att du senare kan skicka en manuell begäran som utlöser din Logic app. 

## <a name="start-a-one-time-job"></a>Starta ett engångs jobb

Om du vill köra eller utlösa ett engångs jobb manuellt skickar du ett anrop till slut punkts-URL: en för din Logic Apps begär ande utlösare. I det här anropet anger du indata eller nytto last att skicka, som du kan ha beskrivit tidigare genom att ange ett schema. 

Med Postman-appen kan du till exempel skapa en POST-begäran med inställningarna som liknar det här exemplet och sedan välja **Skicka** för att göra begäran.

| Frågemetod | URL | Body | Huvuden |
|----------------|-----|------|---------|
| **POST** | <*slut punkt-URL*> | **outspädd** <p>**JSON (Application/JSON)** <p>I rutan **RAW** anger du den nytto last som du vill skicka i begäran. <p>**Obs!** Den här inställningen konfigurerar automatiskt **rubrik** värden. | **Nyckel**: Content-Type <br>**Värde**: Application/JSON |
|||||

![Skicka begäran om att utlösa din Logic app manuellt](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

När du har skickat samtalet visas svaret från din Logi Kap par under rutan **RAW** på fliken **brödtext** . 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Om du vill avbryta jobbet senare väljer du fliken **sidhuvud** . Hitta och kopiera huvudet **x-MS-Workflow-Run-ID** i svaret. 
>
> ![Svar](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Avbryta ett engångs jobb

I Logic Apps körs varje engångs jobb som en enda körnings instans för Logic app. Om du vill avbryta ett engångs jobb kan du använda [arbets flödes körning – Avbryt](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) i Logic Apps REST API. Ange [arbets flödets körnings-ID](#workflow-run-id)när du skickar ett anrop till utlösaren.

## <a name="schedule-recurring-jobs"></a>Schemalägg återkommande jobb

1. I [Azure Portal](https://portal.azure.com)skapar du en tom Logic-app i Logic App Designer. 

   De grundläggande stegen följer [snabb start: Skapa din första Logic-](../logic-apps/quickstart-create-first-logic-app-workflow.md)app.

1. I rutan Sök anger du "upprepning" som filter. Välj den här utlösaren i listan utlösare: **Frekvens** 

   ![Lägg till utlösare för upprepning](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Konfigurera ett mer avancerat schema, om du vill.

   ![Avancerat schema](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Mer information om avancerade alternativ för schemaläggning finns i [skapa och köra återkommande aktiviteter och arbets flöden med Azure Logic Apps](../connectors/connectors-native-recurrence.md).

1. Lägg till andra åtgärder genom att välja bland [hundratals färdiga att använda](../connectors/apis-list.md). Under utlösaren väljer du **Nästa steg**. Sök efter och välj de åtgärder som du vill använda.

   Du kan till exempel inkludera en HTTP-åtgärd som skickar en begäran till en URL eller åtgärder som fungerar med lagrings köer, Service Bus köer eller Service Bus ämnen: 

   ![HTTP-åtgärd](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. När du är klar sparar du din Logic app.

   ![Spara din logikapp](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Avancerad installation

Här följer andra sätt att anpassa dina jobb.

### <a name="retry-policy"></a>Återförsöksprincip

Om du vill styra hur en åtgärd försöker köra igen i din Logic-app när tillfälliga fel inträffar kan du ange principen för [återförsök](../logic-apps/logic-apps-exception-handling.md#retry-policies) i varje åtgärds inställningar, till exempel:

1. Öppna åtgärdens ellipser-menyn ( **...** ) och välj **Inställningar**.

   ![Öppna åtgärds inställningar](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Välj den princip för återförsök som du vill använda. Mer information om varje princip finns i [principer](../logic-apps/logic-apps-exception-handling.md#retry-policies)för återförsök.

   ![Välj princip för återförsök](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Hantera undantag och fel

Om standard åtgärden inte kan köras i Azure Scheduler kan du köra en alterative-åtgärd som åtgärdar fel tillståndet. I Azure Logic Apps kan du också utföra samma uppgift.

1. I Logic App Designer, ovanför den åtgärd som du vill hantera, flyttar du pekaren över pilen mellan stegen och väljer **Lägg till en parallell gren**. 

   ![Lägg till parallell gren](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Sök efter och välj den åtgärd som du vill köra i stället för den alternativa åtgärden.

   ![Lägg till parallell åtgärd](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Öppna menyn ellipser ( **...** ) på den alternativa åtgärden och välj **Konfigurera kör efter**.

   ![Konfigurera kör efter](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Avmarkera kryss rutan för egenskapen **har slutförts** . Välj följande egenskaper: **har misslyckats**, **hoppas över**och **har nått tids gränsen**

   ![Konfigurera egenskaper för kör efter](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. När du är klar väljer du **Klar**.

Mer information om undantags hantering finns i avsnittet [hantera fel och undantag-RunAfter](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

<a name="retire-date"></a>

**F**: När tas Azure Scheduler ur bruk? <br>
**S**: Azure Scheduler är schemalagt för att helt dra tillbaka den 31 december 2019. Viktiga steg som du bör vidta innan det här datumet och en detaljerad tids linje finns i [förlänga tiden för indragningen för Scheduler till 31 December 2019](https://azure.microsoft.com/en-us/updates/extending-retirement-date-of-scheduler/). Allmänna uppdateringar finns i [Azure updates-Scheduler](https://azure.microsoft.com/updates/?product=scheduler).

**F**: Vad händer med mina jobb samlingar och jobb när tjänsten dras om? <br>
**S**: Alla jobb samlingar och jobb i Schemaläggaren slutar att köras och tas bort från systemet.

**F**: Måste jag säkerhetskopiera eller utföra andra uppgifter innan du migrerar mina jobb i Schemaläggaren till Logic Apps? <br>
**S**: Vi rekommenderar att du alltid säkerhetskopierar ditt arbete. Kontrol lera att de Logic Apps som du har skapat körs som förväntat innan du tar bort eller inaktiverar dina jobb i Schemaläggaren. 

**F**: Finns det ett verktyg som kan hjälpa mig att migrera mina jobb från Scheduler till Logic Apps? <br>
**S**: Varje Scheduler-jobb är unikt, vilket innebär att det inte finns något verktyg med en storlek som passar alla. Men olika skript är tillgängliga så att du kan ändra dem efter dina behov. För skript tillgänglighet, kom tillbaka senare.

**F**: Var kan jag få support för att migrera mina jobb I Schemaläggaren? <br>
**S**: Här följer några exempel på hur du kan få support: 

**Azure Portal**

Om din Azure-prenumeration har en avgiftsbelagd Support plan kan du skapa en teknisk supportbegäran i Azure Portal. Annars kan du välja ett annat support alternativ.

1. På [Azure Portal](https://portal.azure.com) huvud menyn väljer du **Hjälp + Support**.

1. På **support** -menyn väljer du **ny supportbegäran**. Ange den här informationen om för din begäran:

   | Egenskap | Value |
   |---------|-------|
   | **Typ av problem** | **Produkt** |
   | **Prenumeration** | <*your-Azure-subscription*> |
   | **Tjänst** | Välj **Scheduler**Under **övervakning & hantering**. Om du inte hittar **Scheduler**väljer du **alla tjänster** först. |
   ||| 

1. Välj det support alternativ som du vill använda. Om du har en avgiftsbelagd Support plan väljer du **Nästa**.

**Community**

* [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Nästa steg

* [Skapa regelbundet aktiva uppgifter och arbets flöden med Azure Logic Apps](../connectors/connectors-native-recurrence.md)
* [Självstudier: Kontrol lera trafiken med en schema-baserad Logic app](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
