---
title: Så här fyller du i specifika fält för ett anpassat, utvecklat program | Microsoft Docs
description: Vägledning om hur du fyller i specifika fält när du registrerar ett anpassat utvecklat program med Azure AD
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46bc6d2ebbb22ffc37ca73a3fff3b7b5a3a534b8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325336"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Så här fyller du i specifika fält för ett anpassat, utvecklat program

Den här artikeln innehåller en kort beskrivning av alla tillgängliga fält i program registrerings formuläret i [Azure Portal](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registrera ett nytt program

-   Om du vill registrera ett nytt program går du till [Azure Portal](https://portal.azure.com).

-   Klicka på Azure Active Directory i det vänstra navigerings fönstret **.**

-   Välj **Appregistreringar** och klicka på **Lägg till**.

-   Då öppnas formuläret för program registrering.

## <a name="fields-in-the-application-registration-form"></a>Fält i program registrerings formuläret


| Fält            | Beskrivning                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Namn             | Namnet på programmet. Det får innehålla minst fyra tecken.                |
| Kontotyper som stöds| Välj vilka konton som du vill att ditt program ska stödja: konton i den här organisations katalogen, konton i valfri organisations katalog eller konton i alla organisations kataloger och personliga Microsoft-konton.  |
| Omdirigerings-URI (valfritt) | Välj den typ av app som du skapar, **webb** eller **offentlig klient (mobilt & Desktop)** och ange omdirigerings-URI (eller svars-URL) för ditt program. För webbappar anger du grundläggande URL för appen. Till exempel kan http://localhost:31544 vara URL för en webbapp som körs på din lokala dator. Användare skulle då använda den här URL:en för att logga in till ett webbklientprogram. För offentliga klientprogram anger du den URI som används av Azure AD för att returnera tokensvar. Ange ett värde som är specifik för ditt program, till exempel myapp://auth. Specifika exempel på webbappar och interna program finns i [snabbstarterna](https://docs.microsoft.com/azure/active-directory/develop).|

När du har fyllt i fälten ovan registreras programmet i Azure Portal och du omdirigeras till program översikts sidan. Sidan Inställningar i den vänstra rutan under **Hantera** har fler fält som du kan använda för att anpassa ditt program. I tabellerna nedan beskrivs alla fält. Du ser bara en delmängd av dessa fält, beroende på om du har skapat ett webb program eller ett offentligt klient program.

### <a name="overview"></a>Översikt
| Fält           | Beskrivning        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Program-ID:t  | När du registrerar ett program tilldelar Azure AD ditt program ett program-ID. Program-ID: t kan användas för att unikt identifiera ditt program i autentiseringsbegäranden till Azure AD, samt för att få åtkomst till resurser som Graph API.                                                          |
| App-ID-URI      | Detta bör vara en unik URI, vanligt vis av formatet **https://&lt;klient\_&lt;namn&gt;/program\_namn&gt;.** Detta används under flödet för auktoriserings beviljande, som en unik identifierare för att ange den resurs som token ska utfärdas för. Det blir också AUD-anspråket i den utfärdade åtkomsttoken. |

### <a name="branding"></a>Anpassning

| Fält           | Beskrivning        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ladda upp ny logo typ | Du kan använda den för att ladda upp en logo typ för ditt program. Logo typen måste vara i formatet. bmp,. jpg eller. png och fil storleken måste vara mindre än 100 KB. Måtten för bilden ska vara 215x215 pixlar, med centrala bild dimensioner för 94x94-bild punkter.|
| URL för start sidan   | Detta är inloggnings-URL: en som anges under program registreringen.|

### <a name="authentication"></a>Authentication

| Fält           | Beskrivning        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Utloggnings-URL      | Detta är URL: en för enkel utloggning. Azure AD skickar en utloggnings förfrågan till denna URL när användaren tar bort sin session med Azure AD med hjälp av något annat registrerat program.|
| Kontotyper som stöds  | Den här växeln anger om programmet kan användas av flera klienter. Det innebär vanligt vis att externa organisationer kan använda ditt program genom att registrera det i klienten och bevilja åtkomst till organisationens data.|
| Omdirigera URL: er      | URL: erna för omdirigering eller svar är de slut punkter där Azure AD returnerar alla tokens som programmet begär. För interna program är det här som användaren skickas efter en lyckad auktorisering. Azure AD kontrollerar att den omdirigerings-URI som ditt program tillhandahåller i OAuth 2,0-begäran matchar ett av de registrerade värdena i portalen.|

### <a name="certificates-and-secrets"></a>Certifikat och hemligheter

| Fält           | Beskrivning        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Klient hemligheter            | Du kan skapa klient hemligheter eller nycklar för att program mässigt få åtkomst till webb-API: er som skyddas av Azure AD utan användar interaktion. På sidan **ny klient hemlighet** anger du en nyckel beskrivning och förfallo datum och spara för att generera nyckeln. Se till att spara den på ett säkert sätt, eftersom du inte kan komma åt den senare.             |

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](../manage-apps/what-is-application-management.md)
