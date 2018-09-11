---
title: Azure IoT-Lösningsacceleratorer och Azure Active Directory | Microsoft Docs
description: Beskriver hur Azure IoT-Lösningsacceleratorer använder Azure Active Directory för att hantera behörigheter.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: c88ae933360b5040ad3d2b877041049512f08b0d
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303412"
---
# <a name="permissions-on-the-azureiotsolutionscom-site"></a>Behörigheter på webbplatsen azureiotsolutions.com

## <a name="what-happens-when-you-sign-in"></a>Vad händer när du loggar in

Första gången du loggar in på [azureiotsuite.com][lnk-azureiotsolutions], platsen avgör behörighetsnivåer baserat på de valda Azure Active Directory (AAD)-klient och Azure-prenumeration.

1. Först för att fylla i listan över klienter som visas bredvid ditt användarnamn, hittar webbplatsen från Azure vilka AAD-klienter som du tillhör. Platsen kan för närvarande kan bara hämta användartoken för en klient i taget. Därför när du växlar klienter med hjälp av listrutan i det övre högra hörnet loggar platsen du i den klienten att hämta token för den klienten.

2. Sedan hittar platsen från Azure vilka prenumerationer som du har associerat med den valda klientorganisationen. Du kan se tillgängliga prenumerationer när du skapar en ny lösningsaccelerator.

3. Slutligen hämtar platsen alla resurserna i prenumerationer och resursgrupper märkts med Lösningsacceleratorer och fyller på panelerna på startsidan.

I följande avsnitt beskrivs de roller som styr åtkomsten till i lösningsacceleratorerna.

## <a name="aad-roles"></a>AAD-roller

AAD-roller styra möjligheten att etablera Lösningsacceleratorer, att hantera användare och vissa Azure-tjänster i en lösningsaccelerator.

Du hittar mer information om administratörsroller i AAD i [Tilldela administratörsroller i Azure AD][lnk-aad-admin]. Den aktuella artikeln fokuserar på de **Global administratör** och **användaren** katalogroller som används av Lösningsacceleratorer.

### <a name="global-administrator"></a>Global administratör

Det kan finnas många globala administratörer per AAD-klient:

* När du skapar en AAD-klient, är du som standard global administratör för klienten.
* Global administratör kan etablera en grundläggande och standard Lösningsacceleratorer (en grundläggande distribution använder en enda Azure-dator).

### <a name="domain-user"></a>Domänanvändare

Det kan finnas många domänanvändare per AAD-klient:

* En domänanvändare kan etablera en grundläggande lösningsaccelerator via den [azureiotsolutions.com] [ lnk-azureiotsolutions] plats.
* En domänanvändare kan skapa en grundläggande lösningsaccelerator med hjälp av CLI.

### <a name="guest-user"></a>Gästanvändare

Det kan finnas många gästanvändare per AAD-klient. Gästanvändare har en begränsad uppsättning rättigheter i AAD-klient. Gästanvändare kan därför kan inte etablera en lösningsaccelerator i AAD-klient.

Mer information om användare och roller i AAD finns i följande resurser:

* [Skapa användare i Azure AD][lnk-create-edit-users]
* [Tilldela användare till appar][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Administratörsroller i Azure-prenumeration

Azure-administratörsroller styra möjligheten att mappa en Azure-prenumeration till en AAD-klient.

Lär dig mer om Azure administratörsroller i artikeln [Lägg till eller ändra Azure-prenumerationsadministratörer][lnk-admin-roles].

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Jag är en tjänstadministratör och jag skulle vilja ändra directory mappningen mellan min prenumeration och en specifik AAD-klient. Hur jag för att slutföra den här uppgiften?

Se [att lägga till en befintlig prenumeration i Azure AD-katalogen](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Jag vill ändra en tjänstadministratör eller delad administratör när du har loggat in med ett organisationskonto

Se supportartikeln [ändra tjänstadministratör och delad administratör när du har loggat in med ett organisationskonto][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Varför ser jag det här felet? ”Ditt konto har inte behörighet att skapa en lösning. Kontrollera kontoadministratören eller försök med ett annat konto ”.

Titta på diagrammet nedan anvisningar:

![][img-flowchart]

> [!NOTE]
> Om du ser felet när du har validerat du är global administratör för AAD-klient och en medadministratör för prenumerationen, har din kontoadministratör tar bort användare och tilldela nödvändiga behörigheter i den här ordningen. Först lägger du till användaren som global administratör och Lägg sedan till användaren som en medadministratör för Azure-prenumerationen. Om problemen kvarstår, kontakta [hjälp och Support][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Varför ser jag det här felet när jag har en Azure-prenumeration? ”En Azure-prenumeration krävs för att skapa förkonfigurerade lösningar. Du kan skapa ett kostnadsfritt utvärderingskonto på bara några minuter ”.

Om du är säker på att du har en Azure-prenumeration Validera klientmappning för din prenumeration och se till att rätt klient har valts i listrutan. Om du har verifierat den önskade klienten är korrekt, följer du föregående diagram och verifiera mappningen av din prenumeration och AAD-klient.

## <a name="next-steps"></a>Nästa steg
Om du vill lära dig mer om IoT-Lösningsacceleratorer, se hur du [anpassar en lösningsaccelerator][lnk-customize].

[img-flowchart]: media/iot-accelerators-permissions/flowchart.png

[lnk-azureiotsolutions]: https://www.azureiotsolutions.com
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]:../active-directory/users-groups-roles/directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com
[lnk-create-edit-users]:../active-directory/fundamentals/active-directory-users-profile-azure-portal.md
[lnk-assign-app-roles]:../active-directory/manage-apps/assign-user-or-group-access-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-accelerators-remote-monitoring-customize.md
