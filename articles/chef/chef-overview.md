---
title: Använda chef med Azure
description: Introduktion till att använda chef för att konfigurera och testa din Azure-infrastruktur
keywords: Azure, chef, DevOps, virtuella datorer, översikt, automatisera
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 4ad8b79b42c9d8d7942f391223c052f63579b11b
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158005"
---
# <a name="using-chef-with-azure"></a>Använda chef med Azure
[Chef](https://www.chef.io) är en kraftfull Automation-plattform som transformerar infrastrukturen för virtuella datorer på Azure till kod. Chef automatiserar hur infrastrukturen konfigureras, distribueras och hanteras i nätverket, oavsett storlek.

I den här artikeln beskrivs fördelarna med att använda chef för att hantera Azure-infrastruktur.

## <a name="chef-extension-on-azure"></a>Chefs tillägg på Azure
Etablera en virtuell dator med chef-klienten som körs som en bakgrunds tjänst med [chefs tillägget](https://docs.microsoft.com/azure/chef/chef-extension-portal) på Azure-portalen. När de har allokerats är de här virtuella datorerna klara att hanteras av en chefs Server.

## <a name="chef-cloud-shell"></a>Chefs Cloud Shell
Använd chefs arbets station direkt i Azure Cloud Shell! Kör alla chefs verktyg och INSPEC direkt från Cloud Shell. Du kan använda chefs kommandon från:

* [chef](https://docs.chef.io/ctl_chef.html)
* [artiklar](https://docs.chef.io/ctl_kitchen.html)
* [INSPEC](https://www.inspec.io/docs/reference/cli/)
* [kniv](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [foodcritic](https://docs.chef.io/foodcritic.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

Kombinera våra kommando verktyg med andra verktyg som är tillgängliga i Cloud Shell, till exempel `git`, `az-cli`och `terraform`, och skriv infrastrukturen och efterlevnaden av automatiseringen från webbläsaren.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Automatisera infrastruktur, appar och efterlevnad med en plattform
Företag kräver hastighet, hastighet och säkerhet för att konkurrera i den digitala Marketplace. Chef och Microsoft hjälper personer, grupper och företag att utföra alla dessa saker. Med en plattform kan kock automatiseras och du kan automatisera och ständigt leverera infrastruktur, program och efterlevnad på din Microsoft-egendom.

## <a name="test-drive-chef-automate-on-azure"></a>Testa enhets chef automatiserad på Azure
Chef som stöds av chef kan du [Automatisera Azure Marketplace-lösningen](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) för att bygga, distribuera och hantera din infrastruktur och dina program som är kollektivt. Med ett klick får du omedelbar till gång till alla kommersiella funktioner som ingår i chefs automatisering; få fullständig insyn i hela flottan, aktivera kontinuerlig kompatibilitet och hantera alla ändringar med ett enhetligt arbets flöde.

## <a name="next-steps"></a>Nästa steg

* [Skapa en virtuell Windows-dator på Azure med chef](/azure/virtual-machines/windows/chef-automation)
