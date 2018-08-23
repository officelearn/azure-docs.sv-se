---
title: Azure Stack syslog-vidarebefordran
description: Lär dig att integrera Azure Stack med övervakningslösningar med hjälp av syslog-vidarebefordran
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 08/14/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: 8e59f2e7e2fceda7f30e12571cd9e2a552f76231
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2018
ms.locfileid: "42059568"
---
# <a name="azure-stack-datacenter-integration---syslog-forwarding"></a>Integrering med Azure Stack datacenter - syslog-vidarebefordran

Den här artikeln visar hur du använder syslog för att integrera Azure Stack-infrastruktur med externa security-lösningar som redan har distribuerats i ditt datacenter. Till exempel ett system Security Information händelsehantering (SIEM). Syslog-kanalen exponerar granskningar, aviseringar och säkerhetsloggar från alla komponenter i Azure Stack-infrastruktur. Använd syslog-vidarebefordran för att integrera med lösningar för säkerhetsövervakning och/eller att hämta alla granskningar, aviseringar och säkerhet loggar för att spara dem för kvarhållning. 

Från och med uppdateringen 1805, har Azure Stack en integrerad syslog-klient som, när konfigurationen är klar, genererar syslog-meddelanden med nyttolast i Common Event Format (CEF). 

> [!IMPORTANT]
> Syslog-vidarebefordran är en förhandsversion. Det bör inte förlita sig på detta i produktionsmiljöer. 

Följande diagram visar huvudkomponenterna som ingår i syslog-integrering.

![Diagram för Syslog-vidarebefordran](media/azure-stack-integrate-security/syslog-forwarding.png)

## <a name="configuring-syslog-forwarding"></a>Konfigurera syslog-vidarebefordran

Syslog-klienten i Azure Stack har stöd för följande konfigurationer:

1. **Syslog via TCP med ömsesidig autentisering (klient och server) och TLS 1.2-kryptering:** i den här konfigurationen både syslog-servern och syslog-klienten kontrollera identiteten på varandra via certifikat. Meddelandena skickas via en krypterad kanal TLS 1.2.

2. **Syslog via TCP med server-autentisering och kryptering av TLS 1.2:** i den här konfigurationen syslog-klienten kan verifiera identiteten för syslog-servern via ett certifikat. Meddelandena skickas via en krypterad kanal TLS 1.2.

3. **Syslog via TCP med Ingen kryptering:** i den här konfigurationen varken syslog-klienten eller syslog-servern verifierar identitet med varandra. Meddelandena skickas i klartext via TCP.

4. **Syslog över UDP med Ingen kryptering:** i den här konfigurationen varken syslog-klienten eller syslog-servern verifierar identitet med varandra. Meddelandena skickas i klartext över UDP.

