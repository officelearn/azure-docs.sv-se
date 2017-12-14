---
title: "Learning PowerShell-arbetsflöde för Azure Automation | Microsoft Docs"
description: "Den här artikeln är avsedd som en snabb lektionen för författare som är bekanta med PowerShell att förstå de specifika skillnaderna mellan PowerShell och PowerShell-arbetsflöde och begrepp som gäller för Automation-runbooks."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 84bf133e-5343-4e0e-8d6c-bb14304a70db
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: magoedte;bwren
ms.openlocfilehash: caa13099b22311502f7a527e4fa017aefeee73c7
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Learning nyckelbegrepp i Windows PowerShell-arbetsflöde för Automation-runbooks 
Azure Automation-Runbooks implementeras som Windows PowerShell-arbetsflöden.  En Windows PowerShell-arbetsflöde är liknar en Windows PowerShell-skript men vissa viktiga skillnader som kan vara förvirrande för en ny användare.  När den här artikeln är avsedd att hjälpa dig att skriva runbooks med PowerShell-arbetsflöde, rekommenderar vi att du skriver runbooks med PowerShell om du inte behöver kontrollpunkter.  Det finns flera syntax skillnader vid redigering av runbooks med PowerShell-arbetsflöde och skillnaderna kräver lite mer arbete att skriva effektiva arbetsflöden.  

Ett arbetsflöde är en sekvens med programmerade, nätverksanslutna steg som utför tidskrävande uppgifter eller kräver samordning av flera steg i flera enheter eller hanterade noder. Fördelarna med ett arbetsflöde jämfört med ett vanligt skript inkluderar möjligheten att utföra en åtgärd mot flera enheter samtidigt och möjligheten att automatisk återställning vid fel. En Windows PowerShell-arbetsflöde är ett Windows PowerShell-skript som använder Windows Workflow Foundation. När arbetsflödet skrivs med Windows PowerShell-syntax och startas av Windows PowerShell, bearbetas men det av Windows Workflow Foundation.

