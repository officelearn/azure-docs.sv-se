---
title: Skapa och hantera åtgärdsgrupper i Azure portal
description: Lär dig hur du skapar och hanterar åtgärdsgrupper i Azure-portalen.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 3d06024b7fa4356d4ad0e8b52c45c2ead62ef784
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60778384"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Skapa och hantera åtgärdsgrupper i Azure portal
## <a name="overview"></a>Översikt ##
En åtgärdsgrupp är en samling av aviseringsinställningarna som definieras av ägaren av en Azure-prenumeration. Azure Monitor och Service Health-aviseringar använda åtgärdsgrupper för att meddela användare att en avisering har utlösts. Olika typer av aviseringar kan använda samma åtgärdsgruppen eller annan åtgärdsgrupper beroende på användarens krav. Du kan konfigurera upp till 2 000 åtgärdsgrupper i en prenumeration.

Du kan konfigurera en åtgärd för att meddela en person med e-post eller SMS, de får en bekräftelse som anger de har lagts till i åtgärdsgruppen.

Den här artikeln visar hur du skapar och hanterar åtgärdsgrupper i Azure-portalen.

Varje åtgärd består av följande egenskaper:

* **Namn**: En unik identifierare i åtgärdsgruppen.  
* **Åtgärdstyp**: Utföra åtgärden. Exempel är att skicka en röst-anrop, SMS, e-postmeddelandet, eller utlösa olika typer av automatiska åtgärder. Se typer senare i den här artikeln. 
* **Information om**: Den motsvarande information som varierar beroende på *åtgärdstyp*. 

