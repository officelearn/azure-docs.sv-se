---
title: Azure Active Directory villkorlig åtkomst och svar | Microsoft Docs
description: Få svar på vanliga frågor om villkorlig åtkomst i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: ea93dab7df920f5a8ff8a1ae518b0bdbdcc80ae9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723821"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure Active Directory villkorlig åtkomst vanliga frågor och svar

## <a name="which-applications-work-with-conditional-access-policies"></a>Vilka program fungerar med principer för villkorlig åtkomst?

Information om program som fungerar med principer för villkorlig åtkomst finns [program och webbläsare som använder regler för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Tillämpas principer för villkorlig åtkomst för B2B-samarbete och gästanvändare?

Principerna tillämpas för business-to-business (B2B) samarbete användare. Men i vissa fall kan kan en användare inte uppfylla principkraven. En gästanvändare organisation kan till exempel inte stöd för multifaktorautentisering. 



## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>En SharePoint Online-principen gäller även för OneDrive för företag?

Ja. SharePoint Online-principen gäller även för OneDrive för företag.


## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Varför kan inte ange en princip på klientprogram som Word och Outlook?

En villkorlig åtkomstprincip anger krav för åtkomst till en tjänst. Den tillämpas när autentiseringen till den tjänsten sker. Principen har inte angetts direkt på ett klientprogram. I stället tillämpas när en klient anropar en tjänst. Till exempel gäller en princip på SharePoint-klienter som anropar SharePoint. En princip på Exchange gäller Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Gäller en princip för villkorlig åtkomst för tjänstkonton

Principer för villkorlig åtkomst gäller för alla användarkonton. Detta innefattar användarkonton som används som tjänstkonton. Ofta kan inte ett tjänstkonto som kör obevakad uppfylla kraven för en princip för villkorlig åtkomst. Multifaktorautentisering kan komma att krävas. Tjänstkonton som kan uteslutas från en princip med hjälp av villkorlig åtkomst principinställningarna för hantering. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Finns Graph API: er för att konfigurera principer för villkorlig åtkomst?

För närvarande inte. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Vad är undantag standardprincipen för plattformar för enheter som inte stöds?

För närvarande kan tillämpas selektivt principer för villkorlig åtkomst för användare av iOS och Android-enheter. Program för andra enhetsplattformar, som standard inte påverkas av principen för villkorlig åtkomst för iOS och Android-enheter. En klientadministratör kan du åsidosätta den globala principen att neka åtkomst till användare på plattformar som inte stöds.


## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Hur fungerar principer för villkorlig åtkomst för Microsoft Teams?

Microsoft-Teams bygger kraftigt på Exchange Online och SharePoint Online för huvudscenarier produktivitet som möten, kalendrar och fildelning. Principer för villkorlig åtkomst som ställs för dessa molnappar gäller för Microsoft-Teams när en användare loggar direkt till Microsoft-Teams.

Microsoft-Teams även stöds separat som en molnapp i Azure Active Directory-principer för villkorlig åtkomst. Principer för villkorlig åtkomst som har angetts för en molnappen gäller för Microsoft-Teams när en användare loggar in. Men utan rätt principer på andra appar som Exchange Online och SharePoint Online kan användare fortfarande att kunna komma åt resurserna direkt.

Microsoft-Teams skrivbordsklienter för Windows och Mac stöder modern autentisering. Modern autentisering kan logga in på Azure Active Directory Authentication Library (ADAL) till Microsoft Office-program baserat på plattformar.