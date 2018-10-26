---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 19e7c919345c0f56b274737840f8150f7d710501
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133206"
---
Om du vill aktivera endast logga in på ditt program kan du använda en **inloggning** princip. Den här principen beskriver hur kunder går till för inloggning och innehållet i de token som programmet tar emot på lyckade inloggningar.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]
Klicka på **inloggningsprinciper**.

Klicka på **+Lägg till** överst på bladet.

**Namn** är namnet på programmets inloggningsprincip. Ange till exempel **SiIn**.

Klicka på **Identitetsprovidrar** och välj **Inloggning från lokalt konto**. Du kan också välja leverantörer via sociala nätverk om det redan har konfigurerats. Klicka på **OK**.

Klicka på **Programanspråk**. Här kan du välja anspråk som du vill ska returneras i de token som skickas tillbaka till programmet efter en genomförd inloggning. Välj till exempel **Visningsnamn**, **Identitetsprovider**, **Postnummer** och **Användarobjekt-id**. Klicka på **OK**.

Klicka på **Skapa**. Den nya principen visas som **B2C_1_SiIn** (fragmentet **B2C\_1\_** läggs till automatiskt) på bladet **Inloggningsprinciper**.

Öppna principen genom att klicka på **B2C_1_SiIn**.

Välj **Contoso B2C-app** i listrutan **Program** och `https://localhost:44321/` i listrutan **Svarswebbadress/omdirigerings-URI**.

Klicka på **Kör nu**. En ny webbläsarflik öppnas och du kan gå igenom inloggningsupplevelsen för ditt program.

> [!NOTE]
> Det kan ta någon minut att skapa en princip och innan uppdateringarna börjar gälla.
>