Information om hur du använder Azure Resource Manager-mallar för att konfigurera åtgärdsgrupper finns i [åtgärd grupp Resource Manager-mallar](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Skapa en grupp med hjälp av Azure-portalen ##
1. I den [portal](https://portal.azure.com)väljer **övervakaren**. Den **övervakaren** fönstret konsoliderar alla dina övervakningsinställningar och -data i en vy.

    ![”Övervakningstjänsten”](./media/action-groups/home-monitor.png)
1. Välj **aviseringar** därefter **hantera åtgärdsgrupper**.

    ![Hantera åtgärdsgrupper knappen](./media/action-groups/manage-action-groups.png)
1. Välj **Lägg till åtgärdsgrupp**, och Fyll i fälten.

    ![Kommandot ”Lägg till åtgärdsgrupp”](./media/action-groups/add-action-group.png)
1. Ange ett namn i den **namn på åtgärdsgrupp** och ange ett namn i den **kortnamnet** box. Det korta namnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.

      ![Dialogrutan Lägg till åtgärdsgrupp ”](./media/action-groups/action-group-define.png)

1. Den **prenumeration** rutan autofills med din aktuella prenumeration. Den här prenumerationen är där åtgärdsgruppen sparas.

1. Välj den **resursgrupp** i åtgärdsgruppen har sparats.

1. Definiera en lista med åtgärder. Ange följande för varje åtgärd:

    a. **Namn**: Ange en unik identifierare för den här åtgärden.

    b. **Åtgärdstyp**: Välj e-post/SMS/Push/röst, Logikapp, Webhook, ITSM eller Automation-Runbook.

    c. **Information om**: Baserat på typen av, ange ett telefonnummer, e-postadress, webhook URI, Azure-app, ITSM-anslutningen eller Automation-runbook. För ITSM-åtgärden dessutom ange **arbetsobjekt** och kräver att andra fält ITSM-verktyg.

1. Välj **OK** skapa åtgärdsgruppen.

## <a name="manage-your-action-groups"></a>Hantera din åtgärdsgrupper ##
När du har skapat en åtgärdsgrupp den syns i den **åtgärdsgrupper** delen av den **övervakaren** fönstret. Välj åtgärdsgrupp som du vill hantera att:

* Lägga till, redigera eller ta bort åtgärder.
* Ta bort åtgärdsgruppen.

## <a name="action-specific-information"></a>Information om specifika
> [!NOTE]
> Se [prenumerationstjänsten för övervakning](https://docs.microsoft.com/azure/azure-subscription-service-limits#monitor-limits) för numeriska gränser för vart och ett av objekten nedan.  

**Azure-app Push** – du kan ha ett begränsat antal åtgärder för Azure i en åtgärdsgrupp. För tillfället stöder Azure-app-åtgärden endast ServiceHealth aviseringar. Någon annan typ av avisering ignoreras. Se [konfigurera aviseringar när en avisering om tjänstens hälsa publiceras](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

**E-post** -e-postmeddelanden ska skickas från följande e-postadresser. Kontrollera att din e-filtrering har konfigurerats på rätt sätt
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Du kan ha ett begränsat antal e poståtgärder i en åtgärdsgrupp. Se den [begränsar information frekvensbegränsningen](./../../azure-monitor/platform/alerts-rate-limiting.md) artikel

**ITSM** – du kan ha ett begränsat antal begränsat antal ITSM-åtgärder i en åtgärdsgrupp. ITSM-åtgärden kräver en ITSM-anslutningen. Lär dig hur du skapar en [ITSM-anslutningen](../../azure-monitor/platform/itsmc-overview.md).

**Logic App** – du kan ha ett begränsat antal Logic App-åtgärder i en åtgärdsgrupp.

**Funktionen App** -funktionen nycklar för Funktionsappar som har konfigurerats som åtgärder läses via API-funktioner, som för närvarande kräver v2 funktionsappar att konfigurera appinställningen ”AzureWebJobsSecretStorageType” till ”filer”. Mer information finns i [ändras till hantering av nycklar i Functions V2]( https://aka.ms/funcsecrets).

**Runbook** – du kan ha ett begränsat antal Runbook-åtgärder i en åtgärdsgrupp. Referera till den [Azure-prenumerationstjänsten](../../azure-subscription-service-limits.md) för gränser för Runbook-nyttolaster.

**SMS** – du kan ha ett begränsat antal SMS åtgärder i en åtgärdsgrupp. Se även de [begränsar information frekvensbegränsningen](./../../azure-monitor/platform/alerts-rate-limiting.md) och [SMS Avisera beteende](../../azure-monitor/platform/alerts-sms-behavior.md) ytterligare viktig information. 

**Röst** – du kan ha ett begränsat antal röst åtgärder i en åtgärdsgrupp. Se den [begränsar information frekvensbegränsningen](./../../azure-monitor/platform/alerts-rate-limiting.md) artikeln.

**Webhook** – du kan ha ett begränsat antal Webhook-åtgärder i en åtgärdsgrupp. Webhooks görs med hjälp av följande regler. Webhook-anrop görs ett nytt högst 2 gånger när följande HTTP-Statuskoder returneras: 408, 429, 503, 504, eller om HTTP-slutpunkten inte svarar. Det första återförsöket görs efter 10 sekunder. Andra återförsök sker efter 100 sekunder. Efter två fel anropar inga åtgärdsgrupp slutpunkten i 30 minuter. 

Käll-IP-adressintervall
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

För att ta emot konfigurerar uppdateringar om ändringar av dessa IP-adresser som vi rekommenderar att du en [Tjänstehälsa för avisering, som övervakar för informationsmeddelanden om tjänsten åtgärdsgrupper.


## <a name="next-steps"></a>Nästa steg ##

* Läs mer om [SMS Avisera beteende](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Få en [förståelse för avisering webhook för aktivitetslogg](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Läs mer om [ITSM-anslutningsprogram](../../azure-monitor/platform/itsmc-overview.md)
* Läs mer om [hastighetsbegränsning](../../azure-monitor/platform/alerts-rate-limiting.md) på aviseringar.
* Hämta en [översikt över aktivitetsloggaviseringar](../../azure-monitor/platform/alerts-overview.md), och lär dig hur du får aviseringar.  
* Lär dig hur du [konfigurera aviseringar när en avisering om tjänstens hälsa publiceras](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

