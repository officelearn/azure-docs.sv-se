---
title: Avancerade programuppgraderingsavsnitt
description: Den här artikeln innehåller några avancerade ämnen som rör uppgradering av ett Service Fabric-program.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: a12d2ec55bda95c1c61d4a73c76f4a777f4237f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414488"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Uppgradering av programuppgraderingen av Service Fabric: Avancerade ämnen

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Lägga till eller ta bort tjänsttyper under en programuppgradering

Om en ny tjänsttyp läggs till i ett publicerat program som en del av en uppgradering läggs den nya tjänsttypen till i det distribuerade programmet. En sådan uppgradering påverkar inte någon av de tjänstinstanser som redan ingick i programmet, men en instans av tjänsttypen som har lagts till måste skapas för att den nya tjänsttypen ska vara aktiv (se [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

På samma sätt kan tjänsttyper tas bort från ett program som en del av en uppgradering. Alla tjänstinstanser av tjänsttypen som ska tas bort måste dock tas bort innan du fortsätter med uppgraderingen (se [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>Undvik anslutningsfall under tillståndslös drift planerad driftstopp

För planerade tillståndslösa instansstopptider, till exempel uppgradering av program/kluster eller nodavaktivering, kan anslutningar tas bort på grund av den exponerade slutpunkten tas bort när instansen går ned, vilket resulterar i tvångsanslutningsstängningar.

Undvik detta genom att konfigurera funktionen *RequestDrain* (preview) genom att lägga till en *fördröjningstid* för instans nära i tjänstkonfigurationen så att begäranden kan tömmas när du tar emot begäranden från andra tjänster i klustret och använder Omvänd proxy eller använder lösnings-API med meddelandemodell för uppdatering av slutpunkter. Detta säkerställer att slutpunkten som annonseras av den tillståndslösa instansen tas bort *innan* fördröjningen startar innan instansen stängs. Den här fördröjningen gör det möjligt för befintliga begäranden att tömma graciöst innan instansen faktiskt går ner. Klienter meddelas om slutpunktsändringen av en motringningsfunktion vid tidpunkten för start av fördröjningen, så att de kan lösa slutpunkten igen och undvika att skicka nya begäranden till den instans som går nedåt.

### <a name="service-configuration"></a>Tjänstkonfiguration

Det finns flera sätt att konfigurera fördröjningen på tjänstsidan.

 * **När du skapar en** `-InstanceCloseDelayDuration`ny tjänst anger du en:

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>`
    ```

 * **När du definierar tjänsten i standardavsnittet i programmanifestet**tilldelar du egenskapen: `InstanceCloseDelayDurationSeconds`

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **När du uppdaterar en** `-InstanceCloseDelayDuration`befintlig tjänst anger du en:

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

### <a name="client-configuration"></a>Klientkonfiguration

Om du vill ta emot ett meddelande när en slutpunkt har ändrats bör klienter registrera en motringning se [ServiceNotificationFilterDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
Ändringsmeddelandet är en indikation på att slutpunkterna har ändrats, klienten ska lösa slutpunkterna igen och inte använda slutpunkterna som inte annonseras längre, eftersom de kommer att gå ner snart.

### <a name="optional-upgrade-overrides"></a>Valfria uppgraderingsidosättningsidosättningar

Förutom att ange standardfördröjningsvaraktighet per tjänst kan du också åsidosätta`InstanceCloseDelayDurationSec`fördröjningen under uppgradering av program/kluster med samma ( ) alternativ:

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

Fördröjningstiden gäller endast för den anropade uppgraderingsinstansen och ändrar inte på annat sätt enskilda tjänstfördröjningskonfigurationer. Du kan till exempel använda detta `0` för att ange en fördröjning för att hoppa över eventuella förkonfigurerade uppgraderingsfördröjningar.

> [!NOTE]
> Inställningen för att tömma begäranden är inte avfäst för begäranden från Azure Load Balancer. Inställningen uppfylls inte om den anropande tjänsten använder klagomålsbaserad lösning.
>
>

> [!NOTE]
> Den här funktionen kan konfigureras i befintliga tjänster med hjälp av Update-ServiceFabricService cmdlet som nämnts ovan, när klusterkodversionen är 7.1.XXX eller högre.
>
>

## <a name="manual-upgrade-mode"></a>Manuellt uppgraderingsläge

> [!NOTE]
> Det *övervakade uppgraderingsläget* rekommenderas för alla Service Fabric-uppgraderingar.
> *Läget UnmonitoredManual* upgrade bör endast beaktas för misslyckade eller pausade uppgraderingar. 
>
>

I *övervakat* läge tillämpar Service Fabric hälsoprinciper för att säkerställa att programmet är felfritt under uppgraderingens gång. Om hälsoprinciper överträds pausas eller återställs uppgraderingen automatiskt beroende på den angivna *FailureAction.*

I *oövervakadmanualt* läge har programadministratören total kontroll över uppgraderingens förlopp. Det här läget är användbart när du använder anpassade principer för hälsoutvärdering eller utför icke-konventionella uppgraderingar för att kringgå hälsoövervakning helt (t.ex. programmet är redan i dataförlust). En uppgradering som körs i det här läget pausar sig själv efter att ha slutfört varje UD och måste uttryckligen återupptas med [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). När en uppgradering pausas och är klar att återupptas av användaren visas *RollforwardPending* (se [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Slutligen är *unmonitoredAuto-läget* användbart för att utföra snabba uppgraderingsiterationer under tjänsteutveckling eller testning eftersom ingen användarindata krävs och inga programhälsoprinciper utvärderas.

## <a name="upgrade-with-a-diff-package"></a>Uppgradera med ett diff-paket

I stället för att etablera ett komplett programpaket kan uppgraderingar också utföras genom att etablera diff-paket som endast innehåller de uppdaterade kod-/config/datapaketen tillsammans med det fullständiga programmanifestet och fullständiga tjänstmanifest. Fullständiga programpaket krävs endast för den första installationen av ett program till klustret. Efterföljande uppgraderingar kan antingen komma från fullständiga programpaket eller diff-paket.  

Alla referenser i programmanifestet eller tjänstmanifesten för ett diff-paket som inte kan hittas i programpaketet ersätts automatiskt med den för närvarande etablerade versionen.

Scenarier för att använda ett jämförelsepaket är:

* När du har ett stort programpaket som refererar till flera tjänstmanifestfiler och/eller flera kodpaket, konfigurationspaket eller datapaket.
* När du har ett distributionssystem som genererar bygglayouten direkt från programversionsprocessen. I det här fallet, även om koden inte har ändrats, får nybyggda sammansättningar en annan kontrollsumma. Om du använder ett fullständigt programpaket måste du uppdatera versionen på alla kodpaket. Med hjälp av ett jämförelsepaket anger du bara de filer som har ändrats och manifestfilerna där versionen har ändrats.

När ett program uppgraderas med Visual Studio publiceras ett jämförelsepaket automatiskt. Om du vill skapa ett jämförelsepaket manuellt måste programmanifestet och tjänstmanifesten uppdateras, men endast de ändrade paketen ska inkluderas i det slutliga programpaketet.

Låt oss till exempel börja med följande program (versionsnummer som tillhandahålls för att underlätta förståelsen):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Anta att du bara ville uppdatera kodpaketet för tjänsten1 med hjälp av ett diff-paket. Det uppdaterade programmet har följande versionsändringar:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

I det här fallet uppdaterar du programmanifestet till 2.0.0 och tjänstmanifestet för service1 för att återspegla uppdateringen av kodpaketet. Mappen för programpaketet skulle ha följande struktur:

```text
app1/
  service1/
    code/
```

Med andra ord, skapa ett komplett programpaket normalt, ta sedan bort alla mappar kod / config / data paket för vilka versionen inte har ändrats.

## <a name="upgrade-application-parameters-independently-of-version"></a>Uppgradera programparametrar oberoende av version

Ibland är det önskvärt att ändra parametrarna för ett Service Fabric-program utan att ändra manifestversionen. Detta kan göras bekvämt med flaggan **-ApplicationParameter** med **Start-ServiceFabricApplicationUpgrade** Azure Service Fabric PowerShell cmdlet. Anta att ett Service Fabric-program har följande egenskaper:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Uppgradera nu programmet med **start-ServiceFabricApplicationUpgrade** cmdlet. Det här exemplet visar en övervakad uppgradering, men en oövervakad uppgradering kan också användas. Information om hur du ser en fullständig beskrivning av flaggor som accepteras av den här cmdleten finns i [Azure Service Fabric PowerShell-modulreferensen](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters)

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Efter uppgraderingen bekräftar du att programmet har uppdaterade parametrar och samma version:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>Uppgraderingar av återställa program

Även om uppgraderingar kan rullas framåt i ett av tre lägen *(Övervakad,* *Oövervakad Auto*eller *UnmonitoredManual),* kan de bara återställas i *antingen UnmonitoredAuto* eller *UnmonitoredManual-läge.* Att rulla tillbaka i *UnmonitoredAuto-läge* fungerar på samma sätt som att rulla framåt med undantag för att standardvärdet för *UpgradeReplicaSetCheckTimeout* är annorlunda - se [Parametrar för programuppgradering](service-fabric-application-upgrade-parameters.md). Att rulla tillbaka i *oövervakatManual-läge* fungerar på samma sätt som att rulla framåt - återställningen pausas efter att ha slutfört varje UD och måste uttryckligen återupptas med [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) för att fortsätta med återställningen.

Återställningar kan utlösas automatiskt när hälsoprinciperna för en uppgradering i *övervakat* läge med en *FailureAction* of *Rollback* bryts (se [Parametrar för programuppgradering)](service-fabric-application-upgrade-parameters.md)eller uttryckligen använder [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Under återställningen kan värdet *för UpgradeReplicaSetCheckTimeout* och läget fortfarande ändras när som helst med hjälp av [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>Nästa steg
[Om du uppgraderar programmet med Visual Studio](service-fabric-application-upgrade-tutorial.md) går du igenom en programuppgradering med Visual Studio.

[Om du uppgraderar programmet med Powershell](service-fabric-application-upgrade-tutorial-powershell.md) får du en programuppgradering med PowerShell.

Styr hur programmet uppgraderas med hjälp av [uppgraderingsparametrar](service-fabric-application-upgrade-parameters.md).

Gör dina programuppgraderingar kompatibla genom att lära dig hur du använder [data serialisering](service-fabric-application-upgrade-data-serialization.md).

Åtgärda vanliga problem i programuppgraderingar genom att referera till stegen i [Felsökning av programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).
