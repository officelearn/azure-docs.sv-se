---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 9cbc48d8bca2f7491d0464be1c5bd64054927dc9
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77608719"
---
Om du vill skapa och hantera virtuella Azure-datorer (VM) på ett konsekvent sätt i stor skala, önskas vanligtvis någon form av automatisering. Det finns många verktyg och lösningar som gör det möjligt att automatisera distributionen av hela Azure-infrastrukturen och livscykeln för hantering. Den här artikeln presenteras några av de verktyg för automatisering av infrastruktur som du kan använda i Azure. Dessa verktyg anpassa ofta till någon av följande metoder:

- Automatisera konfigurationen av virtuella datorer
    - Verktygen är [Ansible](#ansible), [chef](#chef)och [Puppet](#puppet).
    - Verktyg som är speciella för anpassning av virtuella datorer är [Cloud-Init](#cloud-init) för virtuella Linux-datorer, PowerShell-tjänsten för [önskad tillstånds konfiguration (DSC)](#powershell-dsc)och [tillägget för Azures anpassade skript](#azure-custom-script-extension) för alla virtuella Azure-datorer
 
- Automatisera hantering av infrastruktur
    - Verktygen inkluderar [Packer](#packer) för att automatisera anpassade versioner av VM-avbildningar och [terraform](#terraform) för att automatisera skapande processen för infrastrukturen.
    - [Azure Automation](#azure-automation) kan utföra åtgärder i din Azure och lokala infrastruktur.

- Automatisera distribution av program och leverans
    - Exempel är [Azure DevOps Services](#azure-devops-services) och [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) är en automatiserings motor för konfigurations hantering, skapande av virtuella datorer eller program distribution. Ansible använder en modell utan agent, vanligtvis med SSH-nycklar för att autentisera och hantera måldatorer. Konfigurationsuppgifter definieras i strategiböcker med ett antal Ansible-moduler som är tillgängliga för att utföra specifika uppgifter. Mer information finns i [så här fungerar Ansible](https://www.ansible.com/how-ansible-works).

Lär dig att:

- [Installera och konfigurera Ansible i Linux för användning med Azure](../articles/ansible/ansible-install-configure.md).
- [Skapa en virtuell Linux-dator](../articles/ansible/ansible-create-vm.md).
- [Hantera en virtuell Linux-dator](../articles/ansible/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) är en automatiserings plattform som hjälper dig att definiera hur din infrastruktur konfigureras, distribueras och hanteras. Ytterligare komponenter med Chef, Habitat för application lifecycle automation i stället för infrastrukturen och Chef InSpec som hjälper dig att automatisera efterlevnad med säkerhet och kraven för lösenordsprincipen. Chef-klienter är installerade på måldatorer, med en eller flera centrala Chef servrar som kan lagra och hantera konfigurationerna. Mer information finns i [Översikt över chef](https://docs.chef.io/chef_overview.html).

Lär dig att:

- [Distribuera chef automatiserat från Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Installera chef på Windows och skapa virtuella Azure-datorer](../articles/chef/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) är en företags färdig automatiserings plattform som hanterar programmets leverans-och distributions process. Agenter är installerade på måldatorerna så att Puppet originalet, så kör manifest som definierar den önskade konfigurationen för Azure-infrastrukturen och virtuella datorer. Puppet kan integrera med andra lösningar som Jenkins och GitHub för en förbättrad devops-arbetsflöde. Mer information finns i [så här fungerar Puppet](https://puppet.com/products/how-puppet-works).

Lär dig att:

- [Distribuera Puppet från Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Puppet.puppet-agent-windows-asm?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) är ett vanligt sätt att anpassa en virtuell Linux-dator när den startas för första gången. Du kan använda cloud-init till att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom cloud-init anropas under den ursprungliga startprocessen, finns det inga fler steg eller obligatoriska agenter att tillämpa för konfigurationen.  Mer information om hur du formaterar `#cloud-config`-filer korrekt finns på [webbplatsen för Cloud-Init-dokumentation](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config`-filer är textfiler kodade i base64.

Cloud-init fungerar med olika distributioner. Du använder till exempel inte **apt-get install** eller **yum install** när du vill installera ett paket. I stället definierar du en lista med paket att installera. Cloud-init använder automatiskt rätt pakethanteringsverktyg för den distribution du valt.

Vi arbetar aktivt med våra godkända Linux-distribution partner för att få cloud-init aktiverat-avbildningarna i Azure marketplace. Dessa avbildningar gör dina cloud-init-distributioner och konfigurationer fungerar sömlöst med virtuella datorer och VM-skalningsuppsättningar. Lär dig mer om cloud-init på Azure:

- [Cloud-Init-stöd för virtuella Linux-datorer i Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Prova en själv studie kurs om automatiserad VM-konfiguration med Cloud-Init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) är en hanterings plattform för att definiera konfigurationen för mål datorer. DSC kan också användas i Linux via [Open Management Infrastructure (OMI)-servern](https://collaboration.opengroup.org/omi/).

DSC-konfigurationer definierar vad som ska installeras på en dator och hur du konfigurerar värden. En motor för lokala Configuration Manager (LCM) körs på varje målnoden som bearbetar begärda åtgärder baserat på pushade konfigurationer. En pull-server är en webbtjänst som körs på en central värd att lagra DSC-konfigurationer och associerade resurser. Pull-servern kommunicerar med LCM-motorn på varje målvärden för att tillhandahålla konfigurationerna som krävs och rapportera om efterlevnad.

Lär dig att:

- [Skapa en grundläggande DSC-konfiguration](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [Konfigurera en DSC-pull-server](/powershell/scripting/dsc/pull-server/pullserver).
- [Använd DSC för Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Azure anpassat skripttillägg
Det anpassade skript tillägget i Azure för [Linux](../articles/virtual-machines/linux/extensions-customscript.md) eller [Windows](../articles/virtual-machines/windows/extensions-customscript.md) laddar ned och kör skript på virtuella Azure-datorer. Du kan använda tillägget när du skapar en virtuell dator eller som helst efter den virtuella datorn är i användning. 

Skripten kan laddas ned från Azure storage eller valfri offentlig plats, till exempel en GitHub-lagringsplats. Du kan använda det anpassade Skripttillägget för att skriva skript på valfritt språk som körs på den Virtuella källdatorn. Skripten kan användas för att installera program eller konfigurera den virtuella datorn efter behov. Om du vill skydda autentiseringsuppgifter kan känslig information, till exempel lösenord lagras i en skyddad konfiguration. Autentiseringsuppgifterna dekrypteras bara på den virtuella datorn.

Lär dig att:

- [Skapa en virtuell Linux-dator med Azure CLI och Använd det anpassade skript tillägget](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Skapa en virtuell Windows-dator med Azure PowerShell och Använd det anpassade skript tillägget](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packer
[Packer](https://www.packer.io) automatiserar Bygg processen när du skapar en anpassad VM-avbildning i Azure. Du kan använda Packer för att definiera Operativsystemet och kör efter konfigurationsskript för att anpassa den virtuella datorn efter dina specifika behov. När du konfigurerat avbildas den virtuella datorn sedan som en avbildning av en hanterad Disk. Packer automatiserar processen för att skapa källan VM, nätverk och lagringsresurser, kör konfigurationsskript och sedan skapa VM-avbildning.

Lär dig att:

- [Använd Packer för att skapa en virtuell Linux-avbildning i Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Använd Packer för att skapa en Windows VM-avbildning i Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) är ett automatiserings verktyg som gör att du kan definiera och skapa en hel Azure-infrastruktur med ett enda mall format språk – HashiCorp-konfigurations språket (HCL). Med Terraform definierar du mallar som automatiserar processen med att skapa nätverk, lagring och Virtuella resurser för en viss lösning. Du kan använda dina befintliga Terraform-mallar för andra plattformar med Azure för att säkerställa konsekvens och förenkla distribution av infrastruktur utan att behöva konvertera till en Azure Resource Manager-mall.

Lär dig att:

- [Installera och konfigurera terraform med Azure](../articles/terraform/terraform-install-configure.md).
- [Skapa en Azure-infrastruktur med terraform](../articles/terraform/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) använder Runbooks för att bearbeta en uppsättning aktiviteter på de virtuella datorer som du är mål för. Azure Automation används för att hantera befintliga virtuella datorer i stället för att skapa en infrastruktur. Azure Automation kan köras både Linux och Windows-datorer, samt lokala virtuella eller fysiska datorer med en hybrid runbook worker. Runbooks kan lagras i ett källkontrollscentrallager som GitHub. Dessa runbooks kan sedan köra manuellt eller enligt ett definierat schema.

Azure Automation tillhandahåller också en Desired State Configuration (DSC)-tjänst som låter dig skapa definitioner för hur en viss uppsättning virtuella datorer ska konfigureras. DSC ser till att den obligatoriska konfigurationen tillämpas och den virtuella datorn förblir konsekventa. Azure Automation DSC körs på både Windows och Linux-datorer.

Lär dig att:

- [Skapa en PowerShell-Runbook](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Använd hybrid Runbook Worker för att hantera lokala resurser](../articles/automation/automation-hybrid-runbook-worker.md).
- [Använd Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
[Azure DevOps Services](https://www.visualstudio.com/team-services/) är en uppsättning verktyg som hjälper dig att dela och spåra kod, använda automatiserade versioner och skapa en komplett pipeline för kontinuerlig integrering och utveckling (CI/CD). Azure DevOps-tjänster som integreras med Visual Studio och andra redigerare för att förenkla användningen. Azure DevOps-tjänster kan också skapa och konfigurera virtuella Azure-datorer och distribuera kod till dem.

Läs mer om:

- [Azure DevOps-tjänster](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) är en kontinuerlig integrations server som hjälper till att distribuera och testa program och skapa automatiserade pipeliner för kod leverans. Det finns hundratals plugin-program som utökar Jenkins-kärnplattformen och du kan också integreras med många andra produkter och lösningar via webhooks. Du kan manuellt installera Jenkins på en Azure VM, Jenkins från att köras under en Docker-behållare eller använda en färdig Azure Marketplace-avbildning.

Lär dig att:

- [Skapa en utvecklings infrastruktur på en virtuell Linux-dator i Azure med Jenkins, GitHub och Docker](../articles/jenkins/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Nästa steg
Det finns många olika alternativ för att använda verktyg för automatisering av infrastruktur i Azure. Har du friheten att använda den lösning som bäst passar dina behov och miljö. Om du vill komma igång och testa några av de verktyg som är inbyggda i Azure läser du så här automatiserar du anpassningen av en [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) -eller [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) -VM.
