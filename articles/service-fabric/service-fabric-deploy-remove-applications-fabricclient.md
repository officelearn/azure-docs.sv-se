---
title: Azure Service Fabric-programdistribution | Microsoft Docs
description: Använd FabricClient APIs för att distribuera och ta bort program i Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 50c487e6bad2a009b4f719d44b129ba860432e28
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Distribuera och ta bort program med hjälp av FabricClient
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient-API:er](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

När en [programtyp har paketerats][10], den är klar för distribution till Azure Service Fabric-kluster. Distributionen omfattar följande tre steg:

1. Ladda upp programpaketet image store
2. Registrera programtypen
3. Ta bort programmet paketet från avbildningsarkivet
4. Skapa programinstansen

När ett program distribueras och en instans som körs i klustret kan du ta bort programinstansen och dess programtyp. Om du vill ta bort ett program från klustret omfattar följande steg:

1. Ta bort (eller ta bort) för att köra programinstansen
2. Avregistrera programtypen om du inte längre behöver

Om du använder Visual Studio för att distribuera och felsöka program i klustret lokal utveckling, hanteras de föregående stegen automatiskt via ett PowerShell-skript.  Det här skriptet finns i den *skript* mappen i projektet för konsolprogrammet. Den här artikeln innehåller bakgrundsinformation om vad skriptet gör så att du kan utföra samma åtgärder utanför Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Anslut till klustret
Anslut till klustret genom att skapa en [FabricClient](/dotnet/api/system.fabric.fabricclient) instansen innan du kör något av kodexemplen i den här artikeln. Exempel på att ansluta till ett kluster för lokal utveckling eller en fjärrklusterkontext eller ett kluster som skyddas med Azure Active Directory, X509 certifikat eller Windows Active Directory finns [Anslut till en säker kluster](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). För att ansluta till lokal utveckling klustret, kör du följande:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Ladda upp programpaketet
Anta att du skapar och paketerar ett program med namnet *MyApplication* i Visual Studio. Som standard är namnet på programmet som anges i ApplicationManifest.xml ”MyApplicationType”.  Programpaket som innehåller nödvändiga programmanifestet, service manifest och paket i kod-config-data, finns i *C:\Users\&lt; användarnamnet&gt;\Documents\Visual Studio 2017\Projects\MyApplication\MyApplication\pkg\Debug*.

Ladda upp programpaketet placeras på en plats som kan nås av de interna Service Fabric-komponenterna. Service Fabric verifierar programpaketet vid registrering av programpaketet. Om du vill kontrollera programpaket lokalt (d.v.s. innan du laddar upp) kan använda den [Test ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) cmdlet.

Den [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API överför programpaketet till klustret image store. 

Om programmet paketet är stort och/eller har många filer, kan du [komprimera](service-fabric-package-apps.md#compress-a-package) och kopierar den till image store med hjälp av PowerShell. Komprimeringen minskar storleken och antalet filer.

Se [förstå anslutningssträngen för image store](service-fabric-image-store-connection-string.md) för ytterligare information om image store och avbildningen lagra anslutningssträngen.

## <a name="register-the-application-package"></a>Registrera programpaketet
Programtypen och versionen har deklarerats i programmanifestet blir tillgängliga för användning när programpaketet har registrerats. Systemet läser det paket som överförts i det föregående steget, verifierar paketet, bearbetar paketinnehållet och kopierar bearbetade paketet till en plats för internt system.  

Den [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API registrerar programmet skriver i klustret och gör den tillgänglig för distribution.

Den [GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) API ger information om alla programtyper av som har registrerats. Du kan använda detta API för att avgöra när registreringen är klar.

## <a name="remove-an-application-package-from-the-image-store"></a>Ta bort ett programpaket från avbildningsarkivet
Vi rekommenderar att du tar bort programpaketet när programmet har registrerats.  Ta bort programpaket från avbildningsarkivet frigör systemresurser.  Att hålla oanvända programpaket förbrukar disklagring och leder till problem med prestanda. Ta bort programpaketet från den avbildningen store med hjälp av den [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) API.

## <a name="create-an-application-instance"></a>Skapa en instans av programmet
Du kan skapa en instans av ett program från alla program som har registrerats med hjälp av den [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) API. Namnet på varje program måste börja med den *”fabric”:* schemat och måste vara unikt för varje förekomst av programmet (inom ett kluster). Alla standardtjänster som definierats i programmanifestet av programmet måltypen skapas också.

Flera instanser av programmet kan skapas för en viss version av typen registrerade programmet. Varje instans av programmet körs i isolering med sin egen arbetskatalog och processer.

Se som heter program och tjänster som körs i klustret, kör den [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) och [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) API: er.

## <a name="create-a-service-instance"></a>Skapa en instans av tjänsten
Du kan skapa en instans av en tjänst från en typ med hjälp av [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API.  Om tjänsten har deklarerats som en standardtjänst i programmanifestet instansieras tjänsten när programmet instansieras.  Anropar den [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API för en tjänst som redan har instansierats returneras ett undantag av typen FabricException som innehåller en felkod med värdet FabricErrorCode.ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Ta bort en instans av tjänsten
När en instans av tjänsten är inte längre behövs, kan du ta bort den från kör programinstansen genom att anropa den [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) API.  

> [!WARNING]
> Den här åtgärden kan inte ångras och tillståndet för tjänsten kan inte återställas.

## <a name="remove-an-application-instance"></a>Ta bort en instans av programmet
När en instans av programmet inte längre behövs, om du permanent ta bort den med hjälp av namnet på [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) API. [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) tar automatiskt bort alla tjänster som hör till programmet även, permanent tar du bort alla tillstånd för tjänsten.

> [!WARNING]
> Den här åtgärden kan inte ångras och går inte att återställa programtillstånd.

## <a name="unregister-an-application-type"></a>Avregistrera en programtyp
När en viss version av en typ av program inte längre behövs, bör du avregistrera den viss versionen av den typ som använder den [Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) API. Avregistrera oanvända versioner av programtyper Frigör lagringsutrymme som används av image store. En version av en typ av program kan avregistreras så länge inga program instansieras mot den här versionen av programtypen och inga väntande programuppgraderingar refererar till den här versionen av programtypen.

## <a name="troubleshooting"></a>Felsökning
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Kopiera ServiceFabricApplicationPackage begär en ImageStoreConnectionString
Service Fabric SDK-miljö bör redan ha rätt standardvärdena ställa in. Men om det behövs, ImageStoreConnectionString för alla kommandon bör matcha värdet som Service Fabric-klustret. Du hittar ImageStoreConnectionString i klustret-manifestet hämtades med den [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) och Get-ImageStoreConnectionStringFromClusterManifest kommandon:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

Den **Get-ImageStoreConnectionStringFromClusterManifest** cmdlet, som är del av Service Fabric SDK PowerShell-modulen används för att hämta anslutningssträngen för image store.  Om du vill importera modulen SDK, kör du:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```


ImageStoreConnectionString hittades i klustermanifestet:

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Se [förstå anslutningssträngen för image store](service-fabric-image-store-connection-string.md) för ytterligare information om image store och avbildningen lagra anslutningssträngen.

### <a name="deploy-large-application-package"></a>Distribuera stora programpaket
Problem: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API tiden av stora programpaket (ordning GB).
Försök:
- Ange en längre tidsgräns för [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) -metoden med `timeout` parameter. Tidsgränsen är som standard 30 minuter.
- Kontrollera nätverksanslutningen mellan källdatorn och kluster. Om anslutningen är långsam, Överväg att använda en dator med en bättre nätverksanslutning.
Om klientdatorn är i en annan region än klustret, Överväg att använda en klientdator i en närmare eller samma region som klustret.
- Kontrollera om du träffa externa begränsning. Till exempel när image store är konfigurerad för att använda azure storage, kan överför att begränsas.

Problem: Överför paketet slutfördes, men [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API timeout. Försök:
- [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar till image store.
Komprimeringen minskar storleken och det antal filer, vilket i sin tur minskar mängden trafik och fungerar som Service Fabric måste utföra. Överföringen kan ta längre tid (särskilt om du inkluderar komprimering-tid), men registrera och avregistrera programtypen är snabbare.
- Ange en längre tidsgräns för [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API med `timeout` parameter.

### <a name="deploy-application-package-with-many-files"></a>Distribuera programpaket med många filer
Problem: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) tidsgränsen uppnås för ett programpaket med många filer (efter tusentalsavgränsare).
Försök:
- [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar till image store. Komprimeringen minskar antalet filer.
- Ange en längre tidsgräns för [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) med `timeout` parameter.

## <a name="code-example"></a>Kodexempel
I följande exempel kopierar ett programpaket till image store, tillhandahåller programtyp, skapas en instans av programmet, skapar en tjänstinstans, tar bort programinstansen un-bestämmelser programtypen och tar bort programpaketet från avbildningsarkivet.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2017\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>Nästa steg
[Uppgradering av Service Fabric-programmet](service-fabric-application-upgrade.md)

[Service Fabric hälsa introduktion](service-fabric-health-introduction.md)

[Diagnostisera och felsöka en Service Fabric-tjänst](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modellen är ett program i Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
