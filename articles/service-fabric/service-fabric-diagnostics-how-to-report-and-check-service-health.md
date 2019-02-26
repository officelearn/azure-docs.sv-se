---
title: Rapportera och kontrollera hälsa med Azure Service Fabric | Microsoft Docs
description: Lär dig hur du skickar rapporter om hälsotillstånd från koden för tjänsten och hur du kontrollerar hälsotillståndet för din tjänst med hjälp av hälsotillstånd övervakningsverktyg som Azure Service Fabric tillhandahåller.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: mfussell
editor: ''
ms.assetid: 7c712c22-d333-44bc-b837-d0b3603d9da8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 2126157f49bd978d2218986601245cae2e4157b6
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821686"
---
# <a name="report-and-check-service-health"></a>Rapportera och kontrollera hälsan hos tjänster
När dina tjänster får problem kan beror dina möjligheter att svara på och åtgärda incidenter och avbrott på din möjlighet att upptäcka problem snabbt. Om du rapporterar problem och fel till Azure Service Fabric health manager från service koden kan du använda verktygen i Service Fabric för att kontrollera hälsostatus för standard hälsoövervakning.

Det finns tre sätt att du kan rapportera hälsa från tjänsten:

* Använd [Partition](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) eller [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext) objekt.  
  Du kan använda den `Partition` och `CodePackageActivationContext` objekt som ska rapportera om hälsan hos element som ingår i den aktuella kontexten. Kod som körs som en del av en replik kan till exempel rapportera hälsotillstånd endast på den repliken och den partition som den tillhör det program som det är en del av.
* Använd `FabricClient`.   
  Du kan använda `FabricClient` för rapporten hälsa från kod som om klustret inte [säker](service-fabric-cluster-security.md) eller om tjänsten körs med administratörsrättigheter. De flesta verkliga scenarier Använd inte oskyddade kluster, eller ge administratörsprivilegier. Med `FabricClient`, du kan rapportera hälsotillstånd på en enhet som är en del av klustret. Vi rekommenderar dock bör koden bara skicka rapporter som är relaterade till sin egen hälsotillstånd.
* Använda REST-API: er på klustret, program, distribuerat program, service, service-paketet, partition, repliken eller noden nivåer. Detta kan användas för att rapportera hälsa från i en behållare.

Den här artikeln går vi igenom ett exempel som rapporterar hälsa från tjänstkoden. Exemplet visar också hur de verktyg som tillhandahålls av Service Fabric kan användas för att kontrollera hälsostatus. Den här artikeln är avsedd att vara en snabb introduktion till hälsoövervakning funktionerna i Service Fabric. Du kan läsa serien fördjupade artiklar om hälsotillstånd som börjar med en länk i slutet av den här artikeln för mer detaljerad information.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha följande installerat:

* Visual Studio 2015 eller Visual Studio 2017
* Service Fabric SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Att skapa ett kluster för lokal säker utveckling
* Öppna PowerShell med administratörsbehörighet och kör följande kommandon:

![Kommandon som visar hur du skapar ett kluster för säker utveckling](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Att distribuera ett program och kontrollera dess hälsa
1. Öppna Visual Studio som administratör.
1. Skapa ett projekt med hjälp av den **tillståndskänslig tjänst** mall.
   
    ![Skapa ett Service Fabric-program med tillståndskänslig tjänst](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Tryck på **F5** att köra programmet i felsökningsläge. Programmet har distribuerats till det lokala klustret.
1. När programmet körs, högerklicka på ikonen Local Cluster Manager i meddelandefältet och väljer **hantera lokalt kluster** från snabbmenyn för att öppna Service Fabric Explorer.
   
    ![Öppna Service Fabric Explorer från meddelandefältet](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. Programmets hälsotillstånd ska visas som i den här avbildningen. Programmet bör vara felfri utan fel för tillfället.
   
    ![Felfria program i Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. Du kan också kontrollera hälsa med hjälp av PowerShell. Du kan använda ```Get-ServiceFabricApplicationHealth``` att kontrollera hälsotillståndet för ett program, och du kan använda ```Get-ServiceFabricServiceHealth``` att kontrollera tjänstens hälsotillstånd. Hälsorapport för samma program i PowerShell är i den här bilden.
   
    ![Felfria program i PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Att lägga till anpassade hälsohändelser i koden för tjänsten
Mallar för Service Fabric-projekt i Visual Studio innehåller exempelkoden. Följande steg visar hur du kan rapportera anpassade health-händelser från koden för tjänsten. Rapporterna visas automatiskt i standardverktyg för övervakning av hälsotillstånd att Service Fabric tillhandahåller, som Service Fabric Explorer, hälsotillståndsvy för Azure portal och PowerShell.

1. Öppna programmet som du skapade tidigare i Visual Studio, eller skapa ett nytt program med hjälp av den **tillståndskänslig tjänst** Visual Studio-mall.
1. Öppna filen Stateful1.cs och hitta den `myDictionary.TryGetValueAsync` anropa i den `RunAsync` metoden. Du kan se att den här metoden returnerar en `result` som innehåller det aktuella värdet för räknaren eftersom viktiga logiken i det här programmet är att räkna körs. Om detta är ett riktigt program, och om bristen på resultatet visas ett fel, skulle du flaggan händelsen.
1. Lägg till följande steg om du vill rapportera en hälsohändelse när bristen på resultatet representerar ett fel.
   
    a. Lägg till den `System.Fabric.Health` namnområde till filen Stateful1.cs.
   
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
    Vi rapporterar hälsotillståndet för repliken eftersom rapporteras från en tillståndskänslig tjänst. Den `HealthInformation` parametern lagrar information om hälsoproblem som rapporteras.
   
    Om du har skapat en tillståndslös tjänst, Använd följande kod
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Om tjänsten körs med administratörsrättigheter eller om klustret inte [säker](service-fabric-cluster-security.md), du kan också använda `FabricClient` för rapporten hälsa, enligt följande steg.  
   
    a. Skapa den `FabricClient` instans efter den `var myDictionary` deklaration.
   
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
1. Vi simulera detta fel och se hur det visas i Verktyg för övervakning av hälsotillstånd. Kommentera ut den första raden i hälsotillståndet reporting kod som du lade till tidigare för att simulera felet. När du kommentera ut den första raden ska koden se ut som i följande exempel.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Den här koden utlöses hälsorapporten varje gång `RunAsync` körs. När du har gjort ändringen trycker du på **F5** att köra programmet.
1. När programmet körs, öppnar du Service Fabric Explorer för att kontrollera hälsotillståndet för programmet. Den här gången visar Service Fabric Explorer att programmet är i feltillstånd. Det här är på grund av fel som har rapporterats från koden som vi har lagt till tidigare.
   
    ![Felaktigt program i Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Om du väljer den primära repliken i trädvyn för Service Fabric Explorer kan du se att **hälsotillstånd** påvisar ett fel för. Service Fabric Explorer visar även rapporten hälsoinformation som har lagts till i `HealthInformation` parametern i koden. Du kan se samma hälsorapporter i PowerShell och Azure-portalen.
   
    ![Repliken hälsotillstånd i Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Den här rapporten finns kvar i hälsoindikatorn förrän den har ersatts av en annan rapport, eller tills repliken tas bort. Eftersom vi inte har angetts `TimeToLive` för den här hälsorapport i den `HealthInformation` objekt kan rapporten upphör aldrig att gälla.

Vi rekommenderar att health ska rapporteras på den mest detaljerade nivån i det här fallet är repliken. Du kan också rapportera hälsotillstånd på `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

För rapporten hälsa på `Application`, `DeployedApplication`, och `DeployedServicePackage`, använda `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Nästa steg
* [Djupgående om Service Fabrics hälsa](service-fabric-health-introduction.md)
* [REST API för rapportering av tjänstehälsa](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [REST API för rapportering av programmets hälsotillstånd](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)

