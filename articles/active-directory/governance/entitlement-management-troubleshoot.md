---
title: Felsöka Azure AD rättigheten hantering (förhandsversion) – Azure Active Directory
description: Läs mer om saker du bör kontrollera för att hjälpa dig att felsöka Azure Active Directory rättigheten hantering (förhandsversion).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2526ef10c3080dae1b32881a109a9436a0fd390
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473816"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Felsök hantering av Azure AD-rättighet (förhandsversion)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättigheten management är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln beskrivs saker du bör kolla om hjälper dig att felsöka Azure Active Directory (Azure AD) rättigheten management.

## <a name="checklist-for-entitlement-management-administration"></a>Checklista för berättigande management administration

* Om du får ett meddelande om nekad när du konfigurerar rättigheten hantering och du är en Global administratör, kontrollera att katalogen har en [licens för Azure AD Premium P2 (eller EMS E5)](entitlement-management-overview.md#license-requirements).  
* Om du får en nekad meddelande när du skapar eller genom att visa åtkomst-paket och du är medlem i en kataloggrupp skapare, måste du skapa en katalog innan du skapar ditt första åtkomst-paket.

## <a name="checklist-for-adding-a-resource"></a>Checklista för att lägga till en resurs

* För ett program ska vara en resurs i ett paket för åtkomst, måste den ha minst en resurs-roll som kan tilldelas. Rollerna definieras av själva programmet och hanteras i Azure AD. Observera att Azure-portalen kan också visa tjänstens huvudnamn för tjänster som inte kan väljas som program.  I synnerhet **Exchange Online** och **SharePoint Online** är tjänster, inte de program som har resursroller i katalogen, så att de inte kan ingå i ett paket för åtkomst.  I stället använda gruppbaserad licensiering för att upprätta en lämplig licens för en användare som behöver åtkomst till dessa tjänster.

* För en grupp ska vara en resurs i ett åtkomst-paket måste den kunna vara ändringsbar i Azure AD.  Kan inte tilldelas grupper som kommer från en lokal Active Directory som resurser eftersom deras ägare eller Medlemsattribut inte kan ändras i Azure AD.  

* SharePoint Online-dokumentbibliotek och enskilda dokument kan inte läggas till som resurser.  I stället skapa en Azure AD-säkerhetsgrupp, inkluderar gruppen och en platsroll i paketets åtkomst och använda den gruppen för att styra åtkomsten till dokumentbiblioteket eller dokument i SharePoint Online.

* Om det finns användare som redan har tilldelats till en resurs som du vill hantera med ett åtkomst-paket, ska du att användarna har tilldelats åtkomst till paketet med en lämplig princip. Du kanske exempelvis vill inkludera en grupp i ett åtkomst-paket som redan har användare i gruppen. Om dessa användare i grupp kräver fortsatt åtkomst, måste de ha en lämplig princip för åtkomst-paket så att de inte förlorar åtkomsten till gruppen. Du kan tilldela åtkomst till paketet genom att be användare att begära åtkomst till paketet som innehåller den här resursen eller genom att tilldela dem direkt åtkomst till paketet. Mer information finns i [redigera och hantera ett befintligt paket för åtkomst](entitlement-management-access-package-edit.md).

## <a name="checklist-for-providing-external-users-access"></a>Checklista för att tillhandahålla externa användare åtkomst

* Om det finns en B2B [lista över tillåtna](../b2b/allow-deny-list.md), och sedan på användare vars kataloger inte är tillåtna inte kommer att kunna begära åtkomst.

* Se till att det finns inga [principer för villkorlig åtkomst](../conditional-access/require-managed-devices.md) som förhindrar att externa användare begär åtkomst eller att kunna använda program i åtkomst-paket.

## <a name="checklist-for-request-issues"></a>Checklista för problem

* När en användare vill begära åtkomst till ett åtkomst-paket, är det viktigt att de använder den **portalen Mina åtkomstlänk** för åtkomst-paketet. Mer information finns i [kopia Mina portal åtkomstlänk](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* När en användare loggar in på portalen Mina åtkomst att begära ett åtkomst-paket, måste de autentiseras med organisationskontot. Organisationens konto kan vara antingen ett konto i resursbiblioteket eller i en katalog som ingår i en av principerna för åtkomst-paketet. Om användarens konto inte är ett organisationskonto eller katalogen ingår inte i principen, kommer användaren inte att se åtkomst-paketet. Mer information finns i [begär åtkomst till en åtkomst-paketet](entitlement-management-request-access.md).

* Om en användare blockeras från att logga in till resursbiblioteket, kan de inte begära åtkomst i portalen Mina åtkomst. Innan användaren kan begära åtkomst, måste du ta bort blockeringen inloggning från användarens profil. Om du vill ta bort blockeringen logga in på Azure-portalen, klickar du på **Azure Active Directory**, klickar du på **användare**, klickar du på användaren och klicka sedan på **profil**. Redigera den **inställningar** och ändra **blockera inloggning** till **nr**. Mer information finns i [Lägg till eller uppdatera en användares profilinformation med hjälp av Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Du kan också kontrollera om användaren har blockerats på grund av ett [Identity Protection-principen](../identity-protection/howto-unblock-user.md).

* I portalen Mina åtkomst om en användare finns både en begärande och granskare, så visas inte begäran för ett åtkomst-paket på den **godkännanden** sidan. Det här beteendet är avsiktligt – en användare kan inte godkänna sina egna begäran. Se till att åtkomst-paketet som det begär har ytterligare godkännare som konfigurerats på principen. Mer information finns i [redigera en befintlig princip](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Om en ny extern användare som inte har tidigare har registrerat i katalogen, tar emot ett åtkomst-paket, inklusive en SharePoint Online-webbplats, visar sina åtkomst-paket som inte helt levereras tills deras konto har etablerats i SharePoint Online.

## <a name="next-steps"></a>Nästa steg

- [Visa rapporter över hur användare får åtkomst i rätt management](entitlement-management-reports.md)
