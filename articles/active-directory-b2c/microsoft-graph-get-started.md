---
title: Hantera resurser med Microsoft Graph
titleSuffix: Azure AD B2C
description: Förbered för hantering av Azure AD B2C-resurser med Microsoft Graph genom att registrera ett program som har beviljat de nödvändiga Graph API-behörigheterna.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 32117d4bfcf0c0af94eced095b94ab0c1b6f88af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184371"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Hantera Azure AD B2C med Microsoft Graph

[Med Microsoft Graph][ms-graph] kan du hantera många av resurserna i din Azure AD B2C-klientorganisation, inklusive kundanvändarkonton och anpassade principer. Genom att skriva skript eller program som anropar [Microsoft Graph API][ms-graph-api]kan du automatisera klienthanteringsuppgifter som:

* Migrera ett befintligt användararkiv till en Azure AD B2C-klient
* Distribuera anpassade principer med en Azure Pipeline i Azure DevOps och hantera anpassade principnycklar
* Värd för användarregistrering på din egen sida och skapa användarkonton i din Azure AD B2C-katalog bakom kulisserna
* Automatisera registrering av program
* Hämta granskningsloggar

Följande avsnitt hjälper dig att förbereda dig för att använda Microsoft Graph API för att automatisera hanteringen av resurser i din Azure AD B2C-katalog.

## <a name="microsoft-graph-api-interaction-modes"></a>Interaktionslägen för Microsoft Graph API

Det finns två kommunikationslägen som du kan använda när du arbetar med Microsoft Graph API för att hantera resurser i din Azure AD B2C-klient:

* **Interaktiv** - Lämplig för körning en gång uppgifter, du använder ett administratörskonto i B2C-klienten för att utföra hanteringsuppgifter. Det här läget kräver att en administratör loggar in med sina autentiseringsuppgifter innan han anropar Microsoft Graph API.

* **Automatiserad** - För schemalagda eller kontinuerligt köra uppgifter använder den här metoden ett tjänstkonto som du konfigurerar med de behörigheter som krävs för att utföra hanteringsuppgifter. Du skapar "tjänstkontot" i Azure AD B2C genom att registrera ett program som dina program och skript använder för att autentisera med hjälp av dess *program -(klient)-ID* och bevilja beviljaR OAuth 2.0-klientautentiseringsuppgifter. I det här fallet fungerar programmet som sig själv för att anropa Microsoft Graph API, inte administratörsanvändaren som i den tidigare beskrivna interaktiva metoden.

Du aktiverar **scenariot Automatiserad** interaktion genom att skapa en programregistrering som visas i följande avsnitt.

## <a name="register-management-application"></a>Registrera hanteringsprogram

Innan dina skript och program kan interagera med [Microsoft Graph API][ms-graph-api] för att hantera Azure AD B2C-resurser måste du skapa en programregistrering i din Azure AD B2C-klient som ger de nödvändiga API-behörigheterna.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-api-access"></a>Bevilja API-åtkomst

Bevilja sedan de registrerade programbehörigheterna för att manipulera klientresurser via anrop till Microsoft Graph API.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Skapa klienthemlighet

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Du har nu ett program som har behörighet att *skapa,* *läsa,* *uppdatera*och *ta bort* användare i din Azure AD B2C-klient. Fortsätt till nästa avsnitt om du vill lägga till behörigheter *för lösenordsuppdatering.*

## <a name="enable-user-delete-and-password-update"></a>Aktivera uppdatering av användarborttagning och lösenord

Behörigheten *Läs- och skrivkatalogdata* innehåller **INTE** möjligheten att ta bort användare eller uppdatera lösenord för användarkonton.

Om ditt program eller skript behöver ta bort användare eller uppdatera deras lösenord tilldelar du rollen *Användaradministratör* till ditt program:

1. Logga in på [Azure-portalen](https://portal.azure.com) och använd **filtret Katalog + Prenumeration** för att växla till din Azure AD B2C-klient.
1. Sök efter och välj **Azure AD B2C**.
1. Under **Hantera**väljer du **Roller och administratörer**.
1. Välj rollen **Användaradministratör.**
1. Välj **Lägg till tilldelningar**.
1. I textrutan **Välj** anger du namnet på det program som du registrerade tidigare, till exempel *managementapp1*. Välj ditt program när det visas i sökresultaten.
1. Välj **Lägg till**. Det kan ta några minuter innan behörigheterna sprids helt.

## <a name="next-steps"></a>Nästa steg

Nu när du har registrerat ditt hanteringsprogram och har beviljat det nödvändiga behörigheterna, kan dina program och tjänster (till exempel Azure Pipelines) använda sina autentiseringsuppgifter och behörigheter för att interagera med Microsoft Graph API.

* [B2C-åtgärder som stöds av Microsoft Graph](microsoft-graph-operations.md)
* [Hantera Azure AD B2C-användarkonton med Microsoft Graph](manage-user-accounts-graph-api.md)
* [Hämta granskningsloggar med Azure AD-rapporterings-API:et](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview
