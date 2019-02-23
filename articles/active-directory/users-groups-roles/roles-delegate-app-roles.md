---
title: Delegera program-administratörsroller – Azure Active Directory | Microsoft Docs
description: Hantering av programåtkomst delegera roller att bevilja behörigheter i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9409d196e307b75a14a272dee6c8c93789ca8556
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669796"
---
# <a name="delegate-app-administrator-roles-in-azure-active-directory"></a>Delegera app administratörsroller i Azure Active Directory

 Azure AD kan du delegera hantering av programåtkomst till en uppsättning inbyggda administrativa roller. Förutom minskar overhead för global administratör kan kan delegera särskild behörighet för att hantera uppgifter för åtkomst av programmet förbättra din säkerhetsposition och minska risken för obehörig åtkomst. Problem med delegering och allmänna riktlinjer diskuteras i [Delegera administration i Azure Active Directory](roles-concept-delegation.md).

## <a name="delegate-app-administration"></a>Delegera administration av app

Följande roller beviljar behörigheter att hantera programregistreringar, inställningar för enkel inloggning, användaren och grupptilldelningar och samtycker till att delegerade behörigheter och programbehörigheter (utom Microsoft Graph och Azure AD Graph). Den enda skillnaden är att en administratör för programmet även ger behörighet att hantera Application Proxy-inställningar. Varken rollen ger dig möjlighet att hantera inställningar för villkorlig åtkomst.
> [!IMPORTANT]
> Användare som är tilldelade den här rollen kan lägga till autentiseringsuppgifter i ett program och använda dessa autentiseringsuppgifter för att personifiera programmets identitet. Den här personifiering av programmets identitet kan vara en höjning av privilegier över vad användaren kan göra under sin andra rolltilldelningar i Azure AD. En användare som tilldelats den här rollen kan eventuellt skapa eller uppdatera användare eller andra objekt medan personifieras programmet.

Så här ger dig möjlighet att hantera åtkomsten till program i Azure portal:

1. Logga in på din [Azure AD-klient](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med ett konto som är berättigade till den globala administratörsrollen i klienten.
2. När du har tillräcklig behörighet kan öppna den [roller och administratörer sidan](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators).
3. Öppna en av följande roller för att se dess medlem tilldelningar:
  * **Programadministratör**
  * **Molnprogramadministratör**
4. På den **medlemmar** för rollen, väljer **Lägg till medlem**.
5. Välj en eller flera medlemmar som ska läggas till i rollen. <!--Members can be users or groups.-->

Du kan visa beskrivningen av de här rollerna i [tillgängliga roller](directory-assign-admin-roles.md#available-roles).

## <a name="delegate-app-registration"></a>Delegera appregistrering

Alla användare kan skapa programregistreringar som standard, men du bevilja vill behörighet att skapa programregistreringar eller behörighet att ge samtycke att auktorisera en app.

1. Logga in på din [Azure AD-klient](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med ett konto som är berättigade till den globala administratörsrollen i klienten.
2. När du har fått behörighet genom att ange en eller båda av följande:
  * På den [användaren inställningssidan för din klient](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)anger **användare kan registrera program** Nej.
  * På den [användarinställningar för företagsprogram](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)anger **användare kan samtycka till program som får åtkomst till företagsdata å deras vägnar** Nej.
3. Sedan tilldela användare som behöver den här behörigheten ska vara medlemmar i rollen tillämpning developer efter behov.

När en användare registrerar ett program, läggs de automatiskt som första ägare för programmet.

## <a name="delegate-app-ownership"></a>Delegera app ägarskap

Ägare för appen och appen registrering ägare kan varje hantera endast app-program eller app-registreringar som de äger. Till exempel när du lägger till en ägare till Salesforce-programmet kan som ägare hantera åtkomst till och konfiguration för Salesforce, men inte andra program. En app kan ha många ägare och en användare kan vara ägaren av många appar.

En programägaren kan:

* Ändra programegenskaper, till exempel namn och behörigheter för app-begäranden
* Hantera autentiseringsuppgifter
* Konfigurera enkel inloggning
* Tilldela användaråtkomst
* Lägga till eller ta bort andra ägare
* Redigera appmanifestet
* Publicera appen i app-galleriet

> [!IMPORTANT]
> Användare som är tilldelade den här rollen kan lägga till autentiseringsuppgifter i ett program och använda dessa autentiseringsuppgifter för att personifiera programmets identitet. Den här personifiering av programmets identitet kan vara en höjning av privilegier över vad användaren kan göra under sin andra rolltilldelningar i Azure AD. En användare som tilldelats den här rollen kan eventuellt skapa eller uppdatera användare eller andra objekt medan personifieras programmet.

Ägaren av en appregistrering kan visa och redigera appregistreringen.

<!-- ### To assign an enterprise app ownership role to a user

1. Sign in to your [Azure AD tenant](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) with an account that is the Global Administrator for the tenant.
2. On the [Roles and administrators page](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators), open one of the following roles to see its member assignments:
  * **Enterprise Application Owner**
  * **Application Registration Owner**
3. On the **Members** page for the role, select **Add member**.
4. Select one or more members to add to the role. -->

### <a name="to-assign-an-owner-to-an-application"></a>Tilldela en ägare till ett program

1. Logga in på din [Azure AD-klient](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med ett konto som är berättigade till programadministratör eller molnprogramadministratör för klienten.
2. På den [registreringar appsidan](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) för klienten, väljer du en app att öppna den **översikt** för appen.
3. Välj **ägare** att se listan över ägare för appen.
4. Välj **Lägg till** att välja en eller flera ägare att lägga till i appen.

### <a name="to-assign-an-owner-to-an-application-registration"></a>Att tilldela en ägare till en programregistrering

1. Logga in på din [Azure AD-klient](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med ett konto som är berättigade till programadministratör eller Cloud application administratörsrollen i klienten.
2. När du har tillräckliga behörigheter på den [företagsprogram sidan](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) för klienten, väljer du en appregistrering för att öppna den.
3. Välj **inställningar**.
4. Välj **ägare** på den **inställningar** sidan för att se en lista över ägare för appen.
5. Välj **Lägg till ägare** att välja en eller flera ägare att lägga till i appen.

## <a name="next-steps"></a>Nästa steg

* [Rollen referens för Azure AD-administratör](directory-assign-admin-roles.md)
