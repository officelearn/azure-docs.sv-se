---
title: Fylla i specifika fält för en anpassad utvecklade program | Microsoft Docs
description: Anvisningar om hur du fyller i specifika fält när du registrerar ett anpassat utvecklade program med Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: cd4313efb5d08842ba12ec00e6e5160214800d56
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Fylla i specifika fält för en anpassad utvecklade program

Den här artikeln får du en kort beskrivning av alla tillgängliga fält i registreringsformuläret program i den [Azure-portalen](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registrera ett nytt program

-   Om du vill registrera ett nytt program, navigera till den [Azure-portalen](https://portal.azure.com).

-   I det vänstra navigeringsfönstret klickar du på **Azure Active Directory.**

-   Välj **App registreringar** och på **Lägg till**.

-   Den här öppna programmet registreringsformuläret.

## <a name="fields-in-the-application-registration-form"></a>Fält i registreringsformuläret som programmet


| Fält            | Beskrivning                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Namn             | Namnet på programmet. Det bör ha minst fyra tecken.                |
| Programtyp | **Webbapp/webb API**: ett program som representerar ett webbprogram, webb-API eller båda 
| |**Interna**: ett program som kan installeras på en användares dator eller enhet           |
| Inloggnings-URL      | Den URL där användarna kan logga in att använda ditt program                                  |

När du har fyllt fälten ovan kan programmet är registrerad i Azure portal och du omdirigeras till sidan program. Den **inställningar** knappen i rutan programmet öppnas sidan inställningar som har fler fält som du kan anpassa ditt program. I tabellen nedan beskrivs alla fält i inställningssidan. Observera att du bara se en delmängd av dessa fält, beroende på om du har skapat ett webbprogram eller interna program.

| Fält           | Beskrivning                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Program-ID:t  | När du registrerar ett program, tilldelas Azure AD programmet en program-ID. Program-ID kan användas för att identifiera ditt program i autentiseringsbegäranden till Azure AD, samt för att komma åt resurser som Graph API.                                                          |
| App-ID-URI      | Detta bör vara ett unikt URI, vanligtvis i formuläret **https://&lt;klient\_namn&gt;/&lt;programmet\_namn&gt;.** Används under authorization grant flödet, som en unik identifierare för att ange den resurs som ska vara utfärdade token för. Det blir också 'eller' anspråk i utfärdade åtkomsttoken. |
| Ladda upp ny logo | Du kan använda den för att ladda upp en logotyp för ditt program. Logotypen måste vara i formatet .bmp, .jpg eller .png och filstorleken måste vara mindre än 100KB. Dimensioner för avbildningen ska 215 x 215 bildpunkter med central bildmåtten 94 x 94 bildpunkter.                                                       |
| Hemside-URL   | Det här är den inloggning URL som anges under programmet registreringen.                                                                                                                                                                                                                                              |
| Utloggnings-URL      | Den här URL: en enda utloggning logga ut. Azure AD skickar en logga ut begäran till denna URL när användaren tar bort sin session med Azure AD med hjälp av andra registrerade programmet.                                                                                                                                       |
| Multi-klient  | Den här växeln anger om programmet ska kunna användas av flera klienter. Detta innebär normalt att externa organisationer kan använda ditt program genom att registrera det i klientorganisationen och bevilja åtkomst till organisationens data.                                                                   |
| Svars-URL:er      | Svaret URL: er är slutpunkter där Azure AD returnerar de token som ditt program begär.                                                                                                                                                                                                          |
| Omdirigerings-URI:er   | Detta är för interna program där användaren skickas efter auktoriseringen. Azure AD-kontroll som omdirigerings-URI programmet tillhandahåller i OAuth 2.0-begäran matchar ett av de registrerade värdena i portalen.                                                            |
| Nycklar            | Du kan skapa nycklar för att programmatiskt åtkomst web API: er som skyddas av Azure AD utan någon användarinteraktion. Från den \* \*nycklar\* \* anger du en beskrivning av nyckeln och utgångsdatum och spara för att generera nyckeln. Se till att spara den någonstans säker, du kan inte komma åt den senare.             |

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](manage-apps/what-is-application-management.md)
