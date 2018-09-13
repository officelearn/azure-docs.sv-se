---
title: Azure Service Fabric bild store anslutningssträngen | Microsoft Docs
description: Förstå bild store-anslutningssträng
services: service-fabric
documentationcenter: .net
author: alexwun
manager: timlt
editor: ''
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 8a11f9c9ebc2dd0b0eabf7a34d5ef38ae4e29309
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719083"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Om inställningen ImageStoreConnectionString

En del av vår dokumentation nämner vi kort förekomsten av en ”ImageStoreConnectionString”-parametern utan som beskriver vad det verkligen innebär. Och när du går igenom en artikel som [distribuera och ta bort program med hjälp av PowerShell][10], det ser ut som du är kopiera och klistra in värdet som visas i klustermanifestet på målklustret. Så måste vara kan konfigureras per kluster, men när du skapar ett kluster via den [Azure-portalen][11], det finns inget alternativ för att konfigurera den här inställningen och det är alltid ”fabric: ImageStore”. Vad är syftet med den här inställningen?

![Klustermanifestet][img_cm]

Service Fabric startas som en plattform för interna Microsoft-användning av många olika team, så vissa aspekter av det är mycket anpassningsbara – ”bild Store” är en aspekt. Bild-Store är i princip en modulär lagringsplats för att lagra programpaket. När ditt program har distribuerats till en nod i klustret, hämtas noden innehållet i programpaketet från avbildningen Store. ImageStoreConnectionString är en inställning som innehåller all nödvändig information för både klienter och noder att hitta rätt avbildning Store för ett kluster.

Det finns tre möjliga typer av avbildningen Store-providers och deras motsvarande anslutningssträngar är följande:

1. Bild Store-tjänsten: ”fabric: ImageStore”

2. Filsystem: ”file:[file systemsökväg”]

3. Azure Storage ”: xstore:DefaultEndpointsProtocol = https; AccountName = [...]; AccountKey = [...]; Behållaren = [...] ”

Providertyp som används i produktion är tjänsten bild Store, som är en tillståndskänslig beständiga systemtjänst som du ser i Service Fabric Explorer. 

![Bild Store-tjänsten][img_is]

Som är värd för avbildningen Store i en systemtjänst i själva klustret eliminerar externa beroenden för paketdatabasen och ger oss mer kontroll över ort lagringsutrymme. Framtida förbättringar runt bilden Store är sannolikt att rikta bild Store-providern först, om inte exklusivt. Anslutningssträngen för avbildning Store tjänstleverantör har inte någon unik information eftersom klienten är redan ansluten till målklustret. Klienten behöver bara veta att protokoll som riktar in sig på system-tjänsten ska användas.

Filsystem-providern används istället för avbildning Store-tjänsten för lokala kluster i en ruta under utvecklingen ska kunna starta klustret lite snabbare. Skillnaden är normalt små, men det är en användbar optimering för de flesta dem under utvecklingen. Det är möjligt att distribuera ett lokalt kluster i en ruta med de andra providern lagringstyper samt, men det finns vanligtvis ingen anledning att göra det eftersom utveckla/testa arbetsflödet förblir detsamma oavsett providern. Azure Storage-providern finns bara för bakåtkompatibilitet gamla kluster distribueras innan avbildningen Store tjänstleverantören introducerades.

Dessutom inte av filsystemet-providern eller Azure Storage-provider som ska användas som en metod för att dela en avbildning Store mellan flera kluster – detta resulterar i skadade data för klusterkonfigurationen eftersom varje kluster kan skriva data som orsakar konflikter på avbildningen Store. Om du vill dela etablerade programpaket mellan flera kluster, använda [sfpkg] [ 12] filer i stället som kan överföras till någon extern lagring med en nedladdning URI.

Så ImageStoreConnectionString kan konfigureras, men du bara använda standardinställningen. När du publicerar till Azure via Visual Studio, anges parametern automatiskt åt dig i enlighet med detta. Anslutningssträngen är alltid ”fabric: ImageStore” för programdistribution till ett kluster i Azure. Även om tveksamheter värdet alltid kan verifieras genom att hämta klustermanifestet av [PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx), eller [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Testa både lokalt och produktionskluster bör alltid konfigureras för att använda avbildningen Store tjänstleverantören samt.

### <a name="next-steps"></a>Nästa steg
[Distribuera och ta bort program med hjälp av PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
