---
title: Stream Azure Active Directory-loggar till SumoLogic med Azure Monitor | Microsoft Docs
description: Lär dig att integrera Azure Active Directory-loggar med SumoLogic med Azure Monitor
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
ms.openlocfilehash: 7ccf9cd550b3848be841f193f4ab8a6094cf671d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60437871"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Integrera Azure Active Directory-loggar med SumoLogic med Azure Monitor

I den här artikeln får du lära dig hur du integrerar Azure Active Directory (Azure AD) loggar med SumoLogic med Azure Monitor. Du först dirigera loggarna till en Azure event hub och integrerar event hub med SumoLogic.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill använda den här funktionen behöver du:
* En Azure-händelsehubb som innehåller Azure AD activity loggar. Lär dig hur du [strömma din aktivitetsloggar till en händelsehubb](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* En SumoLogic enkel inloggning aktiverat prenumeration.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Steg för att integrera Azure AD-loggar med SumoLogic 

1. Först [strömma Azure AD-loggar till en Azure event hub](quickstart-azure-monitor-stream-logs-to-event-hub.md).
2. Konfigurera din SumoLogic-instans till [samla in loggar för Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Installera appen Azure AD SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) att använda de förkonfigurerade instrumentpaneler som ger realtidsanalys för din miljö.

   ![Instrumentpanel](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Nästa steg

* [Tolka schema för spårningsloggar i Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Tolka schema för inloggningsloggar i Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Vanliga frågor och kända problem](concept-activity-logs-azure-monitor.md#frequently-asked-questions)