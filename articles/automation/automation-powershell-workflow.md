---
title: Utbildnings PowerShell-arbetsflöde för Azure Automation
description: Den här artikeln är avsedd som en snabb lektion för författare som är bekanta med PowerShell för att förstå de olika skillnaderna mellan PowerShell-och PowerShell-arbetsflöde och begrepp som är tillämpliga för Automation-runbooks.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 6e4c8057322b6208ea3b447b264e2bde1344540c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278693"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Inlärnings nyckel koncept för Windows PowerShell-arbetsflöden för Automation-runbooks

Runbooks i Azure Automation implementeras som Windows PowerShell-arbetsflöden.  Ett Windows PowerShell-arbetsflöde liknar ett Windows PowerShell-skript men har några viktiga skillnader som kan vara förvirrande för en ny användare.  Även om den här artikeln är avsedd att hjälpa dig att skriva Runbooks med hjälp av PowerShell-arbetsflöde rekommenderar vi att du skriver Runbooks med hjälp av PowerShell om du inte behöver kontroll punkter.  Det finns flera skillnader i syntaxen när du redigerar PowerShell Workflow-Runbooks och dessa skillnader kräver lite mer arbete för att skriva effektiva arbets flöden.

Ett arbetsflöde är en sekvens med programmerade, nätverksanslutna steg som utför tidskrävande uppgifter eller kräver samordning av flera steg i flera enheter eller hanterade noder. Fördelarna med ett arbetsflöde jämfört med ett vanligt skript är möjligheten att samtidigt kunna utföra en åtgärd på flera enheter och möjligheten att automatisk återställning vid fel. Ett Windows PowerShell-arbetsflöde är ett Windows PowerShell-skript som använder Windows Workflow Foundation. När arbetsflödet skrivs med Windows PowerShell-syntax och startas också av Windows PowerShell, bearbetas det av Windows Workflow Foundation.

