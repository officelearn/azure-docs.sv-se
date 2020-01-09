---
title: Grafisk redigering i Azure Automation
description: Med grafisk redigering kan du skapa Runbooks för Azure Automation utan att arbeta med kod. Den här artikeln innehåller en introduktion till grafisk redigering och all information som behövs för att börja skapa en grafisk Runbook.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 8c1b864eb83a9ffb69c0cb532dc2061636010c60
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450756"
---
# <a name="graphical-authoring-in-azure-automation"></a>Grafisk redigering i Azure Automation

Med grafisk redigering kan du skapa Runbooks för Azure Automation utan att den underliggande Windows PowerShell-eller PowerShell-arbetsflödes koden är komplex. Du lägger till aktiviteter till arbets ytan från ett bibliotek med cmdlets och Runbooks, länkar dem tillsammans och konfigurerar för att skapa ett arbets flöde. Om du någonsin har arbetat med System Center Orchestrator eller Service Management Automation (SMA) bör det se ut som bekant med dig

Den här artikeln innehåller en introduktion till grafisk redigering och de koncept du behöver för att komma igång med att skapa en grafisk Runbook.

## <a name="graphical-runbooks"></a>Grafiska runbooks

Alla Runbooks i Azure Automation är Windows PowerShell-arbetsflöden. Grafiska och grafiska PowerShell Workflow-Runbooks genererar PowerShell-kod som körs av automatiserings arbetare, men du kan inte visa den eller ändra den direkt. En grafisk Runbook kan konverteras till en grafisk PowerShell-Runbook för PowerShell och vice versa, men de kan inte konverteras till en text Runbook. Det går inte att importera en befintlig text-Runbook till den grafiska redigeraren.

## <a name="overview-of-graphical-editor"></a>Översikt över grafiskt redigerings program

Du kan öppna den grafiska redigeraren i Azure Portal genom att skapa eller redigera en grafisk Runbook.

