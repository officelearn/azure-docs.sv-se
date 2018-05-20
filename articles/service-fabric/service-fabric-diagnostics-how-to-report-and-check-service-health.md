---
title: Rapportera och kontrollera hälsan med Azure Service Fabric | Microsoft Docs
description: Lär dig hur du skickar hälsorapporter från koden för tjänsten och hur du kontrollerar hälsotillståndet för din tjänst med hjälp av hälsotillstånd övervakningsverktyg som ger Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: mfussell
editor: ''
ms.assetid: 7c712c22-d333-44bc-b837-d0b3603d9da8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/2/2017
ms.author: dekapur
ms.openlocfilehash: 82ee3cbca40713d527f64ae4698cb9ce64a10215
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="report-and-check-service-health"></a>Rapportera och kontrollera hälsan hos tjänster
När dina tjänster har problem, beror dina möjligheter att svara på och åtgärda incidenter och avbrott på möjligheten att snabbt identifiera problemen. Om du rapportera problem och fel till Azure Service Fabric health manager från service koden använder du standard hälsoövervakning verktygen i Service Fabric för att kontrollera hälsostatus.

Det finns tre sätt att du kan rapportera hälsa från tjänsten:

* Använd [Partition](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) eller [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext) objekt.  
  Du kan använda den `Partition` och `CodePackageActivationContext` objekt rapportera om hälsan för element som ingår i den aktuella kontexten. Kod som körs som en del av en replik kan rapportera hälsa endast på repliken och den partition som tillhör det program som den är en del av.
* Använd `FabricClient`.   
  Du kan använda `FabricClient` för rapporten hälsa från kod som om klustret inte [säker](service-fabric-cluster-security.md) eller om tjänsten körs med administratörsrättigheter. De flesta verkliga scenarier inte använder oskyddade kluster, eller ange admin-privilegier. Med `FabricClient`, kan du rapportera hälsotillstånd på en enhet som är en del av klustret. Vi rekommenderar dock bör Tjänstkod bara skicka rapporter som är relaterade till sin egen hälsa.
* Använda REST-API: er på klustret, program, distribuerat program, tjänst, servicepaket, partition, replik eller nod nivåer. Detta kan användas för att rapportera hälsa från i en behållare.

Den här artikeln vägleder dig genom ett exempel som rapporterar hälsotillståndet från kod. Exemplet visar även hur Service Fabric-verktygen kan användas för att kontrollera hälsostatus. Den här artikeln är avsedd att vara en snabb introduktion till funktionerna i Service Fabric hälsoövervakning. Mer detaljerad information kan du läsa serien djupgående artiklar om hälsa som börjar med länken i slutet av den här artikeln.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha följande installerat:

* Visual Studio 2015 eller Visual Studio 2017
* Service Fabric SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Skapa ett kluster i lokal säker dev
* Öppna PowerShell med administratörsrättigheter och kör följande kommandon:

