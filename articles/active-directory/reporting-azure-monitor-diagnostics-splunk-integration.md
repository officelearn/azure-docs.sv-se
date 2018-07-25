---
title: Hur du integrerar Azure Active Directory-loggar med Splunk med Azure Monitor (förhandsversion) | Microsoft Docs
description: Lär dig att integrera Azure Active Directory-loggar med Splunk med Azure Monitor (förhandsversion)
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
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d1dd62d06c7e3ed634795604ce9660694ea073ca
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239990"
---
# <a name="integrate-azure-active-directory-logs-with-splunk-using-azure-monitor-preview"></a>Integrera Azure Active Directory-loggar med Splunk med Azure Monitor (förhandsversion)

I den här artikeln lär du dig att integrera Azure Active Directory-loggar med Splunk med Azure Monitor. Först måste behöver du dirigera loggarna till en Azure event hub och integrera det med Splunk.

## <a name="prerequisites"></a>Förutsättningar

1. Azure Event Hub som innehåller Azure AD activity loggar. Lär dig hur du [strömma din aktivitetsloggar till Händelsehubb](reporting-azure-monitor-diagnostics-azure-event-hub.md). 
2. Använd följande [instruktioner](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md) att ladda ned Azure monitor-tillägg för Splunk och konfigurera din Splunk-instans.

## <a name="tutorial"></a>Självstudier 

1. Öppna din Splunk-instans och klicka på **Data sammanfattning**.
    ![Datasammanfattning](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png "Data sammanfattning")

2. Navigera till den **Sourcetypes** fliken och markera **amal: aadal:audit** ![Sourcetypes fliken](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png "Sourcetypes fliken")

3. Visas Azure AD-aktivitetsloggar som visas i följande bild.
    ![Aktivitetsloggar](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png "aktivitetsloggar")

> [!NOTE]
> Om du inte kan installera ett tillägg i din Splunk-instans (till exempel, om du använder en proxy eller som körs på Splunk molnet), du kan vidarebefordra dessa händelser till Splunk HTTP Event Collector använder det här [Azure-funktion som utlöses av nya meddelanden i den händelsehubb](https://github.com/Microsoft/AzureFunctionforSplunkVS)”. 
>

## <a name="next-steps"></a>Nästa steg

* [Tolka granska loggarna schemat i Azure monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Tolka inloggning loggar schemat i Azure monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Vanliga frågor och kända problem](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)