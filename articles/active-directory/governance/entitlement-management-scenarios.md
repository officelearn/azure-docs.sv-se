---
title: Vanliga scenarier i rättighetshantering - Azure AD
description: Lär dig de steg på hög nivå som du bör följa för vanliga scenarier i Azure Active Directory-berättigandehantering.
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
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9d259c6e2a6ac9ced5f9a1c29d4aec08010f4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190559"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Vanliga scenarier i Azure AD-rättighetshantering

Det finns flera sätt att konfigurera rättighetshantering för din organisation. Men om du precis har börjat är det bra att förstå de vanliga scenarierna för administratörer, katalogägare, åtkomstpakethanterare, godkännare och beställare.

## <a name="delegate"></a>Delegat

### <a name="administrator-delegate-management-of-resources"></a>Administratör: Delegera hantering av resurser

1. [Titta på video: Delegering från IT till avdelningschef](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Delegera användare till rollen som skapare i katalogen](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Skapare av katalog: Delegera hantering av resurser

- [Skapa en ny katalog](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Katalogägare: Delegera hantering av resurser

1. [Lägg till delägare i katalogen](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Lägga till resurser i katalogen](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Katalogägare: Delegera hantering av åtkomstpaket

1. [Titta på video: Delegering från katalogägare för att komma åt pakethanteraren](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Delegera användare att komma åt rollen pakethanterare](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Styra åtkomsten för användare i organisationen

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Åtkomstpakethanterare: Tillåt anställda i organisationen att begära åtkomst till resurser

1. [Skapa ett nytt åtkomstpaket](entitlement-management-access-package-create.md#start-new-access-package)
1. [Lägga till grupper, Teams, program eller SharePoint-webbplatser för att komma åt paket](entitlement-management-access-package-create.md#resource-roles)
1. [Lägga till en begärandeprincip så att användare i katalogen kan begära åtkomst](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Ange förfallodatuminställningar](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Beställare: Begär åtkomst till resurser

1. [Logga in på My Access-portalen](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Hitta åtkomstpaket
1. [Begär åtkomst](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Godkännare: Godkänna begäranden till resurser

1. [Öppna begäran i My Access-portalen](entitlement-management-request-approve.md#open-request)
1. [Godkänna eller neka åtkomstbegäran](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Beställare: Visa de resurser som du redan har tillgång till

1. [Logga in på My Access-portalen](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Visa aktiva åtkomstpaket

## <a name="govern-access-for-users-outside-your-organization"></a>Styra åtkomst för användare utanför organisationen

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Administratör: Samarbeta med en extern partnerorganisation

1. [Läs om hur åtkomst fungerar för externa användare](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Granska inställningar för externa användare](entitlement-management-external-users.md#settings-for-external-users)
1. [Lägga till en anslutning till den externa organisationen](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Åtkomstpakethanterare: Samarbeta med en extern partnerorganisation

1. [Skapa ett nytt åtkomstpaket](entitlement-management-access-package-create.md#start-new-access-package)
1. [Lägga till grupper, Teams, program eller SharePoint-webbplatser för att komma åt paket](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Lägga till en begärandeprincip så att användare som inte finns i katalogen kan begära åtkomst](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Ange förfallodatuminställningar](entitlement-management-access-package-create.md#lifecycle)
1. [Kopiera länken för att begära åtkomstpaketet](entitlement-management-access-package-settings.md)
1. Skicka länken till din externa partner kontaktpartner för att dela med sina användare

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Beställare: Begär åtkomst till resurser som extern användare

1. Hitta länken till åtkomstpaketet som du fick från din kontakt
1. [Logga in på My Access-portalen](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Begär åtkomst](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Godkännare: Godkänna begäranden till resurser

1. [Öppna begäran i My Access-portalen](entitlement-management-request-approve.md#open-request)
1. [Godkänna eller neka åtkomstbegäran](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Beställare: Visa de resurser som du redan har tillgång till

1. [Logga in på My Access-portalen](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Visa aktiva åtkomstpaket

## <a name="day-to-day-management"></a>Daglig förvaltning

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Pakethanteraren för åtkomst: Uppdatera resurserna för ett projekt

1. [Titta på video: Daglig hantering: Saker och ting har förändrats](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Öppna åtkomstpaketet
1. [Lägga till eller ta bort grupper, Teams, program eller SharePoint-webbplatser](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Åtkomstpakethanterare: Uppdatera varaktigheten för ett projekt

1. [Titta på video: Daglig hantering: Saker och ting har förändrats](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Öppna åtkomstpaketet
1. [Öppna livscykelinställningarna](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [Uppdatera förfalloinställningarna](entitlement-management-access-package-lifecycle-policy.md#lifecycle)

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Åtkomstpakethanteraren: Uppdatera hur åtkomst godkänns för ett projekt

1. [Titta på video: Daglig hantering: Saker och ting har förändrats](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Öppna en befintlig princip för inställningar för begäran och godkännande](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Uppdatera godkännandeinställningarna](entitlement-management-access-package-request-policy.md#approval)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Pakethanteraren för åtkomst: Uppdatera personerna för ett projekt

1. [Titta på video: Daglig hantering: Saker och ting har förändrats](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Ta bort användare som inte längre behöver åtkomst](entitlement-management-access-package-assignments.md)
1. [Öppna en befintlig princip för inställningar för begäran och godkännande](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Lägg till användare som behöver åtkomst](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Pakethanteraren för åtkomst: Tilldela direkt specifika användare till ett åtkomstpaket

1. [Om användarna behöver olika livscykelinställningar lägger du till en ny princip i åtkomstpaketet](entitlement-management-access-package-request-policy.md#add-a-new-policy-of-request-and-approval-settings)
1. [Tilldela direkt specifika användare till åtkomstpaketet](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Uppgifter och rapporter

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Administratör: Visa vem som har tilldelningar till ett åtkomstpaket

1. Öppna ett åtkomstpaket
1. [Visa tilldelningar](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Arkivera rapporter och loggar](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Administratör: Visa resurser som tilldelats användare

1. [Visa åtkomstpaket för en användare](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Visa resurstilldelningar för en användare](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Programmatisk administration

Du kan också hantera åtkomstpaket, kataloger, principer, begäranden och tilldelningar med hjälp av Microsoft Graph.  En användare i en lämplig roll med `EntitlementManagement.ReadWrite.All` ett program som har delegerad behörighet kan anropa [api:et för rättighetshantering](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

## <a name="next-steps"></a>Nästa steg

- [Delegering och roller](entitlement-management-delegate.md)
- [Begär process- och e-postaviseringar](entitlement-management-process.md)
