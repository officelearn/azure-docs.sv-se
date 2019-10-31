---
title: Felsöka hantering av Azure AD-berättigande – Azure Active Directory
description: Lär dig mer om några objekt som du bör kontrol lera för att hjälpa dig att felsöka Azure Active Directory hantering av rättigheter.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f7e5e6168e8b365a04040f595f30e68e4e82f0a
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173331"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Felsöka hantering av Azure AD-berättigande

Den här artikeln beskriver några objekt som du bör kontrol lera för att hjälpa dig att felsöka Azure Active Directory (Azure AD) rättighets hantering.

## <a name="administration"></a>Administration

* Om du får ett meddelande om nekad åtkomst när du konfigurerar rättighets hantering, och du är global administratör, kontrollerar du att katalogen har en [licens för Azure AD Premium P2 (eller EMS E5)](entitlement-management-overview.md#license-requirements).

* Om du får ett meddelande om nekad åtkomst när du skapar eller visar åtkomst paket och du är medlem i en katalog Skapare grupp måste du [skapa en katalog](entitlement-management-catalog-create.md) innan du skapar ditt första Access-paket.

## <a name="resources"></a>Resurser

* Roller för program definieras av själva programmet och hanteras i Azure AD. Om ett program saknar resurs roller tilldelar rättighets hantering användare till en **standard åtkomst** roll.

    Observera att Azure Portal också kan visa tjänstens huvud namn för tjänster som inte kan väljas som program.  I synnerhet är **Exchange Online** och **SharePoint Online** tjänster, inte program som har resurs roller i katalogen, så de kan inte ingå i ett Access-paket.  Använd i stället gruppbaserad licensiering för att upprätta en lämplig licens för en användare som behöver åtkomst till dessa tjänster.

* För att en grupp ska vara en resurs i ett Access-paket måste den kunna ändras i Azure AD.  Grupper som har sitt ursprung i en lokal Active Directory kan inte tilldelas som resurser eftersom deras ägare eller medlems attribut inte kan ändras i Azure AD.   Grupper som har sitt ursprung i Exchange Online som distributions grupper kan inte ändras i Azure AD. 

* SharePoint Online-dokumentbibliotek och enskilda dokument kan inte läggas till som resurser.  Skapa i stället en [Azure AD-säkerhetsgrupp](../fundamentals/active-directory-groups-create-azure-portal.md), inkludera den gruppen och en plats roll i åtkomst paketet och Använd gruppen i SharePoint Online för att kontrol lera åtkomsten till dokument biblioteket eller dokumentet.

* Om det finns användare som redan har tilldelats till en resurs som du vill hantera med ett Access-paket måste du se till att användarna har tilldelats åtkomst paketet med en lämplig princip. Du kanske till exempel vill inkludera en grupp i ett Access-paket som redan har användare i gruppen. Om användarna i gruppen kräver fortsatt åtkomst måste de ha en lämplig princip för åtkomst paketen så att de inte förlorar åtkomsten till gruppen. Du kan tilldela åtkomst paketet genom att be användarna att begära åtkomst paketet som innehåller resursen, eller genom att direkt tilldela dem till åtkomst paketet. Mer information finns i [Inställningar för ändring av begäran och godkännande för ett Access-paket](entitlement-management-access-package-request-policy.md).

* När du tar bort en medlem i ett team tas de även bort från Office 365-gruppen. Borttagning från teamets Chat-funktioner kan vara fördröjd. Mer information finns i [grupp medlemskap](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

* Se till att katalogen inte är konfigurerad för multi-geo. Hantering av rättigheter stöder för närvarande inte Multi-geo-platser för SharePoint Online. SharePoint Online-webbplatser måste vara i standard Geo-Location för att regleras med hantering av rättigheter. Mer information finns i [multi-geo-funktioner i OneDrive och SharePoint Online](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365).

## <a name="external-users"></a>Externa användare

* När en extern användare vill begära åtkomst till ett Access-paket kontrollerar du att de använder **länken min åtkomst Portal** för åtkomst paketet. Mer information finns i [Dela länk för att begära ett Access-paket](entitlement-management-access-package-settings.md). Om en extern användare bara besöker **myaccess.Microsoft.com** och inte använder länken fullständig min åtkomst Portal, kommer de att se de åtkomst paket som är tillgängliga för dem i sin egen organisation och inte i din organisation.

* Om en extern användare inte kan begära åtkomst till ett Access-paket eller inte kan komma åt resurser måste du kontrol lera [inställningarna för externa användare](entitlement-management-external-users.md#settings-for-external-users).

* Om en ny extern användare, som inte tidigare har loggat in i din katalog, tar emot ett Access-paket, inklusive en SharePoint Online-webbplats, så visas deras åtkomst paket som inte fullständigt levererat förrän deras konto har allokerats i SharePoint Online. Mer information om delnings inställningar finns i [Granska dina externa delnings inställningar för SharePoint Online](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings).

## <a name="requests"></a>Begäranden

* När en användare vill begära åtkomst till ett Access-paket kontrollerar du att de använder **länken min åtkomst Portal** för åtkomst paketet. Mer information finns i [Dela länk för att begära ett Access-paket](entitlement-management-access-package-settings.md).

* Om du öppnar portalen My Access med din webbläsare inställd på privat eller Incognito läge kan detta orsaka en konflikt med inloggnings beteendet. Vi rekommenderar att du inte använder privat eller Incognito läge för webbläsaren när du besöker min åtkomst Portal.

* När en användare som ännu inte finns i din katalog loggar in på min åtkomst-Portal för att begära ett åtkomst paket, se till att de autentiseras med hjälp av deras organisations konto. Organisations kontot kan antingen vara ett konto i resurs katalogen eller i en katalog som ingår i någon av åtkomst paketets principer. Om användarens konto inte är ett organisations konto eller om den katalog där de autentiserar inte ingår i principen, kommer användaren inte att se åtkomst paketet. Mer information finns i [begäran om åtkomst till ett Access-paket](entitlement-management-request-access.md).

* Om en användare är blockerad från att logga in till resurs katalogen kommer de inte att kunna begära åtkomst i min åtkomst Portal. Innan användaren kan begära åtkomst måste du ta bort inloggnings blocket från användarens profil. Ta bort inloggnings blocket genom att klicka på **Azure Active Directory**i Azure Portal, klicka på **användare**, klicka på användaren och sedan på **profil**. Redigera avsnittet **Inställningar** och ändra **blockera inloggning** till **Nej**. Mer information finns i [lägga till eller uppdatera en användares profil information med hjälp av Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Du kan också kontrol lera om användaren har blockerats på grund av en [identitets skydds princip](../identity-protection/howto-unblock-user.md).

* Om en användare är både en beställare och en god kännare, kommer de inte att se sin begäran om ett åtkomst paket på sidan **godkännanden** i portalen för åtkomst. Det här beteendet är avsiktligt – en användare kan inte godkänna sin egen begäran. Se till att det åtkomst paket som begärs har ytterligare god kännare konfigurerade i principen. Mer information finns i [Inställningar för ändring av begäran och godkännande för ett Access-paket](entitlement-management-access-package-request-policy.md).

### <a name="view-a-requests-delivery-errors"></a>Visa leverans fel för en begäran

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **begär Anden**.

1. Välj den begäran som du vill visa.

    Om begäran har några leverans fel kommer status för begäran att **levereras eller** **levereras delvis**.

    Om det finns några leverans fel i förfrågningens informations fönster, finns det ett antal leverans fel.

1. Klicka på antalet för att se alla begärans leverans fel.

### <a name="reprocess-a-request"></a>Ombearbeta en begäran

Om en begäran påträffar ett fel kan du bearbeta begäran igen för att försöka igen. Du kan bara ombearbeta en begäran som har statusen **levererad** eller **delvis levererad** och ett avslutat datum som är mindre än en vecka.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **begär Anden**.

1. Klicka på den begäran som du vill bearbeta igen.

1. I informations fönstret för begäran klickar du på **rebearbeta begäran**.

    ![Ombearbeta en misslyckad begäran](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Avbryt en väntande begäran

Du kan bara avbryta en väntande begäran som ännu inte har levererats eller vars leverans har misslyckats.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **begär Anden**.

1. Klicka på den begäran som du vill avbryta.

1. Klicka på **Avbryt förfrågan**i informations fönstret för begäran.

## <a name="multiple-policies"></a>Flera principer

* Rättighets hantering följer de lägsta metod tipsen för behörighet. När en användare begär åtkomst till ett Access-paket som har flera principer som gäller inkluderar rättighets hantering logiken för att säkerställa att strängare eller mer bestämda principer prioriteras över allmänna principer. Om en princip är generisk, kanske inte rättighets hantering visar principen till begär ande eller kan välja en striktare princip automatiskt.

* Anta till exempel ett Access-paket med två principer för interna medarbetare där båda principerna gäller för begär Ande. Den första principen gäller för vissa användare som inkluderar begär Anden. Den andra principen är för alla användare i en katalog som den begär Ande är medlem i. I det här scenariot väljs den första principen automatiskt för beställaren eftersom det är mer strikt. Begär Anden har inte möjlighet att välja den andra principen.

* När flera principer tillämpas, är den princip som väljs automatiskt eller de principer som visas för den begär ande baserat på följande prioritets logik:

    | Princip prioritet | Omfång |
    | --- | --- |
    | P1 | Vissa användare och grupper i din katalog eller vissa anslutna organisationer |
    | P2 | Alla medlemmar i din katalog (exklusive gäster) |
    | P3 | Alla användare i din katalog (inklusive gäster) eller vissa anslutna organisationer |
    | P4 | Alla anslutna organisationer eller alla användare (alla anslutna organisationer och alla nya externa användare) |
    
    Om en princip finns i en kategori med högre prioritet ignoreras de lägre prioritets kategorierna. Ett exempel på hur flera principer med samma prioritet visas för beställaren finns i [Välj en princip](entitlement-management-request-access.md#select-a-policy).

## <a name="next-steps"></a>Nästa steg

- [Styr åtkomsten för externa användare](entitlement-management-external-users.md)
- [Visa rapporter om hur användare har åtkomst till hantering av rättigheter](entitlement-management-reports.md)
