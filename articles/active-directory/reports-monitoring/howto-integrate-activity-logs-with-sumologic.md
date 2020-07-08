---
title: Strömma loggar till SumoLogic med Azure Monitor | Microsoft Docs
description: Lär dig hur du integrerar Azure Active Directory loggar med SumoLogic med Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91c3b1216d5ced766a77bb1fb63e41eaedb8220d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608984"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Integrera Azure Active Directory-loggar med SumoLogic med Azure Monitor

I den här artikeln får du lära dig hur du integrerar Azure Active Directory (Azure AD)-loggar med SumoLogic med hjälp av Azure Monitor. Först dirigerar du loggarna till en Azure Event Hub och integrerar sedan Event Hub med SumoLogic.

## <a name="prerequisites"></a>Krav

Om du vill använda den här funktionen behöver du:
* En Azure Event Hub som innehåller Azure AD-aktivitets loggar. Lär dig hur du [strömmar dina aktivitets loggar till en Event Hub](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* En aktive rad SumoLogic-prenumeration med enkel inloggning.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Steg för att integrera Azure AD-loggar med SumoLogic 

1. Börja [med att strömma Azure AD-loggarna till en Azure Event Hub](quickstart-azure-monitor-stream-logs-to-event-hub.md).
2. Konfigurera SumoLogic-instansen för att [samla in loggar för Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Installera Azure AD SumoLogic-appen](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) för att använda förkonfigurerade instrument paneler som tillhandahåller real tids analyser i din miljö.

   ![Instrumentpanel](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Nästa steg

* [Tolka schemat för gransknings loggar i Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Tolka schema för inloggningsloggar i Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Vanliga frågor och kända problem](concept-activity-logs-azure-monitor.md#frequently-asked-questions)