---
title: ta med fil
description: ta med fil
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 09/19/2018
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: 1887efd741f4779a5186707d60b27ca66fc3c06f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51284007"
---
1. I [Azure-portalen](https://portal.azure.com)öppnar du **Azure Active Directory** från den vänstra navigeringspanelen och öppnar sedan fönstret **Egenskaper**. Kopiera **katalog-ID:t** till en temporär fil. Du använder det här värdet för att konfigurera exempelprogrammet i följande avsnitt.

    ![ID för Azure Active Directory-katalog](./media/digital-twins-permissions/aad-app-reg-tenant.png)

1. Öppna fönstret **Appregistreringar** och klicka sedan på knappen **Ny programregistrering**.
    
    ![Azure Active Directory-appregistrering, ny](./media/digital-twins-permissions/aad-app-reg-start.png)

1. Ge appregistreringen ett eget namn i fältet **Namn**. Ställ in **Programtyp** på **_Intern_** och  **	Omdirigerings-URI** som **_https://microsoft.com_**. Klicka på **Skapa**.

    ![Azure Active Directory-appregistrering, skapa](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Öppna den registrerade appen och kopiera värdet för fältet **Program-ID** till en temporär fil. Det här värdet identifierar din Azure Active Directory-app. Du använder program-ID:t för att konfigurera exempelprogrammet i följande avsnitt.

    ![ID för Azure Active Directory-programmet](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Öppna appregistreringsfönstret och klicka på **Inställningar** >  **	Nödvändiga behörigheter**:
    - Klicka på **Lägg till** uppe till vänster att öppna fönstret **Lägg till API-åtkomst**.
    - Klicka på **Välj ett API** och sök efter **Azure Digital Twins**. Om sökningen inte hittar API:et söker du efter **Azure Smart Spaces** i stället.
    - Välj **Azure Digital Twins (Azure Smart Spaces-tjänsten)** och klicka på **Välj**.
    - Klicka på **Välj behörigheter**. Markera rutan för **läs-/skrivbehörighet** för delegerade behörigheter och klicka på **Välj**.
    - Klicka på **Klar** i fönstret **Lägg till API-åtkomst**.
    - I fönstret **Nödvändiga behörigheter** klickar du på knappen **Bevilja behörigheter** och godkänner bekräftelsen som visas.

       ![Azure Active Directory-appregistrering, lägg till api](./media/digital-twins-permissions/aad-app-req-permissions.png)
