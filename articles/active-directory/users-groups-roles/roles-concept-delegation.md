---
title: Förstå delegering av administratörsroller - Azure Active Directory | Microsoft-dokument
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
ms.openlocfilehash: 6fa3c6bf39dbef601fe64e125999f519f725f2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67083775"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegera administration i Azure Active Directory

Med organisatorisk tillväxt kommer komplexitet. Ett vanligt svar är att minska en del av arbetsbelastningen för åtkomsthantering med Azure Active Directory (AD) administratörsroller. Du kan tilldela användarna minsta möjliga behörighet att komma åt sina appar och utföra sina uppgifter. Även om du inte tilldelar rollen Global administratör till alla programägare placerar du programhanteringsansvar på befintliga globala administratörer. Det finns många orsaker till att en organisation rör sig mot en mer decentraliserad administration. Den här artikeln kan hjälpa dig att planera för delegering i organisationen.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Centraliserade kontra delegerade behörigheter

När en organisation växer kan det vara svårt att hålla reda på vilka användare som har specifika administratörsroller. Om en anställd har administratörsrättigheter som de inte borde kan din organisation vara mer mottaglig för säkerhetsöverträdelser. I allmänhet beror hur många administratörer du stöder och hur detaljerade deras behörigheter är på distributionens storlek och komplexitet.

* I små eller proof-of-concept-distributioner gör en eller några administratörer allt. Det finns ingen delegation. Skapa i så fall varje administratör med rollen Global administratör.
* I större distributioner med fler datorer, program och skrivbord behövs mer delegering. Flera administratörer kan ha mer specifika funktionella ansvarsområden (roller). Vissa kan till exempel vara privilegierade identitetsadministratörer och andra kan vara programadministratörer. Dessutom kan en administratör endast hantera vissa grupper av objekt, till exempel enheter.
* Ännu större distributioner kan kräva ännu mer detaljerade behörigheter, plus eventuellt administratörer med okonventionella eller hybridroller.

I Azure AD-portalen kan du [visa alla medlemmar i valfri roll](directory-manage-roles-portal.md), vilket kan hjälpa dig att snabbt kontrollera distributionen och delegera behörigheter.

Om du är intresserad av att delegera åtkomst till Azure-resurser i stället för administrativ åtkomst i Azure AD läser [du Tilldela en rbac-roll (Role-based Access Control).](../../role-based-access-control/role-assignments-portal.md)

## <a name="delegation-planning"></a>Planering av delegering

Det är ett arbete med att utveckla en delegeringsmodell som passar dina behov. Att utveckla en delegeringsmodell är en iterativ designprocess och vi föreslår att du följer dessa steg:

* Definiera de roller du behöver
* Delegera appadministration
* Ge möjlighet att registrera ansökningar
* Delegera appägarskap
* Ta fram en säkerhetsplan
* Upprätta nödkonton
* Skydda dina administratörsroller
* Gör privilegierad höjning tillfällig

## <a name="define-roles"></a>Definiera roller

Bestäm de Active Directory-uppgifter som utförs av administratörer och hur de mappas till roller. Du kan [visa detaljerade rollbeskrivningar](directory-manage-roles-portal.md) i Azure-portalen.

Varje uppgift bör utvärderas med anledning av frekvens, betydelse och svårighet. Dessa kriterier är viktiga aspekter av uppgiftsdefinitionen eftersom de styr om ett tillstånd ska delegeras:

* Uppgifter som du gör rutinmässigt, har begränsad risk, och är triviala att slutföra är utmärkta kandidater för delegering.
* Uppgifter som du sällan gör men har stor inverkan i hela organisationen och kräver höga kompetensnivåer bör övervägas mycket noggrant innan delegering. I stället kan du [tillfälligt höja ett konto till den nödvändiga rollen](../active-directory-privileged-identity-management-configure.md) eller tilldela om uppgiften.

## <a name="delegate-app-administration"></a>Delegera appadministration

Spridningen av appar inom organisationen kan anstränga din delegeringsmodell. Om det lägger bördan för hantering av programåtkomst på den globala administratören är det troligt att modellen ökar dess omkostnader med tiden. Om du har gett personer rollen Global administratör för saker som att konfigurera företagsprogram kan du nu avlasta dem till följande mindre privilegierade roller. Detta bidrar till att förbättra din säkerhetsposition och minskar risken för olyckliga misstag. De mest privilegierade programadministratörsrollerna är:

