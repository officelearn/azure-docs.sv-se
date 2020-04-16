---
title: Felsöka administrativa enheter och vanliga frågor – Azure Active Directory | Microsoft-dokument
description: Undersöka administrativa enheter för delegering av behörigheter med begränsat scope i Azure Active Directory
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
ms.openlocfilehash: 5c9770b60edad6f956ae99c91b94a232cc48bbbd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428152"
---
# <a name="troubleshooting-and-faq-for-administrative-units-in-azure-active-directory"></a>Felsökning och vanliga frågor och svar om administrativa enheter i Azure Active Directory

För mer detaljerad administrativ kontroll i Azure Active Directory (Azure AD) kan du tilldela användare till en Azure AD-roll med ett scope som är begränsat till en eller flera administrativa enheter (AUs). Du hittar exempel på PowerShell-skript https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0för vanliga uppgifter på .

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Jag kan inte skapa en administrativ enhet**

**A.** Endast en global administratör eller privilegierad rolladministratör kan skapa en administrativ enhet i Azure AD. Kontrollera att användaren som försöker skapa den administrativa enheten har tilldelats rollen global administratör eller administratör för privilegierad roll.

**F: Jag har lagt till en grupp i den administrativa enheten, men medlemmarna i gruppen visas fortfarande inte i den administrativa enheten**

**A.** När du lägger till en grupp i den administrativa enheten, resulterar det inte i att lägga till alla medlemmar i gruppen i den administrativa enheten. Användare måste tilldelas direkt till den administrativa enheten.

**F: Jag har precis lagt till / tagit bort en medlem av den administrativa enheten och det är fortfarande dyker upp i användargränssnittet**

**A.** Det kan ibland ta några minuter att reflektera under sidan **Administrativa enheter.** Du kan välja att vänta i några minuter för att den ska reflektera under de administrativa enheterna. Du kan också gå direkt till den associerade resursens egenskaper och se om åtgärden har slutförts. Mer information om användare och grupper i RU finns i [Lista administrativa enheter för en användare](roles-admin-units-add-manage-users.md) och Lista administrativa enheter för en [grupp](roles-admin-units-add-manage-groups.md).

**F: Som delegerad lösenordsadministratör på en administrativ enhet kan jag inte återställa en viss användares lösenord**

**A.** En administratör som tilldelats över en administrativ enhet kan du återställa lösenordet endast för användare som tilldelats din administrativa enhet. Kontrollera att den användare som lösenordsåterställningen misslyckas tillhör de administrativa enheter som du har tilldelats rollen för. Om användaren tillhör samma administrativa enhet och du fortfarande inte kan återställa lösenordet för användaren kontrollerar du de roller som tilldelats användaren. För att förhindra en behörighetshöjning kan en administratör för begränsad administrationsenhet inte återställa lösenordet för en användare som har tilldelats en roll med ett organisationsomfattande omfång.

**F: Varför behövs administrativa enheter? Kan vi inte ha använt säkerhetsgrupper som ett sätt att definiera ett scope?**

**A.** Säkerhetsgrupper har en befintlig syftes- och auktoriseringsmodell. En användaradministratör kan till exempel hantera medlemskap i alla säkerhetsgrupper i Azure AD-organisationen, till exempel för att använda grupper för att hantera åtkomst till program som Salesforce. En användaradministratör bör inte ha möjlighet att hantera själva delegeringsmodellen, vilket skulle bli resultatet om säkerhetsgrupper utvidgades till att stödja scenarier för resursgruppering. Administrativa enheter, till exempel organisationsenheter i Active Directory i Windows Server, är avsedda att tillhandahålla ett sätt att begränsa administrationen av ett brett spektrum av katalogobjekt. Säkerhetsgrupper själva kan vara medlemmar i resursomfattningar. Om du använder säkerhetsgrupper för att definiera uppsättningen säkerhetsgrupper som en administratör kan hantera kan det bli förvirrande.

**F: Vad innebär det att lägga till en grupp i en administrativ enhet?**

**A.** Om du lägger till en grupp i en administrativ enhet hamnar själva gruppen i hanteringsomfånget för en användaradministratör som också är begränsad till den administratörsenheten. Användaradministratörer för den administrativa enheten kan hantera namn och medlemskap i själva gruppen. Användaradministratören för den administrativa enheten ger inte behörighet att hantera användarna av gruppen (till exempel återställa sina lösenord). För att ge användaradministratören möjlighet att hantera användare måste användarna vara direkta medlemmar i den administrativa enheten.

**F: Kan en resurs (användare eller grupp) vara medlem i mer än en administrativ enhet?**

**A.** Ja, en resurs kan vara medlem i mer än en administrativ enhet. Resursen kan hanteras av alla administratörer som omfattar hela organisationen och administrativ enhet som har behörighet över resursen.

**F: Är administrativa enheter tillgängliga i B2C-organisationer?**

**A.** Nej, administrativa enheter är inte tillgängliga för B2C-organisationer.

**F: Stöds kapslade administrativa enheter?**

**A.** Kapslade administrativa enheter stöds inte.

**F: Stöds administrativa enheter i PowerShell och Graph API?**

**S:** Ja. Stöd för administrativa enheter finns i [PowerShell cmdlet-dokumentation](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) och [exempelskript](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview), och stöd finns i Microsoft Graph för [resurstypen administrativeUnit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit).

## <a name="next-steps"></a>Nästa steg

- [Administrativa enheter för att begränsa omfattningen för roller översikt](directory-administrative-units.md)
- [Hantera administrativa enheter](roles-admin-units-manage.md)