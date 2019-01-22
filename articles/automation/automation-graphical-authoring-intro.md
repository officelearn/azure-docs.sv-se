---
title: Grafisk redigering i Azure Automation
description: Grafisk redigering kan du skapa runbook-flöden för Azure Automation utan att arbeta med kod. Den här artikeln innehåller en introduktion till grafisk redigering och all information som behövs för att börja skapa en grafisk runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6d637436721ff464f58e41069bb00746fcd82410
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427244"
---
# <a name="graphical-authoring-in-azure-automation"></a>Grafisk redigering i Azure Automation

Grafisk redigering kan du skapa runbook-flöden för Azure Automation utan att komplexiteten i den underliggande koden för Windows PowerShell eller PowerShell-arbetsflöde. Du lägger till aktiviteter till arbetsytan från ett bibliotek med cmdlet: ar och runbooks, kopplar ihop dem och konfigurerar för att skapa ett arbetsflöde. Om du någonsin har arbetat med System Center Orchestrator eller Service Management Automation (SMA), sedan det borde se bekant ut till dig

Den här artikeln innehåller en introduktion till grafisk redigering och begrepp som du behöver att komma igång med att skapa en grafisk runbook.

## <a name="graphical-runbooks"></a>Grafiska runbooks

Alla runbooks i Azure Automation är Windows PowerShell-arbetsflöden. Grafisk och grafiska PowerShell Workflow-runbooks generera PowerShell-kod som körs av Automation arbetare, men det går inte att visa den eller ändra den direkt. En grafisk runbook kan konverteras till ett grafiskt PowerShell Workflow-runbook och vice versa, men de kan inte konverteras till en text runbook. En befintlig textbaserade runbook kan inte importeras till den grafiska redigeraren.

## <a name="overview-of-graphical-editor"></a>Översikt över grafiska redigeraren

Du kan öppna den grafiska redigeraren i Azure portal genom att skapa eller redigera en grafisk runbook.

