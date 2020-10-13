---
title: Konfidentiella data behandlings noder i Azure Kubernetes service (AKS) offentlig för hands version
description: Data behandlings noder på AKS
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: 2aa30f86b32005b9c85664b5bb2d0772a6e5f443
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940777"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service-public-preview"></a>Konfidentiella data behandlings noder i Azure Kubernetes service (offentlig för hands version)

Med [Azures konfidentiella data behandling](overview.md) kan du skydda känsliga data medan de används. De underliggande infrastrukturerna skyddar dessa data från andra program, administratörer och moln leverantörer. 

## <a name="overview"></a>Översikt

Azure Kubernetes service (AKS) har stöd för att lägga till [DCsv2 konfidentiella data behandlings noder](confidential-computing-enclaves.md) på Intel SGX. Dessa noder kör känsliga arbets belastningar i en maskinvarubaserad betrodd körnings miljö (TEE) genom att tillåta kod på användar nivå för att allokera privata regioner av minnet. Dessa privata minnes regioner kallas enclaves. Enclaves har utformats för att skydda kod och data från processer som körs med högre privilegier. Modellen för SGX-körning tar bort mellanliggande lager i gäst operativ system och hypervisor. På så sätt kan du köra behållar program direkt ovanpå processorn, samtidigt som det särskilda minnes blocket hålls krypterat. 


![Översikt över SGX-nod](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>AKS funktioner för konfidentiella noder

- Maskin vara och behållar isolering för processnivå genom TEE (SGX Trusted Execution Environment) 
- Heterogena för noder i poolen (blandade konfidentiella och icke-konfidentiella noder i pooler)
- EPC-baserad (Encrypted Page cache) Pod schemaläggning
- DCAP för SGX-drivrutin förinstallerad
- FSGS för Intel-korrigering förinstallerad
- Stöder CPU-konsumtion baserad på vågrät Pod autoskalning och automatisk skalning i kluster
- Slut på proc attesterings hjälp via AKS daemonset
- Linux-behållare stöder genom Ubuntu 18,04 gen 2 VM Worker-noder

## <a name="aks-provided-daemon-sets"></a>AKS angivna daemon-uppsättningar

#### <a name="sgx-device-plugin"></a>Plugin-program för SGX-enhet <a id="sgx-plugin"></a>

Plugin-programmet SGX-enhet implementerar plugin-gränssnittet för Kubernetes-enheten för EPC-minne. Det här plugin-programmet gör effektivt EPC-minne till en ytterligare resurs typ i Kubernetes. Användare kan ange gränser för den här resursen precis som andra resurser. Förutom schemaläggnings funktionen hjälper enhets-plugin-programmet till att tilldela enhets driv rutins behörigheter för SGX till konfidentiella arbets belastnings behållare. En exempel implementering av EPC Memory-baserad distribution ( `kubernetes.azure.com/sgx_epc_mem_in_MiB` )-exemplet finns [här](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)

#### <a name="sgx-quote-helper-service"></a>Hjälp tjänst för SGX-offerter <a id="sgx-quote"></a>

Enklaven-program som utför fjärrattestering måste generera en offert. OFFERTen innehåller kryptografiskt bevis på identiteten och programmets tillstånd, och miljön som enklaven körs i. Generering av OFFERter förlitar sig på vissa betrodda program varu komponenter från Intel, som ingår i program varu komponenterna SGX Platform (PSW/DCAP). Den här PSW paketeras som en daemon-uppsättning som körs per nod. Den kan utnyttjas när du begär attesterings offert från enklaven-appar. Med hjälp av den AKS tjänsten kan du bättre upprätthålla kompatibiliteten mellan PSW och andra SW-komponenter på värden. [Läs mer](confidential-nodes-out-of-proc-attestation.md) om användnings-och funktions information.

## <a name="programming--application-models"></a>Program modeller för programmerings &

### <a name="confidential-containers"></a>Konfidentiella behållare

[Konfidentiella behållare](confidential-containers.md) kör befintliga program och den **vanligaste programmerings** miljön för körning (python, Node, Java osv.), tillsammans med deras befintliga biblioteks beroenden, utan någon ändring av käll kod eller omkompilering. Den här modellen är den snabbaste modellen för konfidentialitet som är aktive rad via projekt med öppen källkod & Azure-partner. Behållar avbildningarna som görs färdiga att köras i säkra enclaves kallas för konfidentiella behållare.

### <a name="enclave-aware-containers"></a>Enklaven-medvetna behållare

AKS stöder program som är programmerade att köras på konfidentiella noder och använder **särskild instruktions uppsättning som** görs tillgänglig via SDK: er och ramverk. Den här program modellen ger mest kontroll över dina program med en lägsta TCB (Trusted Computing Base). [Läs mer](enclave-aware-containers.md) om enklaven-medvetna behållare.

## <a name="next-steps"></a>Nästa steg

[Distribuera AKS-kluster med konfidentiella databeräknings noder](./confidential-nodes-aks-get-started.md)

[Snabb starts exempel för konfidentiella behållare](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU-lista](https://docs.microsoft.com/azure/virtual-machines/dcv2-series)

<!-- LINKS - external -->
[Azure Attestation]: https://docs.microsoft.com/en-us/azure/attestation/


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions