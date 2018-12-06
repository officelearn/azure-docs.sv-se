---
title: Med Ansible med Azure
description: Introduktion till att använda Ansible till automatiserar molntilldelning, konfigurationshantering och programdistributioner.
ms.service: ansible
keywords: ansible, azure, devops, översikt, etablera molnet, konfigurationshantering, programdistribution, ansible-moduler, ansible-spelböcker
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 09/02/2018
ms.topic: article
ms.openlocfilehash: 22eeb3993cd408a8369236683da3db466a348a30
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956788"
---
# <a name="ansible-with-azure"></a>Ansible med Azure

[Ansible](https://www.ansible.com) är en produkt med öppen källkod som automatiserar molntilldelning, konfigurationshantering och programdistributioner. Med Ansible kan du etablera virtuella datorer, behållare och nätverk och slutföra molninfrastrukturer. Dessutom kan Ansible du automatisera distributionen och konfigurationen av resurser i din miljö.

Den här artikeln ger en översikt över några av fördelarna med att använda Ansible med Azure.

## <a name="ansible-playbooks"></a>Ansible-spelböcker

[Ansible-spelböcker](https://docs.ansible.com/ansible/latest/playbooks.html) är Ansibles konfiguration, distribution och orkestrering språk. De kan beskriva en princip som du vill att din fjärrsystem framtvinga eller en uppsättning steg i en allmän IT-process. När du skapar en spelbok göra du det med hjälp av YAML, som definierar en modell av en konfiguration eller process.

## <a name="ansible-modules"></a>Ansible-moduler

Ansible innehåller en uppsättning [Ansible moduler](https://docs.ansible.com/ansible/latest/modules_by_category.html) som kan köras direkt på fjärrvärdar eller via [spelböcker](https://docs.ansible.com/ansible/latest/playbooks.html). Användarna kan också skapa egna moduler. Moduler kan användas för att styra systemresurser - som-tjänster, paket eller filer – eller köra systemkommandon.

För att interagera med Azure-tjänster, Ansible innehåller en uppsättning [Ansible molnet moduler](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) som tillhandahåller verktyg för att enkelt skapa och dirigera infrastrukturen i Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrera en befintlig arbetsbelastning till Azure

När du har använt Ansible för att definiera infrastrukturen kan använda du ditt programs spelbok så att Azure automatiskt skala din miljö efter behov. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatisera molnbaserade program i Azure

Ansible kan du automatisera molnprogram i Azure med Azure-mikrotjänster som [Azure Functions](https://azure.microsoft.com//services/functions/) och [Kubernetes på Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Hantera distributioner med dynamisk lager
Via dess [dynamisk lager](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) funktionen, Ansible gör möjligheten att pull inventering från Azure-resurser. Du kan tagga dina befintliga Azure-distributioner och hantera dessa taggade distributioner via Ansible.

## <a name="additional-azure-marketplace-options"></a>Ytterligare alternativ för Azure Marketplace
Den [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) Azure Marketplace-avbildning från Red Hat hjälper organisationer att skala IT-automatisering och hantera komplexa distributioner mellan fysiska, virtuella och molnbaserade infrastrukturer. Ansible Tower innehåller funktioner som ger ytterligare kontrollmöjligheter synlighet, kontroll, säkerhet och effektivitet som behövs för dagens företag. Ansible Tower krypterar autentiseringsuppgifter, till exempel Azure och SSH-nycklar så att du kan delegera jobb till ovana anställda utan att riskera att exponera dina autentiseringsuppgifter.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Ansible-modulen och version matris för Azure
Ansible levereras med ett antal moduler som kan utföras direkt på fjärrvärdar eller via spelböcker.
Den [Ansible-modulen och version matris](./ansible-matrix.md) visar Ansible-moduler för Azure som kan utnyttja Azure-molnresurser, till exempel virtuella datorer, nätverk och behållartjänster. 

## <a name="next-steps"></a>Nästa steg
- [Konfigurera Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Skapa en virtuell Linux-dator](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
