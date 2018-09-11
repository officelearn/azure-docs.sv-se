---
title: Azure IoT Suite och Azure Active Directory | Microsoft Docs
description: Beskriver hur Azure IoT Suite använder Azure Active Directory för att hantera behörigheter.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: a56d535ee06a097c7c18bcd507c25708f6a33f91
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296932"
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Behörigheter på webbplatsen azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>Vad händer när du loggar in

Första gången du loggar in på [azureiotsuite.com][lnk-azureiotsuite], platsen avgör behörighetsnivåer baserat på de valda Azure Active Directory (AAD)-klient och Azure-prenumeration.

1. Först för att fylla i listan över klienter som visas bredvid ditt användarnamn, hittar webbplatsen från Azure vilka AAD-klienter som du tillhör. Platsen kan för närvarande kan bara hämta användartoken för en klient i taget. Därför när du växlar klienter med hjälp av listrutan i det övre högra hörnet loggar platsen du i den klienten att hämta token för den klienten.

2. Sedan hittar platsen från Azure vilka prenumerationer som du har associerat med den valda klientorganisationen. Du kan se tillgängliga prenumerationer när du skapar en ny förkonfigurerad lösning.

3. Slutligen hämtar platsen alla resurserna i prenumerationer och resursgrupper märkts med förkonfigurerade lösningar och fyller på panelerna på startsidan.

I följande avsnitt beskrivs de roller som styr åtkomsten till de förkonfigurerade lösningarna.

## <a name="aad-roles"></a>AAD-roller

AAD-roller styra möjligheten etablera förkonfigurerade lösningar och hantera användare i en förkonfigurerad lösning.

Du hittar mer information om administratörsroller i AAD i [Tilldela administratörsroller i Azure AD][lnk-aad-admin]. Den aktuella artikeln fokuserar på de **Global administratör** och **användaren** katalogroller som används av de förkonfigurerade lösningarna.

### <a name="global-administrator"></a>Global administratör

Det kan finnas många globala administratörer per AAD-klient:

* När du skapar en AAD-klient, är du som standard global administratör för klienten.
* Global administratör kan etablera en förkonfigurerad lösning och har tilldelats en **Admin** för programmet i sin AAD-klient.
* Om en annan användare i samma AAD-klientorganisation skapar ett program, är den standardroll som tilldelas den globala administratören **ReadOnly**.
* En global administratör kan tilldela användare till roller för program som använder den [Azure-portalen][lnk-portal].

### <a name="domain-user"></a>Domänanvändare

Det kan finnas många domänanvändare per AAD-klient:

* En domänanvändare kan etablera en förkonfigurerad lösning via den [azureiotsuite.com] [ lnk-azureiotsuite] plats. Domänanvändare ges som standard den **Admin** roll i programmet etablerade.
* En domänanvändare kan skapa ett program med hjälp av build.cmd skriptet i den [azure-iot-remote-monitoring][lnk-rm-github-repo], [azure-iot-predictive-maintenance] [ lnk-pm-github-repo], eller [azure-iot-connected-factory] [ lnk-cf-github-repo] lagringsplats. Men det är den standardroll som tilldelas domänanvändaren **ReadOnly**, eftersom en domänanvändare inte har behörighet att tilldela roller.
* Om en annan användare i AAD-klient skapar ett program, domänanvändaren tilldelas den **ReadOnly** roll som standard för programmet.
* En domänanvändare kan inte tilldela roller för program. därför en domänanvändare kan inte lägga till användare eller roller för användare för ett program även om de har etablerat den.

### <a name="guest-user"></a>Gästanvändare

Det kan finnas många gästanvändare per AAD-klient. Gästanvändare har en begränsad uppsättning rättigheter i AAD-klient. Gästanvändare kan därför kan inte etablera en förkonfigurerad lösning på AAD-klient.

Mer information om användare och roller i AAD finns i följande resurser:

