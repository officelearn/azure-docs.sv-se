---
title: Felsöka hantering av berättiganden – Azure AD
description: Lär dig mer om några objekt som du bör kontrollera för att hjälpa dig att felsöka hantering av Azure Active Directory-berättigande.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c38e1a61827da547bb39a699a0e92043e63466c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128475"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Felsöka hantering av Azure AD-berättigande

I den här artikeln beskrivs några objekt som du bör kontrollera för att hjälpa dig att felsöka hantering av Azure Active Directory (Azure AD).

## <a name="administration"></a>Administration

* Om du får ett meddelande om nekad åtkomst när du konfigurerar rättighetshantering och du är global administratör, kontrollerar du att katalogen har en [Azure AD Premium P2-licens (eller EMS E5).](entitlement-management-overview.md#license-requirements)

* Om du får ett meddelande om nekad åtkomst när du skapar eller visar åtkomstpaket och du är medlem i en grupp för innehållsskapare i katalogen måste du [skapa en katalog](entitlement-management-catalog-create.md) innan du skapar ditt första åtkomstpaket.

## <a name="resources"></a>Resurser

* Roller för program definieras av själva programmet och hanteras i Azure AD. Om ett program inte har några resursroller tilldelar rättighetshantering användare till en **standardåtkomstroll.**

    Observera att Azure-portalen också kan visa tjänsthuvudnamn för tjänster som inte kan väljas som program.  **Exchange Online** och **SharePoint Online** är i synnerhet tjänster, inte program som har resursroller i katalogen, så de kan inte inkluderas i ett åtkomstpaket.  Använd i stället gruppbaserad licensiering för att upprätta en lämplig licens för en användare som behöver åtkomst till dessa tjänster.

* För att en grupp ska kunna vara en resurs i ett åtkomstpaket, måste den kunna ändras i Azure AD.  Grupper som har sitt ursprung i en lokal Active Directory Domain Services kan inte tilldelas som resurser, eftersom deras ägar- eller medlemsattribut inte kan ändras i Azure AD.   Grupper som har sitt ursprung i Exchange Online som distributionsgrupper kan inte heller ändras i Azure AD. 

* SharePoint Online-dokumentbibliotek och enskilda dokument kan inte läggas till som resurser.  Skapa i stället en [Azure AD-säkerhetsgrupp](../fundamentals/active-directory-groups-create-azure-portal.md), inkludera den gruppen och en platsroll i åtkomstpaketet och använd den gruppen i SharePoint Online för att styra åtkomsten till dokumentbiblioteket eller dokumentet.

* Om det finns användare som redan har tilldelats till en resurs som du vill hantera med ett åtkomstpaket, måste du se till att användarna har tilldelats till åtkomstpaketet med en lämplig princip. Du kanske till exempel vill inkludera en grupp i ett åtkomstpaket som redan har användare i gruppen. Om användarna i gruppen behöver fortsatt åtkomst, måste de ha en lämplig princip för åtkomstpaketen så att de inte förlorar åtkomsten till gruppen. Du kan tilldela åtkomstpaketet genom att antingen be användarna att begära åtkomstpaketet som innehåller resursen, eller genom att tilldela dem direkt till åtkomstpaketet. Mer information finns i [Ändra inställningar för begäran och godkännande för ett åtkomstpaket](entitlement-management-access-package-request-policy.md).

* När du tar bort medlemmar från ett team tas de även bort från Office 365-gruppen. Borttagningen från teamets chattfunktioner kan vara fördröjd. Mer information finns i [Gruppmedlemskap](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

* Kontrollera att katalogen inte är konfigurerad för Multi-Geo. Berättigandehanteringen stöder för närvarande inte Multi-Geo-platser i SharePoint Online. SharePoint Online-webbplatser måste finns på en geografisk standardplats för att kunna styras med berättigandehanteringen. Mer information finns [i Multi-Geo-funktioner i OneDrive och SharePoint Online](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365).

## <a name="access-packages"></a>Åtkomstpaket

* Om du försöker ta bort ett åtkomstpaket eller en princip och ser ett felmeddelande om att det finns aktiva tilldelningar, om du inte ser några användare med tilldelningar, kontrollerar du om några nyligen borttagna användare fortfarande har tilldelningar. Under 30-dagarsfönstret efter att en användare har tagits bort kan användarkontot återställas.   

## <a name="external-users"></a>Externa användare

* När en extern användare vill begära åtkomst till ett åtkomstpaket kontrollerar du att de använder **länken Min Åtkomst-portal** för åtkomstpaketet. Mer information finns i [Dela länk för att begära ett åtkomstpaket](entitlement-management-access-package-settings.md). Om en extern användare bara besöker **myaccess.microsoft.com** och inte använder hela min åtkomstportallänk, ser de de åtkomstpaket som är tillgängliga för dem i deras egen organisation och inte i din organisation.

* Om en extern användare inte kan begära åtkomst till ett åtkomstpaket eller inte kan komma åt resurser måste du kontrollera [inställningarna för externa användare](entitlement-management-external-users.md#settings-for-external-users).

* Om en ny extern användare (som inte tidigare har loggat in på din katalog) tar emot ett åtkomstpaket med en SharePoint Online-webbplats, visas deras åtkomstpaket som inte fullständigt levererat förrän kontot har etablerats i SharePoint Online. Mer information om delningsinställningar finns i [Granska inställningarna för extern SharePoint Online.](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings)

## <a name="requests"></a>Begäranden

* När en användare vill begära åtkomst till ett åtkomstpaket ska du se till att de använder **länken Min Åtkomst-portal** för åtkomstpaketet. Mer information finns i [Dela länk för att begära ett åtkomstpaket](entitlement-management-access-package-settings.md).

* Om du öppnar Min åtkomst-portalen med din webbläsare inställd på privat läge eller inkognitoläge kan detta orsaka en konflikt med inloggningen. Vi rekommenderar att du inte använder privat läge eller inkognitoläge för webbläsaren när du besöker Min åtkomst-portalen.

* När användare som ännu inte finns i din katalog loggar in på Min åtkomst-portalen för att begära ett åtkomstpaket, bör du se till att de autentiseras med hjälp av sitt organisationskonto. Organisationskontot kan antingen vara ett konto i resurskatalogen, eller i en katalog som ingår i någon av åtkomstpaketets principer. Om användarens konto inte är ett organisationskonto, eller om den katalog där de autentiseras inte ingår i principen, kommer användaren inte att se åtkomstpaketet. Mer information finns i [Begär åtkomst till ett åtkomstpaket](entitlement-management-request-access.md).

* Användare som är blockerade från att logga in på resurskatalogen, kommer inte att kunna begära åtkomst i Min åtkomst-portalen. Innan användaren kan begära åtkomst måste du ta bort inloggningsblockeringen från användarens profil. Om du vill ta bort inloggningsblocket klickar du på Azure Active Directory i **Azure-portalen,** klickar på **Användare,** klickar på användaren och sedan på **Profil**. Redigera avsnittet **Inställningar** och ändra **Blockera inloggning** till **Nr**. Mer information finns i [Lägga till eller uppdatera en användares profilinformation med Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Du kan också kontrollera om användaren har blockerats på grund av en [identitetsskyddsprincip](../identity-protection/howto-unblock-user.md).

* Om en användare är både en beställare och en godkännare i Portalen Min Åtkomst, kommer de inte att se sin begäran om ett åtkomstpaket på sidan **Godkännanden.** Det här är avsiktligt – en användare kan inte godkänna sin egen begäran. Kontrollera att det åtkomstpaket som begärs har fler godkännare konfigurerade i principen. Mer information finns i [Ändra inställningar för begäran och godkännande för ett åtkomstpaket](entitlement-management-access-package-request-policy.md).

### <a name="view-a-requests-delivery-errors"></a>Visa leveransfel för en begäran

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Access-paket** på menyn till vänster och öppna sedan åtkomstpaketet.

1. Klicka på **Begäranden**.

1. Välj den begäran som du vill visa.

    Om begäran har några leveransfel kommer begäran **status vara Olevererade** eller **delvis levererade**.

    Om det finns några leveransfel kommer det i informationsfönstret för begäran att finnas ett antal leveransfel.

1. Klicka på antalet om du vill visa alla leveransfel för begäran.

### <a name="reprocess-a-request"></a>Bearbeta om en begäran

Om en begäran stöter på ett fel kan du bearbeta om begäran för att försöka igen. Du kan bara bearbeta om en begäran som har **statusen Leverans misslyckades** eller **delvis levererad** och ett slutfört datum på mindre än en vecka.

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Access-paket** på menyn till vänster och öppna sedan åtkomstpaketet.

1. Klicka på **Begäranden**.

1. Klicka på den begäran som du vill bearbeta om.

1. Klicka på Bearbeta begäran i fönstret **Förfråbehingsinformation**i fönstret Förfrå berinformation .

    ![Bearbeta om en misslyckad begäran](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Avbryta en väntande begäran

Du kan bara avbryta en väntande begäran som ännu inte har levererats eller vars leverans har misslyckats.

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Access-paket** på menyn till vänster och öppna sedan åtkomstpaketet.

1. Klicka på **Begäranden**.

1. Klicka på den begäran som du vill avbryta.

1. Klicka på **Avbryt begäran**i fönstret Förfrå berinformation.

## <a name="multiple-policies"></a>Flera principer

* Rättighetshantering följer lägsta praxis för privilegier. När en användare begär åtkomst till ett åtkomstpaket som har flera principer som gäller, innehåller rättighetshantering logik för att säkerställa att strängare eller mer specifika principer prioriteras framför allmänna principer. Om en princip är allmän kanske rättighetshanteringen inte visar principen för beställaren eller automatiskt väljer en striktare princip.

* Tänk dig till exempel ett åtkomstpaket med två principer för interna medarbetare där båda principerna gäller för beställaren. Den första principen är för specifika användare som innehåller beställaren. Den andra principen är för alla användare i en katalog som beställaren är medlem i. I det här fallet väljs den första principen automatiskt för beställaren eftersom den är striktare. Beställaren får inte möjlighet att välja den andra principen.

* När flera principer gäller baseras principen som väljs automatiskt eller de principer som visas för beställaren på följande prioritetslogik:

    | Politisk prioritet | Omfång |
    | --- | --- |
    | P1 | Specifika användare och grupper i katalogen ELLER specifika anslutna organisationer |
    | P2 | Alla medlemmar i din katalog (exklusive gäster) |
    | P3 | Alla användare i din katalog (inklusive gäster) ELLER specifika anslutna organisationer |
    | P4 | Alla anslutna organisationer ELLER alla användare (alla anslutna organisationer + alla nya externa användare) |
    
    Om någon princip finns i en kategori med högre prioritet ignoreras kategorierna med lägre prioritet. Ett exempel på hur flera principer med samma prioritet visas för beställaren finns i [Välj en princip](entitlement-management-request-access.md#select-a-policy).

## <a name="next-steps"></a>Nästa steg

- [Styra åtkomst för externa användare](entitlement-management-external-users.md)
- [Visa rapporter om hur användare fick åtkomst i rättighetshantering](entitlement-management-reports.md)
