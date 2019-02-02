---
title: Använda Chef med Azure
description: Introduktion till att använda Chef för att konfigurera och testa Azure-infrastrukturen
ms.service: virtual-machines-linux
keywords: Azure, chef, devops, virtuella datorer, översikt, automatisera
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: c0ec8b98ff711f8e5746d6d4731266ed4b09cc8f
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658852"
---
# <a name="using-chef-with-azure"></a>Använda Chef med Azure
[Chef](http://www.chef.io) är en kraftfull automatisering plattform som omvandlar infrastruktur med virtuella datorer på Azure till koden. Chef automatiserar hur infrastrukturen konfigureras, distribueras och hanteras i nätverket, oavsett dess storlek.

Den här artikeln beskriver fördelarna med att använda Chef för att hantera Azure-infrastrukturen.

## <a name="chef-extension-on-azure"></a>Chef-tillägget på Azure
Etablera en virtuell dator med Chef-klienten som körs som en bakgrundstjänst med den [Chef tillägget](https://docs.microsoft.com/azure/chef/chef-extension-portal) på Azure Portal. Dessa virtuella datorer är nu redo att hanteras av en Chef-server när etablerats.

## <a name="chef-cloud-shell"></a>Chef Cloud Shell
Använd Chef arbetsstation direkt i Azure Cloudshell! Kör alla dina Chef verktyg och InSpec rätt från Cloud Shell. Du kan använda Chef-kommandon från:

* [chef](https://docs.chef.io/ctl_chef.html)
* [kitchen](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [kniv](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [foodcritic](https://docs.chef.io/foodcritic.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

Kombinera våra kommandoradsverktyg med andra verktyg som finns tillgängliga i Cloud Shell, till exempel `git`, `az-cli`, och `terraform`, och skriva din infrastruktur och efterlevnad automation från webbläsaren.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Automatisera infrastruktur, appar och kompatibilitet med en plattform
Företag kräver hastighet, hastighet och säkerhet att tävla i digital marketplace. Tillsammans Chef och Microsoft hjälper enskilda användare, grupper och företag att åstadkomma detta. Med en plattform, Chef Automate, kan du nu automatisera och kontinuerligt leverera infrastruktur, program och efterlevnad i Microsoft-egendom.

## <a name="test-drive-chef-automate-on-azure"></a>Test Chef Automate på Azure
Stöds av Chef, den [Chef automatisera Azure Marketplace-lösningen](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) gör det möjligt att bygga, distribuera och hantera infrastruktur och program samarbeta. Ett enda klick kommer du direkt åtkomst till alla kommersiella funktioner som ingår i Chef Automate; få insyn för slutpunkt till slutpunkt över hela flottan, möjliggör kontinuerlig efterlevnad och hantera alla ändringar i ett enhetligt arbetsflöde.

## <a name="next-steps"></a>Nästa steg

* [Skapa en virtuell Windows-dator på Azure med Chef](/azure/virtual-machines/windows/chef-automation)
