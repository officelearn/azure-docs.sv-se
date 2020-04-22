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
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758233"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Distribuera OpenShift Container Platform eller OKD i Azure Stack

OpenShift kan distribueras i Azure Stack. Det finns några viktiga skillnader mellan Azure och Azure Stack så att distributionen skiljer sig något och funktionerna skiljer sig också något.

Azure Cloud Provider fungerar för närvarande inte i Azure Stack. Därför kan du inte använda diskankrona för beständig lagring i Azure Stack. I stället kan du konfigurera andra lagringsalternativ som NFS, iSCSI, GlusterFS, etc. Alternativt kan du aktivera CNS och använda GlusterFS för beständig lagring. Om CNS är aktiverat distribueras ytterligare tre noder med ytterligare lagringsutrymme för GlusterFS-användning.

Du kan använda någon av flera metoder för att distribuera OpenShift Container Platform eller OKD i Azure Stack:

- Du kan distribuera nödvändiga Azure-infrastrukturkomponenter manuellt och sedan följa [dokumentationen till OpenShift Container Platform](https://docs.openshift.com/container-platform) eller [OKD-dokumentationen](https://docs.okd.io).
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-container-platform/) som förenklar distributionen av OpenShift Container Platform-klustret.
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-origin) som förenklar distributionen av OKD-klustret.

Om du använder resource manager-mallen väljer du rätt gren (azurestack-release-3.x). Mallarna för Azure fungerar inte eftersom API-versionerna skiljer sig mellan Azure och Azure Stack. RHEL-avbildningsreferensen är för närvarande hårdkodad som en variabel i filen azuredeploy.json och måste ändras för att matcha avbildningen.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

För alla alternativ krävs en Red Hat-prenumeration. Under distributionen registreras Red Hat Enterprise Linux-instansen i Red Hat-prenumerationen och är kopplad till pool-ID:t som innehåller berättigandena för OpenShift Container Platform.
Kontrollera att du har ett giltigt användarnamn, lösenord och pool-ID (Red Hat Subscription Manager). Du kan också använda en aktiveringsnyckel, organisations-ID och pool-ID.  Du kan verifiera den här https://access.redhat.cominformationen genom att logga in på .

## <a name="azure-stack-prerequisites"></a>Förutsättningar för Azure Stack

En RHEL-avbildning (OpenShift Container Platform) eller CentOS-avbildning (OKD) måste läggas till i din Azure Stack-miljö för att distribuera ett OpenShift-kluster. Kontakta azure stack-administratören för att lägga till dessa avbildningar. Instruktioner hittar du här:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Distribuera med hjälp av OpenShift Container Platform eller OKD Resource Manager-mallen

Om du vill distribuera med hjälp av Resource Manager-mallen använder du en parameterfil för att ange indataparametrarna. Om du vill anpassa distributionen ytterligare förfalskar du GitHub-repoen och ändrar lämpliga objekt.

Några vanliga anpassningsalternativ är, men är inte begränsade till:

- Bastion VM-storlek (variabel i azuredeploy.json)
- Namngivningskonventioner (variabler i azuredeploy.json)
- OpenShift kluster detaljer, ändras via hosts-fil (deployOpenShift.sh)
- RHEL-avbildningsreferens (variabel i azuredeploy.json)

Om du vill distribuera med Azure CLI följer du lämpligt avsnitt i avsnittet [OpenShift Container Platform](./openshift-container-platform-3x.md) eller [avsnittet OKD.](./openshift-okd.md)

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distribution](./openshift-container-platform-3x-post-deployment.md)
- [Felsöka OpenShift-distribution i Azure](./openshift-container-platform-3x-troubleshooting.md)