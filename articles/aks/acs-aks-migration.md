---
title: Migrera från Azure Container Service (ACS) till Azure Kubernetes service (AKS)
description: Migrera från Azure Container Service (ACS) till Azure Kubernetes service (AKS).
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: 66f76a8a706f60df786786cbd1ce00b7eafd8d7e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097888"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migrera från Azure Container Service (ACS) till Azure Kubernetes service (AKS)

Den här artikeln hjälper dig att planera och köra en lyckad migrering mellan Azure Container Service (ACS) med Kubernetes och Azure Kubernetes service (AKS). För att hjälpa dig att fatta viktiga beslut beskriver den här guiden skillnaderna mellan ACS och AKS och ger en översikt över migreringsprocessen.

## <a name="differences-between-acs-and-aks"></a>Skillnader mellan ACS och AKS

ACS och AKS skiljer sig åt i vissa viktiga områden som påverkar migreringen. Innan du migrerar bör du granska och planera för att åtgärda följande skillnader:

* AKS-noder använder [hanterade diskar](../virtual-machines/windows/managed-disks-overview.md).
    * Ohanterade diskar måste konverteras innan du kan koppla dem till AKS-noder.
    * Anpassade `StorageClass` objekt för Azure-diskar måste ändras från `unmanaged` till `managed`.
    * Alla `PersistentVolumes` ska använda `kind: Managed`.
