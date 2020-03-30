---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184373"
---
Om du vill registrera ett program i din Azure AD B2C-klient kan du använda den aktuella **programupplevelsen** eller vår nya upplevelse av enhetliga **appregistreringar (förhandsversion).** [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Program](#tab/applications/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **Katalog + Prenumeration** i portalverktygsfältet och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Sök efter och välj **Azure Active Directory**i Azure-portalen .
1. Under **Hantera**väljer du **Appregistreringar (Äldre)**.
1. Välj **Ny programregistrering**.
1. Ange ett namn på programmet. Till exempel *managementapp1*.
1. För **programtyp**väljer du **Webbapp/API**.
1. Ange en giltig URL i **inloggnings-URL**. Till exempel `https://localhost`. Slutpunkten behöver inte kunna nås, men måste vara en giltig URL.
1. Välj **Skapa**.
1. Registrera **program-ID:t** som visas på **översiktssidan för registrerade appar.** Du använder det här värdet i ett senare steg.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **Katalog + Prenumeration** i portalverktygsfältet och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Sök efter och välj **Azure AD B2C**i Azure-portalen .
1. Välj **Appregistreringar (förhandsgranskning)** och välj sedan **Ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *managementapp1*.
1. Välj **Konton i den här organisationskatalogen .**
1. Avmarkera kryssrutan Bevilja *administratörsgodkännande för att öppna och offline_access behörigheter* under **Behörigheter.**
1. Välj **Registrera**.
1. Registrera **programmets (klient)-ID** som visas på sidan programöversikt. Du använder det här värdet i ett senare steg.