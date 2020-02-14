---
title: Azure Active Directory vanliga frågor och svar om villkorlig åtkomst | Microsoft Docs
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
ms.openlocfilehash: baa44481c4641f69ead5335298316c837062d2c0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186052"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Vanliga frågor om villkorlig åtkomst för Azure Active Directory

## <a name="which-applications-work-with-conditional-access-policies"></a>Vilka program fungerar med principer för villkorlig åtkomst?

Information om program som fungerar med principer för villkorlig åtkomst finns i [program och webbläsare som använder villkorliga åtkomst regler i Azure Active Directory](concept-conditional-access-cloud-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Tillämpas principer för villkorlig åtkomst för B2B-samarbete och gäst användare?

Principer tillämpas för samarbets användare för företag till företag (B2B). Men i vissa fall kanske en användare inte kan uppfylla princip kraven. Till exempel kanske en gäst användares organisation inte stöder Multi-Factor Authentication. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Gäller även en SharePoint Online-princip för OneDrive för företag?

Ja. En SharePoint Online-princip gäller även för OneDrive för företag.

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Varför kan jag inte ange en princip direkt på klient program, som Word eller Outlook?

En princip för villkorlig åtkomst anger krav för åtkomst till en tjänst. Den används när autentiseringen till den tjänsten sker. Principen har inte ställts in direkt på ett klient program. I stället tillämpas den när en klient anropar en tjänst. En princip uppsättning i SharePoint gäller till exempel klienter som anropar SharePoint. En princip som har angetts för Exchange gäller för Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Gäller en princip för villkorlig åtkomst för tjänst konton?

Principer för villkorlig åtkomst gäller för alla användar konton. Detta inkluderar användar konton som används som tjänst konton. Ett tjänst konto som körs obevakat kan ofta inte uppfylla kraven för en princip för villkorlig åtkomst. Till exempel kan Multi-Factor Authentication krävas. Tjänst konton kan uteslutas från en princip med hjälp av princip hanterings inställningar för villkorlig åtkomst. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Är Graph API: er tillgängliga för att konfigurera principer för villkorlig åtkomst?

För närvarande, nej. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Vad är standard principen för undantag för enhets plattformar som inte stöds?

Principer för villkorlig åtkomst tillämpas för närvarande selektivt på användare av iOS-och Android-enheter. Program på andra plattformar är som standard inte påverkade av principen för villkorlig åtkomst för iOS-och Android-enheter. En klient administratör kan välja att åsidosätta den globala principen för att neka åtkomst till användare på plattformar som inte stöds.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Hur fungerar principer för villkorlig åtkomst för Microsoft Teams?

Microsoft Teams är mycket beroende av Exchange Online och SharePoint Online för grundläggande produktivitets scenarier, som möten, kalendrar och fildelning. Principer för villkorlig åtkomst som anges för dessa molnappar gäller för Microsoft Teams när en användare loggar in direkt i Microsoft Teams.

Microsoft Teams stöds också separat som en molnbaserad app i Azure Active Directory principer för villkorlig åtkomst. Principer för villkorlig åtkomst som anges för en molnbaserad app gäller för Microsoft Teams när en användare loggar in. Men utan rätt principer för andra appar som Exchange Online-och SharePoint Online-användare kan fortfarande komma åt resurserna direkt.

Microsoft team Desktop-klienter för Windows och Mac stöder modern autentisering. Modern autentisering ger inloggning baserat på Azure Active Directory Authentication Library (ADAL) för att Microsoft Office klient program på olika plattformar.

## <a name="next-steps"></a>Nästa steg

- Information om hur du konfigurerar principer för villkorlig åtkomst för din miljö finns i [metod tips för villkorlig åtkomst i Azure Active Directory](best-practices.md). 
