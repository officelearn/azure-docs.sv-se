---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Azure Container instance-resurs.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 09957461fd92114d1181a570550ee1a189edd8ea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876481"
---
## <a name="create-an-azure-container-instance-resource"></a>Skapa en Azure Container instance-resurs

1. Gå till sidan [skapa](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) för container instances.

2. På fliken **grundläggande** anger du följande information:

    |Inställning|Värde|
    |--|--|
    |Prenumeration|Välj din prenumeration.|
    |Resursgrupp|Välj en tillgänglig resurs grupp eller skapa en ny, till exempel `cognitive-services`.|
    |Containerns namn|Ange ett namn som `cognitive-container-instance`. Namnet måste innehålla nedre CAPS.|
    |Plats|Välj en region för distribution.|
    |Avbildningstyp|Om behållar avbildningen lagras i ett behållar register som inte kräver `Public`autentiseringsuppgifter väljer du. Om åtkomsten till behållar avbildningen kräver `Private`autentiseringsuppgifter väljer du. Se [behållar databaser och avbildningar](../../cognitive-services-container-support.md#container-repositories-and-images) för information om huruvida behållar `Public` avbildningen `Private` är eller ("offentlig för hands version). |
    |Avbildningens namn|Ange plats för Cognitive Services behållare. Platsen är det som används som argument till `docker pull` kommandot. Referera till [behållar databaserna och avbildningarna](../../cognitive-services-container-support.md#container-repositories-and-images) för de tillgängliga avbildnings namnen och deras motsvarande lagrings plats.<br><br>Avbildningens namn måste vara fullständigt kvalificerade och ange tre delar. Först, behållar registret, sedan databasen, sist avbildningens namn: `<container-registry>/<repository>/<image-name>`.<br><br>Här är ett exempel `mcr.microsoft.com/azure-cognitive-services/keyphrase` som representerar extrahering av diskussionsämne avbildningen i Microsoft container Registry under Azure Cognitive Services-lagringsplatsen. Ett annat exempel är `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` , som representerar tal till text bild i Microsoft-lagringsplatsen för för hands versionen av container-registret. |
    |OS-typ|`Linux`|
    |Storlek|Ändra storlek till föreslagna rekommendationer för din speciella kognitiva tjänst behållare:<br>2 CPU-kärnor<br>4 GB

3. På fliken **nätverk** anger du följande information:

    |Inställning|Värde|
    |--|--|
    |Portar|Ange TCP-port till `5000`. Exponerar behållaren på port 5000.|

4. På fliken **Avancerat** anger du de **miljövariabler** som krävs för behållarens fakturerings inställningar för Azure Container instance-resursen:

    | Nyckel | Värde |
    |--|--|
    |`apikey`|Kopieras från sidan **nycklar** i resursen. Det är en sträng med 32 alfanumeriska tecken utan blank steg eller bindestreck `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Kopieras från **översikts** sidan för resursen.|
    |`eula`|`accept`|

5. Klicka på **Granska och skapa**
6. När verifieringen är klar klickar du på **skapa** för att slutföra processen som skapas
7. När resursen har distribuerats är den klar
