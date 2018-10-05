---
title: Behållare | Microsoft Docs
description: Steg för att publicera en behållaravbildning.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5653e4b2ec9aa5e21a107611b9d4142168630d4a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811601"
---
<a name="publishing-a-container-image-in-the-cloud-partner-portal"></a>Publicera en Behållaravbildning i partnerportalen i molnet
========================================================

Den här artikeln beskriver hur du publicerar en ny behållaravbildning i partnerportalen i molnet.

Använd följande steg för att publicera en ny behållaravbildning.

1. Välj **nytt erbjudande** och välj sedan **behållare**

    ![Välj behållare alternativ för nytt erbjudande](media/cpp-containers-guide/azure-container-offer.png)

2. På fliken erbjuder inställningar under erbjuder identitet, ange den **erbjudande-ID**, **Publicerings-ID**, och **namn**.

    ![Erbjud identitet](media/cpp-containers-guide/containers-offer-settings.png)

3. Välj fliken SKU: er **nya SKU: N**.
    >[!NOTE]
    >Du kan lägga till mer än en SKU.

    ![Nya SKU-fråga](media/cpp-containers-guide/containers-sku-settings.png)

4. Ange information om SKU: N och behållare. Varje SKU motsvarar en behållaravbildning. Det finns två delar till en SKU:

    -   SKU-metadata
    -   Behållare för metadata

    SKU-metadata innehåller informationen som visas för behållaravbildningarna.

    ![SKU-metadata](media/cpp-containers-guide/containers-sku-details.png)

    Behållare-metadata innehåller referensinformation om information om databasen din avbildning i Azure container registry (ACR). Azure Marketplace kopierar den här avbildningen till registret offentliga marknaden och görs tillgänglig för kunder efter certifiering. Hanteras alla förfrågningar från Azure-användare att använda en behållaravbildning från Marketplace container registry.

   ![Behållare för metadata](media/cpp-containers-guide/containers-image-repository.png)

    Avbildningsdetaljerna som databasen i föregående skärmdumpen innehåller följande fält:

    -   **Prenumerations-ID** -prenumerations-id för Azure där ACR-registret finns.
    -   **Resursgruppens namn** -resursgruppens namn för ACR-registret.
    -   **Registernamn** -namnet för den ACR-registret.
    -   **Namnet på lagringsplatsen** -namnet på lagringsplatsen. När kan det här värdet inte ändras senare. Detta ska ges ett unikt namn så att inga andra erbjudanden under ditt konto har samma namn.
    -   **Användarnamn** -användarnamnet som är associerade med ACR (administratörsanvändarnamn).
    -   **Lösenord** -lösenordet som associeras med ACR.

    >[!NOTE]
    >Det användarnamn och lösenord krävs för att se till att partner har åtkomst till ACR som nämns i publiceringsprocessen.

    När du publicerar en behållaravbildning, kan du även ange en eller flera taggar. Partner kan också ge SHA sammandrag förutom en bildtagg. Var noga med att lägga till en **testtagg** i avbildningen så att du kan identifiera avbildningen under testningen.

5. Lägg till innehåll för din marknadsföring på fliken Marketplace.

    ![Marketplace-informationen](media/cpp-containers-guide/containers-marketplace-tab.png)

6. I fliken stöd anger du dina tekniska kontakt- och information om kundsupport.

   ![Supportinformation](media/cpp-containers-guide/containers-support-tab.png)

7. Välj **publicera** att publicera erbjudandet. När du har valt publicera visas en tidslinje som visar steg som ingår för att publicera din behållaravbildning.
