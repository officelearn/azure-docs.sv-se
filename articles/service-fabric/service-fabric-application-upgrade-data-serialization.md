---
title: 'Uppgradering av program: dataserier'
description: Metodtips för dataseriering och hur det påverkar rullande programuppgraderingar.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 7dc60c28b56982f82c1ac90db55ac752977ea2d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457494"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Hur dataseriering påverkar en programuppgradering
I en [rullande programuppgradering](service-fabric-application-upgrade.md)tillämpas uppgraderingen på en delmängd noder, en uppgraderingsdomän i taget. Under den här processen finns vissa uppgraderingsdomäner på den nyare versionen av ditt program och vissa uppgraderingsdomäner finns på den äldre versionen av ditt program. Under distributionen måste den nya versionen av ditt program kunna läsa den gamla versionen av dina data, och den gamla versionen av ditt program måste kunna läsa den nya versionen av dina data. Om dataformatet inte är framåt- och bakåtkompatibelt kan uppgraderingen misslyckas, eller ännu värre, data kan gå förlorade eller skadas. I den här artikeln beskrivs vad som utgör ditt dataformat och bästa praxis för att se till att dina data är framåt- och bakåtkompatibla.

## <a name="what-makes-up-your-data-format"></a>Vad utgör ditt dataformat?
I Azure Service Fabric kommer data som sparas och replikeras från dina C#-klasser. För program som använder [tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md)är dessa data objekten i tillförlitliga ordlistor och köer. För program som använder [Reliable Actors](service-fabric-reliable-actors-introduction.md)är det bakgrundsläget för aktören. Dessa C#-klasser måste vara serialiserbara för att kunna bevaras och replikeras. Därför definieras dataformatet av de fält och egenskaper som serialiseras, samt hur de serialiseras. I en `IReliableDictionary<int, MyClass>` data är till exempel `int` en serialiserad och en serialiserad `MyClass`.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Kodändringar som resulterar i en ändring av dataformat
Eftersom dataformatet bestäms av C#-klasser kan ändringar i klasserna orsaka en ändring av dataformatet. Försiktighet måste iakttas för att säkerställa att en rullande uppgradering kan hantera ändringen av dataformatet. Exempel som kan orsaka ändringar i dataformat:

* Lägga till eller ta bort fält eller egenskaper
* Byta namn på fält eller egenskaper
* Ändra typer av fält eller egenskaper
* Ändra klassnamn eller namnområde

### <a name="data-contract-as-the-default-serializer"></a>Datakontrakt som standardseriefilter
Serialiseraren är i allmänhet ansvarig för att läsa data och deserialisera den till den aktuella versionen, även om data finns i en äldre eller *nyare* version. Standard serialiseraren är [serialiseraren datakontrakt](https://msdn.microsoft.com/library/ms733127.aspx), som har väldefinierade versionsregler. Tillförlitliga samlingar gör att serialiseraren åsidosätts, men reliable actors för närvarande inte. Data serialiseraren spelar en viktig roll för att aktivera rullande uppgraderingar. Datakontrakt serialiserare är serialiseraren som vi rekommenderar för Service Fabric-program.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Hur dataformatet påverkar en rullande uppgradering
Under en rullande uppgradering finns det två huvudscenarier där serialiseraren kan stöta på en äldre eller *nyare* version av dina data:

1. När en nod har uppgraderats och startar säkerhetskopieringen läses in de data som sparades på disken av den gamla versionen.
2. Under den rullande uppgraderingen innehåller klustret en blandning av gamla och nya versioner av koden. Eftersom repliker kan placeras i olika uppgraderingsdomäner och repliker skickar data till varandra, kan den nya och/eller gamla versionen av dina data påträffas av den nya och/eller gamla versionen av serialiseraren.

> [!NOTE]
> Den "nya versionen" och "gamla versionen" här hänvisar till den version av koden som körs. Den "nya serialiseraren" refererar till serialiserarens kod som körs i den nya versionen av ditt program. Den "nya data" refererar till serialiserade C# klass från den nya versionen av ditt program.
> 
> 

De två versionerna av kod- och dataformat måste vara både framåt- och bakåtkompatibla. Om de inte är kompatibla kan den rullande uppgraderingen misslyckas eller data gå förlorade. Den rullande uppgraderingen kan misslyckas eftersom koden eller serialiseraren kan utlösa undantag eller ett fel när den stöter på den andra versionen. Data kan gå förlorade om till exempel en ny egenskap har lagts till, men den gamla serialiseraren ignorerar den under deserialisering.

Datakontrakt är den rekommenderade lösningen för att säkerställa att dina data är kompatibla. Den har väldefinierade versionshanteringsregler för att lägga till, ta bort och ändra fält. Det har också stöd för att hantera okända fält, koppla in i serialisering och deserialization process, och hantera klass arv. Mer information finns i [Använda datakontrakt](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>Nästa steg
[Om du uppgraderar programmet med Visual Studio](service-fabric-application-upgrade-tutorial.md) går du igenom en programuppgradering med Visual Studio.

[Om du uppgraderar programmet med Powershell](service-fabric-application-upgrade-tutorial-powershell.md) får du en programuppgradering med PowerShell.

Styr hur programmet uppgraderas med hjälp av [uppgraderingsparametrar](service-fabric-application-upgrade-parameters.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar programmet genom att referera till [Avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Åtgärda vanliga problem i programuppgraderingar genom att referera till stegen i [Felsökning av programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).

