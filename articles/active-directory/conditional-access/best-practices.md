---
title: Metodtips för villkorlig åtkomst i Azure Active Directory | Microsoft-dokument
description: Läs mer om saker du bör veta och vad det är du bör undvika när du konfigurerar principer för villkorlig åtkomst.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4560a514ddb9949c8cc07864b2319a5878b245e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295359"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Metodtips för villkorlig åtkomst i Azure Active Directory

Med [Azure Active Directory (Azure AD) Villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md)kan du styra hur behöriga användare kommer åt dina molnappar. Den här artikeln innehåller information om:

- Saker du bör veta 
- Vad det är du bör undvika när du konfigurerar principer för villkorlig åtkomst. 

Den här artikeln förutsätter att du är bekant med begreppen och terminologin som beskrivs i [Vad är villkorlig åtkomst i Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)

## <a name="whats-required-to-make-a-policy-work"></a>Vad krävs för att få en policy att fungera?

När du skapar en ny princip markeras inga användare, grupper, appar eller åtkomstkontroller.

![Molnappar](./media/best-practices/02.png)

Om du vill att din princip ska fungera måste du konfigurera:

| Vad           | Hur                                  | Varför |
| :--            | :--                                  | :-- |
| **Molnappar** |Välj en eller flera appar.  | Målet med en princip för villkorlig åtkomst är att du ska kunna styra hur behöriga användare kan komma åt molnappar.|
| **Användare och grupper** | Välj minst en användare eller grupp som har behörighet att komma åt dina valda molnappar. | En princip för villkorlig åtkomst som inte har några tilldelade användare och grupper utlöses aldrig. |
| **Åtkomstkontroller** | Välj minst en åtkomstkontroll. | Om dina villkor är uppfyllda måste din principbehandlare veta vad du ska göra. |

## <a name="what-you-should-know"></a>Det här bör du känna till

### <a name="how-are-conditional-access-policies-applied"></a>Hur tillämpas principer för villkorlig åtkomst?

Mer än en princip för villkorlig åtkomst kan gälla när du öppnar en molnapp. I det här fallet måste alla principer som gäller vara uppfyllda. Om en princip till exempel kräver MFA (Multifaktor authentication) och en annan kräver en kompatibel enhet måste du fylla i MFA och använda en kompatibel enhet. 

Alla principer tillämpas i två faser:

- Fas 1: 
   - Detaljsamling: Samla in information för att identifiera principer som redan är uppfyllda.
   - Under den här fasen kan användarna se en certifikatfråga om enhetsefterlevnad är en del av dina principer för villkorlig åtkomst. Den här uppmaningen kan uppstå för webbläsarappar när enhetens operativsystem inte är Windows 10.
   - Fas 1 i principutvärderingen sker för alla aktiverade principer och principer i [endast rapportläge](concept-conditional-access-report-only.md).
- Fas 2:
   - Verkställighet: Med hänsyn till de uppgifter som samlats in i fas 1, begära att användaren uppfyller eventuella ytterligare krav som inte har uppfyllts.
   - Tillämpa resultat på sessionen. 
   - Fas 2 av principutvärderingen sker för alla aktiverade principer.

### <a name="how-are-assignments-evaluated"></a>Hur utvärderas tilldelningar?

Alla tilldelningar är logiskt **ANDed**. Om du har konfigurerat mer än en tilldelning måste alla tilldelningar vara uppfyllda för att utlösa en princip.  

Om du behöver konfigurera ett platsvillkor som gäller för alla anslutningar som görs utanför organisationens nätverk:

- Inkludera **alla platser**
- Exkludera **alla betrodda IPs**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Vad gör du om du är utelåst från Azure AD-administratörsportalen?

Om du är utelåst från Azure AD-portalen på grund av en felaktig inställning i en princip för villkorlig åtkomst:

- Kontrollera att det finns andra administratörer i organisationen som inte är blockerade ännu. En administratör med åtkomst till Azure-portalen kan inaktivera principen som påverkar din inloggning. 
- Om ingen av administratörerna i organisationen kan uppdatera principen måste du skicka en supportbegäran. Microsoft-support kan granska och uppdatera principer för villkorlig åtkomst som förhindrar åtkomst.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Vad händer om du har principer i Den klassiska Azure-portalen och Azure-portalen konfigurerade?  

Båda principerna tillämpas av Azure Active Directory och användaren får endast åtkomst när alla krav är uppfyllda.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Vad händer om du har principer i Intune Silverlight-portalen och Azure-portalen?

Båda principerna tillämpas av Azure Active Directory och användaren får endast åtkomst när alla krav är uppfyllda.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Vad händer om jag har flera principer för samma användare konfigurerade?  

För varje inloggning utvärderar Azure Active Directory alla principer och säkerställer att alla krav uppfylls innan de beviljas åtkomst till användaren. Blockåtkomst övertrumfar alla andra konfigurationsinställningar. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>Fungerar villkorlig åtkomst med Exchange ActiveSync?

