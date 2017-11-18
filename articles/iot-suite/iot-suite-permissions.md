---
title: Azure IoT Suite och Azure Active Directory | Microsoft Docs
description: "Beskriver hur Azure IoT Suite använder Azure Active Directory för att hantera behörigheter."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: ee7acd393539277a5bc23a6fed40d07961974b9a
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Behörigheter på webbplatsen azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>Vad händer när du loggar in

Första gången du loggar in på [azureiotsuite.com][lnk-azureiotsuite], platsen avgör behörighetsnivåer baserat på de markerade Azure Active Directory (AAD)-klient och Azure-prenumeration.

1. Först för att fylla i listan över klienter som visas bredvid ditt användarnamn hittar webbplatsen från Azure vilka AAD-klienter som du tillhör. Platsen kan för närvarande kan endast hämta användartoken för en klient i taget. Därför när du växlar innehavare med den nedrullningsbara listrutan i det övre högra hörnet loggar platsen in till den klientorganisationen att hämta token för den klienten.

2. Därefter hittar webbplatsen från Azure vilka prenumerationer som du har associerat med den valda klientorganisationen. Du kan se tillgängliga prenumerationer när du skapar en ny förkonfigurerade lösning.

3. Slutligen hämtar platsen alla resurser i prenumerationer och resursgrupper märks med förkonfigurerade lösningar och fyller paneler på startsidan.

I följande avsnitt beskrivs de roller som styr åtkomsten till de förkonfigurerade lösningarna.

## <a name="aad-roles"></a>AAD-roller

AAD-roller styr möjligheten etablera förkonfigurerade lösningar och hantera användare i en förkonfigurerade lösning.

Du hittar mer information om administratörsroller i AAD i [Tilldela administratörsroller i Azure AD][lnk-aad-admin]. Den aktuella artikeln fokuserar på de **Global administratör** och **användaren** directory roller som används av förkonfigurerade lösningar.

### <a name="global-administrator"></a>Global administratör

Det kan finnas flera globala administratörer per AAD-klient:

* När du skapar en AAD-klient är du som standard global administratör för att klienten.
* Global administratör kan etablera en basic och standard förkonfigurerade lösningar.

### <a name="domain-user"></a>Domänanvändare

Det kan finnas många domänanvändare per AAD-klient:

* En domänanvändare kan etablera en grundläggande förkonfigurerade lösning via den [azureiotsuite.com] [ lnk-azureiotsuite] plats.
* En domänanvändare kan skapa en grundläggande förkonfigurerade lösning med hjälp av CLI.

### <a name="guest-user"></a>Gästanvändare

Det kan finnas många gästanvändare per AAD-klient. Gästanvändare har en begränsad uppsättning rättigheter i AAD-klient. Därför kan inte gästanvändare etablera en förkonfigurerade lösning i AAD-klient.

Mer information om användare och roller i AAD finns i följande resurser:

* [Skapa användare i Azure AD][lnk-create-edit-users]
* [Tilldela användare till appar][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Administratörsroller i Azure-prenumeration

Azure-administratörsroller styra möjlighet att mappa en Azure-prenumeration till en AD-klient.

Lär dig mer om Azure-administratörsroller i artikeln [lägga till eller ändra Medadministratör för Azure och tjänstadministratör kontoadministratör][lnk-admin-roles].

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Jag är en tjänstadministratör och jag skulle vilja ändra directory mappningen mellan min prenumeration och en specifik AAD-klient. Hur jag för att slutföra den här uppgiften?

Se [att lägga till en befintlig prenumeration i Azure AD-katalogen](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-add-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Jag vill ändra en tjänstadministratör eller en Medadministratör när du har loggat in med ett organisatoriska konto

Se support-artikeln [ändra tjänstadministratören och Medadministratör när du har loggat in med ett konto som organisatoriska][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Varför ser jag det här felet? ”Ditt konto har inte rätt behörighet för att skapa en lösning. Kontrollera med din kontoadministratör eller försök med ett annat konto ”.

Titta på följande diagram anvisningar:

![][img-flowchart]

> [!NOTE]
> Om du ser felet efter verifierar du är en global administratör på AAD-klient och en medadministratör för prenumerationen måste ha din kontoadministratör ta bort användaren och tilldela behörigheter som krävs i den här ordningen. Först lägga till användaren som global administratör och sedan lägga till användare som medadministratör på Azure-prenumerationen. Om problem kvarstår kontaktar du [hjälp och Support][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Varför ser jag det här felet när jag har en Azure-prenumeration? ”En Azure-prenumeration krävs för att skapa förkonfigurerade lösningar. Du kan skapa ett kostnadsfritt utvärderingskonto på bara några minuter ”.

Om du är säker på att du har en Azure-prenumeration, verifiera innehavaren mappning för din prenumeration och kontrollera att rätt klient väljs i listrutan. Om du har verifierats önskade innehavaren är korrekt, följ föregående diagram och verifiera mappningen för din prenumeration och AAD-klient.

## <a name="next-steps"></a>Nästa steg
Se hur du kan om du vill fortsätta lära dig mer om IoT Suite [anpassa förkonfigurerade lösning][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]: ../active-directory/active-directory-coreapps-assign-user-azure-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
