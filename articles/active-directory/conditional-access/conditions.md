---
title: Vad är villkor i Azure Active Directory villkorlig åtkomst? | Microsoft Docs
description: Lär dig hur villkor används i Azure Active Directory villkorlig åtkomst för att utlösa en princip.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: eb7d5dc2d38d814625e904d1270446f5f1671624
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321109"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Vad är villkor i Azure Active Directory villkorlig åtkomst? 

Du kan styra hur användare kommer åt dina appar i molnet med hjälp av [villkorlig åtkomst i Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). I en princip för villkorlig åtkomst definierar du svaret (”sedan gör du”) att orsaken för att utlösa principen (”när detta sker”). 

![Orsak och svar](./media/conditions/10.png)


I samband med villkorlig åtkomst, **om det här händer** kallas en **villkor**. **Gör detta** kallas en **åtkomstkontroll**. Kombinationen av dina villkor och din åtkomstkontroller representerar en princip för villkorlig åtkomst.

![Princip för villkorlig åtkomst](./media/conditions/61.png)


Villkor som du inte har konfigurerat i en princip för villkorlig åtkomst tillämpas inte. Vissa villkor är [obligatorisk](best-practices.md) att tillämpa en princip för villkorlig åtkomst till en miljö. 

Den här artikeln är en översikt över villkoren och hur de används i en princip för villkorlig åtkomst. 

## <a name="users-and-groups"></a>Användare och grupper

Villkor för användare och grupper är obligatoriskt i en princip för villkorlig åtkomst. I din princip kan du antingen välja **alla användare** eller Välj specifika användare och grupper.

![Användare och grupper](./media/conditions/111.png)

När du väljer **alla användare**, din princip har tillämpats för alla användare i katalogen, inklusive gästanvändare.

När du **Välj användare och grupper**, du kan ange följande alternativ:

* **Alla gästanvändare** riktar sig mot en princip till B2B-gästanvändare. Det här tillståndet matchar ett användarkonto som har den **userType** attributet inställt på **gäst**. Använd den här inställningen när en princip måste tillämpas när kontot skapas i ett flöde för inbjudan i Azure AD.

* **Katalogroller** riktar sig mot en princip baserat på en användares rolltilldelning. Det här villkoret har stöd för katalogroller som **Global administratör** eller **lösenordsadministratör**.

* **Användare och grupper** riktar sig mot specifika uppsättningar med användare. Du kan till exempel välja en grupp som innehåller alla medlemmar i personalavdelningen när en HR-app har valts som molnappen. En grupp kan vara någon typ av grupp i Azure AD, inklusive dynamiskt eller tilldelat säkerhets- och distributionsgrupper grupper.

Du kan också utesluta specifika användare eller grupper från en princip. Ett vanligt användningsfall är tjänstkonton om principen framtvingar multifaktorautentisering (MFA). 

Det är användbart för distributionen av en ny princip riktar in sig på specifika uppsättningar med användare. I en ny princip, bör du rikta en grunduppsättning med användare att verifiera funktionen. 



## <a name="cloud-apps"></a>Molnappar 

