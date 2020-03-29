---
title: Rapportera och kontrollera hälsotillståndet med Azure Service Fabric
description: Lär dig hur du skickar hälsorapporter från din tjänstkod och hur du kontrollerar tjänstens hälsa med hjälp av hälsoövervakningsverktygen som Azure Service Fabric tillhandahåller.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 2b7a9c44a84e3ce15eaec22c8f57bb48f79dae05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464635"
---
# <a name="report-and-check-service-health"></a>Rapportera och kontrollera hälsan hos tjänster
När dina tjänster stöter på problem beror din förmåga att svara på och åtgärda incidenter och avbrott på din förmåga att upptäcka problemen snabbt. Om du rapporterar problem och fel till hälsohanteraren för Azure Service Fabric från din tjänstkod kan du använda standardverktyg för hälsoövervakning som Service Fabric tillhandahåller för att kontrollera hälsotillståndet.

Det finns tre sätt att rapportera hälsotillstånd från tjänsten:

* Använd [Partitions-](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) eller [CodePackageActivationContext-objekt.](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext)  
  Du kan `Partition` använda `CodePackageActivationContext` och objekt för att rapportera hälsotillståndet för element som ingår i den aktuella kontexten. Kod som körs som en del av en replik kan till exempel rapportera hälsotillstånd endast på den repliken, den partition som den tillhör och det program som den är en del av.
* Använd `FabricClient`.   
  Du kan `FabricClient` använda för att rapportera hälsotillstånd från tjänstkoden om klustret inte är [säkert](service-fabric-cluster-security.md) eller om tjänsten körs med administratörsbehörighet. De flesta verkliga scenarier använder inte oskyddade kluster eller ger administratörsbehörighet. Med `FabricClient`kan du rapportera hälsotillstånd för alla entiteter som ingår i klustret. Men helst bör servicekoden bara skicka rapporter som är relaterade till sin egen hälsa.
* Använd REST-API:erna på kluster-, program-, distribuerade program-, tjänst-, tjänstpaket-, partitions-, replik- eller nodnivåerna. Detta kan användas för att rapportera hälsotillstånd inifrån en behållare.

I den här artikeln får du ett exempel som rapporterar hälsotillstånd från servicekoden. Exemplet visar också hur verktygen som tillhandahålls av Service Fabric kan användas för att kontrollera hälsotillståndet. Den här artikeln är avsedd att vara en snabb introduktion till hälsoövervakningsfunktionerna i Service Fabric. Mer detaljerad information finns i serien med djupgående artiklar om hälsa som börjar med länken i slutet av den här artikeln.

## <a name="prerequisites"></a>Krav
Du måste ha följande installerat:

* Visual Studio 2015 eller Visual Studio 2019
* Service Tyg SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Så här skapar du ett lokalt säkert utvecklingskluster
* Öppna PowerShell med administratörsbehörighet och kör följande kommandon:

