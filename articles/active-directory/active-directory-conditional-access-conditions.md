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
ms.openlocfilehash: d868a59dd5d00dea410cbe8c373263e13903d3ba
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413152"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Vad är villkor i Azure Active Directory villkorlig åtkomst? 

Du kan styra hur behöriga användare åtkomst till dina appar i molnet med hjälp av [villkorlig åtkomst i Azure Active Directory (Azure AD)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal). I en princip för villkorlig åtkomst definierar du svaret till orsaken för att utlösa din princip. Ett exempel på ett svar är **gör detta**. Ett exempel beror **om det här händer**.

![Orsak och svar](./media/active-directory-conditional-access-conditions/10.png)


I samband med villkorlig åtkomst, **om det här händer** kallas en **villkor**. **Gör detta** kallas en **åtkomstkontroll**. Kombinationen av dina villkor och din åtkomstkontroller representerar en princip för villkorlig åtkomst.

![Princip för villkorlig åtkomst](./media/active-directory-conditional-access-conditions/61.png)


Villkor som du inte har konfigurerat i en princip för villkorlig åtkomst tillämpas inte. Vissa villkor är [obligatorisk](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-best-practices) att tillämpa en princip för villkorlig åtkomst till en miljö. 

Den här artikeln är en översikt över villkoren och hur de används i en princip för villkorlig åtkomst. 

## <a name="users-and-groups"></a>Användare och grupper

Villkor för användare och grupper är obligatoriskt i en princip för villkorlig åtkomst. I din princip kan du antingen välja **alla användare** eller Välj specifika användare och grupper.

![Användare och grupper](./media/active-directory-conditional-access-conditions/111.png)

När du väljer **alla användare**, din princip har tillämpats för alla användare i katalogen, inklusive gästanvändare.

När du **Välj användare och grupper**, du kan ange följande alternativ:

* **Alla gästanvändare** riktar sig mot en princip till B2B-gästanvändare. Det här tillståndet matchar ett användarkonto som har den **userType** attributet inställt på **gäst**. Du kan använda den här inställningen när en princip måste tillämpas när kontot skapas i ett flöde för inbjudan i Azure AD.

* **Katalogroller** riktar sig mot en princip baserat på en användares rolltilldelning. Det här villkoret har stöd för katalogroller som **Global administratör** eller **lösenordsadministratör**.

* **Användare och grupper** riktar sig mot specifika uppsättningar med användare. Du kan till exempel välja en grupp som innehåller alla medlemmar i personalavdelningen när en HR-app har valts som molnappen. En grupp kan vara någon typ av grupp i Azure AD, inklusive dynamiskt eller tilldelat säkerhets- och distributionsgrupper grupper.

Du kan också utesluta specifika användare eller grupper från en princip. Ett vanligt användningsfall är tjänstkonton om principen framtvingar multifaktorautentisering (MFA). 

Det är användbart för distributionen av en ny princip riktar in sig på specifika uppsättningar med användare. I en ny princip, bör du rikta en grunduppsättning med användare att verifiera funktionen. 



## <a name="cloud-apps"></a>Molnappar 

