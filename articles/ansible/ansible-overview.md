---
title: Använda Ansible med Azure
description: En introduktion till att använda Ansible för att automatisera etablering i molnet, konfigurationshantering och programdistribution.
ms.service: azure
keywords: ansible, azure, devops, overview, cloud provision, configuration management, application deployment, ansible modules, ansible playbooks
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 09/02/2018
ms.topic: overview
ms.openlocfilehash: d7fc86f9968ede37623ea9c89a666a35cba7eee8
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791604"
---
# <a name="ansible-with-azure"></a>Ansible med Azure

[Ansible](https://www.ansible.com) är en produkt som bygger på öppen källkod och som automatiserar molntilldelning, konfigurationshantering och programdistribution. Med Ansible kan du tilldela virtuella datorer, containrar, nätverk och kompletta molninfrastrukturer. Med Ansible kan du dessutom automatisera distributionen och konfigurationen av resurser i din miljö.

Den här artikeln ger en översikt över några av fördelarna med att använda Ansible med Azure.

## <a name="ansible-playbooks"></a>Spelböcker i Ansible

[Spelböcker](https://docs.ansible.com/ansible/latest/playbooks.html) är konfigurations-, distributions- och orkestreringsspråket i Ansible. De kan beskriva en princip som ska tillämpas i dina fjärrsystem eller en uppsättning steg i en allmän IT-process. Du skapar en spelbok med hjälp av YAML, som definierar en modell av en konfiguration eller process.

## <a name="ansible-modules"></a>Moduler i Ansible

I Ansible ingår en uppsättning [moduler](https://docs.ansible.com/ansible/latest/modules_by_category.html) som kan köras direkt på fjärrvärdar eller via [spelböcker](https://docs.ansible.com/ansible/latest/playbooks.html). Användare kan också skapa egna moduler. Modulerna kan användas till att hantera systemresurser som tjänster, paket eller filer – eller köra systemkommandon.

Ansible innehåller även en uppsättning [molnmoduler](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) för interaktion med Azure-tjänster. Dessa tillhandahåller verktyg så att du enkelt kan skapa och hantera din infrastruktur i Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrera en befintlig arbetsbelastning till Azure

När du har definierat infrastrukturen med Ansible kan du tillämpa programmets spelbok så att Azure automatiskt skalar miljön efter behov. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatisera molnbaserade program i Azure

Med Ansible kan du automatisera molnbaserade program i Azure med Azure-mikrotjänster som [Azure Functions](https://azure.microsoft.com//services/functions/) och [Kubernetes på Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Hantera distributioner med dynamiskt lager
Via dess [dynamiska lagerfunktion](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) tillhandahåller Ansible möjligheten att hämta tillgångar från Azure-resurser. Sedan kan du tagga befintliga Azure-distributioner och hantera dem via Ansible.

## <a name="additional-azure-marketplace-options"></a>Fler Azure Marketplace-alternativ
[Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) från Red Hat hjälper organisationer att skala IT-automatisering och hantera komplexa distributioner mellan fysiska, virtuella och molnbaserade infrastrukturer. Ansible Tower innehåller funktioner som ger ytterligare synlighetsnivåer, kontroll, säkerhet och den effektivitet som krävs för moderna företag. Ansible Tower krypterar autentiseringsuppgifter som Azure- och SSH-nycklar så att du kan delegera jobb till mindre erfarna medarbetare utan att riskera att autentiseringsuppgifterna exponeras.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Ansible-moduler och versioner för Azure
Ansible levereras med ett antal moduler som kan köras direkt på fjärrvärdar eller via spelböcker.
I artikeln om [Ansibles moduler och versioner](./ansible-matrix.md) visas de Ansible-moduler för Azure som kan tillhandahålla Azure-molnresurser, som virtuella dator-, nätverks och containertjänster. 

## <a name="next-steps"></a>Nästa steg
- [Konfigurera Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Skapa en virtuell Linux-dator](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
