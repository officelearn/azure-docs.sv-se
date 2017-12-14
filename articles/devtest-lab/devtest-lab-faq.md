---
title: "Azure DevTest Labs vanliga frågor och svar | Microsoft Docs"
description: "Få svar på vanliga frågor om Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: tarcher
ms.openlocfilehash: 1f261f97bbd9233d47eadc7e902e00ee87af9e34
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs vanliga frågor och svar
Få svar på några av de vanligaste frågorna om Azure DevTest Labs.

**Allmänt**
## <a name="what-if-my-question-isnt-answered-here"></a>Vad gör jag om min fråga inte besvaras här?
Om din fråga inte finns här berätta för oss, så vi kan hjälpa dig att hitta ett svar.

* Skicka en fråga i slutet av den här vanliga frågor och svar. Interaktion med Azure Cache-teamet och andra gruppmedlemmar om den här artikeln.
* För att nå en bredare publik ställa en fråga på den [Azure DevTest Labs MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Interaktion med Azure DevTest Labs teamet och andra medlemmar i gruppen.
* Skicka din begäran och idéer för funktionsbegäranden [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

## <a name="why-should-i-use-azure-devtest-labs"></a>Varför ska jag använda Azure DevTest Labs?
Azure DevTest Labs kan spara ditt team tid och pengar. Utvecklare kan skapa sina egna miljöer med flera olika databaser. De kan också använda artefakter att snabbt distribuera och konfigurera program. Genom att använda anpassade avbildningar och formler kan du spara virtuella datorer (VM) som en mall och kan återskapa dem i teamet. DevTest Labs erbjuder också flera principer som konfigureras som administratörer kan använda för att minska skräp och hantera en grupps miljöer labbet. Dessa principer omfattar automatisk avstängning, kostnad tröskelvärdet maximala virtuella datorer per användare och högsta VM-storlek. En mer detaljerad förklaring av DevTest Labs, finns det [översikt](devtest-lab-overview.md) eller [inledande video](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

## <a name="what-does-worry-free-self-service-mean"></a>Vad ”problemfri självbetjäning” betyder?
Problemfri självbetjäning innebär att utvecklare och testare skapa sina egna miljöer efter behov. Administratörer har säkerhet att veta att DevTest Labs kan du minimera skräp och kontrollera kostnaderna. Administratörer kan ange vilka VM-storlekar tillåts kan det maximala antalet virtuella datorer, och när virtuella datorer är igång och stänga av. DevTest Labs också enkelt att övervaka kostnaderna och ställa in aviseringar som hjälper dig vara medvetna om hur lab resurser används.

## <a name="how-can-i-use-devtest-labs"></a>Hur kan jag använda DevTest Labs?
DevTest Labs är användbart när du behöver dev testmiljöer eller vill du återskapa dem snabbt och hantera dem med hjälp av principer för att spara kostnader.

Här följer några scenarier som våra kunder använder DevTest Labs för:

* Hantera dev och testmiljöer på ett ställe. Använd principer för att minska kostnaderna och skapa anpassade avbildningar till delar bygger i teamet.
* Utveckla ett program genom att använda anpassade avbildningar för att spara diskstatusen under utveckling steg.
* Spåra kostnad i förhållande till pågår.
* Skapa drivrutinslista testmiljöer för quality assurance testning.
* Använda artefakter och formler för att enkelt konfigurera och återskapa ett program i olika miljöer.
* Distribuera virtuella datorer för hackathons (utvecklings- eller samarbete) och sedan enkelt ta bort etableringen av dem när händelsen slutar.

## <a name="how-am-i-billed-for-devtest-labs"></a>Hur är debiteras för DevTest Labs?
DevTest Labs är en kostnadsfri. Skapa labs och konfigurera principer, mallar och artefakter i DevTest Labs är kostnadsfri. Du betalar bara för Azure-resurser används i din labs, till exempel virtuella datorer, lagringskonton och virtuella nätverk. Mer information om kostnaderna för labbet resurser finns [priser för Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).


**Säkerhet**
## <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Vilka är de olika säkerhetsnivåerna i DevTest Labs?
Säkerhetsåtkomst bestäms av [rollbaserad åtkomstkontroll (RBAC)](../active-directory/role-based-access-built-in-roles.md). Om du vill veta hur åtkomst fungerar, hjälper det att kunna skillnaderna mellan en behörighet, en roll och ett omfång som definieras av RBAC.

* **Behörighet**: behörigheter är en definierad åtkomst till en specifik åtgärd. En behörighet kan till exempel vara läsbehörighet till alla virtuella datorer.
* **Rollen**: en roll är en uppsättning behörigheter som kan grupperas och tilldelats till en användare. Till exempel har en användare med en prenumeration ägarrollen åtkomst till alla resurser inom en prenumeration.
* **Scope**: ett scope är en nivå i hierarkin för en Azure-resurs. Ett omfång kan exempelvis vara en resursgrupp, ett enda labb eller hela prenumerationen.

Det finns två typer av roller som definierar användarbehörigheter för inom omfånget för DevTest Labs:

* **Labbägaren**: en labbägaren har åtkomst till alla resurser i labbet. En labbägaren kan ändra principer, läsa och skriva till virtuella datorer, ändra det virtuella nätverket och så vidare.
* **Lab-användare**: lab-användare kan visa alla lab resurser, till exempel virtuella datorer, principer och virtuella nätverk. Men en lab-användare kan inte ändra principer eller virtuella datorer som har skapats av andra användare. 

Du kan också skapa anpassade roller i DevTest Labs. Information om hur du skapar anpassade roller i DevTest Labs finns [bevilja användarbehörighet att principer för specifika labbet](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Eftersom scope är hierarkiska, när en användare har behörighet för ett visst område, beviljas automatiskt dessa behörighet för definitionsområdet var lägre nivåer i omfånget. Till exempel om en användare har tilldelats rollen prenumerationsägaren, har användaren åtkomst till alla resurser i en prenumeration. Dessa resurser inkluderar alla virtuella datorer, alla virtuella nätverk och alla övningar. En prenumeration ägare ärver automatiskt labbägaren roll. Motsatsen är inte sant. En labbägaren har åtkomst till ett labb som är en omfattning som är lägre än prenumerationsnivån. Därför kan inte en labbägaren Se virtuella datorer, virtuella nätverk eller andra resurser som ligger utanför labbet.

## <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Hur skapar jag en roll om du vill tillåta användare att utföra en viss uppgift?
En omfattande artikel om hur du skapar anpassade roller och tilldela behörigheter till en roll finns [bevilja användarbehörighet att principer för specifika labbet](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Här är ett exempel på ett skript som skapar rollen *DevTest Labs avancerade användare*, som har behörighet att starta och stoppa alla virtuella datorer i labbet:

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


**CI/CD-integration och automatisering**
## <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>DevTest Labs kan integreras med min CI/CD toolchain?
Om du använder Visual Studio Team Services kan du använda en [DevTest Labs uppgifter tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) att automatisera din pipeline versionen i DevTest Labs. Några av de uppgifter som du kan göra med det här tillägget är:

* Skapa och distribuera en virtuell dator automatiskt. Du kan också konfigurera den virtuella datorn med den senaste versionen genom att använda Azure File Copy eller PowerShell Team Services uppgifter.
* Automatiskt spara tillståndet för en virtuell dator efter tester för att återskapa ett programfel på samma virtuella dator för ytterligare undersökning.
* Ta bort den virtuella datorn i slutet av pipeline versionen när den inte längre behövs.

Följande blogginlägg erbjudande vägledning och information om hur du använder tillägget Team Services:

* [DevTest Labs och Visual Studio Team Services-tillägget](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Distribuera en ny virtuell dator i en befintlig DevTest Labs labbet från Team Services](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Med hjälp av Team Services versionshantering för kontinuerlig distributioner till DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

För andra kontinuerlig integration (KO) / kontinuerlig leverans (CD) toolchains som du kan uppnå samma scenarier genom att distribuera [Azure Resource Manager-mallar](https://aka.ms/dtlquickstarttemplate) med hjälp av [Azure PowerShell-cmdlets](../azure-resource-manager/resource-group-template-deploy.md) och [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Du kan också använda [REST API: er för DevTest Labs](http://aka.ms/dtlrestapis) att integrera med din toolchain.  


**Virtuella datorer**
## <a name="why-cant-i-see-vms-on-the-virtual-machines-blade-that-i-see-in-devtest-labs"></a>Varför ser inte virtuella datorer på bladet för virtuella datorer som visas i DevTest Labs?
När du skapar en virtuell dator i DevTest Labs ges behörighet att komma åt den virtuella datorn. Du kan visa den virtuella datorn både på bladet labs och på den **virtuella datorer** bladet. Användare som tilldelats rollen DevTest Labs lab-användare kan se alla virtuella datorer som har skapats i labbet på testmiljön **alla virtuella datorer** bladet. Dock beviljas användare som har rollen DevTest Labs lab inte automatiskt läsbehörighet till VM resurser som har skapats av andra användare. Därför visas inte dessa virtuella datorer på den **virtuella datorer** bladet.

## <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Vad är skillnaden mellan en anpassad avbildning och en formel?
En anpassad avbildning är en virtuell hårddisk (VHD). En formel är en avbildning som du kan konfigurera ytterligare inställningar och sedan spara och återskapa. En anpassad avbildning kan vara bäst om du snabbt vill skapa flera miljöer genom att använda samma avbildning för grundläggande, ändras. En formel kan vara bättre om du vill återskapa konfigurationen av den virtuella datorn med de senaste bitarna som en del av ett virtuellt nätverk eller undernät, eller som en virtuell dator i en viss storlek. En mer detaljerad förklaring finns [jämföra anpassade avbildningar och formler i DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

## <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Hur gör jag skapa flera virtuella datorer från samma mall på samma gång?
Har du två alternativ för att skapa flera virtuella datorer samtidigt från samma mall:
* Du kan använda den [Visual Studio Team Services uppgifter tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks). 
* Du kan [Generera en Resource Manager-mall](devtest-lab-add-vm.md#save-azure-resource-manager-template) när du skapar en virtuell dator och [distribuera Resource Manager-mall från Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Hur flytta mina befintliga virtuella Azure-datorer i min labb i DevTest Labs?
Kopiera dina befintliga virtuella datorer till DevTest Labs:

1. Kopiera VHD-filen på den befintliga virtuella datorn med hjälp av en [Windows PowerShell-skript](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1).
2. [Skapa den anpassade avbildningen](devtest-lab-create-template.md) i ditt labb i DevTest Labs.
3. Skapa en virtuell dator i labbet från den anpassade avbildningen.

## <a name="can-i-attach-multiple-disks-to-my-vms"></a>Kan jag ansluta flera diskar till Mina virtuella datorer?
Ja, kan du bifoga flera diskar till dina virtuella datorer.  

## <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Om jag vill använda en Windows-operativsystemsavbildning för min testning måste jag köpa en MSDN-prenumeration?
Om du vill använda operativsystemsavbildningar i Windows-klienten (Windows 7 eller senare) för din utveckling och testning i Azure måste du göra något av följande:

- [Köp en prenumeration på MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Om du har ett Enterprise-avtal, skapa en Azure-prenumeration med den [utveckling och testning Enterprise erbjudande](https://azure.microsoft.com/en-us/offers/ms-azr-0148p).

Läs mer om Azure-krediter för varje MSDN-erbjudande [månatliga Azure-kredit för Visual Studio-prenumeranter](https://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits-details/).

## <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Hur jag för att automatisera processen att överföra VHD-filer för att skapa anpassade avbildningar?
Om du vill automatisera uppladdning VHD-filer för att skapa anpassade avbildningar, har du två alternativ:

* Använd [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) kopiera eller överför VHD-filer till storage-konto som är kopplad till labbet.
* Använd [Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md). Lagringsutforskaren är en fristående app som körs på Windows-, OS X- och Linux.   

Hitta destinationslagringskontot som är kopplad till ditt Labb:

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. På den vänstra menyn väljer **resursgrupper**.
3. Hitta och markera den resursgrupp som är kopplad till ditt labb.
4. Under **översikt**, väljer du något av storage-konton.
5. Välj **Blobbar**.
6. Leta efter överföringar i listan. Om inget finns tillbaka till steg 4 och försök med ett annat lagringskonto.
7. Använd den **URL** som mål i AzCopy-kommandot.

## <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Hur jag för att automatisera processen med att ta bort alla virtuella datorer i min labbet?
Du kan ta bort virtuella datorer från ditt labb i Azure-portalen. Du kan också radera alla virtuella datorer i labbet genom att använda ett PowerShell-skript. I följande exempel under den **värden för att ändra** kommentar, ändra parametervärden. Du kan hämta den `subscriptionId`, `labResourceGroup`, och `labName` värden från fönstret labb i Azure-portalen.

    # Delete all the VMs in a lab.

    # Values to change:
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Sign in to your Azure account.
    Login-AzureRmAccount

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
Artefakter är anpassningsbara element som du kan använda för att distribuera din senaste bitar eller distribuera dev-verktyg till en virtuell dator. Koppla artefakter till den virtuella datorn när du skapar den virtuella datorn. När den virtuella datorn har etablerats artefakter distribuera och konfigurera den virtuella datorn. Olika redan befintliga artefakter finns i vår [offentliga GitHub-lagringsplatsen](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Du kan också [redigera egna artefakter](devtest-lab-artifact-author.md).


**Konfiguration av testlabb**
## <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Hur skapar jag en lab från en Resource Manager-mall
Vi erbjuder en [GitHub-lagringsplatsen för labbet Azure Resource Manager-mallar](https://aka.ms/dtlquickstarttemplate) som du kan distribuera som-är eller ändra för att skapa anpassade mallar för din testlabb. Varje mall har en länk för att distribuera labbet som-finns i din egen Azure-prenumeration. Eller så kan du anpassa mallen och [distribueras med hjälp av PowerShell eller Azure CLI](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Varför skapas Mina virtuella datorer i olika resursgrupper med godtyckliga namn? Kan jag byta namn på eller ändra de här resursgrupperna?
Resursgrupper skapas det här sättet så att DevTest Labs kan hantera behörigheter och åtkomst till virtuella datorer. Även om du kan flytta en virtuell dator till en annan resursgrupp och använder det namn som du vill, rekommenderar vi att du inte ändrar resursgrupper. Vi arbetar på att förbättra upplevelsen för att tillåta mer flexibilitet.   

## <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Hur många labs kan skapa under samma prenumeration?
Det finns inte någon särskild gräns för antalet labs som kan skapas per prenumeration. Mängden resurser som används per prenumeration är dock begränsad. Du kan läsa om den [gränser och kvoter för Azure-prenumerationer](../azure-subscription-service-limits.md) och [öka gränserna](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-many-vms-can-i-create-per-lab"></a>Hur många virtuella datorer kan skapa per labb?
Det finns ingen specifik gräns för antalet virtuella datorer som kan skapas per labb. Resurser (VM kärnor, offentliga IP-adresser och så vidare) som används är dock begränsade per prenumeration. Du kan läsa om den [gränser och kvoter för Azure-prenumerationer](../azure-subscription-service-limits.md) och [öka gränserna](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Hur dela en direktlänk till min labbet?

1. Gå till labbet i Azure-portalen.
2. Kopiera URL som lab från din webbläsare och dela den med din lab-användare.

> [!NOTE]
> Om en lab-användare är en extern användare som har en [Microsoft-konto](#what-is-a-microsoft-account), men som inte är medlem i organisationens Active Directory-instans kan användaren visas ett felmeddelande när de försöker komma åt delad länk. Om en extern användare ser ett felmeddelande, be användaren att först välja sina namn i det övre högra hörnet i Azure-portalen. I den **Directory** på menyn användaren kan välja den katalog där det finns labbet.
>
>

## <a name="what-is-a-microsoft-account"></a>Vad är ett Microsoft-konto?
Ett Microsoft-konto är ett konto som du använder för nästan allt du gör med Microsoft-enheter och tjänster. Det är en e-postadress och lösenord som du använder för att logga in på Skype, Outlook.com, OneDrive, Windows phone och Xbox Live. Ett enskilt konto innebär att dina filer, foton, kontakter och inställningar kan följa du på alla enheter.

> [!NOTE]
> Ett Microsoft-konto som används för att anropa en *Windows Live ID*.
>
>


**Felsökning**
## <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Min artefakt misslyckades under skapande av virtuell dator. Hur felsöker jag det?
Information om hur du hämtar loggarna för din misslyckade artefakt finns [felsökning artefakt fel i DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Varför är min befintliga virtuella nätverk sparas korrekt?
Ett sätt är att din virtuella nätverksnamnet innehåller punkter. I så fall, försök att ta bort perioderna eller ersätta dem med bindestreck. Försök sedan igen att spara det virtuella nätverket.

## <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Varför visas ett ”överordnade resursen hittades inte”-fel när jag etablera en virtuell dator från PowerShell?
När en resurs är en överordnad till en annan resurs, måste överordnade resursen finnas innan du kan skapa underordnade resursen. Om den överordnade resursen inte finns, visas en **ParentResourceNotFound** meddelande. Om du inte anger ett beroende på den överordnade resursen, kan den underordnade resursen distribueras innan överordnat.

Virtuella datorer som är underordnade resurser under ett labb i en resursgrupp. När du använder Resource Manager-mallar för att distribuera virtuella datorer med hjälp av PowerShell, ska resursgruppens namn anges i PowerShell-skript resursgruppens namn labbet. Mer information finns i [Felsök vanliga fel i Azure-distribution](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-common-deployment-errors#parentresourcenotfound).

## <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Var hittar jag mer information om fel om inte en distribution av Virtuella datorer?
VM-distributionsfel fångas i aktivitetsloggar. Du kan hitta lab VM aktivitetsloggar under **granskningsloggar** eller **diagnostik för virtuell dator** på menyn resurs på den testmiljön VM bladet (bladet visas när du har valt den virtuella datorn från den **min virtuella datorer** listan).

Ibland inträffar distribution felet innan du påbörjar distribution av Virtuella datorer. Ett exempel är när gränsen för en resurs som har skapats med den virtuella datorn har överskridits. I det här fallet fångas felinformationen i labb-nivå aktivitetsloggar. Aktivitetsloggar finns längst ned i den **konfiguration och principer** inställningar. Mer information om hur du använder aktiviteten loggar i Azure, finns [visa aktivitetsloggar granska åtgärder på resurser](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-audit).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
