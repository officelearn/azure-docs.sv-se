---
title: Bästa praxis för villkorlig åtkomst i Azure Active Directory | Microsoft Docs
description: Läs mer om saker du bör känna till och vad det är du bör undvika att göra när du konfigurerar principer för villkorlig åtkomst.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d30fe326ef677ca4543534d57dd306ed2a660300
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895570"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Metodtips för villkorsstyrd åtkomst i Azure Active Directory

Med [villkorlig åtkomst i Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), du kan styra hur behöriga användare åtkomst till dina appar i molnet. Den här artikeln innehåller information om:

- Saker du bör känna till 
- Vad det är du bör undvika att göra när du konfigurerar principer för villkorlig åtkomst. 

Den här artikeln förutsätter att du känner koncept och terminologi som beskrivs i [vad är villkorlig åtkomst i Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)



## <a name="whats-required-to-make-a-policy-work"></a>Vad krävs för att göra en princip som fungerar?

När du skapar en ny princip, finns det inga användare, grupper, appar eller åtkomstkontroller som valts.

![Molnappar](./media/best-practices/02.png)


För att göra principen fungerar, måste du konfigurera:


| Vad           | Så här                                  | Varför |
| :--            | :--                                  | :-- |
| **Molnappar** |Välj en eller flera appar.  | Målet med en princip för villkorlig åtkomst är så att du kan styra hur behöriga användare har åtkomst till molnappar.|
| **Användare och grupper** | Välj minst en användare eller grupp som har behörighet att komma åt dina valda molnappar. | Principer för villkorlig åtkomst som har inga användare och grupper som har tilldelats, utlöses aldrig. |
| **Åtkomstkontroller** | Välj minst en åtkomstkontroll. | Om dina villkor uppfylls, måste princip-processor veta vad du gör. |




## <a name="what-you-should-know"></a>Det här bör du känna till



### <a name="how-are-conditional-access-policies-applied"></a>Hur tillämpas principer för villkorlig åtkomst?

Mer än en princip för villkorlig åtkomst kan tillkomma när du använder en molnapp. I det här fallet måste vara uppfyllda alla principer som gäller. Till exempel om en princip kräver MFA och andra kräver en kompatibel enhet, måste du genomgå MFA och använda en kompatibel enhet. 

Alla principer som tillämpas i två faser:

- I den **första** fasen alla principer utvärderas och samlas alla åtkomstkontroller som inte är uppfyllt. 

- I den **andra** fas, uppmanas du för att uppfylla kraven som du inte har uppfyllts. Om en av principerna blockerar, är du blockerad och inte uppmanas att uppfylla andra principkontroller. Om ingen av principerna som blockerar du uppmanas du att uppfylla andra principkontroller i följande ordning:

    ![Beställa](./media/best-practices/06.png)
    
    Externa MFA-leverantörer och användningsvillkor kommer nästa.



### <a name="how-are-assignments-evaluated"></a>Hur utvärderas tilldelningar?

Alla tilldelningar är logiskt **and**. Om du har mer än en tilldelning som har konfigurerats kan måste alla tilldelningar uppfyllas för att utlösa en princip.  

Om du vill konfigurera en platsvillkor som gäller för alla anslutningar som görs från utanför företagets nätverk:

- Inkludera **alla platser**
- Exkludera **alla betrodda IP-adresser**


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Vad du gör om du är utelåst från Azure AD-administrationsportalen?

Om du är utelåst från Azure AD-portalen på grund av en felaktig inställning i en princip för villkorlig åtkomst:

- Kontrollera är att det finns andra administratörer i organisationen som inte är blockerad ännu. En administratör med åtkomst till Azure-portalen kan inaktivera den princip som påverkar din inloggning. 

- Om ingen av administratörer i din organisation kan uppdatera principen, måste du skicka en supportförfrågan. Microsoft-supporten kan granska och uppdatera principer för villkorlig åtkomst som förhindrar åtkomst.


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Vad händer om du har principer i den klassiska Azure-portalen och Azure portal som konfigurerats?  

Båda principerna tillämpas av Azure Active Directory och användaren får åtkomst endast när alla krav är uppfyllda.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Vad händer om du har principer i Intune Silverlight-portalen och Azure-portalen?

Båda principerna tillämpas av Azure Active Directory och användaren får åtkomst endast när alla krav är uppfyllda.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Vad händer om jag har flera principer för samma användare konfigurerade?  