Mer information om ämnen i den här artikeln finns [komma igång med Windows PowerShell-arbetsflöde](http://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>Grundläggande struktur för ett arbetsflöde
Det första steget för att konvertera ett PowerShell-skript till ett PowerShell-arbetsflöde är omsluta den med den **arbetsflöde** nyckelord.  Ett arbetsflöde som startar med den **arbetsflöde** följt av skriptkoden omgiven av klammerparenteser. Namnet på arbetsflödet följer den **arbetsflöde** nyckelord som visas i följande syntax:

    Workflow Test-Workflow
    {
       <Commands>
    }

Namnet på arbetsflödet måste matcha namnet i Automation-runbook. Om runbook importeras sedan filnamnet måste matcha namnet på arbetsflödet och måste sluta med *.ps1*.

Om du vill lägga till parametrar i arbetsflödet, Använd den **Param** nyckelordet precis som du skulle för ett skript.

## <a name="code-changes"></a>Kodändringar
PowerShell-arbetsflöde kod verkar nästan identisk med PowerShell skriptkod förutom några betydande förändringar.  I följande avsnitt beskrivs ändringar som du behöver göra i ett PowerShell-skript för att köra i ett arbetsflöde.

### <a name="activities"></a>Aktiviteter
En aktivitet är en viss aktivitet i ett arbetsflöde. Precis som ett skript består av ett eller flera kommandon, består ett arbetsflöde av en eller flera aktiviteter som utförs i en sekvens. Windows PowerShell-arbetsflöde konverterar automatiskt många av Windows PowerShell-cmdlets för aktiviteter när ett arbetsflöde körs. När du anger en av dessa cmdletar i din runbook, körs motsvarande aktiviteten i Windows Workflow Foundation. För dessa cmdlets utan en motsvarande aktivitet körs Windows PowerShell-arbetsflöde automatiskt cmdleten i en [InlineScript](#inlinescript) aktivitet. Det finns en uppsättning cmdlets som är undantagna och inte kan användas i ett arbetsflöde om du uttryckligen lägga till dem i ett InlineScript-block. Mer information om dessa koncept finns [med hjälp av aktiviteter i Skriptarbetsflöden](http://technet.microsoft.com/library/jj574194.aspx).

Arbetsflödesaktiviteter delar en uppsättning gemensamma parametrar för att konfigurera driften. Mer information om arbetsflödets gemensamma parametrar finns [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Namngivna parametrar
Du kan använda namngivna parametrar med aktiviteter och cmdlets i ett arbetsflöde.  Det innebär är att du måste använda parameternamn.

Tänk dig följande kod som hämtar alla tjänster som körs.

     Get-Service | Where-Object {$_.Status -eq "Running"}

Om du försöker köra samma kod i ett arbetsflöde, får du ett meddelande som ”parameteruppsättningen inte kan matchas med de tillhandahållna namngivna parametrarna”.  Åtgärda detta genom att ange parameternamnet enligt följande.

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### <a name="deserialized-objects"></a>Avserialiserat objekt
Objekt i arbetsflöden är avserialiseras.  Det innebär att deras egenskaper finns kvar, men inte deras metoder.  Tänk dig följande PowerShell-kod som stoppar en tjänst med metoden stopp för Service-objektet.

    $Service = Get-Service -Name MyService
    $Service.Stop()

Om du försöker köra det i ett arbetsflöde, får du ett felmeddelande som säger ”metodanropet inte stöds i en Windows PowerShell-arbetsflöde”.  

Ett alternativ är att omsluta dessa två rader med kod i en [InlineScript](#inlinescript) blockera i vilket fall $Service skulle vara en serviceobjektet i blocket.

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    }

Ett annat alternativ är att använda en annan cmdlet som utför samma funktion som metod, om det inte finns.  I vårt exempel cmdlet Stop-Service fungerar på samma sätt som metod för att stoppa och du kan använda följande för ett arbetsflöde.

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }


## <a name="inlinescript"></a>InlineScript
Den **InlineScript** aktivitet är användbart när du behöver köra ett eller flera kommandon som traditionella PowerShell-skript i stället för PowerShell-arbetsflöde.  Medan kommandon i ett arbetsflöde skickas till Windows Workflow Foundation för bearbetning, bearbetas kommandona i ett InlineScript-block av Windows PowerShell.

InlineScript använder du följande syntax som visas nedan.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Du kan returnera utdata från en InlineScript genom att tilldela en variabel utdata. I följande exempel stoppar en tjänst och matar ut tjänstnamnet.

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Du kan skicka värden i ett InlineScript-block, men du måste använda **$Using** omfångsmodifieraren.  I följande exempel är identiskt med föregående exempel förutom att namnet på tjänsten som tillhandahålls av en variabel.

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


InlineScript-aktiviteter kan vara avgörande i vissa arbetsflöden, stöder inte arbetsflöde för konstruktionerna och bör endast användas när det är nödvändigt av följande skäl:

* Du kan inte använda [kontrollpunkter](#checkpoints) i ett InlineScript-block. Om ett fel uppstår i kodblocket, måste det köras från början av blocket.
* Du kan inte använda [parallell körning](#parallel-processing) inuti en InlineScriptBlock.
* InlineScript påverkar skalbarhet av arbetsflödet eftersom den innehåller Windows PowerShell-sessionen för InlineScript-blockets hela längd.

Läs mer om hur du använder InlineScript [kör du Windows PowerShell-kommandon i ett arbetsflöde](http://technet.microsoft.com/library/jj574197.aspx) och [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Parallell bearbetning
En fördel med Windows PowerShell-arbetsflöden är möjligheten att utföra en uppsättning kommandon parallellt i stället för sekventiellt som i ett vanligt skript.

Du kan använda den **parallella** nyckelord för att skapa ett skriptblock med flera kommandon som körs samtidigt. Detta använder du följande syntax som visas nedan. I det här fallet startar Activity1 och Activity2 på samma gång. Activity3 startas endast efter att både Activity1 och Activity2 har slutförts.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


Tänk dig följande PowerShell-kommandon som kopiera flera filer till en plats i nätverket.  Dessa kommandon körs sekventiellt så att en fil måste avslutas innan nästa startas har kopierats.     

    Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

Följande arbetsflöde kör samma kommandon parallellt så att alla börja kopiera på samma gång.  Endast när de är alla visas kopieras slutförande meddelandet.

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


Du kan använda den **ForEach-Parallel** konstruktion kan kommandon bearbetas för varje objekt i en samling samtidigt. Objekt i samlingen bearbetas parallellt medan kommandona i skriptblocket körs sekventiellt. Detta använder du följande syntax som visas nedan. I det här fallet startar Activity1 samtidigt för alla objekt i samlingen. För varje objekt startar Activity2 efter att Activity1 har slutförts. Activity3 startas endast efter att både Activity1 och Activity2 har slutförts för alla objekt.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

I följande exempel liknar föregående exempel filkopiering parallellt.  I det här fallet visas ett meddelande för varje fil när den kopierar.  Endast när de är alla visas kopierats slutliga slutförande meddelandet.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach -Parallel ($File in $Files)
        {
            Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }

        Write-Output "All files copied."
    }

> [!NOTE]
> Vi rekommenderar inte underordnade runbooks som körs parallellt eftersom det har visat sig ge osäkra resultat.  Utdata från den underordnade runbooken ibland visas inte och inställningar i en underordnad runbook kan påverka andra parallella underordnade runbooks
>

## <a name="checkpoints"></a>Kontrollpunkter
En *kontrollpunkt* är en ögonblicksbild av det aktuella tillståndet för arbetsflödet som innehåller det aktuella värdet för variabler och all utdata som genererats till den punkten. Om ett arbetsflöde slutar i fel eller har pausats, sedan startar nästa gång den körs den från den senaste kontrollpunkten i stället för i början av worfklow.  Du kan ange en kontrollpunkt i ett arbetsflöde med den **Checkpoint-Workflow** aktivitet.

I följande exempelkod ett undantag som inträffar efter Activity2 orsakar arbetsflödet ska sluta. När arbetsflödet körs igen, startar det genom att köra Activity2 eftersom den bara när den senast lagrade kontrollpunkten.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Du bör lagra kontrollpunkter i ett arbetsflöde efter aktiviteter som kan vara utsatt för undantag och inte ska upprepas om arbetsflödet återupptas. Arbetsflödet kan till exempel skapa en virtuell dator. Du kan ange en kontrollpunkt både före och efter kommandona för att skapa den virtuella datorn. Om misslyckas, skulle sedan kommandona upprepas om arbetsflödet startas igen. Om worfklow misslyckas efter skapandet lyckas, sedan skapas den virtuella datorn inte igen när arbetsflödet återupptas.

I följande exempel kopierar flera filer till en nätverksplats och anger en kontrollpunkt efter varje fil.  Om nätverksplatsen tappas bort, slutar arbetsflödet i fel.  När den startas igen fortsätter den med den senaste kontrollpunkten, vilket innebär att endast de filer som redan har kopierats hoppas över.

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

Eftersom användarnamn autentiseringsuppgifter inte sparas när du anropar den [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) aktivitet eller efter den senaste kontrollpunkten, måste du ange autentiseringsuppgifterna som null och sedan hämta dem igen från arkivet tillgången efter  **Pausa arbetsflödet** eller kontrollpunkt anropades.  Annars får du följande felmeddelande: *går inte att återuppta arbetsflödesjobbet, antingen eftersom beständiga data inte kunde spara helt eller spara beständiga data har skadats. Du måste starta om arbetsflödet.*

Samma följande kod visar hur du hanterar detta i PowerShell-arbetsflöde runbooks.

    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

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
          $null = Add-AzureRmAccount -Credential $Cred
         }
     }


Detta är inte nödvändigt om du autentiserar med hjälp av en Kör som-konto konfigureras med en tjänstens huvudnamn.  

Mer information om kontrollpunkter finns [att lägga till kontrollpunkter till ett arbetsflöde för skript](http://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Nästa steg
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
