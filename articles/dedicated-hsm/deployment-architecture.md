---
title: Distributionsarkitektur – Azure dedikerad HSM | Microsoft Docs
description: Grundläggande designaspekter när du använder Azure dedikerad HSM som en del av en programarkitektur
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 913be9d1ee6ce92d71f5f0c7eda46a618483ce8d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078301"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Azure dedikerad HSM-distribution-arkitektur

Azure dedikerad HSM tillhandahåller lagring av kryptografiska nycklar i Azure. Den uppfyller stränga säkerhetskrav. Kunderna kommer ha nytta av Azure dedikerad HSM om de:

* Måste uppfylla FIPS 140-2 Level 3-certifiering
* Kräv att de har exklusiv åtkomst till HSM
* ha fullständig kontroll över sina enheter

HSM: erna är fördelade på Microsofts datacenter och enkelt kan etableras som par av enheter som bas för en lösning med hög tillgänglighet. De kan också distribueras i flera regioner för en flexibel lösning för haveriberedskap. Det finns för närvarande regioner med dedikerad HSM som är tillgängliga:

* Östra USA
* USA, östra 2
* Västra USA
* Södra centrala USA
* Sydostasien
* Östasien
* Norra Europa
* Västra Europa

Var och en av dessa regioner har HSM rack som distribueras i två oberoende Datacenter eller minst två oberoende tillgänglighetszoner. Sydostasien har tre tillgänglighetszoner och östra USA 2 har två. Det finns totalt åtta regioner i Europa, östra och USA som erbjuder dedikerad HSM-tjänst. Mer information om Azure-regioner finns i officiellt [Azure-regioner information](https://azure.microsoft.com/global-infrastructure/regions/).
Vissa design faktorer för alla dedikerad HSM-baserade lösningar är plats/fördröjning och hög tillgänglighet och stöd för andra distribuerade program.

## <a name="device-location"></a>Enhetsplats

Optimal HSM enhetsplats är närmast till de program som utför kryptografiska åtgärder. Regional svarstid förväntas vara ensiffriga millisekunder. Interregionala svarstid kan vara 5 till 10 gånger högre än så.

## <a name="high-availability"></a>Hög tillgänglighet

För att uppnå hög tillgänglighet, måste en kund använder två HSM-enheter i en region som är konfigurerade med Gemalto programvara som par med hög tillgänglighet. Den här typen av distribution säkerställer tillgängligheten för nycklar om ett problem som hindrar den från att bearbeta viktiga aktiviteter inträffar i en enskild enhet. Det minskar också avsevärt risken när Underhåll webbsupport, till exempel power strömförsörjning ersättning. Det är viktigt för en design att kompensera för alla typer av regionala på fel. Regionala på fel kan inträffa om det finns naturkatastrofer som orkaner, översvämningar eller jordbävningar. Dessa typer av händelser bör undvikas genom att etablera HSM-enheter i en annan region. Enheter som distribueras i en annan region kan kopplas ihop via Gemalto programvarukonfiguration. Detta innebär att minsta distribution för hög tillgänglighet och katastrofåterställning flexibel lösning är fyra HSM-enheter mellan två regioner. Lokal redundans och redundans i flera regioner kan användas som utgångspunkt för att lägga till ytterligare HSM enheten distributioner för svarstid, kapacitet och uppfyller andra programspecifika krav.

## <a name="distributed-application-support"></a>Distribuerade programstöd

Dedikerad HSM enheter distribueras vanligen till stöd för program som behöver utföra lagring och hämtning av viktiga åtgärder. Dedikerad HSM-enheter har 10 partitioner för oberoende. Enhetsplats ska baseras på en praktisk vy över alla program som behöver använda tjänsten.

## <a name="next-steps"></a>Nästa steg

De flesta konfigurationsaktiviteter att implementera den arkitekturen kommer att tillhandahållas av Gemalto när distributionsarkitektur bestäms. Detta inkluderar enhetskonfiguration samt program integrationsscenarier. Mer information finns i använda den [Gemalto kundsupport](https://supportportal.gemalto.com/csm/) portal och ladda ned guider för administration och konfiguration. Microsoft partner-webbplats har en mängd olika integreringsguiderna.
Vi rekommenderar att alla nyckelbegreppen för tjänst, till exempel hög tillgänglighet och säkerhet till exempel är väl förstådda innan enhetsetablering eller programmets design och distribution.
Ytterligare konceptet på ämnen:

* [Hög tillgänglighet](high-availability.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
* [Övervakning](monitoring.md)