![Grafiska arbetsyta](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

I följande avsnitt beskrivs kontrollerna i den grafiska redigeraren.

### <a name="canvas"></a>Canvas

Arbetsytan är där du utformar din runbook. Du lägger till aktiviteter från noder i bibliotekskontrollen runbook och Anslut dem med länkar att definiera logiken för runbook.

Du kan använda kontrollerna längst ned på arbetsytan för att zooma in och ut.

### <a name="library-control"></a>Bibliotekskontrollen

Bibliotekskontrollen är här du väljer [aktiviteter](#activities) att lägga till i din runbook. Du lägga till dem till arbetsytan där du ansluter dem till andra aktiviteter. Det innehåller fyra delar som beskrivs i följande tabell:

| Section | Beskrivning |
|:--- |:--- |
| Cmdlet: ar |Innehåller alla cmdletar som kan användas i din runbook. Cmdlet: ar är ordnade efter modulen. Alla moduler som du har installerat i ditt automation-konto är tillgängliga. |
| Runbooks |Innehåller runbooks i ditt automation-konto. Dessa runbooks kan läggas till arbetsytan som ska användas som underordnade runbooks. Endast runbook-flöden av typen samma kärnor som den runbook som redigeras visas; för grafiska visas runbooks endast PowerShell-baserade runbooks, medan för grafiska PowerShell Workflow-runbooks endast PowerShell-arbetsflöde-baserade runbooks visas. |
| Tillgångar |Innehåller den [automationstillgångar](https://msdn.microsoft.com/library/dn939988.aspx) i ditt automation-konto som kan användas i din runbook. När du lägger till en tillgång till en runbook, läggs en arbetsflödesaktivitet som hämtar den markerade tillgången. När det gäller variabler för tillgångar, kan du välja om du vill lägga till en aktivitet för att hämta variabeln eller Ställ in variabeln. |
| Runbook-kontroll |Innehåller runbook kontrollaktiviteter som kan användas i din aktuella runbook. En *knutpunkt* tar flera inmatningar och väntar tills alla har slutförts innan du fortsätter arbetsflödet. En *kod* aktivitet kör en eller flera rader med PowerShell eller PowerShell-arbetsflöde kod beroende på vilken typ av grafisk runbook. Du kan använda den här aktiviteten för anpassad kod eller funktioner som är svåra att uppnå med andra aktiviteter. |

### <a name="configuration-control"></a>Konfigurationskontroll

Konfigurationskontrollen är där du kan ange information för ett objekt som valts på arbetsytan. Egenskaperna som är tillgängliga i den här kontrollen beror på vilken typ av objekt som valts. När du väljer ett alternativ i konfigurationskontrollen den ytterligare blad öppnas för att tillhandahålla ytterligare information.

### <a name="test-control"></a>Testa kontrollen

Testa kontrollen visas inte när den grafiska redigeraren startas första gången. Den öppnas när du interaktivt [testa en grafisk runbook](#graphical-runbook-procedures).

## <a name="graphical-runbook-procedures"></a>Grafisk runbook procedurer

### <a name="exporting-and-importing-a-graphical-runbook"></a>Exportera och importera en grafisk runbook

Du kan bara exportera den publicerade versionen av en grafisk runbook. Om runbooken inte har publicerats ännu, kommer **exportera** knappen är inaktiverad. När du klickar på den **exportera** knapp, runbook laddas ned till den lokala datorn. Filens namn matchar namnet på en runbook med en *graphrunbook* tillägget.

Du kan importera en grafisk eller grafiskt PowerShell-arbetsflöde runbook-fil genom att välja den **importera** alternativet när du lägger till en runbook. När du väljer filen som ska importeras, kan du behålla samma **namn** eller ange ett nytt lösenord. Fältet Typ av Runbook visar vilken typ av runbook när den utvärderar den valda filen och om du försöker att välja en annan typ som inte är korrekt, ett meddelande visas om det finns konflikter och under konverteringen, det kan finnas syntax fel uppstod.

![Importera runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Testa en grafisk runbook

Du kan testa utkastversionen för en runbook i Azure portal medan lämnar den publicerade versionen av runbooken har inte ändrats och du kan testa en ny runbook innan den har publicerats. På så sätt kan du kontrollera att runbooken fungerar korrekt innan du byter ut den publicerade versionen. När du testar en runbook körs utkast-runbooken och alla åtgärder som den utför slutförs. Ingen jobbhistorik skapas, men utdata visas i rutan Testutdata.

Öppna Test-kontroll för en runbook genom att öppna runbook för att redigera och klicka sedan på den **Testfönster** knappen.

Testa kontrollen uppmanar för någon indataparametrar och du kan starta runbooken genom att klicka på den **starta** knappen.

### <a name="publishing-a-graphical-runbook"></a>Publicera en grafisk runbook

Varje runbook i Azure Automation har ett utkast och publicerad version. Bara den publicerade versionen är tillgängligt och kan köras och bara utkastet som kan redigeras. Den publicerade versionen påverkas inte av ändringar i utkastet. När utkastet kommer att vara tillgängliga, publicerar du det, som skriver över den publicerade versionen med utkastversionen.

Du kan publicera en grafisk runbook genom att öppna runbook för att redigera och klicka sedan på den **publicera** knappen.

När en runbook inte har ännu publicerats, har den statusen **New**. När den publiceras har den statusen **publicerad**. Om du redigerar en runbook när den har publicerats och utkast och publicerad versioner är olika runbook-jobbet har statusen **i Redigera**.

![Runbook-status](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Du har också möjlighet att återgå till den publicerade versionen av en runbook. Detta raderas alla ändringar som gjorts sedan runbook senast publicerades och ersätter Utkastversionen av runbooken med den publicerade versionen.

## <a name="activities"></a>Aktiviteter

Aktiviteter är byggblocken i en runbook. En aktivitet kan vara en PowerShell-cmdlet, en underordnad runbook eller en arbetsflödesaktivitet. Du lägger till en aktivitet i runbook genom att högerklicka i bibliotekskontrollen och välja **Lägg till på ytan**. Du kan klicka och dra aktiviteten för att placera den var som helst på arbetsytan som du vill. Platsen för aktiviteten på arbetsytan påverkar inte användningen av runbooken på något sätt. Du kan utforma din runbook men du märker den lämpligaste att visualisera dess drift.

![Lägg till på ytan](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Välj aktiviteten på arbetsytan för att konfigurera dess egenskaper och parametrar i bladet. Du kan ändra den **etikett** för aktiviteten till något som är beskrivande för dig. Ursprungliga cmdleten körs fortfarande, du helt enkelt ändrar dess visningsnamn som används i den grafiska redigeraren. Etiketten måste vara unika inom en runbook.

### <a name="parameter-sets"></a>Parameteruppsättningar

En parameteruppsättning definierar de obligatoriska och valfria parametrar som accepterar värden för en viss cmdlet. Alla cmdlets har minst en parameter som anger och vissa har flera. Om en cmdlet har flera parameteruppsättningar, måste du välja vilka som du använder innan du kan konfigurera parametrar. De parametrar som du kan konfigurera beror på den parameteruppsättning du väljer. Du kan ändra parameteruppsättningen som används av en aktivitet genom att välja **parameteruppsättning** och välja en annan uppsättning. I det här fallet försvinner alla parametervärden som du har konfigurerat.

I följande exempel visas har cmdleten Get-AzureRmVM tre parameteruppsättningar. Du kan inte konfigurera parametervärden förrän du väljer en av parameteruppsättningar. Parameteruppsättningen ListVirtualMachineInResourceGroupParamSet är för att returnera alla virtuella datorer i en resursgrupp och har en valfri parameter. Den **GetVirtualMachineInResourceGroupParamSet** är för att ange den virtuella datorn som du vill gå tillbaka och har två obligatoriskt och en valfri parameter.

![Parameteruppsättning](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Parametervärden

När du anger ett värde för en parameter kan välja du en datakälla för att avgöra hur värdet har angetts. De datakällor som är tillgängliga för en viss parameter är beroende av de giltiga värdena för den parametern. Till exempel är Null inte ett tillgängligt alternativ för en parameter som inte tillåter null-värden.

| Datakälla | Beskrivning |
|:--- |:--- |
| Konstant värde |Ange ett värde för parametern. Detta är endast tillgängligt för följande datatyper: Int32, Int64, sträng, booleskt värde, DateTime, växla. |
| Aktivitetsutdata |Utdata från en aktivitet som föregår den aktuella aktiviteten i arbetsflödet. Alla giltiga aktiviteter visas. Välj bara aktiviteten att använda dess utdata för parametervärdet. Om aktiviteten matar ut ett objekt med flera egenskaper, kan du ange namnet på egenskapen när du har valt aktiviteten. |
| Indata för Runbook |Välj en runbook-indataparameter som indata för Aktivitetsparametern. |
| Variabel tillgång |Välj ett Automation-variabel som indata. |
| Autentiseringstillgång |Välj ett Automation-autentiseringsuppgift som indata. |
| Certifikattillgång |Välj ett Automation-certifikat som indata. |
| Anslutningstillgång |Välj ett Automation-anslutningens som indata. |
| PowerShell-uttryck |Ange enkla [PowerShell-uttryck](#powershell-expressions). Uttrycket utvärderas innan aktiviteten och resultatet som används för parametervärdet. Du kan använda variabler för att referera till utdata för en aktivitet eller en indataparameter för runbook. |
| Inte konfigurerad |Tar bort ett värde som konfigurerats tidigare. |

#### <a name="optional-additional-parameters"></a>Valfria ytterligare parametrar

Alla cmdletar har möjlighet att ange ytterligare parametrar. Det här är parametrarna för PowerShell eller andra anpassade parametrar. Visas en textruta där du kan ange parametrar med PowerShell-syntax. Till exempel vill använda den **utförlig** gemensamma parametern om du anger **”-utförlig: $True”**.

### <a name="retry-activity"></a>Gör om aktivitet

**Funktionssätt för återförsök** gör att en aktivitet som ska köras flera gånger tills ett visst villkor är uppfyllt, ungefär som en loop. Du kan använda den här funktionen för aktiviteter som ska köras flera gånger, är felbenägna och kanske behöver fler än en försök för att lyckas, eller testa utdata-information för aktiviteten för giltiga data.

När du aktiverar återförsök för en aktivitet kan ange du en fördröjning och ett villkor. Fördröjningen är den tid (mäts i sekunder eller minuter) att runbook ska vänta innan den körs aktiviteten igen. Om ingen fördröjning anges, körs aktiviteten igen omedelbart när den är klar.

![Försök fördröjningen för aktivitet](media/automation-graphical-authoring-intro/retry-delay.png)

Villkor för återförsök är ett PowerShell-uttryck som utvärderas efter varje gång som aktiviteten körs. Om uttrycket är sant, sedan körs aktiviteten igen. Om uttrycket är FALSKT, sedan aktiviteten körs inte igen och runbook går vidare till nästa aktivitet.

![Försök fördröjningen för aktivitet](media/automation-graphical-authoring-intro/retry-condition.png)

Villkor för återförsök kan använda en variabel med namnet $RetryData som ger åtkomst till information om återförsök för aktiviteten. Den här variabeln har egenskaper i följande tabell:

| Egenskap  | Beskrivning |
|:--- |:--- |
| NumberOfAttempts |Antal gånger som aktiviteten har körts. |
| Resultat |Utdata från den senaste körningen av aktiviteten. |
| TotalDuration |Tidsgränsen har förflutit sedan aktiviteten startades första gången. |
| StartedAt |Tid i UTC-format som aktiviteten startades först. |

Följande är exempel på aktivitet försök villkor.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

När du har konfigurerat ett villkor för återförsök för en aktivitet innehåller två visuella tips för att påminna dig om aktiviteten. En presenteras i aktiviteten och den andra är när du kontrollerar konfigurationen av aktiviteten.

![Visuella indikatorer för återförsök för aktivitet](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Arbetsflödeskontroll för skript

En kontroll för kod är en särskild aktivitet som accepterar PowerShell eller PowerShell-arbetsflöde skriptet beroende på vilken typ av grafisk runbook som skapats för att tillhandahålla funktioner som annars inte kanske tillgänglig. Det kan inte acceptera parametrar, men den kan använda variabler för aktivitetens utdata och runbook-indataparametrar. Inga utdata för aktiviteten har lagts till i databussen om den inte har ingen utgående länkar i så fall läggs den till utdataporten för runbook.

Följande kod utför till exempel datumberäkningar med hjälp av en runbook inkommande variabel med namnet $NumberOfDays. Sedan skickar den ett beräknat datum-tid som utdata som ska användas av efterföljande aktiviteter i runbook.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Länkar och arbetsflöde

En **länk** ansluter två aktiviteter i en grafisk runbook. Den visas på arbetsytan som en pil som pekar från källaktiviteten målaktiviteten. Aktiviteterna körs i riktning på pilen med målaktiviteten startar när källaktiviteten har slutförts.

### <a name="create-a-link"></a>Skapa en länk

Skapa en länk mellan två aktiviteter genom att välja källaktiviteten och klicka på cirkeln längst ned i formen. Dra pilen till målaktiviteten och versionen.

![Skapa en länk](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Välj länken för att konfigurera dess egenskaper i bladet. Detta omfattar länktyp som beskrivs i följande tabell:

| Länktyp | Beskrivning |
|:--- |:--- |
| Pipeline |Målaktiviteten körs en gång för varje objektutdata från källaktiviteten. Målaktiviteten fungerar inte om källaktiviteten leder till att inga utdata. Utdata från källaktiviteten är tillgängligt som ett objekt. |
| Sekvens |Målaktiviteten körs bara en gång. Den tar emot en matris med objekt från källaktiviteten. Utdata från källaktiviteten är tillgänglig som en matris med objekt. |

### <a name="starting-activity"></a>Startar aktivitet

En grafisk runbook startas med aktiviteter som inte har en inkommande anslutning. Det här är ofta bara en aktivitet som skulle fungera som från aktiviteten för runbook. Om flera aktiviteter inte har en inkommande anslutning, startar runbook genom att köra dem parallellt. Det följer länkarna för att köra andra aktiviteter som var och en är klar.

### <a name="conditions"></a>Villkor

När du anger ett villkor på en länk målaktiviteten endast körs om villkoret är sant. Du använder vanligtvis en $ActivityOutput variabel i ett villkor för att hämta utdata från källaktiviteten

Du anger ett villkor för ett enda objekt för en pipelinelänk, och villkoret utvärderas för varje objektutdata av källaktiviteten. Målaktiviteten körs sedan för varje objekt som uppfyller villkoret. Till exempel med en Källaktivitet för Get-AzureRmVm följande syntax kan användas för en villkorlig pipelinelänk för att hämta endast virtuella datorer i resursgruppen med namnet *Group1*.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

För en sekvens-länk utvärderas bara villkoret som de en gång eftersom returneras en matris som innehåller alla objekt utdata från källaktiviteten. Det innebär en sekvens-länk kan inte användas för filtrering som en pipelinelänk, men bara avgör huruvida nästa aktivitet körs. Ta till exempel följande uppsättning aktiviteter i vår starta VM-runbook.

![Villkorlig länk med sekvenser](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Det finns tre olika sekvens länkar som verifierar värden angavs till två indataparametrar för runbook som representerar virtuella datorns namn och namn på resursgrupp för att avgöra vilket är den lämplig åtgärden att vidta - starta en enskild virtuell dator, starta alla virtuella datorer i resursen gruppen eller alla virtuella datorer i en prenumeration. Här är villkorsstyrd logik för sekvens-länk mellan Anslut till Azure och hämta enskild virtuell dator:

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

När du använder en villkorlig länk kan filtreras data som är tillgängliga från källaktiviteten till andra aktiviteter i den grenen av villkoret. Om en aktivitet är källan till flera länkar, beror data som är tillgängliga för aktiviteter i varje gren på villkor i länken som ansluter till den grenen.

Till exempel den **Start-AzureRmVm** nedan runbook-aktivitet startar alla virtuella datorer. Den har två villkorliga länkar. Den första villkorlig länken använder uttrycket *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - eq $true* att filtrera om Start-AzureRmVm-aktiviteten har slutförts. Andra använder uttrycket *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - ne $true* att filtrera om aktiviteten Start-AzureRmVm inte kunde starta den virtuella datorn.

![Exempel på villkorlig länk](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Alla aktiviteter som följer den första länken och använder aktivitetens utdata från Get-AzureVM får endast de virtuella datorerna som startades vid den tidpunkt då Get-AzureVM kördes. En aktivitet som följer den andra länken hämtar endast de virtuella datorerna som har stoppats vid den tidpunkt då Get-AzureVM kördes. Alla aktiviteter som följa länken tredje hämtar alla virtuella datorer, oavsett deras körtillstånd.

### <a name="junctions"></a>Vägkorsningar

Knutpunkt är en särskild aktivitet som väntar tills alla inkommande grenar har slutförts. På så sätt kan du köra flera aktiviteter parallellt och att alla har slutfört innan du fortsätter.

Medan en knutpunkt kan ha ett obegränsat antal inkommande länkar, kan inte mer än ett av dessa länkar vara en pipeline. Antal inkommande sekvens länkar begränsas inte. Du kan skapa knutpunkten med flera inkommande pipeline-länkar och spara runbooken, men det misslyckas när den körs.

Exemplet nedan är en del av en runbook som startar en uppsättning virtuella datorer vid nedladdning av korrigeringar som ska tillämpas på dessa datorer samtidigt. Knutpunkt används för att se till att båda processer måste slutföras innan runbook fortsätter.

![Knutpunkt](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cykler

En cykel är när en mål-aktivitet länkar tillbaka till dess källaktiviteten eller till en annan aktivitet som så småningom länkar tillbaka till källan. Cykler är inte tillåtna i grafisk redigering. Om din runbook har en cykel, sparar korrekt, men får ett fel när den körs.

![Cykel](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Dela data mellan aktiviteter

Alla data som är resultatet av en aktivitet med en utgående anslutning skrivs till den *databussen* för runbook. Alla aktiviteter i runbook kan använda data på i databussen för att fylla i parametervärden eller inkludera i skriptkod. En aktivitet kan komma åt utdata från alla föregående aktiviteter i arbetsflödet.

Hur data skrivs i databussen beror på vilken typ av länk på aktiviteten. För en **pipeline**, data returneras som multipler objekt. För en **sekvens** länken, data returneras som en matris. Om det finns bara ett värde, är det utdata som en enda element-matris.

Du kan komma åt data på i databussen med någon av två metoder. Först använder en **Aktivitetsutdata** datakälla att fylla i en parameter för en annan aktivitet. Om resultatet är ett objekt, kan du ange en enskild egenskap.

![Aktivitetsutdata](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Du kan också hämta utdata för en aktivitet i en **PowerShell-uttryck** datakälla eller från en **Arbetsflödesskript** aktivitet med en ActivityOutput-variabel. Om resultatet är ett objekt, kan du ange en enskild egenskap. ActivityOutput variabler använder du följande syntax.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Kontrollpunkter

Du kan ange [kontrollpunkter](automation-powershell-workflow.md#checkpoints) i en grafiskt PowerShell Workflow-runbook genom att välja *kontrollpunkt runbook* för varje aktivitet. Detta leder till en kontrollpunkt anges när aktiviteten körs.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Kontrollpunkter är endast aktiverad i grafiska PowerShell Workflow-runbooks, det är inte tillgänglig i grafiska runbooks. Om runbook använder Azure-cmdletar, ska du följa eventuella med kontrollpunkt aktivitet med en Connect-AzureRmAccount om runbooken har pausats och startar om från den här kontrollpunkten på en annan arbetsprocess.

## <a name="authenticating-to-azure-resources"></a>Autentisera till Azure-resurser

Runbooks i Azure Automation som hanterar Azure-resurser kräver autentisering till Azure. Den [kör som-konto](automation-create-runas-account.md) (kallas även ett huvudnamn för tjänsten) är standardmetoden för att få åtkomst till Azure Resource Manager-resurser i din prenumeration med Automation-runbooks. Du kan lägga till den här funktionen en grafisk runbook genom att lägga till den **AzureRunAsConnection** anslutningstillgång som använder PowerShell [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) cmdlet, och [ Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet på arbetsytan. Detta illustreras i följande exempel:

![Kör som autentiseringsaktiviteter](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

Aktiviteten hämta kör som-anslutning (det vill säga Get-AutomationConnection), har konfigurerats med ett konstant värde-datakälla med namnet AzureRunAsConnection.

![Kör som-konfiguration-anslutning](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Nästa aktivitet, Connect-AzureRmAccount, lägger till autentiserade kör som-kontot för användning i runbooken.

![Connect-AzureRmAccount-parameteruppsättningen](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

> [!IMPORTANT]
> **Add-AzureRmAccount** är nu ett alias för **Connect-AzureRMAccount**. När sökningen biblioteket objekt, om du inte ser **Connect-AzureRMAccount**, du kan använda **Add-AzureRmAccount**, eller så kan du uppdatera dina moduler i ditt Automation-konto.

För parametrarna **APPLICATIONID**, **CERTIFICATETHUMBPRINT**, och **TENANTID** måste du ange namnet på egenskapen för fältsökväg eftersom aktiviteten matar ut ett objekt med flera egenskaper. Annars när du kör en runbook misslyckas försöker autentisera. Det här är vad du behöver minst att autentisera din runbook med Kör som-kontot.

Att bibehålla kompatibilitet bakåt för prenumeranter som har skapat ett Automation-konto med en [Azure AD-användarkonto](automation-create-aduser-account.md) att hantera klassiska Azure eller Azure Resource Manager-resurser, metod för att verifiera är den Lägg till-AzureAccount cmdlet med ett [autentiseringstillgång](automation-credentials.md) som representerar en Active Directory-användare med tillgång till Azure-konto.

Du kan lägga till den här funktionen en grafisk runbook genom att lägga till en autentiseringsuppgifttillgång till arbetsytan för följt av en Add-AzureAccount-aktivitet. Lägg till-AzureAccount använder aktiviteten autentiseringsuppgift för indata. Detta illustreras i följande exempel:

![Autentiseringsaktiviteter](media/automation-graphical-authoring-intro/authentication-activities.png)

Du måste autentisera i början av runbooken och efter varje kontrollpunkt. Det innebär att lägga till en tillägg Add-AzureAccount aktivitet efter eventuella Checkpoint-Workflow aktivitet. Du behöver inte en ytterligare autentiseringsuppgifter aktivitet eftersom du kan använda samma

![Aktivitetsutdata](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Runbook indata och utdata

### <a name="runbook-input"></a>Indata för Runbook

En runbook kan kräva indata från en användare när de startar sin runbook via Azure-portalen eller från en annan runbook om den aktuella som används som en underordnad.
Om du har en runbook som skapar en virtuell dator kan behöva du till exempel ge information om exempelvis namnet för den virtuella datorn och andra egenskaper varje gång du startar runbooken.

Du godkänner indata för en runbook genom att definiera en eller flera indataparametrar. Du ange värden för dessa parametrar varje gång runbook startas. När du startar en runbook med Azure-portalen uppmanas du att ange värden för var och en av runbook-indataparametrar.

Du kan komma åt indataparametrar för en runbook genom att klicka på den **indata och utdata** i runbook-verktygsfältet.

Då öppnas det **indata och utdata** kontroll där du kan redigera en befintlig indataparameter eller skapa en ny genom att klicka på **Lägg till indata**.

![Lägg till indata](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Varje indataparameter definieras av egenskaperna i följande tabell:

| Egenskap  | Beskrivning |
|:--- |:--- |
| Namn |Det unika namnet för parametern. Detta får bara innehålla alfanumeriska tecken och får inte innehålla blanksteg. |
| Beskrivning |En valfri beskrivning av Indataparametern. |
| Typ |Datatypen som förväntat för parametervärdet. Azure-portalen ger en lämplig kontroll för datatypen för varje parameter vid fråga om indata. |
| Obligatorisk |Anger om ett värde måste anges för parametern. Runbook kan inte startas om du inte anger ett värde för varje obligatorisk parameter som inte har något definierat standardvärde. |
| Standardvärde |Anger vilket värde som ska användas för parametern om inget anges. Detta kan antingen vara Null eller ett specifikt värde. |

### <a name="runbook-output"></a>Utdata från Runbooks

Data som skapats av alla aktiviteter som inte har en utgående anslutning har sparats till den [utdata från runbooken](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). Utdata sparas med runbook-jobbet och är tillgänglig för en överordnad runbook när runbook används som en underordnad.

## <a name="powershell-expressions"></a>PowerShell-uttryck

En av fördelarna med grafisk redigering är att ge dig möjlighet att skapa en runbook med minimal kunskap om PowerShell. För närvarande kan du behöver veta lite PowerShell även om för att fylla i vissa [parametervärden](#activities) och för inställningen [länka villkor](#links-and-workflow). Det här avsnittet ger en snabb introduktion till PowerShell-uttryck för de användare som inte kanske är bekant med den. Fullständig information om PowerShell finns på [med Windows PowerShell-skript](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Datakälla för PowerShell-uttryck
Du kan använda ett PowerShell-uttryck som en datakälla för att fylla i värdet för en [Aktivitetsparametern](#activities) med resultaten från vissa PowerShell-kod. Detta kan vara en enda rad kod som utför någon funktion som är enkla eller flera rader som utför komplex logik. Alla utdata från ett kommando som inte har tilldelats en variabel är utdata till parametervärdet.

Följande kommando skulle till exempel visa det aktuella datumet.

Följande kommando skulle till exempel visa det aktuella datumet.

```powershell-interactive
Get-Date
```

Följande kommandon skapar en sträng från dagens datum och tilldela den till en variabel. Innehållet i variabeln skickas sedan till utdata

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Följande kommandon utvärdera aktuellt datum och returnerar en sträng som anger om den aktuella dagen är en helg eller veckodag.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Aktivitetsutdata

Om du vill använda utdata från en tidigare aktivitet i runbook, använder du variabeln $ActivityOutput med följande syntax.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Du kan till exempel ha en aktivitet med en egenskap som kräver att namnet på en virtuell dator i så fall kan du använda följande uttryck:

```powershell-interactive
$ActivityOutput['Get-AzureVm'].Name
```

Om den egenskap som krävs för den virtuella datorn objektet i stället för bara en egenskap, skulle du returnerar hela objektet med följande syntax.

```powershell-interactive
$ActivityOutput['Get-AzureVm']
```

Du kan också använda utdata för en aktivitet i ett mer komplext uttryck, till exempel följande som sammanfogar text till virtuella datornamn.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVm'].Name
```

### <a name="conditions"></a>Villkor

Använd [jämförelseoperatorer](https://technet.microsoft.com/library/hh847759.aspx) jämför värden eller kontrollera om ett värde matchar ett specifikt mönster. En jämförelse returnerar värdet $true eller $false.

Till exempel följande villkor avgör om den virtuella datorn från en aktivitet med namnet *Get-AzureVM* är för närvarande *stoppats*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

Följande tillstånd som kontrollerar om samma virtuella dator är i varje delstat än *stoppats*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Du kan ansluta till flera villkor med hjälp av en [logisk operator](https://technet.microsoft.com/library/hh847789.aspx) som **- och** eller **- eller**. Till exempel följande villkor kontrollerar om samma virtuella dator i föregående exempel är i tillståndet *stoppats* eller *stoppar*.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Hash-tabeller

[Hash-tabeller](https://technet.microsoft.com/library/hh847780.aspx) är namn/värde-par som är användbara för att returnera en uppsättning värden. Egenskaper för vissa aktiviteter kan förvänta dig en hash-tabell i stället för ett enkelt värde. Du kan också se som hash-tabell som kallas en ordlista.

Du kan skapa en hash-tabell med följande syntax. En hash-tabell kan innehålla valfritt antal poster, men varje definieras av ett namn och värde.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Följande uttryck skapar till exempel en hash-tabell som ska användas i datakällan för en aktivitetsparameter som förväntas en hash-tabell med värden för en Internetsökning.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

I följande exempel används från en aktivitet som kallas *hämta Twitter-anslutning* att fylla i en hash-tabell.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="next-steps"></a>Nästa steg

* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
* Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
* Information om hur du autentiserar med hjälp av Automation kör som-kontot finns i [konfigurera Azure kör som-konto](automation-sec-configure-azure-runas-account.md)
