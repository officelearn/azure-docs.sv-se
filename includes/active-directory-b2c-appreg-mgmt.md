---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 10/01/2019
ms.author: marsma
ms.openlocfilehash: 67a0832f868fecd47983aa8cddff9cdf139b3f2a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702166"
---
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
