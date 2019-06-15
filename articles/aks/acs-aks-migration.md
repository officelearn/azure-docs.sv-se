---
title: Migrera från Azure Container Service (ACS) till Azure Kubernetes Service (AKS)
description: Migrera från Azure Container Service (ACS) till Azure Kubernetes Service (AKS).
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: dcee8da943603fb0978caf9992be76347ca197d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65977719"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migrera från Azure Container Service (ACS) till Azure Kubernetes Service (AKS)

Den här artikeln hjälper dig att planera och köra en lyckad migrering mellan Azure Container Service (ACS) med Kubernetes och Azure Kubernetes Service (AKS). För att hjälpa dig fatta viktiga beslut, den här guiden beskriver skillnaderna mellan ACS och AKS och ger en översikt över migreringsprocessen.

## <a name="differences-between-acs-and-aks"></a>Skillnader mellan ACS och AKS

ACS och AKS olika några huvudområden som påverkar migrering. Före migreringen, bör du granska och planera att åtgärda följande skillnader:

* AKS-noder använder [hanterade diskar](../virtual-machines/windows/managed-disks-overview.md).
    * Ohanterade diskar måste konverteras innan du kan koppla dem till AKS-noder.
    * Anpassad `StorageClass` objekt för Azure-diskar som måste ändras från `unmanaged` till `managed`.
    * Alla `PersistentVolumes` bör använda `kind: Managed`.