En molnapp är en webbplats eller tjänst. Webbplatser som skyddas av Azure AD Application Proxy är också molnappar. En detaljerad beskrivning av stöds molnappar finns i [molnbaserade appar tilldelningar](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#cloud-apps-assignments). 

Den **molnappar** villkor är obligatoriskt i en princip för villkorlig åtkomst. I din princip kan du antingen välja **alla molnappar** eller Välj specifika appar.

![Inkludera molnappar](./media/conditions/03.png)

Välj:

- **Alla molnappar** till baslinje-principerna ska gälla för hela organisationen. Använd detta alternativ för principer som kräver multifaktorautentisering när inloggningsrisk har identifierats för alla appar i molnet. En princip som tillämpas på **alla molnappar** gäller för åtkomst till alla webbplatser och tjänster. Den här inställningen är inte begränsat till de molnappar som visas på den **Välj appar** lista. 

- Enskilda molnappar till målet specifika tjänster av en princip. Du kan till exempel kräva att användarna ha en [kompatibel enhet](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) åtkomst till SharePoint Online. Den här principen tillämpas också till andra tjänster när de ansluter till SharePoint-innehåll. Ett exempel är Microsoft Teams. 

Du kan utesluta specifika appar från en princip. De här apparna är dock fortfarande omfattas av principer som tillämpas på de tjänster som de har åtkomst till. 



## <a name="sign-in-risk"></a>Inloggningsrisk

En inloggningsrisk är en indikator för sannolikheten (hög, medel eller låg) som en inloggning inte har gjorts av är tillförlitligt ägare för ett användarkonto. Azure AD beräknar inloggningsrisk nivå under en användare användarens inloggning. Du kan använda den beräknade inloggning risknivån som villkor i en princip för villkorlig åtkomst.

![Risknivåer för inloggning](./media/conditions/22.png)

Om du vill använda det här villkoret måste du ha [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable) aktiverat.
 
Vanliga användningsområden för det här villkoret är principer som har följande skydd: 

- Blockera användare med hög risk. Det här skyddet förhindrar potentiellt icke oskadliga användare från att komma åt dina appar i molnet. 
- Kräv multifaktorautentisering för användare med en medelstor inloggningsrisk. Du kan ange ytterligare säker på att inloggningen är klar med är tillförlitligt ägare för ett konto genom att framtvinga multifaktorautentisering.

Mer information finns i [blockera åtkomst när en session risk identifieras](app-sign-in-risk.md).  

## <a name="device-platforms"></a>Enhetsplattformar

Enhetsplattformen kännetecknas av operativsystemet som körs på din enhet. Azure AD identifierar plattformen med hjälp av information som tillhandahålls av enhet, till exempel användaragenten. Den här informationen är inte verifierad. Vi rekommenderar att en princip som tillämpas på alla plattformar. Principen bör antingen blockera åtkomst, Kräv efterlevnad med Microsoft Intune-principer eller kräva enheten vara ansluten till en domän. Standardvärdet är att tillämpa en princip på alla enhetsplattformar. 


![Konfigurera enhetsplattformar](./media/conditions/24.png)

En lista över enhetsplattformar som stöds finns i [enheten plattform villkor](technical-reference.md#device-platform-condition).


Ett vanligt användningsfall för det här villkoret är en princip som begränsar åtkomsten till dina appar i molnet till [hanterade enheter](require-managed-devices.md). Fler scenarier inklusive enheten plattform villkoret, se [Azure Active Directory appbaserad villkorlig åtkomst](app-based-conditional-access.md).



## <a name="device-state"></a>Enhetens tillstånd

Enhetstillståndsvillkor utesluter hybrid Azure AD-anslutna enheter och enheter som har markerats som kompatibel från en princip för villkorlig åtkomst. 


![Konfigurera enhetens tillstånd](./media/conditions/112.png)

Det här villkoret är användbart när en princip för endast ska tillämpas på en ohanterad enhet att tillhandahålla ytterligare sessionssäkerhet. Endast tillämpa exempelvis sessionskontroll för Microsoft Cloud App Security när en enhet är hanterad. 

## <a name="locations"></a>Platser

Du kan definiera villkor baserat på var ett fel med hjälp av platser. 

![Konfigurera platser](./media/conditions/25.png)

Vanliga användningsområden för det här villkoret är principer med de följande skydd:

- Kräv multifaktorautentisering för användare som ansluter till en tjänst när de är av företagets nätverk.  

- Blockera åtkomst för användare som ansluter till en tjänst från vissa länder eller regioner. 

Mer information finns i [vad är platsvillkoret för villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).


## <a name="client-apps"></a>Klientappar

Som standard gäller principer för villkorlig åtkomst för följande appar:

- [Webbläsarbaserade appar](technical-reference.md#supported-browsers) – webbläsarbaserade appar innehåller webbplatser som använder SAML, WS-Federation och OpenID Connect web SSO-protokoll. Detta gäller även för en webbplats eller webbapp-tjänst som har registrerats som en konfidentiell OAuth-klient. Till exempel Office 365 SharePoint-webbplats. 

- [Mobilappar och skrivbordsappar som använder modern autentisering](technical-reference.md#supported-mobile-applications-and-desktop-clients) -de här apparna inkluderar Office-program och phone-appar. 


Dessutom kan du ange en principen på specifika klientappar som inte använder modern autentisering, till exempel:

- Klientappar som använder Microsoft Exchange ActiveSync-protokollet. När en princip block med Exchange ActiveSync, får berörda användare ett enda karantän e-postmeddelande med information om varför de blockeras. Om det behövs, innehåller e-postmeddelandet anvisningar för att registrera sina enheter med Intune.

- Andra klienter. De här apparna inkluderar klienter som använder grundläggande autentisering med e-postprotokoll som IMAP-, MAPI-, POP-, SMTP- och äldre Office-program som inte använder modern autentisering. Mer information finns i [hur modern autentisering fungerar för Office 2013 och Office 2016 klientappar](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016).

![Klientappar](./media/conditions/41.png)

Vanliga användningsområden för det här villkoret är principer med följande krav:

- **[Kräv en hanterad enhet](require-managed-devices.md)**  för mobila och skrivbordsprogram som laddar ned data till en enhet. Tillåt webbläsaråtkomst från alla enheter på samma gång. Det här scenariot förhindrar sparar och synkroniserar dokument till en ohanterad enhet. Med den här metoden kan du minska sannolikheten för förlust av data om enheten tappas bort eller blir stulen.

- **[Kräv en hanterad enhet](require-managed-devices.md)**  med ActiveSync för att få åtkomst till Exchange Online.

- **[Blockera äldre autentisering](block-legacy-authentication.md)**  till Azure AD (andra klienter)

- Blockera åtkomst från webbprogram men tillåta åtkomst från mobila och skrivbordsprogram.



### <a name="exchange-activesync-clients"></a>Exchange ActiveSync-klienter

Du kan bara välja **Exchange ActiveSync-klienter** om:


- Microsoft Office 365 Exchange Online är den enda molnapp som du har valt.

    ![Molnappar](./media/conditions/32.png)

- Du har inte andra villkor som konfigurerats i en princip. Men du kan begränsa omfånget för det här tillståndet gäller endast för [plattformar som stöds](technical-reference.md#device-platform-condition).
 
    ![Tillämpa bara principen på plattformar som stöds](./media/conditions/33.png)


När åtkomst har blockerats eftersom en [hanterad enhet](require-managed-devices.md) är krävs, de berörda användarna får en enda e-post som hjälper dem att använda Intune. 

Om det krävs en godkänd app måste få riktlinjer för att installera och använda Outlook-klienten för mobila användare som påverkas.

I andra fall, till exempel blockeras om MFA krävs, de berörda användarna, eftersom klienter som använder grundläggande autentisering inte stöder MFA.

Du kan bara använda den här inställningen till användare och grupper. Det stöder inte gäster eller roller. Om ett villkor för gäst eller roll är konfigurerad, blockeras alla användare eftersom villkorlig åtkomst inte kan fastställa om principen ska tillämpas på användaren eller inte.


 Mer information finns i:

- [Konfigurera SharePoint Online och Exchange Online för villkorlig åtkomst i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
 
- [Azure Active Directory appbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). 



## <a name="next-steps"></a>Nästa steg

- Om du vill ta reda på hur du konfigurerar en princip för villkorlig åtkomst kan se [snabbstarten: Kräva MFA för specifika appar med villkorlig åtkomst i Azure Active Directory](app-based-mfa.md).

- För att konfigurera principer för villkorlig åtkomst för din miljö, se den [bästa praxis för villkorlig åtkomst i Azure Active Directory](best-practices.md). 

