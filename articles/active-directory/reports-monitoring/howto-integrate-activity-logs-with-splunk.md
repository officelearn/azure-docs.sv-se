---
title: Integrera Splunk med Azure Monitor | Microsoft-dokument
description: Lär dig hur du integrerar Azure Active Directory-loggar med SumoLogic med Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/10/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eda3643a7b1a341c7ed664dbfea933145f1f927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968711"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Så här integrerar du Azure Active Directory-loggar med Splunk med Azure Monitor

I den här artikeln får du lära dig hur du integrerar Azure Active Directory (Azure AD) loggar med Splunk med hjälp av Azure Monitor. Du dirigerar först loggarna till en Azure-händelsehubb och sedan integrerar du händelsehubben med Splunk.

## <a name="prerequisites"></a>Krav

Om du vill använda den här funktionen behöver du:

- En Azure-händelsenav som innehåller Azure AD-aktivitetsloggar. Läs om hur du [streamar dina aktivitetsloggar till en händelsehubb](quickstart-azure-monitor-stream-logs-to-event-hub.md). 

-  [Microsoft Azure-tillägget för Splunk](https://splunkbase.splunk.com/app/3757/). 

## <a name="integrate-azure-active-directory-logs"></a>Integrera Azure Active Directory-loggar 

1. Öppna splunk-förekomsten och välj **Datasammanfattning**.

    ![Knappen "Datasammanfattning"](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Välj fliken **Sourcetypes** och välj sedan **amal: aadal:audit**

    ![Fliken Datasammanfattningskällatyper](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Azure AD-aktivitetsloggarna visas i följande bild:

    ![Aktivitetsloggar](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Om du inte kan installera ett tillägg i din Splunk-förekomst (till exempel om du använder en proxy eller körs på Splunk Cloud) kan du vidarebefordra dessa händelser till Splunk HTTP Event Collector. Det gör du genom att använda den här [Azure-funktionen](https://github.com/Microsoft/AzureFunctionforSplunkVS), som utlöses av nya meddelanden i händelsehubben. 
>

## <a name="next-steps"></a>Nästa steg

* [Tolka granskningsloggschema i Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Tolka schema för inloggningsloggar i Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Vanliga frågor och kända problem](concept-activity-logs-azure-monitor.md#frequently-asked-questions)