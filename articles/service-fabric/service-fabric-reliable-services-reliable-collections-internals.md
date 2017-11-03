---
title: "Azure Service Fabric tillförlitlig Tillståndshanterare och tillförlitlig samling internals | Microsoft Docs"
description: "Ingående om tillförlitliga samling begrepp och design i Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: d607449a16e886337ab1bd96213fbb4231124353
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-reliable-state-manager-and-reliable-collection-internals"></a>Azure Service Fabric tillförlitlig Tillståndshanterare och tillförlitlig samling internals
Det här dokumentet går på djupet i tillförlitliga Tillståndshanterare och tillförlitlig samlingar för att se hur kärnkomponenter fungerar i bakgrunden.

> [!NOTE]
> Det här dokumentet är arbetet pågår. Lägga till kommentarer i den här artikeln att berätta för oss vad du vill veta mer om avsnittet.
>

##  <a name="local-persistence-model-log-and-checkpoint"></a>Lokala beständiga modell: loggen och kontrollpunkt
Följ en beständiga modell som kallas loggen och kontrollpunkt tillförlitliga Tillståndshanterare och tillförlitlig samlingar.
I den här modellen är varje tillståndsändring inloggad disk först och sedan tillämpas i minnet.
Fullständig staten sparas bara ibland (kallas även Kontrollpunkten).
Fördelen är att går blev sekventiella Lägg endast skrivåtgärder på disken för bättre prestanda.

För att bättre förstå loggen och kontrollpunkt modellen, låt oss först titta på oändlig disk-scenariot.
Tillförlitliga Tillståndshanterarens loggar varje åtgärd innan de replikeras.
Loggning kan den tillförlitliga samlingar att tillämpa åtgärden endast i minnet.
Eftersom loggar sparas även när replikeringen misslyckas och måste startas, har tillförlitlig Tillståndshanterare tillräckligt med information i loggen spela om alla åtgärder som repliken har förlorat.
Eftersom disken är oändlig loggposter måste aldrig tas bort och tillförlitlig samling behöver hantera InMemory-tillståndet.

Nu ska vi titta på ändligt disk-scenariot.
Eftersom loggposter ackumuleras körs tillförlitliga Tillståndshanterarens slut på diskutrymme.
Innan i så fall behöver tillförlitliga Tillståndshanterarens korta ned loggen för att göra plats för de nya posterna.
Tillförlitlig Tillståndshanterare begäranden tillförlitliga samlingar till kontrollpunkt deras tillstånd i minnet till disken.
Nu skulle tillförlitliga samlingar sparas tillståndet i minnet.
När tillförlitliga samlingar har slutfört sin kontrollpunkter, trunkera tillförlitlig Tillståndshanterarens loggen att frigöra diskutrymme.
När repliken måste startas om, tillförlitlig samlingar återställa sina kontrollpunkt tillstånd och tillförlitlig Tillståndshanterarens återställer och spelar upp alla tillståndsändringar som gjorts sedan den senaste kontrollpunkten.

Ett annat värde lägga till kontrollpunkter är det förbättrar återställningstiden i vanliga scenarier. Loggen innehåller alla åtgärder som inträffat sedan den senaste kontrollpunkten.
Så kan den innehålla flera versioner av ett objekt som flera värden för en viss rad i tillförlitliga ordlistan.
Däremot en tillförlitlig samling kontrollpunkter endast den senaste versionen av varje värde för en nyckel.

## <a name="next-steps"></a>Nästa steg
* [Transaktioner och lås](service-fabric-reliable-services-reliable-collections-transactions-locks.md)

