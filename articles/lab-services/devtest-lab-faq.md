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
ms.date: 04/19/2019
ms.author: spelluru
ms.openlocfilehash: 91c598bde0912cffb8aa1dd7ba022c86a9084faa
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127403"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs vanliga frågor och svar
Få svar på några av de vanligaste frågorna om Azure DevTest Labs.

## <a name="blog-post"></a>Blogginlägg
Vår Teamblogg för DevTest Labs har tagits bort från och med den 20 mars 2019. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Var kan jag för att spåra funktionsuppdateringar hädanefter?
Hädanefter kan vi publicerar funktions- och informativa blogginlägg på Azure-bloggen och Azure uppdateras. Dessa blogginlägg också länkar till dokumentationen när det behövs.

Prenumerera på den [DevTest Labs Azure-bloggen](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) och [DevTest Labs Azure uppdaterar](https://azure.microsoft.com/updates/?product=devtest-lab) att hålla dig informerad om nya funktioner i DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>Vad händer med befintliga blogginlägg?
Vi arbetar med att migrera befintliga blogginlägg (exklusive avbrott uppdateringar) att vår [dokumentation för DevTest Labs](devtest-lab-overview.md). När MSDN-blogg är inaktuell, omdirigeras den till översikten dokumentation för DevTest Labs. När omdirigeras, kan du söka efter artikeln du söker i rubriken ”filtrera efter”. Vi har inte migrerats alla inlägg ännu, men bör utföras av den här månadens slut. 


### <a name="where-do-i-see-outage-updates"></a>Var ser jag avbrott uppdateringar?
Vi kommer att publicera avbrott uppdateringar med hjälp av vår Twitter-referens från nu och senare. Följ oss på Twitter om du vill få senaste uppdateringarna på avbrott och kända buggar.

### <a name="twitter"></a>Twitter
Vår Twitter-referens: [@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Allmänt
### <a name="what-if-my-question-isnt-answered-here"></a>Vad händer om min fråga inte besvaras här?
Om din fråga inte visas kan berätta för oss, så att vi kan hjälpa dig hitta något svar.

- Ställa en fråga i slutet av den här vanliga frågor och svar. Kommunicera med Azure Cache-teamet och andra community-medlemmar om den här artikeln.
- För att nå en bredare publik, ställa en fråga på den [Azure DevTest Labs MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Kommunicera med Azure DevTest Labs-teamet och andra medlemmar i communityn.
- Skicka in dina förfrågningar och idéer för funktionsförfrågningar om [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>Vad är ett Microsoft-konto?
Ett Microsoft-konto är ett konto som du använder för nästan allt du gör med Microsoft-enheter och tjänster. Det är ett e-postadress och lösenord som används för att logga in på Skype, Outlook.com, OneDrive, Windows phone, Azure och Xbox Live. Ett enda konto innebär att dina filer, foton, kontakter och inställningar kan följa du på valfri enhet.
 
> [!NOTE]
> Ett Microsoft-konto som tidigare kallades ett Windows Live ID.

### <a name="why-should-i-use-azure-devtest-labs"></a>Varför ska jag använda Azure DevTest Labs?
Azure DevTest Labs kan spara ditt team tid och pengar. Utvecklare kan skapa egna miljöer med flera olika baser. De kan också använda artefakter för att snabbt distribuera och konfigurera program. Genom att använda anpassade bilder och formler kan du spara virtuella datorer (VM) som en mall och återskapa dem enkelt i teamet. DevTest Labs erbjuder även flera principer som kan konfigureras att labb som administratörer kan använda för att minska avfall och hantera ett team-miljöer. Dessa principer omfattar automatisk avstängning, kostnad tröskelvärdet maximala virtuella datorer per användare och maximal storlek för virtuell dator. En mer detaljerad förklaring av DevTest Labs finns i den [översikt](devtest-lab-overview.md) eller [introduktionsvideon](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Vad ”problemfri självbetjäning” betyder?
Problemfri självbetjäning innebär att utvecklare och testare skapa egna miljöer efter behov. Administratörer har säkerheten för att känna till att DevTest Labs hjälper lämplig åtkomst, minimera avfall och kontrollera kostnaderna. Administratörer kan ange vilka VM-storlekar är tillåtna, det maximala antalet virtuella datorer, och när virtuella datorer är igång och stänga av. DevTest Labs gör det också enkelt att övervaka kostnader och Ställ in aviseringar som hjälper dig vara medveten om hur labbresurser används.

### <a name="how-can-i-use-devtest-labs"></a>Hur kan jag använda DevTest Labs?
DevTest Labs är användbar när du kräver dev testmiljöer eller vill du återskapa dem snabbt, eller hantera dem med hjälp av kostnadsbesparande principer.
Här följer några scenarier som våra kunder använder labb för:

- Hantera utvecklings- och testmiljöer på samma ställe. Använd principer för att minska kostnaderna och skapa anpassade avbildningar för att dela bygger i teamet.
- Utveckla ett program genom att använda anpassade avbildningar för att spara diskstatusen under hela utvecklingen faser.
- Spåra kostnad i förhållande till pågår.
- Skapa drivrutiner för masslagring testmiljöer för kvalitet assurance testning.
- Använd artefakter och formler för att enkelt konfigurera och återskapa ett program i olika miljöer.
- Distribuera virtuella datorer för då hackathon-evenemang (utveckling eller testning samarbete) och sedan enkelt ta bort etableringen av dem när händelsen slutar.

### <a name="how-am-i-billed-for-devtest-labs"></a>Hur debiteras jag för DevTest Labs?
DevTest Labs är en kostnadsfri tjänst. Skapa labb och konfigurera principer, mallar och artefakter i DevTest Labs är kostnadsfri. Du betalar bara för de Azure-resurser används i dina labb, till exempel virtuella datorer, lagringskonton och virtuella nätverk. Mer information om kostnaden labbresurser finns [priser för Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).

## <a name="security"></a>Säkerhet

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Vilka är de olika säkerhetsnivåerna i DevTest Labs?
Säkerhetsåtkomst bestäms av rollbaserad åtkomstkontroll (RBAC). Om du vill veta hur åtkomst fungerar den här artikeln innehåller information skillnaderna mellan en behörighet, en roll och ett omfång som definieras av RBAC.

- **Behörighet**: En behörighet är en definierad åtkomst till en viss åtgärd. En behörighet kan exempelvis vara läsbehörighet till alla virtuella datorer.
- **Roll**: En roll är en uppsättning behörigheter som kan grupperas och tilldelas till en användare. Exempelvis kan har en användare med en prenumeration ägarrollen åtkomst till alla resurser inom en prenumeration.
- **Omfång**: Ett scope är en nivå i hierarkin för en Azure-resurs. Ett omfång kan exempelvis vara en resursgrupp, ett enda labb eller hela prenumerationen.

Det finns två typer av roller som definierar användarbehörigheter inom omfånget för DevTest Labs:

- **Labbägare**: Labbägare har åtkomst till alla resurser i laboratoriet. Labbägare kan ändra principer, läsa och skriva till alla virtuella datorer, ändra det virtuella nätverket och så vidare.
- **Lab-användare**: En lab-användare kan visa alla labbresurser, till exempel virtuella datorer, principer och virtuella nätverk. Men kan inte en lab-användare ändra principer eller alla virtuella datorer som har skapats av andra användare.

Du kan också skapa anpassade roller i DevTest Labs. Läs hur du skapar anpassade roller i DevTest Labs i [bevilja användarbehörighet att specifika labbprinciper](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Eftersom scope är hierarkiska, när en användare har behörighet för ett visst omfång, beviljas automatiskt dessa behörighet för definitionsområdet varje på lägre nivå i omfånget. Till exempel om en användare har tilldelats rollen som prenumerationsägare, har användaren åtkomst till alla resurser i en prenumeration. Dessa resurser inkluderar virtuella datorer, virtuella nätverk och labb. En prenumerationsägare ärver automatiskt rollen som labbägare. Motsatsen är inte sant. Labbägare har åtkomst till ett labb, vilket är ett omfång som är lägre än prenumerationsnivån. Därför kan inte labbägare Se virtuella datorer, virtuella nätverk eller andra resurser som ligger utanför labbet.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Hur definierar jag rollbaserad åtkomstkontroll för Mina DevTest Labs miljöer för att säkerställa att IT-avdelningen kan styra när utvecklare och testning kan utföra sitt arbete?
Det finns ett brett mönster, men du detaljnivån beror på din organisation.

Central IT ska äga bara vad som krävs och aktivera teamen för projekt och program ha nödvändiga nivå av kontroll. Det innebär normalt den centrala IT äger prenumerationen och hanterar viktiga IT-funktioner, till exempel nätverkskonfigurationer. Uppsättningen **ägare** för en prenumeration ska vara små. Följande ägare kan utse ytterligare ägare när det finns ett behov eller tillämpa prenumerationsnivå principer, till exempel ”ingen offentlig IP-adress”.

Det kan finnas en delmängd av användare som behöver åtkomst i hela prenumerationen, t.ex nivå 1 eller nivå 2-stöd. I det här fallet rekommenderar vi att du ger användarna den **deltagare** så att de inte kan hantera resurser, men ge användaråtkomst eller justera principer.

DevTest Labs-resursen ska ägas av ägare som ligger nära projekt/program-teamet. Det beror på att de förstår sina krav för datorer och programvara som krävs. I de flesta organisationer är ägare till den här DevTest Labs-resurs ofta projekt/utveckling lead. Den här ägaren kan hantera användare och principer i laboratoriemiljön och kan hantera alla virtuella datorer i labb-miljö.

Gruppmedlemmarna projekt/program som ska läggas till i **DevTest Labs-användare** roll. Dessa användare kan skapa virtuella datorer (infogade med lab och prenumerationsnivå principer). De kan också hantera sina egna virtuella datorer. De kan inte hantera virtuella datorer som tillhör andra användare.

Mer information finns i [Azure enterprise kodskelett – normativ prenumeration styrning dokumentation](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Hur skapar jag en roll så att användarna kan utföra en viss uppgift?
En omfattande artikel om hur du skapar anpassade roller och tilldela behörigheter till en roll finns i [bevilja användarbehörighet att specifika labbprinciper](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Här är ett exempel på ett skript som skapar rollen **DevTest Labs-avancerade användare**, som har behörighet att starta och stoppa alla virtuella datorer i labbet:


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Hur kan en organisation till att företagets säkerhetsprinciper är på plats?
En organisation kan åstadkomma det genom att göra följande:

- Utveckla och publicera en omfattande säkerhetsprincip. Principen tydliggör reglerna för godkänd användning som är associerade med den med hjälp av programvara, molntillgångar. Den definierar också vad tydligt bryter mot principen.
- Utveckla en anpassad avbildning, anpassade artefakter och en process som gör det möjligt orchestration inom området säkerhet som definieras med active directory. Den här metoden tillämpar företagsgräns och anger en gemensam uppsättning Miljökontroller. Dessa kontroller mot miljön utvecklare kan ha i åtanke när de utvecklar och följa en utvecklingslivscykel för säker som en del av deras övergripande processen. Målet är också att tillhandahålla en miljö som inte är mycket begränsad som kan hindra utveckling, men en rimlig uppsättning kontroller. Grupprinciper på organisationsenheten (OU) som innehåller virtuella datorer för testlabbet kan vara en delmängd av de totala grupprinciper som påträffas i produktion. De kan också vara en extra uppsättning korrekt hantera alla identifierade risker.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Hur kan en organisation säkerställa dataintegriteten för att säkerställa att fjärrkommunikation utvecklare inte kan ta bort kod eller införa skadlig kod eller icke-godkända programvara?
Det finns flera styrningslager kan minimera hotet från externa konsulter, leverantörer och anställda som är fjärrkommunikation i att samarbeta i DevTest Labs.

Som tidigare nämnts bör måste det första steget ha en policy för acceptabel användning utformas och definieras som tydligt visar konsekvenserna när någon bryter mot principen.

Det första lagret av kontroller för fjärråtkomst är att använda principer för fjärråtkomst via en VPN-anslutning som inte är direkt kopplad till labbet.

Det andra lagret för kontroller är att använda en uppsättning grupprincipobjekt som kan förhindra att kopiera och klistra in via fjärrskrivbord. En princip kan implementeras för att inte tillåta utgående från miljön, till exempel FTP och RDP-tjänsterna från miljön. Användardefinierad routning kan tvinga all Azure nätverkstrafik tillbaka till den lokala, men routning kunde inte hänsyn till alla URL: er som gör överföring av data, såvida inte kontrolleras via en proxy söker igenom innehållet och sessioner. Offentliga IP-adresser kan vara begränsade i det virtuella nätverket som stöd för DevTest Labs för att inte tillåta bryggning för en resurs i externt nätverk.

Slutligen måste samma typ av begränsningar tillämpas i organisationen, vilket skulle konto för alla möjliga metoder för flyttbara medier eller externa URL: er som kunde acceptera ett inlägg av innehåll. Kontakta din professionella att granska och implementera en säkerhetsprincip för säkerhet. Fler rekommendationer finns i [Microsoft Cyberhot Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Konfiguration av testlabb

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Hur skapar jag ett labb från en Resource Manager-mall
Vi erbjuder en [GitHub-lagringsplatsen för Azure Resource Manager-mallar för labb](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) som du kan distribuera som – är eller anpassa den för att skapa anpassade mallar för dina labb. Varje mall har en länk till distribuerar labbet eftersom den är i din Azure-prenumeration. Eller så kan du anpassa mallen och [distribuera med hjälp av PowerShell eller Azure CLI](../azure-resource-manager/resource-group-template-deploy.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Kan jag ha alla virtuella datorer som ska skapas i en gemensam resursgrupp i stället att varje dator i en egen resursgrupp? 
Ja, som labbägare kan du låta antingen labbet hantera allokering av grupp för dig eller har alla virtuella datorer skapas i en gemensam resursgrupp som du anger. 

Separat resurs grupp scenario:
-   DevTest Labs skapar en ny resursgrupp för alla offentliga och privata IP-datorer sätta upp
-   DevTest Labs skapar en resursgrupp för delade IP-datorer som tillhör samma storlek.

Vanligt scenario för resurs-grupp:
-   Alla virtuella datorer skapas i den vanliga resursgruppen som du anger. Läs mer [allokering av grupp för labbet](https://aka.ms/RGControl). 

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Hur jag för att bibehålla en namngivningskonvention för min labb-miljö?
Du kanske vill utöka aktuella enterprise namnkonventioner för Azure-åtgärder och göra dem konsekvent i labb-miljö. Vi rekommenderar att du har specifika från principer när du distribuerar DevTest Labs. Du distribuerar dessa principer genom att en central skript och JSON-mallar för att upprätthålla konsekvens. Namngivning av principer kan implementeras via Azure-principer tillämpas på prenumerationsnivån. JSON-exempel för Azure Policy, se [Azure Policy-exempel](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Hur många labs kan jag skapa under samma prenumeration?
Det finns inte någon särskild gräns för antalet labs som kan skapas per prenumeration. Mängden resurser som används per prenumeration är dock begränsad. Du kan läsa om den [begränsningar och kvoter för Azure-prenumerationer](../azure-subscription-service-limits.md) och [så här ökar du dessa gränser](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>Hur många virtuella datorer per labb kan jag skapa?
Det finns ingen specifik gräns för antalet virtuella datorer som kan skapas per labb. Resurser (Virtuella kärnor, offentliga IP-adresser och så vidare) som används är dock begränsade per prenumeration. Du kan läsa om den [begränsningar och kvoter för Azure-prenumerationer](../azure-subscription-service-limits.md) och [så här ökar du dessa gränser](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Hur vet jag antalet användare per labb och det totala antalet labs som behövs i en organisation?
Vi rekommenderar att affärsenheter och utvecklingsgrupper som är associerade med samma utvecklingsprojekt är associerade med samma labbet. Det möjliggör samma typer av principer, bilder och Stäng av principer som ska tillämpas på båda grupperna.

Du kan också behöva tänka på geografiska gränser. Utvecklare i norra östra USA (USA) kan till exempel använda ett labb som etablerats i östra usa2. Och utvecklare i Dallas, Texas, och Denver, Colorado kan dirigeras till att använda en resurs i södra centrala USA. Om det finns en gemensam ansträngning med en extern från tredje part, kan de tilldelas till ett labb som inte används av egna utvecklare.

Du kan också använda ett labb för ett specifikt projekt inom Azure DevOps-projekt. Sedan kan tillämpa du säkerhet via en angiven Azure Active Directory-grupp, vilket ger åtkomst till båda uppsättning resurser. Det virtuella nätverket som tilldelats labbet kan vara en annan gräns att konsolidera användare.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Hur kan vi för att förhindra borttagningen av resurser i ett laboratorium?
Vi rekommenderar att du anger rätt behörigheter på nivån labb så att endast behöriga användare kan ta bort resurser eller ändra principer för labbet. Utvecklare bör placeras i den **DevTest Labs-användare** grupp. Lead-utvecklare och infrastruktur lead bör vara den **DevTest Labs ägare**. Vi rekommenderar att du har bara två labbägare. Den här principen utökar mot kodlagringsplatsen att undvika skada. Labbanvändare ha behörighet att använda resurser, men kan inte uppdatera principer för labbet. Se följande artikel som visar en lista över roller och behörigheter som varje inbyggd grupp som har i ett laboratorium: [Lägg till ägare och användare i Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Hur gör jag för att dela en direktlänk till mitt labb?

1. I den [Azure-portalen](https://portal.azure.com)går du till labbet.
2. Kopiera den **lab URL** från din webbläsare och sedan dela den med dina labbanvändare.

> [!NOTE]
> Om en lab-användare är en extern användare som har ett Microsoft-konto, men som inte är medlem i din organisations Active Directory-instans, kan användaren visas ett felmeddelande när de försöker komma åt den delade länken. Om en extern användare får ett felmeddelande, be användaren att först markera användarens namn i det övre högra hörnet i Azure Portal. Sedan användaren kan välja den katalog där labbet finns i avsnittet Directory-menyn.

## <a name="virtual-machines"></a>Virtuella datorer

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Varför visas inte virtuella datorer på sidan virtuella datorer som syns i DevTest Labs?
När du skapar en virtuell dator i DevTest Labs, får du behörighet att komma åt den virtuella datorn. Du kan visa den virtuella datorn på sidan labs såväl på den **virtuella datorer** sidan. Användare som tillhör den **DevTest Labs ägare** rollen kan se alla virtuella datorer som har skapats i laboratoriet på testmiljön **alla virtuella datorer** sidan. Dock användare som har den **DevTest Labs-användare** rollen inte automatiskt beviljas läsbehörighet till VM-resurser som har skapats av andra användare. Därför visas inte dessa virtuella datorer på den **virtuella datorer** sidan.


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Hur gör jag skapa flera virtuella datorer från samma mall på samma gång?
Har du två alternativ för att skapa flera virtuella datorer samtidigt från samma mall:

- Du kan använda den [Azure DevOps-uppgifter tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- Du kan [Generera en Resource Manager-mall](devtest-lab-add-vm.md#save-azure-resource-manager-template) medan du skapar en virtuell dator och [distribuera Resource Manager-mall från Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).
- Du kan också ange mer än en instans av en dator som ska skapas under skapande av virtuella datorer. Läs mer om att skapa flera instanser av virtuella datorer i dokumentet du befinner dig på [skapar en virtuell dator i labbet](devtest-lab-add-vm.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Hur flyttar jag min befintliga Azure virtuella datorer i min labb i DevTest Labs?
Kopiera dina befintliga virtuella datorer på DevTest Labs:

1.  Kopiera VHD-filen för den befintliga virtuella datorn med hjälp av en [Windows PowerShell-skript](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Skapa den [anpassad avbildning](devtest-lab-create-template.md) i ditt labb i DevTest Labs.
3.  Skapa en virtuell dator i labbet från den anpassade avbildningen.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Kan jag koppla flera diskar till Mina virtuella datorer?

Ja, du kan koppla flera diskar till dina virtuella datorer.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Om jag vill använda en Windows OS-avbildning för testning, måste jag köpa en prenumeration på MSDN?
Om du vill använda Windows-klienten OS-avbildningar (Windows 7 eller senare) för din utveckling och testning i Azure, gör du något av följande steg:

- [Köp en MSDN-prenumeration](https://www.visualstudio.com/products/how-to-buy-vs).
- Om du har ett Enterprise-avtal kan du skapa en Azure-prenumeration med den [Enterprise – utveckling/testning-erbjudandet](https://azure.microsoft.com/offers/ms-azr-0148p).

Läs mer om Azure-krediter för varje MSDN-erbjudande, [månatliga Azure-kredit för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Hur jag för att automatisera processen med att ta bort alla virtuella datorer i min labb?
Som labbägare kan du ta bort virtuella datorer från ditt labb i Azure-portalen. Du kan också radera alla virtuella datorer i labbet genom att använda ett PowerShell-skript. I följande exempel visas under den **värden för att ändra** kommentar, ändra parametrarnas värden. Du kan hämta det prenumerations-ID och labResourceGroup labName värden från fönstret labb i Azure-portalen.

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>Miljöer 

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Hur kan jag använda Resource Manager-mallar i min DevTest Labs-miljö?
Du distribuera dina Resource Manager-mallar i en labb med hjälp av stegen i den [miljöer funktionen i DevTest Labs](devtest-lab-test-env.md) artikeln. I princip du checka in Resource Manager-mallar i en Git-lagringsplats (Azure-databaser eller GitHub) och lägga till en [privat lagringsplats för dina mallar](devtest-lab-test-env.md) till labbet. Det här scenariot kanske inte användbart om du använder labb till värddatorerna för utveckling, men kan vara användbart om du bygger en mellanlagringsmiljö, som är representativ för produktion.

Det är också värt att antalet virtuella datorer per labb eller per användare endast begränsar antalet datorer som skapats internt i själva labben och inte av några miljöer (Resource Manager-mallar).

## <a name="custom-images"></a>Anpassade avbildningar

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Hur ställer jag in ett enkelt repeterbar process att ta med min organisations anpassade avbildningar i en labb-miljö?
Se den här [video på från en avbildning Factory](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Det här scenariot är ett avancerat scenario och skript som angetts är bara exempel på skript. Om några förändringar krävs, måste du hantera och underhålla de skript som används i din miljö.

Detaljerad information om hur du skapar en avbildning factory finns i [skapa en anpassad avbildning fabrik i Azure DevTest Labs](image-factory-create.md). 

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Vad är skillnaden mellan en anpassad avbildning och en formel?
En anpassad avbildning är en hanterad avbildning. En formel är en avbildning som du kan konfigurera med ytterligare inställningar och sedan spara och återskapa. En anpassad avbildning vara att föredra om du vill att snabbt skapa flera miljöer genom att använda samma avbildning för basic, inte kan ändras. En formel kan vara bättre om du vill återskapa konfigurationen av den virtuella datorn med de senaste nyheterna, som en del av ett virtuellt nätverk eller undernät eller som en virtuell dator av en viss storlek. En mer detaljerad förklaring finns i [jämföra anpassade bilder och formler i DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>När ska jag använda en formel jämfört med anpassad avbildning?
Normalt den avgörande faktorn som i det här scenariot är kostnaden och återanvända. Om du har ett scenario där många användare/labs kräver en avbildning med en mängd program ovanpå basavbildningen kan minska du kostnaderna genom att skapa en anpassad avbildning. Det innebär att avbildningen skapas en gång. Det minskar tiden för installationen av den virtuella datorn och kostnader som uppstår på grund av den virtuella datorn körs när installationsprogrammet sker.

Ytterligare en faktor att notera är dock frekventa ändringarna till programpaketet. Om du kör varje dag bygger och kräver att programvaran på dina användares virtuella datorer, Överväg att använda en formel i stället för en anpassad avbildning.

En mer detaljerad förklaring finns i [jämföra anpassade bilder och formler](devtest-lab-comparing-vm-base-image-types.md) i DevTest Labs.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Hur jag för att automatisera processen att överföra VHD-filer för att skapa anpassade avbildningar?

Om du vill automatisera överför VHD-filer för att skapa anpassade avbildningar, har du två alternativ:

- Använd [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) att kopiera eller ladda upp VHD-filer till storage-kontot som är associerat med labbet.
- Använd [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). Storage Explorer är en fristående app som körs på Windows, OS X och Linux.

Hitta mållagringskontot som är associerat med ditt Labb:

1.  Logga in på [Azure Portal](https://portal.azure.com).
2.  På menyn till vänster väljer **resursgrupper**.
3.  Hitta och välj den resursgrupp som är associerat med ditt labb.
4.  Under **översikt**, väljer du något av storage-konton.
5.  Välj **Blobar**.
6.  Leta efter överföringar i listan. Om ingen sådan finns, återgår till steg 4 och prova ett annat lagringskonto.
7.  Använd den **URL** som mål i AzCopy-kommandot.

När ska jag använda en Azure Marketplace-avbildning jämfört med min egen anpassade organisationens avbildning?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>När ska jag använda en Azure Marketplace-avbildning jämfört med min egen anpassade organisationens avbildning?
Azure Marketplace ska användas som standard om du inte har specifika frågor eller organisationens krav. Några vanliga exempel är;

- Konfiguration av komplexa programvara som kräver ett program för att ingå som en del av basavbildningen.
- Installationen av ett program kan ta flera timmar, som inte är en effektiv användning av beräkningstid som ska läggas till på en Azure Marketplace-avbildning.
- Utvecklare och testare behöver snabbt åtkomst till en virtuell dator och vill minimera tiden för installationen av en ny virtuell dator.
- Efterlevnad eller föreskrifter (till exempel säkerhetsprinciper) som måste vara uppfyllda för alla datorer.
- Med hjälp av anpassade avbildningar bör inte ses som lätt. Du måste nu hanterar VHD-filer för de underliggande Källavbildningen medför de extra komplexiteten. Du måste också regelbundet uppdatera dessa grundläggande avbildningar med programuppdateringar. Dessa uppdateringar innehåller nya uppdateringar av operativsystemet (OS), och eventuella uppdateringar eller konfigurationsändringar som krävs för programpaketet själva.

## <a name="artifacts"></a>Artefakter

### <a name="what-are-artifacts"></a>Vad är artefakter?
Artefakter är anpassningsbara element som du kan använda för att distribuera de senaste bitarna eller distribuera utvecklingsverktyg till en virtuell dator. Koppla artefakter till den virtuella datorn när du skapar den virtuella datorn. När den virtuella datorn har etablerats artefakter att distribuera och konfigurera den virtuella datorn. Olika befintliga artefakter finns i vår [offentliga GitHub-lagringsplatsen](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Du kan också [skapa dina egna artefakter](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Min artefakt misslyckades under skapande av virtuell dator. Hur felsöker jag det?
Läs hur du hämtar loggarna för din misslyckade artefakten i [så diagnostisera fel i artefakter i DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>När ska en organisation använda offentliga artefaktlagringsplatsen jämfört med privata artefaktcentrallagret i DevTest Labs?
Den [offentliga artefaktlagringsplatsen](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) ger en inledande uppsättning programvarupaket som används mest. Det underlättar snabb distribution utan att investera tid att återskapa vanliga utvecklingsverktyg och tillägg. Du kan välja att distribuera sina egna privata lagringsplatsen. Du kan använda en offentlig och en privat lagringsplats parallellt. Du kan också välja att inaktivera den offentliga databasen. Villkoren för att distribuera en privat lagringsplats bör styras av följande frågor och överväganden:

- Har organisationen ett krav att ha företagets licensierad programvara som en del av sitt erbjudande för DevTest Labs? Om svaret är Ja, bör privata lagringsplatsen skapas.
- Organisationen utveckla anpassade program som ger en viss åtgärd som krävs som en del av den övergripande processen för etablering? Om svaret är Ja, bör en privat lagringsplats distribueras.
- Om organisationens principer kräver isolering och externa databaser inte som hanteras direkt konfiguration av organisationen, ska en privat artefaktcentrallagret distribueras. Som en del av den här processen, en inledande kopia av den offentliga databasen kopieras och integrerad med den privata lagringsplatsen. Den offentliga databasen kan sedan inaktiveras så att ingen inom organisationen kan komma åt den längre. Den här metoden tvingar alla användare i organisationen ska ha en enda lagringsplats som är godkänd av organisationen och minimera konfigurationsförändringar.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Bör en organisation planerar för en enda lagringsplats eller tillåta flera databaser?
Som en del av din organisations övergripande styrnings- och hanteringsstrategi för konfiguration rekommenderar vi att du använder en centraliserad databas. När du använder flera databaser, kan de bli silor av ohanterade programvara över tiden. Flera team kan använda artefakter från den här lagringsplatsen för sina projekt med en central databas. Den tillämpar standardisering, säkerhet, enkel hantering och eliminerar dubbelarbete. Som en del av centralisering rekommenderas följande metoder för hantering av långsiktig och hållbarhet:

- Associera Azure-databaser med samma Azure Active Directory-klient som använder Azure-prenumerationen för autentisering och auktorisering.
- Skapa en grupp med namnet `All DevTest Labs Developers` i Azure Active Directory som hanteras centralt. Alla utvecklare som bidrar till artefakt utveckling ska placeras i den här gruppen.
- Samma Azure Active Directory-grupp kan användas för att ge åtkomst till lagringsplatsen Azure-databaser och till labbet.
- I Azure-lagringsplatser, branchning eller förgreningen ska användas till en separat en i utveckling från den primära produktion-lagringsplatsen. Innehåll läggs endast till mastergrenen med en pull-begäran när du har rätt Kodgranskning. När koden granskaren godkänner ändringen, sammanfogar chefsutvecklare som ansvarar för underhåll av huvudgrenen, den uppdaterade koden.

## <a name="cicd-integration"></a>Integration av CI/CD

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>DevTest Labs integrera med min CI/CD-verktygskedja?
Om du använder Azure DevOps kan du använda en [DevTest Labs-uppgifter tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) att automatisera dina releasepipeline i DevTest Labs. Några av de uppgifter som du kan göra med det här tillägget är:

- Skapa och distribuera en virtuell dator automatiskt. Du kan också konfigurera den virtuella datorn med den senaste versionen med hjälp av Azure File Copy eller PowerShell Azure DevOps-tjänsterna uppgifter.
- Spara automatiskt tillståndet för en virtuell dator efter test för att återskapa en bugg i samma virtuella dator för vidare studier.
- Ta bort den virtuella datorn i slutet av versionspipelinen, när den inte längre behövs.

Följande blogginlägg erbjudandet vägledning och information om hur du använder tillägget Azure DevOps-tjänsterna:

- [DevTest Labs och Azure DevOps-tillägget](integrate-environments-devops-pipeline.md)
- [Distribuera en ny virtuell dator i en befintlig labb i DevTest Labs från Azure DevOps-tjänster](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Med Azure DevOps-tjänsterna versionshantering för kontinuerlig distribution till DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

För andra kontinuerlig integrering (CI) / kontinuerlig leverans (CD) verktygskedjor som du kan uppnå samma scenarier genom att distribuera [Azure Resource Manager-mallar](https://azure.microsoft.com/resources/templates/) med hjälp av [Azure PowerShell-cmdlets](../azure-resource-manager/resource-group-template-deploy.md) och [.NET SDK: er](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Du kan också använda [REST API: er för DevTest Labs](https://aka.ms/dtlrestapis) att integrera med din verktygskedja.

## <a name="networking"></a>Nätverk

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>När bör jag skapa ett nytt virtuellt nätverk för min labb-miljö och att använda ett befintligt virtuellt nätverk?
Om dina virtuella datorer behöver interagera med befintlig infrastruktur, Överväg att använda ett befintligt virtuellt nätverk i labb-miljö. Om du använder ExpressRoute, kan du minska mängden virtuella nätverk / undernät så att du inte Fragmentera ditt IP-adressutrymme som hämtar tilldelade för användning i prenumerationerna. 

Överväg att använda mönstret VNet peering här ([modell av typen Hub-Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)) för. Den här metoden möjliggör kommunikation för virtuellt nätverk/undernät mellan prenumerationer. I annat fall kan varje labb-miljö ha sitt eget virtuella nätverk. 

Det finns [gränser](../azure-subscription-service-limits.md) på antalet virtuella nätverk per prenumeration. Standardvärdet är 50, även om den här gränsen kan höjas upp till 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>När ska jag använda en delad IP och offentlig IP-adress jämfört med privata IP?
 
Om du använder en plats-till-plats VPN eller Express Route kan du använda privata IP-adresser så att dina datorer är tillgängliga via det interna nätverket, och kan inte användas via offentliga internet.

> [!NOTE]
> Labbägare kan ändra den här principen för undernätet för att säkerställa att ingen av misstag skapar offentliga IP-adresser för sina virtuella datorer. Prenumerationsägaren ska skapa en prenumerationsprincip som förhindrar att offentliga IP-adresser som skapas.

När du använder delade offentliga IP-adresser kan dela en offentlig IP-adress i de virtuella datorerna i ett labb. Den här metoden kan vara användbart när du behöver att undvika att tränga in begränsningar i den offentliga IP-adresser för en viss prenumeration.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Hur gör jag se till att utveckla och testa virtuella datorer kan inte nå det offentliga internet? Finns det några rekommenderade mönster du ställer in nätverkskonfigurationen?

Ja. Det finns två aspekter att tänka på – inkommande och utgående trafik.

- **Inkommande trafik** – om den virtuella datorn inte har en offentlig IP-adress, och sedan den inte går att nå via internet. En vanlig metod är att säkerställa att en princip på prenumerationsnivå har angetts, så att ingen användare kan skapa en offentlig IP-adress.
- **Utgående trafik** – om du vill förhindra att virtuella datorer direkt åtkomst till offentliga internet och tvinga trafik via en företagsbrandvägg, och du kan dirigera trafik lokalt via expressroute eller VPN, med hjälp av framtvingad routning.

> [!NOTE]
> Om du har en proxyserver som blockerar trafik utan proxyinställningar Glöm inte att lägga till undantag för den testmiljön artefaktlagringskonto.

Du kan också använda nätverkssäkerhetsgrupper för virtuella datorer eller undernät. Det här steget lägger till ytterligare ett lager för att tillåta / blockera trafik.

## <a name="troubleshooting"></a>Felsökning

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Varför inte mina befintliga virtuella nätverk sparas korrekt?
Ett sätt är att din virtuella nätverksnamnet innehåller punkter. I så fall, försök att ta bort punkter eller ersätta dem med bindestreck. Försök sedan igen att spara det virtuella nätverket.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Varför får jag en ”överordnad resursen hittades inte”-fel när jag etablerar en virtuell dator från PowerShell?
När en resurs är en överordnad till en annan resurs, måste den överordnade resursen finnas innan du skapar den underordnade resursen. Om den överordnade resursen inte finns kan du se en **ParentResourceNotFound** meddelande. Om du inte anger ett beroende på den överordnade resursen, kan den underordnade resursen distribueras innan överordnat.

Virtuella datorer är underordnade resurser under ett labb i en resursgrupp. När du använder Resource Manager-mallar för att distribuera virtuella datorer med hjälp av PowerShell, vara resursgruppens namn anges i PowerShell-skriptet resursgruppens namn labbet. Mer information finns i [felsöka vanliga Azure-distributionsfel](../azure-resource-manager/resource-manager-common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Var hittar jag mer information om fel om en VM-distributionen misslyckas?
Distributionsfel för virtuell dator som avbildas i aktivitetsloggarna. Du kan hitta lab VM aktivitetsloggar under **granskningsloggar** eller **VM diagnostics** på resurs-menyn på den lab VM sida (sidan visas när du har valt den virtuella datorn från den min lista över virtuella datorer).

Ibland inträffar distributionsfel innan du påbörjar distribution av virtuella datorer. Ett exempel är när gränsen för en resurs som har skapats med den virtuella datorn har överskridits. I det här fallet fångas felinformationen i labb-nivå aktivitetsloggar. Aktivitetsloggar är placerad längst ned i den **konfiguration och principer** inställningar. Mer information om hur du använder aktiviteten loggar i Azure finns i [visa aktivitetsloggar till granska åtgärder på resurser](../azure-resource-manager/resource-group-audit.md).




