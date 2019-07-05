---
title: Hur du fyller i specifika fält för ett egenutvecklat program | Microsoft Docs
description: Anvisningar för hur du fyller i specifika fält när du registrerar ett anpassat utvecklade program med Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bccaa28d34ebff47c7de73a4d9b3d8296ae9fef
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476130"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Hur du fyller i specifika fält för ett egenutvecklat program

Den här artikeln ger en kort beskrivning av alla tillgängliga fält i registreringsformuläret för programmet i den [Azure-portalen](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registrera ett nytt program

-   Om du vill registrera ett nytt program, navigerar du till den [Azure-portalen](https://portal.azure.com).

-   I det vänstra navigeringsfönstret klickar du på **Azure Active Directory.**

-   Välj **appregistreringar** och klicka på **Lägg till**.

-   Den här öppna programmet registreringsformuläret.

## <a name="fields-in-the-application-registration-form"></a>Fält i registreringsformuläret för program


| Fält            | Beskrivning                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Namn             | Namnet på programmet. Den bör ha minst fyra tecken.                |
| Kontotyper som stöds| Välja vilka konton som du vill ha ditt program för att stödja: konton i den här organisationens katalog, konton i en organisations katalog eller konton i en organisations katalog och personliga Microsoft-konton.  |
| Omdirigerings-URI (valfritt) | Välj typ av app du skapar, **Web** eller **offentlig klient (mobila och stationära)** , och sedan ange omdirigerings-URI: N (eller svars-URL) för ditt program. För webbappar anger du grundläggande URL för appen. Till exempel kan http://localhost:31544 vara URL för en webbapp som körs på din lokala dator. Användare skulle då använda den här URL:en för att logga in till ett webbklientprogram. För offentliga klientprogram anger du den URI som används av Azure AD för att returnera tokensvar. Ange ett värde som är specifika för ditt program, till exempel myapp://auth. Specifika exempel på webbappar och interna program finns i [snabbstarterna](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts).|

När du har fyllt fälten ovan kan programmet registreras i Azure-portalen och omdirigeras du till appen på översiktssidan. För inställningssidor i det vänstra fönstret under **hantera** har fler fält som du kan anpassa ditt program. I tabellerna nedan beskriver alla fält. Du ser bara en delmängd av fälten, beroende på om du har skapat ett webbprogram eller en offentlig klientprogram.

### <a name="overview"></a>Översikt
| Fält           | Beskrivning        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Program-ID:t  | När du registrerar ett program, tilldelas Azure AD ditt program en program-ID. Program-ID kan användas för att identifiera ditt program i autentiseringsförfrågningar till Azure AD, samt för att komma åt resurser som Graph API.                                                          |
| App-ID-URI      | Det här ska vara en unik URI, vanligtvis i formatet **https://&lt;klient\_namn&gt;/&lt;programmet\_namn&gt;.** Den används vid auktorisering bevilja flödet, som en unik identifierare för att ange den resurs som token ska utfärdas för. Det blir också 'aud ”-anspråk i utfärdade åtkomsttoken. |

### <a name="branding"></a>Anpassning

| Fält           | Beskrivning        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ladda upp ny logo | Du kan använda detta för att ladda upp en logotyp för ditt program. Logotypen måste vara i formatet .bmp, .jpg eller .png och filstorleken ska vara mindre än 100 KB. Dimensioner för avbildningen ska vara 215 x 215 bildpunkter, med centrala bildförhållanden på 94 x 94 pixlar.|
| URL-Adressen   | Det här är inloggnings-URL som anges under programregistrering.|

### <a name="authentication"></a>Autentisering

| Fält           | Beskrivning        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Utloggnings-URL      | Det här är URL för enkel utloggning utloggning. Azure AD skickar en utloggningsbegäran till denna URL när användaren tar bort sin session med Azure AD med hjälp av andra registrerade program.|
| Kontotyper som stöds  | Den här växeln anger om programmet kan användas av flera klienter. Detta innebär normalt att externa organisationer kan använda ditt program genom att registrera den i deras klienter och bevilja åtkomst till organisationens data.|
| Omdirigerings-URL      | Omdirigering, eller svara, URL: er är slutpunkter där Azure AD returnerar de token som programmet begär. Detta är för interna program där användaren skickas efter auktoriseringen. Azure AD kontrollerar att omdirigeringen-URI som ditt program finns i OAuth 2.0-begäran matchar något av de registrerade värdena i portalen.|

### <a name="certificates-and-secrets"></a>Certifikat och hemligheter

| Fält           | Beskrivning        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Klienten hemligheter            | Du kan skapa klienten hemligheter eller nycklar för att programmässigt åtkomst webb API: er som skyddas av Azure AD utan någon användarinteraktion. Från den **nya klienthemligheten** anger en nyckelbeskrivning och utgångsdatum och spara för att generera nyckeln. Se till att spara den någonstans säkert, som du kan inte komma åt den senare.             |

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](../manage-apps/what-is-application-management.md)
