---
title: Registreringsfält för Azure Portal för specialutvecklade appar
description: Vägledning för registrering av ett anpassat utvecklat program med Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: 36d74b9926639bb4ec49821a3d73b5d615016394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702682"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>Registreringsfält för Azure Portal för specialutvecklade appar

Den här artikeln ger dig en kort beskrivning av alla tillgängliga fält i ansökningsregistreringsformuläret i [Azure-portalen](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registrera ett nytt program

-   Om du vill registrera ett nytt program navigerar du till [Azure-portalen](https://portal.azure.com).

-   Klicka på **Azure Active Directory** i det vänstra navigeringsfönstret.

-   Välj **Appregistreringar** och klicka på **Lägg till**.

-   Detta öppnar ansökningsregistreringsformuläret.

## <a name="fields-in-the-application-registration-form"></a>Fält i ansökningsregistreringsformuläret

| Field            | Beskrivning                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Namn             | Namnet på programmet. Den bör ha minst fyra tecken.                |
| Kontotyper som stöds| Välj vilka konton du vill att programmet ska stödja: konton i den här organisationskatalogen, konton i en organisationskatalog eller konton i alla organisationskataloger och personliga Microsoft-konton.  |
| Omdirigera URI (valfritt) | Välj vilken typ av app du skapar, **Webb** eller **Offentlig klient (mobil & skrivbord)** och ange sedan omdirigerings-URI-adressen (eller svars-URL:en) för ditt program. För webbappar anger du grundläggande URL för appen. Till exempel kan http://localhost:31544 vara URL för en webbapp som körs på din lokala dator. Användare skulle då använda den här URL:en för att logga in till ett webbklientprogram. För offentliga klientprogram anger du den URI som används av Azure AD för att returnera tokensvar. Ange ett värde som är specifikt för ditt program, till exempel myapp://auth. Om du vill se specifika exempel för webbprogram eller inbyggda program kan du läsa våra [snabbstarter.](https://docs.microsoft.com/azure/active-directory/develop)|

När du har fyllt i ovanstående fält registreras programmet i Azure-portalen och du omdirigeras till programöversiktssidan. Inställningssidorna i den vänstra rutan under **Hantera** har fler fält där du kan anpassa programmet. Tabellerna nedan beskriver alla fält. Du skulle bara se en delmängd av dessa fält, beroende på om du har skapat ett webbprogram eller ett offentligt klientprogram.

### <a name="overview"></a>Översikt

| Field           | Beskrivning        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Program-ID:t  | När du registrerar ett program tilldelar Azure AD ditt program ett program-ID. Program-ID:t kan användas för att unikt identifiera ditt program i autentiseringsbegäranden till Azure AD, samt för att komma åt resurser som Graph API.                                                          |
| URI för app-id      | Detta bör vara en unik URI, vanligtvis i formuläret **https://&lt;klientnamn\_&gt;/&lt;programnamn\_&gt;.** Detta används under auktoriseringsbidragsflödet, som en unik identifierare för att ange den resurs som token ska utfärdas för. Det blir också "aud"-anspråket i den utfärdade åtkomsttoken. |

### <a name="branding"></a>Anpassning

| Field           | Beskrivning        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ladda upp ny logotyp | Du kan använda detta för att ladda upp en logotyp för ditt program. Logotypen måste vara i .bmp-, .jpg- eller .png-format och filstorleken ska vara mindre än 100 kB. Måtten för bilden ska vara 215x215 pixlar, med centrala bilddimensioner på 94x94 pixlar.|
| Url till startsidan   | Detta är den inloggnings-URL som anges under programregistreringen.|

### <a name="authentication"></a>Autentisering

| Field           | Beskrivning        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Utloggnings-URL      | Det här är url:en för utloggning av utloggning. Azure AD skickar en utloggningsbegäran till den här URL:en när användaren rensar sin session med Azure AD med andra registrerade program.|
| Kontotyper som stöds  | Den här växeln anger om programmet kan användas av flera klienter. Detta innebär vanligtvis att externa organisationer kan använda ditt program genom att registrera det i sin klientorganisation och bevilja åtkomst till organisationens data.|
| Omdirigerings-URL:er      | Omdirigering, eller svar, url:er är de slutpunkter där Azure AD returnerar alla token som ditt program begär. För inbyggda program är det här användaren skickas efter lyckad auktorisering. Azure AD kontrollerar att omdirigera URI ditt program tillhandahåller i OAuth 2.0 begäran matchar ett av de registrerade värdena i portalen.|

### <a name="certificates-and-secrets"></a>Certifikat och hemligheter

| Field           | Beskrivning        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Klienthemligheter            | Du kan skapa klienthemligheter, eller nycklar, för att programmässigt komma åt webb-API:er som skyddas av Azure AD utan någon användarinteraktion. Från sidan **Ny klienthemlighet** anger du en nyckelbeskrivning och utgångsdatum och sparar för att generera nyckeln. Se till att spara den någonstans säkert, eftersom du inte kommer åt den senare.             |

## <a name="next-steps"></a>Nästa steg

[Hantera program med Azure Active Directory](../manage-apps/what-is-application-management.md)
