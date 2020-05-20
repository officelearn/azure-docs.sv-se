---
title: Översikt över utgivarens granskning – Microsoft Identity Platform | Azure
description: Innehåller en översikt över utgivarens verifierings program (för hands version) för Microsoft Identity Platform. Visar en lista över fördelar, program krav och vanliga frågor och svar. När ett program har marker ATS som utgivare verifierat, innebär det att utgivaren har verifierat sin identitet med hjälp av ett Microsoft Partner Network konto som har slutfört verifierings processen och associerat det här MPN-kontot med sin program registrering.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 73a96f295d5dfa74130927e5096e9278a0e348e8
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682438"
---
# <a name="publisher-verification-preview"></a>Utgivarens verifiering (för hands version)

Utgivarens verifiering (för hands version) hjälper administratörer och slutanvändare att förstå äktheten hos programutvecklare som integreras med Microsoft Identity Platform. När ett program har marker ATS som utgivare verifierat, innebär det att utgivaren har verifierat sin identitet med hjälp av ett [Microsoft Partner Network](https://partner.microsoft.com/membership) konto som har slutfört [verifierings](/partner-center/verification-responses) processen och ASSOCIERAt det här MPN-kontot med sin program registrering. 

Ett blått "verifierat"-märke visas i frågan om godkännande i Azure AD och andra skärmar: ![ medgivande-prompt](./media/publisher-verification-overview/consent-prompt.png)

Den här funktionen är främst avsedd för utvecklare som skapar appar för flera klienter som utnyttjar [OAuth 2,0 och OpenID ansluter](active-directory-v2-protocols.md) till [Microsoft Identity Platform](v2-overview.md). De här apparna kan logga in användare i med OpenID Connect, eller så kan de använda OAuth 2,0 för att begära åtkomst till data med hjälp av API: er som [Microsoft Graph](https://developer.microsoft.com/graph/).

## <a name="benefits"></a>Fördelar
Utgivar verifieringen ger följande fördelar:
- **Ökad transparens och risk minskning för kunder**– den här funktionen hjälper kunder att förstå vilka appar som används i sina organisationer publiceras av utvecklare som de litar på. 

- **Förbättrat varumärke**– ett "verifierat"-märke visas på sidan Azure AD [medgivande](application-consent-experience.md), sidan företags appar och ytterligare UX-ytor som används av slutanvändare och administratörer. 

- **Smidigare företags införande**– administratörer kan konfigurera nya principer för användar medgivande och statusen för utgivarens verifiering är ett av de primära princip villkoren. 

- **Förbättrad riskbedömning**– Microsofts identifieringar för riskhanterings begär Anden kommer att inkludera utgivar verifiering som en signal. 

## <a name="requirements"></a>Krav
Det finns några krav för utgivar verifiering, varav vissa redan har slutförts av många Microsoft-partner. De är: 

-  Ett MPN-ID för ett giltigt [Microsoft Partner Network](https://partner.microsoft.com/membership) -konto som har slutfört [verifierings](/partner-center/verification-responses) processen. Det här MPN-kontot måste vara [partnerns globala konto (pga)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga) för din organisation. 

-  En Azure AD-klient med en DNS-verifierad [domän](/azure/active-directory/fundamentals/add-custom-domain). Den anpassade domänen måste matcha domänen för den e-postadress som användes vid verifieringen i föregående steg. 

-  En app som är registrerad i en Azure AD-klient, med en [Publisher-domän](howto-configure-publisher-domain.md) konfigurerad med samma domän som tidigare användes. 

-  Den användare som utför verifieringen måste ha behörighet att göra ändringar i både appens registrering i Azure AD och MPN-kontot i Partner Center. 

    -  I Azure AD måste användaren antingen vara ägare till appen eller ha en av följande [roller](/azure/active-directory/users-groups-roles/directory-assign-admin-roles): program administratör, Cloud program admin, global administratör. 

    -  Den här användaren måste ha följande [roller](/partner-center/permissions-overview)i Partner Center: MPN admin, Account admin eller global admin (detta är en delad roll som administreras i Azure AD).
    
-  Utgivaren accepterar [användnings villkoren för Microsoft Identity Platform för utvecklare](/legal/microsoft-identity-platform/terms-of-use).

Utvecklare som redan har uppfyllt dessa krav kan verifieras på några minuter. Om kraven inte har uppfyllts är hämtnings inställningen kostnads fri. 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 
Nedan visas några vanliga frågor om verifierings programmet för utgivare. Vanliga frågor och svar om kraven och processen finns i [Markera en app som utgivare verifierades](mark-app-as-publisher-verified.md).

- **Vilken information kan utgivaren __inte__ ange?**  När ett program har marker ATS som utgivar verifierar detta inte om programmet eller utgivaren har uppnått några specifika certifieringar, uppfyller bransch standarder, följer bästa praxis osv. Andra Microsoft-program tillhandahåller den här informationen, inklusive [Microsoft 365 App-certifiering](/microsoft-365-app-certification/overview).

- **Hur mycket kostar det? Kräver det någon licens?** Microsoft debiterar inte utvecklare för utgivar verifiering och behöver inte någon speciell licens. 

- **Hur relaterar detta till Microsoft 365 utgivarens attestering? Vad gäller för Microsoft 365 App-certifiering?** Detta är kompletterande program som utvecklare kan använda för att skapa betrodda appar som kan antas av kunder. Utgivar verifiering är det första steget i den här processen och bör slutföras av alla utvecklare som skapar appar som uppfyller ovanstående kriterier. 

  Utvecklare som också integrerar med Microsoft 365 kan få ytterligare förmåner från dessa program. Mer information finns i [Microsoft 365 utgivarens attestering](/microsoft-365-app-certification/docs/attestation) och [Microsoft 365 certifiering av appar](/microsoft-365-app-certification/docs/certification). 

- **Är det samma sak som Azure AD-programgalleriet?** Ingen-utgivar verifiering är ett komplett men separat program till [Azure Active Directory program galleriet](/azure/active-directory/azuread-dev/howto-app-gallery-listing). Utvecklare som uppfyller ovanstående kriterier bör slutföra utgivarens verifierings process oberoende av deltagande i det programmet. 

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [markerar en app när utgivaren har verifierats](mark-app-as-publisher-verified.md).
* [Felsöka](troubleshoot-publisher-verification.md) Publisher-verifiering.