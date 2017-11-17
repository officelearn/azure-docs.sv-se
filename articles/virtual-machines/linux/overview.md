---
title: "Översikt över virtuella Linux-datorer i Azure | Microsoft Docs"
description: "Beskriver Azure Compute, lagring och nätverk tjänster med Linux virtuella datorer."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: timlt
editor: 
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: cef9abddf980c695040e99995eb325eeb182fad4
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="azure-and-linux"></a>Azure och Linux
Microsoft Azure är en växande samling integrerade offentliga molntjänster, inklusive analytics, virtuella datorer, databaser, mobil-, nätverk, lagring, och web&mdash;perfekt för värd för dina lösningar.  Microsoft Azure tillhandahåller en skalbar plattform som gör att du bara betalar för vad du använder, när du behöver det – utan att behöva investera i lokal maskinvara.  Azure är klar när du är klar att skala upp och ut lösningar oavsett vilken skala du behöver för dina klienter.

Om du är bekant med de olika funktionerna i Amazon's AWS, kan du undersöka Azure vs AWS [definitionen mappning dokumentet](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).

## <a name="regions"></a>Regioner
Microsoft Azure-resurser är fördelade på flera geografiska regioner runtom i världen.  ”Region” representerar flera datacenter i ett enda geografiskt område.  Vi har 34 regioner som är allmänt tillgänglig i världen med en ytterligare 4 regioner meddelats. Eftersom vi fortsätter att expandera våra global täckning - upprätthålla en uppdaterad lista av befintliga och nya meddelats regioner.

