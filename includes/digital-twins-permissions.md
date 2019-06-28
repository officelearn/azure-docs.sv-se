---
title: ta med fil
description: ta med fil
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 06/26/2019
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: f03ee57867185eac946be5b596477bf942643587
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67459202"
---
>[!NOTE]
>Tänk på att äldre AAD appregistreringen som används här tas ur bruk snart. Det här avsnittet kommer att uppdateras när Azure Digital Twins är helt integrerade med den [ny AAD-appregistrering](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Under tiden kan du experimentera med ny appregistrering för AAD. Observera att du kommer behöva använda [offentlig klient (mobile och Desktop)](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types#mobile-and-native-apps) för den *omdirigerings-URI*. 

1. I [Azure-portalen](https://portal.azure.com)öppnar du **Azure Active Directory** från den vänstra panelen och öppnar sedan fönstret **Egenskaper**. Kopiera **katalog-ID:t** till en temporär fil. Du använder det här värdet för att konfigurera exempelprogrammet i nästa avsnitt.

    ![ID för Azure Active Directory-katalog](./media/digital-twins-permissions/aad-app-reg-tenant.png)

1. I den [Azure-portalen](https://portal.azure.com)öppnar **Azure Active Directory** från den vänstra rutan och sedan öppna den **appregistreringar (äldre)** fönstret. Välj den **ny programregistrering** knappen.

1. Ge appregistreringen ett eget namn i rutan **Namn**. Ställ in **Programtyp** på **Intern** och  **	Omdirigerings-URI** som `https://microsoft.com`. Välj **Skapa**.

    ![Skapa fönster](./media/digital-twins-permissions/aad-app-reg-create.png)

1.  Öppna den registrerade appen och kopiera värdet för fältet **Program-ID** till en temporär fil. Det här värdet identifierar din Azure Active Directory-app. Du använder program-ID:t för att konfigurera exempelprogrammet i följande avsnitt.

    ![ID för Azure Active Directory-programmet](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Öppna fönstret för registrering av din app. Välj **Inställningar** > **Nödvändiga behörigheter** och sedan:

   a. Välj **Lägg till** längst upp till vänster för att öppna fönstret **Lägg till API-åtkomst**.

   b. Markera **Välj ett API** och sök efter **Azure Digital Twins**. Om sökningen inte hittar API:et söker du efter **Azure Smart Spaces** i stället.

   c. Välj **Azure Digital Twins (Azure Smart Spaces-tjänsten)** och sedan **Välj**.

   d. Välj **Välj behörigheter**. Markera kryssrutan för **läs-/skrivbehörighet** för delegerade behörigheter och markera **Välj**.

   e. Markera **Klar** i fönstret **Lägg till API-åtkomst**.

   f. I fönstret **Nödvändiga behörigheter** väljer du knappen **Bevilja behörigheter** och godkänner bekräftelsen som visas. Kontakta administratören om behörigheten inte beviljas för detta API.

      ![Fönstret Nödvändiga behörigheter](./media/digital-twins-permissions/aad-app-req-permissions.png)

 