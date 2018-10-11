---
title: Översikt över virtuella Linux-datorer i Azure | Microsoft Docs
description: Beskriver Azures beräknings-, lagrings- och nätverkstjänster med virtuella Linux-datorer.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: jeconnoc
editor: ''
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/29/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 6ed1ef6019e339768c4ba6a62d8b4d99d844f254
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949541"
---
# <a name="azure-and-linux"></a>Azure och Linux
Microsoft Azure är en växande samling integrerade offentliga molntjänster som omfattar analyser, virtuella datorer, databaser, mobil, nätverk, lagring och webb – perfekt som värd för dina lösningar.  Microsoft Azure tillhandahåller en skalbar plattform som gör att du bara betalar för vad du använder, när du behöver det – utan att behöva investera i lokal maskinvara.  Azure är klar när du är klar att skala upp och ut lösningar oavsett vilken skala du behöver för dina klienter.

Om du känner till de olika funktionerna i Amazons AWS kan du utforska [dokumentet för definitionsmappning](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) för Azure jämfört med AWS.

## <a name="regions"></a>Regioner
Microsoft Azure-resurser fördelas på flera geografiska områden runtom i världen.  En ”region” representerar flera datacenter i ett enda geografiskt område. Azure har för närvarande (i augusti 2018) 42 regioner som är allmänt tillgängliga runt världen och ytterligare 12 regioner har tillkännagivits. Det är fler globala regioner än någon annan molnleverantör. En uppdaterad lista över befintliga och nyligen tillkännagivna regioner finns på följande sida:

* [Azure-regioner](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Tillgänglighet
Azure har tillkännagivit ett branschledande serviceavtal på 99,9 % för virtuella datorer med en instans, förutsatt att du distribuerar den virtuella datorn med premiumlagring för alla diskar.  För att distributionen ska kunna omfattas av standardserviceavtalet på 99,95 % för virtuella datorer behöver du fortfarande distribuera två eller flera virtuella datorer som kör arbetsbelastningen i en tillgänglighetsuppsättning. En tillgänglighetsuppsättning säkerställer att dina virtuella datorer distribueras via flera feldomäner i Azure-datacentren och på värdar med olika underhållsfönster. I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/) förklaras den garanterade tillgängligheten för Azure som helhet.

## <a name="managed-disks"></a>Managed Disks

Managed Disks hanterar skapande och hantering av Azure-lagringskontot i bakgrunden och säkerställer att du inte behöver bekymra dig om lagringskontots skalbarhetsgränser. Du anger diskens storlek och prestandanivå (Standard eller Premium) och sedan skapar och hanterar Azure disken. Om du lägger till diskar eller skalar upp eller ned den virtuella datorn behöver du inte oroa dig om lagringsutrymmet som används. Om du skapar nya virtuella datorer ska du [använda Azure CLI](quick-create-cli.md) eller Azure-portalen för att skapa virtuella datorer med hanterade OS- och datadiskar. Om du har virtuella datorer med ohanterade diskar kan du [konvertera de virtuella datorerna så att de stöds av Managed Disks](convert-unmanaged-to-managed-disks.md).

Du kan även hantera dina anpassade avbildningar i ett lagringskonto per Azure-region och använda dem för att skapa hundratals virtuella datorer i samma prenumeration. Mer information om Managed Disks finns i [översikten över Managed Disks](../linux/managed-disks-overview.md).

## <a name="azure-virtual-machines--instances"></a>Virtuella Azure-datorer och instanser
Microsoft Azure har stöd för körning av ett antal populära Linux-distributioner som tillhandahålls och underhålls av ett antal partners.  Du hittar distributioner som Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD med flera i Azure Marketplace. Microsoft arbetar aktivt med flera Linux-communities för att lägga till ännu fler alternativ i listan över [Azure-godkända Linux-distributioner](endorsed-distros.md).

Om den önskade Linux-distributionen inte finns i galleriet för närvarande kan du ”Bring your own Linux”-VM genom att [skapa och ladda upp en virtuell Linux-hårddisk i Azure](create-upload-generic.md).

