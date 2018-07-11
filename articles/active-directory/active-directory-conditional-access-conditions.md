---
title: Vad är villkor i Azure Active Directory villkorlig åtkomst? | Microsoft Docs
description: Lär dig hur villkor används i Azure Active Directory villkorlig åtkomst för att utlösa en princip.
services: active-directory
keywords: villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 42792170593dbd94d0eae9b408c70f326891508a
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "36232572"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Vad är villkor i Azure Active Directory villkorlig åtkomst? 

Med [villkorlig åtkomst i Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), du kan styra hur behöriga användare åtkomst till dina appar i molnet. I en princip för villkorlig åtkomst definierar du svaret (”gör du”) att orsaken för att utlösa principen (”när detta sker”). 

![Kontroll](./media/active-directory-conditional-access-conditions/10.png)


I samband med villkorlig åtkomst:

- ”**Om det här händer**” kallas **villkor**. 
- ”**Gör detta**” kallas **åtkomstkontroller**.

Kombinationen av dina villkor med din åtkomstkontroller representerar en princip för villkorlig åtkomst.

![Kontroll](./media/active-directory-conditional-access-conditions/61.png)


Villkor som du inte har konfigurerat i en princip för villkorlig åtkomst tillämpas inte. Vissa villkor är [obligatorisk](active-directory-conditional-access-best-practices.md#whats-required-to-make-a-policy-work) att tillämpa en princip för villkorlig åtkomst till en miljö. 

Den här artikeln ger en översikt över villkoren och hur de används i en princip för villkorlig åtkomst. 

## <a name="users-and-groups"></a>Användare och grupper

Villkor för användare och grupper är obligatoriskt i en princip för villkorlig åtkomst. I din princip kan du antingen välja **alla användare** eller Välj specifika användare och grupper.

![Kontroll](./media/active-directory-conditional-access-conditions/111.png)

När du väljer:

- **Alla användare**, din princip har tillämpats för alla användare i katalogen. Detta inkluderar gästanvändare.

- **Välj användare och grupper**, du kan ange följande alternativ:

    - **Alla gästanvändare** – kan du rikta en princip till B2B-gästanvändare. Det här tillståndet matchar ett användarkonto med den *userType* attributet inställt på *gäst*. Du kan använda den här inställningen i fall där en princip måste tillämpas när kontot skapas i ett flöde för inbjudan i Azure AD.

    - **Katalogroller** -kan du rikta en princip baserat på en användares rolltilldelning. Det här villkoret har stöd för katalogroller som *Global administratör* eller *lösenordsadministratör*.

    - **Användare och grupper** – gör det möjligt för target specifika uppsättningar med användare. Du kan till exempel välja en grupp som innehåller alla medlemmar i HR-avdelningen när du har en HR-app som markerats som appar i molnet.

En grupp, det kan vara någon typ av grupp i Azure AD, inklusive dynamiskt eller tilldelat säkerhets- och distributionsgrupper grupper

Du kan också utesluta specifika användare eller grupper från en princip. Ett vanligt användningsfall är tjänstkonton om principen framtvingar multifaktorautentisering (MFA). 

Det är användbart för distributionen av en ny princip riktar in sig på specifika uppsättningar med användare. I en ny princip, bör du rikta en grunduppsättning med användare att verifiera funktionen. 



## <a name="cloud-apps"></a>Molnappar 

En molnapp är ett webbplatser eller tjänst. Detta inkluderar webbplatser som skyddas av Azure Application Proxy. En detaljerad beskrivning av molnappar som stöds finns i [tilldelning av appar i molnet](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).    

Villkor för moln-appar är obligatoriskt i en princip för villkorlig åtkomst. I din princip kan du antingen välja **alla molnappar** eller Välj specifika appar.

![Kontroll](./media/active-directory-conditional-access-conditions/03.png)

Du kan välja:

- **Alla molnappar** baslinje-principer tillämpas i hela organisationen. Ett vanligt användningsfall för det här alternativet är en princip som kräver multifaktorautentisering när inloggningsrisk har identifierats för alla appar i molnet. En princip som tillämpas på **alla molnappar** gäller för åtkomst till alla webbplats och tjänster. Den här inställningen är inte begränsat till de molnappar som visas på den **Välj molnappar** lista.

- Enskilda molnappar till målet specifika tjänster av en princip. Du kan till exempel kräva att användarna ha en [kompatibel enhet](active-directory-conditional-access-policy-connected-applications.md) åtkomst till SharePoint Online. Den här principen tillämpas också till andra tjänster när de ansluter till SharePoint-innehåll, till exempel Microsoft Teams. 

Du kan också utesluta specifika appar från en princip; de här apparna är dock fortfarande omfattas av principer som tillämpas på tjänster som de har åtkomst till. 



## <a name="sign-in-risk"></a>Inloggningsrisk

En inloggningsrisk är en indikator för sannolikheten (hög, medel eller låg) en inloggningsförsök inte har utförts av är tillförlitligt ägare för ett användarkonto. Azure AD beräknar inloggning risknivån under inloggning för en användare. Du kan använda den beräknade inloggning risknivån som villkor i en princip för villkorlig åtkomst. 

![Villkor](./media/active-directory-conditional-access-conditions/22.png)

Om du vill använda det här villkoret måste du ha [Azure Active Directory Identity Protection](active-directory-identityprotection.md) aktiverat.
 
Vanliga användningsområden för det här villkoret är principer som:

- Blockera användare med hög risk att förhindra potentiellt icke oskadliga användare från att komma åt dina appar i molnet. 
- Kräv multifaktorautentisering för användare med en medelstor inloggningsrisk. Du kan ange ytterligare säker på att inloggningen utförs av är tillförlitligt ägare för ett konto med multifaktorautentisering framtvingades.

Mer information finns i avsnittet om [riskfyllda inloggningar](active-directory-identityprotection.md#risky-sign-ins).  

## <a name="device-platforms"></a>Enhetsplattformar

Enhetsplattformen kännetecknas av operativsystemet som körs på din enhet. Azure AD identifierar plattformen med hjälp av information som tillhandahålls av enhet, till exempel användaragenten. Eftersom den här informationen är overifierade, rekommenderar vi att alla plattformar har en princip som tillämpats på dem, antingen genom att blockera åtkomst till, kompatibilitet med Intune-principer eller att kräva att enheten vara ansluten till en domän. Standardvärdet är att tillämpa principen på alla enhetsplattformar. 


![Villkor](./media/active-directory-conditional-access-conditions/24.png)

En fullständig lista över enhetsplattformar som stöds finns i [enheten plattform villkor](active-directory-conditional-access-technical-reference.md#device-platform-condition).


Ett vanligt användningsfall för det här villkoret är en princip som begränsar åtkomsten till dina appar i molnet till [hanterade enheter](active-directory-conditional-access-policy-connected-applications.md#managed-devices). Fler scenarier inklusive enheten plattform villkoret, se [Azure Active Directory appbaserad villkorlig åtkomst](active-directory-conditional-access-mam.md).



## <a name="device-state"></a>Enhetens tillstånd

Enhetstillståndsvillkor kan Azure AD-anslutna och enheter markerad som kompatibel som ska undantas från principen för villkorlig åtkomst. Detta är användbart när en princip för endast ska tillämpas på ohanterad enhet att tillhandahålla ytterligare sessionssäkerhet. Endast tillämpa exempelvis sessionskontroll för Microsoft Cloud App Security när en enhet är hanterad. 


![Villkor](./media/active-directory-conditional-access-conditions/112.png)

Om du vill blockera åtkomst för ohanterade enheter, bör du implementera [enhetsbaserad villkorlig åtkomst](active-directory-conditional-access-policy-connected-applications.md).


## <a name="locations"></a>Platser

Med platser har du möjlighet att definiera villkor som baseras på där ett anslutningsförsök initierades från. 
     
![Villkor](./media/active-directory-conditional-access-conditions/25.png)

Vanliga användningsområden för det här villkoret är principer som:

- Kräv multifaktorautentisering för användare som ansluter till en tjänst när är utanför företagets nätverk.  

- Blockera åtkomst för användare som ansluter till en tjänst från vissa länder eller regioner. 

Mer information finns i [vad är platsvillkoret för villkorlig åtkomst i Azure Active Directory?](active-directory-conditional-access-locations.md)


## <a name="client-apps"></a>Klientappar

Villkoret klient apps kan du tillämpa en princip för olika typer av program, till exempel:

- Webbplatser och tjänster
- Mobilappar och program. 



Ett program har klassificerats som:

- En webbplats eller tjänst om det använder web SSO-protokoll, SAML, WS-Fed eller OpenID Connect för en konfidentiell klient.

- En mobilapp eller skrivbordsprogram om mobilappen OpenID Connect används för en intern klient.

En fullständig lista över klientappar som du kan använda i din princip för villkorlig åtkomst finns i [klient apps villkor](active-directory-conditional-access-technical-reference.md#client-apps-condition) i den tekniska referensen för Azure Active Directory villkorlig åtkomst.

Vanliga användningsområden för det här villkoret är principer som:

- Kräver en [kompatibel enhet](active-directory-conditional-access-policy-connected-applications.md) för mobila och skrivbordsprogram som laddar ned stora mängder data till enheten, samtidigt som webbläsaråtkomst från valfri enhet.

- Blockera åtkomst från webbprogram och tillåta åtkomst från mobila och skrivbordsprogram.

Förutom att använda enkel inloggning på webben och protokoll för modern autentisering kan använda du det här tillståndet till e-postprogram som använder Exchange ActiveSync, t.ex. interna e-postappar i de flesta smartphones. För närvarande måste klientappar som använder äldre protokoll skyddas med hjälp av AD FS.

Du kan bara välja det här tillståndet om **Office 365 Exchange Online** är den enda molnapp som du har valt.

![Molnappar](./media/active-directory-conditional-access-conditions/32.png)

Att välja **Exchange ActiveSync** som klientappar villkor stöds endast om du inte har andra villkor i en princip som konfigurerats. Du kan dock begränsa omfånget för det här tillståndet gäller endast för plattformar som stöds.

 
![Plattformar som stöds](./media/active-directory-conditional-access-conditions/33.png)

Problemet endast gäller för plattformar som stöds är detsamma som att alla enhetsplattformar i en [enheten plattform villkor](active-directory-conditional-access-conditions.md#device-platforms).

![Plattformar som stöds](./media/active-directory-conditional-access-conditions/34.png)


 Mer information finns i:

- [Konfigurera SharePoint Online och Exchange Online för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-no-modern-authentication.md)
 
- [Azure Active Directory appbaserad villkorlig åtkomst](active-directory-conditional-access-mam.md) 


### <a name="legacy-authentication"></a>Äldre autentisering  

Villkorlig åtkomst används nu för äldre Office-klienter som inte stöder modern autentisering samt klienter som använder e-postprotokoll POP, IMAP, SMTP, t.ex. På så sätt kan du konfigurera principer som **blockera åtkomst från andra klienter**.


![Äldre autentisering](./media/active-directory-conditional-access-conditions/160.png)
 



#### <a name="known-issues"></a>Kända problem

- Konfigurera en princip för **andra klienter** blockerar för hela organisationen från vissa klienter som SPConnect. Detta beror på dessa äldre klienter autentiseras på oväntade sätt. Det här problemet gäller inte för högre Office-program som de äldre Office-klienterna. 

- Det kan ta upp till 24 timmar innan principen ska gälla. 


#### <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Blockerar Exchange Web Services (EWS)?**

Det beror på autentiseringsprotokollet som EWS använder. Om EWS-program använder modern autentisering kan omfattas det av klientappen ”mobilappar och skrivbordsklienter”. Om programmet EWS använder grundläggande autentisering, kommer det omfattas av klientappen ”andra klienter”.


**Vilka kontroller kan jag använda för andra klienter**

En kontroll kan konfigureras för ”andra klienter”. Slutanvändarens upplevelse kommer dock att blockera åtkomsten för alla fall. ”Andra klienter” stöder inte kontroller som MFA, kompatibel enhet, domänanslutning osv. 
 
**Vilka villkor kan jag använda för andra klienter?**

Eventuella villkor kan konfigureras för ”andra klienter”.

**Stöder Exchange ActiveSync alla villkor och kontroller?**

Nej. Här är en sammanfattning av stöd för Exchange ActiveSync (EAS):

- EAS har endast stöd för användar- och meddelanderegler. Det stöder inte Gäst, roller. Om gäst/rollen villkor är konfigurerad, kommer alla användare få blockeras eftersom vi inte kan avgöra om principen ska tillämpas på användaren eller inte.

- EAS fungerar bara med Exchange som molnappen. 

- EAS stöder inte alla villkor utom klientappen själva.

- EAS kan konfigureras med en kontroll (alla utom enhetsefterlevnad leder till block).

**Principerna som gäller för alla klientappar som standard framöver?**

Nej. Det finns ingen ändring i princip standardbeteendet. Principerna fortsätta gälla till webbläsaren och klienter för mobila program/desktop som standard.



## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar principer för villkorlig åtkomst finns i [kräver MFA för specifika appar med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).

- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö kan du läsa den [bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md). 

