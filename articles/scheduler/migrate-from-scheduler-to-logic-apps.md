---
title: Migrera från Azure Scheduler till Azure Logic Apps
description: Lär dig hur du kan ersätta jobb i Azure Scheduler, som dras tillbaka, med Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 02/29/2020
ms.openlocfilehash: 90c3cc2e096b9b58465987bc53f718c5d06c6203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899089"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrera Azure Scheduler-jobb till Azure Logic Apps

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som [dras tillbaka](#retire-date). Om du vill fortsätta arbeta med de jobb som du har konfigurerat i Scheduler migrerar du till Azure Logic Apps så snart som möjligt genom att följa den här artikeln. 
>
> Scheduler är inte längre tillgängligt i Azure-portalen, men [REST API-](/rest/api/scheduler) och [Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md) förblir tillgängliga just nu så att du kan hantera dina jobb och jobbsamlingar.

Den här artikeln visar hur du kan schemalägga engångsjobb och återkommande jobb genom att skapa automatiserade arbetsflöden med Azure Logic Apps i stället för med Azure Scheduler. När du skapar schemalagda jobb med Logic Apps får du följande fördelar:

* Skapa ditt jobb med hjälp av en visuell designer och [färdiga kopplingar](../connectors/apis-list.md) från hundratals tjänster, till exempel Azure Blob Storage, Azure Service Bus, Office 365 Outlook och SAP.

* Hantera varje schemalagt arbetsflöde som en förstklassig Azure-resurs. Du behöver inte oroa dig för konceptet med en *jobbsamling* eftersom varje logikapp är en enskild Azure-resurs.

* Kör flera engångsjobb med hjälp av en enda logikapp.

* Ange scheman som stöder tidszoner och automatiskt anpassar sig till sommartid (DST).

Mer information finns i [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md) [Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Om du vill utlösa logikappen genom att skicka HTTP-begäranden använder du ett verktyg som [postman-skrivbordsappen](https://www.getpostman.com/apps).

## <a name="migrate-by-using-a-script"></a>Migrera med hjälp av ett skript

Varje Scheduler-jobb är unikt, så det finns inget verktyg som passar alla för att migrera Scheduler-jobb till Azure Logic Apps. Du kan dock [redigera skriptet](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration) så att det uppfyller dina behov.

## <a name="schedule-one-time-jobs"></a>Schemalägga engångsjobb

Du kan köra flera engångsjobb genom att bara skapa en enda logikapp.

1. Skapa en tom logikapp i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

   De grundläggande stegen följer du [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. I sökrutan anger `when a http request` du för att hitta utlösaren Begäran. Välj den här utlösaren i listan utlösare: **När en HTTP-begäran tas emot**

   ![Lägga till "Begäran"-utlösare](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. För utlösaren Begäran kan du också ange ett JSON-schema, vilket hjälper Logic App Designer att förstå strukturen för de indata som ingår i inkommande anrop till utlösaren begäran och gör utdata lättare för dig att välja senare i arbetsflödet.

   I rutan **Begär brödtext JSON Schema** anger du schemat, till exempel:

   ![Schema för begär](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Om du inte har ett schema, men du har ett exempel nyttolast i JSON-format, kan du generera ett schema från den nyttolasten.

   1. I utlösaren Begäran väljer du **Använd exempelnyttolast för att generera schema**.

   1. Under **Ange eller klistra in ett prov på JSON-nyttolast**anger du exempelnyttolasten och väljer **Klar**, till exempel:

      ![Prov nyttolast](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

      ```json
      {
         "runat": "2012-08-04T00:00Z",
         "endpoint": "https://www.bing.com"
      }
      ```

1. Under utlösaren väljer du **Nästa steg**.

1. Ange som filter `delay until` i sökrutan. Under åtgärdslistan väljer du den här åtgärden: **Fördröja tills**

   Den här åtgärden pausar logikappens arbetsflöde tills ett angivet datum och en viss tid.

   ![Lägg till åtgärden "Fördröjning till"](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Ange tidsstämpeln för när du vill starta logikappens arbetsflöde.

   När du klickar i rutan **Tidsstämpel** visas listan med dynamiskt innehåll så att du kan välja en utdata från utlösaren.

   ![Ange "Fördröjning till" detaljer](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Lägg till andra åtgärder som du vill köra genom att välja [från hundratals färdiga kopplingar](../connectors/apis-list.md).

   Du kan till exempel inkludera en HTTP-åtgärd som skickar en begäran till en URL eller åtgärder som fungerar med lagringsköer, servicebussköer eller servicebussavsnitt:

   ![HTTP-åtgärd](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. När du är klar sparar du logikappen.

   ![Spara din logikapp](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   När du sparar logikappen för första gången visas slutpunktsadressen för logikappens begärandeutlösare i rutan **HTTP POST-URL.** När du vill ringa logikappen och skicka indata till logikappen för bearbetning använder du den här URL:en som samtalsmål.

   ![Url till slutpunkt för spara begäran trigger](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Kopiera och spara den här slutpunkts-URL:en så att du senare kan skicka en manuell begäran som utlöser logikappen.

## <a name="start-a-one-time-job"></a>Starta ett engångsjobb

Om du vill köra eller utlösa ett engångsjobb manuellt skickar du ett anrop till slutpunkts-URL:en för logikappens begärandeutlösare. I det här anropet anger du den indata eller nyttolast som ska skickas, som du kanske har beskrivit tidigare genom att ange ett schema.

Med hjälp av Postman-appen kan du till exempel skapa en POST-begäran med de inställningar som liknar det här exemplet och sedan välja **Skicka** för att göra begäran.

| Metod för begäran | URL | Innehåll | Rubriker |
|----------------|-----|------|---------|
| **POST** | <*url till slutpunkt*> | **Raw** <p>**JSON(ansökan/json)** <p>I rutan **Rå** anger du den nyttolast som du vill skicka i begäran. <p>**Den**här inställningen konfigurerar automatiskt **huvudvärdena.** | **Nyckel**: Innehållstyp <br>**Värde**: ansökan/json |
|||||

![Skicka begäran om att manuellt utlösa logikappen](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

När du har skickat samtalet visas svaret från logikappen under rutan **Rå** på fliken **Brödtext.** 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Om du vill avbryta jobbet senare väljer du fliken **Rubriker.** Sök och kopiera huvudvärdet **x-ms-arbetsflöde-run-id** i svaret. 
>
> ![Svar](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Avbryta ett engångsjobb

I Logic Apps körs varje engångsjobb som en enda logikappkörningsinstans. Om du vill avbryta ett engångsjobb kan du använda [Arbetsflödeskörningar - Avbryt](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) i REST-API:et för logic apps. När du skickar ett samtal till utlösaren anger du [arbetsflödeskörnings-ID.](#workflow-run-id)

## <a name="schedule-recurring-jobs"></a>Schemalägga återkommande jobb

1. Skapa en tom logikapp i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

   De grundläggande stegen följer du [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Skriv "återkommande" som filter i sökrutan. Välj den här utlösaren i listan utlösare: **Återkommande**

   ![Lägga till utlösaren "Återkommande"](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Ställ in ett mer avancerat schema, om du vill.

   ![Avancerat schema](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Mer information om avancerade schemaläggningsalternativ finns i [Skapa och köra återkommande uppgifter och arbetsflöden med Azure Logic Apps](../connectors/connectors-native-recurrence.md).

1. Lägg till andra åtgärder som du vill ha genom att välja [från hundratals färdiga att använda](../connectors/apis-list.md). Under utlösaren väljer du **Nästa steg**. Sök efter och välj de åtgärder du vill ha.

   Du kan till exempel inkludera en HTTP-åtgärd som skickar en begäran till en URL eller åtgärder som fungerar med lagringsköer, servicebussköer eller servicebussavsnitt:

   ![HTTP-åtgärd](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. När du är klar sparar du logikappen.

   ![Spara din logikapp](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Avancerad installation

Här är andra sätt att anpassa dina jobb.

### <a name="retry-policy"></a>Återförsöksprincip

Om du vill styra hur en åtgärd försöker köras igen i logikappen när återkommande fel inträffar kan du ange [återförsöksprincipen](../logic-apps/logic-apps-exception-handling.md#retry-policies) i varje åtgärds inställningar, till exempel:

1. Öppna åtgärdens ellipser (**...**) och välj **Inställningar**.

   ![Öppna åtgärdsinställningar](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Välj den återförsöksprincip som du vill använda. Mer information om varje princip finns i [Återförsöksprinciper](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Välj princip för återförsök](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Hantera undantag och fel

Om standardåtgärden inte körs i Azure Scheduler kan du köra en ändringsåtgärd som åtgärdar felvillkoret. I Azure Logic Apps kan du också utföra samma uppgift.

1. I Logic App Designer, ovanför den åtgärd som du vill hantera, flyttar du pekaren över pilen mellan stegen och väljer **Lägg till en parallell gren**.

   ![Lägg till parallell gren](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Sök efter och välj den åtgärd som du vill köra i stället som alternativ åtgärd.

   ![Lägg till parallell åtgärd](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. På den alternativa åtgärden öppnar du ellipserna (**...**) menyn och väljer **Konfigurera körning efter**.

   ![Konfigurera körning efter](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Rensa rutan för egenskapen **är lyckad.** Markera dessa egenskaper: **har misslyckats**, **hoppas över**och **har timeats ut**

   ![Ställ in egenskaper för "kör efter"](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. När du är klar väljer du **Klar**.

Mer information om [undantagshantering](../logic-apps/logic-apps-exception-handling.md#control-run-after-behavior)finns i Hantera fel och undantag - egenskap RunAfter .

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

<a name="retire-date"></a>

**F**: När går Azure Scheduler i pension? <br>
**S**: Azure Scheduler är schemalagd att gå i pension helt den 31 december 2019. Viktiga steg att ta före detta datum och en detaljerad tidslinje finns i [Förlänga avlösningsdatumet för Scheduler till den 31 december 2019](https://azure.microsoft.com/updates/extending-retirement-date-of-scheduler/). Allmänna uppdateringar finns i [Azure-uppdateringar - Scheduler](https://azure.microsoft.com/updates/?product=scheduler).

**F**: Vad händer med mina jobbsamlingar och jobb efter att tjänsten har går i pension? <br>
**S**: Alla Schemaläggare jobbsamlingar och jobb slutar köras och tas bort från systemet.

**F**: Måste jag säkerhetskopiera eller utföra andra uppgifter innan jag migrerar mina Scheduler-jobb till Logic Apps? <br>
**A**: Som en bästa praxis, alltid säkerhetskopiera ditt arbete. Kontrollera att logikapparna som du har skapat körs som förväntat innan du tar bort eller inaktiverar scheduler-jobben.

**F:** Finns det ett verktyg som kan hjälpa mig att migrera mina jobb från Scheduler till Logic Apps? <br>
**S:** Varje Scheduler-jobb är unikt, så ett verktyg som passar alla inte finns. Baserat på dina behov kan du dock [redigera det här skriptet för att migrera Azure Scheduler-jobb till Azure Logic Apps](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration).

**F**: Var kan jag få stöd för att migrera mina Scheduler-jobb? <br>
**S:** Här är några sätt att få support:

**Azure-portal**

Om din Azure-prenumeration har en betald supportplan kan du skapa en begäran om teknisk support i Azure-portalen. Annars kan du välja ett annat supportalternativ.

1. På [huvudmenyn i Azure-portalen](https://portal.azure.com) väljer du **Stöd + stöd**för Hjälp + .

1. Välj **Ny supportbegäran**på **Support-menyn** . Ange den här informationen om din begäran:

   | Egenskap | Värde |
   |---------|-------|
   | **Typ av problem** | **Teknik** |
   | **Prenumeration** | <*din Azure-prenumeration*> |
   | **Tjänst** | Välj **Schemaläggaren** **under Övervakning & Management**. Om du inte hittar **Scheduler**väljer du **Alla tjänster** först. |
   ||| 

1. Välj önskat supportalternativ. Om du har en betald supportplan väljer du **Nästa**.

**Community**

* [Forum för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stackspill](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Nästa steg

* [Skapa regelbundet gående uppgifter och arbetsflöden med Azure Logic Apps](../connectors/connectors-native-recurrence.md)