Virtuella Azure-datorer gör det möjligt att distribuera en lång rad databehandlingslösningar på ett smidigt sätt. Du kan distribuera i princip vilken arbetsbelastning och vilket språk som helst på nästan vilket operativsystem som helst – Windows, Linux eller ett anpassat från någon av den växande listan med partner. Hittar du fortfarande inte det du letar efter?  Oroa dig inte, du kan även hämta egna avbildningar lokalt.

## <a name="vm-sizes"></a>VM-storlekar
[Storleken](sizes.md) på den virtuella datorn som du använder bestäms av den arbetsbelastning som du vill köra. Storleken som du väljer avgör sedan faktorer som processorkraft, minne och lagringskapacitet. Azure erbjuder en rad olika storlekar för att passa en mängd olika användningar.

Azure debiterar ett [pris per timme](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) baserat på storleken och operativsystemet för den virtuella datorn. För delar av timmar tar Azure bara betalt för användningen per minut. Lagringsutrymme prissätts och debiteras separat.

## <a name="automation"></a>Automation
All infrastruktur måste vara kod för att uppnå en korrekt DevOps-kultur.  När hela infrastrukturen finns i koden är den lätt att återskapa (Phoenix-servrar).  Azure fungerar med alla större automatiseringsverktyg som Ansible, Chef, SaltStack och Puppet.  Azure har även en egen verktygsuppsättning för automatisering:

* [Azure-mallar](create-ssh-secured-vm-from-template.md)
* [Azure VMAccess](using-vmaccess-extension.md)

