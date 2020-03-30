---
title: Använda Ansible med Azure
description: En introduktion till att använda Ansible för att automatisera etablering i molnet, konfigurationshantering och programdistribution.
keywords: ansible, azure, devops, overview, cloud provision, configuration management, application deployment, ansible modules, ansible playbooks
ms.topic: overview
ms.date: 04/30/2019
ms.openlocfilehash: e9d5b8858f052083b157c7d4809fe21018518bcd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77193573"
---
# <a name="using-ansible-with-azure"></a>Använda Ansible med Azure

[Ansible](https://www.ansible.com) är en produkt som bygger på öppen källkod och som automatiserar molntilldelning, konfigurationshantering och programdistribution. Med Ansible kan du tilldela virtuella datorer, containrar, nätverk och kompletta molninfrastrukturer. Med Ansible kan du också automatisera distributionen och konfigurationen av resurser i din miljö.

Den här artikeln ger en översikt över några av fördelarna med att använda Ansible med Azure.

## <a name="ansible-playbooks"></a>Spelböcker i Ansible

[Med ansible-spelböcker](https://docs.ansible.com/ansible/latest/playbooks.html) kan du styra Ansible för att konfigurera din miljö. Spelböcker kodas med YAML för att vara läsbara för människor. Avsnittet Självstudier ger många exempel på hur du använder spelböcker för att installera och konfigurera Azure-resurser. 

## <a name="ansible-modules"></a>Moduler i Ansible

Ansible innehåller en uppsättning [Ansible-moduler](https://docs.ansible.com/ansible/latest/modules_by_category.html) som körs direkt på fjärrvärdar eller via [spelböcker.](https://docs.ansible.com/ansible/latest/playbooks.html) Användare kan skapa sina egna moduler. Moduler används för att styra systemresurser - till exempel tjänster, paket eller filer - eller köra systemkommandon.

För att interagera med Azure-tjänster innehåller Ansible en uppsättning [Ansible-molnmoduler](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Med dessa moduler kan du skapa och dirigera din infrastruktur på Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrera en befintlig arbetsbelastning till Azure

När du använder Ansible för att definiera din infrastruktur kan du använda programmets spelbok så att Azure automatiskt kan skala din miljö efter behov. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatisera molnbaserade program i Azure

Med Ansible kan du automatisera molnbaserade program i Azure med Azure-mikrotjänster som [Azure Functions](https://azure.microsoft.com//services/functions/) och [Kubernetes på Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Hantera distributioner med dynamiskt lager

Via dess [dynamiska lagerfunktion](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) tillhandahåller Ansible möjligheten att hämta tillgångar från Azure-resurser. Sedan kan du tagga befintliga Azure-distributioner och hantera dem via Ansible.

## <a name="additional-azure-marketplace-options"></a>Fler Azure Marketplace-alternativ

[Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) är en Azure Marketplace-avbildning av Red Hat. 

Ansible Tower är ett webbaserat användargränssnitt och instrumentpanel för Ansible som har följande funktioner:

* Gör att du kan definiera rollbaserad åtkomstkontroll, finplanering och grafisk lagerhantering. 
* Innehåller ett REST API och CLI så att du kan infoga Tower i befintliga verktyg och processer. 
* Stöder utskrifter i realtid av spelbokskörningar. 
* Krypterar autentiseringsuppgifter – till exempel Azure- och SSH-nycklar – så att du kan delegera uppgifter utan att exponera autentiseringsuppgifter.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Ansible-moduler och versioner för Azure

Ansible innehåller en uppsättning moduler som kan användas vid etablering och konfiguration av Azure-resurser. Dessa resurser omfattar virtuella datorer, skalningsuppsättningar, nätverkstjänster och behållartjänster. Matrisen [Ansible](./ansible-matrix.md) listar Ansible-modulerna för Azure och de Ansible-versioner som de levereras i.

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Distribuera den ätbara lösningsmallen för Azure till CentOS](./ansible-deploy-solution-template.md)
- [Snabbstart: Konfigurera virtuella Linux-datorer i Azure med Ansible](./ansible-install-configure.md)