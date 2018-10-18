---
title: Migrera från Azure Container Service (ACS) till Azure Kubernetes Service (AKS)
description: Migrera från Azure Container Service (ACS) till Azure Kubernetes Service (AKS)
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: e42b0e7bd1bce40b7c58d75cb07f5a3f8afa5836
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385049"
---
# <a name="migrating-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migrera från Azure Container Service (ACS) till Azure Kubernetes Service (AKS)

Målet med det här dokumentet är att hjälpa dig att planera och köra en lyckad migrering mellan Azure Container Service med Kubernetes (ACS) och Azure Kubernetes Service (AKS). Den här guiden beskriver skillnaderna mellan ACS och AKS, ger en översikt över migreringsprocessen och hjälper dig att fatta viktiga beslut.

## <a name="differences-between-acs-and-aks"></a>Skillnader mellan ACS och AKS

ACS och AKS olika några huvudområden som påverkar migrering. Du bör granska och planera att åtgärda följande skillnader innan migreringen.

* AKS-noder använder [Managed Disks](../virtual-machines/windows/managed-disks-overview.md)
    * Ohanterade diskar behöver du konverteras innan de kan kopplas till AKS-noder
    * Anpassad `StorageClass` objekt för Azure-diskar behöver du ändras från `unmanaged` till `managed`
    * Alla `PersistentVolumes` måste du använda `kind: Managed`
