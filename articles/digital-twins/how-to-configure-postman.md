---
title: Så här konfigurerar du Postman för Azure Digitals flätar | Microsoft Docs
description: Så här konfigurerar du Postman för digital Azures dubbla.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 14e6a52f86586eaae019d9658c2f813a15fc3474
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949213"
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

        [![Search API eller Azure Digitals, dubbla](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png)](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Du kan också söka efter `Azure Smart Spaces Service`. Välj API för **Azure Smart Spaces-tjänsten** .

        [![Search-API för Azure Smart Spaces](../../includes/media/digital-twins-permissions/aad-app-search-api.png)](../../includes/media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Det API-namn och ID för Azure AD som visas beror på din klient organisation:
    > * Testa klient organisation och kund konton bör söka efter `Azure Digital Twins`.
    > * Andra Microsoft-konton bör söka efter `Azure Smart Spaces Service`.

1. Det valda API: et visas som **Azure Digitals dubbla** i samma **API-behörigheter för begäran** . Välj List rutan **Läs (1)** och välj sedan **Läs. Skriv** -kryss rutan. Välj knappen **Lägg till behörigheter** .

    [![Lägga till API-behörigheter](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Beroende på organisationens inställningar kan du behöva vidta ytterligare åtgärder för att bevilja administratörs åtkomst till det här API: et. Kontakta administratören om du vill ha mer information. När administratörs åtkomsten har godkänts visas kolumnen **Administratörs medgivande som krävs** i rutan **API-behörigheter** som liknar följande för dina API: er:

    [![Lägga till API-behörigheter](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png)](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)


1. Välj **manifest** för att öppna applikations manifestet för appen. Ange *oauth2AllowImplicitFlow* till `true`.

    [![Azure Active Directory implicit flöde](media/how-to-configure-postman/implicit-flow.png)](media/how-to-configure-postman/implicit-flow.png#lightbox)

1. Konfigurera en **svars-URL** till `https://www.getpostman.com/oauth2/callback`.

    [![Azure Active Directory svars-URL](media/how-to-configure-postman/reply-url.png)](media/how-to-configure-postman/reply-url.png#lightbox)

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

    | Name  | Ersätt med | Exempel |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Namnet på din klient organisation eller organisation | `microsoft` |

1. Välj fliken **auktorisering** , välj **OAuth 2,0**och välj sedan **Hämta ny**åtkomsttoken.

    | Fält  | Value |
    |---------|---------|
    | Typ av beviljande | `Implicit` |
    | Motringnings-URL | `https://www.getpostman.com/oauth2/callback` |
    | URL för autentisering | Använd **URL:** en för auktorisering från **steg 2** |
    | Klientorganisations-ID | Använd **program-ID:** t för den Azure Active Directory-app som skapades eller återanvändas från föregående avsnitt |
    | Omfång | Lämna tomt |
    | State | Lämna tomt |
    | Klientautentisering | `Send as Basic Auth header` |

1. Klienten bör nu visas som:

    [1Postman-klient exempel @no__t](media/how-to-configure-postman/postman-oauth-token.png)](media/how-to-configure-postman/postman-oauth-token.png#lightbox)

1. Välj **token för begäran**.

    >[!TIP]
    >Om du får fel meddelandet "OAuth 2 kunde inte slutföras" kan du prova följande:
    > * Stäng Postman och öppna det på nytt och försök igen.
  
1. Rulla nedåt och välj **Använd token**.

## <a name="make-a-multipart-post-request"></a>Gör en flerdelade POST-begäran

När du har slutfört de föregående stegen konfigurerar du Postman att göra en autentiserad HTTP multipart-begäran:

1. På fliken **sidhuvud** lägger du till en http-begäran rubrik nyckel **innehåll-typ** med värdet `multipart/mixed`.

   [![Content typ multipart/blandat](media/how-to-configure-postman/content-type.png)](media/how-to-configure-postman/content-type.png#lightbox)

1. Serialisera icke-text data i filer. JSON-data sparas som en JSON-fil.
1. På fliken **brödtext** väljer du `form-data`. 
1. Lägg till varje fil genom att tilldela ett **nyckel** namn och välja `file`.
1. Välj sedan varje fil via knappen **Välj fil** .

   [1Postman-klient exempel @no__t](media/how-to-configure-postman/form-body.png)](media/how-to-configure-postman/form-body.png#lightbox)

   >[!NOTE]
   > * Postman-klienten kräver inte att flerdelade segment har en manuellt tilldelad **innehålls typ** eller **innehålls-disposition**.
   > * Du behöver inte ange dessa rubriker för varje del.
   > * Du måste välja `multipart/mixed` eller en annan lämplig **innehålls typ** för hela begäran.

1. Slutligen väljer du **Skicka** för att skicka din multipart HTTP POST-begäran.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om digitala dubbla hanterings-API: er och hur du använder dem läser [du så här använder du Azure Digitals hanterings-API: er](how-to-navigate-apis.md).

- Använd multipart-begäranden för att [lägga till blobar i entiteter i Azure Digitals dubbla enheter](./how-to-add-blobs.md).

- Läs [autentisera med API: er](./security-authenticating-apis.md)om du vill lära dig mer om att autentisera med hanterings-API: er.
