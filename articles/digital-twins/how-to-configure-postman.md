---
title: Så här konfigurerar du Postman för Azure Digitals flätar | Microsoft Docs
description: Så här konfigurerar du Postman för digital Azures dubbla.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: v-adgera
ms.openlocfilehash: 66dbfd09ef07740c07ddb010b73e33e783340e5a
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873664"
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

1. Följ stegen i [den här snabb](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) starten för att skapa ett internt Azure AD-program av typen Native. Eller så kan du återanvända en befintlig intern app-registrering.

1. Under **nödvändiga behörigheter**väljer du **Lägg till** och anger **Azure Digitals DUBBLAre** under **Lägg till API-åtkomst**. Om sökningen inte hittar API:et söker du efter **Azure Smart Spaces** i stället. Välj sedan **bevilja behörigheter > delegerade behörigheter** och **färdig**.

    [![Azure Active Directory app-registreringar Lägg till API](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Välj **manifest** för att öppna applikations manifestet för appen. Ange *oauth2AllowImplicitFlow* till `true`.

    [![Azure Active Directory implicit flöde](media/how-to-configure-postman/implicit-flow.png)](media/how-to-configure-postman/implicit-flow.png#lightbox)

1. Konfigurera en **svars** - `https://www.getpostman.com/oauth2/callback`URL till.

    [![Azure Active Directory svars-URL](media/how-to-configure-postman/reply-url.png)](media/how-to-configure-postman/reply-url.png#lightbox)

1. Kopiera och behåll **program-ID: t** för din Azure Active Directory-app. Den används i de steg som följer.

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
    | Beviljandetyp | `Implicit` |
    | Motringnings-URL | `https://www.getpostman.com/oauth2/callback` |
    | URL för autentisering | Använd **URL:** en för auktorisering från steg 2 |
    | Klientorganisations-ID | Använd **program-ID:** t för den Azure Active Directory app som skapades eller återanvändas från föregående avsnitt |
    | Omfång | Lämna tomt |
    | State | Lämna tomt |
    | Klientautentisering | `Send as Basic Auth header` |

1. Klienten bör nu visas som:

    [![Exempel på Postman-klient](media/how-to-configure-postman/postman-oauth-token.png)](media/how-to-configure-postman/postman-oauth-token.png#lightbox)

1. Välj **token för begäran**.

    >[!TIP]
    >Om du får fel meddelandet "OAuth 2 kunde inte slutföras" kan du prova följande:
    > * Stäng Postman och öppna det på nytt och försök igen.
  
1. Rulla nedåt och välj **Använd token**.

<div id="multi"></div>

## <a name="make-a-multipart-post-request"></a>Gör en flerdelade POST-begäran

När du har slutfört de föregående stegen konfigurerar du Postman att göra en autentiserad HTTP multipart-begäran:

1. På fliken **sidhuvud** lägger du till en http-begäran rubrik nyckel **innehåll-typ** med `multipart/mixed`värde.

   [![Innehålls typ för multipart/blandat](media/how-to-configure-postman/content-type.png)](media/how-to-configure-postman/content-type.png#lightbox)

1. Serialisera icke-text data i filer. JSON-data sparas som en JSON-fil.
1. På fliken **brödtext** lägger du till varje fil genom att tilldela ett **nyckel** namn, `file` välja `text`eller.
1. Välj sedan varje fil via knappen **Välj fil** .

   [![Exempel på Postman-klient](media/how-to-configure-postman/form-body.png)](media/how-to-configure-postman/form-body.png#lightbox)

   >[!NOTE]
   > * Postman-klienten kräver inte att flerdelade segment har en manuellt tilldelad **innehålls typ** eller **innehålls-disposition**.
   > * Du behöver inte ange dessa rubriker för varje del.
   > * Du måste välja `multipart/mixed` eller en annan lämplig **innehålls typ** för hela begäran.

1. Slutligen väljer du **Skicka** för att skicka din multipart HTTP POST-begäran.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om digitala dubbla hanterings-API: er och hur du använder dem läser [du så här använder du Azure Digitals hanterings-API: er](how-to-navigate-apis.md).

- Använd multipart-begäranden för att [lägga till blobar i entiteter i Azure Digitals dubbla enheter](./how-to-add-blobs.md).

- Läs [autentisera med API: er](./security-authenticating-apis.md)om du vill lära dig mer om att autentisera med hanterings-API: er.
