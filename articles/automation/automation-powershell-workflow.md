---
title: Learning PowerShell-arbetsflöde för Azure Automation
description: Den här artikeln är avsedd som en snabb lektion för författare som är bekant med PowerShell att förstå de specifika skillnaderna mellan PowerShell och PowerShell-arbetsflöde och begrepp som gäller för Automation-runbooks.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 12/14/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c5764c36a646b9639c0eb6463c39b9f014c4272d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58168093"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Lära dig viktiga begrepp i Windows PowerShell-arbetsflöde för Automation-runbooks

Azure Automation-Runbooks implementeras som Windows PowerShell-arbetsflöden.  Ett Windows PowerShell-arbetsflöde är liknar ett Windows PowerShell-skript men vissa viktiga skillnader som kan vara förvirrande för en ny användare.  Även den här artikeln är avsedd att hjälpa dig att skriva runbooks med PowerShell-arbetsflöde, rekommenderar vi att du skriver runbooks med PowerShell om du inte behöver kontrollpunkter.  Det finns flera skillnader i syntaxen vid redigering av PowerShell Workflow-runbooks och dessa skillnader kräver lite mer arbete att skriva effektiva arbetsflöden.

Ett arbetsflöde är en sekvens med programmerade, nätverksanslutna steg som utför tidskrävande uppgifter eller kräver samordning av flera steg i flera enheter eller hanterade noder. Fördelarna med ett arbetsflöde jämfört med ett vanligt skript är möjligheten att samtidigt kunna utföra en åtgärd på flera enheter och möjligheten att automatisk återställning vid fel. Ett Windows PowerShell-arbetsflöde är ett Windows PowerShell-skript som använder Windows Workflow Foundation. När arbetsflödet skrivs med Windows PowerShell-syntax och startas också av Windows PowerShell, bearbetas det av Windows Workflow Foundation.

