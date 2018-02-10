---
title: "Villkoren i Azure Active Directory för villkorlig åtkomst | Microsoft Docs"
description: "Lär dig hur tilldelningar används i Azure Active Directory för villkorlig åtkomst för att utlösa en princip."
services: active-directory
keywords: "villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 22cc43a861e2a9d1d0c508da362a5f0b48d0c268
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="conditions-in-azure-active-directory-conditional-access"></a>Villkoren i Azure Active Directory för villkorlig åtkomst 

Med [villkorlig åtkomst i Azure Active Directory (AD Azure)](active-directory-conditional-access-azure-portal.md), du kan styra hur behöriga användare åtkomst till dina molnappar. I en princip för villkorlig åtkomst definierar du svaret (”gör”) till orsaken till att utlösa principen (”när detta sker”). 

![Kontroll](./media/active-directory-conditional-access-conditions/10.png)


I samband med villkorlig åtkomst:

- ”**När detta sker**” kallas **villkor**. 
- ”**Gör du så här**” kallas **åtkomstkontroller**.

Kombinationen av dina villkor med din åtkomstkontroller representerar en princip för villkorlig åtkomst.

![Kontroll](./media/active-directory-conditional-access-conditions/61.png)

Den här artikeln ger en översikt över villkor och hur de används i en princip för villkorlig åtkomst. 


## <a name="users-and-groups"></a>Användare och grupper

Villkor för användare och grupper är obligatoriskt i en princip för villkorlig åtkomst. I din princip kan du antingen väljer **alla användare** eller Välj användare och grupper.

![Kontroll](./media/active-directory-conditional-access-conditions/02.png)

När du väljer:

- **Alla användare**, din principen gäller för alla användare med i katalogen. Detta inkluderar gästanvändare.

- **Välj användare och grupper**, du kan rikta specifika uppsättningar med användare, till exempel alla medlemmar i HR-avdelningen när de loggar in på en HR-app. 

- En grupp, det kan vara någon typ av grupp i Azure AD, inklusive dynamiska eller tilldelade säkerhets- och distributionsgrupper grupper.

Du kan också utesluta specifika användare eller grupper från en princip. Vanliga användningsfall är tjänstkonton om principen tillämpar multifaktorautentisering. 

Målobjekt för specifika uppsättningar med användare är användbart för distribution av en ny princip. I en ny princip bör du använda en grunduppsättning med användare att verifiera funktionen. 



## <a name="cloud-apps"></a>Molnappar 

En molnapp är en webbplatser eller tjänst. Detta inkluderar webbplatser som skyddas av Azure Application Proxy. En detaljerad beskrivning av molnappar som stöds finns i [molnet appar tilldelning](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).    

Molnet appar villkoret är obligatoriskt i en princip för villkorlig åtkomst. I din princip kan du antingen väljer **alla molnappar** eller markera specifika appar.

![Kontroll](./media/active-directory-conditional-access-conditions/03.png)

Du kan välja:

- **Alla molnappar** principer för baslinjen ska tillämpas för hela organisationen. Ett vanligt användningsfall för den här markeringen är en princip som kräver multifaktorautentisering när inloggningen risk har upptäckts för alla molnappar.

- Enskilda molnappar till specifika mål-tjänster av en princip. Du kan till exempel kräva att användarna har en [kompatibel enhet](active-directory-conditional-access-policy-connected-applications.md) åtkomst till SharePoint Online. Den här principen gäller även andra tjänster när de har åtkomst till SharePoint-innehåll, till exempel Microsoft Teams. 

Du kan också utesluta specifika appar från en princip; de här apparna är dock fortfarande följer principer som tillämpas på de komma åt tjänster. 



## <a name="sign-in-risk"></a>Inloggningsrisk

Logga in risk är en indikator för sannolikheten (hög, medel eller låg) ett försök att logga in inte utfördes av legitima ägaren till ett användarkonto. Azure AD beräknar inloggning risknivå under inloggning för en användare. Du kan använda den beräknade inloggning risknivån som villkor i en princip för villkorlig åtkomst. 

![Villkor](./media/active-directory-conditional-access-conditions/22.png)

