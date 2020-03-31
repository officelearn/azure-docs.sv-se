---
title: Vanliga frågor om villkorlig åtkomst i Azure Active Directory | Microsoft-dokument
description: Få svar på vanliga frågor om villkorlig åtkomst i Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6842338bd27e4bea3436f0b249380ab773d60de6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368082"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Vanliga frågor och frågor om azure Active Directory villkorlig åtkomst

## <a name="which-applications-work-with-conditional-access-policies"></a>Vilka program fungerar med principer för villkorlig åtkomst?

Information om program som fungerar med principer för villkorlig åtkomst finns i [Program och webbläsare som använder regler för villkorlig åtkomst i Azure Active Directory](concept-conditional-access-cloud-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Tillämpas principer för villkorlig åtkomst för B2B-samarbete och gästanvändare?

Principer tillämpas för användare av samarbete mellan företag (B2B). I vissa fall kanske en användare inte kan uppfylla principkraven. En gästanvändares organisation kanske till exempel inte stöder multifaktorautentisering. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Gäller även en SharePoint Online-policy för OneDrive för företag?

Ja. En SharePoint Online-policy gäller även För OneDrive för företag.

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Varför kan jag inte ange en princip direkt på klientappar, till exempel Word eller Outlook?

En princip för villkorlig åtkomst anger krav för åtkomst till en tjänst. Den tillämpas när autentisering till den tjänsten sker. Principen anges inte direkt på ett klientprogram. I stället tillämpas den när en klient anropar en tjänst. En principuppsättning på SharePoint gäller till exempel klienter som anropar SharePoint. En principuppsättning på Exchange gäller outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Gäller en princip för villkorlig åtkomst för tjänstkonton?

Principer för villkorlig åtkomst gäller för alla användarkonton. Detta inkluderar användarkonton som används som tjänstkonton. Ett tjänstkonto som körs obevakat kan ofta inte uppfylla kraven i en princip för villkorlig åtkomst. Multifaktorautentisering kan till exempel krävas. Tjänstkonton kan uteslutas från en princip med hjälp av principhanteringsinställningar för villkorlig åtkomst. 

## <a name="are-microsoft-graph-apis-available-for-configuring-conditional-access-policies"></a>Är Microsoft Graph API:er tillgängliga för att konfigurera principer för villkorlig åtkomst?

För närvarande, nej. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Vilken är standardundansundanskapsprincipen för enhetsplattformar som inte stöds?

För närvarande tillämpas principer för villkorlig åtkomst selektivt på användare av iOS- och Android-enheter. Program på andra enhetsplattformar påverkas som standard inte av principen villkorlig åtkomst för iOS- och Android-enheter. En klientadministratör kan välja att åsidosätta den globala principen för att inte tillåta åtkomst till användare på plattformar som inte stöds.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Hur fungerar principer för villkorlig åtkomst för Microsoft Teams?

Microsoft Teams är starkt beroende av Exchange Online och SharePoint Online för grundläggande produktivitetsscenarier, till exempel möten, kalendrar och fildelning. Principer för villkorlig åtkomst som har angetts för dessa molnappar gäller för Microsoft Teams när en användare loggar in direkt i Microsoft Teams.

Microsoft Teams stöds också separat som en molnapp i Azure Active Directory Conditional Access-principer. Principer för villkorlig åtkomst som har angetts för en molnapp gäller för Microsoft Teams när en användare loggar in. Men utan rätt principer för andra appar som Exchange Online och SharePoint Online-användare kan fortfarande komma åt dessa resurser direkt.

Microsoft Teams stationära klienter för Windows och Mac stöder modern autentisering. Modern autentisering ger inloggning baserat på Azure Active Directory Authentication Library (ADAL) till Microsoft Office-klientprogram på olika plattformar.

## <a name="next-steps"></a>Nästa steg

- Information om hur du konfigurerar principer för villkorlig åtkomst för din miljö finns [i metodtipsen för villkorlig åtkomst i Azure Active Directory](best-practices.md). 
