---
title: Övervaka uppdateringar i Azure Stack med hjälp av privilegierad slutpunkt | Microsoft Docs
description: Lär dig hur du använder Privilegierade slutpunkten för att övervaka status för säkerhetsuppdatering för integrerade Azure Stack-system.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: mabrigg
ms.reviewer: fiseraci
ms.openlocfilehash: 4641dce6fe8518016ee85cd480de6d11354fe170
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037229"
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>Övervaka uppdateringar i Azure Stack med hjälp av privilegierad slutpunkt

*Gäller för: integrerade Azure Stack-system*

Du kan använda den [privilegierad slutpunkt](azure-stack-privileged-endpoint.md) för att övervaka förloppet för ett Azure Stack uppdateringskörningen och återuppta en misslyckad uppdatering köras från det senaste lyckade steget Azure Stack-portalen blir otillgänglig.  Med hjälp av Azure Stack-portalen är den rekommenderade metoden för att hantera uppdateringar i Azure Stack.

Följande nya PowerShell-cmdletar för hantering av uppdateringar som ingår i uppdateringen 1710 för integrerade Azure Stack-system.

| Cmdlet  | Beskrivning  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | Returnerar status för för närvarande körs, slutförda eller misslyckade uppdateringen. Innehåller övergripande status för uppdateringen och ett XML-dokument som beskriver både det aktuella steget och motsvarande tillstånd. |
| `Resume-AzureStackUpdate` | Återupptar en misslyckad uppdatering vid den punkt där det misslyckades. I vissa fall, kan du behöva slutföra avhjälpande stegen innan du återuppta uppdateringen.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Kontrollera cmdletarna som är tillgängliga
Eftersom cmdletarna som är nytt i 1710 uppdateringspaketet för Azure Stack, måste 1710 uppdateringsprocessen få till en viss punkt innan övervakning funktionen är tillgänglig. Normalt cmdletarna är tillgängliga om statusen i administratörsportalen visar att uppdateringen 1710 är på den **starta om Storage värdar** steg. Mer specifikt cmdlet-uppdateringen sker under **steg: kör steg 2.6 - Uppdatera PrivilegedEndpoint godkända**.

Du kan också bestämma om cmdletarna som är tillgängliga via programmering genom att fråga Kommandolistan från Privilegierade slutpunkten. Gör detta genom att köra följande kommandon från maskinvara livscykel värden eller på en arbetsstation för privilegierad åtkomst. Kontrollera också att Privilegierade slutpunkten är en betrodd värd. Mer information finns i steg 1 av [åt den privilegierade slutpunkten](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). 

1. Skapa en PowerShell-session på någon av de virtuella datorerna i ERCS i Azure Stack-miljön (*prefixet*-ERCS01, *prefixet*-ERCS02, eller *prefixet*-ERCS03). Ersätt *Prefix* med Prefixsträngen för virtuell dator som är specifik för din miljö.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   När du tillfrågas om autentiseringsuppgifter, använda den &lt; *Azure Stack-domänen*&gt;\cloudadmin konto eller ett konto som är medlem i gruppen CloudAdmins. Ange samma lösenord som angavs under installationen för AzureStackAdmin domänadministratörskontot för CloudAdmin-kontot.

2. Få en fullständig lista över kommandon som är tillgängliga i den privilegierade slutpunkten. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Avgör om den privilegierade slutpunkten har uppdaterats.

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
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>Använd update management-cmdletar

> [!NOTE]
> Kör följande kommandon från maskinvara livscykel värden eller på en arbetsstation för privilegierad åtkomst. Kontrollera också att Privilegierade slutpunkten är en betrodd värd. Mer information finns i steg 1 av [åt den privilegierade slutpunkten](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>Anslut till privilegierad slutpunkt och tilldela sessionsvariabeln

Kör följande kommandon för att skapa en PowerShell-session på någon av de virtuella datorerna i ERCS i Azure Stack-miljön (*prefixet*-ERCS01, *prefixet*-ERCS02, eller *prefixet*-ERCS03), och för att tilldela en sessionsvariabeln.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 När du tillfrågas om autentiseringsuppgifter, använda den &lt; *Azure Stack-domänen*&gt;\cloudadmin konto eller ett konto som är medlem i gruppen CloudAdmins. Ange samma lösenord som angavs under installationen för AzureStackAdmin domänadministratörskontot för CloudAdmin-kontot.

### <a name="get-high-level-status-of-the-current-update-run"></a>Hämta övergripande status för den aktuella uppdatering körningen 

Kör följande kommandon för att få en övergripande status för den aktuella uppdatering-körningen: 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

Möjliga värden omfattar:

- Körs
- Slutfört
- Misslyckad 
- Avbrutna

Du kan köra dessa kommandon för flera gånger för att se senaste status. Du behöver att återupprätta en anslutning för att söka igen.

### <a name="get-the-full-update-run-status-with-details"></a>Hämta status för fullständig uppdateringskörningen med information 

Du kan hämta den fullständiga uppdateringskörningen sammanfattning som en XML-sträng. Du kan skriva strängen till en fil för undersökning, eller konvertera den till ett XML-dokument och använda PowerShell för att parsa den. Kommandot Parsar XML-filen för att få en hierarkisk lista över steg som för närvarande körs.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

I följande exempel har översta steg (Cloud uppdatering) en underordnad plan för att uppdatera och starta om storage-värdar. Den visar att starta om Storage värdar planen uppdateras Blob Storage-tjänsten på en av värdarna.

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

### <a name="resume-a-failed-update-operation"></a>Återuppta drift för en misslyckad uppdatering

Om uppdateringen misslyckas, kan du återuppta uppdateringskörningen där den avbröts.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Felsöka

Privilegierade slutpunkten är tillgänglig på alla ERCS virtuella datorer i Azure Stack-miljön. Eftersom den inte är ansluta till en slutpunkt för hög tillgänglighet, kan det finnas tillfälliga avbrott, varning eller felmeddelanden. Dessa meddelanden kan tyda på att sessionen kopplades eller att det uppstod ett fel i kommunikationen med tjänsten FN. Det här beteendet är förväntat. Du kan försök igen om några minuter eller skapa en ny privilegierad slutpunkt-session på en av de andra ERCS virtuella datorerna. 

## <a name="next-steps"></a>Nästa steg

- [Hantera uppdateringar i Azure Stack](azure-stack-updates.md) 


