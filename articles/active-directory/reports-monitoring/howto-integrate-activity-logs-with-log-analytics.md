---
title: Strömma Azure Active Directory-loggar till Azure Monitor-loggar | Microsoft-dokument
description: Lär dig hur du integrerar Azure Active Directory-loggar med Azure Monitor-loggar
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
ms.openlocfilehash: 7d0ef55f6f5117ffa77052118155afea716125a4
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639732"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Integrera Azure AD-loggar med Azure Monitor-loggar

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Med Azure Monitor-loggar kan du fråga data för att hitta vissa händelser, analysera trender och utföra korrelation mellan olika datakällor. Med integreringen av Azure AD-aktivitetsloggar i Azure Monitor-loggar kan du nu utföra uppgifter som:

 * Jämför dina Azure AD-inloggningsloggar med säkerhetsloggar som publiceras av Azure Security Center

 * Felsöka flaskhalsar i prestanda på programmets inloggningssida genom att korrelera programprestandadata från Azure Application Insights.  

Följande video från en Ignite-session visar fördelarna med att använda Azure Monitor-loggar för Azure AD-loggar i praktiska användarscenarier.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

I den här artikeln får du lära dig hur du integrerar Azure Active Directory (Azure AD) loggar med Azure Monitor.

## <a name="supported-reports"></a>Rapporter som stöds

Du kan dirigera granskningsaktivitetsloggar och inloggningsaktivitetsloggar till Azure Monitor-loggar för vidare analys. 

* **Spårningsloggar**: [Rapporten om spårningsloggaktivitet](concept-audit-logs.md) ger dig åtkomst till historiken för varje uppgift som utförs i din klientorganisation.
* **Inloggningsloggar**: Med [rapporten om inloggningsaktivitet](concept-sign-ins.md) kan du se vem som har utfört de uppgifter som rapporteras i spårningsloggarna.

> [!NOTE]
> B2C-relaterade spårnings- och inloggningsaktivitetsloggar stöds inte just nu.
>

## <a name="prerequisites"></a>Krav 

Om du vill använda den här funktionen behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [registrera dig för en kostnadsfri utvärdering](https://azure.microsoft.com/free/).
* En Azure AD-klientorganisation.
* En användare som är *global administratör* eller *säkerhetsadministratör* för Azure AD-klientorganisationen.
* En Log Analytics-arbetsyta i din Azure-prenumeration. Läs om hur du [skapar en Log Analytics-arbetsyta](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="licensing-requirements"></a>Licenskrav

För att använda den här funktionen krävs en Azure AD Premium P1- eller P2-licens. Hitta rätt licens för dina behov i [Jämför allmänt tillgängliga funktioner i Free, Basic och Premium-utgåvorna](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="send-logs-to-azure-monitor"></a>Skicka loggar till Azure Monitor

1. Logga in på [Azure-portalen](https://portal.azure.com). 

2. Välj **Azure Active Directory** > **Diagnostic settings** -> **Add diagnostic setting**. Du kan också välja **Exportera inställningar** på sidan **Granskningsloggar** eller inloggningar för att komma till **konfigurationssidan** för diagnostikinställningar.  
    
3. Markera kryssrutan Skicka till **logganalys** arbetsyta på menyn **Diagnostikinställningar** och välj sedan **Konfigurera**.

4. Välj arbetsytan Log Analytics som du vill skicka loggarna till eller skapa en ny arbetsyta i den medföljande dialogrutan.  

5. Gör endera eller båda av följande:
    * Om du vill skicka granskningsloggar till log analytics-arbetsytan markerar du kryssrutan **AuditLogs.** 
    * Om du vill skicka inloggningsloggar till log analytics-arbetsytan markerar du kryssrutan **SignInLogs.**

6. Spara inställningen genom att välja **Spara**.

    ![Diagnostikinställningar](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Efter ca 15 minuter kontrollerar du att händelser strömmas till logganalysarbetsytan.

## <a name="next-steps"></a>Nästa steg

* [Analysera Azure AD-aktivitetsloggar med Azure Monitor-loggar](howto-analyze-activity-logs-log-analytics.md)
* [Installera och använda logganalysvyerna för Azure Active Directory](howto-install-use-log-analytics-views.md)