> [!IMPORTANT]
> Microsoft rekommenderar att du använder TCP som med hjälp av autentisering och kryptering (konfiguration #1 eller vid mycket minsta #2) för produktionsmiljöer som skyddar mot man-in-the-middle-attacker och avlyssning av meddelanden.

### <a name="cmdlets-to-configure-syslog-forwarding"></a>Cmdletar för att konfigurera syslog-vidarebefordran
Konfigurera syslog-vidarebefordran kräver åtkomst till privilegierad slutpunkt (program). Två PowerShell-cmdletar har lagts till detta program att konfigurera syslog-vidarebefordran:


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] 
```
#### <a name="cmdlets-parameters"></a>Cmdlets för parametrar

Parametrar för *Set-SyslogServer* cmdlet:

| Parameter | Beskrivning | Typ |
|---------|---------| ---------|
| *Servernamn* | FQDN eller IP-adress för syslog-servern | Sträng |
|*NoEncryption*| Tvinga klienten att skicka syslog-meddelanden i klartext | Flagga | 
|*SkipCertificateCheck*| Hoppa över verifieringen av det certifikat som tillhandahålls av syslog-servern under den inledande TLS-handskakning | Flagga |
|*SkipCNCheck*| Hoppa över validering av nätverksnamn värdet för det certifikat som tillhandahålls av syslog-servern under den inledande TLS-handskakning | Flagga |
|*UseUDP*| Använda syslog med UDP som transport-protokoll |Flagga |
|*ta bort*| Ta bort konfigurationen av servern från klienten och stoppa syslog-vidarebefordran| Flagga |

Parametrar för *Set-SyslogClient* cmdlet:
| Parameter | Beskrivning | Typ |
|---------|---------| ---------|
| *pfxBinary* | pfx-fil som innehåller certifikatet som ska användas av klienten som identitet för att autentisera mot syslog-servern  | Byte] |
| *CertPassword* |  Lösenord för att importera den privata nyckeln som är associerad med pfx-filen | SecureString |
|*RemoveCertificate* | Ta bort certifikat från klienten | Flagga|

### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>Konfigurera syslog-vidarebefordran med TCP, ömsesidig autentisering och TLS 1.2-kryptering

I den här konfigurationen vidarebefordrar syslog-klienten i Azure Stack de till syslog-servern via TCP, med TLS 1.2-kryptering. Under den inledande handskakningen verifierar klienten att servern tillhandahåller en giltig, betrott certifikat; på samma sätt kan dessutom klienten ett certifikat till servern som identitetsbevis. Den här konfigurationen är det säkraste alternativet eftersom den innehåller en fullständig verifiering av identiteten för både klienten och servern, som skickar meddelanden via en krypterad kanal. 

> [!IMPORTANT]
> Microsoft att rekommenderar du använder den här konfigurationen för produktionsmiljöer. 

Om du vill konfigurera syslog-vidarebefordran med TCP, ömsesidig autentisering och TLS 1.2-kryptering, kör du båda följande cmdlets:
```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```
Klientcertifikatet måste ha samma rot som det som angavs under distributionen av Azure Stack. Det måste också innehålla en privat nyckel.

```powershell
##Example on how to set your syslog client with the ceritificate for mutual authentication. 
##Run these cmdlets from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword 
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>Konfigurera syslog-vidarebefordran med TCP, Server-autentisering och TLS 1.2-kryptering

I den här konfigurationen vidarebefordrar syslog-klienten i Azure Stack de till syslog-servern via TCP, med TLS 1.2-kryptering. Under den inledande handskakningen verifierar klienten också att servern tillhandahåller ett giltigt certifikat som är betrodda. Detta förhindrar att klienten för att skicka meddelanden till ej betrodda mål.
TCP använda autentisering och kryptering är standardkonfigurationen och representerar den lägsta nivån av säkerhet som Microsoft rekommenderar för en produktionsmiljö. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server>
```

Om du vill testa integreringen av syslog-servern med Azure Stack-klienten med hjälp av ett självsignerat och/eller ej betrodda certifikat kan du kan använda dessa flaggor för att hoppa över serververifiering som utförts av klienten under den inledande handskakningen.

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -SkipCNCheck
 
 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -SkipCertificateCheck
```
> [!IMPORTANT]
> Microsoft rekommenderar mot att använda flaggan - SkipCertificateCheck för produktionsmiljöer. 


### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>Konfigurera syslog-vidarebefordran med TCP- och ingen kryptering

I den här konfigurationen vidarebefordrar syslog-klienten i Azure Stack de till syslog-servern via TCP, med Ingen kryptering. Klienten verifiera inte identiteten för servern eller den tillhandahåller sin egen identitet till servern för verifiering. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -NoEncryption
```
> [!IMPORTANT]
> Microsoft rekommenderar mot att använda den här konfigurationen för produktionsmiljöer. 


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>Konfigurera syslog-vidarebefordran med UDP och ingen kryptering

I den här konfigurationen vidarebefordrar syslog-klienten i Azure Stack de till syslog-servern över UDP, med Ingen kryptering. Klienten verifiera inte identiteten för servern eller den tillhandahåller sin egen identitet till servern för verifiering. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -UseUDP
```
UDP med Ingen kryptering är den enklaste att konfigurera, ger inte skydd mot man-in-the-middle-attacker och avlyssning av meddelanden. 

> [!IMPORTANT]
> Microsoft rekommenderar mot att använda den här konfigurationen för produktionsmiljöer. 


