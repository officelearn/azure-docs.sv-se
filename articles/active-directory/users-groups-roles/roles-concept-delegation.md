---
title: Förstå rolldelegering admin - Azure Active Directory | Microsoft Docs
description: Delegeringsmodeller, exempel och rollsäkerhet i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0cb6e2b1df062c3d056bd9a5aa0c1ff89f6636a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60469120"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegera administration i Azure Active Directory

Kommer komplexiteten med organisationens tillväxt. Ett vanligt svar är att minska arbetsbelastningen för åtkomsthantering med administratörsroller i Azure Active Directory (AD). Du kan tilldela minsta möjliga behörighet för användare att komma åt sina appar och utföra sina uppgifter. Även om du inte tilldelar rollen som Global administratör till varje programmets ägare, placerar du application management ansvarsområden på befintliga globala administratörer. Det finns flera anledningar till att en organisation steg mot ett mer decentraliserad administration. Den här artikeln kan hjälpa dig att planera för delegering i din organisation.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Centraliserad jämfört med delegerade behörigheter

När organisationen växer, kan det vara svårt att hålla reda på vilka användare som har specifika administrativa roller. Om en medarbetare har administratörsbehörighet som de får inte kan kan din organisation vara svårare att säkerhetsintrång. I allmänhet hur många administratörer du stöder och hur detaljerade har beror på storleken och komplexiteten för din distribution.

* I små eller proof of concept-distributioner göra en eller några få administratörer allt; Det finns ingen delegering. I det här fallet skapa varje administratör med rollen Global administratör.
* I större distributioner med flera datorer, program och skrivbord krävs mer delegering. Flera administratörer kan ha mer specifika funktionella ansvarsområden (roller). Till exempel vissa kanske är privilegierad Identitetsadministratörer och medan andra kan vara administratörer. Dessutom kan en administratör hantera endast vissa grupper med objekt, till exempel enheter.
* Ännu större distributioner kan kräva ännu mer detaljerade behörigheter plus eventuellt administratörer med ovanliga eller hybrid roller.

I Azure AD-portalen kan du [visa alla medlemmar i någon roll](directory-manage-roles-portal.md), som hjälper dig att snabbt kontrollera dina distributions- och delegera behörigheter.

