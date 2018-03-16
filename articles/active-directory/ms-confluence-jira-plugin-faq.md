---
title: "Microsoft Azure Active Directory enkel inloggning Plugin vanliga frågor och svar | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Microsoft Azure Active Directory enkel inloggning för JIRA."
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
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: 571fbd5078f66375f6e81cba2a790121366f9d60
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-faq"></a>Microsoft Azure Active Directory enkel inloggning Plugin vanliga frågor och svar 

## <a name="1-whats-the-microsoft-sso-add-on"></a>1. Vad är Microsoft SSO-tillägg?

Det här tillägget innehåller kapaciteten för enkel inloggning för Atlassian's JIRA (inklusive JIRA kärnor, JIRA program, JIRA helpdesk) och antal samverkande lokal programvara. Tillägget fungerar med Azure AD som IdP.

## <a name="2-add-on-works-with-which-atlassian-products"></a>2. Tillägg fungerar med vilka Atlassian produkter?

Från och med nu fungerar tillägget med lokala versioner av JIRA och växer samman.

## <a name="3-does-this-add-on-work-on-cloud-version"></a>3. Fungerar det här tilläggsprogrammet på molnversionen?

Nej. Endast stöds lokala versioner av JIRA och antal samverkande.

## <a name="4-which-versions-of-jira-and-confluence-are-supported"></a>4. Vilka versioner av JIRA och antal samverkande stöds?

Nedan visas en lista över versioner som stöds:

* JIRA Core- och programvara: 6.0 7.2.2 
* JIRA helpdesk: 3.0 och 3.2 
* Antal samverkande: 5.0 till 5.10

## <a name="5-is-this-add-on-free-or-paid"></a>5. Är det här tillägget lediga eller betald?

Detta är ett kostnadsfritt tillägg och kan installeras från Atlassian marknadsplatsen.

## <a name="6-do-i-need-to-restart-jiraconfluence-once-i-deploy-the-add-on"></a>6. Behöver jag om JIRA/växer samman när jag distribuerar tillägget

Starta om är inte obligatoriska efter tillägg distribution. Du kan börja använda tillägget direkt efter distributionen.

## <a name="7-how-do-i-get-support-for-the-add-on"></a>7. Hur får jag support för tillägget?

Nå ut till oss på: <email> . Vi kommer att svara inom <> timmar. Du kan också medföra ett supportärende med Microsoft via Azure portal-kanalen. Du kan också kontakta oss på: <Number> mellan <> är att <> pm för veckodagar.

## <a name="8-would-this-add-on-work-on-mac-or-ubuntu-installation-of-jira-and-confluence"></a>8. Det här tilläggsprogrammet fungerar på Mac- eller Ubuntu installation av JIRA och antal samverkande?

Vi har testat det här tillägget bara på 64-bitars Windows server-installationer av JIRA och växer samman.

## <a name="9-does-this-add-on-work-with-other-idps-than-azure-ad"></a>9. Fungerar det här tillägget med andra IdPs än Azure AD?

Nej. Tillägg fungerar endast med Azure AD.

## <a name="10-what-version-of-saml-does-the-add-on-work-with"></a>10. Vilken version av SAML fungerar tillägget med?

Tillägg fungerar med SAML 2.0.

## <a name="11-does-the-add-on-do-use-provisioning-as-well"></a>11. Har tillägget Använd samt etablering?

Nej. Från och med nu här tillägget bara SAML 2.0-baserade enkel inloggning. Användaren måste tillhandahållas i programmet innan SSO-inloggning.

## <a name="12-are-cluster-versions-of-jira-and-confluence-supported-by-add-on"></a>12. Är klustret versioner av JIRA och antal samverkande stöds av tillägget?

Nej. Tillägget fungerar med lokala versioner av JIRA och växer samman.

## <a name="13-would-this-plugin-work-with-http-version-of-jira-and-confluence"></a>13. Det här plugin-programmet skulle fungera med HTTP-versionen av JIRA och antal samverkande?

Nej. Tillägg fungerar med HTTPS aktiverat endast installationer.

## <a name="14-do-i-need-to-buy-license-of-the-add-on"></a>14. Behöver jag köpa licens med tillägget?

Det är ett kostnadsfritt tillägg.