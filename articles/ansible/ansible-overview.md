---
title: Med Azure Ansible | Microsoft Docs
description: En introduktion till att använda Ansible för att automatisera etablering i molnet, konfigurationshantering och programdistribution.
keywords: ansible, azure, devops, overview, cloud provision, configuration management, application deployment, ansible modules, ansible playbooks
ms.topic: overview
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 54d66a0ac425a9a0a63c91317ead0e02ecf9452c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726014"
---
# <a name="using-ansible-with-azure"></a>Använda Ansible med Azure

[Ansible](https://www.ansible.com) är en produkt som bygger på öppen källkod och som automatiserar molntilldelning, konfigurationshantering och programdistribution. Med Ansible kan du tilldela virtuella datorer, containrar, nätverk och kompletta molninfrastrukturer. Dessutom kan Ansible du automatisera distributionen och konfigurationen av resurser i din miljö.

Den här artikeln ger en översikt över några av fördelarna med att använda Ansible med Azure.

## <a name="ansible-playbooks"></a>Spelböcker i Ansible

[Ansible-spelböcker](https://docs.ansible.com/ansible/latest/playbooks.html) kan du dirigera Ansible att konfigurera din miljö. Spelböcker kodas med YAML så att den är läsbart för människor. Avsnittet självstudier innehåller många exempel på hur du använder strategiböcker för att installera och konfigurera Azure-resurser. 

## <a name="ansible-modules"></a>Moduler i Ansible

Ansible innehåller en uppsättning [Ansible moduler](https://docs.ansible.com/ansible/latest/modules_by_category.html) som körs direkt på fjärrvärdar eller via [spelböcker](https://docs.ansible.com/ansible/latest/playbooks.html). Användare kan skapa egna moduler. Moduler som används för att styra systemresurser - som-tjänster, paket eller filer – eller köra systemkommandon.

För att interagera med Azure-tjänster, Ansible innehåller en uppsättning [Ansible molnet moduler](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Dessa moduler kan du skapa och dirigera infrastrukturen i Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrera en befintlig arbetsbelastning till Azure

När du använder Ansible för att definiera infrastrukturen kan använda du programmets spelbok så att Azure automatiskt skala din miljö efter behov. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatisera molnbaserade program i Azure

Med Ansible kan du automatisera molnbaserade program i Azure med Azure-mikrotjänster som [Azure Functions](https://azure.microsoft.com//services/functions/) och [Kubernetes på Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Hantera distributioner med dynamiskt lager

Via dess [dynamiska lagerfunktion](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) tillhandahåller Ansible möjligheten att hämta tillgångar från Azure-resurser. Sedan kan du tagga befintliga Azure-distributioner och hantera dem via Ansible.

## <a name="additional-azure-marketplace-options"></a>Fler Azure Marketplace-alternativ

Den [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) är en Azure Marketplace-avbildning från Red Hat. 

Ansible Tower är en webb-UI och instrumentpanelen för Ansible som har följande funktioner:

* Kan du definiera rollbaserad åtkomstkontroll, schemaläggning av jobb och grafiska lagerhantering. 
* Innehåller en REST-API och CLI så att du kan infoga Tower i befintliga verktyg och processer. 
* Har stöd i realtid utdata från spelbokskörningar. 
* Krypterar autentiseringsuppgifter – till exempel Azure och SSH-nycklar – så att du kan delegera uppgifter utan att exponera autentiseringsuppgifterna.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Ansible-moduler och versioner för Azure

Ansible innehåller en uppsättning moduler för användning i etablering och konfigurera Azure-resurser. Dessa resurser inkluderar virtuella datorer, skalningsuppsättningar, networking services och container services. Den [Ansible matris](./ansible-matrix.md) visar Ansible-moduler för Azure och Ansible-versioner som de skickar.

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Distribuera Ansible-lösningsmall för Azure för att CentOS](./ansible-deploy-solution-template.md)
- [Snabbstart: Konfigurera Linux-datorer i Azure med Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fazure%2Fansible%2Ftoc.json&bc=%2Fazure%2Fbread%2Ftoc.json)