För varje inloggning, Azure Active Directory utvärderar alla principer och säkerställer att alla krav är uppfyllda innan du beviljas åtkomst till användaren. Blockera åtkomst trumps alla andra konfigurationsinställningar. 


### <a name="does-conditional-access-work-with-exchange-activesync"></a>Fungerar villkorlig åtkomst med Exchange ActiveSync?

Ja, du kan använda Exchange ActiveSync i en princip för villkorlig åtkomst.

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Hur ska jag konfigurera villkorlig åtkomst med Office 365-appar?

Eftersom Office 365-appar är sammankopplade, rekommenderar vi att du tilldelar ofta tillsammans appar när du skapar principer.

Vanliga sammankopplade program inkluderar Microsoft Flow, Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, Office 365 SharePoint Online och Office 365 Yammer.

Det är viktigt för principer som kräver att användarinteraktioner, som multifaktorautentisering, när åtkomst styrs i början av en session eller uppgift. Om du inte kan användarna inte att slutföra vissa uppgifter inom en app. Till exempel om du behöver multifaktorautentisering på ohanterade enheter att få åtkomst till SharePoint, men inte till e-post kan användare som arbetar i sin e-post inte att bifoga filer i SharePoint i ett meddelande. Mer information finns i artikeln [vad är tjänstens beroenden i Azure Active Directory villkorlig åtkomst?](service-dependencies.md).





## <a name="what-you-should-avoid-doing"></a>Vad du bör undvika att göra

Ramverk för villkorlig åtkomst ger dig en utmärkt konfigurationsflexibilitet. Stor flexibilitet innebär dock även att bör du noga igenom varje princip för konfiguration innan de släpps för att undvika oönskade resultat. I det här sammanhanget, bör du vara särskilt uppmärksam på tilldelningar som påverkar fullständiga **alla användare / grupper / molnappar**.

I din miljö, bör du undvika följande konfigurationer:


**För alla användare, alla molnappar:**

- **Blockera åtkomst** – den här konfigurationen blockerar hela organisationen, vilket definitivt inte är en bra idé.

- **Kräver en kompatibel enhet** – för användare som inte har registrerat sina enheter ännu, den här principen blockerar all åtkomst, inklusive åtkomst till Intune-portalen. Om du är administratör saknar en registrerad enhet blockerar du från att få tillbaka till Azure-portalen för att ändra principen i den här principen.

- **Kräva domänanslutning** – den här principen blockera åtkomst också finns risken för att blockera åtkomsten för alla användare i din organisation om du inte har en domänansluten enhet än.


**För alla användare, alla molnappar, alla enhetsplattformar:**

- **Blockera åtkomst** – den här konfigurationen blockerar hela organisationen, vilket definitivt inte är en bra idé.


## <a name="how-should-you-deploy-a-new-policy"></a>Hur ska du distribuera en ny princip?

Som ett första steg bör du utvärdera din princip med hjälp av den [konsekvensverktyg](what-if-tool.md).

När nya principer är redo för din miljö kan du distribuera dem i olika faser:

1. Tillämpa en princip till en liten uppsättning användare och kontrollera att den fungerar som förväntat. 

2.  När du expanderar en princip för att inkludera fler användare. Fortsätta att undanta alla administratörer från principen så att de fortfarande har åtkomst och uppdatera en princip om en ändring krävs.

3. Tillämpa en princip för alla användare bara vid behov. 

Skapa ett användarkonto som är ett bra tips är:

- Dedikerad för administration av principer 
- Undantas från alla dina principer


## <a name="policy-migration"></a>Migrering av princip

Överväg att migrera de principer som du inte har skapat i Azure-portalen eftersom:

- Du kan nu hantera scenarier som du inte kan hantera innan.

- Du kan minska antalet principer som du behöver hantera genom att konsolidera dem.   

- Du kan hantera alla principer för villkorlig åtkomst på en central plats.

- Den klassiska Azure-portalen har tagits bort.   


Mer information finns i [migrera klassiska principer i Azure-portalen](policy-migration.md).


## <a name="next-steps"></a>Nästa steg

Om du vill veta:

- Hur du konfigurerar principer för villkorlig åtkomst finns i [kräver MFA för specifika appar med villkorlig åtkomst i Azure Active Directory](app-based-mfa.md).
- Hur du planerar principer för villkorlig åtkomst finns i [hur du planerar distributionen av villkorlig åtkomst i Azure Active Directory](plan-conditional-access.md).
