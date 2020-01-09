---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en Azure Container instance-resurs.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 18f4edf5cc63a448779423cc1b302130b4b80724
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692531"
---
## <a name="create-an-azure-container-instance-resource"></a>Skapa en Azure Container instance-resurs

1. Gå till sidan [skapa](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) för container instances.

2. På fliken **grundläggande** anger du följande information:

    |Inställning|Värde|
    |--|--|
    |Prenumeration|Välj din prenumeration.|
    |Resursgrupp|Välj den tillgängliga resurs gruppen eller skapa en ny som `cognitive-services`.|
    |Containerns namn|Ange ett namn som `cognitive-container-instance`. Namnet måste innehålla nedre CAPS.|
    |Location|Välj en region för distribution.|
    |Avbildningstyp|Om behållar avbildningen lagras i ett behållar register som inte kräver autentiseringsuppgifter väljer du `Public`. Om åtkomsten till behållar avbildningen kräver autentiseringsuppgifter väljer du `Private`. Se [behållar databaser och avbildningar](../../cognitive-services-container-support.md#container-repositories-and-images) för information om huruvida behållar avbildningen är `Public` eller `Private` ("offentlig för hands version"). |
    |Avbildningens namn|Ange plats för Cognitive Services behållare. Platsen är det som används som ett argument för kommandot `docker pull`. Referera till [behållar databaserna och avbildningarna](../../cognitive-services-container-support.md#container-repositories-and-images) för de tillgängliga avbildnings namnen och deras motsvarande lagrings plats.<br><br>Avbildningens namn måste vara fullständigt kvalificerade och ange tre delar. Först, behållar registret, sedan databasen, slutligen avbildnings namnet: `<container-registry>/<repository>/<image-name>`.<br><br>Här är ett exempel `mcr.microsoft.com/azure-cognitive-services/keyphrase` representerar Extrahering av diskussionsämne avbildningen i Microsoft Container Registry under Azure Cognitive Services-lagringsplatsen. Ett annat exempel är `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` som representerar tal till text-bilden i Microsoft-lagringsplatsen för förhands gransknings behållar registret. |
    |OS-typ|`Linux`|
    |Storlek|Ändra storlek till föreslagna rekommendationer för din speciella kognitiva tjänst behållare:<br>2 CPU-kärnor<br>4 GB

3. På fliken **nätverk** anger du följande information:

    |Inställning|Värde|
    |--|--|
    |Portar|Ange TCP-port till `5000`. Exponerar behållaren på port 5000.|

4. På fliken **Avancerat** anger du de **miljövariabler** som krävs för behållarens fakturerings inställningar för Azure Container instance-resursen:

    | Nyckel | Värde |
    |--|--|
    |`apikey`|Kopieras från sidan **nycklar** i resursen. Det är en sträng med 32 alfanumeriska tecken utan blank steg eller bindestreck, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Kopieras från **översikts** sidan för resursen.|
    |`eula`|`accept`|

5. Klicka på **Granska och skapa**
6. När verifieringen är klar klickar du på **skapa** för att slutföra processen som skapas
7. När resursen har distribuerats är den klar
