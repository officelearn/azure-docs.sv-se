---
title: Använda Ansible med Azure
description: En introduktion till att använda Ansible för att automatisera etablering i molnet, konfigurationshantering och programdistribution.
keywords: ansible, azure, devops, overview, cloud provision, configuration management, application deployment, ansible modules, ansible playbooks
ms.topic: overview
ms.date: 04/30/2019
ms.openlocfilehash: e9d5b8858f052083b157c7d4809fe21018518bcd
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193573"
---
# <a name="using-ansible-with-azure"></a>Använda Ansible med Azure

[Ansible](https://www.ansible.com) är en produkt som bygger på öppen källkod och som automatiserar molntilldelning, konfigurationshantering och programdistribution. Med Ansible kan du tilldela virtuella datorer, containrar, nätverk och kompletta molninfrastrukturer. Ansible gör det också möjligt att automatisera distributionen och konfigurationen av resurser i din miljö.

Den här artikeln ger en översikt över några av fördelarna med att använda Ansible med Azure.

## <a name="ansible-playbooks"></a>Spelböcker i Ansible

[Ansible spel böcker](https://docs.ansible.com/ansible/latest/playbooks.html) låter dig dirigera Ansible för att konfigurera din miljö. Spel böcker kodas med YAML, så att det blir läsligt. Avsnittet med självstudier innehåller många exempel på hur du kan använda spel böcker för att installera och konfigurera Azure-resurser. 

## <a name="ansible-modules"></a>Moduler i Ansible

Ansible innehåller en uppsättning [Ansible-moduler](https://docs.ansible.com/ansible/latest/modules_by_category.html) som körs direkt på fjärranslutna värdar eller via [spel böcker](https://docs.ansible.com/ansible/latest/playbooks.html). Användare kan skapa sina egna moduler. Moduler används för att kontrol lera system resurser, t. ex. tjänster, paket eller filer, eller köra system kommandon.

För att interagera med Azure-tjänster innehåller Ansible en svit med [Ansible-molnappar](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Med dessa moduler kan du skapa och dirigera din infrastruktur på Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrera en befintlig arbetsbelastning till Azure

När du använder Ansible för att definiera infrastrukturen kan du använda ditt programs Spelbok som låter Azure automatiskt skala din miljö vid behov. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatisera molnbaserade program i Azure

Med Ansible kan du automatisera molnbaserade program i Azure med Azure-mikrotjänster som [Azure Functions](https://azure.microsoft.com//services/functions/) och [Kubernetes på Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Hantera distributioner med dynamiskt lager

Via dess [dynamiska lagerfunktion](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) tillhandahåller Ansible möjligheten att hämta tillgångar från Azure-resurser. Sedan kan du tagga befintliga Azure-distributioner och hantera dem via Ansible.

## <a name="additional-azure-marketplace-options"></a>Fler Azure Marketplace-alternativ

[Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) är en Azure Marketplace-avbildning med Red Hat. 

Ansible Tower är ett webbaserat användar gränssnitt och en instrument panel för Ansible som har följande funktioner:

* Gör att du kan definiera rollbaserad åtkomst kontroll, jobb schemaläggning och grafisk lager hantering. 
* Innehåller ett REST API och CLI så att du kan infoga Tower i befintliga verktyg och processer. 
* Stöder utdata i real tid av Spelbok-körningar. 
* Krypterar autentiseringsuppgifter – till exempel Azure och SSH-nycklar – så att du kan delegera aktiviteter utan att exponera autentiseringsuppgifter.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Ansible-moduler och versioner för Azure

Ansible innehåller en uppsättning moduler som används vid etablering och konfiguration av Azure-resurser. Bland dessa resurser ingår virtuella datorer, skalnings uppsättningar, nätverks tjänster och behållar tjänster. I [Ansible-matrisen](./ansible-matrix.md) visas Ansible-modulerna för Azure och de Ansible-versioner där de levereras.

## <a name="next-steps"></a>Nästa steg

- [Snabb start: Distribuera Ansible lösnings mal len för Azure till CentOS](./ansible-deploy-solution-template.md)
- [Snabb start: Konfigurera virtuella Linux-datorer i Azure med Ansible](./ansible-install-configure.md)