---
title: Vanliga frågor för Azure Service Fabric nät | Microsoft Docs
description: Läs mer om vanliga frågor och svar för Azure Service Fabric-nät.
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 12/12/2018
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 8d6dcdd4de8fb56935d2f66bfff045b2115d4004
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063581"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Vanliga frågor och svar om Service Fabric-nät

Azure Service Fabric Mesh är en fullständigt hanterad tjänst som gör att utvecklare kan distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring eller nätverk. Den här artikeln innehåller svar på vanliga frågor.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Hur jag för att rapportera ett problem eller Ställ en fråga?

Ställ frågor, få svar från Microsofts tekniker och rapportera problem i den [service-fabric-nät-förhandsversion GitHub-lagringsplatsen](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Kvoter och kostnad

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Vad är kostnaden för att delta i förhandsgranskningen?

Det finns inga avgifter för att distribuera program eller behållare till förhandsversionen av nät. Men vi rekommenderar att du tar bort de resurser som du distribuerar och inte låta dem vara köras om du testar aktivt dem.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Finns det en kvotgräns på antalet kärnor och RAM-minne?

Ja. Kvoter för varje prenumeration är:

- Antal program: 5
- Kärnor per program: 12
- Totalt RAM-minne per program: 48 GB
- Nätverks- och inkommande slutpunkter: 5
- Azure volymer som kan bifogas: 10
- Antal repliker för tjänsten: 3
- Den största behållare som du kan distribuera är begränsad till 4 kärnor och 16GB RAM-minne.
- Du kan allokera partiella kärnor till dina behållare i steg om 0,5 kärnor, upp till högst 6 kärnor.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Hur länge kan jag lämna mitt program som distribuerats?

Vi har för närvarande begränsad livslängd för ett program till två dagar. Det här är för att maximera användningen av de lediga kärnor som allokerats till förhandsversionen. Därför kan du bara ska tillåtas att köras en viss distribution kontinuerligt i 48 timmar efter det kommer att stängas av.

Om du ser det här exemplet kan du verifiera att systemet Stäng av den genom att köra den `az mesh app show` i Azure CLI. Kontrollera om den returnerar `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Exempel: 

```cli
~$ az mesh app show --resource-group myResourceGroup --name helloWorldApp
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

Om du vill ta bort resursgruppen, använda den `az group delete <nameOfResourceGroup>` kommando.

## <a name="supported-container-os-images"></a>Stöds behållaravbildningar OS

Om du utvecklar på en dator med Windows Fall Creators Update (version 1709), kan du bara använda Windows-version 1709 docker-avbildningar.

Om du utvecklar på en Windows 10 April 2018 uppdatera (version 1803)-dator kan du använda Windows-version 1709 eller Windows-version 1803 docker-avbildningar.

Följande behållare OS-avbildningar kan användas för att distribuera tjänster:

- Windows - windowsservercore och nanoserver
    - Windows Server version 1709
    - Windows Server version 1803
- Linux
    - Inga kända begränsningar

## <a name="developer-experience-issues"></a>Problem med Developer-upplevelse

### <a name="dns-resolution-from-a-container-doesnt-work"></a>Fungerar inte DNS-matchningen från en behållare

Utgående DNS-frågor från en behållare till Service Fabric DNS-tjänsten misslyckas under vissa omständigheter. Detta håller på att undersökas. Minimera:

- Använda Windows Fall Creators update (version 1709) eller senare som en grundläggande behållaravbildning.
- Om namnet på tjänsten enbart inte fungerar kan du prova det fullständigt kvalificerade namnet: ServiceName.ApplicationName.
- Lägg till i Docker-filen för din tjänst `EXPOSE <port>` där porten är den du exponera tjänsten på. Exempel:

```DockerFile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>DNS fungerar inte på samma sätt som för Service Fabric-kluster för utveckling och i nät

Du kan behöva referera till tjänster på olika sätt i det lokala utvecklingsklustret än i Azure-nät.

I det lokala utvecklingsklustret använder `{serviceName}.{applicationName}`. I Azure Service Fabric nät, använda `{servicename}`. 

Azure nät stöder för närvarande inte DNS-matchning mellan program.

Andra kända DNS-problem med att köra ett Service Fabric-kluster för utveckling i Windows 10 finns här: [Felsöka Windows-behållare](/azure/service-fabric/service-fabric-how-to-debug-windows-containers) och [kända problem i DNS-](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#known-issues).

### <a name="networking"></a>Nätverk

Service fabric-nätverk NAT kan försvinna när du använder som kör appen på din lokala dator. Om du vill diagnostisera om detta har skett, kör du följande kommando från en kommandotolk:

`docker network ls` och anteckna om `servicefabric_nat` visas.  Om inte, kör följande kommando: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Detta tar upp problemet även om appen redan har distribuerats lokalt och i ett feltillstånd.

### <a name="issues-running-multiple-apps"></a>Problem med att köra flera appar

Du kan stöta på CPU-tillgänglighet och gränser som åtgärdas i alla program. Minimera:
- Skapa ett kluster med fem noder.
- Minska CPU-användning i tjänster i den app som har distribuerats. Ändra exempelvis i din tjänst service.yaml filen `cpu: 1.0` till `cpu: 0.5`

Flera program kan inte distribueras till ett kluster med en nod. Minimera:
- Använd ett kluster med fem noder när du distribuerar flera appar till ett lokalt kluster.
- Ta bort appar som du för närvarande inte testar.

## <a name="feature-gaps-and-other-known-issues"></a>Funktionen luckor och andra kända problem

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>När du har distribuerat mitt program, har den nätverksresurs som är kopplade till den inte en IP-adress

Det finns ett känt problem där IP-adressen inte blir tillgängliga omedelbart. Kontrollera status för nätverksresursen på några minuter att se den associera IP-adressen.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Mitt program inte går att få åtkomst till rätt nätverk/volym-resursen

Använd fullständigt resurs-ID för nätverk och volymer för att kunna komma åt den associerade resursen i din programmodell. Här är ett exempel från Snabbstart-exempel:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>När jag skalar ut som alla mina behållare påverkas, inklusive köra sådana

Detta är ett fel och en korrigering implementeras.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Service Fabric-nät kan läsa den [översikt](service-fabric-mesh-overview.md).
