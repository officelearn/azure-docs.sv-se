---
title: Azure IoT solution Accelerator och Azure Active Directory | Microsoft Docs
description: Beskriver hur Azure IoT-Lösningsacceleratorer använder Azure Active Directory för att hantera behörigheter.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: aa4e1d1f78549a8d1955def7a1c57e61d405e347
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297146"
---
# <a name="permissions-on-the-azureiotsolutionscom-site"></a>Behörigheter på webbplatsen azureiotsolutions.com

## <a name="what-happens-when-you-sign-in"></a>Vad händer när du loggar in

Första gången du loggar in på [azureiotsuite.com][lnk-azureiotsolutions], platsen avgör behörighetsnivåer baserat på de markerade Azure Active Directory (AAD)-klient och Azure-prenumeration.

1. Först för att fylla i listan över klienter som visas bredvid ditt användarnamn hittar webbplatsen från Azure vilka AAD-klienter som du tillhör. Platsen kan för närvarande kan endast hämta användartoken för en klient i taget. Därför när du växlar innehavare med den nedrullningsbara listrutan i det övre högra hörnet loggar platsen in till den klientorganisationen att hämta token för den klienten.

2. Därefter hittar webbplatsen från Azure vilka prenumerationer som du har associerat med den valda klientorganisationen. Du kan se tillgängliga prenumerationer när du skapar en ny solution accelerator.

3. Slutligen hämtar platsen alla resurser i prenumerationer och resursgrupper som solution Accelerator och fyller paneler på startsidan.

I följande avsnitt beskrivs de roller som styr åtkomsten till solution Accelerator.

## <a name="aad-roles"></a>AAD-roller

AAD-roller styra möjlighet att etablera solution Accelerator för att hantera användare och vissa Azure-tjänster i en lösningsaccelerator.

Du hittar mer information om administratörsroller i AAD i [Tilldela administratörsroller i Azure AD][lnk-aad-admin]. Den aktuella artikeln fokuserar på de **Global administratör** och **användaren** directory roller som används av solution Accelerator.

### <a name="global-administrator"></a>Global administratör

Det kan finnas flera globala administratörer per AAD-klient:

* När du skapar en AAD-klient är du som standard global administratör för att klienten.
* Global administratör kan etablera en för basic och standard Lösningsacceleratorer (en grundläggande distribution med en enda virtuell Azure-dator).

### <a name="domain-user"></a>Domänanvändare

Det kan finnas många domänanvändare per AAD-klient:

* En domänanvändare kan etablera en grundläggande lösning för accelerator via den [azureiotsolutions.com] [ lnk-azureiotsolutions] plats.
* En domänanvändare kan skapa en grundläggande lösning för accelerator med hjälp av CLI.

### <a name="guest-user"></a>Gästanvändare

Det kan finnas många gästanvändare per AAD-klient. Gästanvändare har en begränsad uppsättning rättigheter i AAD-klient. Därför kan inte gästanvändare etablera en lösningsaccelerator i AAD-klient.

Mer information om användare och roller i AAD finns i följande resurser:

* [Skapa användare i Azure AD][lnk-create-edit-users]
* [Tilldela användare till appar][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Administratörsroller i Azure-prenumeration

Azure-administratörsroller styra möjlighet att mappa en Azure-prenumeration till en AAD-klient.

Lär dig mer om Azure-administratörsroller i artikeln [lägga till eller ändra Medadministratör för Azure och tjänstadministratör kontoadministratör][lnk-admin-roles].

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Jag är en tjänstadministratör och jag skulle vilja ändra directory mappningen mellan min prenumeration och en specifik AAD-klient. Hur jag för att slutföra den här uppgiften?

Se [att lägga till en befintlig prenumeration i Azure AD-katalogen](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Jag vill ändra en tjänstadministratör eller en Medadministratör när du har loggat in med ett organisationskonto

Se support-artikeln [ändra tjänstadministratören och Medadministratör när du har loggat in med ett organisationskonto][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Varför ser jag det här felet? ”Ditt konto har inte rätt behörighet för att skapa en lösning. Kontrollera med din kontoadministratör eller försök med ett annat konto ”.

Titta på följande diagram anvisningar:

![][img-flowchart]

> [!NOTE]
> Om du ser felet efter verifierar du är global administratör för AAD-klient och en medadministratör för prenumerationen måste ha din kontoadministratör ta bort användaren och tilldela behörigheter som krävs i den här ordningen. Först lägga till användaren som global administratör och sedan lägga till användaren som en medadministratör för Azure-prenumerationen. Om problem kvarstår kontaktar du [hjälp och Support][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Varför ser jag det här felet när jag har en Azure-prenumeration? ”En Azure-prenumeration krävs för att skapa förkonfigurerade lösningar. Du kan skapa ett kostnadsfritt utvärderingskonto på bara några minuter ”.

Om du är säker på att du har en Azure-prenumeration, verifiera innehavaren mappning för din prenumeration och kontrollera att rätt klient väljs i listrutan. Om du har verifierats önskade innehavaren är korrekt, följ föregående diagram och verifiera mappningen för din prenumeration och AAD-klient.

## <a name="next-steps"></a>Nästa steg
Se hur du kan om du vill fortsätta lära dig mer om IoT solution Accelerator [anpassa en lösningsaccelerator][lnk-customize].

[img-flowchart]: media/iot-accelerators-permissions/flowchart.png

[lnk-azureiotsolutions]: https://www.azureiotsolutions.com
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles-azure-portal.md
[lnk-portal]: https://portal.azure.com
[lnk-create-edit-users]: ../active-directory/active-directory-users-profile-azure-portal.md
[lnk-assign-app-roles]:../active-directory/manage-apps/assign-user-or-group-access-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-accelerators-remote-monitoring-customize.md
