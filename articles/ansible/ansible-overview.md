---
title: Med Azure Ansible
description: Introduktion till Ansible till automatiserar punktetablering, konfigurationshantering och distribution av program.
ms.service: ansible
keywords: ansible, azure, devops, översikt, etablera moln, konfigurationshantering, programdistribution, ansible moduler, ansible playbooks
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/19/2018
ms.topic: article
ms.openlocfilehash: a7ce3c239a50462a9af137eb958268f72dbf79d1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
ms.locfileid: "28212108"
---
# <a name="ansible-with-azure"></a>Ansible med Azure

[Ansible](http://www.ansible.com) är en produkt med öppen källkod som automatiserar punktetablering för konfigurationshantering och distribution av program. Med hjälp av Ansible kan du etablera virtuella datorer, behållare och nätverk och slutföra molninfrastrukturer. Dessutom kan Ansible du automatisera distributionen och konfigurationen av resurser i din miljö.

Den här artikeln ger en översikt över några av fördelarna med att använda Ansible med Azure.

## <a name="ansible-playbooks"></a>Ansible playbooks

[Ansible playbooks](http://docs.ansible.com/ansible/latest/playbooks.html) är Ansibles konfiguration, distribution och orchestration-språk. De kan beskriver en princip som du vill att din fjärranslutna system att framtvinga eller en uppsättning steg i en allmän IT-process. När du skapar en playbook göra du det med hjälp av YAML som definierar en modell av en konfiguration eller en process.

## <a name="ansible-modules"></a>Ansible moduler

Ansible innehåller en uppsättning [Ansible moduler](http://docs.ansible.com/ansible/latest/modules_by_category.html) som kan köras direkt på fjärrvärdar eller via [playbooks](http://docs.ansible.com/ansible/latest/playbooks.html). Användare kan också skapa egna moduler. Moduler kan användas för att styra systemresurser -, till exempel tjänster, paket eller filer – eller köra systemkommandon.

För att interagera med Azure-tjänster, Ansible innehåller en uppsättning [Ansible moln moduler](http://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) som tillhandahåller verktyg för att enkelt skapa och samordna infrastrukturen i Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrera befintliga arbetsbelastningar till Azure

När du har använt Ansible för att definiera din infrastruktur kan använda du programmets playbook att låta Azure automatiskt skala din miljö efter behov. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatisera moln-intern program i Azure

Ansible kan du automatisera moln-intern program i Azure med Azure mikrotjänster [Azure Functions](https://azure.microsoft.com//services/functions/) och [Kubernetes på Azure](https://azure.microsoft.com/services/container-service/kubernetes/)).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Hantera distributioner med dynamiska inventering
Via dess [dynamiska inventering](http://docs.ansible.com/ansible/intro_dynamic_inventory.html) funktionen Ansible ger möjlighet till pull-lagret från Azure-resurser. Du kan tagga din befintliga Azure-distributioner och hantera dessa taggade Ansible.

## <a name="additional-azure-marketplace-options"></a>Ytterligare alternativ för Azure Marketplace
Den [Ansible torn](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) Azure Marketplace-avbildning av Red Hat hjälper organisationer att skala IT automation och hantera komplexa distributioner i fysiska, virtuella, och molninfrastrukturer. Ansible torn innehåller funktioner som ger ytterligare synlighet, kontroll, säkerhet och effektivitet som är nödvändiga för dagens företag. Ansible torn krypterar autentiseringsuppgifter, till exempel Azure och SSH-nycklar så att du kan delegera jobb till mindre erfarna anställda utan risken att exponera dina autentiseringsuppgifter.

## <a name="next-steps"></a>Nästa steg
- [Konfigurera Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Skapa en virtuell Linux-dator](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)