Fullständig information om ämnena i den här artikeln finns [komma igång med Windows PowerShell-arbetsflöde](https://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>Grundläggande struktur för ett arbets flöde

Det första steget för att konvertera ett PowerShell-skript till ett PowerShell-arbetsflöde är att stänga det med nyckelordet **arbets flöde** .  Ett arbets flöde börjar med nyckelordet **arbets flöde** följt av bröd texten i skriptet inom klammerparenteser. Namnet på arbets flödet följer arbets **flödets** nyckelord, som du ser i följande syntax:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Namnet på arbetsflödet måste matcha namnet på Automation-runbook. Om runbooken importeras måste fil namnet matcha arbets flödets namn och måste sluta med *. ps1*.

Om du vill lägga till parametrar i arbets flödet använder du nyckelordet **param** precis som du skulle göra med ett skript.

## <a name="code-changes"></a>Kodändringar

PowerShell-arbetsflödet ser nästan likadant ut som skript kod med PowerShell förutom några få betydande ändringar.  I följande avsnitt beskrivs ändringar som du måste göra i ett PowerShell-skript för att det ska kunna köras i ett arbets flöde.

### <a name="activities"></a>Aktiviteter

En aktivitet är en viss uppgift i ett arbetsflöde. Precis som ett skript består av ett eller flera kommandon, består ett arbetsflöde av en eller flera aktiviteter som utförs i en sekvens. Windows PowerShell-arbetsflöde konverterar automatiskt många av Windows PowerShell-cmdlets för aktiviteter när ett arbetsflöde körs. När du anger en av dessa cmdletar i din runbook körs motsvarande aktivitet med Windows Workflow Foundation. För dessa cmdletar utan motsvarande aktivitet, kör Windows PowerShell-arbetsflöde automatiskt cmdleten i en [InlineScript](#inlinescript) -aktivitet. Det finns en uppsättning cmdletar som är undantagna och inte kan användas i ett arbetsflöde om de inte uttryckligen skrivs in i ett InlineScript-block. Mer information om dessa begrepp finns i [använda aktiviteter i skript arbets flöden](https://technet.microsoft.com/library/jj574194.aspx).

Arbetsflödesaktiviteter delar en uppsättning gemensamma parametrar för konfiguration av deras funktion. Mer information om arbets flödets gemensamma parametrar finns [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Positions parametrar

Du kan inte använda positions parametrar med aktiviteter och cmdlets i ett arbets flöde.  Allt detta innebär att du måste använda parameter namn.

Anta till exempel följande kod som hämtar alla tjänster som körs.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Om du försöker köra samma kod i ett arbets flöde får du ett meddelande som "parameter uppsättningen kan inte lösas med de angivna namngivna parametrarna".  Du korrigerar detta genom att ange parameter namnet enligt följande.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Avserialiserade objekt

Objekt i arbets flöden avserialiseras.  Det innebär att deras egenskaper fortfarande är tillgängliga, men inte deras metoder.  Överväg till exempel följande PowerShell-kod som stoppar en tjänst med hjälp av metoden STOP för serviceobjektet.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Om du försöker köra detta i ett arbets flöde får du ett fel meddelande om att "metod anropet inte stöds i ett Windows PowerShell-arbetsflöde".

Ett alternativ är att omsluta dessa två rader med kod i ett [InlineScript](#inlinescript) -block där $service skulle vara ett tjänst objekt i blocket.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Ett annat alternativ är att använda en annan cmdlet som utför samma funktion som metoden, om en sådan finns.  I vårt exempel tillhandahåller cmdleten stoppa-service samma funktioner som metoden stop och du kan använda följande för ett arbets flöde.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

**InlineScript** -aktiviteten är användbar när du behöver köra ett eller flera kommandon som traditionellt PowerShell-skript i stället för PowerShell-arbetsflöde.  Medan kommandon i ett arbetsflöde skickas till Windows Workflow Foundation för bearbetning, bearbetas kommandona i ett InlineScript-block av Windows PowerShell.

InlineScript använder följande syntax som visas nedan.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Du kan returnera utdata från en InlineScript genom att tilldela utdata till en variabel. I följande exempel stoppas en tjänst och sedan genereras tjänst namnet.

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

Du kan skicka värden till ett InlineScript-block, men du måste använda **$using** omfångs modifierare.  Följande exempel är identiskt med föregående exempel, förutom att tjänst namnet tillhandahålls av en variabel.

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

InlineScript-aktiviteter kan vara kritiska i vissa arbets flöden och de har inte stöd för arbets flödes konstruktioner och bör endast användas vid behov av följande orsaker:

* Det går inte att använda [kontroll punkter](#checkpoints) i ett InlineScript-block. Om ett fel inträffar i blocket måste det återupptas från början av blocket.
* Du kan inte använda [parallell körning](#parallel-processing) i en InlineScriptBlock.
* InlineScript påverkar skalbarheten för arbets flödet eftersom den innehåller Windows PowerShell-sessionen för hela längden på InlineScript-blocket.

Mer information om hur du använder InlineScript finns [i köra Windows PowerShell-kommandon i ett arbets flöde](https://technet.microsoft.com/library/jj574197.aspx) och [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Parallell bearbetning

En fördel med Windows PowerShell-arbetsflöden är möjligheten att utföra en uppsättning kommandon parallellt i stället för sekventiellt som i ett vanligt skript.

Du kan använda det **parallella** nyckelordet för att skapa ett skript block med flera kommandon som körs samtidigt. Följande syntax visas nedan. I det här fallet startar Activity1 och Activity2 på samma tidpunkt. Activity3 startar bara efter att både Activity1 och Activity2 har slutförts.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Överväg till exempel följande PowerShell-kommandon som kopierar flera filer till ett nätverks mål.  Dessa kommandon körs i tur och ordning så att en fil måste slutföras innan nästa startas.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Följande arbets flöde kör samma kommandon parallellt så att alla börjar kopieras samtidigt.  När alla har kopierats visas meddelandet slut för ande.

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

Du kan använda den **förgrunds-parallella** konstruktionen för att bearbeta kommandon för varje objekt i en samling samtidigt. Objekt i samlingen bearbetas parallellt medan kommandona i skriptblocket körs sekventiellt. Följande syntax visas nedan. I det här fallet startar Activity1 på samma tid för alla objekt i samlingen. Activity2 startar när Activity1 har slutförts för varje objekt. Activity3 startar bara efter att både Activity1 och Activity2 har slutförts för alla objekt. Vi använder parametern `ThrottleLimit` för att begränsa parallellitet. För hög av en `ThrottleLimit` kan orsaka problem. Det idealiska värdet för parametern `ThrottleLimit` är beroende av många faktorer i din miljö. Du bör försöka starta med ett lågt värde och prova med olika ökande värden tills du hittar något som fungerar för dina speciella omständigheter.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Följande exempel liknar det tidigare exemplet som kopierar filer parallellt.  I det här fallet visas ett meddelande för varje fil efter att den har kopierats.  Det slutliga slut för ande meddelandet visas bara när alla har kopierats fullständigt.

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
> Vi rekommenderar inte att du kör underordnade Runbooks parallellt eftersom detta har visats för att ge otillförlitliga resultat. Utdata från den underordnade runbooken visas ibland inte, och inställningarna i en underordnad Runbook kan påverka de andra parallella underordnade Runbooks. Variabler som $VerbosePreference, $WarningPreference och andra kanske inte sprids till underordnade Runbooks. Och om den underordnade runbooken ändrar dessa värden kanske de inte återställs korrekt efter anrop.

## <a name="checkpoints"></a>Kontrollpunkter

En *kontroll punkt* är en ögonblicks bild av det aktuella läget för det arbets flöde som innehåller det aktuella värdet för variabler och alla utdata som genereras till den punkten. Om ett arbets flöde slutar fungera eller har pausats startar det igen från den senaste kontroll punkten i stället för arbets flödets start.  Du kan ange en kontroll punkt i ett arbets flöde med **kontroll punkt – arbets flödes** aktivitet. Azure Automation har en funktion som kallas [rättvis delning](automation-runbook-execution.md#fair-share), där alla Runbook-flöden som körs i 3 timmar är inaktiverade för att tillåta andra Runbooks att köras. Slutligen kommer den inaktiverade runbooken att läsas in igen och när den är det återupptar den körningen från den senaste kontroll punkten som tagits i runbooken. För att garantera att runbooken slutligen kommer att slutföras, måste du lägga till kontroll punkter i intervall som körs i mindre än tre timmar. Om du lägger till en ny kontroll punkt under varje körning, och om runbooken tas bort efter tre timmar på grund av ett fel, kommer runbooken att återupptas oändligt.

I följande exempel kod inträffar ett undantag efter det att Activity2 orsakar att arbets flödet avslutas. När arbets flödet körs igen startar det genom att köra Activity2 eftersom det var precis efter den senaste kontroll punkts uppsättningen.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Du bör ange kontroll punkter i ett arbets flöde efter aktiviteter som kan vara känsliga för undantag och som inte bör upprepas om arbets flödet återupptas. Arbets flödet kan till exempel skapa en virtuell dator. Du kan ange en kontrollpunkt både före och efter kommandona för att skapa den virtuella datorn. Om det inte går att skapa, upprepas kommandona om arbets flödet startas igen. Om arbets flödet Miss lyckas när skapandet har slutförts skapas inte den virtuella datorn igen när arbets flödet återupptas.

I följande exempel kopieras flera filer till en nätverks plats och anger en kontroll punkt efter varje fil.  Om nätverks platsen tappas bort slutar arbets flödet med fel.  När den startas igen fortsätter den vid den senaste kontroll punkten, vilket innebär att endast de filer som redan har kopierats hoppas över.

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

Eftersom autentiseringsuppgifter för användar namn inte är sparade när du anropar aktiviteten [pausa – arbets flöde](https://technet.microsoft.com/library/jj733586.aspx) eller efter den senaste kontroll punkten, måste du ange autentiseringsuppgifterna till null och sedan hämta dem igen från till gångs lagret efter att du har gjort **uppehåll i arbets flödet** eller kontroll punkten.  Annars kan du få följande fel meddelande: *det går inte att återuppta arbets flödes jobbet, antingen på grund av att beständiga data inte kunde sparas fullständigt eller att sparade beständiga data har skadats. Du måste starta om arbets flödet.*

Följande kod visar hur du hanterar det här i dina PowerShell Workflow-Runbooks.

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
> **Add-AzureRmAccount** är nu ett alias för **Connect-AzureRmAccount**. Om du inte ser **Connect-AzureRMAccount**när du söker i biblioteks objekt kan du använda **Add-AzureRMAccount**, eller så kan du uppdatera dina moduler i ditt Automation-konto.

Detta krävs inte om du autentiserar med hjälp av ett Kör som-konto som kon figurer ATS med ett huvud namn för tjänsten.

Mer information om kontroll punkter finns i [lägga till kontroll punkter i ett skript arbets flöde](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Nästa steg

* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)

