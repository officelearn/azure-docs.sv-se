---
title: Analysera aktivitetsloggar i Azure Active Directory med Azure Monitor-loggar (förhandsversion) | Microsoft Docs
description: Lär dig hur du analyserar aktivitetsloggar i Azure Active Directory med Azure Monitor-loggar (förhandsversion)
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
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e195dd0e52b4938e829ea0aec1f4cc3ef7107c0
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436820"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs-preview"></a>Analysera Azure AD-aktivitetsloggar med Azure Monitor-loggar (förhandsversion)

När du [integrera Azure AD-aktivitetsloggar med Azure Monitor-loggar](howto-integrate-activity-logs-with-log-analytics.md), du kan använda kraften i Azure Monitor-loggar och få insikter om din miljö. Du kan också installera den [logga analysvyer för Azure AD activity loggar](howto-install-use-log-analytics-views.md) att få åtkomst till inbyggda rapporter kring gransknings- och inloggningshändelser i din miljö.

I den här artikeln får du lära dig hur du analyserar Azure AD-aktivitetsloggar i Log Analytics-arbetsytan. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Förutsättningar 

Om du vill följa med, behöver du:

* En Log Analytics-arbetsyta i Azure-prenumerationen. Lär dig hur du [skapa en Log Analytics-arbetsyta](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Först måste slutföra stegen till [vägen med Azure AD-aktivitetsloggar till Log Analytics-arbetsytan](howto-integrate-activity-logs-with-log-analytics.md).

## <a name="navigate-to-the-log-analytics-workspace"></a>Gå till Log Analytics-arbetsytan

1. Logga in på [Azure Portal](https://portal.azure.com). 

2. Välj **Azure Active Directory**, och välj sedan **loggar** från den **övervakning** avsnitt för att öppna Log Analytics-arbetsytan. Arbetsytan öppnas med en standardfråga.

    ![Standardfrågan](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Visa schema för Azure AD activity loggar

Loggarna skickas till den **AuditLogs** och **SigninLogs** tabeller i arbetsytan. Visa schemat för tabellerna:

1. Standard frågevyn i föregående avsnitt, Välj **schemat** och expandera arbetsytan. 

2. Expandera den **Logghantering** och expanderar sedan antingen **AuditLogs** eller **SignInLogs** att visa loggen schemat.
    ![Granskningsloggar](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![inloggning från loggar](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Fråga Azure AD-aktivitetsloggar

Du kan nu köra frågor mot dem nu när du har loggar på din arbetsyta. Ersätt till exempel standardfrågan med följande och välja om du vill hämta de främsta programmen som används i den senaste veckan **kör**

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Om du vill ha de övre granskningshändelserna under den senaste veckan, använder du följande fråga:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Avisera om Azure AD-aktivitetsloggdata

Du kan också ställa in aviseringar på din fråga. Om du vill konfigurera en avisering när fler än 10 har till exempel program använts under den senaste veckan:

1. Från arbetsytan, Välj **Ställ in en avisering** att öppna den **skapa regeln** sidan.

    ![Ställ in en avisering](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Välj standard **Avisera kriterier** skapas i varningen och uppdatera den **tröskelvärdet** i Standardmåttet till 10.

    ![Aviseringsvillkor](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Ange ett namn och en beskrivning av aviseringen och välj allvarlighetsgraden. I vårt exempel kan vi ange den till **information**.

4. Välj den **åtgärdsgrupp** som ska aviseras när signalen inträffar. Du kan välja att meddela ditt team via e-postmeddelande eller textmeddelande eller du kan automatisera åtgärder med webhooks, Azure functions eller logic apps. Läs mer om [skapa och hantera Avisera grupper i Azure-portalen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

5. När du har konfigurerat aviseringen, Välj **skapa avisering** att aktivera den. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Installera och använda fördefinierade vyer för Azure AD activity loggar

Du kan också hämta färdiga log analytics-vyer för Azure AD activity logs. Vyerna tillhandahåller flera rapporter som rör vanliga scenarier som omfattar gransknings- och inloggningshändelser. Du kan även meddela på någon av informationen i rapporterna, genom att följa anvisningarna i föregående avsnitt.

* **Azure AD-konto etablering händelser**: Den här vyn visar rapporter som rör granskning etableringsaktivitet, till exempel hur många nya användare etableras och konfigurationsfel, antal användare uppdateras och uppdatera fel och antalet användare som avetableras och motsvarande fel.    
* **Inloggningar händelser**: Den här vyn visar de mest relevanta rapporter som rör övervakning inloggningsaktivitet, till exempel inloggningar efter program, användare, enhet, samt en sammanfattningsvy spåra antalet inloggningar över tid.
* **Användare som utför medgivande**: Den här vyn visar rapporter som rör användargodkännande, t.ex. samtycke ger av användaren, inloggningar efter användare som givit medgivande samt inloggningar av program för alla medgivande-baserade program. 

Lär dig att [installera och använda Log Analytics-vyer för Azure AD-aktivitetsloggar](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>Nästa steg

* [Kom igång med frågor i Azure Monitor-loggar](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Skapa och hantera aviseringar grupper i Azure portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Installera och använda log analytics-vyer för Azure Active Directory](howto-install-use-log-analytics-views.md)