Om du är intresserad av att delegera åtkomst till Azure-resurser i stället för administrativ åtkomst i Azure AD, se [tilldela en roll för rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Planera för delegering

Det är att utveckla en delegeringsmodell som passar dina behov. Utveckla en delegeringsmodell är en process för upprepad konstruktion och vi rekommenderar att du följer dessa steg:

* Definiera de roller som du behöver
* Delegera administration av app
* Ger möjlighet att registrera program
* Delegera app ägarskap
* Utveckla en säkerhetsplan för
* Upprätta vid akutfall konton
* Skydda din administratörsroller
* Kontrollera Privilegierade höjning tillfälliga

## <a name="define-roles"></a>Definiera roller

Fastställa Active Directory-aktiviteter som utförs av administratörer och hur de mappas till roller. Du kan [visa detaljerad rollbeskrivningar](directory-manage-roles-portal.md) i Azure-portalen.

Varje aktivitet bör utvärderas för frekvens, prioritet och problem. Dessa kriterier är viktiga aspekter av aktivitetsdefinition eftersom de styr om en behörighet som ska delegeras:

* Uppgifter att göra regelbundet, har begränsad risk och är helt enkelt att fullständiga är perfekta kandidater för delegering.
* Uppgifter som att du gör sällan men har kraftigt påverka hela organisationen och kräver hög erfarenhetsnivåer bör övervägas mycket noggrant innan du delegera. I stället kan du [tillfälligt höja ett konto till de nödvändiga rolltjänsterna](../active-directory-privileged-identity-management-configure.md) eller omtilldela uppgiften.

## <a name="delegate-app-administration"></a>Delegera administration av app

Ökningen av appar i din organisation kan överbelasta dina delegeringsmodell. Om belastningen för hantering av programåtkomst placeras på den globala administratören, är det troligt att modellen ökar overhead med tiden. Om du har beviljat personer rollen Global administratör för till exempel konfigurera företagsprogram, kan du nu omfördela dem till följande mindre privilegierade roller. Detta hjälper oss för att förbättra din säkerhetsposition och minskar risken för olycklig misstag. Administratörsroller högprivilegierade de flesta program är:

* Den **programadministratör** vilket ger möjlighet att hantera alla program i katalogen, inklusive registreringar, inställningar för enkel inloggning, användaren och grupptilldelningar och licensiering, Application Proxy-inställningar och medgivande. Det ger inte möjlighet att hantera villkorlig åtkomst.
* Den **Molnprogramadministratör** vilket ger alla funktioner av programadministratör förutom det inte ger åtkomst till Application Proxy-inställningar (eftersom det har inte behörighet för lokalt).

## <a name="delegate-app-registration"></a>Delegera appregistrering

Alla användare kan skapa programregistreringar som standard. Så här selektivt ger möjlighet att skapa programregistreringar:

* Ange **användare kan registrera program** på Nej i **användarinställningar**
* Tilldela användaren till rollen programutvecklare

Så här selektivt ger möjlighet att godkänna att programmet kan komma åt data:

* Ange **användare kan samtycka till program som får åtkomst till företagsdata å deras vägnar** till Nej i **användarinställningar**
* Tilldela användaren till rollen programutvecklare

När en programutvecklare skapar en ny programregistrering, läggs de automatiskt som första ägare.

## <a name="delegate-app-ownership"></a>Delegera app ägarskap

För ännu mer detaljerad appen åtkomst delegering kan du tilldela ägarskap till enskilda företagsprogram. Detta kompletterar befintliga stöd för att tilldela programägare för registrering. Ägarskap tilldelas regelbundet per enterprise program i bladet företagsprogram. Fördelen är ägare kan hantera endast för företagsprogram som de äger. Exempelvis kan du tilldela en ägare till Salesforce-programmet och den aktuella ägaren kan hantera åtkomst till och konfiguration för Salesforce och inga andra program. Ett enterprise-program kan ha många ägare och en användare kan vara ägare för många företagsprogram. Det finns två ägare roller:

* Den **Enterprise programmets ägare** rollen ger dig möjlighet att hantera den ' företagsprogram som användaren äger, inklusive inställningar för enkel inloggning, användaren och grupptilldelningar och lägga till ytterligare ägare. Det ger inte möjlighet att hantera inställningar för Application Proxy eller villkorlig åtkomst.
* Den **registrering Programägaren** rollen ger dig möjlighet att hantera programregistreringar för appen som användaren äger, inklusive applikationsmanifestet och lägga till ytterligare ägare.

## <a name="develop-a-security-plan"></a>Utveckla en säkerhetsplan för

Azure AD tillhandahåller en omfattande guide till planering och köra en skyddsplan på din Azure AD-administratörsroller [referensmaterialet för att skydda privilegierad åtkomst för hybrid- och distributioner](directory-admin-roles-secure.md).

## <a name="establish-emergency-accounts"></a>Upprätta vid akutfall konton

Om du vill ha tillgång till hanteringsarkivet för din identitet när problem uppstår, förbereda konton för åtkomst vid akutfall enligt [skapa nödfall-åtkomst till administratörskonton](directory-emergency-access.md).

## <a name="secure-your-administrator-roles"></a>Skydda din administratörsroller

Angripare som får kontroll över Privilegierade konton kan göra enorma skada, så skydda dessa konton först med hjälp av den [baslinje åtkomstprincip](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) som är tillgängligt som standard för alla Azure AD-klienter (i allmänt tillgänglig förhandsversion). Principen framtvingar multifaktorautentisering på Privilegierade konton för Azure AD. Följande roller i Azure AD omfattas av Azure AD-baslinjeprincip:

* Global administratör
* SharePoint-administratör
* Exchange-administratör
* Administratör för villkorsstyrd åtkomst
* Säkerhetsadministratör

## <a name="elevate-privilege-temporarily"></a>Utöka privilegier tillfälligt

Alla användare behöver behörighet som global administratör för de flesta dagliga aktiviteter, och inte alla permanent ska tilldelas till rollen som Global administratör. När användare behöver behörigheterna för en Global administratör, bör de aktivera rolltilldelning i Azure AD [Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) på sitt eget konto eller ett alternativt administrativt konto.

## <a name="next-steps"></a>Nästa steg

En referens till Azure AD-rollbeskrivningar finns [Tilldela administratörsroller i Azure AD](directory-assign-admin-roles.md)
