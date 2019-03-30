---
title: Avancerade program avsnitt | Microsoft Docs
description: Den här artikeln beskriver några avancerade ämnen som hör till uppgraderingen av en Service Fabric-program.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 3cdddac74552b56dfe3567adf30f1a05b6eb8e24
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663799"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Service Fabric-Programuppgradering: avancerade ämnen
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Att lägga till eller ta bort tjänsttyper under en uppgradering av programmet
Om en ny tjänsttyp läggs till i ett publicerat program som en del av en uppgradering, läggs den nya tjänsttypen till det distribuerade programmet. Sådana uppgraderingen påverkar inte någon av tjänstinstanser som redan har en del av programmet, men måste skapa en instans av den typ av tjänst som har lagts till för ny tjänsttyp aktiveras (se [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Tjänsttyper kan tas bort från ett program som en del av en uppgradering. Dock alla instanser av tjänsten för att ska-bort tjänsttypen måste tas bort innan du fortsätter med uppgraderingen (se [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="manual-upgrade-mode"></a>Manuell Uppgraderingsläge
> [!NOTE]
> Den *övervakade* Uppgraderingsläge rekommenderas för alla Service Fabric-uppgraderingar.
> Den *UnmonitoredManual* Uppgraderingsläge bör endast övervägas för misslyckade eller pausat uppgraderingar. 
>
>

I *övervakade* Service Fabric-läge gäller hälsoprinciper för att säkerställa att programmet är felfri medan uppgraderingen pågår. Om hälsoprinciper är bröt mot kommer uppgraderingen pausas eller automatiskt återställts beroende på den angivna *FailureAction*.

I *UnmonitoredManual* läge, programadministratören har fullständig kontroll över förloppet för uppgraderingen. Det här läget är användbart när du använder anpassade hälsoprinciper för utvärdering eller utför icke-konventionella uppgraderingar för att kringgå hälsoövervakning helt (t.ex. programmet är redan i förlust av data). En uppgradering som körs i detta läge pausa sig självt när du har slutfört varje UD och måste återupptas uttryckligen med hjälp av [återuppta ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). När en uppgradering har pausats och klar att återupptas av användaren, visas dess uppgraderingstillståndet *RollforwardPending* (se [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Slutligen den *UnmonitoredAuto* läget är användbart för att utföra snabbt uppgradera iterationer under tjänsten utveckling eller testning eftersom inga indata från användaren krävs och inget program hälsoprinciper utvärderas.

## <a name="upgrade-with-a-diff-package"></a>Uppgradera en diff-paketet
I stället för att etablera ett paket för hela appen, kan du också utföra uppgraderingar genom att etablera diff-paket som innehåller bara de uppdaterade kod/config/data paketen tillsammans med fullständig applikationsmanifestet och slutföra tjänstmanifest. Fullständig programpaket är bara krävs för den första installationen av ett program i klustret. Efterföljande uppgraderingar kan antingen vara från fullständig programpaket eller diff-paket.  

Alla referenser i applikationsmanifestet eller tjänstmanifest för en diff-paket som inte kan hittas i programpaketet ersätts automatiskt med för etablerade version.

Scenarier för att använda en diff-paketet är:

* När du har ett paket för stora program som refererar till flera manifestfiler för tjänsten och/eller flera kodpaket, config paket eller data paket.
* När du har ett distributionssystem för som genererar build-layout direkt från ditt program genereringsprocessen. Även om koden inte har ändrats, få nybyggda sammansättningar i det här fallet en annan kontrollsumma. Med hjälp av en fullständig programpaket måste du uppdatera versionen på alla kodpaket skulle. Med en diff-paketet kan ange du bara de filer som ändrats och manifest-filer där version har ändrats.

När ett program uppgraderas med Visual Studio, publiceras en diff-paketet automatiskt. Om du vill skapa en diff-paketet manuellt applikationsmanifestet och tjänstmanifest måste uppdateras, men endast ändrade paket som ska ingå i det slutliga programpaketet.

Till exempel börja med följande program (versionsnummer benämningarna förstå):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Anta att du vill uppdatera endast kodpaketet av service1 med hjälp av en diff-paketet. Ditt uppdaterade program har följande ändringar i version:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

I det här fallet kan du uppdatera applikationsmanifestet 2.0.0 och tjänstmanifestet för service1 så att uppdateringen av kod-paketet. Mappen för dina programpaket som skulle ha följande struktur:

```text
app1/
  service1/
    code/
```

Med andra ord skapa ett paket för hela appen normalt och ta sedan bort alla kod/config/datapaketet mappar som versionen inte har ändrats.

## <a name="rolling-back-application-upgrades"></a>Återställa programuppgraderingar

Medan uppgraderingar kan distribueras framåt i ett av tre lägen (*övervakade*, *UnmonitoredAuto*, eller *UnmonitoredManual*), de kan endast återställas i antingen *UnmonitoredAuto* eller *UnmonitoredManual* läge. Löpande i *UnmonitoredAuto* läget fungerar på samma sätt som återställer undantag som standardvärdet *UpgradeReplicaSetCheckTimeout* skiljer sig - finns i [program Uppgraderingsparametrar](service-fabric-application-upgrade-parameters.md). Löpande i *UnmonitoredManual* läget fungerar på samma sätt som återställer – återställningen pausa sig självt när du har slutfört varje UD och måste vara explicit återupptas med [ Återuppta ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) att fortsätta med återställningen.

Återställningar kan aktiveras automatiskt när hälsoprinciper av en uppgradering i *övervakade* läge med en *FailureAction* av *återställning* har överskridits (se [Uppgradera programparametrar](service-fabric-application-upgrade-parameters.md)) eller uttryckligen med [Start ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Under återställningen, värdet för *UpgradeReplicaSetCheckTimeout* och läget som fortfarande kan ändras när som helst med [uppdatering ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Nästa steg
[Uppgradera ditt program med hjälp av Visual Studio](service-fabric-application-upgrade-tutorial.md) vägleder dig genom en uppgradering av programmet med Visual Studio.

[Uppgradera ditt program med hjälp av Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vägleder dig genom en uppgradering av programmet med hjälp av PowerShell.

Styra hur programmet uppgraderas med hjälp av [uppgradera parametrar](service-fabric-application-upgrade-parameters.md).

Gör din programuppgraderingar kompatibel genom att lära dig hur du använder [dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Åtgärda vanliga problem i programuppgraderingar genom att referera till stegen i [felsöka programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).
