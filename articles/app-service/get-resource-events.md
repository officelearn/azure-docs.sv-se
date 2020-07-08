---
title: Hämta resurs händelser i Azure App Service
description: Lär dig hur du hämtar resurs händelser via aktivitets loggar och Event Grid i App Service-appen.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: 7075e3eacc85198e22a9aa0e53f67a22416b2678
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83649069"
---
# <a name="get-resource-events-in-azure-app-service"></a>Hämta resurs händelser i Azure App Service

Azure App Service innehåller inbyggda verktyg för att övervaka resursernas status och hälsa. Resurs händelser hjälper dig att förstå eventuella ändringar som har gjorts i dina underliggande webb program resurser och vidta åtgärder vid behov. Exempel på händelser är: skalning av instanser, uppdateringar av program inställningar, omstart av webbappen och många fler. I den här artikeln får du lära dig hur du visar [Azures aktivitets loggar](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) och aktiverar [Event Grid](https://docs.microsoft.com/azure/event-grid/) för att övervaka resurs händelser som är relaterade till din app service webbapp.

> [!NOTE]
> App Service-integrering med Event Grid är i för **hands version**. [Visa meddelandet för mer information.](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>Visa Azures aktivitets loggar
Azure aktivitets loggar innehåller resurs händelser som har avsänts av åtgärder som vidtagits på resurserna i din prenumeration. Både användar åtgärderna i Azure Portal-och Azure Resource Manager-mallarna bidrar till de händelser som registrerats av aktivitets loggen. 

Azure aktivitets loggar för App Service information som:
- vilka åtgärder utfördes på resurserna (t. ex.: App Service planer)
- Vem startade åtgärden
- När åtgärden utfördes
- åtgärdens status
- egenskaps värden som hjälper dig att undersöka åtgärden

### <a name="what-can-you-do-with-azure-activity-logs"></a>Vad kan du göra med Azure aktivitets loggar?

Azure aktivitets loggar kan frågas med hjälp av Azure Portal, PowerShell, REST API eller CLI. Du kan skicka loggarna till ett lagrings konto, Händelsehubben och Log Analytics. Du kan också analysera dem i Power BI eller skapa aviseringar för att hålla uppdaterad om resurs händelser.

[Visa och hämta Azure aktivitets logg händelser.](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

## <a name="ship-activity-logs-to-event-grid"></a>Skicka aktivitets loggar till Event Grid

Medan aktivitets loggarna är användarspecifika är det en ny [Event Grid](https://docs.microsoft.com/azure/event-grid/) -integration med App Service (för hands version) som loggar både användar åtgärder och automatiserade händelser. Med Event Grid kan du konfigurera en hanterare för att reagera på dessa händelser. Använd exempelvis Event Grid för att direkt utlösa att en funktion utan server kör bildanalys varje gång ett nytt foto läggs till i en bloblagringscontainer.

Du kan även använda Event Grid med Logic Apps för att bearbeta data var som helst, utan att skriva någon kod. Event Grid kopplar samman datakällor och händelsehanterare. Använd exempelvis Event Grid för att direkt utlösa att en funktion utan server kör bildanalys varje gång ett nytt foto läggs till i en bloblagringscontainer.

[Visa egenskaper och schema för Azure App Service händelser.](https://docs.microsoft.com/azure/event-grid/event-schema-app-service)

## <a name="next-steps"></a><a name="nextsteps"></a>Nästa steg
* [Fråga efter loggar med Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Övervaka Azure App Service](web-sites-monitor.md)
* [Felsöka Azure App Service i Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analysera app-loggar i HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
