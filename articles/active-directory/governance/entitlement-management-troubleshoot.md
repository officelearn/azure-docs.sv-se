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
ms.date: 04/25/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ca7955186d6c6fb1dff2acd6a2d5badd1cedaef
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541517"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Felsök hantering av Azure AD-rättighet (förhandsversion)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättigheten management är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln beskrivs saker du bör kolla om hjälper dig att felsöka Azure Active Directory (Azure AD) rättigheten management.

## <a name="checklist-for-entitlement-management-administration"></a>Checklista för berättigande management administration

* Om du får ett meddelande om nekad när du konfigurerar rättigheten hantering och du är en Global administratör, kontrollera att katalogen har en [licens för Azure AD Premium P2 (eller EMS E5)](entitlement-management-overview.md#prerequisites).  
* Om du får en nekad meddelande när du skapar eller genom att visa åtkomst-paket och du är medlem i en kataloggrupp skapare, måste du skapa en katalog innan du skapar ditt första åtkomst-paket.

## <a name="checklist-for-adding-a-resource"></a>Checklista för att lägga till en resurs

* Om det finns användare som redan har tilldelats till en resurs som du vill hantera med ett åtkomst-paket, ska du att användarna har tilldelats åtkomst till paketet med en lämplig princip. Du kanske exempelvis vill inkludera en grupp i ett åtkomst-paket som redan har användare i gruppen. Om dessa användare i grupp kräver fortsatt åtkomst, måste de ha en lämplig princip för åtkomst-paket så att de inte förlorar åtkomsten till gruppen. Du kan tilldela åtkomst till paketet genom att be användare att begära åtkomst till paketet som innehåller den här resursen eller genom att tilldela dem direkt åtkomst till paketet. Mer information finns i [redigera och hantera ett befintligt paket för åtkomst](entitlement-management-access-package-edit.md).

## <a name="checklist-for-request-issues"></a>Checklista för problem

* När en användare vill begära åtkomst till ett åtkomst-paket, är det viktigt att de använder den **portalen Mina åtkomstlänk** för åtkomst-paketet. Mer information finns i [kopia Mina portal åtkomstlänk](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* När en användare loggar in på portalen Mina åtkomst att begära ett åtkomst-paket, måste de autentiseras med organisationskontot. Organisationens konto kan vara antingen ett konto i resursbiblioteket eller i en katalog som ingår i en av principerna för åtkomst-paketet. Om användarens konto inte är ett organisationskonto eller katalogen ingår inte i principen, kommer användaren inte att se åtkomst-paketet. Mer information finns i [begär åtkomst till en åtkomst-paketet](entitlement-management-request-access.md).

* Om en användare blockeras från att logga in till resursbiblioteket, kan de inte begära åtkomst i portalen Mina åtkomst. Innan användaren kan begära åtkomst, måste du ta bort blockeringen inloggning från användarens profil. Om du vill ta bort blockeringen logga in på Azure-portalen, klickar du på **Azure Active Directory**, klickar du på **användare**, klickar du på användaren och klicka sedan på **profil**. Redigera den **inställningar** och ändra **blockera inloggning** till **nr**. Mer information finns i [Lägg till eller uppdatera en användares profilinformation med hjälp av Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md)

* I portalen Mina åtkomst om en användare finns både en begärande och granskare, så visas inte begäran för ett åtkomst-paket på den **godkännanden** sidan. Det här beteendet är avsiktligt – en användare kan inte godkänna sina egna begäran. Se till att åtkomst-paketet som det begär har ytterligare godkännare som konfigurerats på principen. Mer information finns i [redigera en befintlig princip](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Om en ny extern användare som inte har tidigare har registrerat i katalogen, tar emot ett åtkomst-paket, inklusive en SharePoint Online-webbplats, visar sina åtkomst-paket som inte helt levereras tills deras konto har etablerats i SharePoint Online.

## <a name="next-steps"></a>Nästa steg

- [Visa rapporter över hur användare får åtkomst i rätt management](entitlement-management-reports.md)