Ja, du kan använda Exchange ActiveSync i en princip för villkorlig åtkomst.

Vissa molnappar som SharePoint Online och Exchange Online stöder också äldre autentiseringsprotokoll. När en klientapp kan använda ett äldre autentiseringsprotokoll för att komma åt en molnapp kan Azure AD inte tillämpa en princip för villkorlig åtkomst i det här åtkomstförsöket. Om du vill förhindra att en klientapp kringgår efterlevnaden av principer bör du kontrollera om det är möjligt att bara aktivera modern autentisering på de berörda molnapparna.

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Hur ska du konfigurera villkorlig åtkomst med Office 365-appar?

Eftersom Office 365-appar är sammankopplade rekommenderar vi att du tilldelar vanliga appar tillsammans när du skapar principer.

Vanliga sammankopplade program är Microsoft Flow, Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, Office 365 SharePoint Online och Office 365 Yammer.

Det är viktigt för principer som kräver användarinteraktioner, till exempel multifaktorautentisering, när åtkomsten kontrolleras i början av en session eller uppgift. Om du inte gör det kan användarna inte utföra vissa uppgifter i en app. Om du till exempel behöver multifaktorautentisering på ohanterade enheter för att komma åt SharePoint men inte via e-post, kan användare som arbetar i sin e-post inte bifoga SharePoint-filer i ett meddelande. Mer information finns i artikeln, [Vad är tjänstberoenden i Azure Active Directory Villkorlig åtkomst?](service-dependencies.md).

## <a name="what-you-should-avoid-doing"></a>Vad du bör undvika att göra

Ramverket för villkorlig åtkomst ger dig en stor konfigurationsflexibilitet. Men stor flexibilitet innebär också att du noggrant bör granska varje konfigurationsprincip innan du släpper den för att undvika oönskade resultat. I det här sammanhanget bör du ägna särskild uppmärksamhet åt tilldelningar som påverkar kompletta uppsättningar som **alla användare / grupper / molnappar**.

I din miljö bör du undvika följande konfigurationer:

**För alla användare, alla molnappar:**

- **Blockera åtkomst** - Den här konfigurationen blockerar hela organisationen, vilket definitivt inte är en bra idé.
- **Kräv kompatibel enhet** – För användare som inte har registrerat sina enheter ännu blockerar den här principen all åtkomst, inklusive åtkomst till Intune-portalen. Om du är administratör utan en registrerad enhet blockerar den här principen dig från att komma tillbaka till Azure-portalen för att ändra principen.
- **Kräv domänanslutning** – Den här principblockåtkomsten har också potential att blockera åtkomst för alla användare i organisationen om du inte har en domänansluten enhet ännu.
- **Kräv appskyddsprincip** – Den här principblockåtkomsten har också potential att blockera åtkomst för alla användare i organisationen om du inte har en Intune-princip. Om du är administratör utan ett klientprogram som har en Intune-appskyddsprincip blockerar den här principen dig från att komma tillbaka till portaler som Intune och Azure.

**För alla användare, alla molnappar, alla enhetsplattformar:**

- **Blockera åtkomst** - Den här konfigurationen blockerar hela organisationen, vilket definitivt inte är en bra idé.

## <a name="how-should-you-deploy-a-new-policy"></a>Hur ska du distribuera en ny princip?

Som ett första steg bör du utvärdera din princip med hjälp av [vad händer om-verktyget](what-if-tool.md).

När nya principer är klara för din miljö kan du distribuera dem i faser:

1. Tillämpa en princip på en liten uppsättning användare och kontrollera att den fungerar som förväntat. 
1. När du expanderar en princip för att inkludera fler användare. Fortsätt att utesluta alla administratörer från principen för att säkerställa att de fortfarande har åtkomst och kan uppdatera en princip om en ändring krävs.
1. Använd en princip endast för alla användare om det behövs. 

På bästa sätt kan du skapa ett användarkonto som är:

- Tillägnad politisk administration 
- Utesluten från alla dina policyer

## <a name="policy-migration"></a>Politisk migrering

Överväg att migrera de principer som du inte har skapat i Azure-portalen eftersom:

- Du kan nu ta itu med scenarier som du inte kunde hantera tidigare.
- Du kan minska antalet principer som du måste hantera genom att konsolidera dem.   
- Du kan hantera alla principer för villkorlig åtkomst på en central plats.
- Den klassiska Azure-portalen har dragits tillbaka.   

Mer information finns i [Migrera klassiska principer i Azure Portal](policy-migration.md).

## <a name="next-steps"></a>Nästa steg

Om du vill veta:

- Konfigurera en princip för villkorlig åtkomst finns i [Kräv MFA för specifika appar med Azure Active Directory Villkorlig åtkomst](app-based-mfa.md).
- Planera principer för villkorlig åtkomst i hur [du planerar distributionen för villkorlig åtkomst i Azure Active Directory](plan-conditional-access.md).
