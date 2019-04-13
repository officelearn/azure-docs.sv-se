---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 81bde837cd78646f1fc59d921246c72978ecb840
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551628"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Använd verktyg för automatisering av infrastruktur med virtuella datorer i Azure
Om du vill skapa och hantera virtuella Azure-datorer (VM) på ett konsekvent sätt i stor skala, önskas vanligtvis någon form av automatisering. Det finns många verktyg och lösningar som gör det möjligt att automatisera distributionen av hela Azure-infrastrukturen och livscykeln för hantering. Den här artikeln presenteras några av de verktyg för automatisering av infrastruktur som du kan använda i Azure. Dessa verktyg anpassa ofta till någon av följande metoder:

- Automatisera konfigurationen av virtuella datorer
    - Verktygen innehåller [Ansible](#ansible), [Chef](#chef), och [Puppet](#puppet).
    - Verktygen som är specifika för VM-anpassning innehåller [cloud-init](#cloud-init) för virtuella Linux-datorer [PowerShell Desired State Configuration (DSC)](#powershell-dsc), och [Azure-tillägget för anpassat skript](#azure-custom-script-extension) för alla Azure virtuella datorer.
 
- Automatisera hantering av infrastruktur
    - Verktygen innehåller [Packer](#packer) för att automatisera anpassad virtuell dator skapar avbildningen, och [Terraform](#terraform) för att automatisera infrastrukturen genereringsprocessen.
    - [Azure Automation](#azure-automation) kan utföra åtgärder i hela Azure och den lokala infrastrukturen.

- Automatisera distribution av program och leverans
    - Exempel är [Azure DevOps-tjänsterna](#azure-devops-services) och [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) är en automatiseringsmotor för konfigurationshantering, skapa en virtuell dator eller programdistribution. Ansible använder en modell utan agent, vanligtvis med SSH-nycklar för att autentisera och hantera måldatorer. Konfigurationsuppgifter definieras i strategiböcker med ett antal Ansible-moduler som är tillgängliga för att utföra specifika uppgifter. Mer information finns i [hur Ansible fungerar](https://www.ansible.com/how-ansible-works).

Lär dig att:

- [Installera och konfigurera Ansible på Linux för användning med Azure](../articles/virtual-machines/linux/ansible-install-configure.md).
- [Skapa en Linux-dator](../articles/virtual-machines/linux/ansible-create-vm.md).
- [Hantera en Linux-dator](../articles/virtual-machines/linux/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) är en plattform för automatisering som hjälper dig att definiera hur infrastrukturen konfigureras, distribueras och hanteras. Ytterligare komponenter med Chef, Habitat för application lifecycle automation i stället för infrastrukturen och Chef InSpec som hjälper dig att automatisera efterlevnad med säkerhet och kraven för lösenordsprincipen. Chef-klienter är installerade på måldatorer, med en eller flera centrala Chef servrar som kan lagra och hantera konfigurationerna. Mer information finns i [en översikt över Chef](https://docs.chef.io/chef_overview.html).

Lär dig att:

- [Distribuera Chef automatisera från Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Installera Chef på Windows och skapa virtuella Azure-datorer](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) är en klart för företag som hanterar programprocessen leverans och distribution. Agenter är installerade på måldatorerna så att Puppet originalet, så kör manifest som definierar den önskade konfigurationen för Azure-infrastrukturen och virtuella datorer. Puppet kan integrera med andra lösningar som Jenkins och GitHub för en förbättrad devops-arbetsflöde. Mer information finns i [hur Puppet fungerar](https://puppet.com/product/how-puppet-works).

Lär dig att:

- [Distribuera Puppet från Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) är ett vanligt sätt att anpassa en virtuell Linux-dator när den startas för första gången. Du kan använda cloud-init till att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom cloud-init anropas under den ursprungliga startprocessen, finns det inga fler steg eller obligatoriska agenter att tillämpa för konfigurationen.  Mer information om hur du formaterar korrekt din `#cloud-config` filer, finns i den [cloud-init dokumentationswebbplats](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` är i base64-kodade textfiler.

Cloud-init fungerar med olika distributioner. Du använder till exempel inte **apt-get install** eller **yum install** när du vill installera ett paket. I stället definierar du en lista med paket att installera. Cloud-init använder automatiskt rätt pakethanteringsverktyg för den distribution du valt.

Vi arbetar aktivt med våra godkända Linux-distribution partner för att få cloud-init aktiverat-avbildningarna i Azure marketplace. Dessa avbildningar gör dina cloud-init-distributioner och konfigurationer fungerar sömlöst med virtuella datorer och VM-skalningsuppsättningar. Lär dig mer om cloud-init på Azure:

- [Cloud-init-stöd för Linux-datorer i Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Prova en självstudie om automatisk konfiguration av virtuell dator med cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell Desired State Configuration (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) är en hanteringsplattform för att definiera konfigurationen av måldatorer. DSC kan även användas på Linux via den [Open Management Infrastructure (OMI) server](https://collaboration.opengroup.org/omi/).

DSC-konfigurationer definierar vad som ska installeras på en dator och hur du konfigurerar värden. En motor för lokala Configuration Manager (LCM) körs på varje målnoden som bearbetar begärda åtgärder baserat på pushade konfigurationer. En pull-server är en webbtjänst som körs på en central värd att lagra DSC-konfigurationer och associerade resurser. Pull-servern kommunicerar med LCM-motorn på varje målvärden för att tillhandahålla konfigurationerna som krävs och rapportera om efterlevnad.

Lär dig att:

- [Skapa en grundläggande DSC-konfiguration](https://msdn.microsoft.com/powershell/dsc/quickstarts/website-quickstart).
- [Konfigurera en DSC-hämtningsserver](https://msdn.microsoft.com/powershell/dsc/pullserver).
- [Använda DSC för Linux](https://msdn.microsoft.com/powershell/dsc/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Azure anpassat skripttillägg
Tillägget för anpassat skript Azure för [Linux](../articles/virtual-machines/linux/extensions-customscript.md) eller [Windows](../articles/virtual-machines/windows/extensions-customscript.md) hämtar och kör skript på virtuella Azure-datorer. Du kan använda tillägget när du skapar en virtuell dator eller som helst efter den virtuella datorn är i användning. 

Skripten kan laddas ned från Azure storage eller valfri offentlig plats, till exempel en GitHub-lagringsplats. Du kan använda det anpassade Skripttillägget för att skriva skript på valfritt språk som körs på den Virtuella källdatorn. Skripten kan användas för att installera program eller konfigurera den virtuella datorn efter behov. Om du vill skydda autentiseringsuppgifter kan känslig information, till exempel lösenord lagras i en skyddad konfiguration. Autentiseringsuppgifterna dekrypteras bara på den virtuella datorn.

Lär dig att:

- [Skapa en Linux-VM med Azure CLI och använda tillägget för anpassat skript](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Skapa en virtuell Windows-dator med Azure PowerShell och använda tillägget för anpassat skript](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packer
[Packer](https://www.packer.io) automatiserar build-processen när du skapar en anpassad virtuell datoravbildning i Azure. Du kan använda Packer för att definiera Operativsystemet och kör efter konfigurationsskript för att anpassa den virtuella datorn efter dina specifika behov. När du konfigurerat avbildas den virtuella datorn sedan som en avbildning av en hanterad Disk. Packer automatiserar processen för att skapa källan VM, nätverk och lagringsresurser, kör konfigurationsskript och sedan skapa VM-avbildning.

Lär dig att:

- [Använd Packer för att skapa en Linux VM-avbildning i Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Använd Packer för att skapa en Windows VM-avbildning i Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) är ett automatiseringsverktyg som gör det möjligt att definiera och skapa en hel Azure-infrastruktur med en enda mallspråk - HashiCorp Configuration språk (HCL). Med Terraform definierar du mallar som automatiserar processen med att skapa nätverk, lagring och Virtuella resurser för en viss lösning. Du kan använda dina befintliga Terraform-mallar för andra plattformar med Azure för att säkerställa konsekvens och förenkla distribution av infrastruktur utan att behöva konvertera till en Azure Resource Manager-mall.

Lär dig att:

- [Installera och konfigurera Terraform med Azure](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Skapa en Azure-infrastruktur med Terraform](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) använder runbooks för att bearbeta en uppsättning uppgifter på de virtuella datorerna som du riktar. Azure Automation används för att hantera befintliga virtuella datorer i stället för att skapa en infrastruktur. Azure Automation kan köras både Linux och Windows-datorer, samt lokala virtuella eller fysiska datorer med en hybrid runbook worker. Runbooks kan lagras i ett källkontrollscentrallager som GitHub. Dessa runbooks kan sedan köra manuellt eller enligt ett definierat schema.

Azure Automation tillhandahåller också en Desired State Configuration (DSC)-tjänst som låter dig skapa definitioner för hur en viss uppsättning virtuella datorer ska konfigureras. DSC ser till att den obligatoriska konfigurationen tillämpas och den virtuella datorn förblir konsekventa. Azure Automation DSC körs på både Windows och Linux-datorer.

Lär dig att:

- [Skapa en PowerShell-runbook](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Använda Hybrid Runbook Worker för att hantera lokala resurser](../articles/automation/automation-hybrid-runbook-worker.md).
- [Använd Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
[Azure DevOps-tjänster](https://www.visualstudio.com/team-services/) är en uppsättning med verktyg som hjälper dig att dela och spåra code, Använd automatiserade versioner och skapa en fullständig kontinuerlig integrering och utveckling (CI/CD) pipeline. Azure DevOps-tjänster som integreras med Visual Studio och andra redigerare för att förenkla användningen. Azure DevOps-tjänster kan också skapa och konfigurera virtuella Azure-datorer och distribuera kod till dem.

Läs mer om:

- [Azure DevOps-tjänsterna](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) är en kontinuerlig integrering-server som hjälper dig att distribuera och testa program och skapa automatiska pipelines för leverans av kod. Det finns hundratals plugin-program som utökar Jenkins-kärnplattformen och du kan också integreras med många andra produkter och lösningar via webhooks. Du kan manuellt installera Jenkins på en Azure VM, Jenkins från att köras under en Docker-behållare eller använda en färdig Azure Marketplace-avbildning.

Lär dig att:

- [Skapa en infrastruktur för utveckling på en Linux-VM i Azure med Jenkins, GitHub och Docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Nästa steg
Det finns många olika alternativ för att använda verktyg för automatisering av infrastruktur i Azure. Har du friheten att använda den lösning som bäst passar dina behov och miljö. Om du vill komma igång och prova några av verktyg som är inbyggda i Azure, se hur du automatiserar anpassning av en [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) eller [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) VM.
