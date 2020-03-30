---
title: Anslutningssträng för Azure Service Fabric-avbildningsarkiv
description: Lär dig mer om anslutningssträngen för bildlagring, inklusive dess användningsområden och program i ett Service Fabric-kluster.
author: alexwun
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: c3395248188c2a16736cfc8cea262fe163a6944b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645675"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Om inställningen ImageStoreConnectionString

I en del av vår dokumentation nämner vi kortfattat förekomsten av en "ImageStoreConnectionString"-parameter utan att beskriva vad det egentligen betyder. Och efter att ha gått igenom en artikel som [Distribuera och ta bort program med PowerShell,][10]det ser ut som allt du gör är att kopiera / klistra in värdet som visas i klustret manifestet för målklustret. Så inställningen måste vara konfigurerbar per kluster, men när du skapar ett kluster via [Azure-portalen][11]finns det inget alternativ för att konfigurera den här inställningen och det är alltid "tyg:ImageStore". Vad är syftet med den här inställningen då?

![Klustermanifest][img_cm]

Service Fabric började som en plattform för intern Microsoft-konsumtion av många olika team, så vissa aspekter av det är mycket anpassningsbara - "Image Store" är en sådan aspekt. I huvudsak är Image Store en pluggbar lagringsplats för lagring av programpaket. När programmet distribueras till en nod i klustret hämtar noden innehållet i programpaketet från Image Store. ImageStoreConnectionString är en inställning som innehåller all nödvändig information för både klienter och noder för att hitta rätt Image Store för ett visst kluster.

Det finns för närvarande tre möjliga typer av Image Store-leverantörer och deras motsvarande anslutningssträngar är följande:

1. Image Store Service: "tyg:ImageStore"

2. Filsystem: "fil:[sökväg till filsystem]"

3. Azure Storage: "xstore:DefaultEndpointsProtocol=https; AccountName=[...]; AccountKey=[...]; Behållare=[...]"

Providertypen som används i produktionen är Image Store-tjänsten, som är en tillståndskänslig beständig systemtjänst som du kan se från Service Fabric Explorer. 

![Bild store-tjänsten][img_is]

Att vara värd för Image Store i en systemtjänst i själva klustret eliminerar externa beroenden för paketarkivet och ger oss mer kontroll över lagringsplatsen. Framtida förbättringar runt Image Store kommer sannolikt att rikta in sig på Image Store-leverantören först, om inte uteslutande. Anslutningssträngen för Image Store-tjänstleverantören har ingen unik information eftersom klienten redan är ansluten till målklustret. Klienten behöver bara veta att protokoll som är inriktade på systemtjänsten ska användas.

Filsystemprovidern används i stället för Image Store-tjänsten för lokala enluxna kluster under utveckling för att bootstrap klustret något snabbare. Skillnaden är vanligtvis liten, men det är en användbar optimering för de flesta folk under utveckling. Det är möjligt att distribuera ett lokalt enluxna kluster med de andra lagringsprovidertyperna också, men det finns vanligtvis ingen anledning att göra det eftersom arbetsflödet för utveckling/test förblir detsamma oavsett leverantör. Azure Storage-providern finns bara för äldre stöd för gamla kluster som distribuerades innan Image Store-tjänstprovidern introducerades.

Dessutom bör inte filsystemprovidern eller Azure Storage-providern användas som en metod för att dela ett imagelagringslager mellan flera kluster - detta kommer att resultera i skador på klusterkonfigurationsdata eftersom varje kluster kan skriva motstridiga data till avbildningen Lagra. Om du vill dela etablerade programpaket mellan flera kluster använder du [sfpkg-filer][12] i stället, som kan överföras till alla externa butiker med en nedladdnings-URI.

Så medan ImageStoreConnectionString är konfigurerbar, använder du bara standardinställningen. När du publicerar till Azure via Visual Studio ställs parametern automatiskt in åt dig i enlighet med detta. För programmatisk distribution till kluster som finns i Azure är anslutningssträngen alltid "fabric:ImageStore". Även om du är osäker kan dess värde alltid verifieras genom att hämta klustermanifestet av [PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx)eller [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Både lokala test- och produktionskluster bör alltid konfigureras för att även använda Image Store-tjänstleverantören.

### <a name="next-steps"></a>Nästa steg
[Distribuera och ta bort program med PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