![Kommandon som visar hur du skapar ett säkert utvecklingskluster](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Så här distribuerar du ett program och kontrollerar dess
1. Öppna Visual Studio som administratör.
1. Skapa ett projekt med hjälp av den **tillståndskänsliga** servicemallen.
   
    ![Skapa ett Service Fabric-program med tillståndskänslig tjänst](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Tryck på **F5** för att köra programmet i felsökningsläge. Programmet distribueras till det lokala klustret.
1. När programmet har körts högerklickar du på ikonen Lokal klusterhanteraren i meddelandefältet och väljer **Hantera lokalt kluster** på snabbmenyn för att öppna Service Fabric Explorer.
   
    ![Öppna Service Fabric Explorer från meddelandefältet](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. Programmets hälsotillstånd ska visas som i den här bilden. Vid denna tid bör programmet vara felfri utan fel.
   
    ![Felfri tillämpning i Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Du kan också kontrollera hälsotillståndet med powershell. Du kan ```Get-ServiceFabricApplicationHealth``` använda för att kontrollera ett programs ```Get-ServiceFabricServiceHealth``` hälsa och du kan använda för att kontrollera en tjänsts hälsa. Hälsorapporten för samma program i PowerShell finns i den här bilden.
   
    ![Felfri tillämpning i PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Så här lägger du till anpassade hälsohändelser i tjänstkoden
Projektmallarna För Service Fabric i Visual Studio innehåller exempelkod. Följande steg visar hur du kan rapportera anpassade hälsohändelser från tjänstkoden. Sådana rapporter visas automatiskt i standardverktygen för hälsoövervakning som Service Fabric tillhandahåller, till exempel Service Fabric Explorer, Hälsovy för Azure-portalen och PowerShell.

1. Öppna programmet som du skapade tidigare i Visual Studio eller skapa ett nytt program med hjälp av den **tillståndskänsliga tjänsten** Visual Studio-mallen.
1. Öppna Stateful1.cs filen och hitta anropet `myDictionary.TryGetValueAsync` `RunAsync` i metoden. Du kan se att `result` den här metoden returnerar en som innehåller det aktuella värdet för räknaren eftersom nyckellogiken i det här programmet är att hålla ett antal igång. Om det här programmet var ett verkligt program, och om bristen på resultat utgjorde ett fel, skulle du vilja flagga den händelsen.
1. Om du vill rapportera en hälsohändelse när bristen på resultat representerar ett fel lägger du till följande steg.
   
    a. Lägg `System.Fabric.Health` till namnområdet i Stateful1.cs filen.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Lägga till följande `myDictionary.TryGetValueAsync` kod efter samtalet
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Vi rapporterar replikhälsa eftersom det rapporteras från en tillståndskänslig tjänst. Parametern `HealthInformation` lagrar information om hälsoproblem som rapporteras.
   
    Om du hade skapat en tillståndslös tjänst använder du följande
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Om tjänsten körs med administratörsbehörighet eller om klustret inte är [säkert](service-fabric-cluster-security.md)kan du också använda `FabricClient` för att rapportera hälsotillstånd som visas i följande steg.  
   
    a. Skapa `FabricClient` instansen `var myDictionary` efter deklarationen.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Lägg till följande `myDictionary.TryGetValueAsync` kod efter samtalet.
   
    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.Context.PartitionId,
            this.Context.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```
1. Låt oss simulera detta fel och se det dyka upp i hälsoövervakningsverktygen. Om du vill simulera felet kommenterar du den första raden i hälsorapporteringskoden som du lade till tidigare. När du har kommenterat den första raden ser koden ut som följande exempel.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Den här koden utlöser `RunAsync` hälsorapporten varje gång körs. När du har gjort ändringen trycker du på **F5** för att köra programmet.
1. När programmet har körts öppnar du Service Fabric Explorer för att kontrollera programmets hälsotillstånd. Den här gången visar Service Fabric Explorer att programmet är felfritt. Programmet visas som fel eftersom felet som rapporterades från koden som vi lade till tidigare.
   
    ![Felprogram i Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Om du väljer den primära repliken i trädvyn i Service Fabric Explorer ser du att **hälsotillståndet** också anger ett fel. Service Fabric Explorer visar också information om `HealthInformation` hälsorapporten som har lagts till i parametern i koden. Du kan se samma hälsorapporter i PowerShell och Azure-portalen.
   
    ![Replikhälsa i Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Den här rapporten finns kvar i hälsohanteraren tills den ersätts av en annan rapport eller tills repliken tas bort. Eftersom vi inte `TimeToLive` har angett för `HealthInformation` den här hälsorapporten i objektet upphör rapporten aldrig att gälla.

Vi rekommenderar att hälsa ska rapporteras på den mest detaljerade nivån, vilket i det här fallet är repliken. Du kan också `Partition`rapportera hälsotillstånd på .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Om du `Application`vill `DeployedApplication`rapportera `DeployedServicePackage`hälsotillstånd i , använda och använda `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Nästa steg
* [Djupdykning på Service Fabric hälsa](service-fabric-health-introduction.md)
* [REST API för rapportering av tjänstens hälsotillstånd](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [REST API för rapportering av programhälsa](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)

