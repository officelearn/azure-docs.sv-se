---
title: Distribuera OpenShift i Azure Stack | Microsoft Docs
description: Distribuera OpenShift i Azure Stack.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: haroldw
ms.openlocfilehash: 91b37753ae80596612eda9d3ccd34858691e35ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771557"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Distribuera OpenShift Container Platform eller OKD i Azure Stack

OpenShift kan distribueras i Azure Stack. Det finns några huvudsakliga skillnader mellan Azure och Azure Stack så att distributionen ska skilja sig åt och funktioner också skiljer sig något.

Azure Cloud-providern fungerar för närvarande inte i Azure Stack. Därför du inte längre att använda disk bifoga för beständig lagring i Azure Stack. I stället kan du konfigurera andra alternativ för lagring, till exempel NFS, iSCSI, GlusterFS, osv. Alternativt kan du aktivera CNS och använda GlusterFS för beständig lagring. Om CNS aktiveras distribueras tre ytterligare noder med ytterligare lagringsutrymme för GlusterFS användning.

Du kan använda en eller flera metoder för att distribuera OpenShift Container Platform eller OKD i Azure Stack:

- Du kan manuellt distribuera nödvändiga Azure infrastrukturkomponenter och följ sedan den [OpenShift Container Platform dokumentation](https://docs.openshift.com/container-platform) eller [OKD dokumentation](https://docs.okd.io).
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-container-platform/) som förenklar distributionen av klustret OpenShift Container Platform.
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-origin) som förenklar distributionen av klustret OKD.

Om du använder Resource Manager-mall, väljer du rätt grenen (azurestack-release-3.x). Mallar för Azure fungerar inte eftersom API-versioner är olika mellan Azure och Azure Stack. Referens för RHEL-avbildningen är för närvarande hårdkodad som en variabel i filen azuredeploy.JSON och måste ändras för att matcha din avbildning.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

En Red Hat-prenumeration krävs för alla alternativ. Under distributionen, är Red Hat Enterprise Linux-instans registrerad på Red Hat-prenumeration och bifogas Pool-ID som innehåller rättigheter för OpenShift Container Platform.
Kontrollera att du har en giltig Red Hat prenumeration Manager (RHSM) användarnamn, lösenord och Pool-ID. Du kan också använda en aktiveringsnyckeln och Org ID Pool-ID.  Du kan kontrollera den här informationen genom att logga in till https://access.redhat.com.

## <a name="azure-stack-prerequisites"></a>Krav för Azure Stack

En RHEL-avbildningen (OpenShift Container Platform) eller CentOS-avbildning (OKD) måste läggas till i din Azure Stack-miljö för att distribuera ett kluster för OpenShift. Kontakta Azure Stack-administratören om du vill lägga till dessa bilder. Anvisningar finns här:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Distribuera med hjälp av OpenShift Container Platform eller OKD Resource Manager-mall

För att distribuera med hjälp av Resource Manager-mall kan använda du en parameterfil för att ange indataparametrarna som. För att ytterligare anpassa distributionen, Förgrena GitHub-lagringsplatsen och ändra lämpliga objekt.

Vissa vanliga anpassningsalternativ inkludera, men inte begränsat till:

- Skyddsmiljö VM-storlek (variabel i azuredeploy.json)
- Namngivningskonventioner (variabler i azuredeploy.json)
- OpenShift egenskaper för klustret, du ändrar värdfilen (deployOpenShift.sh)
- Referensen till avbildningen RHEL (variabel i azuredeploy.json)

Anvisningar om hur du distribuerar med hjälp av Azure CLI följer du motsvarande avsnitt i den [OpenShift Container Platform](./openshift-container-platform.md) avsnittet eller [OKD](./openshift-okd.md) avsnittet.

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distribution](./openshift-post-deployment.md)
- [Felsöka OpenShift-distribution i Azure](./openshift-troubleshooting.md)