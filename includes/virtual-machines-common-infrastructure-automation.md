---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 7c1ad2581d025a9259c5cb2a84e4237f938fc892
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81637153"
---
För att skapa och hantera virtuella Azure-datorer (VMs) på ett konsekvent sätt i stor skala, är någon form av automatisering vanligtvis önskas. Det finns många verktyg och lösningar som gör att du kan automatisera den kompletta livscykeln för distribution och hantering av Azure-infrastruktur. Den här artikeln introducerar några av de verktyg för automatisering av infrastruktur som du kan använda i Azure. Dessa verktyg passar ofta in i något av följande metoder:

- Automatisera konfigurationen av virtuella datorer
    - Verktygen omfattar [mallen](#azure-resource-manager-template) [Ansible](#ansible), [Chef](#chef), [Puppet](#puppet)och Azure Resource Manager .
    - Verktyg som är specifika för [vm-anpassning omfattar moln-init](#cloud-init) för virtuella Linux-datorer, [PowerShell Desired State Configuration (DSC)](#powershell-dsc)och [Azure Custom Script Extension](#azure-custom-script-extension) för alla virtuella Azure-datorer.

- Automatisera infrastrukturhantering
    - Verktygen inkluderar [Packer](#packer) för att automatisera anpassade VM-avbildningsversioner och [Terraform](#terraform) för att automatisera infrastrukturbyggprocessen.
    - [Azure Automation](#azure-automation) kan utföra åtgärder i din Azure- och lokala infrastruktur.

- Automatisera programdistribution och leverans
    - Exempel är [Azure DevOps Services](#azure-devops-services) och [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) är en automationsmotor för konfigurationshantering, skapande av virtuella datorer eller programdistribution. Ansible använder en agentlös modell, vanligtvis med SSH-nycklar, för att autentisera och hantera måldatorer. Konfigurationsuppgifter definieras i spelböcker, med ett antal Ansible-moduler tillgängliga för att utföra specifika uppgifter. Mer information finns i [Hur Ansible fungerar](https://www.ansible.com/how-ansible-works).

Lär dig att:

- [Installera och konfigurera Ansible på Linux för användning med Azure](../articles/ansible/ansible-install-configure.md).
- [Skapa en virtuell Linux-dator.](../articles/ansible/ansible-create-vm.md)
- [Hantera en virtuell Linux-dator](../articles/ansible/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) är en automatiseringsplattform som hjälper till att definiera hur infrastrukturen konfigureras, distribueras och hanteras. Ytterligare komponenter inkluderade Chef Habitat för automatisering av programmets livscykel i stället för infrastrukturen, och Chef InSpec som hjälper till att automatisera efterlevnaden av säkerhets- och principkrav. Chef-klienter installeras på måldatorer, med en eller flera centrala chefservrar som lagrar och hanterar konfigurationerna. Mer information finns i [En översikt över Chef](https://docs.chef.io/chef_overview.html).

Lär dig att:

- [Distribuera Chef Automate från Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Installera Chef på Windows och skapa virtuella Azure-datorer](../articles/chef/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) är en företagsklar automationsplattform som hanterar programleverans- och driftsättningsprocessen. Agenter installeras på måldatorer så att Puppet Master kan köra manifest som definierar önskad konfiguration av Azure-infrastrukturen och virtuella datorer. Puppet kan integreras med andra lösningar som Jenkins och GitHub för ett förbättrat arbetsflöde för devops. Mer information finns i [Hur Puppet fungerar](https://puppet.com/products/how-puppet-works).

Lär dig att:

- [Distribuera Puppet från Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Puppet.puppet-agent-windows-asm?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) är ett vanligt sätt att anpassa en virtuell Linux-dator när den startas för första gången. Du kan använda cloud-init till att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom cloud-init anropas under den första startprocessen finns det inga ytterligare steg eller nödvändiga agenter för att tillämpa konfigurationen.  Mer information om hur du `#cloud-config` formaterar filerna korrekt finns [på moln-init-dokumentationswebbplatsen](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config`filer är textfiler som kodas i base64.

Cloud-init fungerar med olika distributioner. Du använder till exempel inte **apt-get install** eller **yum install** när du vill installera ett paket. I stället definierar du en lista med paket att installera. Cloud-init använder automatiskt rätt pakethanteringsverktyg för den distribution du valt.

Vi arbetar aktivt med våra godkända Linux-distributioner för att ha moln-init-aktiverade avbildningar tillgängliga på Azure-marknadsplatsen. Dessa avbildningar gör att dina molninitdistributioner och konfigurationer fungerar sömlöst med virtuella datorer och skalningsuppsättningar för virtuella datorer.
Läs mer om cloud-init på Azure:

- [Molninit-stöd för virtuella Linux-datorer i Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Prova en självstudiekurs om automatisk VM-konfiguration med cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) är en hanteringsplattform för att definiera konfigurationen av måldatorer. DSC kan också användas på Linux via [OPEN Management Infrastructure (OMI) server](https://collaboration.opengroup.org/omi/).

DSC-konfigurationer definierar vad som ska installeras på en dator och hur värden ska konfigureras. En LCM-motor (Local Configuration Manager) körs på varje målnod som bearbetar begärda åtgärder baserat på pushade konfigurationer. En pull-server är en webbtjänst som körs på en central värd för att lagra DSC-konfigurationer och associerade resurser. Pull-servern kommunicerar med LCM-motorn på varje målvärd för att tillhandahålla de konfigurationer och rapport om efterlevnad som krävs.

Lär dig att:

- [Skapa en grundläggande DSC-konfiguration](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [Konfigurera en DSC-pull-server](/powershell/scripting/dsc/pull-server/pullserver).
- [Använd DSC för Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Anpassade Azure-skripttillägg
Azure Custom Script Extension för [Linux](../articles/virtual-machines/linux/extensions-customscript.md) eller [Windows](../articles/virtual-machines/windows/extensions-customscript.md) hämtar och kör skript på virtuella Azure-datorer. Du kan använda tillägget när du skapar en virtuell dator, eller när som helst efter att den virtuella datorn används.

Skript kan hämtas från Azure-lagring eller någon offentlig plats, till exempel en GitHub-databas. Med tillägget Anpassat skript kan du skriva skript på alla språk som körs på källdatorns virtuella dator. Dessa skript kan användas för att installera program eller konfigurera den virtuella datorn som önskas. För att skydda autentiseringsuppgifter kan känslig information som lösenord lagras i en skyddad konfiguration. Dessa autentiseringsuppgifter dekrypteras bara inuti den virtuella datorn.

Lär dig att:

- [Skapa en virtuell Linux-dator med Azure CLI och använd det anpassade skripttillägget](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Skapa en Windows-virtuell dator med Azure PowerShell och använd tillägget Anpassat skript](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packer
[Packer](https://www.packer.io) automatiserar byggprocessen när du skapar en anpassad VM-avbildning i Azure. Du använder Packer för att definiera operativsystemet och köra skript efter konfiguration som anpassar den virtuella datorn för dina specifika behov. När den virtuella datorn har konfigurerats tas den sedan som en hanterad diskavbildning. Packer automatiserar processen för att skapa käll-VM, nätverks- och lagringsresurser, köra konfigurationsskript och sedan skapa vm-avbildningen.

Lär dig att:

- [Använd Packer för att skapa en Linux VM-avbildning i Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Använd Packer för att skapa en Windows VM-avbildning i Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) är ett automatiseringsverktyg som låter dig definiera och skapa en hel Azure-infrastruktur med ett enda mallformatspråk - HashiCorp Configuration Language (HCL). Med Terraform definierar du mallar som automatiserar processen för att skapa nätverks-, lagrings- och VM-resurser för en viss programlösning. Du kan använda dina befintliga Terraform-mallar för andra plattformar med Azure för att säkerställa konsekvens och förenkla infrastrukturdistributionen utan att behöva konvertera till en Azure Resource Manager-mall.

Lär dig att:

- [Installera och konfigurera Terraform med Azure](../articles/terraform/terraform-install-configure.md).
- [Skapa en Azure-infrastruktur med Terraform](../articles/terraform/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) använder runbooks för att bearbeta en uppsättning uppgifter på de virtuella datorer du riktar in dig på. Azure Automation används för att hantera befintliga virtuella datorer i stället för att skapa en infrastruktur. Azure Automation kan köras över både Linux och Windows virtuella datorer, samt lokala virtuella eller fysiska datorer med en hybrid runbook-arbetare. Runbooks kan lagras i en källkontrolldatabas, till exempel GitHub. Dessa runbooks kan sedan köras manuellt eller enligt ett definierat schema.

Azure Automation tillhandahåller också en DSC-tjänst (Desired State Configuration) som gör att du kan skapa definitioner för hur en viss uppsättning virtuella datorer ska konfigureras. DSC säkerställer sedan att den nödvändiga konfigurationen tillämpas och den virtuella datorn förblir konsekvent. Azure Automation DSC körs på både Windows- och Linux-datorer.

Lär dig att:

- [Skapa en PowerShell-runbook](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Använd Hybrid Runbook Worker för att hantera lokala resurser](../articles/automation/automation-hybrid-runbook-worker.md).
- [Använd Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
[Azure DevOps Services](https://www.visualstudio.com/team-services/) är en uppsättning verktyg som hjälper dig att dela och spåra kod, använda automatiserade versioner och skapa en komplett pipeline för kontinuerlig integrering och utveckling (CI/CD). Azure DevOps Services integreras med Visual Studio och andra redigerare för att förenkla användningen. Azure DevOps Services kan också skapa och konfigurera Virtuella Azure-datorer och sedan distribuera kod till dem.

Läs mer om:

- [Azure DevOps-tjänster](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://jenkins.io) är en kontinuerlig integrationsserver som hjälper till att distribuera och testa program och skapa automatiserade pipelines för kodleverans. Det finns hundratals plugins för att utöka kärnan Jenkins plattform, och du kan också integrera med många andra produkter och lösningar via webhooks. Du kan manuellt installera Jenkins på en Virtuell Azure-dator, köra Jenkins från en Docker-behållare eller använda en förbyggd Azure Marketplace-avbildning.

Lär dig att:

- [Skapa en utvecklingsinfrastruktur på en Virtuell Linux-dator i Azure med Jenkins, GitHub och Docker](../articles/jenkins/tutorial-jenkins-github-docker-cicd.md).


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
[Azure Resource Manager](../articles/azure-resource-manager/templates/overview.md) är distributions- och hanteringstjänsten för Azure. Det ger ett hanteringslager som gör att du kan skapa, uppdatera och ta bort resurser i din Azure-prenumeration. Du använder hanteringsfunktioner, till exempel åtkomstkontroll, lås och taggar, för att skydda och organisera dina resurser efter distributionen.

Lär dig att:

- [Distribuera spot-virtuella datorer med hjälp av en Resource Manager-mall](../articles/virtual-machines/linux/spot-template.md).
- [Distribuera en virtuell Azure-dator med C#och en Resource Manager-mall](../articles/virtual-machines/windows/csharp-template.md).
- [Skapa en virtuell Windows-dator från en Resource Manager-mall](../articles/virtual-machines/windows/ps-template.md).
- [Hämta mallen för en virtuell dator](../articles/virtual-machines/windows/download-template.md).
- [Skapa en Azure Image Builder-mall](../articles/virtual-machines/linux/image-builder-json.md).

## <a name="next-steps"></a>Nästa steg
Det finns många olika alternativ för att använda infrastrukturautomationsverktyg i Azure. Du har friheten att använda den lösning som bäst passar dina behov och din miljö. För att komma igång och prova några av de verktyg som är inbyggda i Azure, se hur du automatiserar anpassningen av en [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) eller [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) VM.
