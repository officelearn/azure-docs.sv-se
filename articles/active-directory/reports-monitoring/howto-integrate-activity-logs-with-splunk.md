---
title: Integrera Splunk med Azure Monitor | Microsoft Docs
description: Lär dig hur du integrerar Azure Active Directory loggar med SumoLogic med Azure Monitor
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
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7da3a545847382f8fed192a6ec4fe2ac75bb8b35
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014397"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Gör så här: integrera Azure Active Directorys loggar med Splunk med Azure Monitor

I den här artikeln får du lära dig hur du integrerar Azure Active Directory (Azure AD)-loggar med Splunk med hjälp av Azure Monitor. Först dirigerar du loggarna till en Azure Event Hub och integrerar sedan Event Hub med Splunk.

## <a name="prerequisites"></a>Krav

Om du vill använda den här funktionen behöver du:
* En Azure Event Hub som innehåller Azure AD-aktivitets loggar. Lär dig hur du [strömmar dina aktivitets loggar till en Event Hub](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Azure Monitor-tillägget för Splunk. [Hämta och konfigurera din Splunk-instans](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="integrate-azure-active-directory-logs"></a>Integrera Azure Active Directory loggar 

1. Öppna din Splunk-instans och välj **data Sammanfattning**.

    ![Knappen data Sammanfattning](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Välj fliken **Sourcetypes** och välj sedan **Amal: Aadal: Audit**

    ![Fliken Data Sammanfattning Sourcetypes](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Aktivitets loggarna för Azure AD visas i följande figur:

    ![Aktivitetsloggar](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Om du inte kan installera ett tillägg i din Splunk-instans (om du t. ex. använder en proxy eller som körs på Splunk-molnet) kan du vidarebefordra händelserna till händelse insamlaren för HTTP-Splunk. Det gör du genom att använda den här [Azure-funktionen](https://github.com/Microsoft/AzureFunctionforSplunkVS)som utlöses av nya meddelanden i händelsehubben. 
>

## <a name="next-steps"></a>Nästa steg

* [Tolka schema för spårningsloggar i Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Tolka schema för inloggningsloggar i Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Vanliga frågor och kända problem](concept-activity-logs-azure-monitor.md#frequently-asked-questions)