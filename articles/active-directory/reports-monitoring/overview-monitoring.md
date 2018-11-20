---
title: Vad är Azure Active Directory-övervakning? (förhandsversion)| Microsoft Docs
description: En allmän översikt över Azure Active Directory-övervakning.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0afa4f85f443789d1ce7e79d0708967e308a4683
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623213"
---
# <a name="what-is-azure-active-directory-monitoring-preview"></a>Vad är Azure Active Directory-övervakning? (förhandsversion)

Med Azure Active Directory-övervakning (Azure AD) kan du nu dirigera dina Azure AD-aktivitetsloggar till olika slutpunkter. Du kan antingen behålla dem för långvarig användning eller integrera dem med SIEM-verktyg (säkerhetsinformation och händelsehantering) för att få insikter om din miljö.

För närvarande kan du dirigera loggarna till:

- Ett Azure-lagringskonto.
- En Azure-händelsehubb, så du kan integrera med dina Splunk- och Sumologic-instanser.
- Azure Log Analytics-arbetsyta, där du kan analysera data, skapa en instrumentpanel och avisera vid specifika händelser

## <a name="diagnostic-settings-configuration"></a>Konfiguration av diagnostikinställningar

Du konfigurerar övervakningsinställningarna för Azure AD-aktivitetsloggarna genom att först logga in på [Azure-portalen](https://portal.azure.com) och sedan välja **Azure Active Directory**. Härifrån kan du komma åt konfigurationssidan Diagnostikinställningar på två sätt:

* Välj **Diagnostikinställningar** från avsnittet **Övervakning**.

    ![Diagnostikinställningar](./media/overview-monitoring/diagnostic-settings.png)
    
* Välj **Granskningsloggar** eller **Inloggningar**och välj sedan **Exportinställningar**. 

    ![Exportinställningar](./media/overview-monitoring/export-settings.png)


## <a name="route-logs-to-storage-account"></a>Dirigera loggar till lagringskonto

Genom att dirigera loggar till ett Azure Storage-konto kan du behålla dem längre än standardperioden för kvarhållning som beskrivs i våra [principer för kvarhållning](reference-reports-data-retention.md). Lär dig hur du [dirigerar data till ditt lagringskonto](quickstart-azure-monitor-route-logs-to-storage-account.md).

## <a name="stream-logs-to-event-hub"></a>Strömma loggar till händelsehubb

När du dirigerar loggar till en Azure-händelsehubb kan du integrera med SIEM-tredjepartsverktyg som Sumologic och Splunk. Den här integrationen gör att du kan kombinera Azure AD-aktivitetsloggdata med andra data som hanteras av ditt SIEM, för att ge bättre inblick i din miljö. Lär dig hur du [skickar händelser till en händelsehubb](tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="send-logs-to-log-analytics"></a>Skicka loggar till Log Analytics

[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) är en lösning som konsoliderar övervakningsdata från olika källor och tillhandahåller ett frågespråk och en analysmotor som ger dig insikter om hur dina program och resurser fungerar. Genom att skicka Azure AD-aktivitetsloggar till Log Analytics kan du snabbt hämta, övervaka och avisera insamlade data. Lär dig hur du [skickar data till Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).

Du kan också installera fördefinierade vyer för Azure AD-aktivitetsloggar för vanliga scenarier som omfattar inloggningar och granskningshändelser. Lär dig hur du [installerar och använder Log Analytics-vyer för Azure AD-aktivitetsloggar](howto-install-use-log-analytics-views.md).

## <a name="next-steps"></a>Nästa steg

* [Aktivitetsloggar i Azure Monitor](concept-activity-logs-azure-monitor.md)
* [Strömma loggar till händelsehubb](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Skicka loggar till Log Analytics](howto-integrate-activity-logs-with-log-analytics.md)