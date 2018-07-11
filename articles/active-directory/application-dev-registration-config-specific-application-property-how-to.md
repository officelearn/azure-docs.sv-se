---
title: Hur du fyller i specifika fält för ett egenutvecklat program | Microsoft Docs
description: Anvisningar för hur du fyller i specifika fält när du registrerar ett anpassat utvecklade program med Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: c6f6b0685c83d9305c62f121aec562d2afedae21
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "36335728"
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
| Programtyp | **Webbapp/webb API**: ett program som motsvarar ett webbprogram, ett webb-API eller båda 
| |**Interna**: ett program som kan installeras på en användares enhet eller dator           |
| Inloggnings-URL      | URL: en där användare kan logga in använder dina program                                  |

När du har fyllt fälten ovan kan programmet registreras i Azure-portalen och omdirigeras du till appen på sidan. Den **inställningar** knappen i program-panelen öppnas sidan Inställningar, som har fler fält som du kan anpassa ditt program. Tabellen nedan beskriver alla fält på sidan Inställningar. Observera att du bara ser en delmängd av fälten, beroende på om du har skapat ett webbprogram eller ett internt program.

| Fält           | Beskrivning                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Program-ID:t  | När du registrerar ett program, tilldelas Azure AD ditt program en program-ID. Program-ID kan användas för att identifiera ditt program i autentiseringsförfrågningar till Azure AD, samt för att komma åt resurser som Graph API.                                                          |
| App-ID-URI      | Det här ska vara en unik URI, vanligtvis i formatet **https://&lt;klient\_namn&gt;/&lt;programmet\_namn&gt;.** Den används vid auktorisering bevilja flödet, som en unik identifierare för att ange resursen som token ska utfärdas för. Det blir också 'aud ”-anspråk i utfärdade åtkomsttoken. |
| Ladda upp ny logo | Du kan använda detta för att ladda upp en logotyp för ditt program. Logotypen måste vara i formatet .bmp, .jpg eller .png och filstorleken ska vara mindre än 100KB. Dimensioner för avbildningen ska vara 215 x 215 bildpunkter, med centrala bildförhållanden på 94 x 94 pixlar.                                                       |
| Hemside-URL   | Det här är inloggnings-URL som anges under programregistrering.                                                                                                                                                                                                                                              |
| Utloggnings-URL      | Den här URL: en för enkel utloggning utloggning. Azure AD skickar en utloggningsbegäran till denna URL när användaren tar bort sin session med Azure AD med hjälp av andra registrerade program.                                                                                                                                       |
| Multi-klient  | Den här växeln anger om programmet kan användas av flera klienter. Detta innebär normalt att externa organisationer kan använda ditt program genom att registrera den i deras klienter och bevilja åtkomst till organisationens data.                                                                   |
| Svars-URL:er      | Svaret från URL: er är slutpunkter där Azure AD returnerar de token som programmet begär.                                                                                                                                                                                                          |
| Omdirigerings-URI:er   | Detta är för interna program där användaren skickas efter auktoriseringen. Azure AD-Kontrollera att omdirigeringen-URI för ditt program finns i OAuth 2.0-begäran matchar något av de registrerade värdena i portalen.                                                            |
| Nycklar            | Du kan skapa nycklar till programmässigt åtkomst till webb-API: er som skyddas av Azure AD utan någon användarinteraktion. Från den \* \*nycklar\* \* anger en nyckelbeskrivning och utgångsdatum och spara för att generera nyckeln. Se till att spara den någonstans säkert, som du kan inte komma åt den senare.             |

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](manage-apps/what-is-application-management.md)
