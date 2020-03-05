---
title: Fel söknings problem som orsakas av program som inte stöder TLS 1,2 | Microsoft Docs
description: Fel söknings problem som orsakas av program som inte stöder TLS 1,2
services: cloud-services
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: top-support-issue
ms.assetid: ''
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/17/2020
ms.author: tagore
ms.openlocfilehash: a9d15a94421694583562f433c20413fcc84697c7
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270866"
---
# <a name="troubleshooting-applications-that-dont-support-tls-12"></a>Felsöka program som inte stöder TLS 1,2
I den här artikeln beskrivs hur du aktiverar de äldre TLS-protokollen (TLS 1,0 och 1,1) samt tillämpar äldre chiffersviter för att stödja ytterligare protokoll i webb-och arbets roller för Windows Server 2019 Cloud service. 

Vi är införstådd med att vi vidtar åtgärder för att föråldra TLS 1,0 och TLS 1,1, men våra kunder kan behöva stödja de äldre protokollen och chiffersviter för att kunna planera för deras utfasning.  Vi rekommenderar att du inte aktiverar de här äldre värdena igen, men vi ger vägledning för att hjälpa kunderna. Vi uppmuntrar kunderna att utvärdera risken för regression innan du implementerar de ändringar som beskrivs i den här artikeln. 

> [!NOTE]
> Gäst operativ system familj 6-versioner upprätthåller TLS 1,2 genom att inaktivera 1,0/1.1-chiffer. 


## <a name="dropping-support-for-tls-10-tls-11-and-older-cipher-suites"></a>Släpper stöd för TLS 1,0, TLS 1,1 och äldre chiffersviter 
Som ett stöd för vårt åtagande att använda den bästa krypteringen kan Microsoft lansera sina planer på att starta migreringen från TLS 1,0 och 1,1 i juni 2017.   Eftersom det första meddelandet tillkännagede Microsoft oss att inaktivera Transport Layer Security (TLS) 1,0 och 1,1 som standard i versioner av Microsoft Edge och Internet Explorer 11 som stöds i första hälften av 2020.  Liknande meddelanden från Apple, Google och Mozilla visar i vilken riktning branschen är påslagen.   

## <a name="tls-configuration"></a>TLS-konfiguration  
Windows Server 2019 Cloud Server-avbildningen har kon figurer ATS med TLS 1,0 och TLS 1,1 inaktiverat på register nivå. Det innebär att program som distribueras till den här versionen av Windows och använder Windows stack för TLS-förhandling inte tillåter TLS 1,0 och TLS 1,1-kommunikation.   

Servern levereras också med en begränsad uppsättning chiffersviter: 

```
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 
```

## <a name="step-1-create-the-powershell-script-to-enable-tls-10-and-tls-11"></a>Steg 1: skapa PowerShell-skriptet för att aktivera TLS 1,0 och TLS 1,1 

Använd följande kod som exempel för att skapa ett skript som aktiverar de äldre protokollen och chiffersviter. I den här dokumentationen får skriptet namnet: **TLSsettings. ps1**. Lagra skriptet på ditt lokala skriv bord för enkel åtkomst i senare steg. 