Mer information om ämnena i den här artikeln finns [komma igång med Windows PowerShell-arbetsflöde](https://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>Grundstrukturen för ett arbetsflöde

Det första steget för att konvertera ett PowerShell-skript i ett PowerShell-arbetsflöde är omslutande den med den **arbetsflöde** nyckelord.  Ett arbetsflöde som börjar med den **arbetsflöde** följt av skriptkoden omgiven av klammerparenteser. Namnet på arbetsflödet följer den **arbetsflöde** nyckelord som du ser i följande syntax:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Namnet på arbetsflödet måste matcha namnet på Automation-runbook. Om runbook importeras sedan filnamnet måste matcha namnet på arbetsflödet och måste sluta med *.ps1*.

Om du vill lägga till parametrar i arbetsflödet genom att använda den **Param** nyckelordet precis som du skulle för ett skript.

## <a name="code-changes"></a>Kodändringar

PowerShell-arbetsflödeskod verkar nästan identiska med PowerShell-skriptkoden förutom några betydande förändringar.  I följande avsnitt beskrivs ändringar som du behöver göra i ett PowerShell-skript för att den ska köras i ett arbetsflöde.

### <a name="activities"></a>Aktiviteter

En aktivitet är en viss uppgift i ett arbetsflöde. Precis som ett skript består av ett eller flera kommandon, består ett arbetsflöde av en eller flera aktiviteter som utförs i en sekvens. Windows PowerShell-arbetsflöde konverterar automatiskt många av Windows PowerShell-cmdlets för aktiviteter när ett arbetsflöde körs. När du anger en av dessa cmdletar i din runbook körs motsvarande aktivitet av Windows Workflow Foundation. För cmdletar där en motsvarande aktivitet saknas, körs Windows PowerShell-arbetsflöde automatiskt cmdleten i en [InlineScript](#inlinescript) aktivitet. Det finns en uppsättning cmdletar som är undantagna och inte kan användas i ett arbetsflöde om du uttryckligen lägga till dem i ett InlineScript-block. Mer information om de här koncepten finns [med hjälp av aktiviteter i Skriptarbetsflöden](https://technet.microsoft.com/library/jj574194.aspx).

Arbetsflödesaktiviteter delar en uppsättning gemensamma parametrar för konfiguration av deras funktion. Mer information om arbetsflödets gemensamma parametrar finns [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Positionsparametrar

Du kan inte använda namngivna parametrar med aktiviteter och cmdlets i ett arbetsflöde.  Det innebär att är att du måste använda parameternamn.

Tänk dig följande kod som hämtar alla tjänster som körs.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Om du försöker köra samma kod i ett arbetsflöde, får du ett meddelande som ”parameteruppsättningen inte kan matchas med angiven namngivna parametrarna”.  Åtgärda detta genom att ange parameternamnet enligt följande.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Avserialiserade objekt

Objekt i arbetsflöden är deserialisera.  Det innebär att deras egenskaper är fortfarande tillgängliga, men inte deras metoder.  Tänk dig följande PowerShell-kod som stoppar en tjänst som Stop-metoden i objektet.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Om du försöker köra det i ett arbetsflöde, får du ett felmeddelande som säger ”metodanropet inte stöds i ett Windows PowerShell-arbetsflöde”.

Ett alternativ är att omsluta följande två rader med kod i en [InlineScript](#inlinescript) blockera i vilket fall $Service skulle vara en serviceobjektet i blocket.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Ett annat alternativ är att använda en annan cmdlet som utför samma funktioner som metoden, om någon är tillgänglig.  Cmdlet Stop-Service fungerar på samma sätt som Stop-metod i vårt exempel och du kan använda följande för ett arbetsflöde.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

Den **InlineScript** aktivitet är användbart när du vill köra ett eller flera kommandon som för traditionella PowerShell-skript i stället för PowerShell-arbetsflöde.  Medan kommandon i ett arbetsflöde skickas till Windows Workflow Foundation för bearbetning, bearbetas kommandona i ett InlineScript-block av Windows PowerShell.

InlineScript använder du följande syntax som visas nedan.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Du kan returnera utdata från en InlineScript genom att tilldela utdatan till en variabel. I följande exempel stoppar en tjänst och som sedan returnerar namnet på tjänsten.

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

Du kan skicka värden i ett InlineScript-block, men du måste använda **$Using** omfångsmodifieraren.  I följande exempel är identiskt med föregående exempel förutom att namnet på tjänsten tillhandahålls av en variabel.

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

Medan InlineScript aktiviteter kan vara nödvändiga i vissa arbetsflöden, stöder inte arbetsflödet konstruktioner och bör endast användas när det är nödvändigt av följande skäl:

* Du kan inte använda [kontrollpunkter](#checkpoints) i ett InlineScript-block. Om ett fel uppstår i kodblocket, måste det köras från början av blocket.
* Du kan inte använda [parallell körning](#parallel-processing) inuti en InlineScriptBlock.
* InlineScript påverkar arbetsflödet skalbarhet eftersom den innehåller Windows PowerShell-sessionen för InlineScript-blockets hela längd.

Läs mer om hur du använder InlineScript [som kör Windows PowerShell-kommandon i ett arbetsflöde](https://technet.microsoft.com/library/jj574197.aspx) och [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Parallell bearbetning

En fördel med Windows PowerShell-arbetsflöden är möjligheten att utföra en uppsättning kommandon parallellt i stället för sekventiellt som i ett vanligt skript.

Du kan använda den **parallella** nyckelord för att skapa ett skriptblock med flera kommandon som körs samtidigt. Detta använder du följande syntax som visas nedan. I det här fallet kommer Activity1 och Activity2 som startar på samma gång. Activity3 startas endast efter att både Activity1 och Activity2 har slutförts.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Tänk dig följande PowerShell-kommandon som kopierar du flera filer till ett mål för nätverket.  Dessa kommandon körs sekventiellt så att en fil måste avslutas kopiera innan nästa startas.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Följande arbetsflöde kör samma kommandon parallellt så att de alla börjar kopiera på samma gång.  Endast när de har alla är kopierade visas slutförandemeddelandet.

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

Du kan använda den **ForEach-Parallel** konstruktion kommandon bearbetas parallellt för varje objekt i en samling. Objekt i samlingen bearbetas parallellt medan kommandona i skriptblocket körs sekventiellt. Detta använder du följande syntax som visas nedan. I det här fallet startar kommer Activity1 på samma gång för alla objekt i samlingen. För varje objekt startar Activity2 efter att Activity1 har slutförts. Activity3 startas endast efter att både Activity1 och Activity2 har slutförts för alla objekt. Vi använder den `ThrottleLimit` parametern för att begränsa parallellitet. För hög en `ThrottleLimit` kan orsaka problem. Perfekt värdet för den `ThrottleLimit` parametern beror på många faktorer i din miljö. Du bör börja med ett lågt värde och försök olika ökande värden tills du hittar en som fungerar för din specifika situation.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

I följande exempel liknar det föregående exemplet kopierar filer parallellt.  I det här fallet visas ett meddelande för varje fil när kopieras.  Endast när de har alla visas helt kopieras det sista meddelandet.

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
> Vi rekommenderar inte underordnade runbooks som körs parallellt eftersom det har visat sig ge otillförlitliga resultat. Utdata från den underordnade runbooken ibland visas inte och inställningar i en underordnad runbook kan påverka andra parallella underordnade runbooks. Till exempel $VerbosePreference och $WarningPreference kan inte spridas till underordnade runbooks. Och om den underordnade runbooken ändras dessa värden kan de kanske inte korrekt återställas efter anrop.

## <a name="checkpoints"></a>Kontrollpunkter

En *kontrollpunkt* är en ögonblicksbild av det aktuella tillståndet för arbetsflödet som innehåller det aktuella värdet för variabler och all utdata som genererats till den punkten. Om ett arbetsflöde slutar i fel eller är inaktiverad, sedan börjar nästa gång den körs den från den senaste kontrollpunkten istället för i början av arbetsflödet.  Du kan ange en kontrollpunkt i ett arbetsflöde med den **Checkpoint-Workflow** aktivitet. Azure Automation har en funktion som kallas [rättmätiga del](automation-runbook-execution.md#fair-share), där valfri runbook som körs i tre timmar är tas bort från minnet för att tillåta körning av andra runbooks. Slutligen bort från minnet runbook ska läsas och när det är de återupptas om körning från den senaste kontrollpunkten som vidtagits i runbooken. För att säkerställa att runbooken slutförs så småningom måste du lägga till kontrollpunkter med intervall som körs i mindre än tre timmar. Under varje körning läggs till en ny kontrollpunkt, och om runbooken som hämtar avlägsnas efter 3 timmar på grund av ett fel, sedan fortsätter runbook på obestämd tid.

I följande exempelkod, ett undantag som inträffar efter Activity2 orsakar arbetsflödet ska sluta. När arbetsflödet körs igen, startar genom att köra Activity2 eftersom det bara när den senast lagrade kontrollpunkten.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Du bör ange kontrollpunkter i ett arbetsflöde efter aktiviteter som kan vara utsatt för undantag och inte ska upprepas om arbetsflödet återupptas. Ditt arbetsflöde kan till exempel skapa en virtuell dator. Du kan ange en kontrollpunkt både före och efter kommandona för att skapa den virtuella datorn. Om misslyckas, skulle sedan kommandona upprepas om arbetsflödet startas igen. Om arbetsflödet misslyckas när skapandet lyckas, sedan skapas den virtuella datorn inte igen när arbetsflödet återupptas.

I följande exempel kopierar flera filer till en nätverksplats och anger en kontrollpunkt efter varje fil.  Om klientens nätverksplats tappas bort, slutar arbetsflödet i fel.  När den startas igen, fortsätter den på den senaste kontrollpunkten, vilket innebär att endast de filer som redan har kopierats hoppas över.

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

Eftersom användarnamn autentiseringsuppgifter inte sparas när du anropar den [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) aktivitet eller när den senaste kontrollpunkten, måste du ange autentiseringsuppgifterna som null och sedan hämta dem igen från arkivet tillgången efter  **Pausa arbetsflöde** eller kontrollpunkt anropas.  I annat fall kan du få följande felmeddelande visas: *Det går inte att återuppta arbetsflödesjobbet, antingen eftersom persistence data inte kunde sparas helt eller sparat persistence data har skadats. Du måste starta om arbetsflödet.*

Följande samma kod visar hur du hanterar detta i PowerShell Workflow-runbooks.

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
> **Add-AzureRmAccount** är nu ett alias för **Connect-AzureRMAccount**. När sökningen biblioteket objekt, om du inte ser **Connect-AzureRMAccount**, du kan använda **Add-AzureRmAccount**, eller så kan du uppdatera dina moduler i ditt Automation-konto.

Detta är inte nödvändigt om du autentiserar med hjälp av ett kör som-konto konfigurerats med ett huvudnamn för tjänsten.

Mer information om kontrollpunkter finns [att lägga till kontrollpunkter till ett arbetsflöde för skript](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Nästa steg

* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)