Om du vill använda det här villkoret behöver du ha [Azure Active Directory Identity Protection](active-directory-identityprotection.md) aktiverat.
 
Vanliga användningsområden för det här villkoret är principer som:

- Blockera en användare med hög inloggning risk för att förhindra eventuellt inte berättigat användare från att komma åt dina molnappar. 
- Kräv Multi-Factor authentication när för användare med medelhög risk inloggning. Du kan framtvinga multifaktorautentisering för att tillhandahålla ytterligare förtroende att logga in utförs av legitima ägaren till ett konto.

Mer information finns i avsnittet om [riskfyllda inloggningar](active-directory-identityprotection.md#risky-sign-ins).  

## <a name="device-platforms"></a>Enhetsplattformar

Enhetsplattformen kännetecknas av operativsystemet som körs på enheten. Azure AD identifierar plattformen genom att använda information från enheten, till exempel användaragent. Eftersom den här informationen är overifierade, rekommenderas det att alla plattformar har en princip som tillämpats på dem, antingen genom att blockera åtkomst till, kompatibilitet med Intune-principer eller som kräver enheten vara ansluten till en domän. Standardvärdet är att använda principen för alla enhetsplattformar. 


![Villkor](./media/active-directory-conditional-access-conditions/02.png)

En fullständig lista över enhetsplattformar som stöds finns i [enhet plattform villkoret](active-directory-conditional-access-technical-reference.md#device-platform-condition).


Ett vanligt användningsfall för det här villkoret är en princip som begränsar åtkomsten till dina molnappar till [betrodda enheter](active-directory-conditional-access-policy-connected-applications.md#trusted-devices). Fler scenarier inklusive villkoret enhet plattform, se [Azure Active Directory app-baserad villkorlig åtkomst](active-directory-conditional-access-mam.md).


## <a name="locations"></a>Platser

Med platser har du möjlighet att definiera villkor som är baserade på där ett anslutningsförsök initierades från. 
     
![Villkor](./media/active-directory-conditional-access-conditions/03.png)

Vanliga användningsområden för det här villkoret är principer som:

- Kräva multifaktorautentisering för användare som ansluter till en tjänst när är utanför företagsnätverket.  

- Neka åtkomst för användare som ansluter till en tjänst från specifika länder eller regioner. 

Mer information finns i [plats villkor i Azure Active Directory för villkorlig åtkomst](active-directory-conditional-access-locations.md).


## <a name="client-apps"></a>Klientappar

Villkoret klienten appar kan du tillämpa en princip för olika typer av program, exempelvis:

- Webbplatser och tjänster
- Mobila appar och program. 

![Villkor](./media/active-directory-conditional-access-conditions/04.png)

Ett program är klassificerade som:

- En webbplats eller tjänst om det använder web SSO-protokoll, SAML WS-Fed eller OpenID Connect för en konfidentiell klient.

- En en mobil app eller ett skrivbordsprogram om mobilappen OpenID Connect används för en intern klient.

En fullständig lista över de klientappar som du kan använda i din princip för villkorlig åtkomst finns i [Teknisk referens för Azure Active Directory villkorlig åtkomst](active-directory-conditional-access-technical-reference.md#client-apps-condition).

Vanliga användningsområden för det här villkoret är principer som:

- Kräver en [kompatibel enhet](active-directory-conditional-access-policy-connected-applications.md) för bärbara och stationära program som hämtar stora mängder data på enheten, samtidigt som webbläsaråtkomst från valfri enhet.

- Blockera åtkomst från webbprogram och tillåta åtkomst från bärbara och stationära program.

Förutom att använda enkel inloggning på webben och protokoll för modern autentisering kan använda du det här villkoret till e postprogram som använder Exchange ActiveSync, t.ex. interna e-postappar på de flesta smartphones. För närvarande måste klientappar som använder äldre protokoll skyddas med hjälp av AD FS.

 Mer information finns i:

- [Ställ in SharePoint Online och Exchange Online för Azure Active Directory för villkorlig åtkomst](active-directory-conditional-access-no-modern-authentication.md)
 
- [Azure Active Directory app-baserad villkorlig åtkomst](active-directory-conditional-access-mam.md) 








## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst finns [Kom igång med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö finns i [bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md). 

