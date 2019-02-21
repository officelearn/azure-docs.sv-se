---
title: Installera och använda log analytics-vyer för Azure Active Directory (förhandsversion) | Microsoft Docs
description: Lär dig hur du installerar och använder log analytics-vyer för Azure Active Directory (förhandsversion)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7220fc6b239d20e9f1fc81e7d18b4d7ca6f0ded5
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453638"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Installera och använda log analytics-vyer för Azure Active Directory

Azure Active Directory log analytics vyer kan du analysera och Sök efter Azure AD-aktivitetsloggar i Azure AD-klienten. Azure AD-aktivitetsloggar loggar innehåller:

* Granskningsloggar: Den [granskningsloggar](concept-audit-logs.md) ger dig tillgång till historiken för varje aktivitet som utförs i din klient.
* Logga in loggar: Med den [inloggningsaktivitet rapporten](concept-sign-ins.md), du kan bestämma vem som utfört de uppgifter som rapporteras i granskningsloggarna.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda log analytics-vyer, behöver du:

* En Log Analytics-arbetsyta i Azure-prenumerationen. Lär dig hur du [skapa en Log Analytics-arbetsyta](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Först måste slutföra stegen till [vägen med Azure AD-aktivitetsloggar till Log Analytics-arbetsytan](howto-integrate-activity-logs-with-log-analytics.md).
* Ladda ned vyer från den [GitHub-lagringsplatsen](https://aka.ms/AADLogAnalyticsviews) till din lokala dator.

## <a name="install-the-log-analytics-views"></a>Installera log analytics-vyer

1. Gå till Log Analytics-arbetsytan. Om du vill göra detta måste först gå till den [Azure-portalen](https://portal.azure.com) och välj **alla tjänster**. Typ **Log Analytics** i textrutan och välj **Log Analytics-arbetsytor**. Välj den arbetsyta du dirigeras aktivitetsloggar till, som en del av förutsättningarna.
2. Välj **Vydesigner**väljer **importera** och välj sedan **Välj fil** att importera vyerna från din lokala dator.
3. Välj de vyer som du laddade ned från de krav och välj **spara** att spara importen. Gör detta för den **händelser för etablering av Azure AD-konto** vy och **inloggningar händelser** vy.

## <a name="use-the-views"></a>Använda vyer

1. Gå till Log Analytics-arbetsytan. Om du vill göra detta måste först gå till den [Azure-portalen](https://portal.azure.com) och välj **alla tjänster**. Typ **Log Analytics** i textrutan och välj **Log Analytics-arbetsytor**. Välj den arbetsyta du dirigeras aktivitetsloggar till, som en del av förutsättningarna.

2. När du arbetar i arbetsytan, väljer **arbetsytan Sammanfattning**. Du bör se följande tre vyer:

    * **Azure AD-konto etablering händelser**: Den här vyn visar rapporter som rör granskning etableringsaktivitet, till exempel hur många nya användare etableras och konfigurationsfel, antal användare uppdateras och uppdatera fel och antalet användare som avetableras och motsvarande fel.    
    * **Inloggningar händelser**: Den här vyn visar de mest relevanta rapporter som rör övervakning inloggningsaktivitet, till exempel inloggningar efter program, användare, enhet, samt en sammanfattningsvy spåra antalet inloggningar över tid.

3. Välj något av dessa vyer för att komma igång till enskilda rapporter. Du kan också ställa in aviseringar på någon av rapportparametrarna. Nu ska vi ange till exempel en avisering för varje gång det finns ett inloggningsfel. Om du vill göra detta måste först välja den **inloggningar händelser** väljer **inloggningsfel över tid** rapportera och välj sedan **Analytics** att öppna informationssidan om med den faktiska frågan bakom rapporten. 

    ![Information](./media/howto-install-use-log-analytics-views/details.png)


4. Välj **ange avisering**, och välj sedan **när the Custom log search är &lt;logic Odefinierad&gt;**  under den **Avisera kriterier** avsnittet. Eftersom vi vill meddela när det finns ett inloggningsfel, ange den **tröskelvärdet** av standard aviseringslogiken till **1** och välj sedan **klar**. 

    ![Konfigurera signallogiken](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Ange ett namn och en beskrivning av aviseringen och ange allvarlighetsgraden till **varning**.

    ![Skapa regel](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Välj åtgärdsgrupp för aviseringen. I allmänhet är det kan vara antingen ett team som du vill meddela via e-postmeddelande eller textmeddelande eller det kan vara att automatisera en åtgärd med webhooks, runbooks, functions, logic apps eller externa ITSM-lösningar. Lär dig hur du [skapa och hantera åtgärdsgrupper i Azure-portalen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

7. Välj **skapa varningsregel** att skapa aviseringen. Du kommer nu att varnas varje gång det finns ett inloggningsfel.

## <a name="next-steps"></a>Nästa steg

* [Hur du analyserar aktivitetsloggar med Azure Monitor-loggar](howto-analyze-activity-logs-log-analytics.md)
* [Kom igång med Azure Monitor-loggar i Azure portal](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)