* [Skapa användare i Azure AD][lnk-create-edit-users]
* [Tilldela användare till appar][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Administratörsroller i Azure-prenumeration

Azure-administratörsroller styra möjligheten att mappa en Azure-prenumeration till en AD-klient.

Lär dig mer om Azure administratörsroller i artikeln [Lägg till eller ändra Azure-prenumerationsadministratörer][lnk-admin-roles].

## <a name="application-roles"></a>Programroller

Programroller styra åtkomsten till enheter i din förkonfigurerade lösning.

Det finns två definierade roller och en implicit roll som definierats i ett etablerat program:

* **Administratör:** har fullständig behörighet att lägga till, hantera, ta bort enheter och ändra inställningarna.
* **Skrivskyddad:** kan visa enheter, regler, åtgärder, jobb och telemetri.

Du kan hitta de behörigheter som tilldelats varje roll i den [RolePermissions.cs] [ lnk-resource-cs] källfilen.

### <a name="changing-application-roles-for-a-user"></a>Ändra programroller för en användare

Du kan använda följande procedur för att göra en användare i Active Directory administratör för din förkonfigurerade lösning.

Du måste vara en global AAD-administratören att ändra roller för en användare:

1. Gå till [Azure Portal][lnk-portal].
2. Välj **Azure Active Directory**.
3. Kontrollera att du använder den katalog som du har valt på azureiotsuite.com när du har etablerat din lösning. Om du har flera kataloger som är associerad med din prenumeration kan växla du mellan dem om du klickar på namnet på ditt konto på upp till höger i portalen.
4. Klicka på **företagsprogram**, sedan **alla program**.
4. Visa **alla program** med **alla** status. Sök sedan efter ett program med namnet på din förkonfigurerade lösning.
5. Klicka på namnet på det program som matchar Lösningsnamnet på din förkonfigurerade.
6. Klicka på **Användare och grupper**.
7. Välj den användare som du vill växla roller.
8. Klicka på **tilldela** och välj rollen (till exempel **Admin**) som ska tilldelas till användaren, klicka på kryssmarkeringen.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Jag är en tjänstadministratör och jag skulle vilja ändra directory mappningen mellan min prenumeration och en specifik AAD-klient. Hur jag för att slutföra den här uppgiften?

Se [att lägga till en befintlig prenumeration i Azure AD-katalogen](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Jag är användaren/domänmedlem på AAD-klient och jag har skapat en förkonfigurerad lösning. Hur jag tilldelas en roll för mitt program?

Be en global administratör för att göra en global administratör på AAD-klient och sedan tilldela roller till användare själv. Du kan också be en global administratör för att tilldela dig en roll direkt. Om du vill ändra din förkonfigurerade lösning för AAD-klient har distribuerats finns i nästa fråga.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Hur växlar jag min förkonfigurerade lösningen för fjärrövervakning och programmet har tilldelats AAD-klientorganisationen?

Du kan köra en molndistribution från <https://github.com/Azure/azure-iot-remote-monitoring> och distribuera igen med en nyligen skapade AAD-klient. Eftersom du är som standard en global administratör när du skapar en AAD-klient har behörighet att lägga till användare och tilldela roller till användare.

1. Skapa en AAD-katalog i den [Azure-portalen][lnk-portal].
2. Gå till <https://github.com/Azure/azure-iot-remote-monitoring>.
3. Kör `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (till exempel `build.cmd cloud debug myRMSolution`)
4. När du uppmanas, ange den **tenantid** ska vara din nya klient i stället för din tidigare klient.

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Jag vill ändra en tjänstadministratör eller delad administratör när du har loggat in med ett organisationskonto

Se supportartikeln [ändra tjänstadministratör och delad administratör när du har loggat in med ett organisationskonto][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Varför ser jag det här felet? ”Ditt konto har inte behörighet att skapa en lösning. Kontrollera kontoadministratören eller försök med ett annat konto ”.

Titta på diagrammet nedan anvisningar:

![][img-flowchart]

> [!NOTE]
> Om du ser felet när du har validerat du är en global administratör på AAD-klient och en medadministratör för prenumerationen, har din kontoadministratör tar bort användare och tilldela nödvändiga behörigheter i den här ordningen. Först lägger du till användaren som global administratör och Lägg sedan till användaren som en medadministratör för Azure-prenumeration. Om problemen kvarstår, kontakta [hjälp och Support][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Varför ser jag det här felet när jag har en Azure-prenumeration? ”En Azure-prenumeration krävs för att skapa förkonfigurerade lösningar. Du kan skapa ett kostnadsfritt utvärderingskonto på bara några minuter ”.

Om du är säker på att du har en Azure-prenumeration Validera klientmappning för din prenumeration och se till att rätt klient har valts i listrutan. Om du har verifierat den önskade klienten är korrekt, följer du föregående diagram och verifiera mappningen av din prenumeration och AAD-klient.

## <a name="next-steps"></a>Nästa steg
Om du vill lära dig mer om IoT Suite, se hur du [anpassa en förkonfigurerad lösning][lnk-customize].

[img-flowchart]: media/iot-suite-v1-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]:../active-directory/manage-apps/assign-user-or-group-access-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
