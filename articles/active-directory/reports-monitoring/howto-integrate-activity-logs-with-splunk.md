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
ms.date: 03/10/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eda3643a7b1a341c7ed664dbfea933145f1f927
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968711"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Gör så här: integrera Azure Active Directorys loggar med Splunk med Azure Monitor

I den här artikeln får du lära dig hur du integrerar Azure Active Directory (Azure AD)-loggar med Splunk med hjälp av Azure Monitor. Först dirigerar du loggarna till en Azure Event Hub och integrerar sedan Event Hub med Splunk.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här funktionen behöver du:

- En Azure Event Hub som innehåller Azure AD-aktivitets loggar. Lär dig hur du [strömmar dina aktivitets loggar till en Event Hub](quickstart-azure-monitor-stream-logs-to-event-hub.md). 

-  [Microsoft Azure lägga till på för Splunk](https://splunkbase.splunk.com/app/3757/). 

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