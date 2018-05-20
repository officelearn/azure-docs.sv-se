---
title: Avancerade program avsnitt | Microsoft Docs
description: Den här artikeln beskriver vissa avancerade avsnitt om att uppgradera ett Service Fabric-program.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 168d944f72c1409b5b69c9ab7c07f7fcfa04c7c8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Uppgradering av Service Fabric-programmet: avancerade alternativ
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Lägga till eller ta bort tjänsttyper under en uppgradering av programmet
Om en ny tjänsttyp läggs till ett publicerat program som en del av en uppgradering, läggs den nya service-typen till det distribuerade programmet. Sådana uppgradering påverkar inte någon av tjänstinstanser som redan ingår i programmet men måste skapa en instans av den typ av tjänst som har lagts till för den nya service-typen ska aktiveras (se [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

Tjänsttyper kan tas bort från ett program som en del av en uppgradering. Men alla instanser av tjänsten av tjänsttypen till ska-bort måste tas bort innan du fortsätter med uppgraderingen (se [ta bort ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="manual-upgrade-mode"></a>Manuellt Uppgraderingsläge
> [!NOTE]
> Den *övervakade* Uppgraderingsläge rekommenderas för alla Service Fabric-uppgraderingar.
> Den *UnmonitoredManual* Uppgraderingsläge övervägas endast för misslyckad eller pausat uppgraderingar. 
>
>

I *övervakade* Service Fabric-läge gäller hälsoprinciper för att säkerställa att programmet är felfri medan uppgraderingen pågår. Om hälsoprinciper är bröt mot sedan uppgraderingen pausas eller automatiskt återställs beroende på den angivna *FailureAction*.

I *UnmonitoredManual* programadministratören-läge har fullständig kontroll över utvecklingen av uppgraderingen. Det här läget är användbart när använda anpassade hälsoprinciper för utvärdering eller utföra icke-konventionella uppgraderingar om du vill kringgå hälsoövervakning helt (t.ex. programmet är redan i förlust av data). En uppgradering som körs i detta läge pausa sig självt när du har slutfört varje UD och måste vara explicit återupptas med hjälp av [återuppta ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). När en uppgradering är avbruten och klar att återupptas av användaren, visas dess uppgraderingstillståndet *RollforwardPending* (se [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Slutligen den *UnmonitoredAuto* läget är användbart för att utföra snabb uppgradera iterationer under tjänsten utveckling eller testning eftersom inga indata från användaren krävs och inget program hälsoprinciper utvärderas.

## <a name="upgrade-with-a-diff-package"></a>Uppgradera med en diff-paket
I stället för att etablera en fullständig programpaket kan uppgraderingar också utföras av etablering diff-paket som innehåller endast uppdaterade koden-config-data paket tillsammans med fullständig programmanifestet och slutföra service manifest. Fullständig programpaket är bara krävs för den första installationen av ett program i klustret. Efterföljande uppgraderingar kan antingen vara från fullständig programpaket eller diff-paket.  

Alla referenser i programmanifestet eller service manifest för en diff-paket som inte kan hittas i programpaketet ersätts automatiskt med versionen som för närvarande allokerade.

Scenarier för att använda en diff-paketet är:

* Om du har ett stort programpaket som refererar till flera service manifest-filer och/eller flera kod paket, config paket eller data paket.
* När du har ett distributionssystem för som genererar build-layout direkt från ditt program att skapa processen. Även om koden inte har ändrats hämta nyligen inbyggd sammansättningar i det här fallet en annan kontrollsumma. Med hjälp av en fullständig programpaket skulle måste du uppdatera versionen på alla kod paket. Med en diff-paket kan ange du endast de filer som ändrats och manifestfiler där version har ändrats.

När ett program uppgraderas med Visual Studio, publiceras automatiskt en diff-paketet. Om du vill skapa en diff-paketet manuellt programmanifestet och service manifest måste uppdateras, men endast ändrade paket ska tas med i det slutliga programpaketet.

Till exempel börja med följande program (versionsnummer för enkel förstå):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Anta att du vill uppdatera endast kodpaketet av service1 använder en diff-paketet. Uppdaterade programmet har version följande ändringar:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

I det här fallet kan du uppdatera programmanifestet 2.0.0 och service manifest service1 återspeglar Paketuppdatering kod. Mappen för ditt programpaket skulle ha följande struktur:

```text
app1/
  service1/
    code/
```

Med andra ord skapa ett komplett programpaket normalt och ta sedan bort alla kod/config/datapaketet mappar som version inte har ändrats.

## <a name="rolling-back-application-upgrades"></a>Återställer programuppgraderingar

När uppgraderingar kan rullas framåt i ett av tre lägen (*övervakade*, *UnmonitoredAuto*, eller *UnmonitoredManual*), de kan endast återställas i antingen *UnmonitoredAuto* eller *UnmonitoredManual* läge. Rullande tillbaka i *UnmonitoredAuto* läge fungerar på samma sätt som återställer med undantag som standardvärdet *UpgradeReplicaSetCheckTimeout* skiljer sig - finns [program Uppgradera parametrar](service-fabric-application-upgrade-parameters.md). Rullande tillbaka i *UnmonitoredManual* läge fungerar på samma sätt som återställer - återställningen pausa sig självt när du har slutfört varje UD och måste vara explicit återupptas med [ Återuppta ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) att fortsätta med återställningen.

Återställningar kan aktiveras automatiskt när hälsoprinciper för en uppgradering i *övervakade* läge med en *FailureAction* av *återställning* har överskridits (se [Uppgradera Applikationsparametrarna](service-fabric-application-upgrade-parameters.md)) eller explicit med [Start ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Under återställningen värdet för *UpgradeReplicaSetCheckTimeout* och läget kan fortfarande ändras på en gång med hjälp av [uppdatering ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Nästa steg
[Uppgradera ditt program med hjälp av Visual Studio](service-fabric-application-upgrade-tutorial.md) vägleder dig genom en uppgradering av programmet med hjälp av Visual Studio.

[Uppgradera ditt program med hjälp av Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vägleder dig genom en uppgradering av programmet med hjälp av PowerShell.

Styra hur programmet ska uppgraderas med hjälp av [uppgradera parametrar](service-fabric-application-upgrade-parameters.md).

Gör din programuppgraderingar kompatibla genom att lära dig hur du använder [dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lösa vanliga problem i programuppgraderingar genom att referera till stegen i [felsökning programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).
