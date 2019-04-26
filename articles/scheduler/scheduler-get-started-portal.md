---
title: Skapa schemalagda jobb med Azure Scheduler på Azure-portalen | Microsoft-dokument
description: Lär dig att skapa, schemalägga och köra ditt första automatiserade jobb med Azure Scheduler på Azure-portalen
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 3b2cfc932c6322df8237ec7cdf820fc4242bfa72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531868"
---
# <a name="create-and-schedule-your-first-job-with-azure-scheduler---azure-portal"></a>Skapa och schemalägga ditt första jobb med Azure Scheduler – Azure-portalen

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras tillbaka. Om du vill schemalägga jobb kan du [testa Azure Logic Apps istället](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Den här självstudien visar hur du enkelt kan skapa och schemalägga jobb och därefter övervaka och hantera jobbet. 

Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

## <a name="create-job"></a>Skapa jobb

1. Logga in på [Azure Portal](https://portal.azure.com/).  

1. Välj **Skapa en resurs** på Azure-huvudmenyn. Ange ”Scheduler” i sökrutan. Välj **Scheduler** i resultatlistan och välj sedan **Skapa**.

   ![Skapa resurs för Scheduler](./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png)

   Nu ska du skapa ett jobb som skickar en GET-begäran till denna URL: `https://www.microsoft.com/` 

1. Under **Scheduler-jobb**, ange den här informationen:

   | Egenskap  | Exempelvärde | Beskrivning |
   |----------|---------------|-------------| 
   | **Namn** | getMicrosoft | Ange ett namn för ditt jobb | 
   | **Jobbsamling** | <*jobbsamlingsnamnet*> | Skapa en jobbsamling eller välj en befintlig samling. | 
   | **Prenumeration** | <*Azure-prenumerationsnamn*> | Azure-prenumerationens namn | 
   |||| 

1. Välj **Åtgärdsinställningar – konfigurera**, ange den här informationen och välj sedan **OK** när du är klar:

   | Egenskap  | Exempelvärde | Beskrivning |
   |----------|---------------|-------------| 
   | **Åtgärd** | **Http** | Typ av åtgärd som ska köras | 
   | **Metod** | **Hämta** | Anropsmetod | 
   | **URL** | **https://www.microsoft.com** | Mål-URL | 
   |||| 
   
   ![Definiera jobb](./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png)

1. Välj **Schema – konfigurera**, definiera schemat och välj sedan **OK** när du är klar:

   Även om du kan skapa ett engångsjobb ställer det här exemplet in ett återkommande schema.

   | Egenskap  | Exempelvärde | Beskrivning |
   |----------|---------------|-------------| 
   | **Frekvens** | **Återkommande** | Antingen ett engångsjobb eller ett återkommande jobb | 
   | **Starta den** | <*dagens-datum*> | Jobbets startdatum | 
   | **Detta ska upprepas varje** | **1 timme** | Ange intervall och frekvens för upprepningen | 
   | **Slut** | **Sluta** två dagar från dagens datum | Jobbets slutdatum | 
   | **Förskjutning från UTC** | **UTC +08.00** | Skillnaden i tid mellan Coordinated Universal Time (UTC) och din plats observerade tid | 
   |||| 

   ![Definiera schema](./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png)

1. När du är klar kan du välja **Skapa**.

   När du har skapat ditt jobb kommer Azure att distribuera det så att det visas på instrumentpanelen i Azure. 

1. När Azure visar ett meddelande om distributionen är klar väljer du **Fäst på instrumentpanelen**. Annars väljer du ikonen **Meddelanden** (klockan) i verktygsfältet i Azure och välj sedan **Fäst på instrumentpanelen**.

## <a name="monitor-and-manage-jobs"></a>Övervaka och hantera jobb

Välj ditt jobb på Azure-instrumentpanelen för att granska, övervaka och hantera det. Under **inställningar**, här är de områden som du kan granska och hantera för ditt jobb:

![Jobbinställningar](./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png)

Välj ett område för mer information om dessa områden:

* [**Egenskaper**](#properties)
* [**Åtgärdsinställningar**](#action-settings)
* [**Schema**](#schedule)
* [**Historik**](#history)
* [**Användare**](#users)

<a name="properties"></a>

### <a name="properties"></a>Egenskaper

Om du vill visa skrivskyddade egenskaper som beskriver hanteringsmetadata för jobbet väljer du **Egenskaper**.

![Visa jobbegenskaper](./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png)

<a name="action-settings"></a>

### <a name="action-settings"></a>Åtgärdsinställningar

För att ändra ditt jobbs avancerade inställningar, välj **Åtgärdsinställningar**. 

![Granska åtgärdsinställningar](./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png)

| Åtgärdstyp | Beskrivning | 
|-------------|-------------| 
| Alla typer | Du kan ändra inställningarna **återförsöksprincipen** och **felåtgärder**. | 
| HTTP och HTTPS | Du kan ändra **Metoden** till valfri metod som tillåts. Du kan också lägga till, ta bort eller ändra sidhuvudena och grundläggande autentiseringsinformation. | 
| Lagringskö| Du kan ändra lagringskontot, könamnet, SAS-token och brödtexten. | 
| Service Bus | Du kan ändra namnrymden, ämnes-/kösökvägen, autentiseringsinställningarna, transporttypen, meddelandeegenskaperna och meddelandetexten. | 
||| 

<a name="schedule"></a>

### <a name="schedule"></a>Schema

Om du har konfigurerat ett schema via guiden kan du ändra schemat, till exempel startdatum och tid, upprepningsschemat och slutdatum och tid för återkommande jobb.
Du kan också skapa [komplexa scheman och avancerad upprepning](scheduler-advanced-complexity.md).

Om du vill ändra vyn eller ändra schema för jobbets väljer du **Schema**:

![Visa jobbschema](./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png)

<a name="history"></a>

### <a name="history"></a>Historik

Om du vill visa mått om varje körning för ett valt projekt väljer du **Historik**. De här måtten anger värden i realtid om hälsotillstånd för jobbets status, antal återförsök, antal förekomster, starttid och sluttid.

![Visa jobbhistorik och mått](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png)

För att visa historik över informationen för varje körning, till exempel det fullständiga svaret för varje körning under **Historik**, väljer du varje körning. 

![Visa jobbhistorikinformation](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png)

<a name="users"></a>

### <a name="users"></a>Användare

Du kan hantera åtkomst till Azure Scheduler för varje användare på detaljerad nivå med hjälp av rollbaserad åtkomstkontroll (RBAC). Läs hur du ställer in åtkomst baserat på roller i [Hantera åtkomst med RBAC](../role-based-access-control/role-assignments-portal.md)

## <a name="next-steps"></a>Nästa steg

* Läs om [begrepp, terminologi och entitetshierarki](scheduler-concepts-terms.md)
* [Skapa komplexa scheman och avancerad upprepning](scheduler-advanced-complexity.md)
* Läs om [hög tillgänglighet och tillförlitlighet i Scheduler](scheduler-high-availability-reliability.md)
* Läs om [gränser, kvoter, standardvärden och felkoder](scheduler-limits-defaults-errors.md)
