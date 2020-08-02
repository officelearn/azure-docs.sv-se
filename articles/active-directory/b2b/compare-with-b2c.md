---
title: Jämför externa identiteter – Azure Active Directory | Microsoft Docs
description: Externa Azure AD-identiteter gör att personer utanför organisationen kan komma åt dina appar och resurser med sin egen identitet. Jämför lösningar för externa identiteter, inklusive Azure Active Directory B2B-samarbete och Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 05/19/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8eff1a6d8c729a7cea49ef1e7e5ae97d40a7f99
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87491603"
---
# <a name="compare-solutions-for-external-identities-in-azure-active-directory"></a>Jämför lösningar för externa identiteter i Azure Active Directory

Med externa identiteter i Azure AD kan du ge personer utanför organisationen åtkomst till dina appar och resurser, samtidigt som de kan logga in med vilken identitet de föredrar. Dina partner, distributörer, leverantörer, leverantörer och andra gäst användare kan "ta med sig sina egna identiteter". Oavsett om de är en del av Azure AD eller ett annat IT-hanterat system, eller om de har en ohanterad social identitet som Google eller Facebook, kan de använda sina egna autentiseringsuppgifter för att logga in. Identitets leverantören hanterar den externa användarens identitet och du kan hantera åtkomsten till dina appar med Azure AD för att skydda dina resurser. 

## <a name="external-identities-scenarios"></a>Scenarier för externa identiteter

Externa Azure AD-identiteter fokuserar mindre på användarens relation till din organisation och mer på hur en individ vill logga in på dina appar och resurser. I det här ramverket har Azure AD stöd för en mängd olika scenarier från Business-to-Business (B2B)-samarbete till utveckling av appar för kunder och konsumenter (företags-till-konsument eller B2C).

- **Dela appar med externa användare (B2B-samarbete)**. Bjud in externa användare till din egen klient organisation som "gäst"-användare som du kan tilldela behörigheter till (för auktorisering) samtidigt som de tillåter att de använder sina befintliga autentiseringsuppgifter (för autentisering). Användarna loggar in på delade resurser med en enkel Inbjudnings-och inlösnings process med sitt arbets konto, skol konto eller valfritt e-postkonto. Och nu med tillgänglighet för självbetjänings registrering av användar flöden (för hands version) kan du också tillhandahålla en inloggnings upplevelse för dina externa användare via det program som du vill dela. Du kan konfigurera användar flödes inställningarna för att styra hur användaren registrerar sig för programmet och som gör att de kan använda sitt arbets konto, skol konto eller en social identitet (t. ex. Google eller Facebook) som du vill använda.  Mer information finns i [Azure AD B2B-dokumentationen](index.yml).

- **Utveckla appar som är avsedda för andra Azure AD-klienter (en enda klient eller flera innehavare)**. När du utvecklar program för Azure AD kan du rikta in dig på användare från en enda organisation (en enda klient) eller användare från en organisation som redan har en Azure AD-klient (kallas program för flera innehavare). Dessa program för flera klient organisationer registreras en gång i din egen Azure AD, men kan sedan användas av alla Azure AD-användare från alla organisationer utan ytterligare arbete på din sida.

- **Utveckla appar med vita etiketter för konsumenter och kunder (Azure AD B2C)**. Om du är en verksamhet eller utvecklare som skapar kundappar kan du skala till konsumenter, kunder eller medborgare genom att använda en Azure AD B2C. Utvecklare kan använda Azure AD som det fullständiga identitets systemet för sina program, samtidigt som kunderna kan logga in med en identitet som de redan har upprättat (t. ex. Facebook eller Gmail). Med Azure AD B2C kan du helt anpassa och styra hur kunderna registrerar sig, loggar in och hanterar sina profiler när de använder dina program. Mer information finns i Azure AD B2C- [dokumentationen](https://docs.microsoft.com/azure/active-directory-b2c/).

Tabellen nedan innehåller en detaljerad jämförelse av de olika scenarier som du kan aktivera med externa Azure AD-identiteter.

| Program för flera innehavare  | Samarbete mellan externa användare (B2B) | Appar för konsumenter eller kunder (B2C)  |
| ---- | --- | --- |
| Primärt scenario: Enterprise-program vara som en tjänst (SaaS) | Primärt scenario: samarbete med Microsoft-program (Microsoft 365, teams,...) eller din egen samarbets program vara.  | Primärt scenario: transaktionella program som använder anpassade program som har utvecklats.   |
| Avsett för: organisationer som vill ge program vara till många företags kunder.    | Avsedd för: organisationer som vill kunna autentisera användare från en partnerorganisation oavsett identitetsprovider.    | Avsett för: bjuda in kunder i dina mobil-och webbappar, oavsett om de är individer, institutionella eller organisatoriska kunder till en Azure AD-katalog separat från din organisations katalog. |
| Identiteter som stöds: anställda med Azure AD-konton. | Identiteter som stöds: anställda med arbets- eller skolkonton, partners med arbets- eller skolkonton eller valfria e-postadresser. Stödjer snart direkt federation.      | Identiteter som stöds: konsumentanvändare med lokala programkonton (e-postadresser eller användarnamn) eller någon social identitet med direkt federation som stöds.       |
| Externa användare hanteras i sin egen katalog, isolerade från katalogen där programmet registrerades.    | Externa användare hanteras i samma katalog som anställda, men kommenteras särskilt. De kan hanteras på samma sätt som anställda, de kan läggas till i samma grupper och så vidare.    | Externa användare hanteras i programkatalogen. De hanteras separat från organisationens anställde och partner katalog (om det finns några).  |
| Enkel inloggning: SSO till alla Azure AD-anslutna appar stöds.          | Enkel inloggning: SSO till alla Azure AD-anslutna appar stöds. Du kan till exempel ge åtkomst till Microsoft 365 eller lokala appar och till andra SaaS-appar som Salesforce eller Workday.    | Enkel inloggning: SSO till kundägda appar inom Azure AD B2C-klienter stöds. SSO till Microsoft 365 eller till andra Microsoft SaaS-appar stöds inte.    |
| Kund livs cykel: hanteras av användarens hem organisation.      | Livscykel för partner: hanteras av värden/den inbjudande organisationen.    | Livscykel för kunden: självbetjäning eller hanteras av programmet.      |
| Säkerhets princip och efterlevnad: hanteras av värd/bjuda in organisation (till exempel med [principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).           | Säkerhets princip och efterlevnad: hanteras av värd/bjuda in organisation (till exempel med [principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)). | Säkerhetsprincip och efterlevnad: hanteras av programmet.        |
| Varumärke: värd/Bjud in organisationens varumärke används.   | Varumärke: värd/Bjud in organisationens varumärke används.    | Anpassning: hanteras av programmet. Brukar vanligtvis vara produktanpassad med organisationens varumärke intonat i bakgrunden.   |
| Mer information: [hantera identiteter i program med flera klienter](https://docs.microsoft.com/azure/architecture/multitenant-identity/), [instruktions guide](https://docs.microsoft.com/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant) | Mer information: [Blogginlägg](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Dokumentation](what-is-b2b.md)                   | Mer information: [Produktsida](https://azure.microsoft.com/services/active-directory-b2c/), [Dokumentation](https://docs.microsoft.com/azure/active-directory-b2c/)       |

Skydda och hantera kunder och partners bortom organisationens gränser med externa Azure AD-identiteter.

### <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Om Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview)
