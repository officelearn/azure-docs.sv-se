---
title: Analysera aktivitetsloggar med Hjälp av Azure Monitor-loggar | Microsoft-dokument
description: Lär dig hur du analyserar Azure Active Directory-aktivitetsloggar med Hjälp av Azure Monitor-loggar
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
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
ms.openlocfilehash: 2d6212692465270182db541889bed5f03a08a345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008273"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Analysera Azure AD-aktivitetsloggar med Azure Monitor-loggar

När du [har integrerat Azure AD-aktivitetsloggar med Azure Monitor-loggar](howto-integrate-activity-logs-with-log-analytics.md)kan du använda kraften i Azure Monitor-loggar för att få insikter i din miljö. Du kan också installera [logganalysvyerna för Azure AD-aktivitetsloggar för](howto-install-use-log-analytics-views.md) att få åtkomst till fördefinierade rapporter kring gransknings- och inloggningshändelser i din miljö.

I den här artikeln får du lära dig hur du analyserar Azure AD-aktivitetsloggarna på din Log Analytics-arbetsyta. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Krav 

För att följa med behöver du:

* En Log Analytics-arbetsyta i din Azure-prenumeration. Läs om hur du [skapar en Log Analytics-arbetsyta](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Slutför först stegen för att [dirigera Azure AD-aktivitetsloggarna till din Log Analytics-arbetsyta](howto-integrate-activity-logs-with-log-analytics.md).
*  [Tillgång](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) till arbetsytan för logganalys
* Följande roller i Azure Active Directory (om du använder Log Analytics via Azure Active Directory portal)
    - Säkerhetsadministratör
    - Säkerhetsläsare
    - Rapportläsare
    - Global administratör
    
## <a name="navigate-to-the-log-analytics-workspace"></a>Navigera till log analytics-arbetsytan

1. Logga in på [Azure-portalen](https://portal.azure.com). 

2. Välj **Azure Active Directory**och välj sedan **Loggar** från avsnittet **Övervakning** för att öppna arbetsytan Log Analytics. Arbetsytan öppnas med en standardfråga.

    ![Standardfråga](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Visa schemat för Azure AD-aktivitetsloggar

Loggarna skjuts till **tabellerna AuditLogs** och **SigninLogs** på arbetsytan. Så här visar du schemat för dessa tabeller:

1. Välj **Schema** och expandera arbetsytan i standardfrågevyn i föregående avsnitt. 

2. Expandera avsnittet **Logghantering** och expandera sedan **antingen AuditLogs** eller **SignInLogs** för att visa loggschemat.
    ![Granskningsloggar](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![Inloggningsloggar](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Fråga Azure AD-aktivitetsloggarna

Nu när du har loggarna på arbetsytan kan du nu köra frågor mot dem. Om du till exempel vill hämta de bästa programmen som användes under den senaste veckan ersätter du standardfrågan med följande och väljer **Kör**

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Om du vill hämta de vanligaste granskningshändelserna under den senaste veckan använder du följande fråga:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Avisering om Azure AD-aktivitetsloggdata

Du kan också ställa in aviseringar på din fråga. Om du till exempel vill konfigurera en avisering när fler än 10 program har använts under den senaste veckan:

1. På arbetsytan väljer du **Ange avisering** för att öppna sidan **Skapa regel.**

    ![Ställ in avisering](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Välj de **standardvarningsvillkor som** skapats i aviseringen och uppdatera **tröskelvärdet** i standardmåttet till 10.

    ![Varningsvillkor](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Ange ett namn och en beskrivning för aviseringen och välj allvarlighetsgrad. Till vårt exempel kan vi ställa in den till **Informational**.

4. Välj den **åtgärdsgrupp** som ska aviseras när signalen inträffar. Du kan välja att meddela ditt team via e-post eller sms, eller så kan du automatisera åtgärden med webhooks, Azure-funktioner eller logikappar. Läs mer om [hur du skapar och hanterar varningsgrupper i Azure-portalen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

5. När du har konfigurerat aviseringen väljer du **Skapa avisering** för att aktivera den. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Installera och använda färdiga vyer för Azure AD-aktivitetsloggar

Du kan också hämta de färdiga logganalysvyerna för Azure AD-aktivitetsloggar. Vyerna innehåller flera rapporter om vanliga scenarier som involverar gransknings- och inloggningshändelser. Du kan också avisera någon av de data som finns i rapporterna med hjälp av stegen som beskrivs i föregående avsnitt.

* **Azure AD-kontoetableringshändelser:** Den här vyn visar rapporter relaterade till granskningsetableringsaktivitet, till exempel antalet nya användare som etablerats och etableringsfel, antal användare som uppdaterats och uppdaterat fel samt antalet användare som avetableras och motsvarande fel.    
* **Inloggningar Händelser:** Den här vyn visar de mest relevanta rapporterna relaterade till övervakning av inloggningsaktivitet, till exempel inloggningar efter program, användare, enhet, samt en sammanfattningsvy som spårar antalet inloggningar över tid.
* **Användare som utför samtycke**: Den här vyn visar rapporter som rör användarens medgivande, till exempel samtyckesbidrag från användare, inloggningar av användare som har gett sitt samtycke samt inloggningar efter program för alla medgivandebaserade program. 

Lär dig att [installera och använda logganalysvyer för Azure AD-aktivitetsloggar](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>Nästa steg

* [Komma igång med frågor i Azure Monitor-loggar](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Skapa och hantera aviseringsgrupper i Azure-portalen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Installera och använda logganalysvyerna för Azure Active Directory](howto-install-use-log-analytics-views.md)
