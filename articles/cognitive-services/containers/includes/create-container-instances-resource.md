---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Azure-behållarinstansresurs.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 09957461fd92114d1181a570550ee1a189edd8ea
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876481"
---
## <a name="create-an-azure-container-instance-resource"></a>Skapa en Azure Container Instance-resurs

1. Gå till sidan [Skapa](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) för behållarinstanser.

2. På fliken **Grunderna** anger du följande information:

    |Inställning|Värde|
    |--|--|
    |Prenumeration|Välj din prenumeration.|
    |Resursgrupp|Markera den tillgängliga resursgruppen eller skapa `cognitive-services`en ny, till exempel .|
    |Containerns namn|Ange ett namn `cognitive-container-instance`som . Namnet måste vara i nedre lock.|
    |Location|Välj en region för distribution.|
    |Avbildningstyp|Om behållaravbildningen lagras i ett behållarregister som `Public`inte kräver autentiseringsuppgifter väljer du . Om åtkomst till behållaravbildningen kräver autentiseringsuppgifter väljer du `Private`. Mer information om huruvida behållaravbildningen finns `Public` `Private` i [behållardatabaser och bilder](../../cognitive-services-container-support.md#container-repositories-and-images) eller inte ("Offentlig förhandsgranskning"). |
    |Avbildningens namn|Ange behållarens plats för Cognitive Services-behållaren. Platsen är vad som används som `docker pull` ett argument till kommandot. Se [behållardatabaser och avbildningar](../../cognitive-services-container-support.md#container-repositories-and-images) för tillgängliga bildnamn och motsvarande databas.<br><br>Bildnamnet måste vara fullt kvalificerat och ange tre delar. Först behållarregistret, sedan databasen, slutligen avbildningsnamnet: `<container-registry>/<repository>/<image-name>`.<br><br>Här är ett `mcr.microsoft.com/azure-cognitive-services/keyphrase` exempel, skulle representera nyckelfrasextrahering avbildningen i Microsoft Container Registry under Azure Cognitive Services-databasen. Ett annat `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` exempel är, som skulle representera avbildningen Tal till text i Microsoft-databasen i containerförhandsgranskningsbehållarens register. |
    |TYP AV OS|`Linux`|
    |Storlek|Ändra storlek till de föreslagna rekommendationerna för din specifika Cognitive Service-behållare:<br>2 CPU-kärnor<br>4 GB

3. Ange följande på fliken **Nätverk:**

    |Inställning|Värde|
    |--|--|
    |Portar|Ställ in TCP-porten på `5000`. Exponerar behållaren på port 5000.|

4. På fliken **Avancerat** anger du de **miljövariabler** som krävs för behållarfaktureringsinställningarna för Azure Container Instance-resursen:

    | Nyckel | Värde |
    |--|--|
    |`apikey`|Kopierad från sidan **Nycklar** för resursen. Det är en 32 alfanumerisk teckensträng utan `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`blanksteg eller streck.|
    |`billing`|Kopierad från sidan **Översikt** för resursen.|
    |`eula`|`accept`|

5. Klicka på **Granska och skapa**
6. När valideringen har gått klickar du på **Skapa** för att slutföra skapandeprocessen
7. När resursen har distribuerats är den klar
