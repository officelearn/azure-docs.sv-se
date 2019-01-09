---
title: Så här konfigurerar du Postman för Azure Digital Twins | Microsoft Docs
description: Så här konfigurerar du Postman för Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: adgera
ms.openlocfilehash: 824c0caf0d54e8484093304c39c9f5dc05c83298
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117527"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Så här konfigurerar du Postman för Azure Digital Twins

Den här artikeln beskriver hur du konfigurerar Postman REST-klient för att interagera med och testa Azure Digital Twins Management API: erna. Mer specifikt beskrivs:

* Så här konfigurerar du ett Azure Active Directory-program att använda OAuth 2.0 implicit beviljande av flödet.
* Så här konfigurerar du Postman REST-klient för att göra token med HTTP-begäranden till din Management-API: er.

## <a name="postman-summary"></a>Sammanfattning av postman

Komma igång med Azure Digital Twins genom att använda ett REST-klientverktyg som [Postman](https://www.getpostman.com/) att förbereda miljön för lokal testning. Postman-klienten som hjälper dig att snabbt skapa komplexa HTTP-begäranden. Ladda ned skrivbordsversionen av Postman-klienten genom att gå till [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) är ett REST testning verktyget som hittar viktiga HTTP-begäran-funktioner i en användbar desktop och plugin-programmet-baserade GUI. Via Postman-klienten lösningar utvecklare kan ange vilken typ av HTTP-begäran (*POST*, *hämta*, *uppdatering*, *KORRIGERA*, och  *Ta bort*), API-slutpunkt för att anropa och användning av SSL. Postman stöder även att lägga till HTTP-begärans sidhuvud, parametrar, formulärdata och organ.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Konfigurera Azure Active Directory om du vill använda flödet för OAuth 2.0 implicit beviljande

Konfigurera din Azure Active Directory-app för att använda OAuth 2.0 implicit beviljande av flödet.

1. Följ stegen i [snabbstarten](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) att skapa ett Azure AD-program av typen intern. Eller du kan återanvända en befintlig inbyggd app-registrering.

1. Under **nödvändiga behörigheter**väljer **Lägg till** och ange **Azure Digital Twins** under **Lägg till API-åtkomst**. Om sökningen inte hittar API:et söker du efter **Azure Smart Spaces** i stället. Välj **bevilja behörigheter > delegerade behörigheter** och **klar**.

    ![Azure Active Directory app-registreringar Lägg till api](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

1. Klicka på **Manifest** att öppna programmanifestet för din app. Ange *oauth2AllowImplicitFlow* till `true`.

      ![Azure Active Directory implicit flöde][1]

1. Konfigurera en **svars-URL** till `https://www.getpostman.com/oauth2/callback`.

      ![Azure Active Directory svars-URL][2]

1. Kopiera och förvara den **program-ID** för din Azure Active Directory-app. Den används nedan.

### <a name="configure-the-postman-client"></a>Konfigurera Postman-klient

Nu ska installera och konfigurera Postman att skaffa en Azure Active Directory-token. Därefter gör en autentiserad HTTP-begäran i digitala Twins i Azure med hjälp av förvärvade token:

1. Gå till [www.getpostman.com]([https://www.getpostman.com/) att ladda ned appen.
1. Kontrollera att din **Auktoriseringswebbadressen** är korrekt. Det bör ta formatet:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Namn  | Ersätt med | Exempel |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Namnet på din klient eller organisation | `microsoft` |

1. Välj den **auktorisering** fliken **OAuth 2.0**, och välj sedan **hämta ny åtkomsttoken**.

    | Fält  | Värde |
    |---------|---------|
    | Beviljandetyp | `Implicit` |
    | Motringnings-URL | `https://www.getpostman.com/oauth2/callback` |
    | Auth URL | Använd den **Auktoriseringswebbadressen** från steg 2 ovan |
    | Klientorganisations-ID | Använd den **program-ID** för Azure Active Directory-app som har skapats eller ändrat syfte från föregående avsnitt |
    | Scope | Lämna tomt |
    | Status | Lämna tomt |
    | Klientautentisering | `Send as Basic Auth header` |

1. Klienten bör nu se ut som:

   ![Postman-klient, exempel][3]

1. Välj **begära Token**.

    >[!NOTE]
    >Om du får felmeddelandet ”Det gick inte att slutföra OAuth 2” kan du prova följande:
    > * Stäng Postman och öppna den igen och försök igen.
  
1. Bläddra nedåt och välj **Använd Token**.

## <a name="next-steps"></a>Nästa steg

- Mer om Digital Twins management API: er och hur de används finns [hur du använder Azure Digital Twins management API: er](how-to-navigate-apis.md).

- Läs om autentisering med API: er för Management [autentisera med API: er](./security-authenticating-apis.md). 



<!-- Images -->
[1]: media/how-to-configure-postman/implicit-flow.png
[2]: media/how-to-configure-postman/reply-url.png
[3]: media/how-to-configure-postman/postman-oauth-token.png
