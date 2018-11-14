---
title: Hur du integrerar Azure Active Directory-loggar med Splunk med hjälp av Azure Monitor (förhandsversion) | Microsoft Docs
description: Lär dig att integrera Azure Active Directory-loggar med Splunk med hjälp av Azure Monitor (förhandsversion)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e45b175b97b7f395199dbc4521054fa69a4bb4a7
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623816"
---
# <a name="integrate-azure-ad-logs-with-splunk-using-azure-monitor-preview"></a>Integrera Azure AD-loggar med Splunk med Azure Monitor (förhandsversion)

I den här artikeln får du lära dig hur du integrerar Azure Active Directory (Azure AD) loggar med Splunk med hjälp av Azure Monitor. Du först dirigera loggarna till en Azure event hub och integrerar event hub med Splunk.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här funktionen behöver du:
* En Azure-händelsehubb som innehåller Azure AD activity loggar. Lär dig hur du [strömma din aktivitetsloggar till en händelsehubb](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Azure Monitor-tillägget för Splunk. [Hämta och konfigurera din instans av Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="tutorial"></a>Självstudier 

1. Öppna din Splunk-instans och välj **Data sammanfattning**.

    ![Knappen ”Data-sammanfattning”](./media/tutorial-integrate-activity-logs-with-splunk/DataSummary.png)

2. Välj den **Sourcetypes** fliken och välj sedan **amal: aadal:audit**

    ![Fliken Data sammanfattning Sourcetypes](./media/tutorial-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Azure AD-aktiviteten loggarna visas i följande bild:

    ![Aktivitetsloggar](./media/tutorial-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Om du inte kan installera ett tillägg i din Splunk-instans (till exempel, om du använder en proxy eller som körs på Splunk molnet), kan du vidarebefordra dessa händelser till Splunk HTTP Event Collector. Använd det här gör du [Azure-funktion](https://github.com/Microsoft/AzureFunctionforSplunkVS), som utlöses av nya meddelanden i event hub. 
>

## <a name="next-steps"></a>Nästa steg

* [Tolka schema för spårningsloggar i Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Tolka schema för inloggningsloggar i Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Vanliga frågor och kända problem](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