```Powershell
#******************* FUNCTION THAT ACTUALLY UPDATES KEYS; WILL RETURN REBOOT FLAG IF CHANGES *********************** 
 
Function Set-CryptoSetting {  
    param (  
        $regKeyName,  
        $value,  
        $valuedata,  
        $valuetype       
    )  
    
    $restart = $false 
  
    # Check for existence of registry key, and create if it does not exist  
    If (!(Test-Path -Path $regKeyName)) {  
        New-Item $regKeyName | Out-Null  
    }  
 
    # Get data of registry value, or null if it does not exist  
    $val = (Get-ItemProperty -Path $regKeyName -Name $value -ErrorAction SilentlyContinue).$value  
 
    If ($val -eq $null) {  
        # Value does not exist - create and set to desired value  
        New-ItemProperty -Path $regKeyName -Name $value -Value $valuedata -PropertyType $valuetype | Out-Null  
        $restart = $true 
    } 
 
    Else {  
        # Value does exist - if not equal to desired value, change it  
        If ($val -ne $valuedata) {  
            Set-ItemProperty -Path $regKeyName -Name $value -Value $valuedata  
            $restart = $true  
        }  
    }  
 
    $restart  
}  
 
#*************************************************************************************************************** 
 
#****************************** CIPHERSUITES FOR OS VERSIONS WINDOWS 10 AND ABOVE ****************************** 
 
function Get-BaseCipherSuitesWin10Above() 
{ 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384," 
 
# Legacy cipher suites 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256," 
        $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384,"  
        $cipherorder += "TLS_RSA_WITH_AES_128_GCM_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA," 
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA" 
 
 return $cipherorder 
} 
 

#*************************************************************************************************************** 
 
 
#********************************************** REGISTRY KEYS **************************************************** 
 
 
function Get-RegKeyPathToEnable() 
{ 
    $regKeyPath = @( 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2",         
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client", 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" 
    ) 
    return $regKeyPath 
} 
 
#*************************************************************************************************************** 
 
$localRegistryPath = @() 
 
# Enable TLS 1.2, TLS 1.1 and TLS 1.0 
$localRegistryPath += Get-RegKeyPathToEnable 
 
#******************* CREATE THE REGISTRY KEYS IF THEY DON'T EXIST******************************** 
 
# Check for existence of the registry keys, and create if they do not exist  
For ($i = 0; $i -lt $localRegistryPath.Length; $i = $i + 1) {  
   Write-Host "Checking for existing of key: $($localRegistryPath[$i]) Severity Level: Information"
   If (!(Test-Path -Path $localRegistryPath[$i])) {
        New-Item $localRegistryPath [$i] | Out-Null
    Write-Host "Creating key: $($localRegistryPath[$i]) Severity Level: Information"
    }
}  
 
#********************************* EXPLICITLY Enable TLS12,  TLS11 and TLS10********************************* 
 
For ($i = 0; $i -lt $localRegistryPath.Length; $i = $i + 1) { 
    if ($localRegistryPath[$i].Contains("Client") -Or $localRegistryPath[$i].Contains("Server")) { 
      Write-Host "Enabling this key: $($localRegistryPath[$i]) Severity: Information "
        $result = Set-CryptoSetting $localRegistryPath[$i].ToString() Enabled 1 DWord   
        $result = Set-CryptoSetting $localRegistryPath[$i].ToString() DisabledByDefault 0 DWord  
        $reboot = $reboot -or $result 
    } 
} 
 
#**************************************** SET THE CIPHER SUITE ORDER******************************** 
 
$cipherlist = @() 
 
# Set cipher suite order 
$cipherlist += Get-BaseCipherSuitesWin10Above 
$CipherSuiteRegKey = "HKLM:\SOFTWARE\Policies\Microsoft\Cryptography\Configuration\SSL\00010002"  
 
if (!(Test-Path -Path $CipherSuiteRegKey))  
{  
    New-Item $CipherSuiteRegKey | Out-Null  
    $reboot = $True  
    Write-Host "Creating key: $($CipherSuiteRegKey) Severity: Information "
}  
 
#Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherorder  
Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherlist  
#********************************************* REBOOT ******************************************* 
 
Write-Host "A reboot is required in order for changes to effect"  
Write-Host "Rebooting now..."  
shutdown.exe /r /t 5 /c "Crypto settings changed" /f /d p:2:4
```

## <a name="step-2-create-a-command-file"></a>Steg 2: skapa en kommando fil 

Skapa en CMD-fil med namnet **RunTLSSettings. cmd** med hjälp av nedan. Lagra skriptet på ditt lokala skriv bord för enkel åtkomst i senare steg. 

```cmd
PowerShell -ExecutionPolicy Unrestricted %~dp0TLSsettings.ps1
REM This line is required to ensure the startup tasks does not block the role from starting in case of error.  DO NOT REMOVE!!!! 
EXIT /B 0
```

## <a name="step-3-add-the-startup-task-to-the-roles-service-definition-csdef"></a>Steg 3: Lägg till Start uppgiften i rollens tjänst definition (csdef) 

Lägg till följande kodfragment i den befintliga tjänst definitions filen. 


```
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
```

Här är ett exempel som visar både arbets rollen och webb rollen. 

```
<?xmlversion="1.0"encoding="utf-8"?> 
<ServiceDefinitionname="CloudServiceName"xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition"schemaVersion="2015-04.2.6"> 
    <WebRolename="WebRole1"vmsize="Standard_D1_v2"> 
        <Sites> 
            <Sitename="Web"> 
                <Bindings> 
                    <Bindingname="Endpoint1"endpointName="Endpoint1"/> 
                </Bindings> 
            </Site> 
        </Sites> 
        <Startup> 
            <Taske xecutionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
            </Task> 
        </Startup> 
        <Endpoints> 
            <InputEndpointname="Endpoint1"protocol="http"port="80"/> 
        </Endpoints> 
    </WebRole> 
<WorkerRolename="WorkerRole1"vmsize="Standard_D1_v2"> 
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
</WorkerRole> 
</ServiceDefinition> 
```

## <a name="step-5-add-the-scripts-to-your-cloud-service"></a>Steg 5: Lägg till skripten i moln tjänsten 

1) I Visual Studio högerklickar du på din webroll
2) Välj **Lägg till**
3) Välj **befintligt objekt**
4) I Utforskaren navigerar du till Skriv bordet där du sparade filerna **TLSsettings. ps1** och **RunTLSSettings. cmd** 
5) Markera de två filerna för att lägga till dem i ditt Cloud Services-projekt

## <a name="step-6-enable-copy-to-output-directory"></a>Steg 6: Aktivera kopiera till utdatakatalogen

För att se till att skripten laddas upp med varje uppdatering som skickas från Visual Studio måste inställningen *Kopiera till utdata* vara inställd på *Kopiera alltid*

1) Under din webrole högerklickar du på RunTLSSettings. cmd
2) Välj **Egenskaper**
3) På fliken Egenskaper ändrar du *Kopiera till utmatnings katalog* till *Kopiera alltid "*
4) Upprepa stegen för **TLSsettings. ps1**

## <a name="step-7-publish--validate"></a>Steg 7: publicera & Verifiera

Nu när du har slutfört ovanstående steg kan du publicera uppdateringen till din befintliga moln tjänst. 

Du kan använda [SSLLabs](https://www.ssllabs.com/) för att validera TLS-statusen för dina slut punkter 

 
