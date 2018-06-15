---
title: 'Uppgradering av programmet: dataserialisering | Microsoft Docs'
description: Metodtips för dataserialisering och hur den påverkar rullande programuppgraderingar.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: a5f36366-a2ab-4ae3-bb08-bc2f9533bc5a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 2f6fad0ecca09ff9210b5961301fea3446a88f11
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213171"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Hur dataserialisering påverkar en uppgradering av programmet
I en [löpande uppgradering av programmet](service-fabric-application-upgrade.md), uppgraderingen tillämpas på en del noder, en domän i taget. Vissa uppgraderingsdomäner finns på den nya versionen av ditt program under den här processen och vissa uppgraderingsdomäner finns på den äldre versionen av programmet. Den nya versionen av programmet måste kunna läsa den tidigare versionen av data under driftsättningen, och den gamla versionen av programmet måste kunna läsa den nya versionen av dina data. Om formatet inte är kompatibel med framåt och bakåt, misslyckas uppgraderingen eller värre, data kan försvinna eller skadas. Den här artikeln beskrivs vad som utgör ditt dataformat och ger bästa praxis för att säkerställa att dina data är framåt och bakåt kompatibel.

## <a name="what-makes-up-your-data-format"></a>Hur får din dataformat?
Azure Service Fabric kommer de data som är beständiga och replikeras från C#-klasser. För program som använder [tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md), att data är objekten i den tillförlitliga ordböcker och köer. För program som använder [Reliable Actors](service-fabric-reliable-actors-introduction.md), som är stödjande tillståndet för aktören. Dessa klasser i C# måste kunna serialiseras till beständiga och replikeras. Därför definieras dataformatet av fält och egenskaper som serialiseras, samt hur de serialiseras. Till exempel i en `IReliableDictionary<int, MyClass>` data är en serialiserad `int` och en serialiserad `MyClass`.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Kod ändrar som resulterar i en data-formatändring
Eftersom dataformatet bestäms av klasser i C#, kan ändringar till klasser medföra en formatändring data. Vara måste försiktig så att en uppgradering kan hantera formatändring data. Exempel som kan orsaka dataändringar format:

* Lägga till eller ta bort fält eller egenskaper
* Byta namn på fält eller egenskaper
* Ändra vilka typer av fält eller egenskaper
* Ändra namnet för klassen eller namnområdet

### <a name="data-contract-as-the-default-serializer"></a>Datakontrakt som standard serialiseraren
Serialiseraren är vanligtvis ansvarig för läsning av data och avserialisering av det till den aktuella versionen, även om data i en äldre eller *nyare* version. Serialiseraren standard är den [datakontrakt serialiseraren](https://msdn.microsoft.com/library/ms733127.aspx), som har väldefinierade versionshantering regler. Tillförlitliga samlingar att serialiseraren som ska åsidosättas, men Reliable Actors för närvarande inte. Data serialiseraren spelar en viktig roll i att aktivera löpande uppgraderingar. Serialiseraren datakontrakt är serialiseraren som vi rekommenderar för Service Fabric-program.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Hur påverkar dataformatet löpande uppgradering
Det finns två huvudscenarier där serialiseraren kan stöta på en äldre under en uppgradering eller *nyare* version av dina data:

1. När en nod har uppgraderats och börjar säkerhetskopiera, nya serialiseraren läser in de data som har kvar på disken av den gamla versionen.
2. Under uppgraderingen innehåller klustret en blandning av gamla och nya versioner av din kod. Eftersom repliker kan placeras i olika uppgraderingsdomäner och repliker skicka data till varandra, kan den nya eller gamla versionen av dina data påträffas i den nya eller gamla versionen av din serialiserare.

> [!NOTE]
> ”Ny version” och ”gamla version” här avser versionen av din kod som körs. ”Nya serialiseraren” syftar på serialiserare-kod som körs i den nya versionen av programmet. ”Nya data” refererar till den serialiserade C#-klassen från den nya versionen av programmet.
> 
> 

De två versionerna av koden och data måste vara både framåt och bakåt kompatibel. Om de inte är kompatibla uppgraderingen misslyckas eller data kan gå förlorade. Uppgraderingen kan misslyckas om koden eller serialiseraren kan utlösa undantag eller ett fel när den stöter på den andra versionen. Data kan gå förlorade om du till exempel en ny egenskap har lagts till men gamla serialiseraren den under deserialisering.

Datakontrakt är den rekommenderade lösningen för att säkerställa att dina data är kompatibel. Det har väldefinierade versionshantering regler för att lägga till, ta bort och ändra fält. Det har också stöd för behandlar okänt fält och koppla upp i processen för serialisering och deserialisering samt hantera klassarv. Mer information finns i [med datakontrakt](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>Nästa steg
[Uppgradera ditt program med hjälp av Visual Studio](service-fabric-application-upgrade-tutorial.md) vägleder dig genom en uppgradering av programmet med hjälp av Visual Studio.

[Uppgradera ditt program med hjälp av Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vägleder dig genom en uppgradering av programmet med hjälp av PowerShell.

Styra hur programmet ska uppgraderas med hjälp av [uppgradera parametrar](service-fabric-application-upgrade-parameters.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att referera till [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Lösa vanliga problem i programuppgraderingar genom att referera till stegen i [felsökning programuppgraderingar ](service-fabric-application-upgrade-troubleshooting.md).

