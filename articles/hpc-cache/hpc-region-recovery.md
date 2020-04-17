---
title: Regional redundans- och redundansåterställning med Azure HPC-cache
description: Tekniker för att tillhandahålla redundansfunktioner för haveriberedskap med Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 21074ae6bc4959da031bc7065cd7d0639ec2a14f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537278"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Använda flera cacheminnen för regional återställning av redundans

Varje Azure HPC-cacheinstans körs inom en viss prenumeration och i en region. Det innebär att cachearbetsflödet eventuellt kan störas om regionen har ett fullständigt avbrott.

I den här artikeln beskrivs en strategi för att minska risken för avbrott i arbetet genom att använda en andra region för cache-redundans.

Nyckeln använder backend-lagring som är tillgänglig från flera regioner. Den här lagringen kan vara antingen ett lokalt NAS-system med lämplig DNS-support eller Azure Blob-lagring som finns i en annan region än cachen.

När arbetsflödet fortsätter i din primära region sparas data i långtidslagringen utanför regionen. Om cacheregionen blir otillgänglig kan du skapa en dubblett Azure HPC-cacheinstans i en sekundär region, ansluta till samma lagring och återuppta arbetet från den nya cachen.

## <a name="planning-for-regional-failover"></a>Planering för regional redundans

Så här konfigurerar du en cache som är förberedd för eventuell redundans:

1. Kontrollera att backend-lagringen är tillgänglig i en andra region.
1. När du planerar att skapa den primära cacheinstansen bör du också förbereda att replikera den här installationsprocessen i den andra regionen. Inkludera följande:

   1. Virtuellt nätverk och nätstruktur
   1. Cachekapacitet
   1. Information om lagringsmål, namn och namnområde
   1. Information om klientdatorer, om de finns i samma region som cachen
   1. Kommandot Montera för användning av cacheklienter

   > [!NOTE]
   > Azure HPC-cache kan skapas programmässigt, antingen via en [Azure Resource Manager-mall](../azure-resource-manager/templates/overview.md) eller genom direkt åtkomst till dess API. Kontakta Azure HPC-cacheteamet för mer information.

## <a name="failover-example"></a>Exempel på redundans

Anta till exempel att du vill hitta din Azure HPC-cache i Azures region östra USA. Den kommer åt data som lagras i ditt lokala datacenter.

Du kan använda en cache i regionen Västra USA 2 som en säkerhetskopiering av redundans.

När du skapar cacheminnet i östra USA förbereder du en andra cache för distribution i västra USA 2. Du kan använda skript eller mallar för att automatisera den här förberedelsen.

I händelse av ett regionomfattande fel i östra USA skapar du cachen som du har förberett i regionen Västra USA 2.

När cacheminnet har skapats lägger du till lagringsmål som pekar på samma lokala datalager och använder samma aggregerade namnområdessökvägar som den gamla cachens lagringsmål.

Om de ursprungliga klienterna påverkas skapar du nya klienter i regionen Västra USA 2 som kan användas med den nya cachen.

Alla klienter måste montera den nya cachen, även om klienterna inte påverkades av regionens avbrott. Den nya cachen har olika monteringsadresser än den gamla.

## <a name="learn-more"></a>Läs mer

Azure-programarkitekturguiden innehåller mer information om hur du [återställer från ett regionomfattande avbrott](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>)i tjänsten .