![Kommandon som visar hur du skapar en säker dev-kluster](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Att distribuera ett program och kontrollera dess hälsa
1. Öppna Visual Studio som administratör.
2. Skapa ett projekt med hjälp av den **tillståndskänslig Service** mall.
   
    ![Skapa ett Service Fabric-program med tillståndskänslig Service](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
3. Tryck på **F5** att köra programmet i felsökningsläge. Programmet har distribuerats till det lokala klustret.
4. När programmet körs, högerklicka på lokal Klusterhanterare ikonen i meddelandefältet och väljer **hantera lokala klustret** från snabbmenyn för att öppna Service Fabric Explorer.
   
    ![Öppna Service Fabric Explorer från meddelandefältet](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
5. Hälsotillståndet för programmet ska visas som i den här avbildningen. Programmet ska vara felfria utan fel för tillfället.
   
    ![Felfri programmet i Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
6. Du kan också kontrollera hälsan med hjälp av PowerShell. Du kan använda ```Get-ServiceFabricApplicationHealth``` för att kontrollera hälsotillståndet för ett program och du kan använda ```Get-ServiceFabricServiceHealth``` att kontrollera tjänstens hälsa. Hälsorapport för samma program i PowerShell finns i den här avbildningen.
   
    ![Felfri program i PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Lägga till anpassade hälsa händelser i koden för tjänsten
Mallar för Service Fabric-projekt i Visual Studio innehåller exempelkod. Följande steg visar hur du kan rapportera anpassade hälsa händelser från koden för tjänsten. Rapporterna visas automatiskt i standardverktyg för övervakning av hälsotillstånd för att Service Fabric innehåller till exempel Service Fabric Explorer, hälsotillståndsvy för Azure portal och PowerShell.

1. Öppna programmet som du skapade tidigare i Visual Studio eller skapa ett nytt program med hjälp av den **tillståndskänslig Service** Visual Studio-mall.
2. Öppna filen Stateful1.cs och Sök efter den `myDictionary.TryGetValueAsync` -anrop i den `RunAsync` metoden. Du kan se att den här metoden returnerar en `result` som innehåller det aktuella värdet för räknaren eftersom logiken i det här programmet är att räkna körs. Om detta har ett riktigt program, och om bristande resultat visas ett fel, skulle du vill flagga händelsen.
3. Lägg till följande steg om du vill rapportera hälsohändelse när bristen på resultat som representerar ett fel.
   
    a. Lägg till den `System.Fabric.Health` namnområdet till Stateful1.cs-filen.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. Lägg till följande kod efter den `myDictionary.TryGetValueAsync` anropa
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Vi rapporterar replik hälsotillstånd eftersom rapporteras från en tillståndskänslig service. Den `HealthInformation` parametern lagrar information om hälsa problemet som rapporteras.
   
    Om du har skapat en tillståndslös tjänst kan du använda följande kod
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
4. Om din tjänst körs med administratörsrättigheter eller om klustret inte [säker](service-fabric-cluster-security.md), du kan också använda `FabricClient` för rapporten hälsa som visas i följande steg.  
   
    a. Skapa den `FabricClient` instansen efter den `var myDictionary` deklaration.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Lägg till följande kod efter den `myDictionary.TryGetValueAsync` anropa.
   
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
5. Vi simulera felet och se den visas i hälsa övervakningsverktyg. Kommentera ut den första raden i hälsotillståndsrapportering kod som du lade till tidigare för att simulera felet. När du kommentera ut den första raden ska koden se ut som i följande exempel.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Den här koden utlöses hälsorapporten varje gång `RunAsync` körs. När du har ändrat trycker du på **F5** att köra programmet.
6. Öppna Service Fabric Explorer för att kontrollera hälsotillståndet för programmet när programmet körs. Den här gången visar Service Fabric Explorer att programmet är inte felfri. Detta är på grund av fel som rapporterades från koden som vi har lagt till tidigare.
   
    ![Feltillstånd programmet i Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
7. Om du väljer den primära repliken i trädvyn för Service Fabric Explorer kan du se att **hälsotillstånd** indikerar ett fel för. Service Fabric Explorer visar även hälsa rapportdetaljer som har lagts till i `HealthInformation` parameter i koden. Du kan se samma hälsorapporter i PowerShell och Azure portal.
   
    ![Replik hälsa i Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Den här rapporten finns kvar i hälsoindikatorn förrän den har ersatts av en annan rapport eller tills replikeringen tas bort. Eftersom vi inte har angetts `TimeToLive` för den här hälsorapport i den `HealthInformation` objekt rapporten upphör aldrig att gälla.

Vi rekommenderar att hälsa ska rapporteras på den mest detaljerade nivån, vilket i detta fall är repliken. Du kan också rapportera hälsa för `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

För rapporten hälsa på `Application`, `DeployedApplication`, och `DeployedServicePackage`, Använd `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Nästa steg
* [Ingående om hälsa för Service Fabric](service-fabric-health-introduction.md)
* [REST API för rapportering av tjänstens hälsa](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [REST API för rapportering av programmets hälsotillstånd](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)