## <a name="removing-syslog-forwarding-configuration"></a>Ta bort syslog-vidarebefordran konfiguration

Ta bort syslog-serverkonfiguration helt och hållet och stoppa syslog-vidarebefordran:

**Ta bort syslog-serverkonfiguration från klienten**

```PowerShell  
Set-SyslogServer -Remove
```

**Ta bort klientcertifikatet från klienten**

```PowerShell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>Verifiera installationen syslog

Om du har anslutit syslog-klienten till syslog-servern, bör du snart börja ta emot händelser. Om du inte ser alla händelser, kontrollera konfigurationen av din syslog-klienten genom att köra följande cmdlets:

**Verifiera serverkonfigurationen i syslog-klienten**

```PowerShell  
Get-SyslogServer
```

**Verifiera certifikat-konfiguration i syslog-klienten**

```PowerShell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Schema för Syslog-meddelande

Syslog-vidarebefordran av Azure Stack-infrastruktur skickar meddelanden formaterade i Common Event Format (CEF).
Alla syslog-meddelanden är strukturerad baserat på det här schemat: 

```Syslog
<Time> <Host> <CEF payload>
```

CEF-nyttolasten är baserad på strukturen nedan, men mappningen för varje fält varierar beroende på vilken typ av meddelande (Windows-händelse, avisering som skapats, avisering är stängd).

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0 
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack
* Device Version: 1.0
```

### <a name="cef-mapping-for-windows-events"></a>CEF-mappning för Windows-händelser
```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Allvarlighetsgrad tabell för Windows-händelser: 
| CEF-allvarlighetsgrad | Windows Händelsenivå | Numeriskt värde |
|--------------------|---------------------| ----------------|
|0|Odefinierat|Värde: 0. Anger loggar på alla nivåer|
|10|Kritisk|Värde: 1. Anger loggar för en kritisk varning|
|8|Fel| Värde: 2. Anger loggar för ett fel|
|5|Varning|Värde: 3. Anger loggar för en varning|
|2|Information|Värde: 4. Anger loggar för ett informationsmeddelande|
|0|Utförlig|Värde: 5. Anger loggar på alla nivåer|

Anpassat tilläggs-tabell för Windows-händelser i Azure Stack:
| Namn på anpassade tillägg | Exempel för Windows-händelse | 
|-----------------------|---------|
|MasChannel | System|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| Svchost!! 4132, G, 0! EseDiskFlushConsistency!! ESENT!! 0x800000|
|MasEventDescription| Inställningarna för grupprinciper för användaren har bearbetats. Det fanns inga ändringar har identifierats sedan den senaste lyckade bearbetningen av gruppolicy.|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |Lyckad granskning|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |info|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-GroupPolicy|
|MasSecurityUserId |\<Windows SID\> |
|MasTask |0|
|MasTaskCategory| Skapa process|
|MasUserData|KB4093112!! 5112!! Installerat!! 0x0!! WindowsUpdateAgent Xpath: /Event/UserData / *|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>CEF-mappning för aviseringar som har skapats
```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```
Aviseringar allvarlighetsgrad tabell:
| Severity | Nivå |
|----------|-------|
|0|Odefinierat|
|10|Kritisk|
|5|Varning|

Tabell med anpassade tillägg för aviseringar som skapats i Azure Stack:
| Namn på anpassade tillägg | Exempel | 
|-----------------------|---------|
|MasEventDescription|Beskrivning: Ett användarkonto \<TestUser\> skapades för \<TestDomain\>. Det är en potentiell säkerhetsrisk. --REPARATION: Kontakta supporten. Kundtjänst krävs för att lösa problemet. Försök inte att lösa problemet utan deras hjälp. Innan du öppnar en supportförfrågan, starta filen logginsamlingsprocessen med hjälp av vägledningen från https://aka.ms/azurestacklogfiles |

### <a name="cef-mapping-for-alerts-closed"></a>CEF-mappning för aviseringar som stängts
```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

Exemplet nedan visar en syslog-meddelande med nyttolast i CEF:
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```
## <a name="next-steps"></a>Nästa steg

[Hanteringsprincip](azure-stack-servicing-policy.md)