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
ms.openlocfilehash: 1679862b1660d3c8b2505c6e0c54f203f5d4665d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383518"
---
## <a name="create-an-azure-container-instance-resource"></a>Skapa en Azure Container instance-resurs

1. Gå till sidan [skapa](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) för container instances.

2. På fliken **grundläggande** anger du följande information:

    |Inställning|Value|
    |--|--|
    |Prenumeration|Välj din prenumeration.|
    |Resursgrupp|Välj den tillgängliga resurs gruppen eller skapa en ny som `cognitive-services`.|
    |Containerns namn|Ange ett namn som `cognitive-container-instance`. Namnet måste innehålla nedre CAPS.|
    |Plats|Välj en region för distribution.|
    |Avbildnings typ|Om behållar avbildningen lagras i ett behållar register som inte kräver autentiseringsuppgifter väljer du `Public`. Om åtkomsten till behållar avbildningen kräver autentiseringsuppgifter väljer du `Private`. Se [behållar databaser och avbildningar](../../cognitive-services-container-support.md#container-repositories-and-images) för information om huruvida behållar avbildningen är `Public` eller `Private` ("offentlig för hands version"). |
    |Avbildningens namn|Ange plats för Cognitive Services behållare. Platsen är det som används som ett argument för kommandot `docker pull`. Referera till [behållar databaserna och avbildningarna](../../cognitive-services-container-support.md#container-repositories-and-images) för de tillgängliga avbildnings namnen och deras motsvarande lagrings plats.<br><br>Avbildningens namn måste vara fullständigt kvalificerade och ange tre delar. Först, behållar registret, sedan databasen, slutligen avbildnings namnet: `<container-registry>/<repository>/<image-name>`.<br><br>Här är ett exempel `mcr.microsoft.com/azure-cognitive-services/keyphrase` representerar Extrahering av diskussionsämne avbildningen i Microsoft Container Registry under Azure Cognitive Services-lagringsplatsen. Ett annat exempel är `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` som representerar tal till text-bilden i Microsoft-lagringsplatsen för förhands gransknings behållar registret. |
    |OS-typ|`Linux`|
    |Storlek|Ändra storlek till föreslagna rekommendationer för din speciella kognitiva tjänst behållare:<br>2 CPU-kärnor<br>4 GB

3. På fliken **nätverk** anger du följande information:

    |Inställning|Value|
    |--|--|
    |Portar|Ange TCP-port till `5000`. Exponerar behållaren på port 5000.|

4. På fliken **Avancerat** anger du de **miljövariabler** som krävs för behållarens fakturerings inställningar för Azure Container instance-resursen:

    | Nyckel | Value |
    |--|--|
    |`apikey`|Kopieras från sidan **nycklar** i resursen. Det är en sträng med 32 alfanumeriska tecken utan blank steg eller bindestreck, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Kopieras från **översikts** sidan för resursen.|
    |`eula`|`accept`|

1. Klicka på **Granska och skapa**
1. När verifieringen är klar klickar du på **skapa** för att slutföra processen som skapas
1. När resursen har distribuerats är den klar