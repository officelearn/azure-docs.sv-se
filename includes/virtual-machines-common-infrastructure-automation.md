# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Använd verktyg för automatisering av infrastruktur med virtuella datorer i Azure
Om du vill skapa och hantera virtuella Azure-datorer (VM) på ett konsekvent sätt i skala, önskade vanligtvis någon form av automatisering. Det finns många verktyg och lösningar som gör det möjligt att automatisera distributionen av fullständig Azure-infrastrukturen och livscykeln för hantering. Den här artikeln introducerar några av de infrastruktur automation-verktyg som du kan använda i Azure. Dessa verktyg passa ofta någon av följande metoder:

- Automatisera konfigurationen av virtuella datorer
    - Verktygen innehåller [Ansible](#ansible), [Chef](#chef), och [Puppet](#puppet).
    - Innehåller verktyg som är specifika för VM-anpassning [moln init](#cloud-init) för Linux virtuella datorer kan [PowerShell önskad tillstånd Configuration (DSC)](#powershell-dsc), och [Azure-tillägget för anpassat skript](#azure-custom-script-extension) för alla Virtuella Azure-datorer.
 
- Automatisera infrastrukturhantering av
    - Verktygen innehåller [förpackaren](#packer) för att automatisera anpassade VM bygger avbildningen, och [Terraform](#terraform) för att automatisera infrastrukturen för att skapa processen.
    - [Azure Automation](#azure-automation) kan utföra åtgärder för hela infrastrukturen Azure och lokalt.

- Automatisera distribution och leverans
    - Exempel inkluderar [Visual Studio Team Services](#visual-studio-team-services) och [Jenkins](#jenkins).


## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) är en automatiseringsmotor för konfigurationshantering, skapa en virtuell dator eller programdistribution. Ansible använder en agentfri modell vanligtvis med SSH-nycklar för att autentisera och hantera måldatorer. Konfigurationsuppgifter definieras i playbooks med ett antal Ansible moduler som är tillgängliga för att utföra specifika åtgärder. Mer information finns i [hur Ansible fungerar](https://www.ansible.com/how-ansible-works).

Lär dig att:

- [Installera och konfigurera Ansible på Linux för användning med Azure](../articles/virtual-machines/linux/ansible-install-configure.md).
- [Skapa en grundläggande VM](../articles/virtual-machines/linux/ansible-create-vm.md).
- [Skapa en fullständig VM-miljö inklusive stöder resurser](../articles/virtual-machines/linux/ansible-create-complete-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) är en automatiseringsplattform som bidrar till att definiera hur infrastrukturen är konfigurerad, distribueras och hanteras. Ytterligare komponenter med Chef livsmiljö för programmet livscykel automation i stället för infrastrukturen och Chef InSpec som hjälper till att automatisera kompatibilitet med säkerhet och -kraven. Chef klienter installeras på måldatorerna med en eller flera centrala Chef servrar som kan lagra och hantera konfigurationerna. Mer information finns i [en översikt över Chef](https://docs.chef.io/chef_overview.html).

Lär dig att:

- [Distribuera Chef automatisera från Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Installera Chef på Windows och skapa virtuella datorer i Azure](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) är en automatiseringsplattform för enterprise-redo som hanterar programprocessen leverans och distribution. Agenter är installerade på måldatorerna så att Puppet Master att köra manifest som definierar du önskad konfiguration av Azure-infrastrukturen och virtuella datorer. Puppet kan integreras med andra lösningar, till exempel Jenkins och GitHub för en förbättrad devops-arbetsflöde. Mer information finns i [hur Puppet fungerar](https://puppet.com/product/how-puppet-works).

Lär dig att:

- [Distribuera Puppet från Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2016-1?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) är ett vanligt sätt att anpassa en virtuell Linux-dator när den startas för första gången. Du kan använda cloud-init till att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom molnet init anropas under den ursprungliga startprocessen, finns det inga ytterligare steg eller nödvändiga agenter att tillämpa konfigurationen.  Mer information om hur du formaterar korrekt din `#cloud-config` filer finns i [molnet init dokumentationsplatsen](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` är textfiler i base64-kodade.

Cloud-init fungerar med olika distributioner. Du använder till exempel inte **apt-get install** eller **yum install** när du vill installera ett paket. I stället definierar du en lista med paket att installera. Cloud-init använder automatiskt rätt pakethanteringsverktyg för den distribution du valt.

 Vi arbetar aktivt med våra påtecknade Linux distro partners för att molnet init aktiverat bilder som finns i Azure marketplace. Dessa avbildningar gör dina molntjänster init-distributioner och konfigurationer fungerar sömlöst med virtuella datorer och virtuella datorer. I följande tabell beskrivs aktuella molnet init aktiverat bilder tillgängligheten på Azure-plattformen:

| Utgivare | Erbjudande | SKU | Version | redo för molnet initiering
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |16.04-LTS |senaste |ja | 
|Canonical |UbuntuServer |14.04.5-LTS |senaste |ja |
|CoreOS |CoreOS |Stable |senaste |ja |
|OpenLogic |CentOS |7-CI |senaste |förhandsgranskning |
|Redhat |RHEL |7-RAW-CI |senaste |förhandsgranskning |

Lär dig mer information om molnet initiering på Azure:

- [Molnet init stöd för Linux virtuella datorer i Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Försök en självstudiekurs om automatisk VM-konfigurationen med hjälp av molnet init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell önskad tillstånd Configuration (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) är en plattform för att definiera konfigurationen av måldatorerna. DSC kan även användas på Linux via den [Open Management Infrastructure (OMI) server](https://collaboration.opengroup.org/omi/).

DSC-konfigurationer definierar vad som ska installeras på en dator och hur du konfigurerar värden. Motorn för lokala Configuration Manager (MGM) körs på varje målnoden som bearbetar begärda åtgärder baserat på intryckt konfigurationer. En pull-server är en webbtjänst som körs på en central värd för lagring av DSC-konfigurationer och associerade resurser. Pull-servern kommunicerar med MGM-motorn på varje målvärden för att ange konfigurationerna som krävs och rapportera om kompatibiliteten.

Lär dig att:

- [Skapa en grundläggande DSC-konfiguration](https://msdn.microsoft.com/powershell/dsc/quickstart).
- [Konfigurera en server för DSC-pull](https://msdn.microsoft.com/powershell/dsc/pullserver).
- [Använd DSC för Linux](https://msdn.microsoft.com/powershell/dsc/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Tillägget för anpassat skript för Azure
Den Azure tillägget för anpassat skript för [Linux](../articles/virtual-machines/linux/extensions-customscript.md) eller [Windows](../articles/virtual-machines/windows/extensions-customscript.md) hämtar och kör skript på Azure Virtual Machines. Du kan använda tillägget när du skapar en virtuell dator eller när den virtuella datorn är i användning. 

Skript kan laddas ned från Azure storage eller någon offentlig plats, till exempel en GitHub-databas. Du kan använda tillägget för anpassat skript, för att skriva skript på alla språk som körs på den Virtuella källdatorn. Dessa skript kan användas för att installera program eller konfigurera den virtuella datorn efter behov. Om du vill skydda autentiseringsuppgifter kan känslig information, till exempel lösenord lagras i en skyddad konfiguration. Dessa autentiseringsuppgifter dekrypteras endast inuti den virtuella datorn.

Lär dig att:

- [Skapa en Linux VM med Azure CLI och använda tillägget för anpassat skript](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Skapa en virtuell Windows-dator med Azure PowerShell och använda tillägget för anpassat skript](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packare
[Packare](https://www.packer.io) automatiserar skapar när du skapar en anpassad VM-avbildning i Azure. Du kan använda förpackaren för att definiera OS och kör efter konfigurationsskript för att anpassa den virtuella datorn för dina specifika behov. När du konfigurerat avbildas den virtuella datorn sedan som en hanterad diskavbildning. Packare automatiserar processen att skapa källan VM, nätverk och lagringsresurser, kör konfigurationsskript och sedan skapa VM-avbildning.

Lär dig att:

- [Använd förpackaren för att skapa en Linux-VM-avbildning i Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Använd förpackaren för att skapa en Windows VM-avbildning i Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) är ett automatiseringsverktyg som hjälper dig att definiera och skapa en hel Azure-infrastruktur med en enda mallspråk - HashiCorp Configuration språk (Kompatibilitetslistan). Med Terraform definiera mallar som automatiserar processen för att skapa nätverk, lagring och Virtuella resurser för en viss lösning. Du kan använda dina befintliga Terraform mallar för andra plattformar med Azure för att säkerställa konsekvens och förenkla distribution av infrastruktur utan att behöva konvertera till en Azure Resource Manager-mall.

Lär dig att:

- [Installera och konfigurera Terraform med Azure](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Skapa en Azure-infrastruktur med Terraform](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) använder runbooks för att bearbeta en uppsättning uppgifter på virtuella datorer du anger som mål. Azure Automation används för att hantera befintliga virtuella datorer i stället för att skapa en infrastruktur. Azure Automation kan köra över både Linux och Windows-datorer, samt lokala virtuella eller fysiska datorer med en hybrid runbook worker. Runbooks kan lagras i en källkontroll, till exempel GitHub. Dessa runbooks kan sedan köra manuellt eller på ett definierat schema.

Azure Automation innehåller också en önskad tillstånd Configuration DSC ()-tjänst som låter dig skapa definitioner för hur en given uppsättning virtuella datorer ska konfigureras. DSC ser till att den nödvändiga konfigurationen tillämpas och den virtuella datorn är konsekvent. Azure Automation DSC körs på Windows- och Linux-datorer.

Lär dig att:

- [Skapa en PowerShell-runbook](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Använda Hybrid Runbook Worker för att hantera lokala resurser](../articles/automation/automation-hybrid-runbook-worker.md).
- [Använd Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="visual-studio-team-services"></a>Visual Studio Team Services
[Team Services](https://www.visualstudio.com/team-services/) är en uppsättning verktyg som hjälper dig att dela och spåra code, använder automatisk versioner och skapa en fullständig kontinuerlig integration och utveckling (CI/CD) pipeline. Team Services integreras med Visual Studio och andra redigerare för att förenkla användningen. Team Services kan också skapa och konfigurera virtuella Azure-datorer och distribuera kod till dem.

Lär dig att:

- [Skapa en pipeline för kontinuerlig integration med Team Services](../articles/virtual-machines/windows/tutorial-vsts-iis-cicd.md).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) är en kontinuerlig integration server som hjälper dig att distribuera och testa program och skapa automatisk pipelines för leverans av koden. Det finns hundratals plugin-program för att utöka Jenkins kärnplattformen och du kan också integrera med många andra produkter och lösningar via webhooks. Du kan manuellt installera Jenkins på en Azure VM, Jenkins från att köras under en dockerbehållare eller Använd en förskapad Azure Marketplace-avbildning.

Lär dig att:

- [Skapa en infrastruktur för utveckling på en Linux-VM i Azure med Jenkins, GitHub och Docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Nästa steg
Det finns många alternativ att använda verktyg för automatisering av infrastrukturen i Azure. Har du friheten att använda den lösning som bäst passar dina behov och miljö. Om du vill komma igång och prova några av verktyg som är inbyggda i Azure finns automatisera anpassning av en [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) eller [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) VM.
