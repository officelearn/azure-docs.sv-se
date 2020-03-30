---
title: PowerShell-arbetsflöde för Azure Automation
description: Den här artikeln är avsedd som en snabblektion för författare som är bekanta med PowerShell för att förstå de specifika skillnaderna mellan PowerShell och PowerShell-arbetsflödet och begrepp som gäller för Automation-runbooks.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 6e4c8057322b6208ea3b447b264e2bde1344540c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278693"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Utbildningsnyckel-windows powershell-arbetsflödesbegrepp för automationskörningar

Runbooks i Azure Automation implementeras som Windows PowerShell-arbetsflöden.  Ett Windows PowerShell-arbetsflöde liknar ett Windows PowerShell-skript men har några betydande skillnader som kan vara förvirrande för en ny användare.  Den här artikeln är avsedd att hjälpa dig att skriva runbooks med PowerShell-arbetsflöde, men vi rekommenderar att du skriver runbooks med PowerShell om du inte behöver kontrollpunkter.  Det finns flera syntaxskillnader när du redigerar PowerShell Workflow runbooks och dessa skillnader kräver lite mer arbete för att skriva effektiva arbetsflöden.

Ett arbetsflöde är en sekvens med programmerade, nätverksanslutna steg som utför tidskrävande uppgifter eller kräver samordning av flera steg i flera enheter eller hanterade noder. Fördelarna med ett arbetsflöde jämfört med ett vanligt skript är bland annat möjligheten att samtidigt kunna utföra en åtgärd på flera olika enheter samt förmågan till automatisk återställning vid fel. Ett Windows PowerShell-arbetsflöde är ett Windows PowerShell-skript som använder Windows Workflow Foundation. Arbetsflödet skrivs med Windows PowerShell-syntax och startas också av Windows PowerShell, men det bearbetas av Windows Workflow Foundation.

