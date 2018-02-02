---
title: Grafisk redigering i Azure Automation | Microsoft Docs
description: "Grafiska redigering kan du skapa runbooks för Azure Automation utan att arbeta med kod. Den här artikeln innehåller en introduktion till grafiska redigering och all information som behövs för att skapa en grafisk runbook."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 4b6f840c-e941-4293-a728-b33407317943
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 34f896619e8fe02b0a88a8bbf52b9342a72bfd9a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="graphical-authoring-in-azure-automation"></a>Grafisk redigering i Azure Automation
## <a name="introduction"></a>Introduktion
Grafisk redigering kan du skapa runbooks för Azure Automation utan svårigheter underliggande kod för Windows PowerShell eller PowerShell-arbetsflöde. Du lägger till aktiviteter i arbetsytan från ett bibliotek med cmdlets och runbooks, kopplar ihop dem och konfigurera om du vill skapa ett arbetsflöde. Om du någon gång har arbetat med System Center Orchestrator eller Service Management Automation (SMA) sedan detta borde se bekant ut till dig

Den här artikeln innehåller en introduktion till grafiska redigering och begrepp som du behöver att komma igång med att skapa en grafisk runbook.

## <a name="graphical-runbooks"></a>Grafiska runbook-flöden
Alla runbooks i Azure Automation är Windows PowerShell-arbetsflöden. Grafisk och grafisk PowerShell-arbetsflöde runbooks generera PowerShell-kod som körs av Automation arbetare, men det går inte att visa den eller ändra den direkt. En grafisk runbook kan konverteras till en grafisk PowerShell-arbetsflödesrunbook och vice versa, men de kan inte konverteras till text-runbook. En befintlig textrepresentation runbook kan inte importeras till den grafiska redigeraren.

## <a name="overview-of-graphical-editor"></a>Översikt över grafiska redigerare
Du kan öppna grafiska redigerare i Azure-portalen genom att skapa eller redigera en grafisk runbook.

