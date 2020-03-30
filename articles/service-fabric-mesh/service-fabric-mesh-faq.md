---
title: Vanliga frågor för Azure Service Fabric Mesh
description: Läs mer om vanliga frågor och svar för Azure Service Fabric Mesh.
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 2a5c2ea63d162eb6fb78ab702e0519f8ac25dcc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252496"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Vanliga service fabric mesh-frågor

Azure Service Fabric Mesh är en fullständigt hanterad tjänst som gör att utvecklare kan distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring eller nätverk. Den här artikeln har svar på vanliga frågor.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Hur anmäler jag ett problem eller ställer en fråga?

Ställ frågor, få svar från Microsoft-tekniker och rapportera problem i [GitHub-repo-repoen för service-fabric-fabric-mesh-förhandsversionen](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Kvot och kostnad

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Vad kostar det att delta i förhandsversionen?

Det finns för närvarande inga avgifter för att distribuera program eller behållare till förhandsversionen av Mesh. Håll utkik efter uppdateringar i maj för aktivering för fakturering. Vi rekommenderar dock att du tar bort de resurser du distribuerar och inte låter dem fungera om du inte aktivt testar dem.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Finns det en kvotgräns för antalet kärnor och RAM?

Ja. Kvoterna för varje prenumeration är:

- Antal ansökningar: 5
- Kärnor per applikation: 12
- Totalt RAM per program: 48 GB
- Nätverks- och ingångsslutpunkter: 5
- Azure-volymer som du kan bifoga: 10
- Antal tjänstrepliker: 3
- Den största behållaren du kan distribuera är begränsad till 4 kärnor och 16 GB RAM.
- Du kan allokera partiella kärnor till dina behållare i steg om 0,5 kärnor, upp till maximalt 6 kärnor.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Hur länge kan jag lämna mitt program distribuerat?

Vi har för närvarande begränsat livslängden för en ansökan till två dagar. Detta för att maximera användningen av de fria kärnor som tilldelats förhandsgranskningen. Därför får du bara köra en viss distribution kontinuerligt i 48 timmar, varefter den stängs av.

Om du ser detta hända kan du verifiera att `az mesh app show` systemet stänger av det genom att köra kommandot i Azure CLI. Kontrollera om den returneras`"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Ett exempel: 

```azurecli
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

```output
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

Om du vill ta `az group delete <nameOfResourceGroup>` bort resursgruppen använder du kommandot.

## <a name="deployments"></a>Distributioner

### <a name="what-container-images-are-supported"></a>Vilka behållaravbildningar stöds?

Om du utvecklar på en Windows Fall Creators Update (version 1709) maskin, kan du bara använda Windows version 1709 docker avbildningar.

Om du utvecklar på en Windows 10 April 2018 uppdatering (version 1803) maskin, Du kan använda antingen Windows version 1709 eller Windows version 1803 docker avbildningar.

Följande systemavbildningar för behållaren kan användas för att distribuera tjänster:
- Windows - windowsservercore och nanoserver
    - Windows Server 1709
    - Windows Server 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Inga kända begränsningar

> [!NOTE]
> Visual Studio-verktyg för Mesh har ännu inte stöd för distribution i Windows Server 2019- och 1809-behållare.

### <a name="what-types-of-applications-can-i-deploy"></a>Vilka typer av program kan jag distribuera? 

Du kan distribuera allt som körs i behållare som passar inom de begränsningar som placeras på en programresurs (se ovan för mer information om kvoter). Om vi upptäcker att du använder Mesh för att köra olagliga arbetsbelastningar eller missbruka systemet (dvs. gruvdrift), förbehåller vi oss rätten att avsluta dina distributioner och blockera din prenumeration från att köras på tjänsten. Kontakta oss om du har några frågor om hur du kör en viss arbetsbelastning. 

## <a name="developer-experience-issues"></a>Problem med utvecklares upplevelse

### <a name="dns-resolution-from-a-container-doesnt-work"></a>DNS-upplösning från en behållare fungerar inte

Utgående DNS-frågor från en behållare till TJÄNSTEN Fabric DNS-tjänsten kan misslyckas under vissa omständigheter. Detta utreds. Så här minskar du:

- Använd Uppdateringen av Windows Fall Creators (version 1709) eller senare som basbehållarevseende.
- Om enbart tjänstnamnet inte fungerar provar du det fullständigt kvalificerade namnet: ServiceName.ApplicationName.
- Lägg till `EXPOSE <port>` var porten är den port som du exponerar tjänsten på i Docker-filen för tjänsten. Ett exempel:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>DNS fungerar inte på samma sätt som för utvecklingskluster för serviceinfrastruktur och i Mesh

Du kan behöva referera till tjänster på ett annat sätt i ditt lokala utvecklingskluster än i Azure Mesh.

Använd i `{serviceName}.{applicationName}`det lokala utvecklingsklustret . Använd `{servicename}`i Azure Service Fabric Mesh . 

Azure Mesh stöder för närvarande inte DNS-lösning mellan program.

Andra kända DNS-problem med att köra ett utvecklingskluster för tjänstinfrastruktur i Windows 10 finns i: [Felsöka Windows-behållare](/azure/service-fabric/service-fabric-how-to-debug-windows-containers) och [kända DNS-problem](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#known-issues).

### <a name="networking"></a>Nätverk

ServiceFabric-nätverket NAT kan försvinna när du använder appen på din lokala dator. Om du vill diagnostisera om detta har hänt kör du följande från en kommandotolk:

`docker network ls`och notera `servicefabric_nat` om listan visas.  Om inte, kör du följande kommando:`docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Detta åtgärdar problemet även om appen redan har distribuerats lokalt och i ett feltillstånd.

### <a name="issues-running-multiple-apps"></a>Problem med att köra flera appar

Du kan stöta på CPU-tillgänglighet och begränsningar som fastställs i alla program. Så här minskar du:
- Skapa ett kluster med fem noder.
- Minska CPU-användningen i tjänster i appen som distribueras. I tjänstens service.yaml-fil kan du `cpu: 1.0` till exempel ändra till`cpu: 0.5`

Flera program kan inte distribueras till ett ennodskluster. Så här minskar du:
- Använd ett fem nodkluster när du distribuerar flera appar till ett lokalt kluster.
- Ta bort appar som du inte testar för tillfället.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>VS Tooling har begränsat stöd för Windows-behållare

Visual Studio-verktyget stöder endast distribution av Windows-behållare med en bas-OS-version av Windows Server 1709 och 1803 idag. 

## <a name="feature-gaps-and-other-known-issues"></a>Funktionsluckor och andra kända problem

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>När du har distribuerat mitt program har nätverksresursen som är associerad med det ingen IP-adress

Det finns ett känt problem där IP-adressen inte blir tillgänglig omedelbart. Kontrollera nätverksresursens status på några minuter för att se den associerade IP-adressen.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Det går inte att komma åt rätt nätverks-/volymresurs

I programmodellen använder du det fullständiga resurs-ID:t för nätverk och volymer för att kunna komma åt den associerade resursen. Här är ett exempel från snabbstartsexemplet:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>När jag skalar ut påverkas alla mina behållare, inklusive att köra dem

Detta är en bugg och en fix håller på att implementeras.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Fabric Mesh finns i [översikten](service-fabric-mesh-overview.md).