En molnapp är en webbplats eller tjänst. Webbplatser som skyddas av Azure AD Application Proxy är också molnappar. En detaljerad beskrivning av stöds molnappar finns i [molnbaserade appar tilldelningar](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#cloud-apps-assignments). 

Den **molnappar** villkor är obligatoriskt i en princip för villkorlig åtkomst. I din princip kan du antingen välja **alla molnappar** eller Välj specifika appar.

![Inkludera molnappar](./media/active-directory-conditional-access-conditions/03.png)

- Välj **alla molnappar** till baslinje-principerna ska gälla för hela organisationen. Använd detta alternativ för principer som kräver multifaktorautentisering när inloggningsrisk har identifierats för alla appar i molnet. En princip som tillämpas på **alla molnappar** gäller för åtkomst till alla webbplatser och tjänster. Den här inställningen är inte begränsat till de molnappar som visas på den **Välj appar** lista. 

- Välj enskilda molnappar till målet specifika tjänster av en princip. Du kan till exempel kräva att användarna ha en [kompatibel enhet](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) åtkomst till SharePoint Online. Den här principen tillämpas också till andra tjänster när de ansluter till SharePoint-innehåll. Ett exempel är Microsoft Teams. 

Du kan utesluta specifika appar från en princip. De här apparna är dock fortfarande omfattas av principer som tillämpas på de tjänster som de har åtkomst till. 



## <a name="sign-in-risk"></a>Inloggningsrisk

En inloggningsrisk är en indikator för hög, medel eller låg sannolikheten att en inloggningsförsök inte har gjorts av är tillförlitligt ägare för ett användarkonto. Azure AD beräknar inloggningsrisk nivå under en användare användarens inloggning. Den beräknade inloggning risknivån kan vara ett villkor i en princip för villkorlig åtkomst. 

![Risknivåer för inloggning](./media/active-directory-conditional-access-conditions/22.png)

Om du vill använda det här villkoret måste du ha [Azure Active Directory Identity Protection](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-identityprotection-enable) aktiverat.
 
Vanliga användningsområden för det här villkoret är principer som har följande skydd: 

- Blockera användare med hög risk. Det här skyddet förhindrar potentiellt icke oskadliga användare från att komma åt dina appar i molnet. 
- Kräv multifaktorautentisering för användare med en medelstor inloggningsrisk. Du kan ange ytterligare säker på att inloggningen är klar med är tillförlitligt ägare för ett konto genom att framtvinga multifaktorautentisering.

Mer information finns i avsnittet om [riskfyllda inloggningar](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-security-risky-sign-ins).  

## <a name="device-platforms"></a>Enhetsplattformar

Enhetsplattformen kännetecknas av operativsystemet som körs på din enhet. Azure AD identifierar plattformen med hjälp av information som tillhandahålls av enhet, till exempel användaragenten. Den här informationen är inte verifierad. Vi rekommenderar att en princip som tillämpas på alla plattformar. Principen bör antingen blockera åtkomst, Kräv efterlevnad med Microsoft Intune-principer eller kräva enheten vara ansluten till en domän. Standardvärdet är att tillämpa en princip på alla enhetsplattformar. 


![Konfigurera enhetsplattformar](./media/active-directory-conditional-access-conditions/24.png)

En lista över enhetsplattformar som stöds finns i [enheten plattform villkor](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#device-platform-condition).


Ett vanligt användningsfall för det här villkoret är en princip som begränsar åtkomsten till dina appar i molnet till [hanterade enheter](active-directory-conditional-access-policy-connected-applications.md#managed-devices). Fler scenarier inklusive enheten plattform villkoret, se [Azure Active Directory appbaserad villkorlig åtkomst](active-directory-conditional-access-mam.md).



## <a name="device-state"></a>Enhetens tillstånd

Enhetstillståndsvillkor utesluter hybrid Azure AD-anslutna enheter och enheter som har markerats som kompatibel från en princip för villkorlig åtkomst. Det här villkoret är användbart när en princip för endast ska tillämpas på en ohanterad enhet att tillhandahålla ytterligare sessionssäkerhet. Endast tillämpa exempelvis sessionskontroll för Microsoft Cloud App Security när en enhet är hanterad. 


![Konfigurera enhetens tillstånd](./media/active-directory-conditional-access-conditions/112.png)

Om du vill blockera åtkomst för ohanterade enheter kan implementera [enhetsbaserad villkorlig åtkomst](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online).


## <a name="locations"></a>Platser

Du kan definiera villkor baserat på var ett fel med hjälp av platser. 

![Konfigurera platser](./media/active-directory-conditional-access-conditions/25.png)

Vanliga användningsområden för det här villkoret är principer med de följande skydd:

- Kräv multifaktorautentisering för användare som ansluter till en tjänst när de är av företagets nätverk.  

- Blockera åtkomst för användare som ansluter till en tjänst från vissa länder eller regioner. 

Mer information finns i [vad är platsvillkoret för villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-locations).


## <a name="client-apps"></a>Klientappar

Med hjälp av klient apps villkor, kan du tillämpa en princip för olika typer av program. Exempel är webbplatser, tjänster, mobilappar och program. 



Ett program har klassificerats enligt följande:

- En webbplats eller tjänst om det använder web SSO-protokoll, SAML, WS-Fed eller OpenID Connect för en konfidentiell klient.

- Mobilapp eller skrivbordsprogram om mobilappen OpenID Connect används för en intern klient.

En lista över klientappar som du kan använda i din princip för villkorlig åtkomst finns i [klient apps villkor](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#client-apps-condition) i den tekniska referensen för Azure Active Directory villkorlig åtkomst.

Vanliga användningsområden för det här villkoret är principer med de följande skydd: 

- Kräver en [kompatibel enhet](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) för mobila och skrivbordsprogram som laddar ned stora mängder data till enheten. Tillåt webbläsaråtkomst från alla enheter på samma gång.

- Blockera åtkomst från webbprogram men tillåta åtkomst från mobila och skrivbordsprogram.

Du kan använda det här villkoret till web SSO och moderna autentiseringsprotokoll. Du kan också tillämpa den på e-postprogram som använder Microsoft Exchange ActiveSync. Exempel är de interna e-postappar på de flesta smartphones. 

Du kan bara välja klient apps villkoret om Microsoft Office 365 Exchange Online är den enda molnapp som du har valt.

![Molnappar](./media/active-directory-conditional-access-conditions/32.png)

Att välja **Exchange ActiveSync** som en klient apps villkor stöds endast om du inte har andra villkor som konfigurerats i en princip. Du kan dock begränsa omfånget för det här tillståndet gäller endast för plattformar som stöds.

 
![Tillämpa bara principen på plattformar som stöds](./media/active-directory-conditional-access-conditions/33.png)

Problemet endast gäller för plattformar som stöds är lika med alla enhetsplattformar i en [enheten plattform villkor](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online).

![Konfigurera enhetsplattformar](./media/active-directory-conditional-access-conditions/34.png)


 Mer information finns i dessa artiklar:

- [Konfigurera SharePoint Online och Exchange Online för villkorlig åtkomst i Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
 
- [Azure Active Directory appbaserad villkorlig åtkomst](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam). 


### <a name="legacy-authentication"></a>Äldre autentisering  

Villkorlig åtkomst används nu för äldre Microsoft Office-klienter som inte stöder modern autentisering. Det gäller även för klienter som använder e-postprotokoll som POP, IMAP och SMTP. Genom att använda äldre autentisering kan du konfigurera principer som **blockera åtkomst från andra klienter**.


![Konfigurera klientapparna](./media/active-directory-conditional-access-conditions/160.png)  


#### <a name="known-issues"></a>Kända problem

- Konfigurera en princip för **andra klienter** blockerar för hela organisationen från vissa klienter som SPConnect. Det här blocket inträffar eftersom äldre klienter autentiseras på oväntade sätt. Problemet gäller inte för viktiga Office-program som de äldre Office-klienterna. 

- Det kan ta upp till 24 timmar innan principen ska träda i kraft. 


#### <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F:** blockerar den här autentiseringen Microsoft Exchange Web Services?

Det beror på autentiseringsprotokollet som använder Exchange Web Services. Om Exchange Web Services-programmet använder modern autentisering kan det omfattas av den **mobilappar och skrivbordsklienter** klientappen. Grundläggande autentisering omfattas av den **andra klienter** klientappen.


**F:** vilka kontroller kan jag använda för **andra klienter**?

En kontroll kan konfigureras för **andra klienter**. Slutanvändarens upplevelse kommer dock att blockeras åtkomsten för alla fall. **Andra klienter** stöder inte kontroller som MFA och kompatibel enhet domänanslutning. 
 
**F:** vilka villkor kan jag använda för **andra klienter**?

Alla villkor kan konfigureras för **andra klienter**.

**F:** tillåter Exchange ActiveSync stöder alla villkor och kontroller?

Nej. I följande lista sammanfattas stöd för Exchange ActiveSync: 

- Exchange ActiveSync stöder bara användar- och meddelanderegler. Det stöder inte gäster eller roller. Om ett villkor för gäst eller roll är konfigurerad, blockeras alla användare. Exchange ActiveSync blockerar alla användare eftersom den inte kan fastställa om principen ska tillämpas på användaren eller inte.

- Exchange ActiveSync fungerar endast med Microsoft Exchange Online som molnappen. 

- Exchange ActiveSync stöder inte alla villkor utom klientappen själva. 

- Exchange ActiveSync kan konfigureras med en kontroll. Alla kontroller utom enhetsefterlevnad leda till ett block.

**F:** principerna gäller alla klientappar som standard framöver?

Nej. Det finns ingen ändring i princip standardbeteendet. Principerna fortsätta att tillämpas på webbläsare och mobilappar och skrivbordsklienter som standard.



## <a name="next-steps"></a>Nästa steg

- Om du vill ta reda på hur du konfigurerar en princip för villkorlig åtkomst kan se [Snabbstart: kräver MFA för specifika appar med villkorlig åtkomst i Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-app-based-mfa).

- För att konfigurera principer för villkorlig åtkomst för din miljö, se den [bästa praxis för villkorlig åtkomst i Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-best-practices). 

