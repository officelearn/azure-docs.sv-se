---
title: Distribuera OpenShift i Azure Stack
description: Distribuera OpenShift i Azure Stack.
author: haroldwongms
manager: joraio
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 51abfd1cbb438d0987554040867625f7fb71630b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758233"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Distribuera OpenShift container Platform eller OKD i Azure Stack

OpenShift kan distribueras i Azure Stack. Det finns vissa viktiga skillnader mellan Azure och Azure Stack så att distributionen skiljer sig något och några av funktionerna kan skilja sig något.

Azure Cloud-providern fungerar för närvarande inte i Azure Stack. Därför kan du inte använda disk koppling för beständigt lagrings utrymme i Azure Stack. I stället kan du konfigurera andra lagrings alternativ som NFS, iSCSI, GlusterFS osv. Alternativt kan du aktivera CNS och använda GlusterFS för beständig lagring. Om CNS är aktive rad kommer tre ytterligare noder att distribueras med ytterligare lagrings utrymme för GlusterFS-användning.

Du kan använda en av flera metoder för att distribuera OpenShift container Platform eller OKD i Azure Stack:

- Du kan distribuera de nödvändiga komponenterna i Azure-infrastrukturen manuellt och sedan följa [dokumentationen för OpenShift container Platform](https://docs.openshift.com/container-platform) eller [OKD-dokumentationen](https://docs.okd.io).
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-container-platform/) som fören klar distributionen av OpenShift container Platform-klustret.
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-origin) som fören klar distributionen av OKD-klustret.

Om du använder Resource Manager-mallen väljer du rätt gren (azurestack-release-3. x). Mallarna för Azure fungerar inte eftersom API-versionerna skiljer sig mellan Azure och Azure Stack. Avbildnings referensen RHEL är för närvarande hårdkodad som en variabel i filen azuredeploy. JSON och måste ändras för att matcha avbildningen.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

För alla alternativ krävs en Red Hat-prenumeration. Under distributionen registreras Red Hat Enterprise Linux-instansen till Red Hat-prenumerationen och kopplas till det pool-ID som innehåller rättigheterna för OpenShift container Platform.
Se till att du har ett giltigt användar namn, lösen ord och pool-ID för Red Hat Subscription Manager (RHSM). Alternativt kan du använda en aktiverings nyckel, ett org-ID och ett pool-ID.  Du kan kontrol lera den här informationen genom att https://access.redhat.comlogga in på.

## <a name="azure-stack-prerequisites"></a>Azure Stack förutsättningar

En RHEL-avbildning (OpenShift container Platform) eller CentOS Image (OKD) måste läggas till i din Azure Stack-miljö för att distribuera ett OpenShift-kluster. Kontakta Azure Stack-administratören om du vill lägga till avbildningarna. Du hittar anvisningar här:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Distribuera med hjälp av OpenShift container Platform eller OKD Resource Manager-mallen

Om du vill distribuera med hjälp av Resource Manager-mallen använder du en parameter fil för att ange indataparametrarna. Om du vill anpassa distributionen ytterligare delar du GitHub-lagrings platsen och ändrar lämpliga objekt.

Några vanliga anpassnings alternativ är, men är inte begränsade till:

- Skydds VM-storlek (variabel i azuredeploy. JSON)
- Namngivnings konventioner (variabler i azuredeploy. JSON)
- OpenShift-kluster, information, modifierad via hosts-filen (deployOpenShift.sh)
- Referens för RHEL-avbildning (variabel i azuredeploy. JSON)

Om du vill distribuera med hjälp av Azure CLI följer du lämpligt avsnitt i avsnittet [OpenShift container Platform](./openshift-container-platform-3x.md) eller avsnittet [OKD](./openshift-okd.md) .

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distribution](./openshift-container-platform-3x-post-deployment.md)
- [Felsöka OpenShift-distribution i Azure](./openshift-container-platform-3x-troubleshooting.md)