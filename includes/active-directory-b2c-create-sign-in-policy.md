---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 0ab34d6234db9c13ffe82ccd0e8580217085f631
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742387"
---
Om du vill aktivera endast logga in på ditt program kan du använda en **inloggning** användarflödet. Det här användarflödet beskriver hur kunder går till för inloggning och innehållet i de token som programmet tar emot på lyckade inloggningar.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]
Under **hantera**väljer **användarflöden**.

Klicka på +**nytt användarflöde** överst på bladet.

Under **Välj en användarflödestyp**väljer **alla**, och välj sedan versionen av **logga in** du vill använda.

Den **namn** avgör inloggning userjourney-namnet används av ditt program. Ange till exempel **SiIn**.

Under **identitetsprovidrar**, Välj ett alternativ. Du kan också välja leverantörer via sociala om redan har konfigurerats. Klicka på **OK**.

Under **Programanspråk**, klickar du på **visa fler**.

I den **returnerar anspråk** kolumnen välja anspråk som du vill ska returneras i de token som skickas tillbaka till programmet efter en lyckad inloggning. Välj till exempel **Visningsnamn**, **Identitetsprovider**, **Postnummer** och **Användarobjekt-id**. Klicka på **OK**.

Klicka på **Skapa**. Observera att användarflödet nyss skapade visas som **B2C_1_SiIn** (den **B2C\_1\_**  fragment läggs till automatiskt).

Klicka på **kör användarflödet**.

Välj **Contoso B2C-app** i den **programmet** listrutan och `https://localhost:44321/` i den **svars-URL** utskriftsjobb.

Klicka på **kör användarflödet**. En ny webbläsarflik öppnas och du kan gå igenom inloggningsupplevelsen för ditt program.

> [!NOTE]
> Det tar till en minut för användare kan du skapa flöden och uppdateringar ska börja gälla.
>