![Grafisk arbets yta](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

I följande avsnitt beskrivs kontrollerna i den grafiska redigeraren.

### <a name="canvas"></a>Arbetsyta

På arbets ytan kan du utforma din Runbook. Du lägger till aktiviteter från noderna i biblioteks kontrollen i Runbook-flödet och kopplar dem till länkar för att definiera logiken för runbooken.

Du kan använda kontrollerna längst ned på arbets ytan för att zooma in och ut.

### <a name="library-control"></a>Biblioteks kontroll

Biblioteks kontrollen är den plats där du väljer [aktiviteter](#activities) som ska läggas till i din Runbook. Du lägger till dem på arbets ytan där du ansluter dem till andra aktiviteter. Den innehåller fyra avsnitt som beskrivs i följande tabell:

| Section | Beskrivning |
|:--- |:--- |
| Cmdletar |Innehåller alla cmdletar som kan användas i din Runbook. Cmdletar är ordnade efter modul. Alla moduler som du har installerat i ditt Automation-konto är tillgängliga. |
| Runbooks |Inkluderar Runbooks i ditt Automation-konto. Dessa Runbooks kan läggas till i arbets ytan som ska användas som underordnade Runbooks. Endast Runbooks av samma kärn typ som den Runbook som redige ras visas. endast för grafiska runbooks visas PowerShell-baserade Runbooks, medan endast PowerShell-arbetsflödes flöden visas för grafiska PowerShell Workflow-Runbooks. |
| Tillgångar |Innehåller de [Automation-tillgångar](/previous-versions/azure/dn939988(v=azure.100)) i ditt Automation-konto som kan användas i din Runbook. När du lägger till en till gång i en Runbook lägger den till en arbets flödes aktivitet som hämtar den valda till gången. Om det gäller variabla till gångar kan du välja om du vill lägga till en aktivitet för att hämta variabeln eller ange variabeln. |
| Runbook-kontroll |Innehåller aktiviteter för Runbook-kontroll som kan användas i din aktuella Runbook. En *Knut* punkt tar flera indata och väntar tills alla har slutförts innan arbets flödet fortsätter. En *kod* aktivitet kör en eller flera rader med PowerShell-eller PowerShell-arbetsflöde, beroende på vilken grafisk Runbook-typ som används. Du kan använda den här aktiviteten för anpassad kod eller för funktioner som är svåra att uppnå med andra aktiviteter. |

### <a name="configuration-control"></a>Konfigurations kontroll

Konfigurations kontrollen är den plats där du anger information om ett objekt som valts på arbets ytan. Vilka egenskaper som är tillgängliga i den här kontrollen beror på vilken typ av objekt som valts. När du väljer ett alternativ i konfigurations kontrollen öppnas ytterligare blad för att ge ytterligare information.

### <a name="test-control"></a>Test kontroll

Test kontrollen visas inte när den grafiska redigeraren startas första gången. Den öppnas när du interaktivt [testar en grafisk Runbook](#graphical-runbook-procedures).

## <a name="graphical-runbook-procedures"></a>Grafiska Runbook-procedurer

### <a name="exporting-and-importing-a-graphical-runbook"></a>Exportera och importera en grafisk Runbook

Du kan bara exportera den publicerade versionen av en grafisk Runbook. Om Runbook ännu inte har publicerats är knappen **Exportera** inaktive rad. När du klickar på knappen **Exportera** laddas runbooken ned till den lokala datorn. Namnet på filen matchar namnet på runbooken med ett *graphrunbook* -tillägg.

Du kan importera en grafisk eller grafisk PowerShell-Runbook-fil genom att välja alternativet **Importera** när du lägger till en Runbook. När du väljer den fil som ska importeras, kan du behålla samma **namn** eller ange en ny. I fältet typ av Runbook visas typen av runbook när den har bedömt den valda filen och om du försöker välja en annan typ som inte är korrekt visas ett meddelande om att det finns potentiella konflikter och under konverteringen kan det finnas en syntax kompileringsfel.

![Importera Runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Testa en grafisk Runbook

Du kan testa utkast versionen av en Runbook i Azure Portal när du lämnar den publicerade versionen av runbooken oförändrad, eller så kan du testa en ny Runbook innan den har publicerats. På så sätt kan du kontrollera att runbooken fungerar korrekt innan du byter ut den publicerade versionen. När du testar en runbook körs utkast-runbooken och alla åtgärder som den utför slutförs. Ingen jobb historik skapas, men utdata visas i fönstret Testa utdata.

Öppna test kontrollen för en Runbook genom att öppna Runbook för redigering och klicka sedan på knappen **test fönster** .

Test kontrollen efterfrågar eventuella indataparametrar och du kan starta runbooken genom att klicka på knappen **Starta** .

### <a name="publishing-a-graphical-runbook"></a>Publicera en grafisk Runbook

Varje Runbook i Azure Automation har ett utkast och en publicerad version. Bara den publicerade versionen är tillgängligt och kan köras och bara utkastet som kan redigeras. Den publicerade versionen påverkas inte av ändringar i utkastet. När utkast versionen är klar att vara tillgänglig publicerar du den, vilket skriver över den publicerade versionen med utkastet.

Du kan publicera en grafisk Runbook genom att öppna Runbook för redigering och sedan klicka på knappen **publicera** .

När en Runbook ännu inte har publicerats har den statusen **ny**. När den publiceras har den statusen **publicerad**. Om du redigerar runbooken när den har publicerats, och utkastet och de publicerade versionerna skiljer sig, har runbooken statusen **i redigera**.

![Runbook-status](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Du kan också välja att återgå till den publicerade versionen av en Runbook. Detta genererar eventuella ändringar som gjorts sedan Runbook senast publicerades och ersätter utkast versionen av Runbook med den publicerade versionen.

## <a name="activities"></a>Aktiviteter

Aktiviteter är Bygg stenarna i en Runbook. En aktivitet kan vara en PowerShell-cmdlet, en underordnad Runbook eller en arbets flödes aktivitet. Du lägger till en aktivitet i runbooken genom att högerklicka på den i biblioteks kontrollen och välja **Lägg till på arbets ytan**. Du kan sedan klicka och dra aktiviteten för att placera den var som helst på arbets ytan som du vill. Platsen för aktiviteten på arbets ytan påverkar inte driften av runbooken på något sätt. Du kan utforma din Runbook men du tycker att du är mest lämplig för att visualisera åtgärden.

![Lägg till på arbets ytan](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Välj aktivitet på arbets ytan för att konfigurera dess egenskaper och parametrar på bladet konfiguration. Du kan ändra aktivitets **etiketten** till något som är beskrivande. Den ursprungliga cmdleten körs fortfarande. du ändrar bara det visnings namn som används i den grafiska redigeraren. Etiketten måste vara unik inom runbooken.

### <a name="parameter-sets"></a>Parameter uppsättningar

En parameter uppsättning definierar de obligatoriska och valfria parametrarna som accepterar värden för en viss cmdlet. Alla cmdlets har minst en parameter uppsättning och några har flera. Om en cmdlet har flera parameter uppsättningar måste du välja vilken som du använder innan du kan konfigurera parametrar. De parametrar som du kan konfigurera beror på vilken parameter uppsättning du väljer. Du kan ändra den parameter uppsättning som används av en aktivitet genom att välja **parameter uppsättning** och välja en annan uppsättning. I det här fallet går alla parameter värden som du har konfigurerat förlorade.

I följande exempel har cmdleten Get-AzureRmVM tre parameter uppsättningar. Du kan inte konfigurera parameter värden förrän du väljer en av parameter uppsättningarna. ListVirtualMachineInResourceGroupParamSet-parametern anges för att returnera alla virtuella datorer i en resurs grupp och har en enda valfri parameter. **GetVirtualMachineInResourceGroupParamSet** är för att ange den virtuella dator som du vill returnera och har två obligatoriska och en valfri parameter.

![Parameter uppsättning](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Parametervärden

När du anger ett värde för en parameter väljer du en data källa för att avgöra hur värdet anges. De data källor som är tillgängliga för en viss parameter beror på giltiga värden för den parametern. Null är till exempel inte ett tillgängligt alternativ för en parameter som inte tillåter Null-värden.

| Datakälla | Beskrivning |
|:--- |:--- |
| Konstant värde |Ange ett värde för parametern. Detta är endast tillgängligt för följande data typer: Int32, Int64, String, Boolean, DateTime, switch. |
| Aktivitetens utdata |Utdata från en aktivitet som föregår den aktuella aktiviteten i arbets flödet. Alla giltiga aktiviteter visas. Välj bara aktiviteten för att använda utdata för parametervärdet. Om aktiviteten matar ut ett objekt med flera egenskaper kan du ange namnet på egenskapen när du har valt aktiviteten. |
| Inmatade Runbook |Välj en Runbook-indataparameter som indatamängden för aktivitets parametern. |
| Variabel till gång |Välj en Automation-variabel som inmatade. |
| Referens till gång |Välj en Automation-autentiseringsuppgift som indatamängd. |
| Certifikat till gång |Välj ett Automation-certifikat som inmatade. |
| Anslutnings till gång |Välj en Automation-anslutning som inmatade. |
| PowerShell-uttryck |Ange ett enkelt [PowerShell-uttryck](#powershell-expressions). Uttrycket utvärderas före aktiviteten och resultatet som används för parametervärdet. Du kan använda variabler för att referera till utdata för en aktivitet eller en inmatnings parameter för Runbook. |
| Inte konfigurerat |Tar bort alla värden som tidigare har kon figurer ATS. |

#### <a name="optional-additional-parameters"></a>Valfria ytterligare parametrar

Alla cmdlets har möjlighet att ange ytterligare parametrar. Detta är vanliga PowerShell-parametrar eller andra anpassade parametrar. En text ruta visas där du kan ange parametrar med PowerShell-syntax. Om du till exempel vill använda den **utförliga** gemensamma parametern anger du **"-verbose: $true"** .

### <a name="retry-activity"></a>Försök igen-aktivitet

Med **funktionen för återförsök** kan en aktivitet köras flera gånger tills ett visst villkor uppfylls, ungefär som en slinga. Du kan använda den här funktionen för aktiviteter som ska köras flera gånger, är fel känsliga och kan behöva mer än ett försök för att lyckas, eller testa utdata för aktiviteten för giltiga data.

När du aktiverar återförsök för en aktivitet kan du ange en fördröjning och ett villkor. Fördröjningen är den tid (mätt i sekunder eller minuter) som Runbook väntar innan aktiviteten körs igen. Om ingen fördröjning anges körs aktiviteten igen omedelbart efter att den har slutförts.

![Fördröjning för nytt aktivitets försök](media/automation-graphical-authoring-intro/retry-delay.png)

Villkoret för återförsök är ett PowerShell-uttryck som utvärderas efter varje körning som aktiviteten körs. Om uttrycket matchar True körs aktiviteten igen. Om uttrycket matchar falskt körs inte aktiviteten igen, och runbooken flyttas vidare till nästa aktivitet.

![Fördröjning för nytt aktivitets försök](media/automation-graphical-authoring-intro/retry-condition.png)

Villkoret för återförsök kan använda en variabel som kallas $RetryData som ger till gång till information om aktiviteternas återförsök. Den här variabeln har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| NumberOfAttempts |Antal gånger som aktiviteten har körts. |
| Resultat |Utdata från den senaste körningen av aktiviteten. |
| TotalDuration |Förfluten tid sedan aktiviteten startades första gången. |
| StartedAt |Tid i UTC-format som aktiviteten startades första gången. |

Följande är exempel på villkor för aktivitets återförsök.

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

När du har konfigurerat ett villkor för återförsök för en aktivitet innehåller aktiviteten två visuella tips som påminner dig om detta. En visas i aktiviteten och den andra är när du granskar aktivitetens konfiguration.

![Visuella indikatorer för aktivitets försök](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Skript kontroll för arbets flöde

En kod kontroll är en särskild aktivitet som accepterar PowerShell-eller PowerShell-arbetsflöde-skript beroende på vilken typ av grafisk Runbook som skapas för att tillhandahålla funktioner som annars annars inte är tillgängliga. Den kan inte ta emot parametrar, men den kan använda variabler för aktivitets utdata och indataparametrar för Runbook. Alla utdata från aktiviteten läggs till i data bussen, såvida inte den inte har någon utgående länk i vilket fall den läggs till i Runbook-resultatet.

Följande kod utför t. ex. datum beräkningar med hjälp av en indata-variabel för Runbook som kallas $NumberOfDays. Därefter skickas en beräknad datum tid som utdata som ska användas av efterföljande aktiviteter i runbooken.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Länkar och arbets flöde

En **länk** i en grafisk Runbook ansluter två aktiviteter. Den visas på arbets ytan som en pil som pekar på käll aktiviteten till mål aktiviteten. Aktiviteterna körs i pilens riktning med mål aktiviteten som startar när käll aktiviteten har slutförts.

### <a name="create-a-link"></a>Skapa en länk

Skapa en länk mellan två aktiviteter genom att välja käll aktivitet och klicka på cirkeln längst ned i formen. Dra pilen till mål aktiviteten och släpp.

![Skapa en länk](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Välj länken för att konfigurera dess egenskaper på bladet konfiguration. Detta inkluderar länk typen, som beskrivs i följande tabell:

| Länktyp | Beskrivning |
|:--- |:--- |
| Pipeline |Mål aktiviteten körs en gång för varje objekts utdata från käll aktiviteten. Mål aktiviteten körs inte om käll aktiviteten resulterar i inga utdata. Utdata från käll aktiviteten är tillgängligt som ett objekt. |
| Sekvens |Mål aktiviteten körs bara en gång. Den tar emot en matris med objekt från käll aktiviteten. Utdata från käll aktiviteten är tillgängligt som en matris med objekt. |

### <a name="starting-activity"></a>Startar aktivitet

En grafisk Runbook börjar med aktiviteter som inte har någon inkommande länk. Detta är ofta bara en aktivitet, som skulle fungera som start aktivitet för runbooken. Om flera aktiviteter inte har en inkommande länk börjar runbooken genom att köra dem parallellt. Det följer länkarna för att köra andra aktiviteter som var och en är klar.

### <a name="conditions"></a>Villkor

När du anger ett villkor för en länk körs mål aktiviteten bara om villkoret matchar sant. Du använder vanligt vis en $ActivityOutput variabel i ett villkor för att hämta utdata från käll aktiviteten

För en pipeline-länk anger du ett villkor för ett enskilt objekt, och villkoret utvärderas för varje objekts utdata av käll aktiviteten. Mål aktiviteten körs sedan för varje objekt som uppfyller villkoret. Till exempel, med en käll aktivitet av Get-AzureRmVm, kan följande syntax användas för en villkorlig pipeline-länk för att bara hämta virtuella datorer i resurs gruppen med namnet *Grupp1*.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

För en Sequence-länk utvärderas villkoret bara en gång eftersom en enda matris returneras och innehåller alla objekt som ska matas från käll aktiviteten. Därför kan en Sequence-länk inte användas för filtrering som en pipeline-länk, men det är bara att avgöra om nästa aktivitet körs eller inte. Ta till exempel följande uppsättning aktiviteter i vår Starta VM-Runbook.

![Villkorlig länk med sekvenser](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Det finns tre olika sekvenser som verifierar att värden har angetts till två indataparametrar för Runbook som representerar VM-namn och resurs grupp namn för att fastställa vilken åtgärd som är lämplig för att starta en enskild virtuell dator. starta alla virtuella datorer i resursen grupp eller alla virtuella datorer i en prenumeration. Följande är villkors logiken för nummerföljden mellan Anslut till Azure och hämta en enda virtuell dator:

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

När du använder en villkorlig länk filtreras data som är tillgängliga från käll aktiviteten till andra aktiviteter i grenen av villkoret. Om en aktivitet är källa till flera länkar, beror de data som är tillgängliga för aktiviteter i varje gren på villkoret i länken som ansluter till grenen.

**Start-AzureRmVm** -aktiviteten i runbooken nedan startar till exempel alla virtuella datorer. Det har två villkorliga länkar. Den första villkorliga länken använder uttrycket *$ActivityOutput [' start-AzureRmVM ']. IsSuccessStatusCode-EQ $true* för att filtrera om aktiviteten start-AzureRmVm har slutförts. Den andra använder uttrycket *$ActivityOutput [' start-AzureRmVM ']. IsSuccessStatusCode – Ne $true* för att filtrera om aktiviteten starta-AzureRmVm kunde inte starta den virtuella datorn.

![Exempel på villkorlig länk](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Alla aktiviteter som följer den första länken och som använder aktiviteten utdata från get-AzureVM får bara de virtuella datorer som startades vid tidpunkten då get-AzureVM kördes. Alla aktiviteter som följer den andra länken får bara de virtuella datorer som stoppades vid tidpunkten då get-AzureVM kördes. Alla aktiviteter som följer den tredje länken hämtar alla virtuella datorer oavsett deras körnings tillstånd.

### <a name="junctions"></a>Knut punkter

En Knut punkt är en särskild aktivitet som väntar tills alla inkommande grenar har slutförts. På så sätt kan du köra flera aktiviteter parallellt och se till att alla har slutförts innan du går vidare.

En Knut punkt kan ha ett obegränsat antal inkommande länkar, men inte fler än en av dessa länkar kan vara en pipeline. Antalet länkar för inkommande sekvenser är inte begränsat. Du får skapa en Knut punkt med flera inkommande pipeline-länkar och spara runbooken, men den fungerar inte när den körs.

Exemplet nedan är en del av en Runbook som startar en uppsättning virtuella datorer samtidigt som de uppdateringar som tillämpas på dessa datorer hämtas samtidigt. En Knut punkt används för att säkerställa att båda processerna slutförs innan runbooken fortsätter.

![Knut punkt](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cykler

En cykel är när en mål aktivitet länkar tillbaka till dess käll aktivitet eller till en annan aktivitet som till sist länkar tillbaka till källan. Cykler är för närvarande inte tillåtna i grafisk redigering. Om din Runbook har en cykel sparas den korrekt men får ett fel meddelande när den körs.

![Cykler](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Dela data mellan aktiviteter

Alla data som skrivs ut av en aktivitet med en utgående länk skrivs till *data bussen* för runbooken. Alla aktiviteter i runbooken kan använda data på data bussen för att fylla i parameter värden eller ta med i skript koden. En aktivitet kan komma åt utdata från en tidigare aktivitet i arbets flödet.

Hur data skrivs till data bussen beror på typen av länk för aktiviteten. För en **pipeline**är data utdata som multipler-objekt. För en **Sequence** -länk matas data ut som en matris. Om det bara finns ett värde, matas det ut som en enda element mat ris.

Du kan komma åt data på data bussen med en av två metoder. Först använder du en data källa för **aktivitets utdata** för att fylla i en parameter för en annan aktivitet. Om utdata är ett objekt kan du ange en enskild egenskap.

![Aktivitetens utdata](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Du kan också hämta utdata för en aktivitet i en data källa med **PowerShell-uttryck** eller från en **arbets flödes skript** aktivitet med en ActivityOutput-variabel. Om utdata är ett objekt kan du ange en enskild egenskap. ActivityOutput variabler använder följande syntax.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Kontrollpunkter

Du kan ställa in [kontroll punkter](automation-powershell-workflow.md#checkpoints) i ett grafiskt PowerShell Workflow-Runbook genom att välja *kontroll punkts-Runbook* i valfri aktivitet. Detta gör att en kontroll punkt anges när aktiviteten har körts.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Kontroll punkter är bara aktiverade i grafiska PowerShell Workflow-Runbooks, det är inte tillgängligt i grafiska runbooks. Om Runbook använder Azure-cmdlets bör du följa alla kontroll punkts aktiviteter med en Connect-AzureRmAccount om Runbook-flödet har pausats och startas om från kontroll punkten på en annan arbetare.

## <a name="authenticating-to-azure-resources"></a>Autentisera till Azure-resurser

Runbooks i Azure Automation som hanterar Azure-resurser kräver autentisering till Azure. [Kör som-kontot](automation-create-runas-account.md) (kallas även för tjänstens huvud namn) är standard metoden för att komma åt Azure Resource Manager resurser i din prenumeration med Automation-runbooks. Du kan lägga till den här funktionen i en grafisk Runbook genom att lägga till **AzureRunAsConnection** -anslutnings till gången, som använder PowerShell-cmdleten [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) och [Connect-AzureRmAccount-](/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet: en till arbets ytan. Detta illustreras i följande exempel:

![Aktiviteter för kör som-autentisering](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

Aktiviteten hämta kör som-anslutning (det vill säga get-AutomationConnection) är konfigurerad med en konstant värde data källa med namnet AzureRunAsConnection.

![Konfiguration av kör som-anslutning](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Nästa aktivitet, Connect-AzureRmAccount, lägger till det autentiserade kör som-kontot för användning i runbooken.

![Anslut AzureRmAccount parameter uppsättning](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

> [!IMPORTANT]
> **Add-AzureRmAccount** är nu ett alias för **Connect-AzureRmAccount**. Om du inte ser **Connect-AzureRMAccount**när du söker i biblioteks objekt kan du använda **Add-AzureRMAccount**, eller så kan du uppdatera dina moduler i ditt Automation-konto.

För parametrarna **APPLICATIONID**, **CERTIFICATETHUMBPRINT**och **TENANTID** måste du ange namnet på egenskapen för fält Sök vägen eftersom aktiviteten matar ut ett objekt med flera egenskaper. Annars Miss lyckas autentiseringen vid körning av runbooken. Det här är vad du behöver för att autentisera din Runbook med kör som-kontot.

För att upprätthålla bakåtkompatibilitet för prenumeranter som har skapat ett Automation-konto med hjälp av ett [Azure AD-användarkonto](automation-create-aduser-account.md) för att hantera den klassiska Azure-distributionen eller för Azure Resource Manager resurser är metoden för att autentisera en Add-AzureAccount-cmdlet med en [behörighets till gång](automation-credentials.md) som representerar en Active Directory användare med åtkomst till Azure-kontot.

Du kan lägga till den här funktionen i en grafisk Runbook genom att lägga till en inloggnings resurs till arbets ytan följt av en Add-AzureAccount-aktivitet. Add-AzureAccount använder aktiviteten autentiseringsuppgifter för indatamängden. Detta illustreras i följande exempel:

![Autentiserings aktiviteter](media/automation-graphical-authoring-intro/authentication-activities.png)

Du måste autentisera i början av runbooken och efter varje kontroll punkt. Det innebär att lägga till en addition-AzureAccount-aktivitet efter en kontroll punkt – arbets flödes aktivitet. Du behöver ingen ytterligare aktivitet för autentiseringsuppgifter eftersom du kan använda samma

![Aktivitetens utdata](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Indata och utdata för Runbook

### <a name="runbook-input"></a>Inmatade Runbook

En Runbook kan kräva indata från en användare när de startar runbooken via Azure Portal eller från en annan Runbook om den aktuella används som underordnad.
Om du till exempel har en Runbook som skapar en virtuell dator kan du behöva ange information, till exempel namnet på den virtuella datorn och andra egenskaper varje gången du startar runbooken.

Du accepterar ininformation för en Runbook genom att definiera en eller flera indataparametrar. Du anger värden för dessa parametrar varje gång Runbook startas. När du startar en Runbook med Azure Portal, uppmanas du att ange värden för var och en av Runbook-indataparametrarna.

Du kan komma åt indataparametrar för en Runbook genom att klicka på knappen för **indata och utdata** i Runbook-verktygsfältet.

Då öppnas kontroll av **indata och utdata** där du kan redigera en befintlig indataparameter eller skapa en ny genom att klicka på **Lägg till indata**.

![Lägg till indata](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Varje indataparameter definieras av egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| Namn |Parameterns unika namn. Detta får bara innehålla alfanumeriska tecken och får inte innehålla blank steg. |
| Beskrivning |En valfri beskrivning av indataparametern. |
| Typ |Datatyp förväntas för parametervärdet. Azure Portal ger en lämplig kontroll för data typen för varje parameter när du uppmanas att ange indata. |
| Obligatorisk |Anger om ett värde måste anges för parametern. Det går inte att starta runbooken om du inte anger något värde för varje obligatorisk parameter som inte har något definierat standardvärde. |
| Default Value |Anger vilket värde som ska användas för parametern om ingen har angetts. Detta kan antingen vara null eller ett angivet värde. |

### <a name="runbook-output"></a>Utdata från runbooks

Data som skapats av en aktivitet som inte har någon utgående länk sparas i Runbook- [utdata](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). Utdata sparas med Runbook-jobbet och är tillgängligt för en överordnad runbook när runbooken används som underordnad.

## <a name="powershell-expressions"></a>PowerShell-uttryck

En av fördelarna med grafisk redigering är att du kan skapa en Runbook med minimal kunskap om PowerShell. För närvarande behöver du känna till en viss PowerShell-version för att fylla i vissa [parameter värden](#activities) och för att ange [länk villkor](#links-and-workflow). Det här avsnittet ger en snabb introduktion till PowerShell-uttryck för de användare som kanske inte är bekanta med det. Fullständig information om PowerShell finns i [skript med Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>Data källa för PowerShell-uttryck
Du kan använda ett PowerShell-uttryck som en data källa för att fylla i värdet för en [aktivitets parameter](#activities) med resultatet av en PowerShell-kod. Detta kan vara en enda kodrad som utför en viss enkel funktion eller flera rader som utför en viss komplex logik. Utdata från ett kommando som inte är tilldelade till en variabel matas ut till parametervärdet.

Följande kommando skulle till exempel resultera i det aktuella datumet.

Följande kommando skulle till exempel resultera i det aktuella datumet.

```powershell-interactive
Get-Date
```

Följande kommandon skapar en sträng från det aktuella datumet och tilldelar det till en variabel. Innehållet i variabeln skickas sedan till utdata

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Följande kommandon utvärderar aktuellt datum och returnerar en sträng som anger om den aktuella dagen är en helg eller veckodag.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Aktivitetens utdata

Om du vill använda utdata från en tidigare aktivitet i runbooken använder du variabeln $ActivityOutput med följande syntax.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Du kan till exempel ha en aktivitet med en egenskap som kräver namnet på en virtuell dator i vilket fall du kan använda följande uttryck:

```powershell-interactive
$ActivityOutput['Get-AzureVm'].Name
```

Om den egenskap som krävde objektet för virtuella datorer i stället för bara en egenskap, returnerar du hela objektet med hjälp av följande syntax.

```powershell-interactive
$ActivityOutput['Get-AzureVm']
```

Du kan också använda utdata från en aktivitet i ett mer komplext uttryck, till exempel följande som sammanfogar text till namnet på den virtuella datorn.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVm'].Name
```

### <a name="conditions"></a>Villkor

Använd [jämförelse operatorer](https://technet.microsoft.com/library/hh847759.aspx) för att jämföra värden eller bestämma om ett värde matchar ett angivet mönster. En jämförelse returnerar värdet för antingen $true eller $false.

Följande villkor avgör till exempel om den virtuella datorn från en aktivitet med namnet *Get-AzureVM* har *stoppats*för tillfället.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

Följande villkor kontrollerar om samma virtuella dator är i något annat tillstånd än *stoppad*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Du kan koppla flera villkor med en [logisk operator](https://technet.microsoft.com/library/hh847789.aspx) som **-och** eller **-eller**. Följande villkor kontrollerar exempelvis om samma virtuella dator i föregående exempel är i tillståndet *stoppad* *eller stoppad*.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Hash

[Hash](https://technet.microsoft.com/library/hh847780.aspx) är namn/värde-par som är användbara för att returnera en uppsättning värden. Egenskaper för vissa aktiviteter kan förväntas ha en hash i stället för ett enkelt värde. Du kan också se att en hash-form kallas för en ord lista.

Du skapar en hash-tabellen med följande syntax. En hash-post kan innehålla valfritt antal poster, men varje definieras med ett namn och ett värde.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Till exempel skapar följande uttryck en hash-post som ska användas i data källan för en aktivitets parameter som förväntar sig en hash med värden för en Internets ökning.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

I följande exempel används utdata från en aktivitet som kallas *Hämta Twitter-anslutning* för att fylla i en hash-tabellen.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="next-steps"></a>Efterföljande moment

* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
* Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
* Information om hur du autentiserar med hjälp av Automation kör som-kontot finns i [Konfigurera kör som-konto i Azure](automation-sec-configure-azure-runas-account.md)
