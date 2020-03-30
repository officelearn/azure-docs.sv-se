---
title: Bevilja administratör för klient hela till ett program - Azure AD
description: Läs om hur du ger klientomfattande samtycke till ett program så att slutanvändarna inte uppmanas att godkänna när de loggar in på ett program.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: c515fef4997720435c64bd5f3ae7b18f8921fc5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480923"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Bevilja administratör för hela klienten till ett program

Lär dig hur du förenklar användarupplevelsen genom att bevilja administratörsgodkännande för hela klienten till ett program. Denna artikel ger olika sätt att uppnå detta. Metoderna gäller för alla slutanvändare i din Azure Active Directory -klientorganisation (Azure AD).

Mer information om godkännande till program finns i [Azure Active Directory consent framework](../develop/consent-framework.md).

## <a name="prerequisites"></a>Krav

För att ge administratörsgodkännande för hela klienten måste du logga in som [global administratör,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [programadministratör](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)eller [molnprogramadministratör](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).

> [!IMPORTANT]
> När ett program har beviljats administratörsmedgivande för hela klienten kan alla användare logga in på appen om det inte har konfigurerats för att kräva användartilldelning. Om du vill begränsa vilka användare som kan logga in på ett program kräver du användartilldelning och tilldelar sedan användare eller grupper till programmet. Mer information finns i [Metoder för att tilldela användare och grupper](methods-for-assigning-users-and-groups.md).

> [!WARNING]
> Genom att bevilja administratör för hela klienten medgivande till ett program får appen och appens utgivare åtkomst till organisationens data. Granska noggrant de behörigheter som ansökan begär innan du beviljar samtycke.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Bevilja administratörsmedgivande från Azure-portalen

### <a name="grant-admin-consent-in-enterprise-apps"></a>Bevilja administratörsmedgivande i Enterprise-appar

Du kan bevilja administratörsmedgivande för hela klienten via *Enterprise-program* om programmet redan har etablerats i din klientorganisation. En app kan till exempel etableras i din klientorganisation om minst en användare redan har samtyckt till programmet. Mer information finns i [Så här läggs program till i Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

Så här beviljar du administratör för klient hela 2018 till en app som visas i **Enterprise-program:**

1. Logga in på [Azure-portalen](https://portal.azure.com) som [global administratör,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [programadministratör](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)eller [molnprogramadministratör](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Välj **Azure Active Directory** och sedan **Företagsprogram**.
3. Välj det program som du vill bevilja administratörsmedgivande för hela klienten.
4. Välj **Behörigheter** och klicka sedan på **Bevilja administratörsmedgivande**.
5. Granska noggrant de behörigheter som programmet kräver.
6. Om du godkänner de behörigheter som ansökan kräver, bevilja samtycke. Om inte klickar du på **Avbryt** eller stänger fönstret.

### <a name="grant-admin-consent-in-app-registrations"></a>Bevilja administratörsmedgivande i appregistreringar

För program som din organisation har utvecklat, eller som är registrerade direkt i din Azure AD-klientorganisation, kan du också bevilja administratörsmedgivande för klienter från **appregistreringar** i Azure-portalen.

Så här beviljar du administratörsgodkännande för hela klienten från **appregistreringar:**

1. Logga in på [Azure-portalen](https://portal.azure.com) som [global administratör,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [programadministratör](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)eller [molnprogramadministratör](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Välj **Azure Active Directory** och sedan **Appregistreringar**.
3. Välj det program som du vill bevilja administratörsmedgivande för hela klienten.
4. Välj **API-behörigheter** och klicka sedan på **Bevilja administratörsmedgivande**.
5. Granska noggrant de behörigheter som programmet kräver.
6. Om du godkänner de behörigheter som ansökan kräver, bevilja samtycke. Om inte klickar du på **Avbryt** eller stänger fönstret.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Skapa URL:en för att bevilja administratörsmedgivande för hela klienten

När du beviljar administratörsgodkännande för hela klienten med någon av metoderna som beskrivs ovan öppnas ett fönster från Azure-portalen för att fråga efter administratörsmedgivande för hela klienten. Om du känner till klient-ID (kallas även program-ID) för programmet kan du skapa samma URL för att bevilja administratörsmedgivande för hela klienten.

Url:en för administratörer för hela klienten följer följande format:

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

där:

* `{client-id}`är programmets klient-ID (kallas även app-ID).
* `{tenant-id}`är organisationens klient-ID eller något verifierat domännamn.

Som alltid, noggrant granska de behörigheter en ansökan begär innan du beviljar samtycke.

## <a name="next-steps"></a>Nästa steg

[Konfigurera hur slutanvändare samtycker till program](configure-user-consent.md)

[Konfigurera arbetsflödet för administratörsgodkännande](configure-admin-consent-workflow.md)

[Behörigheter och medgivande på Microsofts identitetsplattform](../develop/active-directory-v2-scopes.md)

[Azure AD på StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
