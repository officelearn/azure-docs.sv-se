---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/30/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: 17c0213d63879687e9c6d5f8dca06b9113c44af8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456908"
---
Om du vill aktivera endast registrera dig för ditt program kan du använda en **registrering** användarflödet. Det här användarflödet beskriver hur kunder registreringen går till och innehållet i de token som programmet tar emot på genomförda registreringar.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Under **hantera**väljer **användarflöden**.

Klicka på +**nytt användarflöde** överst på bladet.

Under **Välj en användarflödestyp**väljer **alla**, och välj sedan versionen av **registrera** du vill använda.

Den **namn** avgör registrering userjourney-namnet används av ditt program. Ange till exempel **SiUp**.

Under **identitetsprovidrar**väljer **e-postregistrering**. Du kan också välja leverantörer via sociala nätverk om det redan har konfigurerats.

Under **användarattribut och anspråk**, klickar du på **visa fler**.

I den **samla in attributet** kolumnen väljer attribut som du vill samla in från konsumenten under registreringen. Välj till exempel **Land/region**, **Visningsnamn** och **Postnummer**.

I den **returnerar anspråk** kolumnen välja anspråk som du vill ska returneras i de token som skickas tillbaka till programmet efter en genomförd registrering. Välj till exempel **Visningsnamn**, **Identitetsprovidrar**, **Postnummer**, **Ny användare** och **Användarobjekt-id**.

Klicka på **OK**.

Klicka på **Skapa**. Användarflödet skapade visas som **B2C_1_SiUp** (den **B2C\_1\_**  fragment läggs till automatiskt).

Klicka på **kör användarflödet**.

Välj **Contoso B2C-app** i den **programmet** listrutan och `https://localhost:44321/` i den **svars-URL** utskriftsjobb.

Klicka på **kör användarflödet**. En ny webbläsarflik öppnas och du kan gå igenom användarupplevelsen av registrering för ditt program.

> [!NOTE]
> Det tar till en minut för användare kan du skapa flöden och uppdateringar ska börja gälla.
>

