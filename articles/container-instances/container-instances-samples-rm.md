---
title: Azure Resource Manager-mallexempel
description: Hitta Azure Resource Manager Template-exempel för att distribuera Azure Container Instances i olika konfigurationer
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: 7208e1b4b67879979507cc451d40a188a84373de
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84299765"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Resource Manager mallar för Azure Container Instances

Följande exempel mallar distribuerar behållar instanser i olika konfigurationer.

Information om distributions alternativ finns i avsnittet [distribution](#deployment) . Om du vill skapa egna mallar kan du använda mallarna för referens information för mallen i Azure Container Instances [Resource Manager][ref] och tillgängliga egenskaper.

## <a name="sample-templates"></a>Exempelmallar

| | |
|-|-|
| **Program** ||
| [WordPress][app-wp] | Skapar en WordPress-webbplats och dess MySQL-databas i en behållar grupp. WordPress-webbplatsens innehåll och MySQL-databasen är bestående av en Azure Files-resurs. Skapar också en Programgateway för att exponera offentlig nätverks åtkomst till WordPress. |
| [MS NAV med SQL Server och IIS][app-nav] | Distribuerar en enda Windows-behållare med en helt aktuell fristående Dynamics NAV/Dynamics 365 Business central-miljö. |
| **Enheter** ||
| [emptyDir][vol-emptydir] | Distribuerar två Linux-behållare som delar en emptyDir-volym. |
| [gitRepo][vol-gitrepo] | Distribuerar en Linux-behållare som klonar en GitHub-lagrings platsen och monterar den som en volym. |
| [icke][vol-secret] | Distribuerar en Linux-behållare med ett PFX-certifikat monterat som en hemlig volym. |
| **Nätverk** ||
| [UDP-exponerad behållare][net-udp] | Distribuerar en Windows-eller Linux-behållare som exponerar en UDP-port. |
| [Linux-behållare med offentlig IP][net-publicip] | Distribuerar en enda Linux-behållare som kan nås via en offentlig IP-adress. |
| [Distribuera en behållar grupp med ett virtuellt nätverk][net-vnet] | Distribuerar ett nytt virtuellt nätverk, undernät, nätverks profil och behållar grupp. |
| **Azure-resurser** ||
| [Skapa Azure Storage konto-och fil resurs][az-files] | Använder Azure CLI i en behållar instans för att skapa ett lagrings konto och en Azure Files-resurs.

## <a name="deployment"></a>Distribution

Du har flera alternativ för att distribuera resurser med Resource Manager-mallar:

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure Portal][deploy-portal]

[REST-API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[net-vnet]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
[deploy-portal]: ../azure-resource-manager/templates/deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-rest]: ../azure-resource-manager/templates/deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
