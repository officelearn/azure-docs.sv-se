---
title: 'Snabb start: Konfigurera en app för att exponera ett webb-API | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten får du lära dig hur du konfigurerar ett program för att exponera en ny behörighet/omfattning och roll för att göra programmet tillgängligt för klient program.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/05/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 93b0c3392a32a6ff18a285d34fdaede6ceea6528
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830299"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Snabb start: Konfigurera ett program för att exponera ett webb-API

Du kan utveckla ett webb-API och göra det tillgängligt för klientprogram genom att exponera [behörigheter/omfång](developer-glossary.md#scopes) och [roller](developer-glossary.md#roles). Ett korrekt konfigurerat webb-API tillhandahålls precis som de andra webb-API:erna från Microsoft, inklusive Graph API och Office 365-API:erna.

I den här snabb starten får du lära dig hur du konfigurerar ett program för att exponera ett nytt omfång för att göra det tillgängligt för klient program.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Slut för ande av [snabb start: registrera ett program med Microsoft Identity Platform](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Logga in på Azure-portalen och välj appen

Innan du kan konfigurera appen gör du följande:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** och väljer sedan **Appregistreringar**.
1. Leta reda på och välj den app du vill konfigurera. När du har valt appen ser du dess **översikt** eller huvudregistreringssida.
1. Välj vilken metod du vill använda, användargränssnitt eller applikationsmanifest, för att exponera ett nytt omfång:
    * [Exponera ett nytt omfång via användargränssnittet](#expose-a-new-scope-through-the-ui)
    * [Exponera ett nytt omfång eller en ny roll via applikationsmanifestet](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>Exponera ett nytt omfång via användargränssnittet

[![Visar hur du exponerar ett API med hjälp av användar gränssnittet](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

Så här exponerar du ett nytt omfång via användargränssnittet:

1. På appens **översiktssida** väljer du avsnittet **Exponera ett API**.

1. Välj **Lägg till omfång**.

1. Om du inte har angett **Program-ID-URI** visas en uppmaning att ange en. Ange din program-ID-URI eller en använd den som ges och välj sedan **Spara och fortsätt**.

1. När sidan **Lägg till omfång** visas anger du omfångets information:

    | Fält | Beskrivning |
    |-------|-------------|
    | **Namn på sökomfång** | Ange ett beskrivande namn på omfånget.<br><br>Till exempel `Employees.Read.All`. |
    | **Vem kan ge medgivande** | Välj om användare kan ge sitt medgivande för det här omfånget eller om administratörsmedgivande krävs. Välj **Endast administratörer** för behörigheter på högre nivå. |
    | **Visningsnamn för administratörsmedgivande** | Ge en beskrivning av omfånget, som administratörer kan se.<br><br>Till exempel `Read-only access to Employee records` |
    | **Beskrivning av administratörsmedgivande** | Ge en beskrivning av omfånget, som administratörer kan se.<br><br>Till exempel `Allow the application to have read-only access to all Employee data.` |

    Om användare kan ge sitt medgivande för omfånget lägger du även till värden för följande fält:

    | Fält | Beskrivning |
    |-------|-------------|
    | **Visningsnamn för användarmedgivande** | Ange ett beskrivande namn på omfånget, som användarna kan se.<br><br>Till exempel `Read-only access to your Employee records` |
    | **Beskrivning av användarmedgivande** | Ge en beskrivning av omfånget, som användarna kan se.<br><br>Till exempel `Allow the application to have read-only access to your Employee data.` |

1. Ange **Tillstånd** och välj **Lägg till omfång** när du är klar.

1. Valfritt Om du vill förhindra att användare av din app godkänner medgivande till de omfattningar som du har definierat, kan du "förauktorisera" klient programmet för att få åtkomst till ditt webb-API. Du bör för hands auktorisera *enbart* de klient program som du litar på eftersom användarna inte har möjlighet att avstå från medgivande.
    1. Under **auktoriserade klient program**väljer du **Lägg till ett klient program**
    1. Ange **program-ID** för det klient program som du vill förauktorisera. Till exempel för ett webb program som du har registrerat tidigare.
    1. Under **godkända omfattningar**väljer du de omfattningar som du vill utelämna medgivande frågor för och väljer sedan **Lägg till program**.

    Klient appen är nu en förauktoriserad klient app (PCA) och användarna uppmanas inte att ange medgivande när de loggar in på den.

1. Följ stegen för att [kontrollera att webb-API:et exponeras för andra appar](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Exponera ett nytt omfång eller en ny roll via applikationsmanifestet

Program manifestet fungerar som en mekanism för att uppdatera programentiteten som definierar attributen för en Azure AD App-registrering.

[![Exponera ett nytt omfång med oauth2Permissions-samlingen i manifestet](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

För att exponera ett nytt omfång genom att redigera applikations manifestet:

1. På appens **översiktssida** väljer du avsnittet **Manifest**. En webbaserad manifestredigerare öppnas så att du kan **Redigera** manifestet i portalen. Du kan också välja **Ladda ned** och redigera manifestet lokalt, och sedan använda **Ladda upp** för att tillämpa det på appen igen.

    I följande exempel visas hur du exponerar ett nytt omfång med namnet `Employees.Read.All` i resursen/API:et genom att lägga till följande JSON-element i `oauth2Permissions`-samlingen.

    Generera `id` värdet program mässigt eller med ett verktyg för GUID-generering som [guidgen](https://www.microsoft.com/download/details.aspx?id=55984).

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

1. När det är klart klickar du på **Spara**. Nu är ditt webb-API konfigurerat för användning av andra program i din katalog.
1. Följ stegen för att [kontrollera att webb-API:et exponeras för andra appar](#verify-the-web-api-is-exposed-to-other-applications).

Mer information om program entiteten och dess schema finns Microsoft Graph referens dokumentation för [program][ms-graph-application] resurs typ.

Mer information om applikations manifestet, inklusive dess schema referens, finns i [förstå Azure AD-appens manifest](reference-app-manifest.md).

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Verifiera att webb-API:et görs tillgängligt för andra appar

1. Gå tillbaka till Azure AD-klienten, Välj **Appregistreringar**och leta upp och välj det klient program som du vill konfigurera.
1. Upprepa stegen som beskrivs i [Konfigurera ett klient program för att få åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md).
1. När du kommer till steget för att [välja ett API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)väljer du din resurs (registreringen av webb-API-appen).
    * Om du har skapat webb-API-appens registrering med hjälp av Azure Portal, visas din API-resurs på fliken **Mina API: er** .
    * Om du tillåter att Visual Studio skapar registreringen av webb-API-appen när projektet skapas visas din API-resurs i **API: erna min organisation använder** fliken.

När du har valt webb-API-resursen bör du se det nya omfånget som är tillgängligt för klient behörighets begär Anden.

## <a name="using-the-exposed-scopes"></a>Använda de exponerade omfattningarna

När en klient har kon figurer ATS korrekt med behörighet att komma åt ditt webb-API, kan den utfärdas en OAuth 2,0-åtkomsttoken från Azure AD. När klienten anropar webb-API: et, visar den den åtkomsttoken som har scope ( `scp` )-anspråket inställt på de behörigheter som begärs i dess program registrering.

Du kan göra ytterligare omfång tillgängliga senare, vid behov. Tänk på att ditt webb-API kanske gör flera omfång tillgängliga som är associerade med en mängd olika funktioner. Din resurs kan styra åtkomsten till webb-API:et vid körning genom att utvärdera omfångsanspråken (`scp`) i den mottagna OAuth 2.0-åtkomsttoken.

I dina program är det fullständiga omfånget en sammanfogning av webb-API: ns **URI för program-ID** (resursen) och **omfångs namnet**.

Exempel: om webb-API: n för program-ID: t är `https://contoso.com/api` och ditt scopenamn är `Employees.Read.All` , är det fullständiga omfånget:

`https://contoso.com/api/Employees.Read.All`

## <a name="next-steps"></a>Nästa steg

Nu när du har exponerat ditt webb-API genom att konfigurera dess omfång konfigurerar du klient appens registrering med behörighet att komma åt dessa omfattningar.

> [!div class="nextstepaction"]
> [Konfigurera en app-registrering för webb-API-åtkomst](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
