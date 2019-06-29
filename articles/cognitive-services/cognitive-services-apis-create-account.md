---
title: Skapa ett Cognitive Services-konto i Azure portal
titlesuffix: Azure Cognitive Services
description: 'Så här skapar du ett konto för Azure Cognitive Services API: er i Azure-portalen.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: aahi
ms.openlocfilehash: b857ee0395c447c8699b8f6a812853528812a7bd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445858"
---
# <a name="create-a-cognitive-services-account-using-the-azure-portal"></a>Skapa ett Cognitive Services-konto med Azure portal

I den här snabbstarten lär du dig att registrera dig för Azure Cognitive Services och skapa ett konto som har en prenumeration för en tjänst eller flera tjänster. De här tjänsterna representeras av Azure [resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), vilket gör det möjligt att ansluta till en eller fler av Azure-API: er för Cognitive Services.

## <a name="prerequisites"></a>Förutsättningar

* En giltig Azure-prenumeration. [Skapa ett konto](https://azure.microsoft.com/free/) utan kostnad.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Skapa en ny Azure Cognitive Services-resurs

Du måste ha en Azure-resursgrupp innan du skapar en resurs. Varje Cognitive Services-konto (och dess associerade Azure-resurs) måste tillhöra en Azure-resursgrupp. När du skapar ett konto har du möjlighet att antingen skapa en ny resursgrupp eller Använd en befintlig. Den här artikeln visar hur du skapar en ny resursgrupp.

1. Logga in på den [Azure-portalen](https://portal.azure.com), och klicka på **+ skapa en resurs**.

    ![Välj API: er för Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Du kan hitta tillgängliga kognitiva tjänster med på följande sätt:
    * Använda sökfältet och ange namnet på den tjänst du vill prenumerera på.
        * Om du vill skapa en resurs för en prenumeration som flera tjänster, ange **Cognitive Services** i sökningen och väljer den **Cognitive Services** resurs.

        ![Sök efter Cognitive Services](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * Om du vill se alla tillgängliga kognitiva tjänster, **AI + Maskininlärning**under **Azure Marketplace**. Om du inte ser den tjänst som du är intresserad av, klickar du på **se alla** och bläddra till **Cognitive Services**. Klicka på **mer** att visa hela katalogen med Cognitive Services API: er.
    
        ![Välj API: er för Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. På den **skapa** anger du följande information:

    > [!IMPORTANT]
    > Kom ihåg dina Azure-plats som du kan behöva den när du anropar Azure Cognitive Services.

    |    |    |
    |--|--|
    | **Namn** | Ett beskrivande namn för din resurs för cognitive services. Vi rekommenderar att du använder ett beskrivande namn, till exempel *MyCognitiveServicesAccount*. |
    | **Prenumeration** | Välj en av dina tillgängliga Azure-prenumerationer. |
    | **Location** | Platsen för din cognitive service-instans. Olika platser kan introducera svarstid, men har ingen inverkan på runtime tillgängligheten för din resurs. |
    | **prisnivå** | Kostnaden för Cognitive Services-kontot beror på de alternativ du väljer och din användning. Mer information finns i API: et [prisinformation](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Resursgrupp** | Den [Azure-resursgrupp](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) som innehåller din resurs för Cognitive Services. Du kan skapa en ny grupp eller lägga till den i en befintlig grupp. |

    ![Resursen skapa-skärmen](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-subscription"></a>Hämta nycklar för din prenumeration

När du har skapat din resurs kommer du åt den från instrumentpanelen för Azure om du har Fäst. I annat fall hittar du den i **resursgrupper**. När du har valt din resurs, kan du hämta nycklar genom att välja **nycklar** under **resurshantering**.

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Ta bort resursgruppen raderas även andra resurser som är associerade med resursgruppen.

Ta bort en resursgrupp med Azure-portalen:

1. I Azure-portalen expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer **Resursgrupper** för att visa listan över dina resursgrupper.
2. Leta upp resursgruppen som ska ta bort och högerklicka på knappen Mer (...) till höger om listan.
3. Välj **Ta bort resursgrupp** och bekräfta.

## <a name="see-also"></a>Se också

* [Autentisera begäranden till Azure Cognitive Services](authentication.md)
* [Vad är Azure Cognitive Services?](Welcome.md)
* [Stöd för naturligt språk](language-support.md)
* [Stöd för docker-behållare](cognitive-services-container-support.md)
