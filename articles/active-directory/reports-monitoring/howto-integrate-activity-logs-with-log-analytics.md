---
title: Stream Azure Active Directory-loggarna till Log Analytics med Azure Monitor (förhandsversion) | Microsoft Docs
description: Lär dig att integrera Azure Active Directory-loggar med Log Analytics med hjälp av Azure Monitor (förhandsversion)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ad01784f3a808be4a27c97934b7ce4e83de8cdc2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168489"
---
# <a name="integrate-azure-ad-logs-with-log-analytics-using-azure-monitor-preview"></a>Integrera Azure AD-loggar med Log Analytics med Azure Monitor (förhandsversion)

Du kan köra frågor mot data att söka efter specifika händelser, analysera trender och utföra korrelationen mellan olika datakällor i log Analytics. Med integreringen av Azure AD-aktivitetsloggar i Log Analytics, du kan utföra uppgifter, t.ex.:

 * Jämför dina Azure AD-inloggningen loggar mot säkerhetsloggar som publicerats av Azure Security Center

 * Felsöka flaskhalsar i prestanda på inloggningssidan för ditt program genom att korrelera programprestanda från Azure Application Insights.  

Följande videoklipp från en Ignite-sessionen visar fördelarna med att använda Log Analytics för Azure AD-loggar i praktiska användarscenarier.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

I den här artikeln får du lära dig hur du integrerar Azure Active Directory (Azure AD) loggar med Log Analytics med Azure Monitor.

## <a name="supported-reports"></a>Rapporter som stöds

Du kan dirigera aktiviteten granskningsloggar och logga in aktivitetsloggar till Log Analytics för vidare analys. 

* **Granskningsloggar**: Den [granskningsloggar](concept-audit-logs.md) ger dig tillgång till historiken för varje aktivitet som utförs i din klient.
* **Logga in loggar**: Med den [inloggningsaktivitet rapporten](concept-sign-ins.md), du kan bestämma vem som utfört de uppgifter som rapporteras i granskningsloggarna.

> [!NOTE]
> B2C-relaterade spårnings- och inloggningsaktivitetsloggar stöds inte just nu.
>

## <a name="prerequisites"></a>Förutsättningar 

Om du vill använda den här funktionen behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [registrera dig för en kostnadsfri utvärdering](https://azure.microsoft.com/free/).
* En Azure AD-klientorganisation.
* En användare som är *global administratör* eller *säkerhetsadministratör* för Azure AD-klientorganisationen.
* En Log Analytics-arbetsyta i Azure-prenumerationen. Lär dig hur du [skapa en Log Analytics-arbetsyta](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="send-logs-to-log-analytics"></a>Skicka loggar till Log Analytics

1. Logga in på [Azure Portal](https://portal.azure.com). 

2. Välj **Azure Active Directory** > **diagnostikinställningar** -> **Lägg till diagnostikinställning**. Du kan också välja **exportinställningar** från den **granskningsloggar** eller **inloggningar** vill gå till konfigurationssidan för diagnostikinställningar.  
    
3. I den **diagnostikinställningar** menyn och välj den **skicka till Log Analytics** kryssrutan och välj sedan **konfigurera**.

4. Välj Log Analytics-arbetsytan som du vill skicka loggar till eller skapa en ny arbetsyta i dialogrutan angivna.  

5. Gör endera eller båda av följande:
    * Om du vill skicka granskningsloggar till Log Analytics-arbetsytan, Välj den **AuditLogs** markerar du kryssrutan. 
    * Om du vill skicka loggar med inloggning till Log Analytics-arbetsytan, Välj den **SignInLogs** markerar du kryssrutan.

6. Spara inställningen genom att välja **Spara**.

    ![Diagnostikinställningar](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Kontrollera att händelser som strömmas till Log Analytics-arbetsytan efter 15 minuter.

## <a name="next-steps"></a>Nästa steg

* [Analysera Azure AD-aktivitetsloggar i Log Analytics](howto-analyze-activity-logs-log-analytics.md)
* [Installera och använda Log Analytics-vyer för Azure Active Directory](howto-install-use-log-analytics-views.md)