* Har stöd för AKS [flera nodpooler](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) (för närvarande i förhandsversion).
* Noder som baseras på Windows Server är för närvarande i [förhandsversion i AKS](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* AKS har stöd för en begränsad uppsättning [regioner](https://docs.microsoft.com/azure/aks/quotas-skus-regions).
* AKS är en hanterad tjänst med en värdbaserade kontrollplanet i Kubernetes. Du kan behöva ändra dina program om du tidigare har ändrat konfigurationen av ACS-huvudservrar.

## <a name="differences-between-kubernetes-versions"></a>Skillnader mellan Kubernetes-versioner

Om du migrerar till en nyare version av Kubernetes (till exempel från 1.7.x till 1.9.x), kan du läsa följande resurser för att förstå några ändringar i Kubernetes-API:

* [Migrera en ThirdPartyResource till CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [Arbetsbelastningar API-ändringar i version 1.8 och 1.9](https://kubernetes.io/docs/reference/workloads-18-19/)

## <a name="migration-considerations"></a>Överväganden vid migrering

### <a name="agent-pools"></a>Agentpooler

Även om AKS hanterar kontrollplanet Kubernetes kan definierar du fortfarande storlek och antalet noder som ska ingå i det nya klustret. Anta att du vill att en 1:1-mappning från ACS till AKS och vill du samla in information för dina befintliga ACS-nod. Använd dessa data när du skapar nya AKS-klustret.

Exempel:

| Namn | Count | Storlek på virtuell dator | Operativsystem |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Eftersom fler virtuella datorer ska distribueras till din prenumeration vid migrering, bör du kontrollera att dina kvoter och begränsningar är tillräckliga för de här resurserna. 

Mer information finns i [Azure-prenumeration och tjänstbegränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits). Du kan kontrollera dina aktuella kvoter i Azure portal, välja den [prenumerationsbladet](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), Välj din prenumeration och välj sedan **användning + kvoter**.

### <a name="networking"></a>Nätverk

För komplexa program kommer du vanligtvis migrerar över tid i stället för på samma gång. Det innebär att de gamla och nya miljöerna kan behöva kommunicera över nätverket. Program som tidigare använt `ClusterIP` tjänster kan kommunicera kan behöva exponeras som typ `LoadBalancer` och skyddas på lämpligt sätt.

För att slutföra migreringen bör du pekar på de nya tjänsterna som körs på AKS. Vi rekommenderar att du omdirigera trafik genom att uppdatera DNS så att den pekar till belastningsutjämnare som är placerad framför AKS-klustret.

### <a name="stateless-applications"></a>Tillståndslösa program

Programmigreringen av tillståndslösa är det enklaste fallet. Du gäller YAML-definitioner för det nya klustret, se till att allt fungerar som förväntat och omdirigera trafik för att aktivera det nya klustret.

### <a name="stateful-applications"></a>Tillståndskänsliga program

Noggrant planera migreringen av tillståndskänsliga program för att undvika dataförlust eller oplanerade driftstopp.

#### <a name="highly-available-applications"></a>Program med hög tillgänglighet

Du kan distribuera vissa tillståndskänsliga program i en konfiguration med hög tillgänglighet. Dessa program kan kopiera data mellan repliker. Om du använder den här typen av distribution, kanske du kan skapa en ny medlem i det nya AKS-klustret och sedan migrera med minimal påverkan på underordnade anropare. I allmänhet är migreringssteg för det här scenariot:

1. Skapa en ny sekundär replik i AKS.
2. Vänta tills att replikera data.
3. Växla över att se den nya primärt för en sekundär replik.
4. Peka trafik på AKS-klustret.

#### <a name="migrating-persistent-volumes"></a>Migrera beständiga volymer

Om du migrerar befintliga beständiga volymer till AKS, kommer du vanligtvis gör följande:

1. Inaktivera skriver till programmet. (Det här steget är valfritt och kräver driftstopp.)
2. Ta ögonblicksbilder av diskarna.
3. Skapa nya hanterade diskar från ögonblicksbilder.
4. Skapa beständiga volymer i AKS.
5. Uppdatera pod-specifikationer till [använda befintliga volymer](https://docs.microsoft.com/azure/aks/azure-disk-volume) i stället för PersistentVolumeClaims (statisk allokering).
6. Distribuera programmet till AKS.
7. Verifiera.
8. Peka trafik på AKS-klustret.

> [!IMPORTANT]
> Om du väljer att inte inaktivera skrivningar, måste du att replikera data till den nya distributionen. Annars kommer du missar de data som har skrivits när du tog disk-ögonblicksbilder.

Vissa open source-verktyg kan hjälpa dig att skapa hanterade diskar och migrera volymer mellan Kubernetes-kluster:

* [Azure CLI Diskkopieringen tillägget](https://github.com/noelbundick/azure-cli-disk-copy-extension) kopierar och konverterar diskar över resursgrupper och Azure-regioner.
* [Azure Kube CLI-tillägg](https://github.com/yaron2/azure-kube-cli) räknar upp ACS Kubernetes-volymer och migrerar dem till ett AKS-kluster.

#### <a name="azure-files"></a>Azure Files

Till skillnad från diskar, kan Azure Files monteras på flera värdar samtidigt. AKS-klustret hindra Azure och Kubernetes inte dig från att skapa en pod som fortfarande använder ACS-kluster. Se till att klustren inte skriva till samma filer på samma gång för att förhindra dataförlust och oväntat beteende.

Om ditt program kan vara värd för flera repliker som pekar på samma filresurs, följer du stegen i tillståndslösa migreringen och distribuerar YAML-definitioner till det nya klustret. Om inte, en metod för möjliga migreringen omfattar följande steg:

1. Distribuera programmet till AKS med en replikantal 0.
2. Skala programmet på ACS till 0. (Det här steget kräver driftstopp.)
3. Skala programmet på AKS upp till 1.
4. Verifiera.
5. Peka trafik på AKS-klustret.

Om du vill börja med en tom resursen och gör en kopia av källdata, kan du använda den [ `az storage file copy` ](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) kommandon för att migrera dina data.

### <a name="deployment-strategy"></a>Distributionsstrategi

Vi rekommenderar att du använder din befintliga CI/CD-pipeline för att distribuera en fungerande konfiguration till AKS. Klona din befintliga distribution aktiviteter och se till att `kubeconfig` pekar på det nya AKS-klustret.

Om detta inte är möjligt, exportera resursdefinitionerna från ACS och sedan tillämpa dem på AKS. Du kan använda `kubectl` att exportera objekt.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Flera verktyg för öppen källkod kan, beroende på dina distributionsbehov:

* [Velero](https://github.com/heptio/ark) (det här verktyget kräver Kubernetes 1.7.)
* [Azure Kube CLI-tillägg](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Migreringsanvisningar

1. [Skapa ett AKS-kluster](https://docs.microsoft.com/azure/aks/create-cluster) via Azure portal, Azure CLI eller Azure Resource Manager-mall.

   > [!NOTE]
   > Hitta Azure Resource Manager-exempelmallar för AKS i den [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) arkivet på GitHub.

2. Gör nödvändiga ändringar YAML-definitioner. Ersätt till exempel `apps/v1beta1` med `apps/v1` för `Deployments`.

3. [Migrera volymer](#migrating-persistent-volumes) (valfritt) från ACS-kluster i AKS-klustret.

4. Använd dina CI/CD-system för att distribuera program till AKS. Eller använda kubectl för att tillämpa YAML-definitioner.

5. Verifiera. Se till att dina program fungerar som förväntat och att alla migrerade data har kopierats över.

6. Omdirigera trafik. Uppdatera DNS-server för att peka klienter på AKS-distributionen.

7. Slutföra uppgifter efter migreringen. Om du migrerat volymer och valde att inte inaktivera skrivningar, kopiera den till det nya klustret.
