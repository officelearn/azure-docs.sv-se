---
title: Avere vFXT kluster justering - Azure
description: Översikt över anpassade inställningar för att optimera prestanda i Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f5e780dcab20befe19ca34020908eee93c290516
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409181"
---
# <a name="cluster-tuning"></a>Justering av kluster


De flesta vFXT kluster kan dra nytta av anpassade inställningar. De här inställningarna kan klustret fungerar bäst med din visst arbetsflöde, datauppsättningen och verktyg. 

Den här anpassningen bör göras tillsammans med en supportmedarbetare eftersom den omfattar vanligtvis konfigurera funktioner som inte är tillgängliga från Kontrollpanelen Avere.

Det här avsnittet beskrivs några av den anpassade justering som kan göras.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>Allmän optimeringar

Dessa ändringar kan bör baserat på datauppsättningen egenskaper eller arbetsflödet format.

* Om arbetsbelastningen är hög, öka storleken på skrivcache från dess standardvärdet 20%. 
* Om datauppsättningen omfattar många små filer, öka gränsvärdet för kluster-cache-fil. 
* Om arbetet innefattar kopiering eller flytta data mellan två databaserna, justera antalet trådar som används för att flytta data: 
  * Du kan öka antalet parallella trådar som används för att öka hastigheten.
  * Om backend-lagringsvolymen blir överbelastad, kan du behöva minska antalet parallella trådar som används.
* Om klustret cachelagrar data för en core-filer som använder NFSv4 ACL: er, aktivera cachelagring för att effektivisera filauktorisering för specifika klienter åtkomstläge.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Cloud NAS eller gateway optimeringar i molnet

Om du vill dra nytta av högre hastighet för data mellan vFXT kluster och cloud storage i ett moln-NAS eller gateway-scenario (där vFXT klustret ger NAS-style åtkomst till en behållare i molnet), rekommendera din representant ändra inställningar som dessa till fler aggressivt skicka data till lagringsvolymen från cachen:

* Öka antalet TCP-anslutningar mellan klustret och storage-behållare
* Minska värdet för REST-tidsgränsen för kommunikation mellan klustret och storage att försöka igen skriver tidigare om de inte omedelbart lyckas  
* Öka storleken på segment så att varje serverdel skriva segment överföringar en 8 MB segment av data i stället för 1 MB

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>”Cloud bursting” eller hybrid WAN-optimering

Dessa ändringar kan vara användbart i ett moln som bursting scenario eller storage WAN-optimering hybridscenario (där vFXT klustret ger integrering mellan molnet och lokal lagring för maskinvara):

* Öka antalet TCP-anslutningar tillåts mellan klustret och core-filer
* Aktivera inställningen WAN-optimering för fjärranslutna core-filer (den här inställningen kan användas för en fjärransluten lokal filer eller ett moln core filer i en annan Azure-region).
* Öka buffertstorleken för TCP-socket (beroende på arbetsbelastningen och prestanda måste)
* Aktivera inställningen ”alltid vidarebefordra” att minska redundant cachelagrade filer (beroende på arbetsbelastningen och prestanda måste)

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Hjälp med att optimera dina Avere vFXT för Azure

Använd proceduren som beskrivs i [få hjälp med ditt system](avere-vfxt-open-ticket.md) kontakta supportpersonalen om dessa optimeringar. 