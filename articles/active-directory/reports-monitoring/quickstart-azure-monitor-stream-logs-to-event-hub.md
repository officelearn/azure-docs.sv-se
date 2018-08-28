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
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: f6f3d9625e5469823a9c0c6eb6b549a6eaaeb0e9
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "41919833"
---
# <a name="tutorial-stream-azure-ad-logs-to-an-azure-event-hub-preview"></a>Självstudie: Strömma Azure AD-loggar till en Azure-händelsehubb (förhandsversion)

I den här självstudien får du lära dig hur du konfigurerar Azure Monitor-diagnostikinställningar för att strömma Azure Active Directory-loggar (Azure AD) till en Azure-händelsehubb. Använd den här mekanismen för att integrera dina loggar med SIEM-verktyg (Säkerhetsinformation och händelsehantering) från tredje part, till exempel Splunk och QRadar.

## <a name="prerequisites"></a>Nödvändiga komponenter 

Om du vill använda den här funktionen behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [registrera dig för en kostnadsfri utvärdering](https://azure.microsoft.com/free/).
* En Azure AD-klientorganisation.
* En användare som är *global administratör* eller *säkerhetsadministratör* för Azure AD-klientorganisationen.
* En Event Hubs-namnrymd och en händelsehubb i din Azure-prenumeration. Lär dig att [skapa en händelsehubb](https://docs.microsoft.com/azure/event-hubs/event-hubs-create.md).

## <a name="archive-logs-to-an-event-hub"></a>Arkivera loggar till en händelsehubb

1. Logga in på [Azure Portal](https://portal.azure.com). 

2. Välj **Azure Active Directory** > **Aktivitet** > **Spårningsloggar**. 

3. Välj **Exportinställningar**.  
    
4. I fönsterrutan **Diagnostikinställningar** gör du något av följande:
    * Om du vill ändra befintliga inställningar väljer du **Redigera inställning**.
    * Om du vill lägga till nya inställningar väljer du **Add diagnostics setting** (Lägg till diagnostikinställning).  
      Du kan ha upp till tre inställningar.

      ![Exportinställningar](./media/quickstart-azure-monitor-stream-logs-to-event-hub/ExportSettings.png)

5. Markera kryssrutan **Strömma till en händelsehubb** och välj sedan **Event Hub/Configure** (Händelsehubb/Konfigurera).

6. Välj Azure-prenumerationen och den Event Hubs-namnrymd som du vill dirigera loggarna till.  
    Både prenumerationen och Event Hubs-namnrymden måste vara associerade med den Azure AD-klientorganisation som loggarna strömmar från. Du kan även ange en händelsehubb i Event Hubs-namnrymden som loggar ska skickas till. Om ingen händelsehubb anges skapas en händelsehubb i namnrymden med standardnamnet **insights-logs-audit**.

7. Välj **OK** för att avsluta händelsehubbkonfigurationen.

8. Gör endera eller båda av följande:
    * För att skicka spårningsloggar till lagringskontot markerar du kryssrutan **AuditLogs** (Spårningsloggar). 
    * För att skicka inloggningsloggar till lagringskontot markerar du kryssrutan **SignInLogs** (Inloggningsloggar).

9. Spara inställningen genom att välja **Spara**.

    ![Diagnostikinställningar](./media/quickstart-azure-monitor-stream-logs-to-event-hub/DiagnosticSettings.png)

10. Efter ungefär 15 minuter kontrollerar du att händelserna visas i din händelsehubb. För att göra detta går du till händelsehubben från portalen och kontrollerar att antalet **inkommande meddelanden** är större än noll. 

    ![Granskningsloggar](./media/quickstart-azure-monitor-stream-logs-to-event-hub/InsightsLogsAudit.png)

## <a name="access-data-from-your-event-hub"></a>Komma åt data från din händelsehubb

När data visas i händelsehubben kan du komma åt och läsa data på två sätt:

* **Konfigurera ett SIEM-stödverktyg**. För att läsa data från händelsehubben kräver de flesta verktyg händelsehubbens anslutningssträng och vissa behörigheter till din Azure-prenumeration. Verktyg från tredje part med Azure Monitor-integrering inkluderar men är inte begränsade till:
    * **Splunk**: Information om att integrera Azure AD-loggar med Splunk finns på sidan om att [integrera Azure AD-loggar med Splunk med hjälp av Azure Monitor](tutorial-integrate-activity-logs-with-splunk.md).
    
    * **IBM QRadar**: DSM- och Azure Event Hub-protokollet är tillgängligt för nedladdning vid [IBM-supporten](http://www.ibm.com/support). Mer information om integrering med Azure finns på sidan [IBM QRadar Security Intelligence Platform 7.3.0](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
    
    * **Sumo Logic**: Om du vill konfigurera Sumo Logic för att använda data från en händelsehubb läser du [Install the Azure AD app and view the dashboards](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) (Installera Azure AD-appen och visa instrumentpanelerna). 

* **Konfigurera anpassade verktyg**. Om din aktuella SIEM inte stöds i Azure Monitor-diagnostiken ännu kan du konfigurera anpassade verktyg genom att använda Event Hub-API. Mer information finns på sidan om att [komma igång med att ta emot meddelanden från en händelsehubb](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph).


## <a name="next-steps"></a>Nästa steg

* [Integrera Azure AD-loggar med Splunk med hjälp av Azure Monitor](tutorial-integrate-activity-logs-with-splunk.md)
* [(Installera Azure AD-appen och visa instrumentpanelerna).](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards)
* [Tolka schema för spårningsloggar i Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Tolka schema för inloggningsloggar i Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
