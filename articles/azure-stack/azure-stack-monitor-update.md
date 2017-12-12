---
title: "Övervaka uppdateringar i Azure-stacken använder Privilegierade slutpunkten | Microsoft Docs"
description: "Lär dig använda Privilegierade slutpunkten för att övervaka uppdateringsstatus för Azure-stacken integrerat system."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: mabrigg
ms.openlocfilehash: 55688ad4959d59e41dca9be2d00011e1d41ebd8c
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>Övervaka uppdateringar i Azure-stacken använder Privilegierade slutpunkten

*Gäller för: Azure Stack integrerat system*

Du kan använda Privilegierade slutpunkten för att övervaka förloppet för en Azure-Stack-uppdatering körs och att återuppta en misslyckad uppdatering köras från det senaste lycka steget. 

Följande nya PowerShell-cmdlets för uppdateringshantering ingår i uppdateringen 1710 för Azure-stacken integrerat system.

| Cmdlet  | Beskrivning  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | Returnerar status för uppdateringen för närvarande körs, slutförda och misslyckade. Ger en övergripande status för uppdateringen och ett XML-dokument som beskriver både det aktuella steget och motsvarande tillstånd. |
| `Get-AzureStackUpdateVerboseLog` | Returnerar de utförliga loggar som genereras av uppdateringen. |
| `Resume-AzureStackUpdate` | Återupptar en misslyckad uppdatering vid den punkt där det misslyckades. I vissa fall kanske att slutföra säkerhetsåtgärder innan du fortsätter uppdateringen.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Kontrollera cmdletarna som är tillgängliga
Eftersom cmdletarna som är nya i 1710-uppdateringspaket för Azure-stacken måste 1710 uppdateringsprocessen få till en viss punkt innan övervakning funktion är tillgänglig. Normalt cmdlets är tillgängliga om statusen i administratörsportalen anger att 1710 uppdateringen finns på den **starta om lagring värdar** steg. Mer specifikt cmdlet uppdateringen sker under **steg: kör steg 2.6 - Uppdatera PrivilegedEndpoint godkända**.

Du kan också bestämma om cmdletarna som är tillgängliga via programmering genom att fråga Kommandolistan från Privilegierade slutpunkten. Gör detta genom att köra följande kommandon från maskinvara livscykel värden eller från en arbetsstation för privilegierad åtkomst. Kontrollera också Privilegierade slutpunkten är en betrodd värd. Mer information finns i steg 1 av [komma åt den privilegierade slutpunkten](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). 

1. Skapa en PowerShell-session på någon av ERCS virtuella datorerna i din Azure Stack-miljö (*prefixet*-ERCS01, *prefixet*-ERCS02, eller *prefixet*-ERCS03). Ersätt *Prefix* med den virtuella datorn Prefixsträngen som är specifik för din miljö.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   När du tillfrågas om autentiseringsuppgifter, Använd den &lt; *Azure Stack domän*&gt;\cloudadmin konto eller ett konto som är medlem i gruppen CloudAdmins. Ange samma lösenord som du angav under installationen för AzureStackAdmin domänadministratörskonto för CloudAdmin-kontot.

2. Hämta den fullständiga listan med kommandon som är tillgängliga i Privilegierade slutpunkten. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Avgör om Privilegierade slutpunkten har uppdaterats.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. Lista över kommandona som är specifika för modulen Microsoft.AzureStack.UpdateManagement.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   Exempel:
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Get-AzureStackUpdateVerboseLog                     0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>Använda update management-cmdlets

> [!NOTE]
> Kör följande kommandon från maskinvara livscykel värden eller från en arbetsstation för privilegierad åtkomst. Kontrollera också Privilegierade slutpunkten är en betrodd värd. Mer information finns i steg 1 av [komma åt den privilegierade slutpunkten](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>Anslut till Privilegierade slutpunkten och tilldela sessionsvariabeln

Kör följande kommandon för att skapa en PowerShell-session på någon av ERCS virtuella datorerna i din Azure Stack-miljö (*prefixet*-ERCS01, *prefixet*-ERCS02, eller *prefixet*-ERCS03), och tilldela en sessionsvariabel.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 När du tillfrågas om autentiseringsuppgifter, Använd den &lt; *Azure Stack domän*&gt;\cloudadmin konto eller ett konto som är medlem i gruppen CloudAdmins. Ange samma lösenord som du angav under installationen för AzureStackAdmin domänadministratörskonto för CloudAdmin-kontot.

### <a name="get-high-level-status-of-the-current-update-run"></a>Hämta övergripande status för den aktuella uppdatering-körningen 

För att få en övergripande status för den aktuella uppdatering körningen, kör du följande kommandon: 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

Möjliga värden omfattar:

- Körs
- Slutfört
- Misslyckades 
- Avbrutna

Du kan köra dessa kommandon flera gånger för att se senaste status. Du behöver inte återupprätta en anslutning till Kontrollera igen.

### <a name="get-the-full-update-run-status-with-details"></a>Hämta status för fullständig uppdateringskörningen med information 

Du kan få fullständig uppdatering kör sammanfattning som en XML-sträng. Du kan skriva strängen till en fil för granskning, eller konvertera den till ett XML-dokument och använda PowerShell för att parsa den. Följande kommando Parsar XML-filen för att få en hierarkisk lista över steg som körs för tillfället.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

I följande exempel har steget översta (moln uppdatering) en underordnad plan för att uppdatera och starta om lagring-värdar. Det visar att starta om lagring värdar planen uppdaterar Blob Storage-tjänsten på en av värdarna.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="get-the-verbose-progress-log"></a>Hämta loggen utförlig pågår

Du kan skriva loggen till en fil för granskning. Detta kan hjälpa dig att felsöka en misslyckad uppdatering.

```powershell
$log = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateVerboseLog }

$log > ".\UpdateVerboseLog.txt" 
```

### <a name="actively-view-the-verbose-logging"></a>Visa aktivt utförlig loggning

För att aktivt visa den detaljerade loggen under en uppdatering kör och gå till de senaste posterna kör följande kommandon för att ange sessionen i interaktivt läge och för att visa loggen:

```powershell
Enter-PSSession -Session $pepSession 

Get-AzureStackUpdateVerboseLog -Wait 
```
Loggen uppdateras var 60: e sekund och nytt innehåll (om tillgängligt) skrivs till konsolen. 

Under tidskrävande bakgrundsprocesser kan konsolens utdata inte skrivas till konsolen under en viss tid. Tryck på Ctrl + C om du vill avbryta interaktiva utdata. 

### <a name="resume-a-failed-update-operation"></a>En misslyckad uppdateringsåtgärden återuppta

Om uppdateringen misslyckas, kan du återuppta uppdateringskörningen där den avbröts.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Felsöka

Privilegierade slutpunkten är tillgänglig på alla ERCS virtuella datorer i Azure Stack-miljö. Eftersom den inte är ansluta till en slutpunkt för hög tillgänglighet, kan det uppstå tillfälliga avbrott, varning eller felmeddelanden. Dessa meddelanden kan indikera att sessionen har kopplats från eller att det uppstod ett fel i kommunikationen med tjänsten FN. Det är förväntat. Du kan försök igen om några minuter eller skapa en ny Privilegierade endpoint-session på en av de andra ERCS virtuella datorerna. 

## <a name="next-steps"></a>Nästa steg

- [Hantering av uppdateringar i Azure-stacken](azure-stack-updates.md) 


