---
title: Bevilja administratörs medgivande för hela klienten till ett program – Azure AD
description: Lär dig hur du beviljar ett programs medgivande till ett program så att slutanvändare inte tillfrågas om medgivande vid inloggning till ett program.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: d31f03d8e6138829bbf57003b504f68f5c2315b2
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763371"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Bevilja administratörsmedgivande för hela klientorganisationen till ett program

Lär dig hur du fören klar användar upplevelsen genom att bevilja en klients administratörs medgivande till ett program. Den här artikeln innehåller olika sätt att åstadkomma detta. Metoderna gäller för alla slutanvändare i din Azure Active Directory (Azure AD)-klient.

Mer information om samtycker till program finns i [Azure Active Directory medgivande Framework](../develop/consent-framework.md).

## <a name="prerequisites"></a>Krav

För att bevilja administratörs tillåtelse för hela klienten måste du logga in som [Global administratör](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), en [program administratör](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)eller en [moln program administratör](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).

> [!IMPORTANT]
> När ett program har beviljats administratörs medgivande kan alla användare logga in på appen om den inte har kon figurer ATS för att kräva användar tilldelning. För att begränsa vilka användare som kan logga in i ett program, kräver användar tilldelning och tilldelar sedan användare eller grupper till programmet. Mer information finns i [metoder för att tilldela användare och grupper](methods-for-assigning-users-and-groups.md).

> [!WARNING]
> Om du beviljar administratörs medgivande för klient organisationer till ett program får appen och appens utgivare åtkomst till din organisations data. Noggrant granska de behörigheter som programmet begär innan medgivande beviljas.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Bevilja administratörs medgivande från Azure Portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Bevilja administrativt medgivande i företags program

Du kan bevilja klient organisationens godkännande genom *företags program* om programmet redan har etablerats i din klient organisation. En app kan till exempel tillhandahållas i din klient om minst en användare redan har samtyckt till programmet. Mer information finns i [hur och varför program läggs till i Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

Så här beviljar du ett administrativt medgivande till en app som anges i **företags program**:

1. Logga in på [Azure Portal](https://portal.azure.com) som [Global administratör](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [program administratör](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)eller en [moln program administratör](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Välj **Azure Active Directory** sedan **företags program**.
3. Välj det program som du vill bevilja klient organisationens administratörs medgivande till.
4. Välj **behörigheter** och klicka sedan på **bevilja administrativt medgivande**.
5. Granska de behörigheter som programmet kräver noggrant.
6. Om du samtycker till de behörigheter som krävs för programmet måste du bevilja medgivande. Annars klickar du på **Avbryt** eller Stäng fönstret.

### <a name="grant-admin-consent-in-app-registrations"></a>Bevilja administratörs medgivande i Appregistreringar

För program som din organisation har utvecklat, eller som har registrerats direkt i din Azure AD-klient, kan du även bevilja administratörs tillåtelse för hela klienten från **Appregistreringar** i Azure Portal.

Så här tilldelar du administratörs medgivande från klient organisationen från **Appregistreringar**:

1. Logga in på [Azure Portal](https://portal.azure.com) som [Global administratör](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [program administratör](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)eller en [moln program administratör](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Välj **Azure Active Directory** sedan **Appregistreringar**.
3. Välj det program som du vill bevilja klient organisationens administratörs medgivande till.
4. Välj **API-behörigheter** och klicka sedan på **bevilja administrativt medgivande**.
5. Granska de behörigheter som programmet kräver noggrant.
6. Om du samtycker till de behörigheter som krävs för programmet måste du bevilja medgivande. Annars klickar du på **Avbryt** eller Stäng fönstret.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Skapa URL: en för att bevilja administratörs tillåtelse för hela klienten

När du beviljar administratörs medgivande för hela klienten med hjälp av någon av metoderna som beskrivs ovan, öppnas ett fönster från Azure Portal för att begära att klient organisationens administratörs medgivande. Om du känner till klient-ID: t (även kallat program-ID) för programmet kan du bygga samma URL för att bevilja administratörs medgivande för hela klienten.

URL: en för hela klient organisationens administratörs medgivande följer följande format:

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

där:

* `{client-id}`är programmets klient-ID (även kallat app-ID).
* `{tenant-id}`är organisationens klient-ID eller ett verifierat domän namn.

Som alltid bör du noga granska de behörigheter som ett program begär innan medgivande beviljas.

## <a name="next-steps"></a>Nästa steg

[Konfigurera hur slutanvändare godkänner program](configure-user-consent.md)

[Konfigurera arbets flödet för administratörs medgivande](configure-admin-consent-workflow.md)

[Behörigheter och medgivande i Microsoft Identity Platform](../develop/active-directory-v2-scopes.md)

[Azure AD på StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
