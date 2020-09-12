---
title: Förstå Kubernetes-arbetsbelastnings hantering på Azure Stack Edge-enhet | Microsoft Docs
description: Beskriver hur Kubernetes-arbetsbelastningar kan hanteras på din Azure Stack Edge-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: alkohli
ms.openlocfilehash: 53bd7a404e4635833b03507e8b5ae93ae40b1c61
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318989"
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

## <a name="deployment-flow"></a>Distributions flöde

Om du vill distribuera program på en Azure Stack Edge-enhet följer du dessa steg: 
 
1. **Konfigurera åtkomst**: först ska du använda PowerShell-körnings utrymme för att skapa en användare, skapa ett namn område och bevilja användar åtkomst till namn området.
2. **Konfigurera lagring**: härnäst ska du använda Azure Stack Edge-resursen i Azure Portal för att skapa beständiga volymer med antingen statisk eller dynamisk etablering för de tillstånds känsliga program som du ska distribuera.
3. **Konfigurera nätverk**: Slutligen ska du använda tjänsterna för att exponera program externt och i Kubernetes-klustret.
 
## <a name="deployment-types"></a>Distributions typer

Det finns tre huvudsakliga sätt att distribuera dina arbets belastningar. Med var och en av dessa distributions metoder kan du ansluta till en distinkt namnrymd på enheten och sedan distribuera tillstånds lösa eller tillstånds känsliga program.

![Kubernetes arbets belastnings distribution](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Lokal distribution**: den här distributionen sker via kommando rads verktyget för åtkomst, t. ex. så `kubectl` att du kan distribuera Kubernetes `yamls` . Du kommer åt Kubernetes-klustret på din Azure Stack Edge via en `kubeconfig` fil. Mer information finns i [komma åt ett Kubernetes-kluster via kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **IoT Edge distribution**: det här är via IoT Edge, som ansluter till Azure-IoT Hub. Du ansluter till Kubernetes-klustret på din Azure Stack Edge-enhet via `iotedge` namn området. De IoT Edge agenter som distribueras i det här namn området ansvarar för anslutning till Azure. Du tillämpar `IoT Edge deployment.json` konfigurationen med Azure DEVOPS CI/CD. Hantering av namn områden och IoT Edge görs via moln operatören.

- **Azure/Arc-distribution**: Azure Arc är ett hybrid hanterings verktyg som gör att du kan distribuera program i Kubernetes-kluster. Du ansluter Kubernetes-klustret på din Azure Stack Edge-enhet via `azure-arc namespace` . Agenter distribueras i det här namn området som ansvarar för anslutning till Azure. Du tillämpar distributions konfigurationen med hjälp av GitOps konfigurations hantering. Med Azure-bågen kan du också använda Azure Monitor för behållare för att visa och övervaka dina kluster. Mer information finns i [Vad är Azure-Arc-aktiverade Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview).

## <a name="choose-the-deployment-type"></a>Välj distributions typ

Tänk på följande information när du distribuerar program:

- En **eller flera typer**: du kan välja ett enda distributions alternativ eller en blandning av olika distributions alternativ.
- **Molnet och lokalt**: beroende på dina program kan du välja lokal distribution via kubectl eller moln distribution via IoT Edge och Azure-bågen. 
    - När du väljer en lokal distribution begränsas nätverket där Azure Stack Edge-enheten distribueras.
    - Om du har en moln agent som du kan distribuera bör du distribuera din moln operatör och använda moln hantering.
- **IoT vs Azure Arc**: valet av distribution beror också på syftet med ditt produkt scenario. Om du distribuerar program eller behållare som har djupare integrering med IoT eller IoT eko system väljer du IoT Edge för att distribuera dina program. Om du har befintliga Kubernetes-distributioner är Azure Arc det bästa valet.


## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar en app lokalt via kubectl finns i:

- [Distribuera ett tillstånds lösa program på din Azure Stack Edge via kubectl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Om du vill distribuera en app via IoT Edge, se:

- [Distribuera en exempel modul på din Azure Stack Edge via IoT Edge](azure-stack-edge-gpu-deploy-sample-module.md).

Information om hur du distribuerar en app via Azure Arc finns i:

- [Distribuera ett program med Azure-båge](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).
