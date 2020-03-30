---
title: Använda Chef med Azure
description: Introduktion till att använda Chef för att konfigurera och testa din Azure-infrastruktur
keywords: azurblå,, devops, virtuella datorer, översikt, automatisera
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: c22faa69bf8f42111d328a9c156dc1a2432731b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586350"
---
# <a name="using-chef-with-azure"></a>Använda Chef med Azure
[Chef](https://www.chef.io) är en kraftfull automatiseringsplattform som omvandlar infrastruktur för virtuella datorer på Azure till kod. Chef automatiserar hur infrastrukturen konfigureras, distribueras och hanteras i nätverket, oavsett storlek.

I den här artikeln beskrivs fördelarna med att använda Chef för att hantera Azure-infrastruktur.

## <a name="chef-extension-on-azure"></a>Chef-tillägg på Azure
Etablera en virtuell dator med Chef-klient som körs som en bakgrundstjänst med [cheftillägget](https://docs.microsoft.com/azure/chef/chef-extension-portal) på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040). När de har etablerats är dessa virtuella datorer redo att hanteras av en Chef-server.

## <a name="chef-cloud-shell"></a>Chef Cloud Shell
Använd Chef Workstation direkt i Azure Cloud Shell! Kör alla dina chefverktyg och InSpec direkt från Cloud Shell. Du kan använda chefkommandona från:

* [chef](https://docs.chef.io/ctl_chef.html)
* [Kök](https://docs.chef.io/ctl_kitchen.html)
* [inspec (inspec)](https://www.inspec.io/docs/reference/cli/)
* [Kniv](https://docs.chef.io/knife.html)
* [cookstyle (olikartade)](https://docs.chef.io/cookstyle.html)
* [kock-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

Kombinera våra kommandoverktyg med de andra verktygen `git`som `az-cli`finns `terraform`i Cloud Shell, till exempel , och och skriv din infrastruktur och efterlevnadsautomatisering från webbläsaren.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Automatisera infrastruktur, appar och efterlevnad av en plattform
Företag kräver snabbhet, hastighet och säkerhet för att konkurrera på den digitala marknaden. Tillsammans hjälper Chef och Microsoft individer, team och företag att utföra alla dessa saker. Med en plattform, Chef Automate, kan du nu automatisera och kontinuerligt leverera din infrastruktur, dina program och din efterlevnad i din Microsoft-egendom.

## <a name="test-drive-chef-automate-on-azure"></a>Provkörningskockautomatera på Azure
Med chef [Automate Azure Marketplace-lösningen](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) med stöd av Chef kan du skapa, distribuera och hantera din infrastruktur och dina program tillsammans. Ett klick ger dig omedelbar tillgång till alla kommersiella funktioner som ingår i Chef Automate; få heltäckande synlighet i hela din vagnpark, möjliggör kontinuerlig efterlevnad och hantera alla ändringar med ett enhetligt arbetsflöde.

## <a name="next-steps"></a>Nästa steg

* [Skapa en virtuell Windows-dator på Azure med Chef](chef-automation.md)
