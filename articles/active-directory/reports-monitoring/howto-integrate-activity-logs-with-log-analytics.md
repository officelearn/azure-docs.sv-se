---
title: Stream Azure Active Directory-loggar till Azure Monitor-loggar | Microsoft Docs
description: Lär dig att integrera Azure Active Directory-loggar med Azure Monitor-loggar
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
ms.openlocfilehash: 213fb6e73ae2fc4314320d0e3e593632d8eb7f85
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60010943"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Integrera Azure AD-loggar med Azure Monitor-loggar

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Du kan köra frågor mot data att söka efter specifika händelser, analysera trender och utföra korrelationen mellan olika datakällor i Azure Monitor-loggar. Med integreringen av Azure AD-aktivitetsloggar i Azure Monitor-loggar, du kan utföra uppgifter, t.ex.:

 * Jämför dina Azure AD-inloggningen loggar mot säkerhetsloggar som publicerats av Azure Security Center

 * Felsöka flaskhalsar i prestanda på inloggningssidan för ditt program genom att korrelera programprestanda från Azure Application Insights.  

Följande videoklipp från en Ignite-sessionen visar fördelarna med att använda Azure Monitor-loggar för Azure AD-loggar i praktiska användarscenarier.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

I den här artikeln får du lära dig hur du integrerar Azure Active Directory (Azure AD) loggar med Azure Monitor.

## <a name="supported-reports"></a>Rapporter som stöds

Du kan dirigera aktiviteten granskningsloggar och logga in aktivitetsloggar till Azure Monitor-loggar för vidare analys. 

* **Granskningsloggar**: Den [granskningsloggar](concept-audit-logs.md) ger dig tillgång till historiken för varje aktivitet som utförs i din klient.
* **Logga in loggar**: Med den [inloggningsaktivitet rapporten](concept-sign-ins.md), du kan bestämma vem som utfört de uppgifter som rapporteras i granskningsloggarna.

> [!NOTE]
> B2C-relaterade spårnings- och inloggningsaktivitetsloggar stöds inte just nu.
>

## <a name="prerequisites"></a>Nödvändiga komponenter 

Om du vill använda den här funktionen behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [registrera dig för en kostnadsfri utvärdering](https://azure.microsoft.com/free/).
* En Azure AD-klientorganisation.
* En användare som är *global administratör* eller *säkerhetsadministratör* för Azure AD-klientorganisationen.
* En Log Analytics-arbetsyta i Azure-prenumerationen. Lär dig hur du [skapa en Log Analytics-arbetsyta](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="send-logs-to-azure-monitor"></a>Skicka loggar till Azure Monitor

1. Logga in på [Azure Portal](https://portal.azure.com). 

2. Välj **Azure Active Directory** > **diagnostikinställningar** -> **Lägg till diagnostikinställning**. Du kan också välja **exportinställningar** från den **granskningsloggar** eller **inloggningar** vill gå till konfigurationssidan för diagnostikinställningar.  
    
3. I den **diagnostikinställningar** menyn och välj den **skicka till Log Analytics-arbetsyta** kryssrutan och välj sedan **konfigurera**.

4. Välj Log Analytics-arbetsytan som du vill skicka loggar till eller skapa en ny arbetsyta i dialogrutan angivna.  

5. Gör endera eller båda av följande:
    * Om du vill skicka granskningsloggar till Log Analytics-arbetsytan, Välj den **AuditLogs** markerar du kryssrutan. 
    * Om du vill skicka loggar med inloggning till Log Analytics-arbetsytan, Välj den **SignInLogs** markerar du kryssrutan.

6. Spara inställningen genom att välja **Spara**.

    ![Diagnostikinställningar](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Kontrollera att händelser som strömmas till Log Analytics-arbetsytan efter 15 minuter.

## <a name="next-steps"></a>Nästa steg

* [Analysera Azure AD-aktivitetsloggar med Azure Monitor-loggar](howto-analyze-activity-logs-log-analytics.md)
* [Installera och använda log analytics-vyer för Azure Active Directory](howto-install-use-log-analytics-views.md)