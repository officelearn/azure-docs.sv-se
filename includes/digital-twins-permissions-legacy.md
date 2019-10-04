---
title: ta med fil
description: ta med fil
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 08/16/2019
ms.custom: include file
ms.openlocfilehash: 690ff402c632bfc28f1a9a218677e9772f5a3200
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949922"
---
1. I [Azure-portalen](https://portal.azure.com)öppnar du **Azure Active Directory** från den vänstra panelen och öppnar sedan fönstret **Egenskaper**. Kopiera **katalog-ID:t** till en temporär fil. Du använder det här värdet för att konfigurera exempelprogrammet i nästa avsnitt.

    [![Azure Active Directory Directory-ID](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png#lightbox)

1. Öppna **Azure Active Directory** i den vänstra rutan i [Azure Portal](https://portal.azure.com)och öppna sedan fönstret **Appregistreringar (bakåtkompatibelt)** . Välj knappen **ny program registrering** .

1. Ge appregistreringen ett eget namn i rutan **Namn**. Ställ in **Programtyp** på **Intern** och **Omdirigerings-URI** som `https://microsoft.com`. Välj **Skapa**.

    [![Create-fönstret](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)](./media/digital-twins-permissions-legacy/aad-app-reg-create.png#lightbox)

1.  Öppna den registrerade appen och kopiera värdet för fältet **Program-ID** till en temporär fil. Det här värdet identifierar din Azure Active Directory-app. Du använder program-ID:t för att konfigurera exempelprogrammet i följande avsnitt.

    [![Azure Active Directory program-ID](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png#lightbox)

1. Öppna fönstret för registrering av din app. Välj **Inställningar** > **Nödvändiga behörigheter** och sedan:

   a. Välj **Lägg till** längst upp till vänster för att öppna fönstret **Lägg till API-åtkomst**.

   b. Markera **Välj ett API** och sök efter **Azure Digital Twins**. Om sökningen inte hittar API:et söker du efter **Azure Smart Spaces** i stället.

   c. Välj **Azure Digital Twins (Azure Smart Spaces-tjänsten)** och sedan **Välj**.

   d. Välj **Välj behörigheter**. Markera kryssrutan för **läs-/skrivbehörighet** för delegerade behörigheter och markera **Välj**.

   e. Markera **Klar** i fönstret **Lägg till API-åtkomst**.

   f. I fönstret **nödvändiga behörigheter** väljer du knappen **bevilja behörigheter** och godkänner bekräftelsen som visas. Kontakta administratören om behörigheten inte beviljas för detta API.

      [@no__t 1Required behörighets fönstret](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png#lightbox)

 
