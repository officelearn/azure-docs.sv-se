---
title: Migrera från Azure Scheduler till Azure Logic Apps
description: Lär dig hur du kan ersätta jobb i Azure Scheduler, som dras, med Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/20/2018
ms.openlocfilehash: 25ed66fd75301475542dbac8e8a01670ee37563c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531776"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrera Azure Scheduler-jobb med Azure Logic Apps

> [!IMPORTANT]
> Med Azure Logic Apps ersätter Azure Scheduler, som dras. Följ den här artikeln för att flytta till Azure Logic Apps i stället för att schemalägga jobb.

Den här artikeln visar hur du kan schemalägga enstaka och återkommande jobb genom att skapa automatiserade arbetsflöden med Azure Logic Apps, i stället för med Azure Scheduler. När du skapar schemalagda jobb med Logic Apps kan få du följande fördelar:

* Du inte behöver bekymra dig om konceptet med en *jobbsamling* eftersom varje logic app är en separat Azure-resurs.

* Du kan köra flera enstaka jobb med hjälp av en enkel logikapp.

* Azure Logic Apps-tjänsten stöder tidszon och sommartid (DST).

Mer information finns i [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md) eller försök att skapa din första logikapp i den här snabbstarten: [Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* Om du vill starta logikappen genom att skicka HTTP-begäranden, Använd ett verktyg som de [skrivbordsappen Postman](https://www.getpostman.com/apps).

## <a name="schedule-one-time-jobs"></a>Schemalägga engångsjobb

Du kan köra flera engångsjobb genom att skapa bara en enkel logikapp. 

### <a name="create-your-logic-app"></a>Skapa en logikapp

1. I den [Azure-portalen](https://portal.azure.com), skapa en tom logikapp i Logic App Designer. 

   De grundläggande stegen följer [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Ange ”när en http-begäran” i sökrutan som filter. Välj den här utlösaren från listan över utlösare: **När en HTTP-begäran tas emot** 

   ![Lägg till ”begär” utlösare](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Du kan också bifoga en JSON-schema, som hjälper dig att förstå strukturen för indata från den inkommande begäranden Logic App Designer och gör det enklare för dig att välja senare i arbetsflödet utdata för begäran-utlösaren.

   Ange om du vill ange ett schema, schemat i den **begär JSON-Brödtextsschema** rutan, till exempel: 

   ![Schemat för begäran](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Om du inte har ett schema, men du har en exempelnyttolast i JSON-format, kan du generera ett schema från den nyttolasten.

   1. I begäran-utlösaren väljer **Använd exempel för att generera schemat**.

   1. Under **Skriv eller klistra in en JSON-exempelnyttolast**, ange din exempelnyttolast och välj sedan **klar**, till exempel:

      ![Exempel på en nyttolast](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. Under utlösaren väljer **nästa steg**. 

1. I sökrutan anger du ”Fördröj tills” som filter. Välj den här åtgärden under åtgärder: **Fördröj tills**

   Den här åtgärden pausar logikapparbetsflödet fram till ett angivet datum och tid.

   ![Lägg till ”Fördröj tills” åtgärd](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Ange tidsstämpeln för när du vill starta den logikappens arbetsflöde. 

   När du klickar i den **tidsstämpel** den dynamiska innehållslistan visas rutan, så att du kan också välja utdata från utlösaren.

   ![Ange ”Fördröj tills” information](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Lägga till andra åtgärder som du vill köra genom att välja från [~ 200 + anslutningsappar](../connectors/apis-list.md). 

   Du kan till exempel inkludera en HTTP-åtgärd som skickar en begäran till en URL eller åtgärder som fungerar med Storage-köer, Service Bus-köer och Service Bus-ämnen: 

   ![HTTP-åtgärd](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. När du är klar sparar du din logikapp.

   ![Spara din logikapp](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   När du sparar logikappen för första gången slutpunkten URL: en för din logikapp-begäran-utlösaren visas i den **HTTP post-URL** box. 
   Om du vill att anropa logikappen och skicka indata till din logikapp för bearbetning kan använda den här URL: en som mål för anropet.

   ![Spara begäran utlösaren slutpunkts-URL](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Kopiera och spara den här slutpunkts-URL så att du kan skicka en manuell begäran som utlöser logikappen. 

## <a name="start-a-one-time-job"></a>Starta ett engångsjobb

Skicka ett anrop till slutpunkts-URL för din logikapp begäransutlösare för att manuellt köra eller utlösa ett engångsjobb. Ange indata eller för att skicka, som du kanske har beskrivit tidigare genom att ange ett schema i det här anropet. 

Till exempel med hjälp av Postman-appen, du kan skapa en POST-begäran med inställningarna som liknar det här exemplet och välj sedan **skicka** att utföra begäran.

| Frågemetod | URL | Innehåll | Rubriker |
|----------------|-----|------|---------| 
| **POST** | <*endpoint-URL*> | **rådata** <p>**JSON(application/json)** <p>I den **raw** anger nyttolasten som du vill skicka i begäran. <p>**Obs!** Den här inställningen automatiskt konfigurerar den **rubriker** värden. | **nyckeln**: Content-Type <br>**Värdet**: application/json
 |||| 

![Skicka begäran för att utlösa logikappen manuellt](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

När du har skickat anropet svaret från din logikapp visas under den **raw** rutan på den **brödtext** fliken. 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Om du vill avbryta jobbet senare väljer du den **rubriker** fliken. Hitta och kopiera den **x-ms-arbetsflöde-körning-id** huvudvärde i svaret. 
>
> ![Svar](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Avbryt ett engångsjobb

I Logic Apps, varje gång jobb som körs som en enkel logikapp som kör instans. Om du vill avbryta ett engångsjobb, kan du använda [Arbetsflödeskörningar - Avbryt](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) i Logic Apps REST API. När du skickar ett anrop till utlösaren, ange den [arbetsflöde körnings-ID](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Schemalägg återkommande jobb

### <a name="create-your-logic-app"></a>Skapa en logikapp

1. I den [Azure-portalen](https://portal.azure.com), skapa en tom logikapp i Logic App Designer. 

   De grundläggande stegen följer [Snabbstart: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. I sökrutan anger du ”återkommande” som filter. Välj den här utlösaren från listan över utlösare: **Frekvens** 

   ![Lägg till ”återkommande” utlösare](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Skapa en mer avancerad schema, om du vill.

   ![Avancerat schema](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Mer information om avancerade alternativ för schemaläggning finns [skapa och kör återkommande uppgifter och arbetsflöden med Azure Logic Apps](../connectors/connectors-native-recurrence.md)

1. Lägga till andra åtgärder som du vill genom att välja från [över 200 anslutningsappar](../connectors/apis-list.md). Under utlösaren väljer **nästa steg**. Hitta och välja vilka åtgärder du vill.

   Du kan till exempel inkludera en HTTP-åtgärd som skickar en begäran till en URL eller åtgärder som fungerar med Storage-köer, Service Bus-köer och Service Bus-ämnen: 

   ![HTTP-åtgärd](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. När du är klar sparar du din logikapp.

   ![Spara din logikapp](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Avancerade inställningar

Här finns andra sätt som du kan anpassa dina jobb.

### <a name="retry-policy"></a>Återförsöksprincip

Om du vill styra hur en åtgärd som försöker igen i din logikapp när återkommande fel inträffa, kan du ange den [återförsöksprincip](../logic-apps/logic-apps-exception-handling.md#retry-policies) i inställningarna för varje åtgärd, till exempel:

1. Öppna åtgärdens (**...** ) menyn och välj **inställningar**.

   ![Öppna åtgärdsinställningar](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Välj den återförsöksprincip som du vill. Mer information om varje princip finns i [Återförsöksprinciper](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Välj återförsöksprincip](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Hantera undantag och fel

Om standardåtgärden som inte kan köras, i Azure Scheduler kan du köra en alternativt åtgärd som åtgärdar felet. Du kan också utföra samma uppgift i Azure Logic Apps.

1. I Logic App Designer ovanför åtgärden du vill hantera, flyttar du pekaren över pilen mellan steg och välj och **lägga till en parallell gren**. 

   ![Lägg till parallell gren](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Hitta och välj den åtgärd som du vill köra i stället som alternativ åtgärd.

   ![Lägg till parallell åtgärd](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. På den alternativa åtgärden, öppna den (**...** ) menyn och välj **konfigurera körning efter**.

   ![Konfigurera körningen efter](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Avmarkera kryssrutan för de **lyckas** egenskapen. Välj de här egenskaperna: **misslyckades**, **hoppas över**, och **har nått sin tidsgräns**

   ![Konfigurera egenskaper för ”körning efter”](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. När du är klar väljer du **Klar**.

Läs mer om undantagshantering i [hantera fel och undantag - egenskapen för RunAfter](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

<a name="retire-date"></a> 

**FRÅGOR OCH**: När är Azure Scheduler tas ur bruk? <br>
**S**: Azure Scheduler är schemalagd att dra tillbaka den 30 September 2019.

**FRÅGOR OCH**: Vad händer med Mina Scheduler-jobbsamlingar och jobben när tjänsten drar tillbaka? <br>
**S**: Alla Scheduler-jobbsamlingar och jobben tas bort från systemet.

**FRÅGOR OCH**: Måste jag säkerhetskopiera eller utföra andra uppgifter innan du migrerar min Scheduler-jobb till Logic Apps? <br>
**S**: Du bör alltid säkerhetskopiera ditt arbete. Kontrollera att logikappar som du skapade körs som förväntat innan du tar bort eller inaktivera Scheduler-jobb. 

**FRÅGOR OCH**: Finns det ett verktyg som kan hjälpa mig att migrera Mina jobb från Scheduler till Logic Apps? <br>
**S**: Varje Scheduler-jobb är unikt, så ett verktyg för enkel inte finns. Men kan olika skript som du kan ändra för dina behov. För skriptet tillgänglighet Kom tillbaka senare.

**FRÅGOR OCH**: Var kan jag få support för att migrera min Scheduler-jobb? <br>
**S**: Här följer några metoder för att få support: 

**Azure Portal**

Om din Azure-prenumeration har en plan för betald support, kan du skapa en förfrågan om teknisk support i Azure-portalen. Annars väljer du ett olika supportalternativ.

1. På den [Azure-portalen](https://portal.azure.com) Huvudmeny väljer **hjälp + support**.

1. Under **stöder**väljer **ny supportbegäran**. Ge den här informationen för din förfrågan:

   | Inställning | Värde |
   |---------|-------|
   | **Typ av problem** | **Teknisk** | 
   | **Prenumeration** | <*your-Azure-subscription*> | 
   | **Tjänst** | Under **övervakning och hantering**väljer **Scheduler**. | 
   ||| 

1. Välj önskat supportalternativ. Om du har ett betalt supportavtal väljer **nästa**.

**Community**

* [Azure Logic Apps-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Nästa steg

* [Skapa regelbundet pågående aktiviteter och arbetsflöden med Azure Logic Apps](../connectors/connectors-native-recurrence.md)
* [Självstudie: Kontrollera trafik med en schemabaserad logikapp](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)