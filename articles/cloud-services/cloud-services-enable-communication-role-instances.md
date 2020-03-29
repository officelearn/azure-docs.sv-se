---
title: Kommunikation för roller i molntjänster | Microsoft-dokument
description: Rollinstanser i Molntjänster kan ha slutpunkter (http, https, tcp, udp) definierade för dem som kommunicerar med utsidan eller mellan andra rollinstanser.
services: cloud-services
documentationcenter: ''
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.topic: article
ms.date: 12/14/2016
ms.author: tagore
ms.openlocfilehash: 094e08becf4f3a60c98d89bfae7e7c3a69b677f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386348"
---
# <a name="enable-communication-for-role-instances-in-azure"></a>Aktivera kommunikation för rollinstanser i azure
Molntjänstroller kommunicerar via interna och externa anslutningar. Externa anslutningar kallas **indataslutpunkter** medan interna anslutningar kallas **interna slutpunkter**. I det här avsnittet beskrivs hur du ändrar [tjänstdefinitionen](cloud-services-model-and-package.md#csdef) för att skapa slutpunkter.

## <a name="input-endpoint"></a>Slutpunkt för indata
Ingångsslutpunkten används när du vill exponera en port på utsidan. Du anger protokolltypen och porten för slutpunkten som sedan gäller för både externa och interna portar för slutpunkten. Om du vill kan du ange en annan intern port för slutpunkten med [attributet localPort.](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint)

Ingångsslutpunkten kan använda följande protokoll: **http, https, tcp, udp**.

Om du vill skapa en indataslutpunkt lägger du till det underordnade elementet **InputEndpoint** i **slutpunkterselementet** i en webb- eller arbetarroll.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Slutpunkt för indata för instans
Instansindataslutpunkter liknar ingångsslutpunkter, men du kan mappa specifika offentliga portar för varje enskild rollinstans med hjälp av portbefordring på belastningsutjämnaren. Du kan ange en enskild port som ska ställas mot allmänheten eller en rad portar.

Instansindataslutpunkten kan bara använda **tcp** eller **udp** som protokoll.

Om du vill skapa en instansinmatningsslutpunkt lägger du till det underordnade elementet **InstanceInputEndpoint** i **slutpunkterselementet** i en webb- eller arbetarroll.

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
Interna slutpunkter är tillgängliga för instans-till-instans-kommunikation. Porten är valfri och om den utelämnas tilldelas en dynamisk port till slutpunkten. Ett portområde kan användas. Det finns en gräns på fem interna slutpunkter per roll.

Den interna slutpunkten kan använda följande protokoll: **http, tcp, udp, any**.

Om du vill skapa en intern indataslutpunkt lägger du till det underordnade elementet **InternalEndpoint** i **slutpunkterselementet** i en webb- eller arbetarroll.

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


## <a name="worker-roles-vs-web-roles"></a>Arbetarroller jämfört med webbroller
Det finns en mindre skillnad med slutpunkter när du arbetar med både arbets- och webbroller. Webbrollen måste ha minst en enda ingångsslutpunkt med **HTTP-protokollet.**

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Använda .NET SDK för att komma åt en slutpunkt
Azure Managed Library innehåller metoder för rollinstanser att kommunicera vid körning. Från kod som körs i en rollinstans kan du hämta information om förekomsten av andra rollinstanser och deras slutpunkter samt information om den aktuella rollinstansen.

> [!NOTE]
> Du kan bara hämta information om rollinstanser som körs i molntjänsten och som definierar minst en intern slutpunkt. Du kan inte hämta data om rollinstanser som körs i en annan tjänst.
> 
> 

Du kan använda egenskapen [Instanser](/previous-versions/azure/reference/ee741904(v=azure.100)) för att hämta instanser av en roll. Använd först [CurrentRoleInstance](/previous-versions/azure/reference/ee741907(v=azure.100)) för att returnera en referens till den aktuella rollinstansen och sedan använda egenskapen [Roll](/previous-versions/azure/reference/ee741918(v=azure.100)) för att returnera en referens till själva rollen.

När du ansluter till en rollinstans programmässigt via .NET SDK är det relativt enkelt att komma åt slutpunktsinformationen. När du till exempel redan har anslutit till en viss rollmiljö kan du hämta porten för en viss slutpunkt med den här koden:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

Egenskapen **Instances returnerar** en samling **RoleInstance-objekt.** Den här samlingen innehåller alltid den aktuella instansen. Om rollen inte definierar en intern slutpunkt innehåller samlingen den aktuella instansen men inga andra instanser. Antalet rollinstanser i samlingen kommer alltid att vara 1 i det fall där ingen intern slutpunkt har definierats för rollen. Om rollen definierar en intern slutpunkt kan dess instanser identifieras vid körning och antalet instanser i samlingen motsvarar antalet instanser som angetts för rollen i tjänstkonfigurationsfilen.

> [!NOTE]
> Azure Managed Library tillhandahåller inte ett sätt att bestämma hälsotillståndet för andra rollinstanser, men du kan själv implementera sådana hälsobedömningar om din tjänst behöver den här funktionen. Du kan använda [Azure Diagnostics](cloud-services-dotnet-diagnostics.md) för att hämta information om att köra rollinstanser.
> 
> 

Om du vill bestämma portnumret för en intern slutpunkt på en rollinstans kan du använda egenskapen [InstanceEndpoints](/previous-versions/azure/reference/ee741917(v=azure.100)) för att returnera ett dictionary-objekt som innehåller slutpunktsnamn och motsvarande IP-adresser och portar. [Egenskapen IPEndpoint](/previous-versions/azure/reference/ee741919(v=azure.100)) returnerar IP-adressen och porten för en angiven slutpunkt. Egenskapen **PublicIPEndpoint** returnerar porten för en belastningsbalanserad slutpunkt. IP-adressdelen av **egenskapen PublicIPEndpoint** används inte.

Här är ett exempel som itererar rollinstanser.

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

Här är ett exempel på en arbetarroll som får slutpunkten exponerad via tjänstdefinitionen och börjar lyssna efter anslutningar.

> [!WARNING]
> Den här koden fungerar bara för en distribuerad tjänst. När du kör i Azure Compute Emulator ignoreras tjänstkonfigurationselement som skapar direkta portslutpunkter (**InstanceInputEndpoint-element).**
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
      // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Regler för nätverkstrafik för att styra rollkommunikation
När du har definierat interna slutpunkter kan du lägga till nätverkstrafikregler (baserat på de slutpunkter som du har skapat) för att styra hur rollinstanser kan kommunicera med varandra. Följande diagram visar några vanliga scenarier för att kontrollera rollkommunikation:

![Scenarier för nätverkstrafikregler](./media/cloud-services-enable-communication-role-instances/scenarios.png "Scenarier för nätverkstrafikregler")

I följande kodexempel visas rolldefinitioner för de roller som visas i föregående diagram. Varje rolldefinition innehåller minst en definierad intern slutpunkt:

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
> Begränsning av kommunikation mellan roller kan uppstå med interna slutpunkter för både fasta och automatiskt tilldelade portar.
> 
> 

Som standard, när en intern slutpunkt har definierats, kan kommunikation flöda från valfri roll till den interna slutpunkten för en roll utan några begränsningar. Om du vill begränsa kommunikationen måste du lägga till ett **NetworkTrafficRules-element** i **Elementet ServiceDefinition** i tjänstdefinitionsfilen.

### <a name="scenario-1"></a>Scenario 1
Tillåt endast nätverkstrafik från **WebRole1** till **WorkerRole1**.

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
Tillåter endast nätverkstrafik från **WebRole1** till **WorkerRole1** och **WorkerRole2**.

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
Tillåter endast nätverkstrafik från **WebRole1** till **WorkerRole1**och **WorkerRole1** till **WorkerRole2**.

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
Tillåter endast nätverkstrafik från **WebRole1** till **WorkerRole1**, **WebRole1** till **WorkerRole2**och **WorkerRole1** till **WorkerRole2**.

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

En XML-schemareferens för de element som används ovan finns [här](/previous-versions/azure/reference/gg557551(v=azure.100)).

## <a name="next-steps"></a>Nästa steg
Läs mer om Cloud [Service-modellen](cloud-services-model-and-package.md).




