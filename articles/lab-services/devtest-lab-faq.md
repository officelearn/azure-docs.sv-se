---
title: Azure DevTest Labs vanliga frågor och svar | Microsoft Docs
description: Hitta svar på vanliga frågor om Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: a295cad2bf1cafce4dc64909174e9417daa7918e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38235456"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs vanliga frågor och svar
Få svar på några av de vanligaste frågorna om Azure DevTest Labs.

**Allmänt**
## <a name="what-if-my-question-isnt-answered-here"></a>Vad händer om min fråga inte besvaras här?
Om din fråga inte listas här, berätta för oss, så att vi kan hjälpa dig hitta något svar.

* Ställa en fråga i slutet av den här vanliga frågor och svar. Kommunicera med Azure Cache-teamet och andra community-medlemmar om den här artikeln.
* För att nå en bredare publik, ställa en fråga på den [Azure DevTest Labs MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Kommunicera med Azure DevTest Labs-teamet och andra medlemmar i communityn.
* Skicka in dina förfrågningar och idéer för funktionsförfrågningar om [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

## <a name="why-should-i-use-azure-devtest-labs"></a>Varför ska jag använda Azure DevTest Labs?
Azure DevTest Labs kan spara ditt team tid och pengar. Utvecklare kan skapa egna miljöer med flera olika baser. De kan också använda artefakter för att snabbt distribuera och konfigurera program. Genom att använda anpassade bilder och formler kan du spara virtuella datorer (VM) som en mall och återskapa dem enkelt i teamet. DevTest Labs erbjuder även flera principer som kan konfigureras att labb som administratörer kan använda för att minska avfall och hantera ett team-miljöer. Dessa principer omfattar automatisk avstängning, kostnad tröskelvärdet maximala virtuella datorer per användare och maximal storlek för virtuell dator. En mer detaljerad förklaring av DevTest Labs finns i den [översikt](devtest-lab-overview.md) eller [introduktionsvideon](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

## <a name="what-does-worry-free-self-service-mean"></a>Vad ”problemfri självbetjäning” betyder?
Problemfri självbetjäning innebär att utvecklare och testare skapa egna miljöer efter behov. Administratörer har säkerheten för att känna till att DevTest Labs kan du minimera avfall och kontrollera kostnaderna. Administratörer kan ange vilka VM-storlekar är tillåtna, det maximala antalet virtuella datorer, och när virtuella datorer är igång och stänga av. DevTest Labs gör det också enkelt att övervaka kostnader och Ställ in aviseringar som hjälper dig vara medveten om hur labbresurser används.

## <a name="how-can-i-use-devtest-labs"></a>Hur kan jag använda DevTest Labs?
DevTest Labs är användbart när som helst krävs utveckling eller testmiljöer och vill du återskapa dem snabbt, eller hantera dem med hjälp av kostnadsbesparande principer.

Här följer några scenarier som våra kunder använder labb för:

* Hantera utvecklings- och testmiljöer på samma ställe. Använd principer för att minska kostnaderna och skapa anpassade avbildningar för att dela bygger i teamet.
* Utveckla ett program genom att använda anpassade avbildningar för att spara diskstatusen under hela utvecklingen faser.
* Spåra kostnad i förhållande till pågår.
* Skapa drivrutiner för masslagring testmiljöer för kvalitet assurance testning.
* Använd artefakter och formler för att enkelt konfigurera och återskapa ett program i olika miljöer.
* Distribuera virtuella datorer för då hackathon-evenemang (utveckling eller testning samarbete) och sedan enkelt ta bort etableringen av dem när händelsen slutar.

## <a name="how-am-i-billed-for-devtest-labs"></a>Hur debiteras jag för DevTest Labs?
DevTest Labs är en kostnadsfri tjänst. Skapa labb och konfigurera principer, mallar och artefakter i DevTest Labs är kostnadsfri. Du betalar bara för de Azure-resurser används i dina labb, till exempel virtuella datorer, lagringskonton och virtuella nätverk. Mer information om kostnaden labbresurser finns [priser för Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).


**Säkerhet**
## <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Vilka är de olika säkerhetsnivåerna i DevTest Labs?
Säkerhetsåtkomst bestäms av [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/built-in-roles.md). Om du vill veta hur åtkomst fungerar den här artikeln innehåller information skillnaderna mellan en behörighet, en roll och ett omfång som definieras av RBAC.

* **Behörighet**: behörigheter är en definierad åtkomst till en viss åtgärd. En behörighet kan exempelvis vara läsbehörighet till alla virtuella datorer.
* **Rollen**: en roll är en uppsättning behörigheter som kan grupperas och tilldelas till en användare. Exempelvis kan har en användare med en prenumeration ägarrollen åtkomst till alla resurser inom en prenumeration.
* **Omfång**: ett scope är en nivå i hierarkin för en Azure-resurs. Ett omfång kan exempelvis vara en resursgrupp, ett enda labb eller hela prenumerationen.

Det finns två typer av roller som definierar användarbehörigheter inom omfånget för DevTest Labs:

* **Labbägare**: labbägare har åtkomst till alla resurser i laboratoriet. Labbägare kan ändra principer, läsa och skriva till alla virtuella datorer, ändra det virtuella nätverket och så vidare.
* **Lab-användare**: lab-användare kan visa alla labbresurser, till exempel virtuella datorer, principer och virtuella nätverk. Men en lab-användare kan inte ändra principer eller alla virtuella datorer som har skapats av andra användare. 

Du kan också skapa anpassade roller i DevTest Labs. Läs hur du skapar anpassade roller i DevTest Labs i [bevilja användarbehörighet att specifika labbprinciper](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Eftersom scope är hierarkiska, när en användare har behörighet för ett visst omfång, beviljas automatiskt dessa behörighet för definitionsområdet varje på lägre nivå i omfånget. Till exempel om en användare har tilldelats rollen som prenumerationsägare, har användaren åtkomst till alla resurser i en prenumeration. Dessa resurser inkluderar alla virtuella datorer, alla virtuella nätverk och alla labs. En prenumerationsägare ärver automatiskt rollen som labbägare. Motsatsen är inte sant. Labbägare har åtkomst till ett labb, vilket är ett omfång som är lägre än prenumerationsnivån. Därför kan inte labbägare Se virtuella datorer, virtuella nätverk eller andra resurser som ligger utanför labbet.

## <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Hur skapar jag en roll så att användarna kan utföra en viss uppgift?
En omfattande artikel om hur du skapar anpassade roller och tilldela behörigheter till en roll finns i [bevilja användarbehörighet att specifika labbprinciper](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Här är ett exempel på ett skript som skapar rollen *DevTest Labs-avancerade användare*, som har behörighet att starta och stoppa alla virtuella datorer i labbet:

    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advanced User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  


**Integration av CI/CD och automatisering**
## <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>DevTest Labs integrera med min CI/CD-verktygskedja?
Om du använder Visual Studio Team Services, kan du använda en [DevTest Labs-uppgifter tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) att automatisera dina releasepipeline i DevTest Labs. Några av de uppgifter som du kan göra med det här tillägget är:

* Skapa och distribuera en virtuell dator automatiskt. Du kan också konfigurera den virtuella datorn med den senaste versionen med hjälp av Azure File Copy eller PowerShell Team Services-uppgifter.
* Spara automatiskt tillståndet för en virtuell dator efter test för att återskapa en bugg i samma virtuella dator för vidare studier.
* Ta bort den virtuella datorn i slutet av versionspipelinen, när den inte längre behövs.

Följande blogginlägg erbjudandet vägledning och information om hur du använder Team Services-tillägget:

* [DevTest Labs och Visual Studio Team Services-tillägget](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Distribuera en ny virtuell dator i en befintlig labb i DevTest Labs från Team Services](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Med Team Services-versionshantering för kontinuerlig distribution till DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

För andra kontinuerlig integrering (CI) / kontinuerlig leverans (CD) verktygskedjor som du kan uppnå samma scenarier genom att distribuera [Azure Resource Manager-mallar](https://aka.ms/dtlquickstarttemplate) med hjälp av [Azure PowerShell-cmdlets](../azure-resource-manager/resource-group-template-deploy.md) och [.NET SDK: er](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Du kan också använda [REST API: er för DevTest Labs](http://aka.ms/dtlrestapis) att integrera med din verktygskedja.  


**Virtuella datorer**
## <a name="why-cant-i-see-vms-on-the-virtual-machines-blade-that-i-see-in-devtest-labs"></a>Varför visas inte virtuella datorer på bladet Virtual Machines som syns i DevTest Labs?
När du skapar en virtuell dator i DevTest Labs kan ges behörighet att komma åt den virtuella datorn. Du kan visa den virtuella datorn både på bladet labs och på den **virtuella datorer** bladet. Användare som har tilldelats till rollen DevTest Labs labb kan se alla virtuella datorer som har skapats i laboratoriet på testmiljön **alla virtuella datorer** bladet. Användare som har rollen DevTest Labs lab är dock inte automatiskt beviljats läsbehörighet till VM-resurser som har skapats av andra användare. Därför visas inte dessa virtuella datorer på den **virtuella datorer** bladet.

## <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Vad är skillnaden mellan en anpassad avbildning och en formel?
En anpassad avbildning är en virtuell hårddisk (VHD). En formel är en avbildning som du kan konfigurera med ytterligare inställningar och sedan spara och återskapa. En anpassad avbildning vara att föredra om du vill att snabbt skapa flera miljöer genom att använda samma avbildning för basic, inte kan ändras. En formel kan vara bättre om du vill återskapa konfigurationen av den virtuella datorn med de senaste nyheterna, som en del av ett virtuellt nätverk eller undernät eller som en virtuell dator av en viss storlek. En mer detaljerad förklaring finns i [jämföra anpassade bilder och formler i DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

## <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Hur gör jag skapa flera virtuella datorer från samma mall på samma gång?
Har du två alternativ för att skapa flera virtuella datorer samtidigt från samma mall:
* Du kan använda den [tillägget för Visual Studio Team Services-uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks). 
* Du kan [Generera en Resource Manager-mall](devtest-lab-add-vm.md#save-azure-resource-manager-template) när du skapar en virtuell dator och [distribuera Resource Manager-mall från Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Hur flyttar jag min befintliga Azure virtuella datorer i min labb i DevTest Labs?
Kopiera dina befintliga virtuella datorer på DevTest Labs:

1. Kopiera VHD-filen för den befintliga virtuella datorn med hjälp av en [Windows PowerShell-skript](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1).
2. [Skapa en anpassad avbildning](devtest-lab-create-template.md) i ditt labb i DevTest Labs.
3. Skapa en virtuell dator i labbet från den anpassade avbildningen.

## <a name="can-i-attach-multiple-disks-to-my-vms"></a>Kan jag koppla flera diskar till Mina virtuella datorer?
Ja, du kan koppla flera diskar till dina virtuella datorer.  

## <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Om jag vill använda en Windows OS-avbildning för testning, måste jag köpa en prenumeration på MSDN?
Om du vill använda Windows-klienten OS-avbildningar (Windows 7 eller senare) för din utveckling och testning i Azure måste du göra något av följande:

- [Köp en MSDN-prenumeration](https://www.visualstudio.com/products/how-to-buy-vs).
- Om du har ett Enterprise-avtal kan du skapa en Azure-prenumeration med den [Enterprise – utveckling/testning-erbjudandet](https://azure.microsoft.com/offers/ms-azr-0148p).

Läs mer om Azure-krediter för varje MSDN-erbjudande, [månatliga Azure-kredit för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Hur jag för att automatisera processen att överföra VHD-filer för att skapa anpassade avbildningar?
Om du vill automatisera överför VHD-filer för att skapa anpassade avbildningar, har du två alternativ:

* Använd [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) att kopiera eller ladda upp VHD-filer till storage-kontot som är associerat med labbet.
* Använd [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). Storage Explorer är en fristående app som körs på Windows, OS X och Linux.   

Hitta mållagringskontot som är associerat med ditt Labb:

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. På menyn till vänster väljer **resursgrupper**.
3. Hitta och välj den resursgrupp som är associerat med ditt labb.
4. Under **översikt**, väljer du något av storage-konton.
5. Välj **Blobar**.
6. Leta efter överföringar i listan. Om ingen sådan finns, återgår till steg 4 och prova ett annat lagringskonto.
7. Använd den **URL** som mål i AzCopy-kommandot.

## <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Hur jag för att automatisera processen med att ta bort alla virtuella datorer i min labb?
Du kan ta bort virtuella datorer från ditt labb i Azure-portalen. Du kan också radera alla virtuella datorer i labbet genom att använda ett PowerShell-skript. I följande exempel visas under den **värden för att ändra** kommentar, ändra parametrarnas värden. Du kan hämta den `subscriptionId`, `labResourceGroup`, och `labName` värden från fönstret labb i Azure-portalen.

    # Delete all the VMs in a lab.

    # Values to change:
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Sign in to your Azure account.
    Connect-AzureRmAccount

    # Select the Azure subscription that has the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Get the lab that has the VMs that you want to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }

**Artefakter**
## <a name="what-are-artifacts"></a>Vad är artefakter?
Artefakter är anpassningsbara element som du kan använda för att distribuera de senaste bitarna eller distribuera utvecklingsverktyg till en virtuell dator. Koppla artefakter till den virtuella datorn när du skapar den virtuella datorn. När den virtuella datorn har etablerats artefakter att distribuera och konfigurera den virtuella datorn. Olika redan befintliga artefakter finns i vår [offentliga GitHub-lagringsplatsen](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Du kan också [skapa dina egna artefakter](devtest-lab-artifact-author.md).


**Konfiguration av testlabb**
## <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Hur skapar jag ett labb från en Resource Manager-mall
Vi erbjuder en [GitHub-lagringsplatsen för Azure Resource Manager-mallar för labb](https://aka.ms/dtlquickstarttemplate) som du kan distribuera som – är eller anpassa den för att skapa anpassade mallar för dina labb. Varje mall har en länk till distribuerar labbet som-är i din Azure-prenumeration. Eller så kan du anpassa mallen och [distribuera med hjälp av PowerShell eller Azure CLI](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Varför är Mina virtuella datorer skapas i olika resursgrupper med godtyckliga namn? Kan jag byta namn på eller ändra de här resursgrupperna?
Resursgrupper skapas på så sätt så att DevTest Labs kan hantera, användarbehörigheter och åtkomst till virtuella datorer. Även om du kan flytta en virtuell dator till en annan resursgrupp och använda det namn som du vill, rekommenderar vi att du inte gör ändringar i resursgrupper. Vi arbetar på att förbättra upplevelsen för att tillåta mer flexibilitet.   

## <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Hur många labs kan jag skapa under samma prenumeration?
Det finns inte någon särskild gräns för antalet labs som kan skapas per prenumeration. Mängden resurser som används per prenumeration är dock begränsad. Du kan läsa om den [begränsningar och kvoter för Azure-prenumerationer](../azure-subscription-service-limits.md) och [så här ökar du dessa gränser](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-many-vms-can-i-create-per-lab"></a>Hur många virtuella datorer per labb kan jag skapa?
Det finns ingen specifik gräns för antalet virtuella datorer som kan skapas per labb. Resurser (Virtuella kärnor, offentliga IP-adresser och så vidare) som används är dock begränsade per prenumeration. Du kan läsa om den [begränsningar och kvoter för Azure-prenumerationer](../azure-subscription-service-limits.md) och [så här ökar du dessa gränser](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Hur gör jag för att dela en direktlänk till mitt labb?

1. I Azure-portalen går du till labbet.
2. Kopiera URL som lab från din webbläsare och sedan dela den med dina labbanvändare.

> [!NOTE]
> Om en lab-användare är en extern användare som har en [microsoftkonto](#what-is-a-microsoft-account), men som inte är medlem i din organisations Active Directory-instans, kan användaren visas ett felmeddelande när de försöker komma åt den delade länken. Om en extern användare får ett felmeddelande, be användaren att först markera användarens namn i det övre högra hörnet i Azure Portal. I den **Directory** på menyn användaren kan välja den katalog där det inte finns labbet.
>
>

## <a name="what-is-a-microsoft-account"></a>Vad är ett Microsoft-konto?
Ett Microsoft-konto är ett konto som du använder för nästan allt du gör med Microsoft-enheter och tjänster. Det är en e-postadress och lösenord som användes för att logga in på Skype, Outlook.com, OneDrive, Windows phone och Xbox Live. Ett enda konto innebär att dina filer, foton, kontakter och inställningar kan följa du på valfri enhet.

> [!NOTE]
> Ett Microsoft-konto som tidigare kallades en *Windows Live ID*.
>
>


**Felsökning**
## <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Min artefakt misslyckades under skapande av virtuell dator. Hur felsöker jag det?
Läs hur du hämtar loggarna för din misslyckade artefakten i [så diagnostisera fel i artefakter i DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Varför inte mina befintliga virtuella nätverk sparas korrekt?
Ett sätt är att din virtuella nätverksnamnet innehåller punkter. I så fall, försök att ta bort punkter eller ersätta dem med bindestreck. Försök sedan igen att spara det virtuella nätverket.

## <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Varför får jag en ”överordnad resursen hittades inte”-fel när jag etablerar en virtuell dator från PowerShell?
När en resurs är en överordnad till en annan resurs, måste den överordnade resursen finnas innan du skapar den underordnade resursen. Om den överordnade resursen inte finns, visas en **ParentResourceNotFound** meddelande. Om du inte anger ett beroende på den överordnade resursen, kan den underordnade resursen distribueras innan överordnat.

Virtuella datorer är underordnade resurser under ett labb i en resursgrupp. När du använder Resource Manager-mallar för att distribuera virtuella datorer med hjälp av PowerShell, vara resursgruppens namn anges i PowerShell-skriptet resursgruppens namn labbet. Mer information finns i [felsöka vanliga Azure-distributionsfel](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors#parentresourcenotfound).

## <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Var hittar jag mer information om fel om en VM-distributionen misslyckas?
Distributionsfel för virtuell dator som avbildas i aktivitetsloggarna. Du kan hitta lab VM aktivitetsloggar under **granskningsloggar** eller **VM diagnostics** på resurs-menyn på bladet för den lab VM (bladet visas när du har valt den virtuella datorn från den **min virtuella datorer** listan).

Ibland inträffar distributionsfel innan du påbörjar distribution av virtuella datorer. Ett exempel är när gränsen för en resurs som har skapats med den virtuella datorn har överskridits. I det här fallet fångas felinformationen i labb-nivå aktivitetsloggar. Aktivitetsloggar är placerad längst ned i den **konfiguration och principer** inställningar. Mer information om hur du använder aktiviteten loggar i Azure finns i [visa aktivitetsloggar till granska åtgärder på resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
