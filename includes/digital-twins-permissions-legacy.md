---
title: ta med fil
description: ta med fil
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: 9f4bf6fb92b590e274e8880b5f900e5469f85727
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012296"
---
1. I [Azure-portalen](https://portal.azure.com)öppnar du **Azure Active Directory** från den vänstra panelen och öppnar sedan fönstret **Egenskaper**. Kopiera **katalog-ID:t** till en temporär fil. Du använder det här värdet för att konfigurera exempelprogrammet i nästa avsnitt.

    ![ID för Azure Active Directory-katalog](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)

1. Öppna **Azure Active Directory** i den vänstra rutan i [Azure Portal](https://portal.azure.com)och öppna sedan fönstret **Appregistreringar (bakåtkompatibelt)** . Välj knappen **ny program registrering** .

1. Ge appregistreringen ett eget namn i rutan **Namn**. Ställ in **Programtyp** på **Intern** och  **	Omdirigerings-URI** som `https://microsoft.com`. Välj **Skapa**.

    ![Skapa fönster](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)

1.  Öppna den registrerade appen och kopiera värdet för fältet **Program-ID** till en temporär fil. Det här värdet identifierar din Azure Active Directory-app. Du använder program-ID:t för att konfigurera exempelprogrammet i följande avsnitt.

    ![ID för Azure Active Directory-programmet](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)

1. Öppna fönstret för registrering av din app. Välj **Inställningar** > **Nödvändiga behörigheter** och sedan:

   a. Välj **Lägg till** längst upp till vänster för att öppna fönstret **Lägg till API-åtkomst**.

   b. Markera **Välj ett API** och sök efter **Azure Digital Twins**. Om sökningen inte hittar API:et söker du efter **Azure Smart Spaces** i stället.

   c. Välj **Azure Digital Twins (Azure Smart Spaces-tjänsten)** och sedan **Välj**.

   d. Välj **Välj behörigheter**. Markera kryssrutan för **läs-/skrivbehörighet** för delegerade behörigheter och markera **Välj**.

   e. Markera **Klar** i fönstret **Lägg till API-åtkomst**.

   f. I fönstret **Nödvändiga behörigheter** väljer du knappen **Bevilja behörigheter** och godkänner bekräftelsen som visas. Kontakta administratören om behörigheten inte beviljas för detta API.

      ![Fönstret Nödvändiga behörigheter](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)

 