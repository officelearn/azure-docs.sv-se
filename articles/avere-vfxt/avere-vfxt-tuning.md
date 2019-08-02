---
title: Aver vFXT-kluster justering – Azure
description: Översikt över anpassade inställningar för att optimera prestanda i AVERT vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 17e55dbe84cda87ee902c94e0024c9a3aad8b31b
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698344"
---
# <a name="cluster-tuning"></a>Kluster justering


De flesta vFXT-kluster kan dra nytta av anpassade prestanda inställningar. De här inställningarna hjälper klustret att fungera bäst med specifika arbets flöden, data uppsättningar och verktyg. 

Den här anpassningen bör göras tillsammans med en support representant, eftersom det innebär att konfigurera funktioner som inte är tillgängliga från den Avera kontroll panelen.

I det här avsnittet beskrivs några av de anpassade justeringarna som kan göras.

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helpful in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>Allmänna optimeringar

Dessa ändringar kan vara rekommenderade baserat på data uppsättnings kvaliteter eller arbets flödes format.

* Om arbets belastningen är skrivbar, ökar du storleken på skrivcache från standardvärdet 20%. 
* Om data uppsättningen omfattar många små filer ökar du antalet filer för klustrets cacheminne. 
* Om arbetet innebär att kopiera eller flytta data mellan två databaser, justera antalet trådar som används för att flytta data: 
  * För att öka hastigheten kan du öka antalet parallella trådar som används.
  * Om Server delens lagrings volym blir överbelastad kan du behöva minska antalet parallella trådar som används.
* Om klustret cachelagrar data för en kärn post som använder NFSv4 ACL: er aktiverar du cachelagring av åtkomst läge för att effektivisera Filauktorisering för vissa klienter.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Moln-NAS eller moln-Gateway-optimeringar

Om du vill dra nytta av högre data hastigheter mellan vFXT-klustret och moln lagring i ett Cloud NAS-eller gateway-scenario (där vFXT-klustret ger åtkomst till NAS-typ till en moln behållare) kan du rekommendera att ändra inställningar som dessa till fler Skicka data aggressivt till lagrings volymen från cachen:

* Öka antalet TCP-anslutningar mellan klustret och lagrings behållaren

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Cloud bursting eller hybrid WAN-optimeringar

I ett scenario med moln överföring eller ett scenario för Hybrid optimering i molnet (där vFXT-klustret tillhandahåller integrering mellan molnet och den lokala maskin varu lagringen), kan de här ändringarna vara användbara:

* Öka antalet TCP-anslutningar som tillåts mellan klustret och kärn filen
* Aktivera inställningen för WAN-optimering för fjärrcore-filer (den här inställningen kan användas för fjärranslutna filer på plats eller en moln kärna som sparas i en annan Azure-region.)
* Öka buffertstorleken för TCP-socketen (beroende på arbets belastning och prestanda krav)
* Aktivera inställningen "Always Forward" för att minska redundanta cachelagrade filer (beroende på arbets belastning och prestanda krav)

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Hjälp till att optimera ditt AVERT vFXT för Azure

Använd proceduren som beskrivs i [få hjälp med systemet](avere-vfxt-open-ticket.md) för att kontakta support personal om dessa optimeringar. 