Fullständig information om ämnena i den här artikeln finns i [Komma igång med Windows PowerShell Workflow](https://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>Grundläggande struktur för ett arbetsflöde

Det första steget för att konvertera ett PowerShell-skript till ett PowerShell-arbetsflöde omsluter det med nyckelordet **Arbetsflöde.**  Ett arbetsflöde börjar med nyckelordet **Arbetsflöde** följt av brödtexten i skriptet som omges av klammerparenteser. Namnet på arbetsflödet följer nyckelordet **Arbetsflöde** som visas i följande syntax:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Namnet på arbetsflödet måste matcha namnet på Automation-runbooken. Om runbooken importeras måste filnamnet matcha arbetsflödesnamnet och sluta i *PS1*.

Om du vill lägga till parametrar i arbetsflödet använder du nyckelordet **Param** på samma sätt som i ett skript.

## <a name="code-changes"></a>Kodändringar

PowerShell-arbetsflödeskoden ser nästan identisk ut med PowerShell-skriptkod förutom några viktiga ändringar.  I följande avsnitt beskrivs ändringar som du måste göra i ett PowerShell-skript för att det ska kunna köras i ett arbetsflöde.

### <a name="activities"></a>Aktiviteter

En aktivitet är en viss åtgärd i ett arbetsflöde. Precis som ett skript består av ett eller flera kommandon, består ett arbetsflöde av en eller flera aktiviteter som utförs i en sekvens. När ett arbetsflöde körs i Windows PowerShell konverteras många Windows PowerShell-cmdletar automatiskt till aktiviteter. När du anger en av dessa cmdlets i runbooken körs motsvarande aktivitet av Windows Workflow Foundation. För de cmdlets utan motsvarande aktivitet körs cmdleten automatiskt i en [InlineScript-aktivitet.](#inlinescript) Det finns en uppsättning cmdlets som är undantagna och kan inte användas i ett arbetsflöde om du inte uttryckligen inkluderar dem i ett InlineScript-block. Mer information om dessa begrepp finns [i Använda aktiviteter i skriptarbetsflöden](https://technet.microsoft.com/library/jj574194.aspx).

Arbetsflödesaktiviteter delar en uppsättning gemensamma parametrar för konfiguration av deras funktion. Mer information om vanliga parametrar för arbetsflödet finns [i about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Positionella parametrar

Du kan inte använda positionsparametrar med aktiviteter och cmdlets i ett arbetsflöde.  Allt detta innebär är att du måste använda parameternamn.

Tänk dig till exempel följande kod som hämtar alla tjänster som körs.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Om du försöker köra samma kod i ett arbetsflöde visas ett meddelande som "Parameteruppsättningen kan inte matchas med de angivna namngivna parametrarna".  Om du vill korrigera detta anger du parameternamnet som i följande.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Deserialiserade objekt

Objekt i arbetsflöden deserialiseras.  Detta innebär att deras egenskaper fortfarande är tillgängliga, men inte deras metoder.  Tänk dig till exempel följande PowerShell-kod som stoppar en tjänst med hjälp av stoppmetoden för Service-objektet.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Om du försöker köra detta i ett arbetsflöde visas ett felmeddelande om att "Metod-anrop stöds inte i ett Windows PowerShell-arbetsflöde".

Ett alternativ är att radbrytas dessa två kodrader i ett [InlineScript-block,](#inlinescript) i vilket fall $Service skulle vara ett serviceobjekt i blocket.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Ett annat alternativ är att använda en annan cmdlet som utför samma funktioner som metoden, om en sådan är tillgänglig.  I vårt exempel tillhandahåller Cmdlet för stoppservice samma funktioner som Stopp-metoden, och du kan använda följande för ett arbetsflöde.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

**InlineScript-aktiviteten** är användbar när du behöver köra ett eller flera kommandon som traditionellt PowerShell-skript i stället för PowerShell-arbetsflöde.  Medan kommandon i ett arbetsflöde skickas till Windows Workflow Foundation för bearbetning, bearbetas kommandona i ett InlineScript-block av Windows PowerShell.

InlineScript använder följande syntax som visas nedan.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Du kan returnera utdata från ett InlineScript genom att tilldela utdata till en variabel. I följande exempel stoppas en tjänst och servicenamnet matas sedan ut.

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Du kan skicka värden till ett InlineScript-block, men du måste använda **$Using** scopemodifierare.  Följande exempel är identiskt med föregående exempel förutom att tjänstnamnet tillhandahålls av en variabel.

```powershell
Workflow Stop-MyService
{
    $ServiceName = "MyService"

    $Output = InlineScript {
        $Service = Get-Service -Name $Using:ServiceName
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

InlineScript-aktiviteter kan vara kritiska i vissa arbetsflöden, men de stöder inte arbetsflödeskonstruktioner och bör endast användas när det behövs av följande skäl:

* Du kan inte använda [kontrollpunkter](#checkpoints) i ett InlineScript-block. Om ett fel inträffar i blocket måste det återupptas från början av blocket.
* Du kan inte använda [parallell körning](#parallel-processing) i en InlineScriptBlock.
* InlineScript påverkar arbetsflödets skalbarhet eftersom den innehåller Windows PowerShell-sessionen under hela InlineScript-blockets längd.

Mer information om hur du använder InlineScript finns [i Köra Windows PowerShell-kommandon i ett arbetsflöde](https://technet.microsoft.com/library/jj574197.aspx) och [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Parallellbearbetning

En av fördelarna med Windows PowerShell-arbetsflöden är möjligheten att utföra en uppsättning kommandon parallellt i stället för sekventiellt som i ett vanligt skript.

Du kan använda nyckelordet **Parallel** för att skapa ett skriptblock med flera kommandon som körs samtidigt. Detta använder följande syntax som visas nedan. I det här fallet startar Activity1 och Activity2 samtidigt. Activity3 startar först efter att både Activity1 och Activity2 har slutförts.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Tänk dig till exempel följande PowerShell-kommandon som kopierar flera filer till ett nätverksmål.  Dessa kommandon körs sekventiellt så att en fil måste slutföra kopieringen innan nästa startas.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Följande arbetsflöde kör samma kommandon parallellt så att de alla börjar kopiera samtidigt.  Först efter att alla har kopierats visas det slutförandemeddelande som visas.

```powershell
Workflow Copy-Files
{
    Parallel
    {
        Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
    }

    Write-Output "Files copied."
}
```

Du kan använda **forEach -Parallell** konstruera för att bearbeta kommandon för varje objekt i en samling samtidigt. Objekten i samlingen bearbetas parallellt medan kommandona i skriptblocket körs sekventiellt. Detta använder följande syntax som visas nedan. I det här fallet startar Activity1 samtidigt för alla objekt i samlingen. För varje objekt startar Activity2 när Aktivitet1 har slutförts. Activity3 startar först efter att både Activity1 och Activity2 har slutförts för alla artiklar. Vi använder `ThrottleLimit` parametern för att begränsa parallellismen. För hög `ThrottleLimit` av en kan orsaka problem. Det idealiska `ThrottleLimit` värdet för parametern beror på många faktorer i din miljö. Du bör försöka börja med ett lågt värde och prova olika ökande värden tills du hittar en som fungerar för din specifika omständighet.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Följande exempel liknar föregående exempel när filer kopieras parallellt.  I det här fallet visas ett meddelande för varje fil när den har kopierats.  Först efter att alla har kopierats helt visas det slutliga slutförandemeddelandet.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> Vi rekommenderar inte att köra underordnade runbooks parallellt eftersom detta har visat sig ge otillförlitliga resultat. Utdata från den underordnade runbooken visas ibland inte, och inställningarna i en underordnad runbook kan påverka de andra parallella underordnade runbooksna. Variabler som $VerbosePreference, $WarningPreference och andra får inte spridas till de underordnade runbooks. Och om den underordnade runbooken ändrar dessa värden kanske de inte återställs korrekt efter anrop.

## <a name="checkpoints"></a>Kontrollpunkter

En *kontrollpunkt* är en ögonblicksbild av det aktuella tillståndet för arbetsflödet som innehåller det aktuella värdet för variabler och alla utdata som genereras till den punkten. Om ett arbetsflöde slutar av misstag eller pausas startar det nästa gång det körs från den senaste kontrollpunkten i stället för början av arbetsflödet.  Du kan ange en kontrollpunkt i ett arbetsflöde med aktiviteten **Kontrollpunkt-arbetsflöde.** Azure Automation har en funktion som kallas [rättvis resurs](automation-runbook-execution.md#fair-share), där alla runbook som körs i 3 timmar tas bort för att tillåta andra runbooks att köras. Så småningom kommer den olastade runbooken att laddas om, och när den är det, kommer den att återuppta körningen från den sista kontrollpunkten som tas i runbooken. För att garantera att runbooken så småningom slutförs måste du lägga till kontrollpunkter med intervall som körs i mindre än 3 timmar. Om en ny kontrollpunkt läggs till under varje körning, och om runbooken vräkas efter 3 timmar på grund av ett fel, kommer runbooken att återupptas på obestämd tid.

I följande exempelkod inträffar ett undantag efter att Activity2 som gör att arbetsflödet upphör. När arbetsflödet körs igen börjar det med att köra Activity2 eftersom det var strax efter den senaste kontrollpunktsuppsättningen.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Du bör ange kontrollpunkter i ett arbetsflöde efter aktiviteter som kan vara benägna att göra undantag och bör inte upprepas om arbetsflödet återupptas. Arbetsflödet kan till exempel skapa en virtuell dator. Du kan ange en kontrollpunkt både före och efter kommandona som skapar den virtuella datorn. Om skapandet misslyckas, då kommandona skulle upprepas om arbetsflödet startas igen. Om arbetsflödet misslyckas när skapandet har slutförts skapas inte den virtuella datorn igen när arbetsflödet återupptas.

I följande exempel kopieras flera filer till en nätverksplats och en kontrollpunkt anges efter varje fil.  Om nätverksplatsen försvinner slutar arbetsflödet av misstag.  När den startas igen återupptas den vid den senaste kontrollpunkten, vilket innebär att endast de filer som redan har kopierats hoppas över.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
        Checkpoint-Workflow
    }

    Write-Output "All files copied."
}
```

Eftersom användarnamnsautentiseringsuppgifterna inte sparas efter att du har anropat aktiviteten [Paus-Arbetsflöde](https://technet.microsoft.com/library/jj733586.aspx) eller efter den senaste kontrollpunkten måste du ange att autentiseringsuppgifterna ska null och sedan hämta dem igen från tillgångsarkivet efter **att Paus-Arbetsflöde** eller kontrollpunkt anropas.  Annars kan följande felmeddelande visas: *Arbetsflödesjobbet kan inte återupptas, antingen på grund av att beständighetsdata inte kunde sparas helt eller så har sparade persistensdata skadats. Du måste starta om arbetsflödet.*

Följande samma kod visar hur du hanterar detta i powershellarbetsflödeskörningsböckerna.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzureAutomationCredential -Name "MyCredential"
    $null = Connect-AzureRmAccount -Credential $Cred

    $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzureAutomationCredential -Name "MyCredential"
        $null = Connect-AzureRmAccount -Credential $Cred
        }
}
```

> [!IMPORTANT]
> **Add-AzureRmAccount** är nu ett alias för **Connect-AzureRMAccount**. När du söker i dina biblioteksobjekt, om du inte ser **Connect-AzureRMAccount,** kan du använda **Add-AzureRmAccount**eller uppdatera dina moduler i ditt Automation-konto.

Detta krävs inte om du autentiserar med ett Run As-konto som konfigurerats med ett tjänsthuvudnamn.

Mer information om kontrollpunkter finns i [Lägga till kontrollpunkter i ett skriptarbetsflöde](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Nästa steg

* Information om hur du kommer igång med PowerShell-arbetsflödeskörningsböcker läser du [Min första PowerShell-arbetsflödeskörningsbok](automation-first-runbook-textual.md)