* [Azure-regioner](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Tillgänglighet
Vi har meddelat en branschen inledande instans virtuella serviceavtal för 99,9% som du distribuerar den virtuella datorn med premium-lagring för alla diskar.  För distributionen att vår standard 99,95% VM servicenivåavtalet behöver du fortfarande distribuera två eller flera virtuella datorer som körs din arbetsbelastning i en tillgänglighetsuppsättning. Detta garanterar dina virtuella datorer är fördelade på flera feldomäner i våra datacenter som distribuerats på värdar med olika underhållsfönster. I det fullständiga[Azure-serviceavtalet](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) förklaras den garanterade tillgängligheten för Azure som helhet.

## <a name="managed-disks"></a>Managed Disks

Hanterade diskar referenser Azure Storage skapande av konton och hantering i bakgrunden för dig och ser till att du inte behöver bry dig om skalbarhetsbegränsningar för lagringskontot. Du bara ange diskens storlek och prestandanivå (Standard eller Premium) och Azure skapar och hanterar disken åt dig. Även om du lägger till diskar eller skalar upp eller ned den virtuella datorn behöver du inte oroa dig om lagringsutrymmet som används. Om du skapar nya virtuella datorer, [använder Azure CLI 2.0](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller Azure portal för att skapa virtuella datorer med hanterade Operativsystemet och datadiskarna. Om du har virtuella datorer med ohanterad diskar, kan du [omvandla dina virtuella datorer till säkerhetskopieras med hanterade diskar](convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Du kan även hantera dina anpassade avbildningar i ett lagringskonto per Azure-region och använda dem för att skapa hundratals virtuella datorer i samma prenumeration. Mer information om Managed Disks finns i [Översikt över Azure Managed Disks](../windows/managed-disks-overview.md).

## <a name="azure-virtual-machines--instances"></a>Virtuella Azure-datorer & instanser
Microsoft Azure har stöd för flera populära Linux-distributioner tillhandahålls och underhålls av ett antal partners.  Du hittar distributioner, till exempel Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, virtuell CoreOS, RancherOS, FreeBSD och mer i Azure Marketplace. Vi arbetar aktivt med olika Linux communities att lägga till ytterligare varianter till den [Azure-godkända Linux Distros](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lista.

Om din önskade Linux distro väljer inte är för närvarande finns i galleriet, du kan ”ta egna Linux” VM av [skapa och ladda upp en Linux VHD i Azure](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Virtuella Azure-datorer kan du distribuera en mängd olika databehandling lösningar i en flexibel metod. Du kan distribuera valfri arbetsbelastning och alla språk på nästan alla operativsystem - Windows, Linux, eller en anpassad skapat en från någon av våra växande listan över partners. Fortfarande inte kan se vad du letar efter?  Oroa dig inte – du kan också hämta dina egna avbildningar från lokala.

## <a name="vm-sizes"></a>VM-storlekar
När du distribuerar en virtuell dator i Azure, ska du välja en VM-storlek i en av våra serie storlekar som är lämplig för din arbetsbelastning. Storlek påverkar också bearbetningskapacitet för power, minne och lagringsutrymme på den virtuella datorn. Du debiteras baserat på hur lång tid den virtuella datorn körs och använda dess allokerade resurser. En fullständig lista över [storlekar på virtuella datorer](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Här följer några riktlinjer för att välja en VM-storlek från en av våra (A, D, DS, G och GS).
* A-serien är vår värdet prissatta enklare virtuella datorer för enstaka arbetsbelastningar och scenarier för utveckling och testning. De kan är allmänt tillgänglig i alla regioner och ansluta och använda alla standard resurser tillgängliga för virtuella datorer.
* A-series-storlekar (A8 - A11) är särskilda beräkning beräkningsintensiva konfigurationer lämpligt för högpresterande kluster datorprogram.
* Virtuella datorer i D-serien är utformade för att köra program som kräver högre beräkningskraft och tillfälliga diskprestanda. Virtuella datorer i D-serien erbjuder snabbare processorer, högre ”minne till virtuell processor”-förhållande och en Solid State-hårddisk (SSD) för den temporära disken.
* Dv2-serien, är den senaste versionen av vår D-serien, har en kraftfullare processor. Processorn i Dv2-serien är cirka 35 % snabbare än den i D-serien. Den är baserad på den senaste generationen 2,4 GHz Intel Xeon® E5-2673 v3 (Haskell) processor, och kan gå upp till 3,2 GHz med Intel Turbo förstärkningen teknik 2.0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.
* Virtuella datorer i G-serien erbjuder mest minne och körs på värdar som är utrustade med processorer i Intel Xeon E5 V3-familjen.

Obs: DS-serien och GS-serien virtuella datorer har tillgång till Premium-lagring – våra SSD säkerhetskopieras hög prestanda och låg latens lagring för i/o-intensiv arbetsbelastning. Premium Storage är tillgängligt i vissa regioner. Mer information finns i:

* [Premium-lagring: Högpresterande lagring för arbetsbelastningar på virtuella Azure-datorn](../windows/premium-storage.md)

## <a name="automation"></a>Automation
För att få en korrekt DevOps-kultur, finnas alla infrastruktur kod.  När alla infrastrukturen bor i koden den enkelt kan återskapas (Phoenix servrar).  Azure fungerar med alla större automation tooling som Ansible, Chef, SaltStack och Puppet.  Azure har också ett eget verktygsuppsättning för automatisering:

* [Azure-mallar](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Stöd för inför Azure [moln init](http://cloud-init.io/) i de flesta Linux Distros som stöder detta.  För närvarande distribueras Canonical's Ubuntu virtuella datorer med molnet init aktiverad som standard.  Röd tak RHEL, CentOS och Fedora stöder molnet init, men Azure-avbildningar som underhålls av RedHat inte har installerat molnet initiering.  För att använda molnet initiering på en RedHat familj OS, måste du skapa en anpassad avbildning med molnet init installerad.

* [Med hjälp av molnet initiering på virtuella Azure Linux-datorer](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quotas"></a>Kvoter
Varje Azure-prenumeration har standard kvotgränser som kan påverka distributionen av ett stort antal virtuella datorer för projektet. Den aktuella gränsen på basis av per prenumeration är 20 virtuella datorer per region.  Kvotgränser kan aktiveras snabbt och genom att arkivera ett supportärende begär en gräns ökar enkelt.  Mer information om kvotgränserna:

* [Tjänstbegränsningarna för Azure-prenumeration](../../azure-subscription-service-limits.md)

## <a name="partners"></a>Partner
Microsoft nära samarbete med våra samarbetspartners så tillgängliga avbildningarna, uppdateras och optimerats för en Azure körning.  Kontrollera marketplace sidorna nedan för mer information om våra partner.

* Linux på Azure - [-godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* SUSE - [Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=%27SUSE%27)
* Redhat - [Azure Marketplace - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Kanoniskt - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Azure Marketplace - Debian 8 ”Jessie”](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD - [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
* Virtuell CoreOS - [Azure Marketplace - virtuell CoreOS (stabil)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [Bitnami bibliotek för Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Mesosphere DC/OS på Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker - [Azure Marketplace - Azure Container Service med Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins - [Azure Marketplace - CloudBees Jenkins plattform](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)

## <a name="getting-started-with-linux-on-azure"></a>Komma igång med Linux på Azure
Om du vill börja använda Azure behöver du ett Azure-konto, Azure CLI installerad och ett par SSH offentliga och privata nycklar.

### <a name="sign-up-for-an-account"></a>Registrera dig för ett konto
Det första steget i med hjälp av Azure-molnet är att registrera dig för ett Azure-konto.  Gå till den [Azure kontoinloggning](https://azure.microsoft.com/pricing/free-trial/) sidan för att komma igång.

### <a name="install-the-cli"></a>Installera CLI
Med ditt nya Azure-konto kan du sätta igång direkt med Azure-portalen, vilket är en webbaserad admin-panel.  Om du vill hantera Azure-molnet via kommandoraden måste du installera den `azure-cli`.  Installera den [Azure CLI 2.0](/cli/azure/install-azure-cli) på arbetsstationen Mac eller Linux.

### <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar
Nu har du ett Azure-konto, Azure web-portalen och Azure CLI.  Nästa steg är att skapa en SSH-nyckel som används till att SSH i Linux utan lösenord.  [Skapa SSH-nycklar för Linux och Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) att aktivera inloggningar och bättre säkerhet.

### <a name="create-a-vm-using-the-cli"></a>Skapa en virtuell dator med CLI
Att skapa en Linux VM med hjälp av CLI är ett snabbt sätt att distribuera en virtuell dator utan att lämna terminalen du arbetar med.  Allt du kan ange på webbportalen är tillgänglig via en kommandorad flagga eller växel.  

* [Skapa en Linux VM med hjälp av CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="create-a-vm-in-the-portal"></a>Skapa en virtuell dator i portalen
Skapa en Linux VM i Azure web-portalen är ett sätt att enkelt peka och klicka dig igenom de olika alternativen för att komma till en distribution.  Istället för att använda kommandoradsverktyget flaggor och växlar, kan du visa en bra Webblayout av olika alternativ och inställningar.  Allt tillgängligt via kommandoradsgränssnittet är också tillgänglig i portalen.

* [Skapa en Linux VM som använder portalen](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="login-using-ssh-without-a-password"></a>Logga in med hjälp av SSH utan lösenord
Den virtuella datorn körs nu på Azure och du är redo att logga in.  Använder lösenord för att logga in via SSH är osäker och tidskrävande.  Använda SSH-nycklar är det säkraste sättet och det snabbaste sättet att logga in.  När du skapar Linux VM via portalen eller CLI, har du två alternativ för autentisering.  Om du väljer ett lösenord för SSH konfigurerar Azure VM för att tillåta inloggningar via lösenord.  Om du väljer att använda en offentlig SSH-nyckel Azure konfigurerar den virtuella datorn så att bara inloggningar via SSH-nycklar och inaktiverar Lösenordsinloggning. Om du vill skydda din Linux VM genom att bara tillåta SSH-nyckel inloggningar alternativet SSH offentlig nyckel när VM skapas i portalen eller CLI.

## <a name="related-azure-components"></a>Relaterade Azure-komponenter
## <a name="storage"></a>Lagring
* [Introduktion till Microsoft Azure Storage](../../storage/common/storage-introduction.md)
* [Lägg till en disk till en Linux-VM med hjälp av azure cli](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Så här kopplar du en datadisk till en Linux-VM i Azure-portalen](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="networking"></a>Nätverk
* [Översikt över virtuella nätverk](../../virtual-network/virtual-networks-overview.md)
* [IP-adresser i Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Öppna portar till en Linux-VM i Azure](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa ett fullständigt kvalificerat domännamn i Azure-portalen](portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="containers"></a>Behållare
* [Virtuella datorer och behållare i Azure](containers.md)
* [Azure Container Service-introduktion](../../container-service/container-service-intro.md)
* [Distribuera ett Azure Container Service-kluster](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>Nästa steg
Nu har du en översikt över Linux på Azure.  Nästa steg är att Sök och skapa ett fåtal virtuella datorer!

* [Utforska våra växande listan över exempel på skript för vanliga uppgifter via AzureCLI](cli-samples.md)
