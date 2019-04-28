---
title: Så här konfigurerar du Postman för Azure Digital Twins | Microsoft Docs
description: Så här konfigurerar du Postman för Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: adgera
ms.openlocfilehash: 49b073952b0923b940204b19680dcc9a1ffa44b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926200"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Så här konfigurerar du Postman för Azure Digital Twins

Den här artikeln beskriver hur du konfigurerar Postman REST-klient för att interagera med och testa Azure Digital Twins Management API: erna. Mer specifikt beskrivs:

* Så här konfigurerar du ett Azure Active Directory-program att använda OAuth 2.0 implicit beviljande av flödet.
* Hur du använder Postman REST-klient för att token med HTTP-begäranden till din Management-API: er.
* Hur du använder Postman för att göra flera delar POST-förfrågningar till din Management-API: er.

## <a name="postman-summary"></a>Sammanfattning av postman

Komma igång med Azure Digital Twins genom att använda ett REST-klientverktyg som [Postman](https://www.getpostman.com/) att förbereda miljön för lokal testning. Postman-klienten som hjälper dig att snabbt skapa komplexa HTTP-begäranden. Ladda ned skrivbordsversionen av Postman-klienten genom att gå till [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) är ett REST testning verktyget som hittar viktiga HTTP-begäran-funktioner i en användbar desktop och plugin-programmet-baserade GUI. 

Via Postman-klienten lösningar utvecklare kan ange vilken typ av HTTP-begäran (*POST*, *hämta*, *uppdatering*, *KORRIGERA*, och  *Ta bort*), API-slutpunkt för att anropa och användning av SSL. Postman stöder även att lägga till HTTP-begärans sidhuvud, parametrar, formulärdata och organ.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Konfigurera Azure Active Directory om du vill använda flödet för OAuth 2.0 implicit beviljande

Konfigurera din Azure Active Directory-app för att använda OAuth 2.0 implicit beviljande av flödet.

1. Följ stegen i [snabbstarten](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) att skapa ett Azure AD-program av typen intern. Eller du kan återanvända en befintlig inbyggd app-registrering.

1. Under **nödvändiga behörigheter**väljer **Lägg till** och ange **Azure Digital Twins** under **Lägg till API-åtkomst**. Om sökningen inte hittar API:et söker du efter **Azure Smart Spaces** i stället. Välj **bevilja behörigheter > delegerade behörigheter** och **klar**.

    ![Azure Active Directory app-registreringar Lägg till api](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

1. Klicka på **Manifest** att öppna programmanifestet för din app. Ange *oauth2AllowImplicitFlow* till `true`.

      ![Azure Active Directory implicit flöde][1]

1. Konfigurera en **svars-URL** till `https://www.getpostman.com/oauth2/callback`.

      ![Azure Active Directory svars-URL][2]

1. Kopiera och förvara den **program-ID** för din Azure Active Directory-app. Den används i stegen nedan.

## <a name="obtain-an-oauth-20-token"></a>Hämta en OAuth 2.0-token

Nu ska installera och konfigurera Postman att skaffa en Azure Active Directory-token. Därefter gör en autentiserad HTTP-begäran i digitala Twins i Azure med hjälp av förvärvade token:

1. Gå till [www.getpostman.com](https://www.getpostman.com/) att ladda ned appen.
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
    | Auth URL | Använd den **Auktoriseringswebbadressen** från steg 2 |
    | Klientorganisations-ID | Använd den **program-ID** för Azure Active Directory-app som har skapats eller ändrat syfte från föregående avsnitt |
    | Scope | Lämna tomt |
    | Status | Lämna tomt |
    | Klientautentisering | `Send as Basic Auth header` |

1. Klienten bör nu visas som:

   ![Postman-klient, exempel][3]

1. Välj **begära Token**.

    >[!TIP]
    >Om du får felmeddelandet ”Det gick inte att slutföra OAuth 2” kan du prova följande:
    > * Stäng Postman och öppna den igen och försök igen.
  
1. Bläddra nedåt och välj **Använd Token**.

<div id="multi"></div>

## <a name="make-a-multipart-post-request"></a>Utföra en multipart POST-begäran

Konfigurera Postman för att göra en autentiserad HTTP multipart POST-begäran när du har slutfört föregående steg:

1. Under den **rubrik** fliken, lägga till en HTTP-begäran-huvud nyckel **Content-Type** med värdet `multipart/mixed`.

   ![Innehållstyp multipart/blandad][4]

1. Serialisera data för icke-text till filer. JSON-data sparas som en JSON-fil.
1. Under den **brödtext** fliken, lägga till varje fil genom att tilldela en **nyckel** namn, välja `file` eller `text`.
1. Markera varje fil via den **Välj fil** knappen.

   ![Postman-klient, exempel][5]

   >[!NOTE]
   > * Postman-klienten kräver inte att multipart segment har manuellt tilldelade **Content-Type** eller **Content-Disposition**.
   > * Du behöver inte ange dessa rubriker för varje del.
   > * Du måste välja `multipart/mixed` eller någon annan lämplig **Content-Type** för hela begäran.

1. Klicka slutligen på **skicka** skicka din multipart HTTP POST-begäran.

## <a name="next-steps"></a>Nästa steg

- Mer om Digital Twins management API: er och hur de används finns [hur du använder Azure Digital Twins management API: er](how-to-navigate-apis.md).

- Använda flera delar begäranden till [lägga till BLOB-objekt till Azure Digital Twins' entiteter](./how-to-add-blobs.md).

- Läs om autentisering med API: er för Management [autentisera med API: er](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-configure-postman/implicit-flow.png
[2]: media/how-to-configure-postman/reply-url.png
[3]: media/how-to-configure-postman/postman-oauth-token.png
[4]: media/how-to-configure-postman/content-type.png
[5]: media/how-to-configure-postman/form-body.png