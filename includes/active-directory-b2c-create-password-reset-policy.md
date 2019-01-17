---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 78abb190dccd27c5bf70dfe12f978e1118601815
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355752"
---
Om du vill aktivera detaljerad lösenordsåterställning i ditt program måste du använda en **lösenordsåterställning** användarflödet. Observera att alternativet för återställning av lösenord klienttäckande anges [här](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md). Det här användarflödet beskriver hur kunderna går till för återställning av lösenord och innehållet i de token som programmet tar emot vid genomförda inloggningar.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Under **hantera**väljer **användarflöden** och klicka på +**nytt användarflöde**.

![Välj nytt användarflöde](media/active-directory-b2c-create-password-reset-policy/add-b2c-new-user-flow.png)

På den **rekommenderas** fliken **lösenordsåterställning**.

Ange ett användarflöde **namn** för programmet. Ange till exempel `SSPR`.

Under **identitetsprovidrar**, kontrollera **Återställ lösenord med e-postadress**.

![Ange namn och väljer Återställ lösenord med e-postadress som identitetsprovider](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

Under **Programanspråk**, klickar du på **visa fler** och välj anspråk som du vill ska returneras i de auktoriseringstoken som skickas tillbaka till programmet efter en genomförd återställning av lösenord. Välj till exempel **Användarobjekt-id**.

Klicka på **OK**.

![Välj några programanspråk och klicka på OK](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

Klicka på **skapa** att lägga till användarflödet. Användarflödet har listats som **B2C_1_SSPR**. Prefixet **B2C_1_** läggs till i namnet.

Klicka på **kör användarflödet**. Kontrollera inställningarna i tabellen och klicka sedan på **kör användarflödet**.

![Välj användarflödet och kör den](media/active-directory-b2c-create-password-reset-policy/add-b2c-sspr-run-user-flow.png)

| Inställning      | Värde  |
| ------------ | ------ |
| **Programmet** | Contoso B2C-app |
| **Välj svarswebbadress** | `https://localhost:44316/` |

En ny webbläsarflik öppnas och du kan kontrollera hur lösenordsåterställningen fungerar.

> [!NOTE]
> Det kan ta någon minut att skapa en princip och innan uppdateringarna börjar gälla.
>
