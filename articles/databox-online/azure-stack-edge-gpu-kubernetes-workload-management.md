---
title: Förstå Kubernetes-arbetsbelastnings hantering på Azure Stack Edge-enhet | Microsoft Docs
description: Beskriver hur Kubernetes-arbetsbelastningar kan hanteras på din Azure Stack Edge-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: 21845b51fdd108221d5e1bce50e953b79084d17d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89085357"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-device"></a>Kubernetes arbets belastnings hantering på din Azure Stack Edge-enhet

På din Azure Stack Edge-enhet skapas ett Kubernetes-kluster när du konfigurerar Compute-rollen. När Kubernetes-klustret har skapats kan program i behållare distribueras i Kubernetes-klustret i poddar. Det finns olika sätt att distribuera arbets belastningar i ditt Kubernetes-kluster. 

I den här artikeln beskrivs de olika metoder som kan användas för att distribuera arbets belastningar på din Azure Stack Edge-enhet.

## <a name="workload-types"></a>Arbets belastnings typer

De två vanligaste typerna av arbets belastningar som du kan distribuera på din Azure Stack Edge-enhet är tillstånds lösa program eller tillstånds känsliga program.

- **Tillstånds lösa program** bevarar inte sitt tillstånd och sparar inga data på beständiga lagrings enheter. Alla användar-och sessionsdata är kvar hos klienten. Några exempel på tillstånds lösa program är webb-frontend-filer som nginx och andra webb program.

    Du kan skapa en Kubernetes-distribution för att distribuera ett tillstånds löst program i klustret. 

- **Tillstånds känsliga program** kräver att deras tillstånd sparas. Tillstånds känsliga program använder beständig lagring, till exempel beständiga volymer, för att spara data som ska användas av servern eller av andra användare. Exempel på tillstånds känsliga program är databaser som MongoDB.

    Du kan skapa en Kubernetes-distribution för att distribuera ett tillstånds känsligt program. 

## <a name="namespaces-types"></a>Typer av namn områden

Kubernetes-resurser, till exempel poddar och distributioner, grupperas logiskt i ett namn område. Dessa grupperingar ger ett sätt att logiskt dela upp ett Kubernetes-kluster och begränsa åtkomsten till att skapa, Visa eller hantera resurser. Användare kan bara interagera med resurser inom de tilldelade namn områdena.

Namn områden är avsedda att användas i miljöer med många användare som sprids över flera team eller projekt. För kluster med några få till användare behöver du inte skapa eller tänka på namn områden alls. Börja använda namnrum när du behöver de funktioner som de tillhandahåller.

Mer information finns i [Kubernetes-namnområden](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).


Din Azure Stack Edge-enhet har följande namn rymder:

- **System namn område** – det här namn området är där kärn resurser finns, till exempel nätverks funktioner som DNS och proxy eller Kubernetes-instrumentpanelen. Du distribuerar vanligt vis inte dina egna program till det här namn området. Använd det här namn området för att felsöka problem med Kubernetes-kluster. 

    Det finns flera system namn rymder på enheten och namnen som motsvarar dessa system namn rymder är reserverade. Här är en lista över reserverade system namn rymder: 
    - Kube-system
    - metallb-system
    - DBE – namnrymd
    - standard
    - Kubernetes-instrument panel
    - standard
    - Kube-nod-Lease
    - Kube – offentlig
    - iotedge
    - Azure-båg

    Se till att inte använda reserverade namn för användar namn rymder som du skapar. 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **Användar namn område** – det här är de namn områden som du kan skapa via **kubectl** för att distribuera program lokalt.
 
- **IoT Edge namnrymd** – du ansluter till det här `iotedge` namn området för att distribuera program via IoT Edge.

- **Azure Arc-namnrymd** – du ansluter till det här `azure-arc` namn området för att distribuera program via Azure Arc.

 
## <a name="deployment-types"></a>Distributions typer

Det finns tre huvudsakliga sätt att distribuera dina arbets belastningar. Med var och en av dessa distributions metoder kan du ansluta till en distinkt namnrymd på enheten och sedan distribuera tillstånds lösa eller tillstånds känsliga program.

![Kubernetes arbets belastnings distribution](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Lokal distribution**: det här är via kommando rads verktyget åtkomst verktyg, till exempel `kubectl` som gör att du kan distribuera K8 `yamls` . Du ansluter till K8-klustret på Azure Stack Edge som du skapar med hjälp av `kubeconfig` filen. Mer information finns i [komma åt ett Kubernetes-kluster via kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **IoT Edge distribution**: det här är via IoT Edge, som ansluter till Azure-IoT Hub. Du ansluter till K8-klustret på din Azure Stack Edge-enhet via `iotedge` namn området. De IoT Edge agenter som distribueras i det här namn området ansvarar för anslutning till Azure. Du tillämpar `IoT Edge deployment.json` konfigurationen med Azure DEVOPS CI/CD. Hantering av namn områden och IoT Edge görs via moln operatören.

- **Azure/Arc-distribution**: Azure Arc är ett hybrid hanterings verktyg som gör att du kan distribuera program i K8-kluster. Du ansluter K8-klustret på din Azure Stack Edge-enhet via `azure-arc namespace` .  Agenter distribueras i det här namn området som ansvarar för anslutning till Azure. Du tillämpar distributions konfigurationen med hjälp av GitOps konfigurations hantering. Med Azure-bågen kan du också använda Azure Monitor för behållare för att visa och övervaka dina kluster. Mer information finns i [Vad är Azure-Arc-aktiverade Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview).

## <a name="choose-the-deployment-type"></a>Välj distributions typ

Tänk på följande information när du distribuerar program:

- En **eller flera typer**: du kan välja ett enda distributions alternativ eller en blandning av olika distributions alternativ.
- **Molnet och lokalt**: beroende på dina program kan du välja lokal distribution via kubectl eller moln distribution via IoT Edge och Azure-bågen. 
    - Lokal distribution passar bättre för utvecklings scenarier. När du väljer en lokal distribution begränsas nätverket där Azure Stack Edge-enheten distribueras.
    - Om du har en moln agent som du kan distribuera bör du distribuera din moln operatör och använda moln hantering.
- **IoT vs Azure Arc**: valet av distribution beror också på syftet med ditt produkt scenario. Om du distribuerar program eller behållare som har djupare integrering med IoT eller IoT eko system, bör du välja IoT Edge sätt att distribuera program. Om du har befintliga Kubernetes-distributioner är Azure Arc det bästa valet.


## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar en app lokalt via kubectl finns i:

- [Distribuera ett tillstånds lösa program på din Azure Stack Edge via kubectl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Om du vill distribuera en app via IoT Edge, se:

- [Distribuera en exempel modul på din Azure Stack Edge via IoT Edge](azure-stack-edge-gpu-deploy-sample-module.md).

Information om hur du distribuerar en app via Azure Arc finns i:

- [Distribuera ett program med Azure-båge](azure-stack-edge-gpu-deploy-sample-module.md).
