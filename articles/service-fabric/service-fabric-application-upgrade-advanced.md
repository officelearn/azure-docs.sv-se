---
title: Avancerade program avsnitt | Microsoft Docs
description: "Den här artikeln beskriver vissa avancerade avsnitt om att uppgradera ett Service Fabric-program."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar;chackdan
ms.openlocfilehash: 8d3b922f3d50b645ac9db2cc879a319df1262e0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Uppgradering av Service Fabric-programmet: avancerade alternativ
## <a name="adding-or-removing-services-during-an-application-upgrade"></a>Att lägga till eller ta bort tjänster under en uppgradering av programmet
Om du lägger till en ny tjänst till ett program som redan distribuerats och publiceras som en uppgradering, läggs den nya tjänsten till det distribuerade programmet.  En sådan uppgradering påverkar inte någon av de tjänster som redan var en del av programmet. Men en instans av tjänsten som har lagts till måste startas för den nya tjänsten aktiveras (med hjälp av den `New-ServiceFabricService` cmdlet).

Tjänster kan också tas bort från ett program som en del av en uppgradering. Men alla aktuella instanser av tjänsten till ska-bort måste stoppas innan du fortsätter med uppgraderingen (med hjälp av den `Remove-ServiceFabricService` cmdlet).

## <a name="manual-upgrade-mode"></a>Manuellt Uppgraderingsläge
> [!NOTE]
> Det oövervakade manuellt läget bör övervägas endast för en misslyckad eller pausat uppgradering. Övervakat läge är det rekommenderade uppgraderingsläget för Service Fabric-program.
>
>

Azure Service Fabric innehåller flera uppgradera lägen för att stödja kluster för utveckling och produktion. Distributionsalternativ valt kan vara olika för olika miljöer.

Övervakade program uppgraderingen är de vanligaste uppgradering i produktionsmiljön. När principen för versionsuppgradering anges säkerställer Service Fabric att programmet är felfri innan du fortsätter uppgraderingen.

 Programadministratör kan använda det rullande program uppgraderingsläget ha fullständig kontroll över uppgradering pågår via olika uppgraderingsdomäner. Det här läget är användbart när en anpassad eller komplexa utvärdering hälsoprincip krävs, eller en icke-konventionella uppgraderingen sker (till exempel programmet är redan i förlust av data).

Slutligen är automatiserat program uppgraderingen användbart för utveckling och testmiljöer för att ge en snabb iteration cykel under utvecklingen av tjänsten.

## <a name="change-to-manual-upgrade-mode"></a>Ändra till manuellt Uppgraderingsläge
**Manuell**--stoppa programmet uppgraderingen på den aktuella UD och ändra uppgraderingsläget oövervakade manuellt. Måste administratören manuellt anropa **MoveNextApplicationUpgradeDomainAsync** att fortsätta med uppgraderingen eller starta en återställning genom att initiera en ny uppgradering. När uppgraderingen går in i manuellt läge, förblir den i manuellt läge tills en ny uppgradering initieras. Den **GetApplicationUpgradeProgressAsync** kommando returnerar FABRIC\_programmet\_uppgradera\_tillstånd\_rullande\_framåt\_väntar.

## <a name="upgrade-with-a-diff-package"></a>Uppgradera med en diff-paket
Ett Service Fabric-program kan uppgraderas genom etablering med en fullständig, självständiga programpaket. Ett program kan också uppgraderas med hjälp av en diff-paket som innehåller de uppdaterade programfilerna och uppdaterade programmanifestet service manifest-filer.

En fullständig programpaketet innehåller alla filer som krävs för att starta och köra ett Service Fabric-program. En diff-paketet innehåller endast de filer som ändrats mellan den sista etablera och uppgraderingen, plus fullständig programmanifestet och service manifest filer. Alla referenser i programmanifestet eller tjänstmanifestet som inte kan hittas i build-layouten sökte efter i image store.

Fullständig programpaket krävs för den första installationen av ett program i klustret. Efterföljande uppdateringar kan vara ett fullständigt programpaket eller en diff-paketet.

Tillfällen när du använder en diff-paketet är ett bra alternativ:

* En diff-paketet rekommenderas när du har ett stort programpaket som refererar till flera service manifest-filer och/eller flera kod paket, config paket eller data paket.
* En diff-paketet rekommenderas när du har en distribution som genererar build-layout direkt från din Byggprocessen. Även om koden inte har ändrats hämta nyligen inbyggd sammansättningar i det här fallet en annan kontrollsumma. Med hjälp av en fullständig programpaket skulle måste du uppdatera versionen på alla kod paket. Med en diff-paket kan ange du endast de filer som ändrats och manifestfiler där version har ändrats.

När ett program uppgraderas med Visual Studio, publiceras diff-paketet automatiskt. Om du vill skapa en diff-paketet manuellt programmanifestet och service manifest måste uppdateras, men endast ändrade paket ska tas med i det slutliga programpaketet.

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

Nu antar vi att du vill uppdatera endast kodpaketet av service1 använder en diff-paketet med hjälp av PowerShell. Uppdaterade programmet har nu följande mappstrukturen:

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

## <a name="next-steps"></a>Nästa steg
[Uppgradera ditt program med hjälp av Visual Studio](service-fabric-application-upgrade-tutorial.md) vägleder dig genom en uppgradering av programmet med hjälp av Visual Studio.

[Uppgradera ditt program med hjälp av Powershell](service-fabric-application-upgrade-tutorial-powershell.md) vägleder dig genom en uppgradering av programmet med hjälp av PowerShell.

Styra hur programmet ska uppgraderas med hjälp av [uppgradera parametrar](service-fabric-application-upgrade-parameters.md).

Gör din programuppgraderingar kompatibla genom att lära dig hur du använder [dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lösa vanliga problem i programuppgraderingar genom att referera till stegen i [felsökning programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).