* AKS stöder [flera resurspooler](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) (för närvarande i för hands version).
* Noder som baseras på Windows Server är för närvarande för [hands versioner i AKS](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* AKS stöder en begränsad uppsättning [regioner](https://docs.microsoft.com/azure/aks/quotas-skus-regions).
* AKS är en hanterad tjänst med ett kontroll plan för värdbaserade Kubernetes. Du kan behöva ändra dina program om du tidigare har ändrat konfigurationen av ACS-huvud.

## <a name="differences-between-kubernetes-versions"></a>Skillnader mellan Kubernetes-versioner

Om du migrerar till en nyare version av Kubernetes kan du läsa följande resurser för att förstå Kubernetes-versions strategierna:

* [Support princip för Kubernetes version och version skev](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)

## <a name="migration-considerations"></a>Överväganden vid migrering

### <a name="agent-pools"></a>Agentpooler

Även om AKS hanterar Kubernetes-kontroll planet definierar du fortfarande storlek och antal noder som ska ingå i det nya klustret. Förutsatt att du vill ha en 1:1-mappning från ACS till AKS, ska du samla in din befintliga information om ACS-noden. Använd dessa data när du skapar ditt nya AKS-kluster.

Exempel:

| Name | Count | Storlek på virtuell dator | Operativsystem |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Eftersom ytterligare virtuella datorer ska distribueras till din prenumeration under migreringen bör du kontrol lera att kvoterna och gränserna är tillräckliga för dessa resurser. 

Mer information finns i [prenumerations-och tjänst begränsningar i Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits). Du kan kontrol lera dina aktuella kvoter genom att gå till [bladet prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)i Azure Portal, välja din prenumeration och sedan välja **användning + kvoter**.

### <a name="networking"></a>Nätverk

För komplexa program migrerar du normalt över tid i stället för alla samtidigt. Det innebär att de gamla och nya miljöerna kan behöva kommunicera över nätverket. Program som tidigare använde `ClusterIP` tjänster för att kommunicera kan behöva exponeras som typ `LoadBalancer` och skyddas på lämpligt sätt.

För att slutföra migreringen vill du peka klienter till de nya tjänster som körs på AKS. Vi rekommenderar att du omdirigerar trafik genom att uppdatera DNS för att peka på Load Balancer som finns framför ditt AKS-kluster.

### <a name="stateless-applications"></a>Tillstånds lösa program

Tillstånds lös programmigrering är det enklaste fallet. Du tillämpar YAML-definitionerna på det nya klustret, se till att allt fungerar som förväntat och dirigera om trafik för att aktivera det nya klustret.

### <a name="stateful-applications"></a>Tillstånds känsliga program

Planera noggrant migreringen av tillstånds känsliga program för att undvika data förlust eller oväntad stillestånds tid.

#### <a name="highly-available-applications"></a>Program med hög tillgänglighet

Du kan distribuera vissa tillstånds känsliga program i en konfiguration med hög tillgänglighet. Dessa program kan kopiera data mellan repliker. Om du för närvarande använder den här typen av distribution kan du skapa en ny medlem i det nya AKS-klustret och sedan migrera med minimal påverkan på efterföljande anropare. I allmänhet är migrerings stegen för det här scenariot:

1. Skapa en ny sekundär replik på AKS.
2. Vänta tills data har repliker ATS.
3. Redundansväxla för att göra en sekundär replik till den nya primära repliken.
4. Peka trafik till AKS-klustret.

#### <a name="migrating-persistent-volumes"></a>Migrera beständiga volymer

Om du migrerar befintliga beständiga volymer till AKS följer du normalt de här stegen:

1. Skrivningar från sidan till programmet. (Det här steget är valfritt och kräver nedtid.)
2. Ta ögonblicks bilder av diskarna.
3. Skapa nya hanterade diskar från ögonblicks bilderna.
4. Skapa beständiga volymer i AKS.
5. Uppdatera Pod-specifikationerna för att [använda befintliga volymer](https://docs.microsoft.com/azure/aks/azure-disk-volume) i stället för PersistentVolumeClaims (statisk etablering).
6. Distribuera programmet till AKS.
7. Kontrollerar.
8. Peka trafik till AKS-klustret.

> [!IMPORTANT]
> Om du väljer att inte använda offline-skrivning måste du replikera data till den nya distributionen. Annars saknar du de data som skrevs efter att du tog disk ögonblicks bilderna.

Vissa verktyg med öppen källkod kan hjälpa dig att skapa hanterade diskar och migrera volymer mellan Kubernetes-kluster:

* [Tillägget Azure CLI Disk Copy](https://github.com/noelbundick/azure-cli-disk-copy-extension) kopierar och konverterar diskar över resurs grupper och Azure-regioner.
* [Azure Kube CLI-tillägget](https://github.com/yaron2/azure-kube-cli) räknar upp ACS Kubernetes-volymer och migrerar dem till ett AKS-kluster.

#### <a name="azure-files"></a>Azure Files

Till skillnad från diskar kan Azure Files monteras till flera värdar samtidigt. I ditt AKS-kluster förhindrar Azure och Kubernetes dig inte att skapa en pod som används av ACS-klustret fortfarande. För att förhindra data förlust och oväntade beteenden, se till att klustren inte skriver till samma filer samtidigt.

Om ditt program kan vara värd för flera repliker som pekar på samma fil resurs, följer du stegen för stegvis migrering och distribuerar YAML-definitionerna till det nya klustret. Om inte, så innebär en möjlig migrering följande steg:

1. Distribuera ditt program till AKS med ett replik antal 0.
2. Skala programmet på ACS till 0. (Det här steget kräver nedtid.)
3. Skala programmet på AKS upp till 1.
4. Kontrollerar.
5. Peka trafik till AKS-klustret.

Om du vill börja med en tom resurs och göra en kopia av data källan kan du använda [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) kommandona för att migrera dina data.

### <a name="deployment-strategy"></a>Distributions strategi

Vi rekommenderar att du använder din befintliga CI/CD-pipeline för att distribuera en fungerande konfiguration till AKS. Klona dina befintliga distributions uppgifter och `kubeconfig` se till att de pekar på det nya AKS-klustret.

Om detta inte är möjligt exporterar du resurs definitioner från ACS och tillämpar dem sedan på AKS. Du kan använda `kubectl` för att exportera objekt.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Flera verktyg med öppen källkod kan hjälpa, beroende på dina distributions behov:

* [Velero](https://github.com/heptio/ark) (Det här verktyget kräver Kubernetes 1,7.)
* [Azure Kube CLI-tillägg](https://github.com/yaron2/azure-kube-cli)
* [Flytta om](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Migreringsanvisningar

1. [Skapa ett AKS-kluster](https://docs.microsoft.com/azure/aks/create-cluster) via Azure Portal, Azure CLI eller Azure Resource Manager mall.

   > [!NOTE]
   > Hitta exempel Azure Resource Manager mallar för AKS på [Azure/AKS-](https://github.com/Azure/AKS/tree/master/examples/vnet) lagringsplatsen på GitHub.

2. Gör nödvändiga ändringar i YAML-definitionerna. Ersätt `apps/v1beta1` till exempel med `apps/v1` for `Deployments`.

3. [Migrera volymer](#migrating-persistent-volumes) (valfritt) från ACS-klustret till ditt AKS-kluster.

4. Använd ditt CI/CD-system för att distribuera program till AKS. Eller Använd kubectl för att tillämpa definitionerna för YAML.

5. Kontrollerar. Se till att dina program fungerar som förväntat och att alla migrerade data har kopierats.

6. Omdirigera trafik. Uppdatera DNS för att peka klienter till din AKS-distribution.

7. Slutför uppgifter efter migreringen. Om du har migrerat volymer och valt att inte skriva av offline kan du kopiera dessa data till det nya klustret.
