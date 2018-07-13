---
title: Kommunikation för roller i molntjänster | Microsoft Docs
description: Rollinstanser i Cloud Services kan ha (http, https, tcp, udp) definierade slutpunkter för dem som kommunicerar med utsidan eller mellan andra rollinstanser.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7008a083-acbe-4fb8-ae60-b837ef971ca1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: jeconnoc
ms.openlocfilehash: 9d6cc542b6af0b508529c1392e16df93e1ba084d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005952"
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Aktivera kommunikation för rollinstanser i azure
Molntjänstroller kommunicerar via interna och externa anslutningar. Externa anslutningar kallas **inkommande slutpunkter** medan interna anslutningar kallas **interna slutpunkter**. Det här avsnittet beskrivs hur du ändrar den [tjänstdefinitionsfilen](cloud-services-model-and-package.md#csdef) att skapa slutpunkter.

## <a name="input-endpoint"></a>Slutpunkt för indata
Slutpunkt för indata används när du vill exponera en port på utsidan. Anger vilken typ av protokoll och porten för den slutpunkt som gäller för externa och interna portar för slutpunkten. Om du vill kan du ange en annan Intern port för slutpunkten med den [lokal port](https://msdn.microsoft.com/library/azure/gg557552.aspx#InputEndpoint) attribut.

Slutpunkt för indata kan använda följande protokoll: **http, https, tcp, udp**.

När du skapar en slutpunkt för indata till den **InputEndpoint** underordnat element till den **slutpunkter** element i en webb-eller arbetsroll.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Instans indataslutpunkten
Instans indataslutpunkter liknar inkommande slutpunkter, men kan du mappa specifika offentliga portar för varje enskild roll-instans med hjälp av portvidarebefordran på belastningsutjämnaren. Du kan ange en enskild offentlig port eller ett portintervall.

Instans-indataslutpunkten kan bara använda **tcp** eller **udp** som protokoll.

Om du vill skapa en slutpunkt för indata instans, lägger du till den **InstanceInputEndpoint** underordnat element till den **slutpunkter** element i en webb-eller arbetsroll.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Intern slutpunkt
Interna slutpunkter som är tillgängliga för instans-instans-kommunikation. Porten är valfri och om det utelämnas används en dynamisk port är tilldelad till slutpunkten. Du kan använda ett portintervall. Det finns en gräns på fem interna slutpunkter per roll.

Den interna slutpunkten kan använda följande protokoll: **http, tcp, udp, alla**.

När du skapar en intern slutpunkt för indata till den **InternalEndpoint** underordnat element till den **slutpunkter** element i en webb-eller arbetsroll.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Du kan också använda ett portintervall.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Jämfört med Worker-roller Web-roller
Det finns en mindre skillnad med slutpunkter när du arbetar med både worker och web-roller. Web-roll måste ha minst en enda slutpunkt för indata med hjälp av den **HTTP** protokoll.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Med .NET SDK för att få åtkomst till en slutpunkt
Azure Managed-biblioteket innehåller metoder för rollinstanser att kommunicera vid körning. Kod som körs i en rollinstans, kan du hämta information om finns andra rollinstanser och deras slutpunkter, samt information om den aktuella rollinstansen.

> [!NOTE]
> Du kan bara hämta information om rollinstanser som körs i din molntjänst och som definierar minst en intern slutpunkt. Du kan inte hämta data om rollinstanser som körs på en annan tjänst.
> 
> 

Du kan använda den [instanser](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.role.instances.aspx) egenskap för att hämta instanser av en roll. Först använder den [CurrentRoleInstance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.currentroleinstance.aspx) att returnera en referens till den aktuella rollinstansen och sedan använda den [rollen](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.role.aspx) som returnerar en referens till rollen själva.

När du ansluter till en rollinstans programmässigt via .NET SDK, är det relativt enkelt att komma åt information om slutpunkten. Du kan exempelvis få porten för en viss slutpunkt med den här koden när du redan har anslutit till en viss roll-miljö:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

Den **instanser** egenskapen returnerar en samling **Rollinstans** objekt. Den här samlingen alltid innehåller den aktuella instansen. Om rollen inte definierar en intern slutpunkt, innehåller den aktuella instansen men inga andra instanser i samlingen. Antalet rollinstanser i samlingen ska alltid vara 1 i de fall där ingen intern slutpunkt som har definierats för rollen. Om rollen definierar en intern slutpunkt, dess instanser kan upptäckas vid körning och antalet instanser i samlingen motsvarar antalet instanser som angetts för rollen i tjänstekonfigurationsfilen.

> [!NOTE]
> Azure hanterade biblioteket ger inte ett sätt att fastställa hälsotillståndet för andra rollinstanser, men du kan implementera sådana hälsotillstånd utvärderingar själv om din tjänst måste den här funktionen. Du kan använda [Azure Diagnostics](cloud-services-dotnet-diagnostics.md) att få information om hur du kör rollinstanser.
> 
> 

Du kan använda för att fastställa portnumret för en intern slutpunkt på en rollinstans som den [InstanceEndpoints](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.instanceendpoints.aspx) egenskapen att returnera ett Dictionary-objekt som innehåller slutpunktsnamn och deras motsvarande IP-adresser och portar. Den [IPEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstanceendpoint.ipendpoint.aspx) egenskapen returnerar IP-adressen och porten för den angivna slutpunkten. Den **PublicIPEndpoint** egenskapen returnerar porten för en belastningsutjämnad slutpunkt. IP-adress-delen av den **PublicIPEndpoint** egenskapen används inte.

Här är ett exempel som upprepas över rollinstanser.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Här är ett exempel på en arbetsroll som hämtar den slutpunkt som exponeras via tjänstdefinitionen och börjar lyssna efter anslutningar.

> [!WARNING]
> Den här koden fungerar endast för en distribuerad tjänst. När du kör i Azure Compute Emulator, tjänsten konfigurationselement som skapar direkt port slutpunkter (**InstanceInputEndpoint** element) ignoreras.
> 
> 

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;

        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;

        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);

        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Regler för nätverkstrafik att styra rollerna
När du har definierat interna slutpunkter kan du lägga till regler för nätverkstrafik (baserat på de slutpunkter som du skapade) att styra hur rollinstanser kan kommunicera med varandra. Följande diagram visar några vanliga scenarier för att styra rollerna:

![Trafik regler scenarier](./media/cloud-services-enable-communication-role-instances/scenarios.png "trafik regler scenarier")

I följande kodexempel visar rolldefinitioner för de roller som visas i diagrammet ovan. Varje rolldefinitionen innehåller minst en intern slutpunkt som definierats:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [!NOTE]
> Begränsning av kommunikation mellan roller kan uppstå med interna slutpunkter för både fasta och tilldelas automatiskt portar.
> 
> 

Som standard när en intern slutpunkt som har definierats, kan kommunikation flöda från en roll på den interna slutpunkten för en roll utan begränsningar. Om du vill begränsa kommunikationen, måste du lägga till en **NetworkTrafficRules** elementet så att den **ServiceDefinition** element i tjänstdefinitionsfilen.

### <a name="scenario-1"></a>Scenario 1
Endast tillåta nätverkstrafik från **WebRole1** till **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Scenario 2
Tillåter endast trafik från **WebRole1** till **WorkerRole1** och **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Scenario 3
Tillåter endast trafik från **WebRole1** till **WorkerRole1**, och **WorkerRole1** till **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Scenario 4
Tillåter endast trafik från **WebRole1** till **WorkerRole1**, **WebRole1** till **WorkerRole2**, och **WorkerRole1**  till **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

En XML-Schemareferens för element som används ovan finns [här](https://msdn.microsoft.com/library/azure/gg557551.aspx).

## <a name="next-steps"></a>Nästa steg
Läs mer om Molntjänsten [modellen](cloud-services-model-and-package.md).

