---
title: Felsökning och vanliga frågor och svar om administrativa enheter – Azure Active Directory | Microsoft-dokument
description: Undersök administrativa enheter för att bevilja behörigheter med begränsat scope i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 022658306d6e4d69174cc616d230cfe4892f1204
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684859"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Administrativa Azure AD-enheter: Felsökning och vanliga frågor och svar

För mer detaljerad administrativ kontroll i Azure Active Directory (Azure AD) kan du tilldela användare till en Azure AD-roll med ett scope som är begränsat till en eller flera administrativa enheter (AUs). Exempel på PowerShell-skript för vanliga uppgifter finns i [Arbeta med administrativa enheter](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Varför kan jag inte skapa en administrativ enhet?**

**A.** Endast en *global administratör* eller *privilegierad rolladministratör* kan skapa en administrativ enhet i Azure AD. Kontrollera att användaren som försöker skapa den administrativa enheten tilldelas rollen *global administratör* eller administratör *för privilegierad roll.*

**F: Jag har lagt till en grupp i den administrativa enheten. Varför dyker inte gruppmedlemmarna upp där?**

**A.** När du lägger till en grupp i den administrativa enheten, resulterar det inte i att alla gruppens medlemmar läggs till i den. Användare måste tilldelas direkt till den administrativa enheten.

**F: Jag har precis lagt till (eller tagit bort) en medlem av den administrativa enheten. Varför visas inte medlemmen (eller dyker fortfarande upp) i användargränssnittet?**

**A.** Ibland kan det ta några minuter att bearbeta eller ta bort en eller flera medlemmar av den administrativa enheten för att återspegla på sidan **Administrativa enheter.** Du kan också gå direkt till den associerade resursens egenskaper och se om åtgärden har slutförts. Mer information om användare och grupper i RU finns i [Lista administrativa enheter för en användare](roles-admin-units-add-manage-users.md) och Lista administrativa enheter för en [grupp](roles-admin-units-add-manage-groups.md).

**F: Jag är en delegerad lösenordsadministratör på en administrativ enhet. Varför kan jag inte återställa en viss användares lösenord?**

**A.** Som administratör för en administrativ enhet kan du bara återställa lösenord för användare som har tilldelats din administrativa enhet. Kontrollera att den användare vars lösenordsåterställning inte hör till den administrativa enhet som du har tilldelats. Om användaren tillhör samma administrativa enhet men du fortfarande inte kan återställa lösenordet kontrollerar du de roller som tilldelats användaren. 

För att förhindra en behörighetshöjning kan en administratör för begränsad administrationsenhet inte återställa lösenordet för en användare som har tilldelats en roll med ett organisationsomfattande scope.

**F: Varför behövs administrativa enheter? Kan vi inte ha använt säkerhetsgrupper som ett sätt att definiera ett scope?**

**A.** Säkerhetsgrupper har en befintlig syftes- och auktoriseringsmodell. En *användaradministratör*, till exempel, kan hantera medlemskap i alla säkerhetsgrupper i Azure AD-organisationen. Rollen kan använda grupper för att hantera åtkomst till program som Salesforce. En *användaradministratör* bör inte kunna hantera själva delegeringsmodellen, vilket skulle bli resultatet om säkerhetsgrupper utvidgades till att stödja scenarier för resursgruppering. Administrativa enheter, till exempel organisationsenheter i Active Directory i Windows Server, är avsedda att tillhandahålla ett sätt att begränsa administrationen av ett brett spektrum av katalogobjekt. Säkerhetsgrupper själva kan vara medlemmar i resursomfattningar. Om du använder säkerhetsgrupper för att definiera uppsättningen säkerhetsgrupper som en administratör kan hantera kan det bli förvirrande att använda säkerhetsgrupper för att definiera den uppsättning säkerhetsgrupper som en administratör kan hantera.

**F: Vad innebär det att lägga till en grupp i en administrativ enhet?**

**A.** Om du lägger till en grupp i en administrativ enhet hamnar själva gruppen i hanteringsomfånget för alla *användaradministratörer* som också är begränsade till den administrativa enheten. Användaradministratörer för den administrativa enheten kan hantera namn och medlemskap för själva gruppen. Användaradministratören beviljas inte *behörigheten för* den administrativa enheten att hantera användarna av gruppen (till exempel för att återställa deras lösenord). För att *ge användaradministratören* möjlighet att hantera användare måste användarna vara direkta medlemmar i den administrativa enheten.

**F: Kan en resurs (användare eller grupp) vara medlem i mer än en administrativ enhet?**

**A.** Ja, en resurs kan vara medlem i mer än en administrativ enhet. Resursen kan hanteras av alla organisationsomfattande och administrativa enhetsbeskådeadministratörer som har behörighet över resursen.

**F: Är administrativa enheter tillgängliga i B2C-organisationer?**

**A.** Nej, administrativa enheter är inte tillgängliga för B2C-organisationer.

**F: Stöds kapslade administrativa enheter?**

**A.** Nej, kapslade administrativa enheter stöds inte.

**F: Stöds administrativa enheter i PowerShell och Graph API?**

**S:** Ja. Du hittar stöd för administrativa enheter i [PowerShell cmdlet-dokumentation](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) och [exempelskript](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview). 

Hitta stöd för [resurstypen administrationUnit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit) i Microsoft Graph.

## <a name="next-steps"></a>Nästa steg

- [Begränsa utrymme för roller med hjälp av administrativa enheter](directory-administrative-units.md)
- [Hantera administrativa enheter](roles-admin-units-manage.md)