![Grafisk arbetsytan](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

I följande avsnitt beskrivs kontrollerna i grafiska redigerare.

### <a name="canvas"></a>Arbetsytan
Arbetsytan är där du utformar din runbook. Du lägger till aktiviteter från noder i kontrollen biblioteket i runbook och Anslut dem med länkar till definiera logiken för runbook.

Du kan använda reglagen längst ned i arbetsytan för att zooma in och ut.

### <a name="library-control"></a>Biblioteket kontroll
Biblioteket kontrollen är där du väljer [aktiviteter](#activities) att lägga till i din runbook. Du lägger till dem till arbetsytan där du ansluter dem till andra aktiviteter. Det innehåller fyra avsnitt som beskrivs i följande tabell:

| Section | Beskrivning |
|:--- |:--- |
| Cmdlet: ar |Innehåller alla cmdletar som kan användas i din runbook. Cmdlets ordnas efter modulen. Alla moduler som du har installerat i ditt automation-konto är tillgängliga. |
| Runbooks |Innehåller runbooks i ditt automation-konto. Dessa runbooks kan läggas till på arbetsytan som ska användas som underordnade runbooks. Endast runbookar av samma typ core som runbook redigeras visas; för grafisk som runbooks endast PowerShell-baserade runbooks visas, medan för grafisk PowerShell-arbetsflöde runbooks endast PowerShell--Arbetsflödesbaserade runbooks visas. |
| Tillgångar |Innehåller den [automation tillgångar](http://msdn.microsoft.com/library/dn939988.aspx) i ditt automation-konto som kan användas i din runbook. När du lägger till en tillgång till en runbook, läggs en arbetsflödesaktivitet som hämtar den markerade tillgången. Du kan välja om du vill lägga till en aktivitet för att få variabeln eller Ställ in variabeln om variabeln tillgångar. |
| Runbook-kontroll |Innehåller runbook kontroll aktiviteter som kan användas i din aktuella runbook. En *knutpunkt* tar flera inmatningar och väntar tills alla har slutförts innan du fortsätter arbetsflödet. En *kod* aktiviteten körs en eller flera rader med PowerShell eller PowerShell-arbetsflöde kod beroende på vilken typ av grafisk runbook. Du kan använda den här aktiviteten för anpassad kod eller funktioner som är svåra att uppnå med andra aktiviteter. |

### <a name="configuration-control"></a>Konfigurationskontroll
Konfigurationen är där du kan ange information för ett objekt som valts på arbetsytan. Egenskaper som är tillgängliga i den här kontrollen beror på vilken typ av objekt som valts. När du väljer ett alternativ i kontrollen Configuration öppnas ytterligare blad för att ge ytterligare information.

### <a name="test-control"></a>Testa kontrollen
Testa kontrollen visas inte när grafiska redigerare först startas. Det öppnas när du interaktivt [testa en grafisk runbook](#graphical-runbook-procedures). 

## <a name="graphical-runbook-procedures"></a>Grafisk runbook procedurer
### <a name="exporting-and-importing-a-graphical-runbook"></a>Exportera och importera en grafisk runbook
Du kan bara exportera den publicerade versionen av en grafisk runbook. Om runbook inte har ännu publicerats, sedan **exportera** är inaktiverat. När du klickar på den **exportera** knappen runbook hämtas till den lokala datorn. Namnet på filen matchar namnet på en runbook med en *graphrunbook* tillägg.

Du kan importera en grafisk eller grafisk PowerShell-arbetsflöde runbook-filen genom att välja den **importera** alternativ när du lägger till en runbook. När du väljer att importera filen kan du behålla samma **namn** eller ange en ny. Runbook-Type-fältet visas typ av runbook när den utvärderar den markerade filen och om du försöker att välja en annan typ som inte är korrekt, ett meddelande visas om det finns möjliga konflikter och under konverteringen, kan det finnas syntaxfel. 

![Importera runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Testa en grafisk runbook
Du kan testa utkastversionen för en runbook i Azure-portalen medan lämnar den publicerade versionen av runbook oförändrade, eller en ny runbook kan du testa innan den har publicerats. På så sätt kan du kontrollera att runbooken fungerar korrekt innan du ersätter den publicerade versionen. När du testar en runbook körs utkast-runbooken och alla åtgärder som den utför slutförs. Ingen jobbhistorik skapas, men utdata visas i rutan Testutdata. 

Öppna Test-kontroll för en runbook genom att öppna runbook för redigering och klicka sedan på den **Test rutan** knappen.

Testa kontrollen uppmanar för någon indataparametrar och du kan starta runbook genom att klicka på den **starta** knappen.

### <a name="publishing-a-graphical-runbook"></a>Publicera en grafisk runbook
Varje runbook i Azure Automation har ett utkast och en publicerad version. Bara den publicerade versionen är tillgängligt och kan köras och bara utkastet som kan redigeras. Den publicerade versionen påverkas inte av ändringar i utkastet. När utkastet är redo att vara tillgängliga, sedan publicerar du det och som skriver över den publicerade versionen med utkastet.

Du kan publicera en grafisk runbook genom att öppna runbook för redigering och sedan klicka på den **publicera** knappen.

När en runbook inte har ännu publicerats, har den statusen **ny**. När den publiceras, har den statusen **publicerade**. Om du redigerar en runbook när den har publicerats och utkast och publicerad versioner är olika, runbook har statusen **i Redigera**.

![Runbook-status](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png) 

Du har också möjlighet att återgå till den publicerade versionen av en runbook. Detta raderas alla ändringar som gjorts sedan den senast publicerades och ersätter utkastet för runbook med den publicerade versionen.

## <a name="activities"></a>Aktiviteter
Aktiviteter är byggblocken i en runbook. En aktivitet kan vara en PowerShell-cmdlet, en underordnad runbook eller en arbetsflödesaktivitet. Du lägger till en aktivitet i runbook genom att högerklicka i kontrollen biblioteket och markera **Lägg till arbetsytan**. Sedan kan du klicka och dra aktiviteten för att placera den någonstans på arbetsytan som du vill använda. Platsen för aktiviteten på arbetsytan påverkar inte användningen av runbook på något sätt. Du kan utforma din runbook men du tycker att det lämpligaste visualisera dess drift. 

![Lägg till i arbetsytan](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Välj aktiviteten på arbetsytan för att konfigurera dess egenskaper och parametrar i bladet konfiguration. Du kan ändra den **etikett** för aktiviteten till något som är en beskrivning för dig. Ursprungliga cmdleten körs fortfarande kan du bara ändrar dess namn som används i den grafiska redigeraren. Etiketten måste vara unika inom en runbook. 

### <a name="parameter-sets"></a>Parameteruppsättningarna
En parameteruppsättning definierar de obligatoriska och valfria parametrar som accepterar värden för en viss cmdlet. Alla cmdletar har minst en parameter har angetts och vissa har flera. Om en cmdlet har flera parameteruppsättningar, måste du välja vilka som du använder innan du kan konfigurera parametrar. De parametrar som du kan konfigurera beror på den parameteruppsättning du väljer. Du kan ändra den parameteruppsättning som används av en aktivitet genom att välja **parameterinställning** och välja en annan uppsättning. I det här fallet går ett parametervärde som du har konfigurerat förlorade.

I följande exempel har cmdleten Get-AzureRmVM tre parameteruppsättningar. Du kan konfigurera parametervärden förrän du väljer en av mängderna parametern. Parameteruppsättningen ListVirtualMachineInResourceGroupParamSet finns för att returnera alla virtuella datorer i en resursgrupp och en valfri parameter. Den **GetVirtualMachineInResourceGroupParamSet** är för att ange den virtuella datorn du vill gå tillbaka och har två obligatoriska och en valfri parameter.

![Parameteruppsättningen](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Parametervärden
När du anger ett värde för en parameter kan välja du en datakälla för att fastställa hur värdet har angetts. De datakällor som är tillgängliga för en viss parameter är beroende av de giltiga värdena för denna parameter. Till exempel är Null inte ett tillgängligt alternativ för en parameter som inte tillåter null-värden.

| Datakälla | Beskrivning |
|:--- |:--- |
| Konstantvärde |Ange ett värde för parametern. Detta är endast tillgängligt för följande datatyper: Int32, Int64, String, Boolean, DateTime, växel. |
| Aktivitetsutdata |Utdata från en aktivitet som föregår den aktuella aktiviteten i arbetsflödet. Alla giltiga aktiviteter visas. Välj bara aktiviteten för att använda dess utdata för parametervärdet. Om aktiviteten matar ut ett objekt med flera egenskaper, kan du ange namnet på egenskapen när du har valt aktiviteten. |
| Runbook-indata |Välj en runbookinmatningsparameter som indata för Aktivitetsparametern. |
| Variabeltillgång |Välj ett Automation-variabel som indata. |
| Autentiseringsuppgiftstillgång |Välj autentiseringsuppgifter för Automation som indata. |
| Certifikattillgång |Välj ett Automation-certifikat som indata. |
| Anslutningstillgång |Välj en Automationsanslutning som indata. |
| PowerShell-uttryck |Ange enkla [PowerShell-uttryck](#powershell-expressions). Uttrycket utvärderas innan aktiviteten och resultatet som används för parametervärdet. Du kan använda variabler för att referera till utdata från en aktivitet eller en runbookinmatningsparameter. |
| Inte konfigurerade |Rensar alla värden som tidigare har konfigurerats. |

#### <a name="optional-additional-parameters"></a>Valfri ytterligare parametrar
Alla cmdletar har möjlighet att ange ytterligare parametrar. Dessa är vanliga PowerShell-parametrar eller andra anpassade parametrar. Visas med en textruta där du kan ange parametrar med PowerShell-syntax. Till exempel för att använda den **utförlig** gemensamma parametern anger du **”-Verbose: $True”**.

### <a name="retry-activity"></a>Gör om aktivitet
**Omförsök** gör att en aktivitet ska köras flera gånger tills ett visst villkor är uppfyllt, ungefär som en loop. Du kan använda den här funktionen för aktiviteter som ska köras flera gånger, är tillförlitligt och kanske behöver fler än en försöka för lyckad eller testa utdata-information för aktiviteten för giltiga data. 

När du aktiverar retry för en aktivitet kan ange du en fördröjning och ett villkor. Fördröjningen är den tid (i sekunder eller minuter) att runbook ska vänta innan aktiviteten körs igen. Om ingen fördröjning anges körs aktiviteten igen omedelbart när den är klar. 

![Fördröjning av försök igen om en aktivitet](media/automation-graphical-authoring-intro/retry-delay.png)

Försök igen om villkoret är ett PowerShell-uttryck som utvärderas för varje gång som aktiviteten körs. Om uttrycket matchar till True, sedan körs aktiviteten igen. Om uttrycket matchar False, sedan aktiviteten körs inte igen och runbook flyttar till nästa aktivitet. 

![Fördröjning av försök igen om en aktivitet](media/automation-graphical-authoring-intro/retry-condition.png)

Villkor för återförsök kan använda en variabel med namnet $RetryData som ger åtkomst till information om återförsök för aktiviteten. Den här variabeln har egenskaper i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| NumberOfAttempts |Antal gånger som aktiviteten har körts. |
| Resultat |Utdata från den senaste körningen av aktiviteten. |
| TotalDuration |Tidsgränsen överskreds sedan aktiviteten startades första gången. |
| StartedAt |Tid i UTC-format som aktiviteten startades. |

Följande är exempel på aktiviteten försök villkor.

    # Run the activity exactly 10 times.
    $RetryData.NumberOfAttempts -ge 10 

    # Run the activity repeatedly until it produces any output.
    $RetryData.Output.Count -ge 1 

    # Run the activity repeatedly until 2 minutes has elapsed. 
    $RetryData.TotalDuration.TotalMinutes -ge 2

När du har konfigurerat ett villkor för återförsök för aktiviteten innehåller två visuella tips för att påminna dig om aktiviteten. En presenteras i aktiviteten och den andra är när du granskar konfigurationen av aktiviteten.

![Visuella indikatorer för återförsök för aktivitet](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Arbetsflödeskontroll för skript
En kod kontroll är en särskild aktivitet som accepterar PowerShell eller PowerShell-arbetsflöde skript beroende på vilken typ av grafisk runbook som skapats för att ge funktioner som annars inte kanske tillgänglig. Den kan inte acceptera parametrar, men den kan använda variabler för aktivitetens utdata och runbook-indataparametrar. Alla utdata för aktiviteten har lagts till i databussen om den inte har några utgående länka då läggs den till utdataporten för runbook.

Till exempel beräkningar följande kod datum med hjälp av en runbook inkommande variabel med namnet $NumberOfDays. Sedan skickar den ett beräknat datum tid som utdata som ska användas av efterföljande aktiviteter i runbook.

    $DateTimeNow = (Get-Date).ToUniversalTime()
    $DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
    $DateTimeStart


## <a name="links-and-workflow"></a>Länkar och arbetsflöde
En **länk** ansluter två aktiviteter i en grafisk runbook. Den visas på arbetsytan som en pil som pekar från källaktiviteten till målaktiviteten. Aktiviteter som kör i riktning på pilen med målaktiviteten startar när källaktiviteten har slutförts. 

### <a name="create-a-link"></a>Skapa en länk
Skapa en länk mellan två aktiviteter genom att markera aktiviteten källa och klicka på cirkeln längst ned i formen. Dra pilen till målaktiviteten och versionen.

![Skapa en länk](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Klicka på länken om du konfigurerar dess egenskaper i bladet konfiguration. Detta omfattar länktypen som beskrivs i följande tabell:

| Länktypen | Beskrivning |
|:--- |:--- |
| Pipeline |Målaktiviteten körs en gång för varje objektutdata från källaktiviteten. Målaktiviteten körs inte om källaktiviteten ger inga utdata. Utdata från källaktiviteten är tillgänglig som ett objekt. |
| Sekvens |Målaktiviteten körs en gång. Den tar emot en matris med objekt från källaktiviteten. Utdata från källaktiviteten är tillgängliga som en matris med objekt. |

### <a name="starting-activity"></a>Startar aktivitet
En grafisk runbook startas med aktiviteter som inte har en inkommande anslutning. Det här är ofta bara en aktivitet som skulle fungera som den första aktiviteten för runbook. Om flera aktiviteter inte har en inkommande anslutning, startar runbook genom att köra dem parallellt. Följer länkar för att köra andra aktiviteter som varje är klar.

### <a name="conditions"></a>Villkor
När du anger ett villkor på en länk målaktiviteten körs bara om villkoret matchas till true. Du använder vanligtvis en $ActivityOutput variabel i ett villkor för att hämta utdata från källaktiviteten. 

Du anger ett villkor för ett enskilt objekt för en pipelinelänk och villkoret har utvärderats för varje objektutdata i källaktiviteten. Målaktiviteten körs sedan för alla objekt som uppfyller villkoret. Till exempel med en Källaktivitet för Get-AzureRmVm följande syntax kan användas för en villkorlig pipelinelänk för att hämta virtuella datorer i resursgruppen med namnet *Group1*. 

    $ActivityOutput['Get Azure VMs'].Name -match "Group1"

För en sekvens-länk utvärderas bara villkoret som de en gång eftersom returneras en matris som innehåller alla objekt utdata från källaktiviteten. Därför en länk i aktivitetssekvensen kan inte användas för att filtrera som en pipelinelänk, men bara avgör huruvida nästa aktivitet körs. Ta till exempel följande uppsättning aktiviteter i vår starta VM-runbook.<br> ![Villkorlig länk med sekvenser](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)<br>
Det finns tre olika sekvens länkar som verifierar värden angavs till två runbook indataparametrar som representerar namn på virtuell dator och resursgruppens namn för att avgöra vilket är lämplig åtgärd att vidta - startar en enskild virtuell dator, starta alla virtuella datorer i resursen gruppen eller alla virtuella datorer i en prenumeration. Här är sekvens-länken mellan Anslut till Azure och Get enskild VM logiken villkor:

    <# 
    Both VMName and ResourceGroupName runbook input parameters have values 
    #>
    (
    (($VMName -ne $null) -and ($VMName.Length -gt 0))
    ) -and (
    (($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
    )

När du använder en villkorlig länk filtreras data som är tillgängliga från källaktiviteten till andra aktiviteter i den grenen av villkoret. Om en aktivitet är källan till flera länkar, beror data tillgängliga för aktiviteter i varje gren på villkor i länken som ansluter till det kontoret.

Till exempel den **Start AzureRmVm** aktivitet i runbook nedan startar alla virtuella datorer. Den har två villkorliga länkar. Den första villkorlig länken använder uttryck *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - eq $true* att filtrera om aktiviteten starta AzureRmVm har slutförts. Andra använder uttrycket *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - ne $true* att filtrera om aktiviteten starta AzureRmVm gick inte att starta den virtuella datorn. 

![Exempel på villkorlig länk](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Alla aktiviteter som följer den första länken och använder aktivitetsutdata från Get-AzureVM visas endast de virtuella datorerna som startades vid den tidpunkt då Get-AzureVM kördes. Alla aktiviteter som följer den andra länken hämtar endast virtuella datorer som har stoppats vid den tidpunkt då Get-AzureVM kördes. Alla aktiviteter efter den tredje länken hämtar alla virtuella datorer oavsett deras körtillstånd.

### <a name="junctions"></a>Vägkorsningar
Knutpunkt är en särskild aktivitet som väntar tills inkommande grenar har slutförts. På så sätt kan du köra flera aktiviteter parallellt och kontrollera att alla har slutfört innan du fortsätter.

När en förgrening kan ha ett obegränsat antal inkommande länkar, kan inte mer än en av dessa länkar vara en pipeline. Antal inkommande sekvens länkar är inte begränsad. Du kan skapa knutpunkten med flera inkommande pipeline-länkar och spara runbook, men det misslyckas när den körs.

Exemplet nedan är en del av en runbook som startas en uppsättning virtuella datorer och samtidigt hämta korrigeringsfiler som ska tillämpas på dessa datorer. Knutpunkt används för att se till att båda dessa förfaranden måste slutföras innan runbook fortsätter.

![Knutpunkt](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cykler
En cykel är när mål aktivitet länkar tillbaka till dess källaktiviteten eller en annan aktivitet som slutligen länkar tillbaka till källan. Cykler tillåts inte för närvarande i grafiska redigering. Om din runbook har en cykel, sparar korrekt men får ett fel när den körs.

![Cykel](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Dela data mellan aktiviteter
Alla data som är resultatet av en aktivitet med en utgående anslutning av skrivs till den *databussen* för runbook. Alla aktiviteter i runbook kan använda data på i databussen för att fylla i parametervärden eller inkludera i skriptkod. En aktivitet kan komma åt utdata från alla föregående aktiviteter i arbetsflödet. 

Hur data skrivs i databussen beror på vilken typ av länk på aktiviteten. För en **pipeline**, data är utdata som multiplar objekt. För en **sekvens** länka data visas som en matris. Om endast ett värde är utdata som ett enstaka element-matris.

Du kan komma åt data i databussen på något av två sätt. Först använder en **Aktivitetsutdata** datakälla att fylla i en parameter av en annan aktivitet. Om resultatet är ett objekt kan ange du en enskild egenskap.

![Aktivitetsutdata](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Du kan också hämta utdata för en aktivitet i en **PowerShell-uttryck** datakällan eller från en **Arbetsflödesskriptet** aktivitet med en ActivityOutput-variabel. Om resultatet är ett objekt kan ange du en enskild egenskap. ActivityOutput variabler använder du följande syntax.

    $ActivityOutput['Activity Label']
    $ActivityOutput['Activity Label'].PropertyName 

### <a name="checkpoints"></a>Kontrollpunkter
Du kan ange [kontrollpunkter](automation-powershell-workflow.md#checkpoints) i en grafisk PowerShell-arbetsflödesrunbook genom att välja *kontrollpunkt runbook* på alla aktiviteter. Detta leder till en kontrollpunkt anges när aktiviteten körs.

![Kontrollpunkt](media/automation-graphical-authoring-intro/set-checkpoint.png)

Kontrollpunkter är endast aktiverad i grafisk PowerShell-arbetsflöde runbooks, den är inte tillgänglig i grafiska runbook-flöden. Om runbook använder Azure-cmdlets, ska du följa eventuella kontrollpunkt aktivitet med en Add-AzureRMAccount om runbooken har pausats och startar om från den här kontrollpunkten på en annan worker. 

## <a name="authenticating-to-azure-resources"></a>Autentisering till Azure-resurser
Runbooks som hanterar Azure-resurser i Azure Automation kräver autentisering till Azure. Den [kör som-konto](automation-offering-get-started.md#creating-an-automation-account) (kallas även en tjänstens huvudnamn) är standardmetoden åtkomst till Azure Resource Manager-resurser i din prenumeration med Automation-runbooks. Du kan lägga till den här funktionen en grafisk runbook genom att lägga till den **AzureRunAsConnection** anslutningstillgång som använder PowerShell [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) cmdlet, och [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet till arbetsytan. Detta illustreras i följande exempel:<br>![Kör som-aktiviteter för autentisering](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)<br>
Aktiviteten hämta kör som-anslutning (det vill säga Get-AutomationConnection), har konfigurerats med ett konstant värde-datakälla med namnet AzureRunAsConnection.<br>![Kör som-anslutningskonfiguration](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)<br>
Nästa aktivitet Add-AzureRmAccount lägger till autentiserade kör som-kontot för användning i runbook.<br>
![Add-AzureRmAccount Parameter Set](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)<br>
För parametrarna **APPLICATIONID**, **CERTIFICATETHUMBPRINT**, och **TENANTID** måste du ange namnet på egenskapen för fältet sökvägen eftersom aktiviteten matar ut ett objekt med flera egenskaper. Annars när du kör runbook misslyckas försöker autentisera. Det här är vad du behöver minst autentisera din runbook med Kör som-kontot.

För att upprätthålla bakåtkompatibilitet kompatibilitet för prenumeranter som har skapat ett Automation-kontot med en [Azure AD-användarkontot](automation-create-aduser-account.md) att hantera Azure klassisk distribution eller för Azure Resource Manager-resurser, metod för att verifiera cmdleten Add-AzureAccount med en [autentiseringsuppgiftstillgång](automation-credentials.md) som representerar en Active Directory-användare med åtkomst till Azure-konto.

Du kan lägga till den här funktionen en grafisk runbook genom att lägga till en autentiseringstillgång arbetsytan följt av en Add-AzureAccount aktivitet. Lägg till AzureAccount använder aktiviteten autentiseringsuppgifter för indata. Detta illustreras i följande exempel:

![Autentisering aktiviteter](media/automation-graphical-authoring-intro/authentication-activities.png)

Du måste autentisera i början av runbooken och efter varje kontrollpunkt. Det innebär att lägga till en tillägg Add-AzureAccount aktivitet efter alla Checkpoint-Workflow aktivitet. Du behöver inte en ytterligare autentiseringsuppgifter aktivitet eftersom du kan använda samma 

![Aktivitetsutdata](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Runbook indata och utdata
### <a name="runbook-input"></a>Runbook-indata
En runbook kan kräva indata från en användare när de startar sin runbook via Azure-portalen eller från en annan runbook om den aktuella som används som en underordnad.
Om du har en runbook som skapar en virtuell dator måste du kan ge information om exempelvis namnet för den virtuella datorn och andra egenskaper varje gång du startar runbook. 

Du accepterar indata för en runbook genom att definiera en eller flera indataparametrar. Du ange värden för parametrarna varje gång runbook startas. När du startar en runbook med Azure-portalen, uppmanas du att ange värden för varje runbook-indataparametrar.

Du kan komma åt indataparametrar för en runbook genom att klicka på den **ingående och utgående** i verktygsfältet runbook. 

Då öppnas den **indata och utdata** kontroll där du kan redigera en befintlig indataparameter eller skapa en ny genom att klicka på **lägga till indata**. 

![Lägga till indata](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Varje Indataparametern definieras av egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--- |:--- |
| Namn |Det unika namnet för parametern. Detta kan bara innehålla alfanumeriska tecken och får inte innehålla blanksteg. |
| Beskrivning |En valfri beskrivning för indataparametern för. |
| Typ |Datatypen för värdet för parametern. Azure-portalen innehåller en lämplig kontroll för datatypen för varje parameter vid fråga om indata. |
| Obligatorisk |Anger om ett värde måste anges för parametern. Runbook kan inte startas om du inte anger ett värde för varje obligatorisk parameter som inte har något definierat standardvärde. |
| Standardvärde |Anger vilket värde som ska användas för parametern om inget anges. Detta kan antingen vara Null eller ett specifikt värde. |

### <a name="runbook-output"></a>Utdata från Runbooks
Data som skapats av alla aktiviteter som inte har en utgående anslutning är att den [utdata för runbook](http://msdn.microsoft.com/library/azure/dn879148.aspx). Utdata sparas med runbook-jobbet och är tillgängliga för en överordnad runbook när runbook används som en underordnad. 

## <a name="powershell-expressions"></a>PowerShell-uttryck
En av fördelarna med grafiska redigering är att ge dig möjlighet att skapa en runbook med minimal kunskap om PowerShell. För närvarande kan du behöver veta lite PowerShell även om för att fylla vissa [parametervärden](#activities) och för inställningen [länka villkor](#links-and-workflow). Det här avsnittet ger en snabb introduktion till PowerShell-uttryck för de användare som inte kanske är bekant med den. Fullständig information om PowerShell finns på [med Windows PowerShell-skript](http://technet.microsoft.com/library/bb978526.aspx). 

### <a name="powershell-expression-data-source"></a>Datakälla för PowerShell-uttryck
Du kan använda ett PowerShell-uttryck som datakälla för att fylla värdet för en [Aktivitetsparametern](#activities) med resultatet från PowerShell kod. Detta kan vara en rad med kod som utför en enkel funktion eller flera rader som utför vissa komplex logik. Alla utdata från ett kommando som inte har tilldelats en variabel är utdata till parametervärdet. 

Följande kommando skulle till exempel spara det aktuella datumet. 

    Get-Date

Följande kommandon skapar en sträng från det aktuella datumet och tilldela en variabel. Innehållet i variabeln skickas sedan till utdata 

    $string = "The current date is " + (Get-Date)
    $string

Följande kommandon utvärdera det aktuella datumet och returnerar en sträng som anger om den aktuella dagen är en helg eller veckodag. 

    $date = Get-Date
    if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
    else { "Weekday" }


### <a name="activity-output"></a>Aktivitetsutdata
Om du vill använda utdata från en tidigare aktivitet i runbook, använder du variabeln $ActivityOutput med följande syntax.

    $ActivityOutput['Activity Label'].PropertyName

Du kan till exempel ha en aktivitet med en egenskap som kräver att namnet på en virtuell dator i så fall kan du använda följande uttryck:

    $ActivityOutput['Get-AzureVm'].Name

Om den egenskap som krävs för den virtuella datorn i stället för bara en egenskap, skulle du returnera hela objektet med följande syntax.

    $ActivityOutput['Get-AzureVm']

Du kan också använda utdata från en aktivitet i ett mer komplext uttryck, till exempel följande som sammanfogar text till namnet på virtuella datorn.

    "The computer name is " + $ActivityOutput['Get-AzureVm'].Name


### <a name="conditions"></a>Villkor
Använd [jämförelseoperatorer](https://technet.microsoft.com/library/hh847759.aspx) att jämföra värden eller ta reda på om ett värde som matchar ett specifikt mönster. En jämförelse returnerar värdet $true eller $false.

Till exempel följande villkor avgör om den virtuella datorn från en aktivitet med namnet *Get-AzureVM* är för närvarande *stoppats*. 

    $ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"

Följande villkor kontrollerar om samma virtuella dator finns i några tillstånd än *stoppats*.

    $ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"

Du kan ansluta till flera villkor med hjälp av en [logisk operator](https://technet.microsoft.com/library/hh847789.aspx) som **- och** eller **- eller**. Till exempel följande villkor kontrollerar om samma virtuella dator i föregående exempel är i tillståndet *stoppats* eller *stoppar*.

    ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping") 


### <a name="hashtables"></a>Hashtabeller
[Hashtabeller](http://technet.microsoft.com/library/hh847780.aspx) är namn/värde-par som är användbara för att returnera en uppsättning värden. Egenskaper för vissa aktiviteter kan förvänta dig en hash-tabell i stället för ett enkelt värde. Du kan också se som hash-tabell som kallas en ordlista. 

Du kan skapa en hash-tabell med följande syntax. En hash-tabell kan innehålla valfritt antal poster men varje definieras av namn och värde.

    @{ <name> = <value>; [<name> = <value> ] ...}

Följande uttryck skapar till exempel en hash-tabell som ska användas i datakällan för en aktivitetsparameter som förväntas en hash-tabell med värden för en Internetsökning.

    $query = "Azure Automation"
    $count = 10
    $h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
    $h

I följande exempel används utdata från en aktivitet som kallas *hämta Twitter-anslutningen* att fylla i en hash-tabell.

    @{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
      'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
      'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
      'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}



## <a name="next-steps"></a>Nästa steg
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md) 
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
* Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
* Information om hur du autentiserar med hjälp av Automation kör som-kontot finns [konfigurera Azure kör som-konto](automation-sec-configure-azure-runas-account.md)