* Rollen **Programadministratör,** som ger möjlighet att hantera alla program i katalogen, inklusive registreringar, inställningar för enkel inloggning, användar- och grupptilldelningar och licensiering, inställningar för programproxy och medgivande. Det ger inte möjlighet att hantera villkorlig åtkomst.
* Rollen **Cloud Application Administrator,** som ger alla funktioner för programadministratören, förutom att den inte ger åtkomst till inställningar för programproxy (eftersom den inte har någon lokal behörighet).

## <a name="delegate-app-registration"></a>Delegera appregistrering

Som standard kan alla användare skapa programregistreringar. Så här ger du selektivt möjlighet att skapa programregistreringar:

* Ange **användare kan registrera program** till Nej i **användarinställningar**
* Tilldela användaren till rollen Programutvecklare

Att selektivt ge möjlighet att samtycka till att tillåta en ansökan att få tillgång till data:

* Ange **att användare kan godkänna program som använder företagsdata för deras räkning** Till nej i **användarinställningar**
* Tilldela användaren till rollen Programutvecklare

När en programutvecklare skapar en ny programregistrering läggs de automatiskt till som den första ägaren.

## <a name="delegate-app-ownership"></a>Delegera appägarskap

För ännu finare åtkomstdelegering av app kan du tilldela ägarskap till enskilda företagsprogram. Detta kompletterar det befintliga stödet för tilldelning av ägare av programregistrering. Ägarskapet tilldelas per företagsapplikation i bladet Enterprise Applications. Fördelen är att ägarna endast kan hantera de företagsprogram de äger. Du kan till exempel tilldela en ägare för Salesforce-programmet och den ägaren kan hantera åtkomst till och konfiguration för Salesforce och inga andra program. Ett företagsprogram kan ha många ägare och en användare kan vara ägare till många företagsprogram. Det finns två rollerna för appens ägare:

* Rollen **Ägare av företagsprogram** ger möjlighet att hantera de företagsprogram som användaren äger, inklusive inställningar för enkel inloggning, användar- och grupptilldelningar och lägga till ytterligare ägare. Det ger inte möjlighet att hantera inställningar för programproxy eller villkorlig åtkomst.
* Rollen Ägare för **programregistrering** ger möjlighet att hantera programregistreringar för app som användaren äger, inklusive programmanifestet och lägga till ytterligare ägare.

## <a name="develop-a-security-plan"></a>Ta fram en säkerhetsplan

Azure AD innehåller en omfattande guide för planering och körning av en säkerhetsplan för dina Azure AD-administratörsroller, [skydda privilegierad åtkomst för hybrid- och molndistributioner](directory-admin-roles-secure.md).

## <a name="establish-emergency-accounts"></a>Upprätta nödkonton

Förbered konton för nödåtkomst enligt Skapa administrativa konton för [nödåtkomst](directory-emergency-access.md)om du vill behålla åtkomsten till ditt identitetshanteringsarkiv när problemet uppstår.

## <a name="secure-your-administrator-roles"></a>Skydda dina administratörsroller

Angripare som får kontroll över privilegierade konton kan göra enorma skador, så skydda dessa konton först, med hjälp av [baslinjeåtkomstprincipen](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) som är tillgänglig som standard för alla Azure AD-klienter (i offentlig förhandsversion). Principen tillämpar multifaktorautentisering på privilegierade Azure AD-konton. Följande Azure AD-roller omfattas av Azure AD-originalprincipen:

* Global administratör
* SharePoint-administratör
* Exchange-administratör
* Administratör för villkorlig åtkomst
* Säkerhetsadministratör

## <a name="elevate-privilege-temporarily"></a>Höj privilegiet tillfälligt

För de flesta dagliga aktiviteter behöver inte alla användare globala administratörsrättigheter, och alla av dem bör inte tilldelas permanent till rollen Global administratör. När användare behöver behörigheter för en global administratör bör de aktivera rolltilldelningen i Azure AD [Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) på antingen sitt eget konto eller ett alternativt administrativt konto.

## <a name="next-steps"></a>Nästa steg

En referens till Azure AD-rollbeskrivningarna finns [i Tilldela administratörsroller i Azure AD](directory-assign-admin-roles.md)
