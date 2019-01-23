---
title: Azure Active Directory villkorsstyrd åtkomst vanliga frågor och svar | Microsoft Docs
description: Få svar på vanliga frågor om villkorlig åtkomst i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 4fc2a5aced68ad27b87f409ff4220443648b9d1f
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446059"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure Active Directory villkorsstyrd åtkomst vanliga frågor och svar

## <a name="which-applications-work-with-conditional-access-policies"></a>Vilka program fungerar med principer för villkorlig åtkomst?

Information om program som fungerar med principer för villkorlig åtkomst finns i [program och webbläsare som använder regler för villkorlig åtkomst i Azure Active Directory](technical-reference.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Upprätthålls principer för villkorlig åtkomst för B2B-samarbete och gästanvändare?

Principer tillämpas för business-to-business (B2B) samarbete användare. Men i vissa fall kan kanske en användare inte uppfyller principkraven. En gästanvändare organisation kan till exempel inte stöd för multifaktorautentisering. 



## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Gäller en princip för SharePoint Online också för OneDrive för företag?

Ja. En SharePoint Online-principen gäller även för OneDrive för företag.


## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Varför kan jag ange principer för klientappar som Word och Outlook?

En villkorlig åtkomstprincip anger krav för åtkomst till en tjänst. Den ska tillämpas när sker autentiseringen till den tjänsten. Principen har inte angetts direkt på ett klientprogram. I stället tillämpas när en klient anropar en tjänst. En princip som angetts SharePoint gäller exempelvis för klienter som anropar SharePoint. En princip som angetts Exchange gäller för Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Gäller principer för villkorlig åtkomst för tjänstkonton?

Principer för villkorlig åtkomst gäller för alla användarkonton. Detta innefattar användarkonton som används som tjänstkonton. Ofta inte kan ett tjänstkonto som kör obevakad uppfylla kraven i en princip för villkorlig åtkomst. Till exempel kan multifaktorautentisering krävas. Service-konton kan uteslutas från en princip med hjälp av villkorlig åtkomst principinställningarna för hantering. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Är Graph API: er tillgängliga för att konfigurera principer för villkorlig åtkomst?

Inte för närvarande. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Vad är standard undantagsprincipen för plattformar som inte stöds enheter?

För närvarande kan tillämpas selektivt principer för villkorlig åtkomst för användare av iOS- och Android-enheter. Program på andra plattformar för enheter, som standard inte påverkas av principen för villkorlig åtkomst för iOS och Android-enheter. Klientorganisationens administratör kan välja att åsidosätta den globala principen att neka åtkomst till användare på plattformar som inte stöds.


## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Hur fungerar principer för villkorlig åtkomst för Microsoft Teams?

Microsoft Teams förlitar sig kraftigt på Exchange Online och SharePoint Online för core produktivitet lösningar, till exempel möten, kalendrar och fildelning. Principer för villkorlig åtkomst som har angetts för de här molnapparna gäller för Microsoft Teams när en användare loggar direkt i Microsoft Teams.

Microsoft Teams kan också användas separat som en cloud-app i Azure Active Directory-principer för villkorlig åtkomst. Principer för villkorlig åtkomst som har angetts för en molnapp gäller för Microsoft Teams när en användare loggar in. Men utan rätt principer på andra appar som Exchange Online och SharePoint Online kan användare fortfarande att kunna komma åt dessa resurser direkt.

Microsoft Teams stationära klienter för Windows och Mac stöd för modern autentisering. Modern autentisering skapar stöd för inloggning baserat på de Azure Active Directory Authentication Library (ADAL) till Microsoft Office-program mellan plattformar.