* AKS stöder för närvarande endast en agentpool
* Windows Server-baserade noder är för närvarande i [privat förhandsgranskning](https://azure.microsoft.com/blog/kubernetes-on-azure/)
* Kontrollera listan över AKS [regioner som stöds](https://docs.microsoft.com/azure/aks/container-service-quotas)
* AKS är en hanterad tjänst med en värdbaserade kontrollplanet i Kubernetes. Du kan behöva ändra dina program om du tidigare har ändrat konfigurationen av ACS-huvudservrar

### <a name="differences-between-kubernetes-versions"></a>Skillnader mellan Kubernetes-versioner

Om du migrerar till en nyare version av Kubernetes (ex: 1.7.x till 1.9.x), det finns några ändringar i API: et i k8s som kräver din uppmärksamhet.

* [Migrera en ThirdPartyResource till CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [Arbetsbelastningar API-ändringar i version 1.8 och 1.9](https://kubernetes.io/docs/reference/workloads-18-19/).

## <a name="migration-considerations"></a>Överväganden vid migrering

### <a name="agent-pools"></a>Agentpooler

Även om AKS hanterar kontrollplanet Kubernetes kan definierar du fortfarande storlek och antalet noder som du vill ska ingå i det nya klustret. Anta att du vill att en 1:1-mappning från ACS till AKS och vill du samla in information för dina befintliga ACS-nod. När du skapar nya AKS-klustret ska du använda dessa data.

Exempel:

| Namn | Antal | Storlek på virtuell dator | Operativsystem |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Eftersom fler virtuella datorer ska distribueras till din prenumeration vid migrering, bör du kontrollera att dina kvoter och begränsningar är tillräckliga för de här resurserna. Du kan lära dig mer genom att granska [Azure-prenumeration och tjänstbegränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits). Du kan kontrollera dina aktuella kvoter, välja den [prenumerationsbladet](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure portal, Välj din prenumeration och välj sedan `Usage + quotas`.

### <a name="networking"></a>Nätverk

För komplexa program kommer du vanligtvis migrerar över tid i stället för på samma gång. Det innebär att de gamla och nya miljöerna kan behöva kommunicera över nätverket. Program som tidigare kunde du använda `ClusterIP` tjänster kan kommunicera kan behöva exponeras som typ `LoadBalancer` och skyddas på lämpligt sätt.

För att slutföra migreringen bör du pekar på de nya tjänsterna som körs på AKS. Det är det rekommenderade sättet att omdirigera trafik genom att uppdatera DNS så att den pekar till belastningsutjämnare som är placerad framför AKS-klustret.

### <a name="stateless-applications"></a>Tillståndslösa program

Programmigreringen av tillståndslösa är det enklaste fallet. Du gäller YAML-definitioner för det nya klustret, kontrollera att allt fungerar som förväntat och omdirigera trafik för att aktivera det nya klustret.

### <a name="stateful-applications"></a>Tillståndskänsliga program

Migrera tillståndskänsliga program kräver noggrann planering att undvika dataförlust eller oplanerade driftstopp.

#### <a name="highly-available-applications"></a>Program med hög tillgänglighet

Vissa tillståndskänsliga program kan distribueras i en konfiguration med hög tillgänglighet och kan kopiera data mellan repliker. Om det här beskriver din aktuella distribution kan det vara möjligt att skapa en ny medlem i det nya AKS-klustret och migrera med minimal påverkan på underordnade anropare. Det är vanligtvis migreringsstegen för det här scenariot:

1. Skapa en ny sekundär replik på AKS
2. Vänta på att replikera data
3. Växla över att se den nya primärt för sekundär replik
4. Peka trafik på AKS-kluster

#### <a name="migrating-persistent-volumes"></a>Migrera beständiga volymer

Det finns flera faktorer att tänka på om du migrerar befintliga beständiga volymer till AKS. I allmänhet är stegen:

1. (Valfritt) Inaktivera skrivningar till programmet (kräver avbrott)
2. Ögonblicksbild diskar
3. Skapa nya Managed Disks från ögonblicksbilder
4. Skapa beständiga volymer i AKS
5. Uppdatera Pod-specifikationer till [använda befintliga volymer](https://docs.microsoft.com/azure/aks/azure-disk-volume) i stället för PersistentVolumeClaims (statisk allokering)
6. Distribuera program till AKS
7. Verifiera
8. Peka trafik på AKS-kluster

> **Viktiga**: Om du väljer att inte inaktivera skrivningar, måste att replikera data till den nya distributionen som du kommer att sakna data som har skrivits sedan diskens ögonblicksbild

Open source-verktyg finns som kan hjälpa dig skapa Managed Disks och migrera volymer mellan Kubernetes-kluster.

* [noelbundick/azure-cli-disk-extension](https://github.com/noelbundick/azure-cli-disk-copy-extension) – kopiera och konvertera diskar över resursgrupper och Azure-regioner
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli) – räkna upp ACS Kubernetes-volymer och migrera dem till ett AKS-kluster

#### <a name="azure-files"></a>Azure Files

Till skillnad från diskar, kan Azure Files monteras på flera värdar samtidigt. Varken Azure eller Kubernetes undviker du att skapa en Pod i AKS-klustret som fortfarande används av ACS-kluster. För att förhindra dataförlust och oväntade resultat, bör du kontrollera att båda klustren inte skriva till samma filer på samma gång.

Om ditt program kan vara värd för flera repliker som pekar på samma filresurs, kan du följa tillståndslösa migreringsstegen och distribuera YAML-definitioner till det nya klustret.

Om inte, en metod för möjliga migreringen omfattar följande steg:

1. Distribuera programmet till AKS med en replikantal 0
2. Skala ACS programmet till 0 (kräver avbrott)
3. Skala program på AKS upp till 1
4. Verifiera
5. Peka trafik på AKS-kluster

I fall där du vill starta med en tom filresurs och göra en kopia av källdata kan du använda den [ `az storage file copy` ](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) kommandon för att migrera dina data.

### <a name="deployment-strategy"></a>Distributionsstrategi

Den rekommenderade metoden är att använda din befintliga CI/CD-pipeline för att distribuera en fungerande konfiguration till AKS. Du klona befintliga distribuera uppgifter och se till att din `kubeconfig` pekar på det nya AKS-klustret.

I fall där det inte är möjligt, måste du exportera resursdefinitionen från ACS och sedan tillämpa dem på AKS. Du kan använda `kubectl` att exportera objekt.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Det finns också flera verktyg för öppen källkod som kan hjälpa att, beroende på dina behov:

* [heptio/ark](https://github.com/heptio/ark) -kräver k8s 1.7
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli)
* [mhausenblas/reshifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Migreringssteg

### <a name="1-create-an-aks-cluster"></a>1. Skapa ett AKS-kluster

Du kan följa dokumenten till [skapa ett AKS-kluster](https://docs.microsoft.com/azure/aks/create-cluster) via Azure portal, Azure CLI eller Resource Manager-mall.

> Du hittar Azure Resource Manager-exempelmallar för AKS i den [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) arkivet på GitHub

### <a name="2-modify-applications"></a>2. Ändra de program

Genomför nödvändiga ändringar YAML-definitioner. Exempel: ersätta `apps/v1beta1` med `apps/v1` för `Deployments`

### <a name="3-optional-migrate-volumes"></a>3. (Valfritt) Migrera volymer

Migrera volymer från ACS-kluster i AKS-klustret. Mer information finns i den [migrera beständiga volymer](#Migrating-Persistent-Volumes) avsnittet.

### <a name="4-deploy-applications"></a>4. Distribuera program

Använd dina CI/CD-system för att distribuera program till AKS eller använda kubectl för att tillämpa YAML-definitioner.

### <a name="5-validate"></a>5. Verifiera

Kontrollera att dina program fungerar som förväntat och att alla migrerade data har kopierats över.

### <a name="6-redirect-traffic"></a>6. Omdirigera trafik

Uppdatera DNS-server för att peka klienter på AKS-distributionen.

### <a name="7-post-migration-tasks"></a>7. Uppgifter efter migrering

Om du migrerat volymer och valde att inte inaktivera skrivningar, måste du kopiera dessa data till det nya klustret.
