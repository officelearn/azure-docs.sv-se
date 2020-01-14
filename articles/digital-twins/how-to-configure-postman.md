---
title: Så här konfigurerar du Postman – Azure Digitals flätar | Microsoft Docs
description: 'Lär dig hur du konfigurerar och använder Postman för att testa Azure Digitals dubbla API: er.'
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: 3d0220f23c8098222b93473dc6c7aa7a4f2dd791
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933447"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Så här konfigurerar du Postman för digital Azures dubbla

Den här artikeln beskriver hur du konfigurerar Postman REST-klienten att interagera med och testa Azure Digitals hanterings-API: er. Mer specifikt beskrivs:

* Så här konfigurerar du ett Azure Active Directory program att använda det implicita tilldelnings flödet för OAuth 2,0.
* Använda Postman REST-klienten för att skapa token HTTP-begäranden till dina hanterings-API: er.
* Hur du använder Postman för att göra flerdelade POST-förfrågningar till dina hanterings-API: er.

## <a name="postman-summary"></a>Postman-Sammanfattning

Kom igång med Azure Digitals dubbla med ett REST-klient verktyg som [Postman](https://www.getpostman.com/) för att förbereda din lokala test miljö. Postman-klienten hjälper dig att snabbt skapa komplexa HTTP-begäranden. Ladda ned Skriv bords versionen av Postman-klienten genom att gå till [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) är ett rest-testverktyg som söker efter funktioner för nyckel-http-begäran i ett användbart Skriv bords-och plugin-baserade användar gränssnitt.

Med Postman-klienten kan Solutions-utvecklare ange typen av HTTP-begäran (*post*, *Get*, *Update*, *patch*och *Delete*), API-slutpunkt för anrop och användning av SSL. Postman stöder även tillägg av HTTP-begärandehuvuden, parametrar, formulär data och organ.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Konfigurera Azure Active Directory att använda det implicita tilldelnings flödet för OAuth 2,0

Konfigurera Azure Active Directory-appen så att den använder det implicita bidrags flödet OAuth 2,0.

1. Öppna fönstret **API-behörigheter** för din app-registrering. Välj **Lägg till en behörighet** -knapp. I rutan **begär API-behörigheter** väljer du fliken **API: er min organisation använder** och söker sedan efter:
    
    1. `Azure Digital Twins`. Välj **Azure Digitals dubbla** API: er.

        [![Sök-API eller digitala Azure-dubbla](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png)](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Du kan också söka efter `Azure Smart Spaces Service`. Välj API för **Azure Smart Spaces-tjänsten** .

        [![Sök-API för Azure Smart Spaces](../../includes/media/digital-twins-permissions/aad-app-search-api.png)](../../includes/media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Det API-namn och ID för Azure AD som visas beror på din klient organisation:
    > * Testa klient organisation och kund konton bör söka efter `Azure Digital Twins`.
    > * Andra Microsoft-konton bör söka efter `Azure Smart Spaces Service`.

1. Det valda API: et visas som **Azure Digitals dubbla** i samma **API-behörigheter för begäran** . Välj List rutan **Läs (1)** och välj sedan **Läs. Skriv** -kryss rutan. Välj knappen **Lägg till behörigheter** .

    [![lägga till API-behörigheter för Azure Digital-dubbla](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Beroende på organisationens inställningar kan du behöva vidta ytterligare åtgärder för att bevilja administratörs åtkomst till det här API: et. Kontakta administratören om du vill ha mer information. När administratörs åtkomsten har godkänts visas kolumnen **Administratörs medgivande som krävs** i rutan **API-behörigheter** som liknar följande för dina API: er:

    [![Konfigurera godkännande av administratörs godkännande](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png)](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

1. Konfigurera en andra **omdirigerings-URI** till `https://www.getpostman.com/oauth2/callback`.

    [![konfigurera en ny Postman omdirigerings-URI](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. För att se till att [appen är registrerad som en **offentlig klient**](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration)öppnar du fönstret **autentisering** för din app-registrering och bläddrar nedåt i fönstret. I avsnittet **standard klient typ** väljer du **Ja** för att **behandla program som en offentlig klient**och trycker på **Spara**.

    Kontrol **lera åtkomsttoken för** att aktivera **oauth2AllowImplicitFlow** -inställningen i manifestet. JSON.

    [konfigurations inställning för ![offentlig klient](../../includes/media/digital-twins-permissions/aad-configure-public-client.png)](../../includes/media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1. Kopiera och behåll **program-ID: t** för din Azure Active Directory-app. Den används i de steg som följer.

   [![Azure Active Directory program-ID](../../includes/media/digital-twins-permissions/aad-app-reg-app-id.png)](../../includes/media//digital-twins-permissions/aad-app-reg-app-id.png#lightbox)


## <a name="obtain-an-oauth-20-token"></a>Hämta en OAuth 2,0-token

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Konfigurera och konfigurera Postman för att hämta en Azure Active Directory-token. Därefter gör du en autentiserad HTTP-begäran till Azure Digitals med den hämtade token:

1. Gå till [www.getpostman.com](https://www.getpostman.com/) för att ladda ned appen.
1. Kontrol lera att **behörighets webb adressen** är korrekt. Det bör ha formatet:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Namn  | Ersätt med | Exempel |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Namnet på din klient organisation eller organisation | `microsoft` |

1. Välj fliken **auktorisering** , välj **OAuth 2,0**och välj sedan **Hämta ny**åtkomsttoken.

    | Field  | Värde |
    |---------|---------|
    | Grant Type (Typ av beviljande) | `Implicit` |
    | Callback URL (Webbadress för återanrop) | `https://www.getpostman.com/oauth2/callback` |
    | Auth URL (Auktoriseringswebbadress) | Använd **URL:** en för auktorisering från **steg 2** |
    | Klientorganisations-ID | Använd **program-ID:** t för den Azure Active Directory-app som skapades eller återanvändas från föregående avsnitt |
    | Omfång | Lämna tomt |
    | Status | Lämna tomt |
    | Klientautentisering | `Send as Basic Auth header` |

1. Klienten bör nu visas som:

    [exempel på ![Postman-klient](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Välj **Request Token** (Begär token).
  
1. Rulla nedåt och välj **Använd token**.

## <a name="make-a-multipart-post-request"></a>Gör en flerdelade POST-begäran

När du har slutfört de föregående stegen konfigurerar du Postman att göra en autentiserad HTTP multipart-begäran:

1. På fliken **sidhuvud** lägger du till en http-begäran rubrik nyckel **innehåll-typ** med värdet `multipart/mixed`.

   [![ange innehålls typen multipart/Mixed](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Serialisera icke-text data i filer. JSON-data sparas som en JSON-fil.
1. Under fliken **brödtext** väljer du `form-data`. 
1. Lägg till varje fil genom att tilldela ett **nyckel** namn och välja `File`.
1. Välj sedan varje fil via knappen **Välj fil** .

   [exempel på Postman-klientens formulär text ![](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * Postman-klienten kräver inte att flerdelade segment har en manuellt tilldelad **innehålls typ** eller **innehålls-disposition**.
   > * Du behöver inte ange dessa rubriker för varje del.
   > * Du måste välja `multipart/mixed` eller en annan lämplig **innehålls typ** för hela begäran.

1. Slutligen väljer du **Skicka** för att skicka din multipart HTTP POST-begäran. Status koden för `200` eller `201` anger en lyckad begäran. Lämpligt svars meddelande visas i klient gränssnittet.

1. Verifiera dina HTTP POST-begär ande data genom att anropa API-slutpunkten: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om digitala dubbla hanterings-API: er och hur du använder dem läser [du så här använder du Azure Digitals hanterings-API: er](how-to-navigate-apis.md).

- Använd multipart-begäranden för att [lägga till blobar i entiteter i Azure Digitals dubbla enheter](./how-to-add-blobs.md).

- Läs [autentisera med API: er](./security-authenticating-apis.md)om du vill lära dig mer om att autentisera med hanterings-API: er.
