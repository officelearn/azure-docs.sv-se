---
title: Azure Service Fabric image store anslutningssträngen | Microsoft Docs
description: Förstå image store-anslutningssträng
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
ms.openlocfilehash: 7d164fea62afac83c4fe2216c56a9980d9279f3a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207136"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Förstå inställningen ImageStoreConnectionString

I vissa av vår dokumentation nämna vi utan som beskriver vad det verkligen innebär en kort förekomsten av en ”ImageStoreConnectionString”-parametern. Och efter att gå via en artikel som [distribuera och ta bort program med hjälp av PowerShell][10], det ser ut som du är kopiera och klistra in värdet som visas i klustermanifestet på målklustret. Så måste vara kan konfigureras per kluster, men när du skapar ett kluster via den [Azure-portalen][11], det går inte att konfigurera den här inställningen och det är alltid ”fabric: Avbildningsarkiv”. Vad är syftet med den här inställningen?

![Klustermanifestet][img_cm]

Service Fabric startas som en plattform för intern Microsoft-användning av många olika team så vissa aspekter av det är mycket anpassningsbara - ”Image Store” är en aspekt. Image Store är i princip en pluggable databas för att lagra programpaket. När programmet distribueras till en nod i klustret, hämtar innehållet i ditt programpaket från avbildningsarkivet noden. ImageStoreConnectionString är en inställning som innehåller all nödvändig information för både klienter och noder att hitta rätt Image Store för ett kluster.

Det finns tre möjliga typer av Image Store-providers och deras motsvarande anslutningssträngar är följande:

1. Image Store-tjänsten: ”fabric: Avbildningsarkiv”

2. Filsystem: ”file:[file systemsökvägen]”

3. Azure Storage ”: xstore:DefaultEndpointsProtocol = https; AccountName = [...]; AccountKey = [...]; Behållaren = [...] ”

Providertyp som används i produktionen är Image Store-tjänsten, som är en tillståndskänslig beständiga systemtjänst som du ser i Service Fabric Explorer. 

![Image Store-tjänsten][img_is]

Värd för Image Store i en systemtjänst i själva klustret eliminerar externa beroenden för paketdatabasen och ger oss mer kontroll över ort lagringsutrymme. Framtida förbättringar runt Image Store är sannolikt att rikta Image Store-providern först, om inte exklusivt. Anslutningssträngen för Image Store Service-providern har inte någon unik information eftersom klienten är redan ansluten till målklustret. Klienten behöver bara känna protokoll som systemtjänsten som mål som ska användas.

Filsystem-providern används i stället för Image Store-tjänsten för den lokala en ruta kluster under utvecklingen för att starta klustret något snabbare. Skillnaden är vanligtvis liten, men det är en användbar optimering för de flesta avdelningen under utvecklingen. Det är möjligt att distribuera ett kluster med lokala en ruta med de andra provider lagringstyper samt, men det finns vanligtvis ingen anledning att göra det eftersom utveckla och testning arbetsflödet fortsätter att vara detsamma oavsett providern. Azure Storage-providern finns bara för bakåtkompatibilitet gamla kluster distribueras innan Image Store Service provider infördes.

Dessutom varken providern filsystem eller Azure Storage-providern ska inte användas som en metod för att dela en Image Store mellan flera kluster - resulterar detta i skadade data för klusterkonfigurationen som varje kluster kan skriva motstridiga data till den Image Store. Om du vill dela etablerade programpaket mellan flera kluster använder [sfpkg] [ 12] filer i stället som kan överföras till en extern butik hämta URI.

Så medan ImageStoreConnectionString konfigureras använda du vanligtvis bara standardinställningen. När du publicerar till Azure via Visual Studio anges parametern automatiskt åt dig därför. Anslutningssträngen är alltid ”fabric: Avbildningsarkiv” för programdistribution till kluster finns i Azure. Även om när osäkra, värdet alltid kan verifieras genom att hämta klustermanifestet av [PowerShell](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx), eller [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Testa både lokalt och driftskluster alltid ska konfigureras för att du använder Image Store Service-providern.

### <a name="next-steps"></a>Nästa steg
[Distribuera och ta bort program med hjälp av PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