Azure inför stöd för [cloud-init](http://cloud-init.io/) i de flesta Linux-distributioner som har stöd för det.  För närvarande distribueras Canonicals virtuella Ubuntu-datorer med cloud-init aktiverat som standard.  Red Hats RHEL, CentOS och Fedora har stöd för cloud-init, men Azure-avbildningarna som underhålls av Red Hat har inte cloud-init installerat för närvarande.  Om du vill använda cloud-init på ett operativsystem i Red Hat-gruppen måste du skapa en anpassad avbildning med cloud-init installerat.

* [Använda cloud-init på virtuella Azure Linux-datorer](using-cloud-init.md)

## <a name="quotas"></a>Kvoter
Varje Azure-prenumeration har standardkvotgränser som kan påverka ditt projekt om många virtuella datorer distribueras. Den aktuella gränsen på basis av per prenumeration är 20 virtuella datorer per region.  Kvotgränser kan höjas snabbt och enkelt genom att skicka in ett supportärende med en fråga om ökning av gränsen.  Mer information om kvotgränser:

* [Tjänstbegränsningar för Azure-prenumeration](../../azure-subscription-service-limits.md)

## <a name="partners"></a>Partner
Microsoft har ett nära samarbete med partner för att se till att de tillgängliga avbildningarna är uppdaterade och optimerade för Azure-körning.  Mer information om Azure-partner finns på följande länkar:

* Linux på Azure – [godkända distributioner](endorsed-distros.md)
* SUSE – [Azure Marketplace – SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=%27SUSE%27)
* Red Hat – [Azure Marketplace – Red Hat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical – [Azure Marketplace – Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian – [Azure Marketplace – Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD – [Azure Marketplace – FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
* CoreOS – [Azure Marketplace – CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS – [Azure Marketplace – RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami – [Bitnami Library för Azure](https://azure.bitnami.com/)
* Mesosphere – [Azure Marketplace – Mesosphere DC/OS på Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker – [Azure Marketplace – Azure Container Service med Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins – [Azure Marketplace – CloudBees Jenkins Platform](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)

## <a name="getting-started-with-linux-on-azure"></a>Komma igång med Linux på Azure
Om du vill börja använda Azure behöver du ett Azure-konto, Azure CLI installerat och ett par offentliga och privata SSH-nycklar.

### <a name="sign-up-for-an-account"></a>Registrera dig för ett konto
Det första steget i att använda Azure Cloud är att registrera dig för ett Azure-konto.  Gå till sidan [Azure-kontoinloggning](https://azure.microsoft.com/pricing/free-trial/) för att komma igång.

### <a name="install-the-cli"></a>Installera CLI
Med ditt nya Azure-konto kan du komma igång direkt med att använda Azure-portalen, som är en webbaserad administrationspanel.  Om du vill hantera Azure Cloud via kommandoraden installerar du `azure-cli`.  Installera [Azure CLI](/cli/azure/install-azure-cli) på Mac- eller Linux-arbetsstationen.

### <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar
Nu har du ett Azure-konto, Azure-webbportalen och Azure CLI.  Nästa steg är att skapa ett SSH-nyckelpar som används för SSH i Linux utan att behöva använda ett lösenord.  [Skapa SSH-nycklar på Linux och Mac](mac-create-ssh-keys.md) om du vill möjliggöra lösenordsfri inloggning och bättre säkerhet.

### <a name="create-a-vm-using-the-cli"></a>Skapa en virtuell dator med CLI
Att skapa en virtuell Linux-dator med CLI är ett snabbt sätt att distribuera en virtuell dator utan att lämna terminalen du arbetar på.  Allt du kan ange på webbportalen är tillgängligt via en flagga eller switch på kommandoraden.  

* [Skapa en virtuell Linux-dator med CLI](quick-create-cli.md)

### <a name="create-a-vm-in-the-portal"></a>Skapa en virtuell dator i portalen
Att skapa en virtuell Linux-dator i Azure-webbportalen är ett sätt att enkelt gå igenom de olika alternativen för att komma till en distribution.  I stället för att använda flaggor eller switchar på kommandoraden kan du se en snygg webblayout med olika alternativ och inställningar.  Allt som är tillgängligt via kommandoradsgränssnittet är även tillgängligt i portalen.

* [Skapa en virtuell Linux-dator med portalen](quick-create-portal.md)

### <a name="log-in-using-ssh-without-a-password"></a>Logga in med SSH utan ett lösenord
Den virtuella datorn körs nu på Azure och du är redo att logga in.  Att använda lösenord för att logga in via SSH är osäkert och tidskrävande.  Det säkraste och snabbaste sättet att logga in är att använda SSH-nycklar.  När du skapar den virtuella Linux-datorn via portalen eller CLI har du två autentiseringsval.  Om du väljer ett lösenord för SSH konfigurerar Azure den virtuella datorn så att den tillåter inloggning via lösenord.  Om du väljer att använda en offentlig SSH-nyckel konfigurerar Azure den virtuella datorn så att den endast tillåter inloggningar via SSH-nycklar och inaktiverar inloggningar med lösenord. Om du vill skydda den virtuella Linux-datorn genom att endast tillåta SSH-nyckelinloggningar använder du alternativet med offentlig SSH-nyckel när du skapar den virtuella datorn i portalen eller CLI.

## <a name="related-azure-components"></a>Relaterade Azure-komponenter
## <a name="storage"></a>Storage
* [Introduktion till Microsoft Azure Storage](../../storage/common/storage-introduction.md)
* [Lägga till en disk i en virtuell Linux-dator med azure-cli](add-disk.md)
* [Lägga till en datadisk i en virtuell Linux-dator i Azure-portalen](attach-disk-portal.md)

## <a name="networking"></a>Nätverk
* [Översikt över virtuella nätverk](../../virtual-network/virtual-networks-overview.md)
* [IP-adresser i Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Öppna portar till en virtuell Linux-dator i Azure](nsg-quickstart.md)
* [Skapa ett fullständigt domännamn i Azure-portalen](portal-create-fqdn.md)

## <a name="containers"></a>Containrar
* [Virtuella datorer och containrar i Azure](containers.md)
* [Introduktion till Azure Container Service](../../container-service/container-service-intro.md)
* [Distribuera ett Azure Container Service-kluster](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>Nästa steg
Nu har du en översikt över Linux på Azure.  Nästa steg är att sätta igång och skapa några virtuella datorer!

* [Utforska den växande listan med exempelskript för vanliga uppgifter via AzureCLI](cli-samples.md)
