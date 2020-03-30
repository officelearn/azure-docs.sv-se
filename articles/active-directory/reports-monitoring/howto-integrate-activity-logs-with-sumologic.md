---
title: Strömma loggar till SumoLogic med Azure Monitor | Microsoft-dokument
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
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdfc4e393ca7bf4bcbd523b4fad72690d5f2744
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014391"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Integrera Azure Active Directory-loggar med SumoLogic med Azure Monitor

I den här artikeln får du lära dig hur du integrerar Azure Active Directory (Azure AD) loggar med SumoLogic med Azure Monitor. Du dirigerar först loggarna till en Azure-händelsehubb och sedan integrerar du händelsehubben med SumoLogic.

## <a name="prerequisites"></a>Krav

Om du vill använda den här funktionen behöver du:
* En Azure-händelsenav som innehåller Azure AD-aktivitetsloggar. Läs om hur du [streamar dina aktivitetsloggar till en händelsehubb](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* En SumoLogic enkel inloggning aktiverad prenumeration.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Steg för att integrera Azure AD-loggar med SumoLogic 

1. Strömma först [Azure AD-loggarna till en Azure-händelsehubb](quickstart-azure-monitor-stream-logs-to-event-hub.md).
2. Konfigurera sumoLogic-instansen för att [samla in loggar för Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Installera Azure AD SumoLogic-appen](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) för att använda de förkonfigurerade instrumentpanelerna som ger realtidsanalys av din miljö.

   ![Instrumentpanel](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Nästa steg

* [Tolka granskningsloggschema i Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Tolka schema för inloggningsloggar i Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Vanliga frågor och kända problem](concept-activity-logs-azure-monitor.md#frequently-asked-questions)