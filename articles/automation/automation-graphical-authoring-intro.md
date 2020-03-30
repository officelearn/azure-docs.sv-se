---
title: Grafisk redigering i Azure Automation
description: Med grafisk redigering kan du skapa runbooks för Azure Automation utan att arbeta med kod. Den här artikeln innehåller en introduktion till grafisk redigering och alla detaljer som behövs för att börja skapa en grafisk runbook.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 09afca7eaf385795baf9c4a3c94232622527e357
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501052"
---
# <a name="graphical-authoring-in-azure-automation"></a>Grafisk redigering i Azure Automation

Med grafisk redigering kan du skapa runbooks för Azure Automation utan komplexiteten i den underliggande Windows PowerShell- eller PowerShell-arbetsflödeskoden. Du kan lägga till aktiviteter på arbetsytan från ett bibliotek med cmdlets och runbooks, länka ihop dem och konfigurera dem så att de kan skapa ett arbetsflöde. Om du någonsin har arbetat med System Center Orchestrator eller Service Management Automation (SMA), bör grafisk redigering se bekant ut. Den här artikeln innehåller en introduktion till de begrepp du behöver för att komma igång med att skapa en grafisk runbook.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="graphical-runbooks"></a>Grafiska runbooks

Alla runbooks i Azure Automation är Windows PowerShell-arbetsflöden. Grafiska runbooks och grafiska PowerShell Workflow runbooks generera PowerShell-kod som Automation-arbetarna kör men som du inte kan visa eller ändra. Du kan konvertera en grafisk runbook till en grafisk PowerShell-arbetsflödeskörningsbok och vice versa. Du kan dock inte konvertera dessa runbooks till en text runbook. Dessutom kan den grafiska redigeraren För Automatisering inte importera en textkörningsbok.

## <a name="overview-of-graphical-editor"></a>Översikt över grafisk redigerare

Du kan öppna den grafiska redigeraren i Azure-portalen genom att skapa eller redigera en grafisk runbook.

![Grafisk arbetsyta](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

I följande avsnitt beskrivs kontrollerna i den grafiska redigeraren.

### <a name="canvas-control"></a>Kontroll av arbetsyta

Med du kan designa runbooken med duk. Du kan lägga till aktiviteter från noderna i bibliotekskontrollen i runbooken och ansluta dem till länkar för att definiera runbook-logik. Längst ned på arbetsytan finns kontroller som gör att du kan zooma in och ut.

### <a name="library-control"></a>Bibliotekskontroll

Med bibliotekskontrollen kan du välja [aktiviteter](#activities) som du vill lägga till i runbooken. Du lägger till dem på arbetsytan, där du kan ansluta dem till andra aktiviteter. Bibliotekskontrollen innehåller de avsnitt som definieras i följande tabell.

| Section | Beskrivning |
|:--- |:--- |
| Cmdletar |Alla cmdlets som kan användas i din runbook. Cmdlets är ordnade efter modul. Alla moduler som du har installerat i ditt Automation-konto är tillgängliga. |
| Runbooks |Runbooks i ditt Automation-konto. Du kan lägga till dessa runbooks på arbetsytan som ska användas som underordnade runbooks. Endast runbooks av samma kärntyp som runbooken som redigeras visas. För grafiska runbooks visas endast PowerShell-baserade runbooks. För grafiska PowerShell-arbetsflödeskörningsböcker visas endast PowerShell-arbetsflödesbaserade runbooks. |
| Tillgångar |De [automatiseringstillgångar](/previous-versions/azure/dn939988(v=azure.100)) i ditt Automation-konto som du kan använda i din runbook. Om du lägger till en tillgång i en runbook läggs en arbetsflödesaktivitet till som hämtar den valda tillgången. När det gäller rörliga tillgångar kan du välja om du vill lägga till en aktivitet för att hämta variabeln eller ange variabeln. |
| Runbook-kontroll |Kontrollera aktiviteter som kan användas i din aktuella runbook. En junction-aktivitet tar flera indata och väntar tills alla har slutförts innan arbetsflödet fortsätter. En kodaktivitet kör en eller flera rader med PowerShell- eller PowerShell-arbetsflödeskod, beroende på den grafiska runbooktypen. Du kan använda den här aktiviteten för anpassad kod eller för funktioner som är svåra att uppnå med andra aktiviteter. |

### <a name="configuration-control"></a>Konfigurationskontroll

Med konfigurationskontrollen kan du ange information om ett objekt som har valts på arbetsytan. Vilka egenskaper som är tillgängliga i den här kontrollen beror på vilken typ av objekt som är markerat. När du väljer ett alternativ i konfigurationskontrollen öppnas ytterligare blad för att ge mer information.

### <a name="test-control"></a>Testkontroll

Testkontrollen visas inte när den grafiska redigeraren startas. Den öppnas när du interaktivt testar en grafisk runbook.

## <a name="activities"></a>Aktiviteter

Aktiviteter är byggstenarna i en runbook. En aktivitet kan vara en PowerShell-cmdlet, en underordnad runbook eller ett arbetsflöde. Du kan lägga till en aktivitet i runbooken genom att högerklicka på den i bibliotekskontrollen och välja **Lägg till på arbetsytan**. Du kan sedan klicka och dra aktiviteten för att placera den var som helst på arbetsytan som du vill. Aktivitetens placering på arbetsytan påverkar inte körningsbokens funktion. Du kan utforma din runbook på något sätt du tycker är mest lämplig för att visualisera dess funktion.

![Lägg till på arbetsyta](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Välj en aktivitet på arbetsytan för att konfigurera dess egenskaper och parametrar i konfigurationsbladet. Du kan ändra etiketten på aktiviteten till ett namn som du tycker är beskrivande. Runbooken kör fortfarande den ursprungliga cmdleten. Du ändrar helt enkelt visningsnamnet som den grafiska redigeraren använder. Observera att etiketten måste vara unik i runbooken.

### <a name="parameter-sets"></a>Parameteruppsättningar

En parameteruppsättning definierar obligatoriska och valfria parametrar som accepterar värden för en viss cmdlet. Alla cmdlets har minst en parameteruppsättning, och vissa har flera uppsättningar. Om en cmdlet har flera parameteruppsättningar måste du välja den som ska användas innan du kan konfigurera parametrar. Du kan ändra parameteruppsättningen som används av en aktivitet genom att välja **Parameteruppsättning** och välja en annan uppsättning. I det här fallet går alla parametervärden som du redan har konfigurerat förlorade.

I följande exempel har [Cmdlet Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) tre parameteruppsättningar. I exemplet används en uppsättning som heter **ListVirtualMachineInResourceGroupParamSet**, med en enda valfri parameter, för att returnera alla virtuella datorer i en resursgrupp. I exemplet används också parameteruppsättningen **GetVirtualMachineInResourceGroupParamSet** för att ange den virtuella datorn som ska returneras. Den här uppsättningen har två obligatoriska parametrar och en valfri parameter.

![Parameteruppsättning](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Parametervärden

När du anger ett värde för en parameter väljer du en datakälla för att bestämma hur värdet anges. Vilka datakällor som är tillgängliga för en viss parameter beror på giltiga värden för den parametern. Null är till exempel inte ett tillgängligt alternativ för en parameter som inte tillåter null-värden.

| Datakälla | Beskrivning |
|:--- |:--- |
| Konstant värde |Skriv in ett värde för parametern. Den här datakällan är endast tillgänglig för följande datatyper: Int32, Int64, String, Boolean, DateTime, Switch. |
| Aktivitetsutdata |Använd utdata från en aktivitet som föregår den aktuella aktiviteten i arbetsflödet. Alla giltiga aktiviteter visas. Använd bara den aktivitet som producerar utdata för parametervärdet. Om aktiviteten matar ut ett objekt med flera egenskaper kan du skriva in namnet på en viss egenskap när du har valt aktiviteten. |
| Runbook-ingång |Välj en runbook-indata som indata för aktivitetsparametern. |
| Variabel tillgång |Välj en automatiseringsvariabel som indata. |
| Tillgång för autentiseringsuppgifter |Välj en automatiseringsautentisering som indata. |
| Certifikattillgång |Välj ett Automation-certifikat som indata. |
| Tillgång till anslutning |Välj en Automation-anslutning som indata. |
| PowerShell-uttryck |Ange ett enkelt [PowerShell-uttryck](#powershell-expressions). Uttrycket utvärderas före aktiviteten och resultatet används för parametervärdet. Du kan använda variabler för att referera till utdata för en aktivitet eller en runbook-inmatningsparameter. |
| Inte konfigurerat |Ta bort alla värden som tidigare har konfigurerats. |

#### <a name="optional-additional-parameters"></a>Valfria ytterligare parametrar

Alla cmdlets har möjlighet att tillhandahålla ytterligare parametrar. Dessa är PowerShell-vanliga parametrar eller andra anpassade parametrar. Den grafiska redigeraren visar en textruta där du kan tillhandahålla parametrar med PowerShell-syntax. Om du till `Verbose` exempel vill använda `-Verbose:$True`den vanliga parametern bör du ange .

### <a name="retry-activity"></a>Återförsöksaktivitet

Med funktionen Försök igen för en aktivitet kan den köras flera gånger tills ett visst villkor är uppfyllt, ungefär som en loop. Du kan använda den här funktionen för aktiviteter som ska köras flera gånger, är felbenägna, kan behöva mer än ett försök att lyckas eller testa utdatainformationen för aktiviteten för giltiga data.

När du aktiverar återförsök för en aktivitet kan du ställa in en fördröjning och ett villkor. Fördröjningen är den tid (mätt i sekunder eller minuter) som runbooken väntar innan den kör aktiviteten igen. Om du inte anger en fördröjning körs aktiviteten igen omedelbart efter att den har slutförts.

![Fördröjning för återförsök för aktivitet](media/automation-graphical-authoring-intro/retry-delay.png)

Återförsöksvillkoret är ett PowerShell-uttryck som utvärderas efter varje gång aktiviteten körs. Om uttrycket går till Sant körs aktiviteten igen. Om uttrycket går till False körs inte aktiviteten igen och runbooken går vidare till nästa aktivitet.

![Fördröjning för återförsök för aktivitet](media/automation-graphical-authoring-intro/retry-condition.png)

Tillståndet för återförsök kan `RetryData` använda en variabel med namnet som ger åtkomst till information om aktivitetens återförsök. Den här variabeln har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| `NumberOfAttempts` |Antal gånger som aktiviteten har körts. |
| `Output` |Utdata från den sista körningen av aktiviteten. |
| `TotalDuration` |Tidsinförd tid sedan aktiviteten startades första gången. |
| `StartedAt` |Tid (i UTC-format) när aktiviteten startades. |

Följande är exempel på villkor för återförsök.

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

När du har konfigurerat ett villkor för återförsök för en aktivitet innehåller aktiviteten två visuella tips som påminner dig. Den ena presenteras i aktiviteten och den andra visas när du granskar konfigurationen av aktiviteten.

![Visuella indikatorer för aktivitetsförsök](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Kontroll av arbetsflödesskript

En arbetsflödesskriptkontroll är en särskild aktivitet som accepterar PowerShell- eller PowerShell-arbetsflödesskriptet, beroende på vilken typ av grafisk runbook som skapas. Den här kontrollen innehåller funktioner som kanske inte är tillgängliga på annat sätt. Den kan inte acceptera parametrar, men den kan använda variabler för aktivitetsutdata och runbook-inmatningsparametrar. Alla utdata för aktiviteten läggs till i databussen. Ett undantag är utdata utan utgående länk, i vilket fall utdata läggs till i utdata för runbooken.

Följande kod utför till exempel datumberäkningar med hjälp `NumberOfDays`av en runbook-inmatningsvariabel med namnet . Den skickar sedan ett beräknat DateTime-värde som utdata som ska användas av efterföljande aktiviteter i runbooken.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Länkar och arbetsflöde

En länk i en grafisk runbook ansluter två aktiviteter. Den visas på arbetsytan som en pil som pekar från källaktiviteten till målaktiviteten. Aktiviteterna körs i pilens riktning med målaktiviteten som börjar när källaktiviteten har slutförts.

### <a name="link-creation"></a>Skapa länkar

Du kan skapa en länk mellan två aktiviteter genom att markera källaktiviteten och klicka på cirkeln längst ned i formen. Dra pilen till målaktiviteten och släpp.

![Skapa en länk](media/automation-graphical-authoring-intro/create-link-options.png)

Välj länken för att konfigurera dess egenskaper i konfigurationsbladet. Egenskaper inkluderar länktypen, som beskrivs i följande tabell.

| Länktyp | Beskrivning |
|:--- |:--- |
| Pipeline |Målaktiviteten körs en gång för varje objektutdata från källaktiviteten. Målaktiviteten körs inte om källaktiviteten inte resulterar i någon utdata. Utdata från källaktiviteten är tillgängligt som ett objekt. |
| Sequence |Målaktiviteten körs bara en gång när den tar emot utdata från källaktiviteten. Utdata från källaktiviteten är tillgängligt som en matris med objekt. |

### <a name="start-of-activity"></a>Aktivitetens början

En grafisk runbook börjar med alla aktiviteter som inte har en inkommande länk. Det finns ofta bara en aktivitet som fungerar som startaktivitet för runbooken. Om flera aktiviteter inte har någon inkommande länk börjar runbooken med att köra dem parallellt. Den följer länkarna för att köra andra aktiviteter som varje slutför.

### <a name="link-conditions"></a>Länkvillkor

När du anger ett villkor på en länk körs målaktiviteten endast om villkoret matchas till Sant. Du använder `ActivityOutput` vanligtvis en variabel i ett villkor för att hämta utdata från källaktiviteten.

För en pipeline-länk måste du ange ett villkor för ett enskilt objekt. Runbooken utvärderar villkoret för varje objektutdata av källaktiviteten. Den kör sedan målaktiviteten för varje objekt som uppfyller villkoret. Med en källaktivitet `Get-AzVM`i kan du till exempel använda följande syntax för en villkorsstyrd pipeline-länk för att hämta endast virtuella datorer i resursgruppen Group1.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

För en sekvenslänk utvärderar runbooken bara villkoret en gång, eftersom en enda matris som innehåller alla objekt från källaktiviteten returneras. På grund av detta kan runbooken inte använda en sekvenslänk för filtrering, som den kan med en pipeline-länk. Sekvenslänken kan helt enkelt avgöra om nästa aktivitet körs eller inte.

Ta till exempel följande uppsättning aktiviteter i vår **Start VM-runbook:**

![Villkorlig länk med sekvenser](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Runbook använder tre olika sekvenslänkar som `VMName` verifierar värdena för indataparametrarna och `ResourceGroupName` för att bestämma vilken åtgärd som ska vidtas. Möjliga åtgärder starta en enda virtuell dator, starta alla virtuella datorer i resursgruppen eller starta alla virtuella datorer i en prenumeration. För sekvenslänken `Connect to Azure` `Get single VM`mellan och , här är villkorslogiken:

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

När du använder en villkorslänk filtreras de data som är tillgängliga från källaktiviteten till andra aktiviteter i den grenen av villkoret. Om en aktivitet är källan till flera länkar beror de data som är tillgängliga för aktiviteter i varje gren på villkoret i länken som ansluter till den grenen.

`Start-AzVM` Aktiviteten i runbooken nedan startar till exempel alla virtuella datorer. Den har två villkorliga länkar. Den första villkorliga `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` länken använder `Start-AzVM` uttrycket för att filtrera om aktiviteten har slutförts. Den andra villkorliga `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` länken använder `Start-AzVm` uttrycket för att filtrera om aktiviteten inte startar den virtuella datorn.

![Exempel på villkorsstyrd länk](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Alla aktiviteter som följer den första länken `Get-AzureVM` och använder aktivitetsutdata från hämtar `Get-AzureVM` bara de virtuella datorer som startades vid den tidpunkt då körningen kördes. All aktivitet som följer den andra länken får bara de `Get-AzureVM` virtuella datorer som stoppades vid den tidpunkt då körningen kördes. All aktivitet efter den tredje länken får alla virtuella datorer oavsett deras körtillstånd.

### <a name="junctions"></a>Korsningar

En korsning är en speciell aktivitet som väntar tills alla inkommande grenar har slutförts. Detta gör att runbooken kan köra flera aktiviteter parallellt och se till att alla har slutförts innan de går vidare.

Medan en korsning kan ha ett obegränsat antal inkommande länkar, kan bara en av dessa länkar vara en pipeline. Antalet inkommande sekvenslänkar är inte begränsat. Du kan skapa korsningen med flera inkommande pipeline-länkar och spara runbooken, men den misslyckas när den körs.

Exemplet nedan är en del av en runbook som startar en uppsättning virtuella datorer samtidigt som korrigeringsfiler hämtas som ska tillämpas på dessa datorer. Den använder en korsning för att säkerställa att båda processerna slutförs innan runbooken fortsätter.

![Knutpunkt](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cykler

En cykel bildas när en målaktivitet länkar tillbaka till källaktiviteten eller till en annan aktivitet som så småningom länkar tillbaka till källan. Grafisk redigering stöder för närvarande inte cykler. Om runbooken har en cykel sparas den korrekt men får ett felmeddelande när den körs.

![Cykler](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="data-sharing-between-activities"></a>Datadelning mellan aktiviteter

Alla data som en aktivitet matar ut med en utgående länk skrivs till databussen för runbooken. All aktivitet i runbooken kan använda data på databussen för att fylla i parametervärden eller inkludera i skriptkod. En aktivitet kan komma åt utdata från alla tidigare aktiviteter i arbetsflödet.

Hur data skrivs till databussen beror på typen av länk på aktiviteten. För en pipeline-länk matas data ut som flera objekt. För en sekvenslänk matas data ut som en matris. Om det bara finns ett värde matas det ut som en matris med ett element.

Runbooken har två sätt att komma åt data på databussen: 
* Använd en aktivitetsdatakälla.
* Använd en PowerShell-uttrycksdatakälla.

Den första mekanismen använder en aktivitetsdatakälla för att fylla i en parameter för en annan aktivitet. Om utdata är ett objekt kan runbooken ange en enskild egenskap.

![aktivitetsproduktion](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Den andra dataåtkomstmekanismen hämtar utdata från en aktivitet i en PowerShell-uttrycksdatakälla eller en arbetsflödesskriptaktivitet med en `ActivityOutput` variabel, med hjälp av syntaxen nedan. Om utdata är ett objekt kan runbooken ange en enskild egenskap.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Kontrollpunkter

Du kan ange [kontrollpunkter](automation-powershell-workflow.md#checkpoints) i en grafisk PowerShell-arbetsflödeskörningsbok genom att välja **Kontrollpunktskörning** på valfri aktivitet. Detta medför att en kontrollpunkt ska ställas in när aktiviteten körs.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Kontrollpunkter är endast aktiverade i grafiska PowerShell-arbetsflödeskörningsböcker och är inte tillgängliga i grafiska runbooks. Om runbook använder Azure-cmdlets bör den följa `Connect-AzAccount` alla kontrollpunkter med en aktivitet. Anslutningsåtgärden används om runbooken pausas och måste starta om från den här kontrollpunkten på en annan arbetare.

## <a name="runbook-input-and-output"></a>Runbook-in- och utdata

### <a name="runbook-input"></a>Runbook-indata<a name="runbook-input"></a>

En runbook kräver indata antingen från en användare som startar runbook via Azure-portalen eller från en annan runbook, om den aktuella används som underordnad. För en runbook som skapar en virtuell dator kan användaren till exempel behöva ange sådan information som namnet på den virtuella datorn och andra egenskaper varje gång runbooken startar.

Runbook accepterar indata genom att definiera en eller flera indataparametrar. Användaren tillhandahåller värden för dessa parametrar varje gång runbooken startar. När användaren startar runbooken med Azure-portalen uppmanas användaren att ange värden för varje indataparameter som stöds av runbooken.

När du skapar runbooken kan du komma åt dess indataparametrar genom att klicka på **Indata och utdata** i verktygsfältet runbook. Då öppnas kontrollen In- och utdata där du kan redigera en befintlig indataparameter eller skapa en ny genom att klicka på **Lägg till indata**.

![Lägg till indata](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Varje indataparameter definieras av egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| Namn | Krävs. Namnet på parametern. Namnet måste vara unikt i runbooken. Den måste börja med en bokstav och kan bara innehålla bokstäver, siffror och understreck. Namnet får inte innehålla ett blanksteg. |
| Beskrivning |Valfri. Beskrivning av syftet med indataparametern. |
| Typ | Valfri. Datatyp som förväntas för parametervärdet. Azure-portalen ger en lämplig kontroll för datatypen för varje parameter när du frågar efter indata. Parametertyper som stöds är String, Int32, Int64, Decimal, Boolean, DateTime och Object. Om en datatyp inte är markerad används Sträng som standard.|
| Obligatorisk | Valfri. Inställning som anger om ett värde måste anges för parametern. Om du `yes`väljer måste ett värde anges när runbooken startas. Om du `no`väljer krävs inget värde när runbooken startas och ett standardvärde kan användas. Runbooken kan inte starta om du inte anger ett värde för varje obligatorisk parameter som inte har ett standardvärde definierat. |
| Standardvärde | Valfri. Värdet som används för en parameter om en inte skickas in när runbooken startas. Om du vill ange `Custom`ett standardvärde väljer du . Välj `None` om du inte vill ange något standardvärde. |

### <a name="runbook-output"></a>Utdata från Runbooks

Grafisk redigering sparar data som skapats av en aktivitet som inte har en utgående länk till [utdata från runbooken](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). Utdata sparas med runbook-jobbet och är tillgängligt för en överordnad runbook när runbooken används som underordnad.

## <a name="powershell-expressions"></a>PowerShell-uttryck

En av fördelarna med grafisk redigering är att det låter dig bygga en runbook med minimal kunskap om PowerShell. För närvarande behöver du dock veta lite powershell för att fylla vissa [parametervärden](#activities) och för att ställa in [länkvillkor](#links-and-workflow). Det här avsnittet innehåller en snabb introduktion till PowerShell-uttryck. Fullständig information om PowerShell finns på [Skript med Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Datakälla för PowerShell-uttryck

Du kan använda ett PowerShell-uttryck som en datakälla för att fylla i värdet för en [aktivitetsparameter](#activities) med resultatet av PowerShell-kod. Uttrycket kan vara en enda kodrad som utför en enkel funktion eller flera rader som utför en viss komplex logik. Alla utdata från ett kommando som inte har tilldelats en variabel matas ut till parametervärdet.

Följande kommando matar till exempel ut det aktuella datumet.

```powershell-interactive
Get-Date
```

Nästa kodavsnitt skapar en sträng från det aktuella datumet och tilldelar det till en variabel. Koden skickar innehållet i variabeln till utdata.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Följande kommandon utvärderar det aktuella datumet och returnerar en sträng som anger om den aktuella dagen är en helg eller en veckodag.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Aktivitetsutflöde

Om du vill använda utdata från en `ActivityOutput` tidigare aktivitet i runbooken använder du variabeln med följande syntax.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Du kan till exempel ha en aktivitet med en egenskap som kräver namnet på en virtuell dator. I det här fallet kan runbook använda följande uttryck.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Om egenskapen kräver objektet för den virtuella datorn i stället för bara ett namn returneras hela objektet med hjälp av följande syntax.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Runbook kan använda utdata från en aktivitet i ett mer komplext uttryck, till exempel följande. Det här uttrycket sammanfogar text till namnet på den virtuella datorn.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="conditions"></a>Villkor

Använd [jämförelseoperatorer](https://technet.microsoft.com/library/hh847759.aspx) för att jämföra värden eller avgöra om ett värde matchar ett angivet mönster. En jämförelse returnerar ett värde av antingen Sant eller Falskt.

Följande villkor avgör till exempel om den virtuella `Get-AzureVM` datorn från en aktivitet som heter för närvarande stoppas.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

Följande villkor avgör om samma virtuella dator är i något annat tillstånd än stoppas.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Du kan koppla till flera villkor i runbooken `-or`med en [logisk operator,](https://technet.microsoft.com/library/hh847789.aspx)till exempel `-and` eller . Följande villkor kontrollerar till exempel om den virtuella datorn i föregående exempel är i ett tillstånd av Stoppad eller Stoppad.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Hashtables (hashtables)

[Hashtables](https://technet.microsoft.com/library/hh847780.aspx) är namn-värde par som är användbara för att returnera en uppsättning värden. Du kan också se en hashtable kallas en ordlista. Egenskaper för vissa aktiviteter förväntar sig en hashtable i stället för ett enkelt värde.

Skapa en hashtable med följande syntax. Den kan innehålla valfritt antal poster, men var och en definieras av ett namn och värde.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Följande uttryck skapar till exempel en hashtable som ska användas som datakälla för en aktivitetsparameter som förväntar sig en hashtable med värden för en internetsökning.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

I följande exempel används utdata från en aktivitet som anropas `Get Twitter Connection` för att fylla i en hashtable.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticating-to-azure-resources"></a>Autentisera till Azure-resurser

Runbooks i Azure Automation som hanterar Azure-resurser kräver autentisering till Azure. [Run As-kontot](automation-create-runas-account.md), även kallat tjänsthuvudnamn, är standardmekanismen som en Automation-runbook använder för att komma åt Azure Resource Manager-resurser i din prenumeration. Du kan lägga till den `AzureRunAsConnection` här funktionen i en grafisk runbook genom att lägga till anslutningstillgången, som använder cmdleten PowerShell [Get-AutomationConnection,](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) på arbetsytan. Du kan också lägga till cmdleten [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Det här scenariot illustreras i följande exempel.

![Köra som autentiseringsaktiviteter](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

Aktiviteten, `Get Run As Connection` eller `Get-AutomationConnection`, konfigureras med en datakälla med konstant värde med namnet `AzureRunAsConnection`.

![Kör som anslutningskonfiguration](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Nästa aktivitet `Connect-AzAccount`lägger till det autentiserade Run As-kontot som ska användas i runbooken.

![Parameteruppsättningen Connect-AzAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>För `Add-AzAccount` PowerShell-runbooks `Add-AzureRMAccount` och är `Connect-AzAccount`alias för . Observera att dessa alias inte är tillgängliga för dina grafiska runbooks. En grafisk runbook kan `Connect-AzAccount` bara använda sig själv.

För parameterfälten **APPLICATIONID,** **CERTIFICATETHUMBPRINT**och **TENANTID**anger du namnet på egenskapen för fältsökvägen, eftersom aktiviteten matar ut ett objekt med flera egenskaper. Annars misslyckas den när runbooken körs när den försöker autentiseras. Det här är vad du behöver som ett minimum för att autentisera din runbook med kontot Kör som.

Vissa prenumeranter skapar ett Automation-konto med ett [Azure AD-användarkonto](automation-create-aduser-account.md) för att hantera Azure-klassisk distribution eller för Azure Resource Manager-resurser. För att bibehålla bakåtkompatibiliteten för dessa prenumeranter `Add-AzureAccount` är autentiseringsmekanismen som ska användas i runbooken cmdlet med en [referenstillgång](automation-credentials.md). Tillgången representerar en Active Directory-användare med åtkomst till Azure-kontot.

Du kan aktivera den här funktionen för den grafiska runbooken genom att lägga `Add-AzureAccount` till en referenstillgång på arbetsytan, följt av en aktivitet som använder referenstillgången för indata. Se följande exempel.

![Autentiseringsaktiviteter](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook måste autentisera vid början och efter varje kontrollpunkt. Därför måste du `Add-AzureAccount` använda `Checkpoint-Workflow` en aktivitet efter någon aktivitet. Du behöver inte använda ytterligare en autentiseringsaktivitet.

![Aktivitetsutflöde](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="exporting-and-importing-a-graphical-runbook"></a>Exportera och importera en grafisk runbook

Du kan bara exportera den publicerade versionen av en grafisk runbook. Om runbooken ännu inte har publicerats inaktiveras knappen **Exportera.** När du klickar på knappen **Exportera** hämtas runbooken till den lokala datorn. Namnet på filen matchar namnet på runbook med en **.graphrunbook** förlängning.

Du kan importera en grafisk eller grafisk PowerShell-arbetsflödeskörningsfil genom att välja alternativet **Importera** när du lägger till en runbook. När du väljer den fil som ska importeras kan du behålla samma namn eller ange ett nytt. I fältet **Runbook Type** visas typen av runbook när den har bedömt den valda filen. Om du försöker välja en annan typ som inte är korrekt visas ett meddelande om att det finns potentiella konflikter och att det kan finnas syntaxfel under konverteringen.

![Importera runbook](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="testing-a-graphical-runbook"></a>Testa en grafisk runbook

Varje grafisk runbook i Azure Automation har en utkastversion och en publicerad version. Du kan bara köra den publicerade versionen, medan du bara kan redigera utkastversionen. Den Publicerade versionen påverkas inte av ändringar i Utkastet. När utkastversionen är klar för användning publicerar du den, som skriver över den aktuella publicerade versionen med utkastversionen.

Du kan testa utkastversionen av en runbook i Azure-portalen samtidigt som den publicerade versionen är oförändrad. Du kan också testa en ny runbook innan den har publicerats så att du kan kontrollera att runbooken fungerar korrekt innan någon version ersätter. Testning av en runbook kör utkastversionen och säkerställer att alla åtgärder som utförs slutförs. Ingen jobbhistorik skapas, men fönstret Testutdata visar utdata.

Öppna testkontrollen för den grafiska runbooken genom att öppna runbooken för redigering och klicka sedan på **Testfönstret**. Testkontrollen frågar efter indataparametrar och du kan starta runbooken genom att klicka på **Start**.

## <a name="publishing-a-graphical-runbook"></a>Publicera en grafisk runbook

Publicera en grafisk runbook genom att öppna runbook för redigering och sedan klicka på **Publicera**. Möjliga status för runbooken är:

* Ny - runbooken har inte publicerats ännu. 
* Publicerad - runbooken har publicerats.
* I edit – runbooken har redigerats efter att den har publicerats och versionerna Utkast och Publicerat är olika.

![Runbook-status](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Du kan återgå till den publicerade versionen av en runbook. Den här åtgärden kastar bort alla ändringar som gjorts sedan runbooken senast publicerades. Den ersätter utkastversionen av runbooken med den publicerade versionen.

## <a name="next-steps"></a>Efterföljande moment

* Information om hur du kommer igång med PowerShell Workflow runbooks finns i [Min första PowerShell-arbetsflödeskörningsbok](automation-first-runbook-textual.md).
* Kom igång med grafiska runbooks finns i [Min första grafiska runbook](automation-first-runbook-graphical.md).
* Mer information om runbook-typer och deras fördelar och begränsningar finns i [Azure Automation-runbooktyper](automation-runbook-types.md).
* Information om hur du autentiserar med kontot För körning som automatisering finns i [Konfigurera Azure Run As Account](automation-sec-configure-azure-runas-account.md).
