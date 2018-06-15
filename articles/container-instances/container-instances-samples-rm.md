---
title: Azure Resource Manager mallen exempel - instanser för Azure-behållare
description: Azure Resource Manager-mall exempel för Azure-Behållarinstanser
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/17/2018
ms.author: marsma
ms.openlocfilehash: fcc2e6c52e773d95bcdfe43d881fce036fae6513
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260170"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Resource Manager-mallar för Azure-Behållarinstanser

Följande exempelmallarna distribuera behållarinstanser i olika konfigurationer.

Distributionsalternativ finns i [distribution](#deployment) avsnitt. Om du vill skapa egna mallar, Azure Behållarinstanser [Resource Manager mallreferensen] [ ref] information mallformat och tillgängliga egenskaper.

## <a name="sample-templates"></a>Exempelmallarna

| | |
|-|-|
| **Program** ||
| [WordPress][app-wp] | Skapar en WordPress-webbplats och dess MySQL-databas i en behållare-instans. WordPress webbplatsens innehåll och MySQL-databas sparas till en Azure-filer dela. |
| [MS NAV med SQLServer och IIS][app-nav] | Distribuerar en enskild Windows-behållare med en komplett funktionalitet självständiga Dynamics NAV / Dynamics 365 Business Central miljö. |
| **Volymer** ||
| [emptyDir][vol-emptydir] | Distribuerar två Linux-behållare som delar en emptyDir volym. |
| [GitRepo][vol-gitrepo] | Distribuerar en Linux-behållare som klonar en GitHub-lagringsplatsen och monterar som en volym. |
| [hemligt][vol-secret] | Distribuerar en Linux-behållare med ett PFX-certifikat som monterats som en hemlig volym. |
| **Nätverk** ||
| [UDP-exponerade behållare][net-udp] | Distribuerar en Windows- eller Linux-behållare som Exponerar en UDP-port. |
| [Linux-behållare med offentliga IP-adress][net-publicip] | Distribuerar en enskild Linux behållare som är tillgänglig via en offentlig IP-adress. |
| **Azure-resurser** ||
| [Skapa Azure Storage-konto och dela filer][az-files] | Använder Azure CLI i en instans av behållare för att skapa ett lagringskonto och en resurs i Azure-filer.

## <a name="deployment"></a>Distribution

Du har flera alternativ för att distribuera resurser med Resource Manager-mallar:

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure-portalen][deploy-portal]

[REST-API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups