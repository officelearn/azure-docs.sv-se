---
title: Felsöka hantering av Azure AD-rättigheter (för hands version) – Azure Active Directory
description: Lär dig mer om några objekt som du bör kontrol lera för att hjälpa dig att felsöka Azure Active Directory hantering av rättigheter (för hands version).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39ec27c75ff5ba9164b44b0524f90a4e28ab20f1
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488977"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Felsöka hantering av Azure AD-berättigande (för hands version)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättighets hantering är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln beskriver några objekt som du bör kontrol lera för att hjälpa dig att felsöka Azure Active Directory (Azure AD) rättighets hantering.

## <a name="checklist-for-entitlement-management-administration"></a>Check lista för administration av rättighets hantering

* Om du får ett meddelande om nekad åtkomst när du konfigurerar rättighets hantering, och du är global administratör, kontrollerar du att katalogen har en [licens för Azure AD Premium P2 (eller EMS E5)](entitlement-management-overview.md#license-requirements).  
* Om du får ett meddelande om nekad åtkomst när du skapar eller visar åtkomst paket och du är medlem i en katalog Skapare grupp måste du skapa en katalog innan du skapar ditt första Access-paket.

## <a name="checklist-for-adding-a-resource"></a>Check lista för att lägga till en resurs

* För att ett program ska vara en resurs i ett Access-paket måste det finnas minst en resurs roll som kan tilldelas. Rollerna definieras av själva programmet och hanteras i Azure AD. Observera att Azure Portal också kan visa tjänstens huvud namn för tjänster som inte kan väljas som program.  I synnerhet är **Exchange Online** och **SharePoint Online** tjänster, inte program som har resurs roller i katalogen, så de kan inte ingå i ett Access-paket.  Använd i stället gruppbaserad licensiering för att upprätta en lämplig licens för en användare som behöver åtkomst till dessa tjänster.

* För att en grupp ska vara en resurs i ett Access-paket måste den kunna ändras i Azure AD.  Grupper som har sitt ursprung i en lokal Active Directory kan inte tilldelas som resurser eftersom deras ägare eller medlems attribut inte kan ändras i Azure AD.  

* SharePoint Online-dokumentbibliotek och enskilda dokument kan inte läggas till som resurser.  Skapa i stället en Azure AD-säkerhetsgrupp, inkludera den gruppen och en plats roll i åtkomst paketet och Använd gruppen i SharePoint Online för att kontrol lera åtkomsten till dokument biblioteket eller dokumentet.

* Om det finns användare som redan har tilldelats till en resurs som du vill hantera med ett Access-paket måste du se till att användarna har tilldelats åtkomst paketet med en lämplig princip. Du kanske till exempel vill inkludera en grupp i ett Access-paket som redan har användare i gruppen. Om användarna i gruppen kräver fortsatt åtkomst måste de ha en lämplig princip för åtkomst paketen så att de inte förlorar åtkomsten till gruppen. Du kan tilldela åtkomst paketet genom att be användarna att begära åtkomst paketet som innehåller resursen, eller genom att direkt tilldela dem till åtkomst paketet. Mer information finns i [Redigera och hantera ett befintligt Access-paket](entitlement-management-access-package-edit.md).

## <a name="checklist-for-providing-external-users-access"></a>Check lista för att ge åtkomst till externa användare

* Om det finns en [lista över tillåtna](../b2b/allow-deny-list.md)B2B-listor kommer användare vars kataloger inte är tillåtna att kunna begära åtkomst.

* Se till att det inte finns några [principer för villkorlig åtkomst](../conditional-access/require-managed-devices.md) som hindrar externa användare från att begära åtkomst eller att kunna använda programmen i åtkomst paketen.

## <a name="checklist-for-request-issues"></a>Check lista för problem med begäran

* När en användare vill begära åtkomst till ett Access-paket kontrollerar du att de använder **länken min åtkomst Portal** för åtkomst paketet. Mer information finns i [Kopiera min åtkomst Portal länk](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* När en användare loggar in på My Access-portalen för att begära ett åtkomst paket, se till att de autentiseras med hjälp av deras organisations konto. Organisations kontot kan antingen vara ett konto i resurs katalogen eller i en katalog som ingår i någon av åtkomst paketets principer. Om användarens konto inte är ett organisations konto, eller om katalogen inte ingår i principen, kommer användaren inte att se åtkomst paketet. Mer information finns i [begäran om åtkomst till ett Access-paket](entitlement-management-request-access.md).

* Om en användare är blockerad från att logga in till resurs katalogen kommer de inte att kunna begära åtkomst i min åtkomst Portal. Innan användaren kan begära åtkomst måste du ta bort inloggnings blocket från användarens profil. Ta bort inloggnings blocket genom att klicka på **Azure Active Directory**i Azure Portal, klicka på **användare**, klicka på användaren och sedan på **profil**. Redigera avsnittet **Inställningar** och ändra **blockera inloggning** till **Nej**. Mer information finns i [lägga till eller uppdatera en användares profil information med hjälp av Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Du kan också kontrol lera om användaren har blockerats på grund av en [identitets skydds princip](../identity-protection/howto-unblock-user.md).

* Om en användare är både en beställare och en god kännare, kommer de inte att se sin begäran om ett åtkomst paket på sidan godkännanden i  portalen för åtkomst. Det här beteendet är avsiktligt – en användare kan inte godkänna sin egen begäran. Se till att det åtkomst paket som begärs har ytterligare god kännare konfigurerade i principen. Mer information finns i [Redigera en befintlig princip](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Om en ny extern användare, som inte tidigare har loggat in i din katalog, tar emot ett Access-paket, inklusive en SharePoint Online-webbplats, så visas deras åtkomst paket som inte fullständigt levererat förrän deras konto har allokerats i SharePoint Online.

## <a name="next-steps"></a>Nästa steg

- [Visa rapporter om hur användare har åtkomst till hantering av rättigheter](entitlement-management-reports.md)
