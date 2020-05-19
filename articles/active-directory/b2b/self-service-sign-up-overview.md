---
title: Registrering av självbetjäning för externa identiteter – Azure AD
description: Lär dig hur du tillåter externa användare att registrera sig för dina program genom att aktivera självbetjänings registrering. Skapa en personlig registrerings upplevelse genom att anpassa användar flödet för självbetjänings registrering.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e0325b43b6726f04d5994b60404f218ac58122d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597536"
---
# <a name="self-service-sign-up-preview"></a>Självbetjänings registrering (för hands version)
|     |
| --- |
| Självbetjänings registrering är en offentlig förhands gransknings funktion i Azure Active Directory. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.|
|     |

När du delar program med externa användare är det inte säkert att du alltid vet i förväg vilka som behöver åtkomst till ett program. Som ett alternativ till att skicka inbjudningar direkt till enskilda användare kan du tillåta att externa användare registrerar sig för enskilda program genom att aktivera självbetjänings registrering. Du kan skapa en personlig registrerings upplevelse genom att anpassa användar flödet för självbetjänings registrering. Du kan till exempel ange alternativ för Azure AD eller sociala identitets leverantörer och samla in information om användaren.

## <a name="user-flow-for-self-service-sign-up"></a>Användar flöde för självbetjänings registrering

Ett självbetjänings registrerings användar flöde skapar en registrerings upplevelse för dina externa användare genom det program som du vill dela. Användar flödet kan associeras med ett eller flera av dina program. Först ska du aktivera självbetjänings registrering för din klient och federera med alla identitets leverantörer som du vill att externa användare ska kunna använda för inloggning. Sedan skapar du och anpassar registrerings användar flödet och tilldelar dina program till den.
Du kan konfigurera användar flödes inställningarna för att styra hur användaren registrerar sig för programmet:

- Konto typer som används för inloggning, till exempel sociala konton som Facebook eller Azure AD-konton
- Attribut som ska samlas in från användaren som registrerar sig, till exempel förnamn, post nummer eller land i placering

När en användare vill logga in i ditt program, oavsett om det är en webb-, mobil-, skriv bords-eller Enkels Ides applikation (SPA), initierar programmet en auktoriseringsbegäran till slut punkten för användar flödet. Användar flödet definierar och styr användarens upplevelse. När de slutför ett registrerat användar flöde genererar Azure AD en token och omdirigerar sedan tillbaka användaren till ditt program. Flera program kan använda samma användar flöde.

## <a name="example-of-self-service-sign-up"></a>Exempel på självbetjänings registrering

I följande exempel visas hur vi samlar in sociala identitets leverantörer till Azure AD med självbetjänings registrerings funktioner för gäst användare.  
En partner av Sparbanken öppnar Sparbanken-appen. De bestämmer sig för att registrera sig för ett leverantörs konto, så att de väljer begär ditt leverantörs konto, som initierar registrerings flödet för självbetjäning.

![Exempel på Start sidan för självbetjänings registrering](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

De använder e-postmeddelandet för att registrera dig.

![Exempel som visar valet av Facebook för inloggning](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

Azure AD skapar en relation med Sparbanken med partnerns Facebook-konto och skapar ett nytt konto.

Sparbank vill veta mer om användaren, t. ex. namn, företags namn, kod för företags registrering, telefonnummer.

![Exempel som visar användarens registrerings-attribut](media/self-service-sign-up-overview/example-enter-user-attributes.png)

Användaren anger informationen, fortsätter med registrerings flödet och får åtkomst till de resurser som de behöver.

![Exempel som visar den inloggade användaren](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>Nästa steg

 Mer information finns i så här lägger du till självbetjänings [registrering i en app](self-service-sign-up-user-flow.md).