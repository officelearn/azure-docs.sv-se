---
title: Vanliga frågor om Azure Active Directory SSO plugin | Microsoft Docs
description: Få svar på vanliga frågor om hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jira/växer samman.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: jeedes
ms.openlocfilehash: eb7576c48d3836eec8051d849cefe4c5ec6658c9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699214"
---
# <a name="faq-for-the-azure-active-directory-sso-plug-in"></a>Vanliga frågor om Azure Active Directory SSO plugin-program

Mer information finns nedan vanliga frågor och svar om du har en fråga om det här plugin-programmet.

## <a name="what-does-the-plug-in-do"></a>Vad gör plugin-programmet gör?

Plugin-programmet innehåller kapaciteten för enkel inloggning (SSO) för Atlassian Jira (inklusive Jira kärnor, Jira program, Jira helpdesk) och antal samverkande lokal programvara. Plugin-program fungerar med Azure Active Directory (AD Azure) som en identitetsleverantör (IdP).

## <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Vilka Atlassian produkter har plugin-program fungerar med?

Plugin-program fungerar med lokala versioner av Jira och växer samman.

## <a name="does-the-plug-in-work-on-cloud-versions"></a>Är den plugin-programmet i molnet versioner?

Nej. Plugin-programmet stöder endast lokala versioner av Jira och växer samman.

## <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Vilka versioner av Jira och antal samverkande stöder plugin-programmet?

Plugin-programmet stöder följande versioner:

* Jira Core- och programvara: 6.0 7,8
* Jira helpdesk: 3.0 och 3.2
* Antal samverkande: 5.0 till 5.10

## <a name="is-the-plug-in-free-or-paid"></a>Är gratis plugin-programmet eller betald?

Det är ett kostnadsfritt tillägg.

## <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Jag behöver starta om Jira eller växer samman när jag distribuerar plugin-programmet?

En omstart krävs inte. Du kan börja använda plugin-programmet omedelbart.

## <a name="how-do-i-get-support-for-the-plug-in"></a>Hur får jag support för plugin-programmet?

Du kan nå ut till den [Azure AD SSO Integration Team](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) för stöd krävs för den här plugin-programmet. Teamet svarar i 24-48 kontorstid.

Du kan också medföra ett supportärende med Microsoft via Azure portal-kanalen.

## <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Skulle plugin arbetet på en Mac- eller Ubuntu installation av Jira och antal samverkande?

Vi har testat plugin-programmet bara på 64-bitars Windows Server-installationer av Jira och växer samman.

## <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Stöder plugin-program fungerar med IdPs än Azure AD?

Nej. Den fungerar bara med Azure AD.

## <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Vilken version av SAML har plugin-program fungerar med?

Den fungerar med SAML 2.0.

## <a name="does-the-plug-in-do-user-provisioning"></a>Plugin-programmet gör användaretablering?

Nej. Plugin-programmet innehåller SAML 2.0-baserade SSO. Användaren måste tillhandahållas i programmet innan SSO-inloggning.

## <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Har stöd för plugin-programmet kluster-versioner av Jira och växer samman?

Nej. Plugin-program fungerar med lokala versioner av Jira och växer samman.

## <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Stöder plugin-program fungerar med HTTP-versioner av Jira och antal samverkande?

Nej. Plugin-program fungerar med endast HTTPS-aktiverad installationer.