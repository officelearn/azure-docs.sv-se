---
title: Använda chef med Azure
description: Introduktion till att använda chef för att konfigurera och testa din Azure-infrastruktur
keywords: Azure, chef, DevOps, virtuella datorer, översikt, automatisera
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: c22faa69bf8f42111d328a9c156dc1a2432731b2
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586350"
---
# <a name="using-chef-with-azure"></a>Använda chef med Azure
[Chef](https://www.chef.io) är en kraftfull Automation-plattform som transformerar infrastrukturen för virtuella datorer på Azure till kod. Chef automatiserar hur infrastrukturen konfigureras, distribueras och hanteras i nätverket, oavsett storlek.

I den här artikeln beskrivs fördelarna med att använda chef för att hantera Azure-infrastruktur.

## <a name="chef-extension-on-azure"></a>Chefs tillägg på Azure
Etablera en virtuell dator med chef-klienten som körs som en bakgrunds tjänst med [chefs tillägget](https://docs.microsoft.com/azure/chef/chef-extension-portal) på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040). När de har allokerats är de här virtuella datorerna klara att hanteras av en chefs Server.

## <a name="chef-cloud-shell"></a>Chefs Cloud Shell
Använd chefs arbets station direkt i Azure Cloud Shell! Kör alla chefs verktyg och INSPEC direkt från Cloud Shell. Du kan använda chefs kommandon från:

* [chef](https://docs.chef.io/ctl_chef.html)
* [artiklar](https://docs.chef.io/ctl_kitchen.html)
* [INSPEC](https://www.inspec.io/docs/reference/cli/)
* [kniv](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [chef-kör](https://www.chef.sh/docs/chef-workstation/getting-started/)

Kombinera våra kommando verktyg med andra verktyg som är tillgängliga i Cloud Shell, till exempel `git`, `az-cli`och `terraform`, och skriv infrastrukturen och efterlevnaden av automatiseringen från webbläsaren.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Automatisera infrastruktur, appar och efterlevnad med en plattform
Företag kräver hastighet, hastighet och säkerhet för att konkurrera i den digitala Marketplace. Chef och Microsoft hjälper personer, grupper och företag att utföra alla dessa saker. Med en plattform kan kock automatiseras och du kan automatisera och ständigt leverera infrastruktur, program och efterlevnad på din Microsoft-egendom.

## <a name="test-drive-chef-automate-on-azure"></a>Testa enhets chef automatiserad på Azure
Chef som stöds av chef kan du [Automatisera Azure Marketplace-lösningen](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) för att bygga, distribuera och hantera din infrastruktur och dina program som är kollektivt. Med ett klick får du omedelbar till gång till alla kommersiella funktioner som ingår i chefs automatisering; få fullständig insyn i hela flottan, aktivera kontinuerlig kompatibilitet och hantera alla ändringar med ett enhetligt arbets flöde.

## <a name="next-steps"></a>Nästa steg

* [Skapa en virtuell Windows-dator på Azure med chef](chef-automation.md)
