---
title: Metod tips för villkorlig åtkomst i Azure Active Directory | Microsoft Docs
description: Lär dig mer om saker du bör känna till och vad du bör undvika när du konfigurerar principer för villkorlig åtkomst.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60d72a98a22fa85e87eb8560ad968415ca70f9a5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87275436"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Metod tips för villkorlig åtkomst i Azure Active Directory

Med [Azure Active Directory (Azure AD) villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md)kan du styra hur behöriga användare får åtkomst till dina molnappar. Den här artikeln innehåller information om:

- Saker du bör känna till 
- Det bör du undvika när du konfigurerar principer för villkorlig åtkomst. 

Den här artikeln förutsätter att du är bekant med begreppen och terminologin som beskrivs i [Vad är villkorlig åtkomst i Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)

## <a name="whats-required-to-make-a-policy-work"></a>Vad krävs för att en princip ska fungera?

När du skapar en ny princip finns det inga användare, grupper, appar eller åtkomst kontroller markerade.

![Molnappar](./media/best-practices/02.png)

För att principen ska fungera måste du konfigurera:

| Vad           | Hur                                  | Varför |
| :--            | :--                                  | :-- |
| **Molnappar** |Välj en eller flera appar.  | Målet med en princip för villkorlig åtkomst är att du ska kunna styra hur auktoriserade användare kan komma åt molnappar.|
| **Användare och grupper** | Välj minst en användare eller grupp som har behörighet att komma åt dina valda molnappar. | En princip för villkorlig åtkomst som inte har några tilldelade användare och grupper utlöses aldrig. |
| **Åtkomstkontroller** | Välj minst en åtkomstkontroll. | Om dina villkor är uppfyllda måste din principbehandlare veta vad den ska göra. |

## <a name="what-you-should-know"></a>Det här bör du veta

### <a name="how-are-conditional-access-policies-applied"></a>Hur tillämpas principer för villkorlig åtkomst?

Mer än en princip för villkorlig åtkomst kan gälla när du öppnar en molnbaserad app. I det här fallet måste alla principer som gäller vara uppfyllda. Om en princip till exempel kräver Multi-Factor Authentication (MFA) och en annan kräver en kompatibel enhet måste du slutföra MFA och använda en kompatibel enhet. 

Alla principer tillämpas i två faser:

- Fas 1: samla in sessionsinformation 
   - Samla in sessionsinformation, t. ex. användar plats och enhets identitet som krävs för utvärdering av principer. 
   - Under den här fasen kan användare se en certifikats tolk om enhetens efterlevnad är en del av dina principer för villkorlig åtkomst. Den här varningen kan inträffa för webb läsar appar när enhetens operativ system inte är Windows 10. 
   - Fas 1 av princip utvärderingen sker för aktiverade principer och principer i [endast rapport läge](concept-conditional-access-report-only.md).
- Fas 2: tvång 
   - Använd sessionsinformation som samlats in i fas 1 för att identifiera eventuella krav som inte har uppfyllts. 
   - Om det finns en princip som har kon figurer ATS för att blockera åtkomst, med blockera beviljande kontroll, kommer tvång att stoppas här och användaren kommer att blockeras. 
   - Användaren uppmanas sedan att slutföra ytterligare krav för beviljande kontroll som inte har uppfyllts under fas 1 i följande ordning, tills principen är nöjd:  
      - Multi-Factor Authentication 
      - Godkänd klient App/app-skydds princip 
      - Hanterad enhet (kompatibel eller hybrid Azure AD-anslutning) 
      - Villkor för användning 
      - Anpassade kontroller  
      - När beviljade kontroller har uppfyllts tillämpar du sessionsbaserade (appen tvingas, Microsoft Cloud App Security och token livs längd) 
   - Fas 2 av princip utvärderingen sker för alla aktiverade principer. 

### <a name="how-are-assignments-evaluated"></a>Hur utvärderas tilldelningar?

Alla tilldelningar är logiskt **ANDed**. Om du har konfigurerat fler än en tilldelning måste alla tilldelningar uppfyllas för att utlösa en princip.  

Om du behöver konfigurera ett plats villkor som gäller för alla anslutningar som görs utanför organisationens nätverk:

- Ta med **alla platser**
- Undanta **alla betrodda IP-adresser**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Vad ska jag göra om du är utelåst från Azure AD admin-portalen?

Om du är utelåst från Azure AD-portalen på grund av en felaktig inställning i en princip för villkorlig åtkomst:

- Det finns andra administratörer i din organisation som inte har blockerats än. En administratör med åtkomst till Azure Portal kan inaktivera den princip som påverkar din inloggning. 
- Om ingen av administratörerna i din organisation kan uppdatera principen måste du skicka in en support förfrågan. Microsoft support kan granska och uppdatera principer för villkorlig åtkomst som förhindrar åtkomst.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Vad händer om du har principer i den klassiska Azure-portalen och Azure Portal har kon figurer ATS?  

Båda principerna tillämpas av Azure Active Directory och användaren får bara åtkomst när alla krav är uppfyllda.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Vad händer om du har principer i Intune Silverlight-portalen och Azure Portal?

Båda principerna tillämpas av Azure Active Directory och användaren får bara åtkomst när alla krav är uppfyllda.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Vad händer om jag har flera principer för samma användare som har kon figurer ATS?  

