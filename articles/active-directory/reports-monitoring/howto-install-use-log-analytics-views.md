---
title: Så här installerar och använder du logganalysvyerna | Microsoft-dokument
description: Lär dig hur du installerar och använder logganalysvyerna för Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
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
ms.openlocfilehash: b17026e4cfbe69e36c8e459aa259fe16b1c9d80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014426"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Installera och använda logganalysvyerna för Azure Active Directory

Azure Active Directory-logganalysvyerna hjälper dig att analysera och söka efter Azure AD-aktivitetsloggar i din Azure AD-klientorganisation. Azure AD-aktivitetsloggar inkluderar:

* Granskningsloggar: I [aktivitetsrapporten för granskningsloggar](concept-audit-logs.md) får du tillgång till historiken för varje aktivitet som utförs i din klientorganisation.
* Inloggningsloggar: Med [inloggningsaktivitetsrapporten](concept-sign-ins.md)kan du bestämma vem som utförde de uppgifter som rapporteras i granskningsloggarna.

## <a name="prerequisites"></a>Krav

Om du vill använda logganalysvyerna behöver du:

* En Log Analytics-arbetsyta i din Azure-prenumeration. Läs om hur du [skapar en Log Analytics-arbetsyta](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Slutför först stegen för att [dirigera Azure AD-aktivitetsloggarna till din Log Analytics-arbetsyta](howto-integrate-activity-logs-with-log-analytics.md).
* Hämta vyerna från [GitHub-databasen](https://aka.ms/AADLogAnalyticsviews) till den lokala datorn.

## <a name="install-the-log-analytics-views"></a>Installera logganalysvyerna

1. Navigera till logganalysarbetsytan. Det gör du genom att först navigera till [Azure-portalen](https://portal.azure.com) och välja **Alla tjänster**. Skriv **Logganalys** i textrutan och välj **Logganalysarbetsytor**. Välj den arbetsyta som du dirigerade aktivitetsloggarna till, som en del av förutsättningarna.
2. Välj **Visa designer**, välj **Importera** och välj sedan Välj **fil** för att importera vyerna från den lokala datorn.
3. Välj de vyer som du hämtade från förutsättningarna och välj **Spara** för att spara importen. Gör detta för vyn **Azure AD-kontoetablering** och vyn **Inloggningshändelser.**

## <a name="use-the-views"></a>Använda vyerna

1. Navigera till logganalysarbetsytan. Det gör du genom att först navigera till [Azure-portalen](https://portal.azure.com) och välja **Alla tjänster**. Skriv **Logganalys** i textrutan och välj **Logganalysarbetsytor**. Välj den arbetsyta som du dirigerade aktivitetsloggarna till, som en del av förutsättningarna.

2. När du är på arbetsytan väljer du **Sammanfattning av arbetsytan**. Du bör se följande tre vyer:

    * **Azure AD-kontoetableringshändelser:** Den här vyn visar rapporter relaterade till granskningsetableringsaktivitet, till exempel antalet nya användare som etablerats och etableringsfel, antal användare som uppdaterats och uppdaterat fel samt antalet användare som avetableras och motsvarande fel.    
    * **Inloggningar Händelser:** Den här vyn visar de mest relevanta rapporterna relaterade till övervakning av inloggningsaktivitet, till exempel inloggningar efter program, användare, enhet, samt en sammanfattningsvy som spårar antalet inloggningar över tid.

3. Markera någon av dessa vyer om du vill hoppa in i de enskilda rapporterna. Du kan också ställa in aviseringar på någon av rapportparametrarna. Vi ska till exempel ange en avisering för varje gång det finns ett inloggningsfel. Det gör du genom att först välja vyn **Inloggningar händelser,** välj **Inloggningsfel över tidsrapporten** och väljer sedan **Analytics** för att öppna informationssidan, med den faktiska frågan bakom rapporten. 

    ![Information](./media/howto-install-use-log-analytics-views/details.png)


4. Välj **Ange avisering**och välj sedan **När den anpassade loggsökningen är &lt;logikdefinierad&gt; ** under avsnittet **Varningsvillkor.** Eftersom vi vill avisera när det finns ett inloggningsfel ställer du in **tröskelvärdet för** standardaviseringslogiken till **1** och väljer sedan **Klar**. 

    ![Konfigurera signallogiken](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Ange ett namn och en beskrivning för aviseringen och ange **allvarlighetsgraden**till Varning .

    ![Skapa regel](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Välj den åtgärdsgrupp som ska aviseras. I allmänhet kan detta vara antingen ett team som du vill meddela via e-post eller sms, eller det kan vara en automatiserad uppgift med webhooks, runbooks, funktioner, logikappar eller externa ITSM-lösningar. Lär dig hur du [skapar och hanterar åtgärdsgrupper i Azure-portalen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

7. Välj **Skapa aviseringsregel** om du vill skapa aviseringen. Nu kommer du att meddelas varje gång det finns ett inloggningsfel.

## <a name="next-steps"></a>Nästa steg

* [Så här analyserar du aktivitetsloggar med Azure Monitor-loggar](howto-analyze-activity-logs-log-analytics.md)
* [Komma igång med Azure Monitor-loggar i Azure-portalen](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)