---
title: Skapa en Cognitive Services resurs i Azure Portal
titleSuffix: Azure Cognitive Services
description: Kom igång med Azure Cognitive Services genom att skapa och prenumerera på en resurs i Azure Portal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: c7db2b4d49e3b9297c32d2e11ffe7c7702c17544
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274579"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Skapa en Cognitive Services resurs med hjälp av Azure Portal

Använd den här snabb starten för att skapa en Azure Cognitive Services-resurs med hjälp av Azure Portal. När du har skapat en Cognitive Services resurs får du en slut punkt och en nyckel som du kan använda för att autentisera dina program.

## <a name="prerequisites"></a>Förutsättningar

* En giltig Azure-prenumeration – [skapa en kostnads fri](https://azure.microsoft.com/free/).

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Skapa en ny Azure Cognitive Services-resurs

Innan du skapar en Cognitive Services resurs måste du ha en Azure-resurs grupp som innehåller resursen. När du skapar en ny resurs har du möjlighet att antingen skapa en ny resurs grupp eller använda en befintlig. Den här artikeln visar hur du skapar en ny resurs grupp.

1. Logga in på [Azure Portal](https://portal.azure.com)och klicka på **+ skapa en resurs**.

    ![Välj API:er för Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Som tidigare nämnts kan du skapa en Cognitive Services-resurs på två sätt – med hjälp av en resurs för flera tjänster eller en resurs för en enskild tjänst.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Resurs för flera tjänster](#tab/multiservice)

    Om du vill skapa en resurs för flera tjänster anger du **Cognitive Services** i Sök fältet.

    ![Sök efter Cognitive Services](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    På sidan Cognitive Services väljer du **skapa**.

    ![Skapa Cognitive Services konto](media/cognitive-services-apis-create-account/azurecogservsearchmulti-2.png)

    #### <a name="single-service-resourcetabsingleservice"></a>[Resurs för enskild tjänst](#tab/singleservice)

    Om du vill se alla tillgängliga kognitiva tjänster väljer du **AI + Machine Learning**under **Azure Marketplace**. Om du inte ser den tjänst som du är intresse rad av, klickar du på **Visa alla** och bläddrar till **Cognitive Services**. Klicka på **Visa mer** om du vill visa hela katalogen med Cognitive Services.

    När du är på den tjänst som du är intresse rad av, klickar du på **skapa**.
    
    ![Välj API:er för Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

    ***
3. Ange följande information på sidan **skapa** :

    #### <a name="multi-service-resourcetabmultiservice"></a>[Resurs för flera tjänster](#tab/multiservice)

    |    |    |
    |--|--|
    | **Namn** | Ett beskrivande namn för din resurs för kognitiva tjänster. Till exempel *MyCognitiveServicesResource*. |
    | **Prenumeration** | Välj en av dina tillgängliga Azure-prenumerationer. |
    | **Location** | Platsen för din kognitiva tjänst instans. Olika platser kan orsaka svars tid, men har ingen inverkan på resursens tillgänglighet för körning. Kom ihåg din Azure-plats, som du kan behöva när du anropar Azure-Cognitive Services. |
    | **prisnivå** | Kostnaden för ditt Cognitive Services-konto beror på vilka alternativ du väljer och din användning. Mer information finns i [pris informationen](https://azure.microsoft.com/pricing/details/cognitive-services/)för API.
    | **Resursgrupp** | Den Azure-resurs grupp som ska innehålla din Cognitive Services-resurs. Du kan skapa en ny grupp eller lägga till den i en befintlig grupp. |

    ![Skärmen skapa resurs](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Klicka på **Skapa**.

    #### <a name="single-service-resourcetabsingleservice"></a>[Resurs för enskild tjänst](#tab/singleservice)

    |    |    |
    |--|--|
    | **Namn** | Ett beskrivande namn för din resurs för kognitiva tjänster. Till exempel *TextAnalyticsResource*. |
    | **Prenumeration** | Välj en av dina tillgängliga Azure-prenumerationer. |
    | **Location** | Platsen för din kognitiva tjänst instans. Olika platser kan orsaka svars tid, men har ingen inverkan på resursens tillgänglighet för körning. Kom ihåg din Azure-plats, som du kan behöva när du anropar Azure-Cognitive Services. |
    | **prisnivå** | Kostnaden för ditt Cognitive Services-konto beror på vilka alternativ du väljer och din användning. Mer information finns i [pris informationen](https://azure.microsoft.com/pricing/details/cognitive-services/)för API.
    | **Resursgrupp** | Den Azure-resurs grupp som ska innehålla din Cognitive Services-resurs. Du kan skapa en ny grupp eller lägga till den i en befintlig grupp. |

    ![Skärmen skapa resurs](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Klicka på **Skapa**.

    ***

## <a name="get-the-keys-for-your-resource"></a>Hämta nycklar för din resurs

När resursen har skapats visas ett popup-meddelande längst upp till höger på skärmen. Klicka på **gå till resurs** i meddelandet för att se den kognitiva tjänst resurs som du har skapat. 

![Gå till kognitiva tjänst resurser](media/cognitive-services-apis-create-account/cog-serv-go-to-resource.png)

Från snabb starts fönstret som öppnas kan du komma åt din slut punkt och nyckel.

![Hämta nyckel och slut punkt](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Pris nivåer och fakturering

Pris nivåerna (och den mängd du debiteras) baseras på antalet transaktioner som du skickar med hjälp av autentiseringsinformationen. Varje pris nivå anger:
* maximalt antal tillåtna transaktioner per sekund (TPS).
* tjänst funktioner som Aktiver ATS inom pris nivån.
* Kostnaden för ett fördefinierat antal transaktioner. Om du fortsätter över den här mängden kommer du att få en extra avgift som anges i [pris informationen](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) för din tjänst.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen tas även andra resurser som ingår i gruppen bort.

Ta bort en resursgrupp med Azure-portalen:

1. I Azure-portalen expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer **Resursgrupper** för att visa listan över dina resursgrupper.
2. Leta upp resurs gruppen som du vill ta bort och högerklicka på knappen mer (...) på höger sida av listan.
3. Välj **Ta bort resursgrupp** och bekräfta.

## <a name="see-also"></a>Se också

* [Autentisera begär anden till Azure Cognitive Services](authentication.md)
* [Vad är Azure Cognitive Services?](Welcome.md)
* [Stöd för naturligt språk](language-support.md)
* [Stöd för Docker-behållare](cognitive-services-container-support.md)
