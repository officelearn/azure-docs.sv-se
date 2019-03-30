---
title: 'Programuppgradering: dataserialisering | Microsoft Docs'
description: Bästa praxis för dataserialisering och hur den påverkar löpande programuppgraderingar.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: a5f36366-a2ab-4ae3-bb08-bc2f9533bc5a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 55cbd869e7434469ebddd7af493c91bfedafc594
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665686"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Hur dataserialisering påverkar en uppgradering av programmet
I en [löpande uppgradering av programmet](service-fabric-application-upgrade.md), uppgraderingen gäller för en delmängd av noderna, en uppgraderingsdomän i taget. Vissa uppgraderingsdomäner finns på den nya versionen av ditt program under den här processen och vissa uppgraderingsdomäner finns på den äldre versionen av ditt program. Den nya versionen av ditt program måste kunna läsa den gamla versionen av dina data under distributionen, och den gamla versionen av ditt program måste kunna läsa den nya versionen av dina data. Om dataformatet inte är kompatibel med framåt och bakåt, misslyckas uppgraderingen eller ännu värre data kan försvinna eller skadas. Den här artikeln beskriver vad som utgör din dataformat och erbjuder rekommendationer för att säkerställa att dina data är framåt och bakåt kompatibel.

## <a name="what-makes-up-your-data-format"></a>Vad utgör din dataformat?
I Azure Service Fabric, kommer de data som är sparade som replikeras från din C# klasser. För program som använder [Reliable Collections](service-fabric-reliable-services-reliable-collections.md), att data är objekten i tillförlitliga ordlistor och köer. För program som använder [Reliable Actors](service-fabric-reliable-actors-introduction.md), det vill säga säkerhetskopiering tillståndet för aktören. Dessa C# klasser måste kunna serialiseras att bevara och replikera redundansenheten. Därför definieras dataformatet av fält och egenskaper som serialiseras, samt hur de serialiseras. I exempelvis ett `IReliableDictionary<int, MyClass>` data är en serialiserad `int` och en serialiserad `MyClass`.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Kod ändrar som resulterar i en dataändring format
Eftersom dataformatet bestäms av C# klasser, ändringar till klasser kan orsaka en dataändring format. Försiktighet måste vidtas för att se till att en löpande uppgradering kan hantera format dataändring. Exempel som kan orsaka dataändringar i formatet:

* Att lägga till eller ta bort fält eller egenskaper
* Byta namn på egenskaper eller fält
* Ändra vilka typer av fält eller egenskaper
* Ändra klassnamn eller namnområde

### <a name="data-contract-as-the-default-serializer"></a>Datakontrakt som standard serialiserare
Serialiseraren ansvarar Allmänt för läsning av data och avserialisera det till den aktuella versionen, även om data finns i en äldre eller *nyare* version. Standard-serialiserare är den [datakontrakt serialiserare](https://msdn.microsoft.com/library/ms733127.aspx), som har väldefinierade versionshantering regler. Tillförlitliga samlingar tillåter serialiseraren kan åsidosättas, men Reliable Actors för närvarande inte. Data serialiserare spelar en viktig roll i att aktivera löpande uppgraderingar. Datakontrakt serialiserare är serialiserare som vi rekommenderar för Service Fabric-program.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Hur dataformatet påverkar en löpande uppgradering
Det finns två huvudscenarier där serialiseraren kan stöta på en äldre under en löpande uppgradering eller *nyare* version av dina data:

1. När en nod har uppgraderats och startas igen kan den nya serialiseraren laddas de data som fanns sparade på disk av den gamla versionen.
2. Under den löpande innehåller klustret en blandning av de gamla och nya versionerna av din kod. Eftersom repliker kan placeras i olika uppgraderingsdomäner och repliker skicka data till varandra, kan den nya och/eller den gamla versionen av dina data i den nya och/eller gamla versionen av din serialiserare påträffas.

> [!NOTE]
> ”Ny version” och ”gamla version” här avser versionen av din kod som körs. ”Ny serialiserare” avser den serialiserare-kod som körs i den nya versionen av ditt program. ”Nya data” refererar till den serialiserade C# klass från den nya versionen av ditt program.
> 
> 

De två versionerna av kod och data format måste vara både framåt och bakåt kompatibel. Om de inte är kompatibla uppgraderingen misslyckas eller kan data gå förlorade. Uppgraderingen misslyckas eftersom kod eller serialiseraren kan utlösa undantag eller ett fel när den stöter på den andra versionen. Data kan gå förlorade om, till exempel en ny egenskap har lagts till men gamla serialiserare det under deserialisering.

Datakontrakt är den rekommenderade lösningen för att säkerställa att dina data är kompatibla. Det har väldefinierade versionshantering regler för att lägga till, ta bort och ändra fält. Det har även stöd för behandlar okänt fält, anslutning i processen för serialisering och deserialisering och ta itu med klassarv. Mer information finns i [med datakontrakt](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>Nästa steg
[Uppgradera ditt program med hjälp av Visual Studio](service-fabric-application-upgrade-tutorial.md) vägleder dig genom en uppgradering av programmet med Visual Studio.

[Uppgradera ditt program med hjälp av Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vägleder dig genom en uppgradering av programmet med hjälp av PowerShell.

Styra hur programmet uppgraderas med hjälp av [uppgradera parametrar](service-fabric-application-upgrade-parameters.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att referera till [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Åtgärda vanliga problem i programuppgraderingar genom att referera till stegen i [felsöka programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).

