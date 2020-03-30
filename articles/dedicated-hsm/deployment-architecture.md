---
title: Distributionsarkitektur – Azure Dedikerad HSM | Microsoft-dokument
description: Grundläggande designöverväganden när du använder Azure Dedicated HSM som en del av en programarkitektur
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 89e3bf95a6b048e5e97cfb151ef9302b70eac1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048552"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Distributionsarkitektur för Azure Dedicated HSM

Azure Dedicated HSM tillhandahåller kryptografisk nyckellagring i Azure. Den uppfyller stränga säkerhetskrav. Kunderna kommer att dra nytta av att använda Azure Dedicated HSM om de:

* Måste uppfylla FIPS 140-2 Nivå 3-certifiering
* Kräv att de har exklusiv tillgång till HSM
* bör ha fullständig kontroll över sina enheter

HSM:erna distribueras över Microsofts datacenter och kan enkelt etableras som ett par enheter som grund för en lösning med hög tillgänglighet. De kan också distribueras över regioner för en katastroftålig lösning. De regioner med dedikerad HSM som är tillgängliga för närvarande är:

* USA, östra
* USA, östra 2
* USA, västra
* USA, södra centrala
* Sydostasien
* Asien, östra
* Indien, centrala
* Södra Indien
* Japan, östra
* Japan, västra
* Europa, norra
* Europa, västra
* Storbritannien, södra
* Storbritannien, västra
* Kanada, centrala
* Kanada, östra
* Australien, östra
* Australien, sydöstra

Var och en av dessa regioner har HSM-rack distribuerade i antingen två oberoende datacenter eller minst två oberoende tillgänglighetszoner. Sydostasien har tre tillgänglighetszoner och Östra USA 2 har två. Det finns totalt åtta regioner i Europa, Asien och USA som erbjuder dedikerad HSM-tjänsten. Mer information om Azure-regioner finns i den officiella [Azure-regionsinformationen](https://azure.microsoft.com/global-infrastructure/regions/).
Vissa designfaktorer för alla dedikerade HSM-baserade lösningar är plats/svarstid, hög tillgänglighet och stöd för andra distribuerade program.

## <a name="device-location"></a>Enhetsplats

Optimal HSM-enhetsplats är i närheten av de program som utför kryptografiska åtgärder. Svarstiden i regionen förväntas vara ensiffriga millisekunder. Svarstiden mellan regioner kan vara 5 till 10 gånger högre än så.

## <a name="high-availability"></a>Hög tillgänglighet

För att uppnå hög tillgänglighet måste en kund använda två HSM-enheter i en region som är konfigurerade med Gemalto-programvaran som ett par med hög tillgänglighet. Den här typen av distribution säkerställer tillgängligheten för nycklar om en enda enhet uppstår ett problem som hindrar den från att bearbeta nyckelåtgärder. Det minskar också avsevärt risken vid avbrott/fix underhåll såsom byte av strömförsörjning. Det är viktigt att en design står för alla former av regionala nivå misslyckande. Regionala nivå misslyckanden kan hända när det finns naturkatastrofer som orkaner, översvämningar eller jordbävningar. Dessa typer av händelser bör minskas genom att etablera HSM-enheter i en annan region. Enheter som distribueras i en annan region kan paras ihop via Gemaltos programvarukonfiguration. Det innebär att minsta möjliga distribution för en högtillgänglig och katastroftålig lösning är fyra HSM-enheter i två regioner. Lokal redundans och redundans mellan regioner kan användas som en baslinje för att lägga till ytterligare HSM-enhetsdistributioner för att stödja svarstid, kapacitet eller för att uppfylla andra programspecifika krav.

## <a name="distributed-application-support"></a>Stöd för distribuerade program

Dedikerade HSM-enheter distribueras vanligtvis till stöd för program som behöver utföra nyckellagring och nyckelhämtningsåtgärder. Dedikerade HSM-enheter har 10 partitioner för oberoende programstöd. Enhetens plats bör baseras på en helhetssyn på alla program som behöver använda tjänsten.

## <a name="next-steps"></a>Nästa steg

När distributionsarkitekturen har bestämts kommer de flesta konfigurationsaktiviteter för att implementera den arkitekturen att tillhandahållas av Gemalto. Detta inkluderar enhetskonfiguration samt scenarier för programintegrering. Mer information finns i [Gemaltos kundsupportportal](https://supportportal.gemalto.com/csm/) och hämta administrations- och konfigurationsguider. Microsofts partnerwebbplats har en mängd olika integrationsguider.
Vi rekommenderar att alla viktiga begrepp för tjänsten, till exempel hög tillgänglighet och säkerhet, är väl förstådda innan enhetsetablering eller programdesign och distribution.
Ytterligare begreppsnivå ämnen:

* [Hög tillgänglighet](high-availability.md)
* [Fysisk säkerhet](physical-security.md)
* [Nätverk](networking.md)
* [Support](supportability.md)
* [Övervakning](monitoring.md)