För varje inloggning utvärderar Azure Active Directory alla principer och säkerställer att alla krav uppfylls innan åtkomst beviljas till användaren. Blockera åtkomst till trumf alla andra konfigurations inställningar. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>Fungerar villkorlig åtkomst med Exchange ActiveSync?

Ja, du kan använda Exchange ActiveSync i en princip för villkorlig åtkomst.

Vissa molnappar som SharePoint Online och Exchange Online stöder även bakåtkompatibla autentiseringsprotokoll. När ett klient program kan använda ett äldre autentiseringsprotokoll för att få åtkomst till en molnbaserad app, kan inte Azure AD tillämpa en princip för villkorlig åtkomst för det här åtkomst försöket. För att förhindra att en klient app kringgår verk ställandet av principer bör du kontrol lera om det är möjligt att endast aktivera modern autentisering för de berörda molnappar.

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Hur ska du konfigurera villkorlig åtkomst med Office 365-appar?

Eftersom Office 365-appar är anslutna rekommenderar vi att du tilldelar ofta använda appar tillsammans när du skapar principer.

Vanliga sammankopplade program är Microsoft Flow, Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, Office 365 SharePoint Online och Office 365 Yammer.

Det är viktigt att principer som kräver användar interaktioner, t. ex. Multi-Factor Authentication, när åtkomsten kontrol leras i början av en session eller uppgift. Om du inte gör det kan användarna inte utföra vissa uppgifter i en app. Om du till exempel behöver Multi-Factor Authentication på ohanterade enheter för åtkomst till SharePoint men inte till e-post, kommer användare som arbetar i e-postmeddelandet inte att kunna bifoga SharePoint-filer till ett meddelande. Mer information hittar du i artikeln [Vad är tjänst beroenden i Azure Active Directory villkorlig åtkomst?](service-dependencies.md).

## <a name="what-you-should-avoid-doing"></a>Vad du bör undvika

Med ramverket för villkorlig åtkomst får du en bra flexibel konfiguration. Men stor flexibilitet innebär också att du noga bör granska varje konfigurations princip innan du släpper den för att undvika oönskade resultat. I det här sammanhanget bör du ägna särskild uppmärksamhet på tilldelningar som påverkar kompletta uppsättningar, till exempel **alla användare/grupper/molnappar**.

I din miljö bör du undvika följande konfigurationer:

**Alla molnappar för alla användare:**

- **Blockera åtkomst** – den här konfigurationen blockerar hela organisationen, vilket är absolut inte en bra idé.
- **Kräv kompatibel enhet** – för användare som inte har registrerat sina enheter ännu, blockerar den här principen all åtkomst, inklusive åtkomst till Intune-portalen. Om du är en administratör utan en registrerad enhet, blockerar den här principen dig från att komma tillbaka till Azure Portal för att ändra principen.
- **Kräv domän anslutning** – den här princip block åtkomsten har också möjlighet att blockera åtkomst för alla användare i din organisation om du inte har en domänansluten enhet än.
- **Kräv app Protection-princip** – den här princip block åtkomsten har också möjlighet att blockera åtkomst för alla användare i din organisation om du inte har en Intune-princip. Om du är en administratör utan ett klient program som har en princip för Intune-App-skydd blockerar den här principen dig från att komma tillbaka till portaler, till exempel Intune och Azure.

**För alla användare, alla molnappar, alla enhets plattformar:**

- **Blockera åtkomst** – den här konfigurationen blockerar hela organisationen, vilket är absolut inte en bra idé.

## <a name="how-should-you-deploy-a-new-policy"></a>Hur ska du distribuera en ny princip?

Som ett första steg bör du utvärdera principen med hjälp av [verktyget vad om](what-if-tool.md).

När nya principer är klara för din miljö kan du distribuera dem i faser:

1. Tillämpa en princip för en liten uppsättning användare och kontrol lera att den fungerar som förväntat. 
1. När du expanderar en princip för att inkludera fler användare. Fortsätt att undanta alla administratörer från principen för att säkerställa att de fortfarande har åtkomst och kan uppdatera en princip om en ändring krävs.
1. Tillämpa en princip för alla användare endast om det behövs. 

Vi rekommenderar att du skapar ett användar konto som är:

- Dedikerad till princip administration 
- Exkluderas från alla dina principer

## <a name="policy-migration"></a>Princip migration

Överväg att migrera principerna som du inte har skapat i Azure Portal eftersom:

- Nu kan du adressera scenarier som du inte kan hantera tidigare.
- Du kan minska antalet principer som du måste hantera genom att konsolidera dem.   
- Du kan hantera alla principer för villkorlig åtkomst på en central plats.
- Den klassiska Azure-portalen har dragits tillbaka.   

Mer information finns i [Migrera klassiska principer i Azure Portal](policy-migration.md).

## <a name="next-steps"></a>Nästa steg

Om du vill veta:

- Hur du konfigurerar en princip för villkorlig åtkomst finns i [KRÄV MFA för vissa appar med Azure Active Directory villkorlig åtkomst](app-based-mfa.md).
- Hur du planerar dina principer för villkorlig åtkomst finns i [Planera distribution av villkorlig åtkomst i Azure Active Directory](plan-conditional-access.md).
