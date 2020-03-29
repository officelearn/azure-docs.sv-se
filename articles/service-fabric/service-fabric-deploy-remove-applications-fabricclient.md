---
title: Azure Service Fabric-distribution med FabricClient
description: Använd FabricClient-API:erna för att distribuera och ta bort program i Service Fabric.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 25b874d1be8ab50d8076ff8fe9423c8cc0187512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75376978"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Distribuera och ta bort program med FabricClient
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [Powershell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient-API:er](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

När en [programtyp har paketerats][10]är den klar för distribution till ett Azure Service Fabric-kluster. Distributionen omfattar följande tre steg:

1. Ladda upp programpaketet till bildarkivet
2. Registrera programtypen
3. Ta bort programpaketet från bildarkivet
4. Skapa programinstansen

När du har distribuerat ett program och kört en instans i klustret kan du ta bort programinstansen och dess programtyp. Ta bort ett program helt från klustret genom att följa dessa steg:

1. Ta bort (eller ta bort) den programinstans som körs
2. Avregistrera programtypen om du inte längre behöver den

Om du använder Visual Studio för att distribuera och felsöka program i det lokala utvecklingsklustret hanteras alla föregående steg automatiskt via ett PowerShell-skript.  Det här skriptet finns i mappen *Skript* i programprojektet. Den här artikeln innehåller bakgrund om vad skriptet gör så att du kan utföra samma åtgärder utanför Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Anslut till klustret
Anslut till klustret genom att skapa en [FabricClient-förekomst](/dotnet/api/system.fabric.fabricclient) innan du kör något av kodexemplen i den här artikeln. Exempel på hur du ansluter till ett lokalt utvecklingskluster eller ett fjärrkluster eller kluster som är skyddat med Azure Active Directory, X509-certifikat eller Windows Active Directory finns [i Anslut till ett säkert kluster](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Om du vill ansluta till det lokala utvecklingsklustret kör du följande exempel:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Ladda upp programpaketet
Anta att du skapar och paketerar ett program med namnet *MyApplication* i Visual Studio. Som standard är programtypnamnet i ApplicationManifest.xml "MyApplicationType".  Programpaketet, som innehåller det nödvändiga programmanifestet, tjänstmanifesten och kod-/config/datapaketen, finns i *C:\Users\&&gt;lt;username \Documents\Visual Studio 2019\Projects\MyApplication\MyApplication\pkg\Debug*.

Om du laddar upp programpaketet placeras det på en plats som är tillgänglig för de interna Service Fabric-komponenterna. Service Fabric verifierar programpaketet under registreringen av programpaketet. Men om du vill verifiera programpaketet lokalt (det vill säga innan du laddar upp), använd [testserviceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) cmdlet.

[CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API överför programpaketet till klusteravbildningsarkivet. 

Om programpaketet är stort och/eller har många filer kan du [komprimera det](service-fabric-package-apps.md#compress-a-package) och kopiera det till bildarkivet med PowerShell. Komprimeringen minskar storleken och antalet filer.

Se [Förstå anslutningssträngen](service-fabric-image-store-connection-string.md) för bildlagring för kompletterande information om anslutningssträngen för bildarkivet och bildarkivet.

## <a name="register-the-application-package"></a>Registrera ansökningspaketet
Programtypen och versionen som deklareras i programmanifestet blir tillgängliga för användning när programpaketet registreras. Systemet läser paketet som laddades upp i föregående steg, verifierar paketet, bearbetar paketets innehåll och kopierar det bearbetade paketet till en intern systemplats.  

[Api:et provisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) registrerar programtypen i klustret och gör den tillgänglig för distribution.

[Api:et GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) innehåller information om alla programtyper som har registrerats. Du kan använda det här API:et för att avgöra när registreringen är klar.

## <a name="remove-an-application-package-from-the-image-store"></a>Ta bort ett programpaket från bildarkivet
Vi rekommenderar att du tar bort programpaketet när programmet har registrerats.  Om du tar bort programpaket från avbildningsarkivet frigörs systemresurser.  Om du behåller oanvända programpaket förbrukar disklagring och leder till problem med programmets prestanda. Ta bort programpaketet från avbildningsarkivet med hjälp av [Api:et RemoveApplicationPackage.](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage)

## <a name="create-an-application-instance"></a>Skapa en programinstans
Du kan instansiera ett program från alla programtyper som har registrerats med hjälp av [Api:et CreateApplicationAsync.](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) Namnet på varje program måste börja med *schemat "fabric:"* och måste vara unikt för varje programinstans (inom ett kluster). Alla standardtjänster som definieras i programmanifestet för målprogramtypen skapas också.

Flera programinstanser kan skapas för en viss version av en registrerad programtyp. Varje programinstans körs isolerat, med sin egen arbetskatalog och uppsättning processer.

Om du vill se vilka namngivna program och tjänster som körs i klustret kör du API:erna [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) och [GetServiceListAsync.](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)

## <a name="create-a-service-instance"></a>Skapa en tjänstinstans
Du kan instansiera en tjänst från en tjänsttyp med hjälp av [Api:et För CreateServiceAsync.](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync)  Om tjänsten deklareras som standardtjänst i programmanifestet instansieras tjänsten direktutanförs när programmet instansieras.  Anropa [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API för en tjänst som redan är instansierad returnerar ett undantag av typen FabricException. Undantaget innehåller en felkod med värdet FabricErrorCode.ServiceExreadyExists.

## <a name="remove-a-service-instance"></a>Ta bort en tjänstinstans
När en tjänstinstans inte längre behövs kan du ta bort den från programinstansen som körs genom att anropa DeleteServiceAsync API.When a service instance is no longer needed, you can remove it from the running application instance by calling the [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) API.  

> [!WARNING]
> Den här åtgärden kan inte ångras och servicetillståndet kan inte återställas.

## <a name="remove-an-application-instance"></a>Ta bort en programinstans
När en programinstans inte längre behövs kan du ta bort den permanent med namnet med hjälp av [Api:et DeleteApplicationAsync.](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) tar automatiskt bort alla tjänster som tillhör programmet också, och tar bort alla tjänsttillstånd permanent.

> [!WARNING]
> Den här åtgärden kan inte återföras och programtillståndet kan inte återställas.

## <a name="unregister-an-application-type"></a>Avregistrera en programtyp
När en viss version av en programtyp inte längre behövs bör du avregistrera den specifika versionen av programtypen med [API:et Unregister-ServiceFabricApplicationType.](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) Om du avregistrerar oanvända versioner av programtyper frigörs lagringsutrymme som används av bildarkivet. En version av en programtyp kan avregistreras så länge inga program instansieras mot den versionen av programtypen. Programtypen kan inte heller ha några väntande programuppgraderingar som refererar till den versionen av programtypen.

## <a name="troubleshooting"></a>Felsökning
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage ber om en ImageStoreConnectionString
Service Fabric SDK-miljön bör redan ha rätt standardinställningar inställda. Men om det behövs bör ImageStoreConnectionString för alla kommandon matcha det värde som Service Fabric-klustret använder. Du hittar imagestoreConnectionString i klustermanifestet, hämtat med kommandona [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) och Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

**Get-ImageStoreConnectionStringFromClusterManifest** cmdlet, som är en del av Modulen Service Fabric SDK PowerShell, används för att hämta anslutningssträngen för bildarkivet.  Om du vill importera SDK-modulen kör du:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```


ImageStoreConnectionString finns i klustermanifestet:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Se [Förstå anslutningssträngen](service-fabric-image-store-connection-string.md) för bildlagring för kompletterande information om anslutningssträngen för bildarkivet och bildarkivet.

### <a name="deploy-large-application-package"></a>Distribuera ett stort programpaket
Problem: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API time out för ett stort programpaket (ordning gb).
Försök:
- Ange en större timeout för [Metoden CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) med `timeout` parameter. Som standard är tidsgränsen 30 minuter.
- Kontrollera nätverksanslutningen mellan källdatorn och klustret. Om anslutningen är långsam bör du överväga att använda en dator med en bättre nätverksanslutning.
Om klientdatorn finns i en annan region än klustret bör du överväga att använda en klientdator i en närmare eller samma region som klustret.
- Kontrollera om du slår på extern begränsning. När avbildningsarkivet till exempel är konfigurerat för att använda azure storage kan överföringen begränsas.

Problem: Ladda upp paketet har slutförts, men [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API time out. Försök:
- [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar till bildarkivet.
Komprimeringen minskar storleken och antalet filer, vilket i sin tur minskar mängden trafik och arbete som Service Fabric måste utföra. Uppladdningen kan vara långsammare (särskilt om du inkluderar komprimeringstiden), men registrera och avregistrera programtypen är snabbare.
- Ange en större timeout för [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API med `timeout` parametern.

### <a name="deploy-application-package-with-many-files"></a>Distribuera programpaket med många filer
Problem: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) time out för ett programpaket med många filer (ordning på tusentals).
Försök:
- [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar till bildarkivet. Komprimeringen minskar antalet filer.
- Ange en större timeout för [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) med `timeout` parametern.

## <a name="code-example"></a>Kodexempel
I följande exempel kopieras ett programpaket till bildarkivet och programtypen etableras. Sedan skapar exemplet en programinstans och skapar en tjänstinstans. Slutligen tar exemplet bort programinstansen, avetablerar programtypen och tar bort programpaketet från avbildningsarkivet.

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
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2019\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
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

[Diagnostisera och felsöka en service med servicevävnad](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modellera ett program i Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
