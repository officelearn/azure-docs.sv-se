---
title: Vanliga frågor och svar om Azure DevTest Labs | Microsoft-dokument
description: Den här artikeln innehåller svar på några av de vanliga frågorna om Azure DevTest Labs.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: de99e9b1e4adceaf08beaf8ad3b5ea114b31a586
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270789"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs vanliga frågor och svar
Få svar på några av de vanligaste frågorna om Azure DevTest Labs.

## <a name="blog-post"></a>Blogginlägg
Vår DevTest Labs Team blogg har gått i pension från och med den 20 mars 2019. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Var kan jag spåra funktionsuppdateringar från och med nu?
Från och med nu kommer vi att publicera funktionsuppdateringar och informativa blogginlägg på Azure-bloggen och Azure-uppdateringar. Dessa blogginlägg kommer också att länka till vår dokumentation där det behövs.

Prenumerera på [DevTest Labs Azure-bloggen](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) och [DevTest Labs Azure-uppdateringar](https://azure.microsoft.com/updates/?product=devtest-lab) för att hålla dig informerad om nya funktioner i DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>Vad händer med de befintliga blogginläggen?
Vi arbetar för närvarande med att migrera befintliga blogginlägg (exklusive avbrottsuppdateringar) till vår [DevTest Labs-dokumentation](devtest-lab-overview.md). När MSDN-bloggen är föråldrad omdirigeras den till dokumentationsöversikten för DevTest Labs. När omdirigeras kan du söka efter artikeln du letar efter i rubriken Filter efter. Vi har inte migrerat alla inlägg ännu, men bör göras i slutet av denna månad. 


### <a name="where-do-i-see-outage-updates"></a>Var ser jag avbrottsuppdateringar?
Vi kommer att publicera avbrott uppdateringar med hjälp av vår Twitter handtag från och med nu. Följ oss på Twitter för att få senaste uppdateringarna om avbrott och kända buggar.

### <a name="twitter"></a>Twitter
Vår Twitter handtag:[@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Allmänt
### <a name="what-if-my-question-isnt-answered-here"></a>Tänk om min fråga inte besvaras här?
Om din fråga inte finns med här, låt oss veta, så att vi kan hjälpa dig att hitta ett svar.

- Post en fråga i slutet av denna FAQ.
- Om du vill nå en bredare målgrupp lägger du upp en fråga på [AZURE DevTest Labs MSDN-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Samarbeta med Azure DevTest Labs-teamet och andra medlemmar i communityn.
- Skicka dina begäranden och idéer till [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs)för funktionsförfrågningar .

### <a name="what-is-a-microsoft-account"></a>Vad är ett Microsoft-konto?
Ett Microsoft-konto är ett konto som du använder för nästan allt du gör med Microsoft-enheter och -tjänster. Det är en e-postadress och ett lösenord som du använder för att logga in på Skype, Outlook.com, OneDrive, Windows Phone, Azure och Xbox Live. Ett enda konto innebär att dina filer, foton, kontakter och inställningar kan följa dig på vilken enhet som helst.

> [!NOTE]
> Ett Microsoft-konto kallades tidigare ett Windows Live ID.

### <a name="why-should-i-use-azure-devtest-labs"></a>Varför ska jag använda Azure DevTest Labs?
Azure DevTest Labs kan spara tid och pengar för ditt team. Utvecklare kan skapa sina egna miljöer med hjälp av flera olika baser. De kan också använda artefakter för att snabbt distribuera och konfigurera program. Genom att använda anpassade avbildningar och formler kan du spara virtuella datorer som mallar och enkelt återskapa dem i hela teamet. DevTest Labs erbjuder också flera konfigurerbara principer som labbadministratörer kan använda för att minska avfallet och hantera ett teams miljöer. Dessa principer omfattar automatisk avstängning, kostnadströskel, maximal virtuella datorer per användare och maximal vm-storlek. En mer djupgående förklaring av DevTest Labs finns i [översikten](devtest-lab-overview.md) eller [introduktionsvideon](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Vad betyder "bekymmersfri självbetjäning"?
Bekymmersfri självbetjäning innebär att utvecklare och testare skapar sina egna miljöer efter behov. Administratörer har säkerheten att veta att DevTest Labs kan hjälpa till att ställa in lämplig åtkomst, minimera avfalls- och kontrollkostnader. Administratörer kan ange vilka vm-storlekar som är tillåtna, det maximala antalet virtuella datorer och när virtuella datorer startas och stängs av. DevTest Labs gör det också enkelt att övervaka kostnader och ställa in aviseringar, så att du kan hålla dig medveten om hur labbresurser används.

### <a name="how-can-i-use-devtest-labs"></a>Hur kan jag använda DevTest Labs?
DevTest Labs är användbart när du behöver utvecklings- eller testmiljöer och vill återskapa dem snabbt eller hantera dem med hjälp av kostnadsbesparande principer.
Här är några scenarier som våra kunder använder DevTest Labs för:

- Hantera utvecklings- och testmiljöer på ett ställe. Använd principer för att minska kostnaderna och skapa anpassade avbildningar för att dela byggen i hela teamet.
- Utveckla ett program med hjälp av anpassade avbildningar för att spara disktillståndet under utvecklingsfaserna.
- Spåra kostnad i förhållande till förloppet.
- Skapa masstestmiljöer för kvalitetssäkringstestning.
- Använd artefakter och formler för att enkelt konfigurera och återskapa ett program i olika miljöer.
- Distribuera virtuella datorer för hackathons (gemensamt utveckling eller testarbete) och sedan enkelt avetablera dem när händelsen avslutas.

### <a name="how-am-i-billed-for-devtest-labs"></a>Hur faktureras jag för DevTest Labs?
DevTest Labs är en kostnadsfri tjänst. Det är gratis att skapa labb och konfigurera principer, mallar och artefakter i DevTest Labs. Du betalar bara för Azure-resurser som används i dina labb, till exempel virtuella datorer, lagringskonton och virtuella nätverk. Mer information om kostnaden för labbresurser finns i [Azure DevTest Labs prissättning](https://azure.microsoft.com/pricing/details/devtest-lab/).

## <a name="security"></a>Säkerhet

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Vilka är de olika säkerhetsnivåerna i DevTest Labs?
Åtkomsten för säkerhet bestäms av rollbaserad åtkomstkontroll (RBAC). Om du vill veta hur åtkomst fungerar hjälper det att lära sig skillnaderna mellan en behörighet, en roll och ett scope, enligt definitionen i RBAC.

- **Behörighet**: En behörighet är en definierad åtkomst till en viss åtgärd. En behörighet kan till exempel läsas åtkomst till alla virtuella datorer.
- **Roll**: En roll är en uppsättning behörigheter som kan grupperas och tilldelas en användare. En användare med en prenumerationsägareroll har till exempel åtkomst till alla resurser i en prenumeration.
- **Scope**: Ett scope är en nivå i hierarkin för en Azure-resurs. Ett scope kan till exempel vara en resursgrupp, ett enda labb eller hela prenumerationen.

Inom ramen för DevTest Labs finns det två typer av roller som definierar användarbehörigheter:

- **Lab ägare**: En labbägare har tillgång till alla resurser i labbet. En labbägare kan ändra principer, läsa och skriva till alla virtuella datorer, ändra det virtuella nätverket och så vidare.
- **Labbanvändare:** En labbanvändare kan visa alla labbresurser, till exempel virtuella datorer, principer och virtuella nätverk. En labbanvändare kan dock inte ändra principer eller virtuella datorer som har skapats av andra användare.

Du kan också skapa anpassade roller i DevTest Labs. Mer information om hur du skapar anpassade roller i DevTest Labs finns i [Bevilja användarbehörigheter till specifika labbprinciper](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Eftersom scope är hierarkiska, när en användare har behörigheter i ett visst omfång, får användaren automatiskt dessa behörigheter på varje lägre nivåomfång i omfånget. Om en användare till exempel tilldelas rollen som prenumerationsägare har användaren åtkomst till alla resurser i en prenumeration. Dessa resurser omfattar virtuella datorer, virtuella nätverk och labb. En prenumerationsägare ärver automatiskt rollen som labbägare. Det motsatta är dock inte sant. En labbägare har åtkomst till ett labb, vilket är ett lägre omfång än prenumerationsnivån. Så en labbägare kan inte se virtuella datorer, virtuella nätverk eller andra resurser som finns utanför labbet.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Hur definierar jag rollbaserad åtkomstkontroll för mina DevTest Labs-miljöer för att säkerställa att IT-tjänster kan styra medan utvecklare/test kan utföra sitt arbete?
Det finns ett brett mönster, men detaljerna beror på din organisation.

Central IT bör endast äga det som är nödvändigt och göra det möjligt för projekt- och applikationsteamen att ha den kontrollnivå som krävs. Vanligtvis innebär det att central IT äger prenumerationen och hanterar centrala IT-funktioner som nätverkskonfigurationer. Ägaruppsättningen **för** en prenumeration ska vara liten. Dessa ägare kan nominera ytterligare ägare när det finns ett behov, eller tillämpa principer på prenumerationsnivå, till exempel "Ingen offentlig IP".

Det kan finnas en delmängd användare som kräver åtkomst över en prenumeration, till exempel Nivå 1- eller Nivå 2-stöd. I det här fallet rekommenderar vi att du ger dessa användare åtkomst **till deltagaren** så att de kan hantera resurserna, men inte ge användaråtkomst eller justera principer.

DevTest Labs-resursen ska ägas av ägare som ligger nära projekt-/programteamet. Det beror på att de förstår sina krav för maskiner och nödvändig programvara. I de flesta organisationer är ägaren till den här DevTest Labs-resursen ofta projektledare för projekt/utveckling. Den här ägaren kan hantera användare och principer i labbmiljön och hantera alla virtuella datorer i DevTest Labs-miljön.

Projekt-/programgruppsmedlemmarna ska läggas till i rollen **DevTest Labs-användare.** Dessa användare kan skapa virtuella datorer (i linje med policyerna för labb- och prenumerationsnivå). De kan också hantera sina egna virtuella datorer. De kan inte hantera virtuella datorer som tillhör andra användare.

Mer information finns i [Azure enterprise scaffold – prescriptive subscription governance documentation](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Hur skapar jag en roll så att användarna kan utföra en viss uppgift?
En omfattande artikel om hur du skapar anpassade roller och tilldelar behörigheter till en roll finns i [Bevilja användarbehörigheter till specifika labbprinciper](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Här är ett exempel på ett skript som skapar rollen **DevTest Labs Advanced User**, som har behörighet att starta och stoppa alla virtuella datorer i labbet:


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

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Hur kan en organisation se till att företagets säkerhetsprinciper finns på plats?
En organisation kan uppnå det genom att göra följande åtgärder:

- Utveckla och publicera en omfattande säkerhetspolicy. Principen beskriver reglerna för acceptabel användning som är associerade med hjälp av programvara, molntillgångar. Det definierar också vad som tydligt bryter mot policyn.
- Utveckla en anpassad avbildning, anpassade artefakter och en distributionsprocess som möjliggör orkestrering inom säkerhetssfären som definieras med active directory. Detta tillvägagångssätt upprätthåller företagets gräns och fastställer en gemensam uppsättning miljökontroller. Dessa kontroller mot miljön som en utvecklare kan tänka på när de utvecklar och följer en säker utvecklingslivscykel som en del av deras övergripande process. Målet är också att tillhandahålla en miljö som inte är alltför restriktiv som kan hindra utvecklingen, men en rimlig uppsättning kontroller. Gruppprinciperna på organisationsenheten (OU) som innehåller virtuella labbdatorer kan vara en delmängd av den totala gruppprincip som finns i produktionen. Alternativt kan de vara ytterligare en uppsättning för att på ett korrekt sätt minska eventuella identifierade risker.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Hur kan en organisation säkerställa dataintegritet för att säkerställa att återmotsättningsutvecklare inte kan ta bort kod eller införa skadlig kod eller icke godkänd programvara?
Det finns flera lager av kontroll för att minska hotet från externa konsulter, entreprenörer eller anställda som remoting in för att samarbeta i DevTest Labs.

Som tidigare nämnts måste det första steget ha en godtagbar användningspolicy utarbetad och definierad som tydligt beskriver konsekvenserna när någon bryter mot policyn.

Det första lagret med kontroller för fjärråtkomst är att tillämpa en fjärråtkomstprincip via en VPN-anslutning som inte är direkt ansluten till labbet.

Det andra kontrolllagret är att använda en uppsättning grupprincipobjekt som förhindrar att kopiera och klistra in via fjärrskrivbord. En nätverkspolitik skulle kunna implementeras för att inte tillåta utgående tjänster från miljön, till exempel FTP- och RDP-tjänster, ut ur miljön. Användardefinierad routning kan tvinga all Azure-nätverkstrafik tillbaka till lokalt, men routningen kunde inte ta hänsyn till alla webbadresser som kan tillåta överföring av data om de inte kontrolleras via en proxy för att skanna innehåll och sessioner. Offentliga IP-adresser kan begränsas inom det virtuella nätverket som stöder DevTest Labs för att inte tillåta överbryggning av en extern nätverksresurs.

I slutändan måste samma typ av begränsningar tillämpas i hela organisationen, vilket skulle ta hänsyn till alla möjliga metoder för flyttbara media eller externa webbadresser som kan acceptera ett inlägg av innehåll. Rådgör med din säkerhetspersonal för att granska och implementera en säkerhetsprincip. Fler rekommendationer finns i [Microsoft Cyber Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Labbkonfiguration

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Hur skapar jag ett labb från en Resource Manager-mall?
Vi erbjuder en [GitHub-lagringsplats för lab Azure Resource Manager-mallar](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) som du kan distribuera som den är eller ändra för att skapa anpassade mallar för dina labb. Varje mall har en länk för att distribuera labbet som det är i din egen Azure-prenumeration. Du kan också anpassa mallen och [distribuera med PowerShell eller Azure CLI](../azure-resource-manager/templates/deploy-powershell.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Kan jag ha alla virtuella datorer som ska skapas i en gemensam resursgrupp i stället ha varje dator i sin egen resursgrupp?
Ja, som labbägare kan du antingen låta labbet hantera resursgruppsallokering för dig eller låta alla virtuella datorer skapas i en gemensam resursgrupp som du anger.

Separat resursgruppsscenario:
-   DevTest Labs skapar en ny resursgrupp för varje offentlig/privat virtuell IP-dator som du snurrar upp
-   DevTest Labs skapar en resursgrupp för delade IP-datorer som tillhör samma storlek.

Scenario för gemensam resursgrupp:
-   Alla virtuella datorer snurras upp i den gemensamma resursgrupp som du anger. Läs mer [om resursgruppsallokering för labbet](https://aka.ms/RGControl).

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Hur upprätthåller jag en namngivningskonvention i hela min DevTest Labs-miljö?
Du kanske vill utöka aktuella företagsnamngivningskonventioner till Azure-åtgärder och göra dem konsekventa i devtest labs-miljön. När du distribuerar DevTest Labs rekommenderar vi att du har specifika startprinciper. Du distribuerar dessa principer med ett centralt skript och JSON-mallar för att framtvinga konsekvens. Namngivningsprinciper kan implementeras via Azure-principer som tillämpas på prenumerationsnivå. För JSON-exempel för Azure Policy finns i [Azure Policy-exempel](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Hur många labb kan jag skapa under samma prenumeration?
Det finns inte en specifik gräns för hur många labb som kan skapas per prenumeration. Mängden resurser som används per prenumeration är dock begränsad. Du kan läsa om [begränsningar och kvoter för Azure-prenumerationer](../azure-resource-manager/management/azure-subscription-service-limits.md) och [hur du ökar dessa gränser](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>Hur många virtuella datorer kan jag skapa per labb?
Det finns ingen specifik gräns för antalet virtuella datorer som kan skapas per labb. De resurser (VM-kärnor, offentliga IP-adresser och så vidare) som används är dock begränsade per prenumeration. Du kan läsa om [begränsningar och kvoter för Azure-prenumerationer](../azure-resource-manager/management/azure-subscription-service-limits.md) och [hur du ökar dessa gränser](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Hur avgör jag förhållandet mellan användare per labb och det totala antalet labb som behövs i en organisation?
Vi rekommenderar att affärsenheter och utvecklingsgrupper som är associerade med samma utvecklingsprojekt associeras med samma labb. Det gör att samma typer av principer, bilder och avstängningsprinciper kan tillämpas på båda grupperna.

Du kan också behöva överväga geografiska gränser. Utvecklare i nordöstra USA (USA) kan till exempel använda ett labb som etablerats i östra US2. Och utvecklare i Dallas, Texas och Denver, Colorado kan riktas för att använda en resurs i US South Central. Om det finns ett samarbete med en extern tredje part kan de tilldelas ett labb som inte används av interna utvecklare.

Du kan också använda ett labb för ett visst projekt inom Azure DevOps-projekt. Sedan tillämpar du säkerhet via en angiven Azure Active Directory-grupp, som ger åtkomst till båda resursuppsättningen. Det virtuella nätverket som tilldelats labbet kan vara en annan gräns för att konsolidera användare.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Hur kan vi förhindra att resurser tas bort i ett labb?
Vi rekommenderar att du anger rätt behörigheter på labbnivå så att endast behöriga användare kan ta bort resurser eller ändra labbprinciper. Utvecklare bör placeras i gruppen **DevTest Labs-användare.** Huvudutvecklaren eller infrastrukturledningen ska vara **DevTest Labs-ägaren**. Vi rekommenderar att du bara har två labbägare. Den här principen sträcker sig till kodarkivet för att undvika skador. Labbanvändare har behörighet att använda resurser men kan inte uppdatera labbprinciper. Se följande artikel där du visar de roller och rättigheter som varje inbyggd grupp har i ett labb: [Lägg till ägare och användare i Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Hur delar jag en direktlänk till mitt labb?

1. Gå till labbet i [Azure-portalen.](https://portal.azure.com)
2. Kopiera **labbadressen** från webbläsaren och dela den sedan med labbanvändarna.

> [!NOTE]
> Om en labbanvändare är en extern användare som har ett Microsoft-konto, men som inte är medlem i organisationens Active Directory-instans, kan användaren se ett felmeddelande när de försöker komma åt den delade länken. Om en extern användare ser ett felmeddelande ber du användaren att först välja sitt namn i det övre högra hörnet av Azure-portalen. Sedan, i katalogavsnittet på menyn, kan användaren välja den katalog där labbet finns.

## <a name="virtual-machines"></a>Virtuella datorer

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Varför kan jag inte se virtuella datorer på sidan Virtuella datorer som jag ser i DevTest Labs?
När du skapar en virtuell dator i DevTest Labs får du behörighet att komma åt den virtuella datorn. Du kan visa den virtuella datorn både på labbsidan och på sidan **Virtuella datorer.** Användare som **tilldelats rollen DevTest Labs-ägare** kan se alla virtuella datorer som har skapats i labbet på labbets sida **Alla virtuella datorer.** Användare som har rollen **DevTest Labs-användare** beviljas dock inte automatiskt läsbehörighet till vm-resurser som andra användare har skapat. Därför visas inte dessa virtuella datorer på sidan **Virtuella datorer.**


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Hur skapar jag flera virtuella datorer från samma mall samtidigt?
Du har två alternativ för att samtidigt skapa flera virtuella datorer från samma mall:

- Du kan använda [tillägget Azure DevOps-uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- Du kan [generera en Resource Manager-mall](devtest-lab-add-vm.md#save-azure-resource-manager-template) när du skapar en virtuell dator och [distribuera Resource Manager-mallen från Windows PowerShell](../azure-resource-manager/templates/deploy-powershell.md).
- Du kan också ange mer än en instans av en dator som ska skapas när den virtuella datorn skapas. Mer information om hur du skapar flera instanser av virtuella datorer finns i dokumentet när [du skapar en virtuell labbdator](devtest-lab-add-vm.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Hur flyttar jag mina befintliga virtuella Azure-datorer till mitt DevTest Labs-labb?
Så här kopierar du befintliga virtuella datorer till DevTest Labs:

1.  Kopiera VHD-filen för den befintliga virtuella datorn med hjälp av ett [Windows PowerShell-skript](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Skapa den [anpassade avbildningen](devtest-lab-create-template.md) i ditt DevTest Labs-labb.
3.  Skapa en virtuell dator i labbet från din anpassade avbildning.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Kan jag koppla flera diskar till mina virtuella datorer?

Ja, du kan koppla flera diskar till dina virtuella datorer.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Måste jag köpa en MSDN-prenumeration om jag vill använda en Windows OS-avbildning för testningen?
Så här använder du Windows-klientoperosavbildningar (Windows 7 eller en senare version) för din utveckling eller testning i Azure:

- [Köp en MSDN-prenumeration](https://www.visualstudio.com/products/how-to-buy-vs).
- Om du har ett enterprise-avtal skapar du en Azure-prenumeration med [Enterprise Dev/Test-erbjudandet](https://azure.microsoft.com/offers/ms-azr-0148p).

Mer information om Azure-krediter för varje MSDN-erbjudande finns i [Monthly Azure credit for Visual Studio subscribers](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Hur automatiserar jag processen med att ta bort alla virtuella datorer i mitt labb?
Som labbägare kan du ta bort virtuella datorer från ditt labb i Azure-portalen. Du kan också ta bort alla virtuella datorer i labbet med hjälp av ett PowerShell-skript. I följande exempel ändrar du parametervärdena under **de värden som ska ändras.** Du kan hämta värdena subscriptionId, labResourceGroup och labName från labbfönstret i Azure-portalen.

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
Du distribuerar dina Resource Manager-mallar i en DevTest Labs-miljö med hjälp av steg som nämns i [miljöfunktionen i devtestlabb-artikeln.](devtest-lab-test-env.md) I grund och botten kontrollerar du dina Resource Manager-mallar i en Git-databas (antingen Azure Repos eller GitHub) och lägger till en [privat databas för dina mallar](devtest-lab-test-env.md) i labbet. Det här scenariot kanske inte är användbart om du använder DevTest Labs som värd för utvecklingsdatorer, men kan vara användbart om du bygger en mellanlagringsmiljö, som är representativ för produktionen.

Det är också värt att notera att antalet virtuella datorer per labb eller per användare alternativet bara begränsar antalet datorer som skapats internt i labbet själv, och inte av några miljöer (Resource Manager mallar).

## <a name="custom-images"></a>Anpassade avbildningar

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Hur kan jag ställa in en lätt repeterbar process för att föra in mina anpassade organisationsavbildningar i en DevTest Labs-miljö?
Se den här [videon på Image Factory mönster](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Det här scenariot är ett avancerat scenario och skripten som tillhandahålls är endast exempelskript. Om några ändringar krävs måste du hantera och underhålla skripten som används i din miljö.

Detaljerad information om hur du skapar en avbildningsfabrik finns [i Skapa en anpassad avbildningsfabrik i Azure DevTest Labs](image-factory-create.md).

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Vad är skillnaden mellan en anpassad bild och en formel?
En anpassad avbildning är en hanterad avbildning. En formel är en bild som du kan konfigurera med ytterligare inställningar och sedan spara och reproducera. En anpassad bild kan vara att föredra om du snabbt vill skapa flera miljöer med samma grundläggande, oföränderliga bild. En formel kan vara bättre om du vill återskapa konfigurationen av den virtuella datorn med de senaste bitarna, som en del av ett virtuellt nätverk eller undernät, eller som en virtuell dator av en viss storlek. En mer djupgående förklaring finns i [Jämföra anpassade bilder och formler i DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>När ska jag använda en formel kontra anpassad bild?
Vanligtvis är den avgörande faktorn i det här scenariot kostnad och återanvändning. Om du har ett scenario där många användare / labs kräver en avbildning med mycket programvara ovanpå basavbildningen, då kan du minska kostnaderna genom att skapa en anpassad avbildning. Det betyder att bilden skapas en gång. Det minskar inställningstiden för den virtuella datorn och kostnaden som uppstår på grund av att den virtuella datorn körs när installationen inträffar.

En ytterligare faktor att notera är dock hur ofta du ändrar programvarupaketet. Om du kör dagliga versioner och kräver att programvaran ska finnas på användarnas virtuella datorer kan du överväga att använda en formel i stället för en anpassad avbildning.

En mer djupgående förklaring finns i [Jämföra anpassade bilder och formler](devtest-lab-comparing-vm-base-image-types.md) i DevTest Labs.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Hur automatiserar jag processen med att ladda upp VHD-filer för att skapa anpassade bilder?

Om du vill automatisera överföringen av VHD-filer för att skapa anpassade bilder har du två alternativ:

- Använd [AzCopy](../storage/common/storage-use-azcopy-v10.md) för att kopiera eller ladda upp VHD-filer till lagringskontot som är kopplat till labbet.
- Använd [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). Storage Explorer är en fristående app som körs på Windows, OS X och Linux.

Så här hittar du mållagringskontot som är kopplat till ditt labb:

1.  Logga in på [Azure-portalen](https://portal.azure.com).
2.  Välj **Resursgrupper på**den vänstra menyn .
3.  Sök efter och välj den resursgrupp som är kopplad till labbet.
4.  Välj ett av lagringskontona under **Översikt.**
5.  Välj **Blobbar**.
6.  Leta efter uppladdningar i listan. Om det inte finns något går du tillbaka till steg 4 och provar ett annat lagringskonto.
7.  Använd **webbadressen** som mål i kommandot AzCopy.

När ska jag använda en Azure Marketplace-avbildning jämfört med min egen anpassade organisationsavbildning?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>När ska jag använda en Azure Marketplace-avbildning jämfört med min egen anpassade organisationsavbildning?
Azure Marketplace bör användas som standard om du inte har specifika problem eller organisatoriska krav. Några vanliga exempel är;

- Komplex programvarukonfiguration som kräver att ett program inkluderas som en del av basavbildningen.
- Installation och installation av ett program kan ta många timmar, vilket inte är en effektiv användning av beräkningstid som ska läggas till på en Azure Marketplace-avbildning.
- Utvecklare och testare kräver åtkomst till en virtuell dator snabbt och vill minimera inställningstiden för en ny virtuell dator.
- Efterlevnad eller regelvillkor (till exempel säkerhetsprinciper) som måste finnas på plats för alla datorer.
- Använda anpassade bilder bör inte övervägas lätt. De introducerar extra komplexitet, eftersom du nu måste hantera VHD-filer för de underliggande basavbildningar. Du måste också rutinmässigt korrigera dessa basavbildningar med programuppdateringar. Dessa uppdateringar inkluderar nya operativsystemuppdateringar och eventuella uppdateringar eller konfigurationsändringar som behövs för själva programvarupaketet.

## <a name="artifacts"></a>Artefakter

### <a name="what-are-artifacts"></a>Vad är artefakter?
Artefakter är anpassningsbara element som du kan använda för att distribuera dina senaste bitar eller distribuera dina utvecklingsverktyg till en virtuell dator. Bifoga artefakter till den virtuella datorn när du skapar den virtuella datorn. När den virtuella datorn har etablerats distribuerar och konfigurerar artefakterna den virtuella datorn. Olika befintliga artefakter finns i vårt [offentliga GitHub-arkiv.](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) Du kan också [skapa egna artefakter.](devtest-lab-artifact-author.md)

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Min artefakt misslyckades när den virtuella datorn skapades. Hur felsöker jag det?
Mer information om hur du hämtar loggar för den misslyckade artefakten finns [i Så här diagnostiserar du artefaktfel i DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>När ska en organisation använda en offentlig artefaktdatabas kontra privat artefaktlagringsplats i DevTest Labs?
Den [offentliga artefaktdatabasen](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) innehåller en första uppsättning programpaket som oftast används. Det hjälper till med snabb distribution utan att behöva investera tid för att reproducera vanliga utvecklarverktyg och tillägg. Du kan välja att distribuera en egen privat databas. Du kan använda en offentlig och en privat databas parallellt. Du kan också välja att inaktivera den offentliga databasen. Kriterierna för att distribuera en privat databas bör styras av följande frågor och överväganden:

- Har organisationen ett krav på att ha företagslicensierad programvara som en del av deras DevTest Labs erbjudande? Om svaret är ja ska en privat databas skapas.
- Utvecklar organisationen anpassad programvara som tillhandahåller en specifik åtgärd, vilket krävs som en del av den övergripande etableringsprocessen? Om svaret är ja ska en privat databas distribueras.
- Om organisationens styrningsprincip kräver isolering och externa databaser inte är under direkt konfigurationshantering av organisationen, bör en privat artefaktdatabas distribueras. Som en del av den här processen kan en första kopia av den offentliga databasen kopieras och integreras med den privata databasen. Sedan kan den offentliga databasen inaktiveras så att ingen inom organisationen kan komma åt den längre. Den här metoden tvingar alla användare inom organisationen att bara ha en enda databas som är godkänd av organisationen och minimera konfigurationsdrift.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Ska en organisation planera för en enskild databas eller tillåta flera databaser?
Som en del av organisationens övergripande strategi för styrning och konfigurationshantering rekommenderar vi att du använder en centraliserad databas. När du använder flera databaser kan de bli silor av ohanterat program under tiden. Med en central databas kan flera team använda artefakter från den här databasen för sina projekt. Det upprätthåller standardisering, säkerhet, enkel hantering och eliminerar dubblering av insatser. Som en del av centraliseringen är följande åtgärder rekommenderade metoder för långsiktig förvaltning och hållbarhet:

- Associera Azure Repos med samma Azure Active Directory-klient som Azure-prenumerationen använder för autentisering och auktorisering.
- Skapa en `All DevTest Labs Developers` grupp med namnet Azure Active Directory som hanteras centralt. Alla utvecklare som bidrar till artefaktutveckling bör placeras i den här gruppen.
- Samma Azure Active Directory-grupp kan användas för att ge åtkomst till Azure Repos-databasen och till labbet.
- I Azure Repos bör förgrening eller forking användas för att separera en in-development-databas från den primära produktionsdatabasen. Innehåll läggs bara till i huvudgrenen med en pull-begäran efter en korrekt kodgranskning. När kodregransaren har godkänt ändringen sammanfogar en leadutvecklare, som ansvarar för underhållet av huvudgrenen, den uppdaterade koden.

## <a name="cicd-integration"></a>CI/CD-integrering

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Integrerar DevTest Labs med min CI/CD-verktygskedja?
Om du använder Azure DevOps kan du använda tillägget [DevTest Labs-uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) för att automatisera din versionspipeline i DevTest Labs. Några av de uppgifter som du kan göra med det här tillägget är:

- Skapa och distribuera en virtuell dator automatiskt. Du kan också konfigurera den virtuella datorn med den senaste versionen med hjälp av Azure File Copy eller PowerShell Azure DevOps Services-uppgifter.
- Fånga automatiskt tillståndet för en virtuell dator efter testning för att återskapa ett fel på samma virtuella dator för vidare undersökning.
- Ta bort den virtuella datorn i slutet av versionspipelinen, när den inte längre behövs.

Följande blogginlägg ger vägledning och information om hur du använder tillägget Azure DevOps Services:

- [DevTest Labs och Azure DevOps-tillägget](integrate-environments-devops-pipeline.md)
- [Distribuera en ny virtuell dator i ett befintligt DevTest Labs-labb från Azure DevOps Services](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Använda Azure DevOps Services utgivningshantering för kontinuerliga distributioner till DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

För andra cd-verktygskedjor för kontinuerlig integrering (CI)/kontinuerlig leverans (CD) kan du uppnå samma scenarier genom att distribuera [Azure Resource Manager-mallar](https://azure.microsoft.com/resources/templates/) med hjälp av [Azure PowerShell-cmdlets](../azure-resource-manager/templates/deploy-powershell.md) och [.NET SDK.](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/) Du kan också använda [REST API:er för DevTest Labs för](https://aka.ms/dtlrestapis) att integrera med din verktygskedja.

## <a name="networking"></a>Nätverk

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>När ska jag skapa ett nytt virtuellt nätverk för min DevTest Labs-miljö jämfört med ett befintligt virtuellt nätverk?
Om dina virtuella datorer behöver interagera med befintlig infrastruktur bör du överväga att använda ett befintligt virtuellt nätverk i devtestlabbmiljön. Om du använder ExpressRoute kanske du vill minimera mängden virtuella nätverk/undernät så att du inte fragmenterar ditt IP-adressutrymme som tilldelas för användning i prenumerationerna.

Överväg att använda VNet peering mönster här[(Hub-Spoke modell)](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)också. Den här metoden möjliggör kommunikation mellan virtuella nätverk och nät över prenumerationer. Annars kan varje DevTest Labs-miljö ha ett eget virtuellt nätverk.

Det finns [gränser för](../azure-resource-manager/management/azure-subscription-service-limits.md) antalet virtuella nätverk per prenumeration. Standardbeloppet är 50, men den här gränsen kan höjas till 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>När ska jag använda en delad IP kontra offentlig IP kontra privat IP?

Om du använder en VPN-tjänst eller expressväg från plats kan du överväga att använda privata IPs så att dina datorer är tillgängliga via ditt interna nätverk och otillgängliga via offentligt internet.

> [!NOTE]
> Labbägare kan ändra den här undernätsprincipen för att säkerställa att ingen av misstag skapar offentliga IP-adresser för sina virtuella datorer. Prenumerationsägaren bör skapa en prenumerationsprincip som förhindrar att offentliga IPs skapas.

När du använder delade offentliga IP-adresser delar de virtuella datorerna i ett labb en offentlig IP-adress. Den här metoden kan vara till hjälp när du behöver undvika att överskrida gränserna för offentliga IP-adresser för en viss prenumeration.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Hur ser jag till att utvecklings- och test virtuella datorer inte kan nå det offentliga internet? Finns det några rekommenderade mönster för att ställa in nätverkskonfiguration?

Ja. Det finns två aspekter att tänka på – inkommande och utgående trafik.

- **Inkommande trafik** – Om den virtuella datorn inte har en offentlig IP-adress kan den inte nås av Internet. En vanlig metod är att se till att en princip på prenumerationsnivå har angetts, så att ingen användare kan skapa en offentlig IP-adress.
- **Utgående trafik** – Om du vill förhindra virtuella datorer som ansluter till offentligt internet direkt och tvinga trafik genom en företagsbrandvägg kan du dirigera trafik lokalt via expressväg eller VPN genom att använda tvångsroutning.

> [!NOTE]
> Om du har en proxyserver som blockerar trafik utan proxyinställningar, glöm inte att lägga till undantag till labbets artefaktlagringskonto.

Du kan också använda nätverkssäkerhetsgrupper för virtuella datorer eller undernät. Detta steg lägger till ett extra lager av skydd för att tillåta / blockera trafik.

## <a name="troubleshooting"></a>Felsökning

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Varför sparas inte mitt befintliga virtuella nätverk på rätt sätt?
En möjlighet är att ditt virtuella nätverksnamn innehåller punkter. Om så är fallet kan du prova att ta bort perioderna eller ersätta dem med bindestreck. Försök sedan spara det virtuella nätverket igen.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Varför visas felet "Överordnad resurs hittades inte" när jag etablerar en virtuell dator från PowerShell?
När en resurs är överordnad till en annan resurs måste den överordnade resursen finnas innan du skapar den underordnade resursen. Om den överordnade resursen inte finns visas ett **ParentResourceNotFound-meddelande.** Om du inte anger ett beroende av den överordnade resursen kan den underordnade resursen distribueras före den överordnade resursen.

Virtuella datorer är underordnade resurser under ett labb i en resursgrupp. När du använder Resource Manager-mallar för att distribuera virtuella datorer med hjälp av PowerShell ska resursgruppsnamnet i PowerShell-skriptet vara labbets resursgruppsnamn. Mer information finns i [Felsöka vanliga Azure-distributionsfel](../azure-resource-manager/templates/common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Var hittar jag mer felinformation om en vm-distribution misslyckas?
VM-distributionsfel fångas in i aktivitetsloggar. Du hittar aktivitetsloggar för labb-VM under **Granskningsloggar** eller **diagnostik för virtuella datorer** på resursmenyn på labbets VM-sida (sidan visas när du har valt den virtuella datorn i listan Mina virtuella datorer).

Ibland uppstår distributionsfelet innan vm-distributionen börjar. Ett exempel är när prenumerationsgränsen för en resurs som skapades med den virtuella datorn överskrids. I det här fallet fångas felinformationen i aktivitetsloggarna på labbnivå. Aktivitetsloggar finns längst ned i **konfigurations- och** principerinställningarna. Mer information om hur du använder aktivitetsloggar i Azure finns i [Visa aktivitetsloggar för granskningsåtgärder på resurser](../azure-resource-manager/management/view-activity-logs.md).

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>Varför får jag felet "Plats är inte tillgängligt för resurstyp" när jag försöker skapa ett labb?
Ett felmeddelande kan visas som liknar följande när du försöker skapa ett labb:

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

Du kan lösa det här felet genom att göra något av följande:

#### <a name="option-1"></a>Alternativ 1
Kontrollera tillgängligheten för resurstypen i Azure-regioner på sidan [Produkter som är tillgängliga efter region.](https://azure.microsoft.com/global-infrastructure/services/) Om resurstypen inte är tillgänglig i en viss region stöder DevTest Labs inte skapandet av ett labb i den regionen. Välj en annan region när du skapar labbet.

#### <a name="option-2"></a>Alternativ 2
Om resurstypen är tillgänglig i din region kontrollerar du om den är registrerad med din prenumeration. Det kan göras på prenumerationsägaren nivå som visas i [den här artikeln](../azure-resource-manager/management/resource-providers-and-types.md).
