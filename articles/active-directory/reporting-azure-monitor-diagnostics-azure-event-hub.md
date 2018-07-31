---
title: Självstudie – Strömma Azure Active Directory-loggar till en Azure-händelsehubb (förhandsversion) | Microsoft Docs
description: Lär dig hur du konfigurerar Azure-diagnostik för att skicka Azure Active Directory-loggar till en händelsehubb (förhandsversion)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c4f5bf1b49d7f59b2bb938a7ddc53b96c1d354ef
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240272"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub-preview"></a>Självstudie: Strömma Azure Active Directory-loggar till en Azure-händelsehubb (förhandsversion)

I den här självstudien får du lära dig hur du konfigurerar Azure Monitor-diagnostikinställningar för att strömma Azure Active Directory-loggar till en Azure-händelsehubb. Använd den här mekanismen för att integrera dina loggar med SIEM-verktyg från tredje part såsom Splunk och QRadar.

## <a name="prerequisites"></a>Nödvändiga komponenter 

Du behöver:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [registrera dig för en kostnadsfri utvärdering](https://azure.microsoft.com/free/).
* En Azure Active Directory-klientorganisation
* En användare som är global administratör eller säkerhetsadministratör för den klientorganisationen
* En Event Hubs-namnrymd och en händelsehubb i din Azure-prenumeration. Lär dig hur du [skapar en sådan här](https://docs.microsoft.com/azure/event-hubs/event-hubs-create.md).

## <a name="archive-logs-to-event-hub"></a>Arkivera loggar till händelsehubb

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Klicka på **Azure Active Directory** -> **Aktivitet** -> **Spårningsloggar**. 
3. Klicka på **Exportera inställningar** för att öppna bladet Diagnostikinställningar. Klicka på **Redigera inställning** om du vill ändra befintliga inställningar eller klicka på **Lägg till diagnostikinställning** om du vill lägga till en ny. Du kan ha upp till tre inställningar. 
    ![Exportera inställningar](./media/reporting-azure-monitor-diagnostics-azure-event-hub/ExportSettings.png "Exportera inställningar")

4. Markera kryssrutan **Strömma till en händelsehubb** och klicka på **Event Hub/Configure** (Händelsehubb/Konfigurera).
5. Välj en Azure-prenumeration och den Event Hubs-namnrymd som du vill dirigera loggarna till. Både prenumerationen och Event Hubs-namnrymden måste vara associerade med den Active Directory-klientorganisation som loggarna strömmar från. Du kan även ange en händelsehubb i Event Hubs-namnrymden som loggar ska skickas till. Om ingen händelsehubb anges skapas en händelsehubb i namnrymden med standardnamnet **insights-logs-audit**.
6. Klicka på **OK** för att avsluta händelsehubbkonfigurationen.
7. Markera kryssrutan **AuditLogs** (Spårningsloggar) för att skicka spårningsloggar till lagringskontot. 
8. Markera kryssrutan **SignInLogs** (Inloggningsloggar) för att skicka inloggningsloggar till lagringskontot.
9. Klicka på **Spara** för att spara inställningen.
    ![Diagnostikinställningar](./media/reporting-azure-monitor-diagnostics-azure-event-hub/DiagnosticSettings.png "Diagnostikinställningar")

10. Efter ungefär 15 minuter kontrollerar du att händelserna visas i din händelsehubb. För att göra detta navigerar du till händelsehubben från portalen och kontrollerar att antalet **inkommande meddelanden** är större än noll. 
    ![Granskningsloggar](./media/reporting-azure-monitor-diagnostics-azure-event-hub/InsightsLogsAudit.png "Granskningsloggar")


## <a name="access-data-from-event-hubs"></a>Komma åt data från Event Hubs

När data visas i händelsehubben kan du komma åt dem på två sätt.

* **Konfigurera ett SIEM-verktyg som stöds för att läsa data**: De flesta verktyg kräver händelsehubbens anslutningssträng och vissa behörigheter till din Azure-prenumeration för att kunna läsa data från händelsehubben. Här är en ofullständig lista över verktyg med Azure Monitor-integrering:
    1. **Splunk**: Information om hur du integrerar Azure AD-loggar med Splunk finns på sidan om [hur du integrerar Azure Active Directory-loggar med Splunk med hjälp av Azure Monitor](reporting-azure-monitor-diagnostics-splunk-integration.md).
    
    2. **IBM QRadar**: Microsoft Azure DSM- och Microsoft Azure Event Hub-protokollet är tillgängliga för nedladdning från [IBM:s supportwebbplats](http://www.ibm.com/support). Du kan [lära dig mer om integrering med Azure här](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
    
    3. **SumoLogic**: Följ [dessa instruktioner](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub) för att konfigurera SumoLogic att använda data från en händelsehubb. 

* **Konfigurera anpassade verktyg för att läsa data**: Om din aktuella SIEM inte stöds i Azure Monitor-diagnostiken ännu kan du konfigurera anpassade verktyg med hjälp av Event Hub-API:er. Mer information finns på sidan om [Event Hub-API:er](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph).


## <a name="next-steps"></a>Nästa steg

* [Integrera Azure Active Directory-loggar med Splunk med hjälp av Azure Monitor-diagnostik](reporting-azure-monitor-diagnostics-splunk-integration.md)
* [Tolka schema för spårningsloggar i Azure Monitor-diagnostik](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Tolka schema för inloggningsloggar i Azure Monitor-diagnostik](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)