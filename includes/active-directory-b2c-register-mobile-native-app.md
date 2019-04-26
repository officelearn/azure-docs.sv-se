---
title: ta med fil
description: ta med fil
services: active-directory-b2c
author: davidmu1
ms.service: active-directory-b2c
ms.topic: include
ms.date: 04/09/2018
ms.author: davidmu
ms.custom: include file
ms.openlocfilehash: adeaed3625ce42087d874e67cd6cb91d406e8fae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60455678"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Du registrerar ditt mobila eller inbyggda program med hjälp av inställningarna i tabellen.

![Exempel på registreringsinställningar för nytt mobilt eller inbyggt program](./media/active-directory-b2c-register-mobile-native-app/b2c-new-mobile-native-app-settings.png)

| Inställning      | Exempelvärde  | Beskrivning                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Namn** | Contoso B2C-app | Ange ett **namn** som beskriver programmet för konsumenterna. |
| **Inbyggd klient** | Ja | Välj **Ja** om du har ett mobilt eller inbyggt program. |
| **Anpassad omdirigerings-URI** | `com.onmicrosoft.contoso.appname://redirect/path` | Ange en omdirigerings-URI med ett anpassat schema. Se till att välja en [bra omdirigerings-URI](../articles/active-directory-b2c/active-directory-b2c-app-registration.md) som inte innehåller specialtecken, till exempel understreck. |

Klicka på **Skapa** för att registrera ditt program.

Ditt nyligen registrerade program visas i programlistan för B2C-klientorganisationen. Välj din mobila eller inbyggda app i listan. Programmets egenskapsruta visas.

![Egenskaper för program](./media/active-directory-b2c-register-mobile-native-app/b2c-mobile-native-app-properties.png)

Anteckna det globala unika id:t **Programklients-id**. Använd det här id:t i programkoden.

Gör så här om det inbyggda programmet anropar ett webb-API som skyddas av Azure AD B2C:
   1. Skapa en programhemlighet genom att gå till bladet **Nycklar** och klicka på **Generera nyckel**. Anteckna **appnyckel**-värdet. Du använder värdet som programhemlighet i programkoden.
   2. Klicka på **API-åtkomst**, **Lägg till** och välj webb-API och områden (behörigheter).

> [!NOTE]
> En **programhemlighet** är en viktig autentiseringsuppgift och bör skyddas på lämpligt sätt.
> 
