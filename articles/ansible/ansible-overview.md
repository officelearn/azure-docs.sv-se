---
title: Med Ansible med Azure
description: Introduktion till att använda Ansible till automatiserar molntilldelning, konfigurationshantering och programdistributioner.
ms.service: ansible
keywords: ansible, azure, devops, översikt, etablera molnet, konfigurationshantering, programdistribution, ansible-moduler, ansible-spelböcker
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 01/19/2018
ms.topic: article
ms.openlocfilehash: e710770131c844598762feebe09ba50dc120de0c
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43106908"
---
# <a name="ansible-with-azure"></a>Ansible med Azure

[Ansible](http://www.ansible.com) är en produkt med öppen källkod som automatiserar molntilldelning, konfigurationshantering och programdistributioner. Med Ansible kan du etablera virtuella datorer, behållare och nätverk och slutföra molninfrastrukturer. Dessutom kan Ansible du automatisera distributionen och konfigurationen av resurser i din miljö.

Den här artikeln ger en översikt över några av fördelarna med att använda Ansible med Azure.

## <a name="ansible-playbooks"></a>Ansible-spelböcker

[Ansible-spelböcker](http://docs.ansible.com/ansible/latest/playbooks.html) är Ansibles konfiguration, distribution och orkestrering språk. De kan beskriva en princip som du vill att din fjärrsystem framtvinga eller en uppsättning steg i en allmän IT-process. När du skapar en spelbok göra du det med hjälp av YAML, som definierar en modell av en konfiguration eller process.

## <a name="ansible-modules"></a>Ansible-moduler

Ansible innehåller en uppsättning [Ansible moduler](http://docs.ansible.com/ansible/latest/modules_by_category.html) som kan köras direkt på fjärrvärdar eller via [spelböcker](http://docs.ansible.com/ansible/latest/playbooks.html). Användarna kan också skapa egna moduler. Moduler kan användas för att styra systemresurser - som-tjänster, paket eller filer – eller köra systemkommandon.

För att interagera med Azure-tjänster, Ansible innehåller en uppsättning [Ansible molnet moduler](http://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) som tillhandahåller verktyg för att enkelt skapa och dirigera infrastrukturen i Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrera en befintlig arbetsbelastning till Azure

När du har använt Ansible för att definiera infrastrukturen kan använda du ditt programs spelbok så att Azure automatiskt skala din miljö efter behov. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatisera molnbaserade program i Azure

Ansible kan du automatisera molnprogram i Azure med Azure-mikrotjänster som [Azure Functions](https://azure.microsoft.com//services/functions/) och [Kubernetes på Azure](https://azure.microsoft.com/services/container-service/kubernetes/)).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Hantera distributioner med dynamisk lager
Via dess [dynamisk lager](http://docs.ansible.com/ansible/intro_dynamic_inventory.html) funktionen, Ansible gör möjligheten att pull inventering från Azure-resurser. Du kan tagga dina befintliga Azure-distributioner och hantera dessa taggade distributioner via Ansible.

## <a name="additional-azure-marketplace-options"></a>Ytterligare alternativ för Azure Marketplace
Den [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) Azure Marketplace-avbildning från Red Hat hjälper organisationer att skala IT-automatisering och hantera komplexa distributioner mellan fysiska, virtuella och molnbaserade infrastrukturer. Ansible Tower innehåller funktioner som ger ytterligare kontrollmöjligheter synlighet, kontroll, säkerhet och effektivitet som behövs för dagens företag. Ansible Tower krypterar autentiseringsuppgifter, till exempel Azure och SSH-nycklar så att du kan delegera jobb till ovana anställda utan att riskera att exponera dina autentiseringsuppgifter.

## <a name="next-steps"></a>Nästa steg
- [Konfigurera Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Skapa en virtuell Linux-dator](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)