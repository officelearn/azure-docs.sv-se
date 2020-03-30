---
title: Avere vFXT klusterjustering - Azure
description: Översikt över anpassade inställningar för att optimera prestanda i Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: df20f050ff87fdb59a3e5cca373098240f8bfbb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152943"
---
# <a name="cluster-tuning"></a>Klusterjustering

De flesta vFXT-kluster kan dra nytta av anpassade prestandainställningar. De här inställningarna hjälper klustret att fungera bäst med just arbetsflöde, datauppsättning och verktyg.

Den här anpassningen bör göras med hjälp av en supportrepresentant, eftersom det kan innebära att konfigurera funktioner som inte är tillgängliga från Avere-kontrollpanelen.

I det här avsnittet beskrivs några av de anpassade justeringar som kan göras.

## <a name="general-optimizations"></a>Allmänna optimeringar

Dessa ändringar kan rekommenderas baserat på datauppsättningsegenskaper eller arbetsflödesformat.

* Om arbetsbelastningen är skrivtung ökar du storleken på skrivcachen från standardvärdet på 20 %.
* Om datauppsättningen omfattar många små filer ökar du gränsen för filräkningsgränsen för klustercachen.
* Om arbetet innebär att kopiera eller flytta data mellan två databaser justerar du antalet trådar som används för att flytta data:
  * För att öka hastigheten kan du öka antalet parallella trådar som används.
  * Om backend-lagringsvolymen blir överbelastad kan du behöva minska antalet parallella trådar som används.
* Om klustret cachelagrar data för en kärnfiler som använder NFSv4-ACL:er aktiverar du cachelagring av åtkomstläge för att effektivisera filauktoriseringen för vissa klienter.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Moln-NAS- eller molngatewayoptimeringar

I ett moln-NAS- eller gateway-scenario ger vFXT-klustret åtkomst i NAS-stil till en molnbehållare. Om du vill dra nytta av högre datahastigheter mellan vFXT-klustret och molnlagringen kan din representant rekommendera att ändra inställningarna för att mer aggressivt skicka data till lagringsvolymen från cachen. Ett exempel:

* Öka antalet TCP-anslutningar mellan klustret och lagringsbehållaren

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Cloud bursting eller hybrid WAN optimeringar

I ett molnavbräckningsscenario eller hybridlagring AV WAN-optimeringsscenario ger vFXT-klustret integrering mellan molnet och lokal maskinvarulagring. Dessa ändringar kan vara till hjälp:

* Öka antalet TCP-anslutningar som tillåts mellan klustret och kärnfilerna
* Aktivera WAN Optimization-inställningen för fjärrkärnfiler (Den här inställningen kan användas för en fjärrlokal filer eller en molnkärna filer i en annan Azure-region.)
* Öka buffertstorleken för TCP-socket<sup>*</sup>
* Aktivera inställningen "alltid framåt" för att minska överflödigt cachelagrade filer<sup>*</sup>

<sup>*</sup>Dessa justeringar kanske inte gäller för alla system, beroende på arbetsbelastnings- och prestandabehov.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Hjälp till att optimera din Avere vFXT för Azure

Om du vill kontakta supportpersonalen om dessa optimeringar använder du proceduren som beskrivs i [Få hjälp med ditt system](avere-vfxt-open-ticket.md).
