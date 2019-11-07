---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 77c3aee4fdb5f8b2ee7ed83f92917573ad6ad529
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643594"
---
Om du vill registrera ett program i din Azure AD B2C klient kan du använda den aktuella **program** upplevelsen eller vår nya enhetliga **Appregistreringar (förhands granskning)** . [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applicationstabapplications"></a>[Program](#tab/applications/)

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure Active Directory** (*inte* Azure AD B2C). Eller Välj **alla tjänster** och Sök sedan efter och välj **Azure Active Directory**.
1. Under **Hantera**väljer du **Appregistreringar (bakåtkompatibelt)** .
1. Välj **Ny programregistrering**.
1. Ange ett namn på programmet. Till exempel *managementapp1*.
1. För **program typ**väljer du **webbapp/API**.
1. Ange en giltig URL i **inloggnings-URL: en**. Till exempel `https://localhost`. Slut punkten behöver inte vara nåbar, men måste vara en giltig URL.
1. Välj **Skapa**.
1. Registrera **program-ID: t** som visas på sidan Översikt över **registrerade appar** . Du använder det här värdet i ett senare steg.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Appregistreringar (för hands version)](#tab/app-reg-preview/)

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar (för hands version)** och välj sedan **ny registrering**.
1. Ange ett **namn** för programmet. Till exempel *managementapp1*.
1. Välj **konton endast i den här organisations katalogen**.
1. Under **behörigheter**avmarkerar du kryss rutan *bevilja administratörs medgivande till OpenID och offline_access behörigheter* .
1. Välj **Registrera**.
1. Registrera **program-ID: t (Client)** som visas på program översikts sidan. Du använder det här värdet i ett senare steg.