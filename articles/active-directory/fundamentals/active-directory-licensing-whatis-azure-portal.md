---
title: Vad är gruppbaserad licensiering – Azure Active Directory | Microsoft Docs
description: Lär dig mer om att Azure Active Directory gruppbaserad licensiering, inklusive hur det fungerar och bästa praxis.
services: active-directory
keywords: Azure AD-licensiering
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eec896e6cccaf58c83820161c54c4f10cfadadd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77561569"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Vad är gruppbaserad licensiering i Azure Active Directory?

Microsoft-betaltjänster i molnet, till exempel Office 365, Enterprise Mobility + Security, Dynamics 365 och andra liknande produkter kräver licenser. Dessa licenser tilldelas till varje användare som behöver åtkomst till dessa tjänster. För att hantera licenser använder administratörer någon av hanteringsportalerna (Office eller Azure) och PowerShell-cmdlets. Azure Active Directory (Azure AD) är den underliggande infrastrukturen som har stöd för identitetshantering för alla Microsofts molntjänster. Azure AD lagrar information om användarnas licenstilldelningsstatus.

Hittills har det endast gått att tilldela licenser på nivån för enskilda användare, vilket kan försvåra storskalig hantering. För att till exempel lägga till eller ta bort licenser baserat på organisationsförändringar, till exempel om användare börjar eller slutar på företaget eller på en avdelning, måste administratören ofta skriva ett komplext PowerShell-skript. Det här skriptet gör separata anrop till molntjänsten.

För att underlätta för administratörer erbjuder Azure AD nu gruppbaserad licensiering. Du kan tilldela en eller flera produktlicenser till en grupp. Azure AD ser till att licenserna tilldelas till alla medlemmar i gruppen. Nya medlemmar som ansluter till gruppen tilldelas korrekta licenser. När de lämnar gruppen tas dessa licenser bort. Den här licensierings hanteringen eliminerar behovet av att automatisera licens hantering via PowerShell för att avspegla ändringar i organisations-och avdelnings strukturen per användare.

## <a name="licensing-requirements"></a>Licenskrav
Du måste ha någon av följande licenser för att använda gruppbaserad licensiering:

- Betald eller utvärderings prenumeration för Azure AD Premium P1 och senare

- Betald eller utvärderings version av Office 365 Enterprise E3 eller Office 365 a3 eller Office 365 GCC G3 eller Office 365 E3 för GCCH eller Office 365 E3 för DOD och senare

### <a name="required-number-of-licenses"></a>Antalet licenser som krävs
För grupper som har tilldelats en licens måste du också ha en licens för varje unik medlem. Även om du inte behöver tilldela varje medlem i gruppen en licens måste du ha minst tillräckligt många licenser för att inkludera alla medlemmar. Om du till exempel har 1 000 unika medlemmar som är en del av licensierade grupper i din klient organisation måste du ha minst 1 000 licenser för att uppfylla licens avtalet.

## <a name="features"></a>Funktioner

Här är de viktigaste funktionerna i gruppbaserad licensiering:

- Licenser kan tilldelas till valfri säkerhetsgrupp i Azure AD. Säkerhets grupper kan synkroniseras lokalt med hjälp av Azure AD Connect. Du kan också skapa säkerhetsgrupper direkt i Azure AD (även kallade molnspecifika grupper) eller automatiskt via funktionen för dynamiska grupper i Azure AD.

- När en produktlicens har tilldelats en grupp kan administratören inaktivera en eller flera tjänstplaner i produkten. Normalt görs den här tilldelningen när organisationen ännu inte är redo att börja använda en tjänst som ingår i en produkt. Administratören kan till exempel tilldela Office 365 till en avdelning, men tillfälligt inaktivera tjänsten Yammer.

- Alla Microsoft-molntjänster som kräver licensiering på användarnivå stöds. Det här stödet omfattar alla Office 365-produkter, Enterprise Mobility + Security och Dynamics 365.

- Gruppbaserad licensiering är för närvarande endast tillgängligt via [Azure Portal](https://portal.azure.com). Om du huvudsakligen använder andra hanterings portaler för användar-och grupp hantering, till exempel [Microsoft 365 administrations Center](https://admin.microsoft.com), kan du fortsätta att göra det. Men du bör använda Azure Portal för att hantera licenser på gruppnivå.

- Azure AD hanterar automatiskt licensändringar som uppstår vid ändringar av gruppmedlemskap. Licensändringarna börjar normalt att tillämpas inom några minuter efter en ändring av gruppmedlemskapet.

- En användare kan vara medlem i flera grupper med definierade licensprinciper. En användare kan också ha vissa licenser som tilldelats direkt, utanför grupper. Det slutliga användartillståndet är en kombination av alla tilldelade produkt- och tjänstlicenser. Om en användare tilldelas samma licens från flera källor, används licensen bara en gång.

- I vissa fall kan licenser inte tilldelas till en användare. Exempelvis kanske det inte finns tillräckligt med tillgängliga licenser i klientorganisationen, eller så kanske tjänster som står i konflikt har tilldelats på samma gång. Administratörer har åtkomst till information om användare för vilka Azure AD inte helt kan bearbeta grupplicenser. De kan sedan vidta åtgärder baserat på den informationen.

## <a name="your-feedback-is-welcome"></a>Vi vill gärna ha din feedback!

Om du har feedback eller funktions förfrågningar kan du dela dem med oss med hjälp av [Azure AD admin-forumet](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Nästa steg

Mer information om andra scenarier för licenshantering via gruppbaserad licensiering finns här:

* [Tilldela licenser till en grupp i Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Identifiera och lösa licensproblem för en grupp i Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Migrera enskilda licensierade användare till gruppbaserad licensiering i Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Så här migrerar du användare mellan produkt licenser med gruppbaserad licensiering i Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Fler scenarier med gruppbaserad licensiering i Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
* [PowerShell-exempel för gruppbaserad licensiering i Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
