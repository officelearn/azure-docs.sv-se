---
title: Rotera hemligheter i Azure Stack | Microsoft Docs
description: Lär dig mer om att rotera dina hemligheter i Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 27fd02503881ef1f0587ccb0301f8490101d5bcb
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720653"
---
# <a name="rotate-secrets-in-azure-stack"></a>Rotera hemligheter i Azure Stack

*Dessa anvisningar gäller endast för Azure Stack integrerade system Version 1803 och senare. Försök inte hemliga rotation i förväg 1802 Azure Stack-versioner*

Azure Stack använder olika hemligheter för att underhålla säker kommunikation mellan resurser i Azure Stack-infrastrukturen och tjänster.

- **Intern hemligheter**  
Alla de certifikat, lösenord, säker strängar och nycklar som används av Azure Stack-infrastruktur utan inblandning av Azure Stack-operatör. 

- **Externa hemligheter**  
Infrastruktur tjänstcertifikat för externa tjänster som tillhandahålls av Azure Stack-operatör. Detta inkluderar certifikat för följande tjänster: 
  - Administratörsportal  
  - Offentlig Portal  
  - Administratören Azure Resource Manager  
  - Global Azure Resource Manager  
  - Administratören Keyvault  
  - KeyVault  
  - Admininistrationstillägg värden  
  - ACS (inklusive blob-, tabell- och queue storage)  
  - AD FS *  
  - Graph *  

\* Gäller endast om den miljön identitetsprovider är Active Directory Federation Services (AD FS).

> [!Note]  
> Alla andra skydda nycklar och strängar, inklusive BMC och byta lösenord, lösenord för användare och administratör uppdateras manuellt av administratören. 

> [!Note]  
> Från och med Azure Stack 1811 har hemliga rotation separerats för interna och externa certifikat. 

Operatörer måste regelbundet rotera sin infrastruktur hemligheter på frekvenser som är konsekventa med organisationens säkerhetskrav för att upprätthålla integriteten hos Azure Stack-infrastruktur.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Rotera hemligheter med externa certifikat från en ny certifikatutfärdare

Azure Stack stöder hemliga rotation med externa certifikat från en ny certifikat (CA) i följande kontexter:

|Installerade certifikat CA: N|Certifikatutfärdaren för att rotera till|Stöds|Azure Stack-versioner som stöds|
|-----|-----|-----|-----|
|Från självsignerat|Enterprise-|Stöds inte||
|Från självsignerat|Att självsignerat|Stöds inte||
|Från självsignerat|Till offentligt<sup>*</sup>|Stöds|1803 och senare|
|Från Enterprise|Enterprise-|Så länge som kunder använder samma företagets Certifikatutfärdare som används vid distribution som stöds|1803 och senare|
|Från Enterprise|Att självsignerat|Stöds inte||
|Från Enterprise|Till offentligt<sup>*</sup>|Stöds|1803 och senare|
|Från offentlig<sup>*</sup>|Enterprise-|Stöds inte|1803 och senare|
|Från offentlig<sup>*</sup>|Att självsignerat|Stöds inte||
|Från offentlig<sup>*</sup>|Till offentligt<sup>*</sup>|Stöds|1803 och senare|

<sup>*</sup>Anger att den offentliga certifikatutfärdare som är de som ingår i Windows Trusted Root Program. Du hittar en fullständig lista i artikeln [Microsoft Trusted Root Certificate Program: Deltagare (från och med 27 juni 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>Reparation av aviseringar

När hemligheter är inom 30 dagar från upphör att gälla, genereras följande aviseringar i Administratörsportalen: 

- Lösenordets giltighetstid för väntande tjänster-konto 
- Väntar på interna certifikatet upphör att gälla 
- Väntande externt certifikat upphör att gälla 

Kör hemliga rotation med hjälp av anvisningarna nedan kommer att åtgärda dessa aviseringar.

> [!Note]  
> Azure Stack-miljöer i förväg 1811 versioner kan se aviseringar för väntande internt certifikat eller Hemlig förfallodatum. Dessa aviseringar är felaktig och bör ignoreras utan att köra interna hemliga rotation. Felaktiga utgångsdatum för interna hemliga aviseringar är ett känt problem som löses i 1811 – intern hemligheter inte upphör att gälla om miljön har varit aktiva under två år. 

## <a name="pre-steps-for-secret-rotation"></a>Steg före hemliga rotation

   > [!IMPORTANT]  
   > Om hemlig rotation redan har gjorts på Azure Stack-miljön måste du uppdatera systemet till version 1811 eller senare innan du kan köra hemliga roteringen igen. Hemlig Rotation måste köras av den [privilegierad slutpunkt](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) och kräver autentiseringsuppgifter för Azure Stack-operatör. Om din miljö Azure Stack operatören eller operatörerna inte vet om hemliga rotation har körts på miljön, kan du uppdatera till 1811 innan du kör hemliga roteringen igen.

1. Vi rekommenderar starkt att du uppdaterar din Azure Stack-instans till version 1811.

    > [!Note] 
    > Du behöver inte rotera hemligheter för att lägga till värden tilläggscertifikat för pre-1811 versioner. Du bör följa anvisningarna i artikeln [förbereda för tillägget för värd för Azure Stack](azure-stack-extension-host-prepare.md) att lägga till tillägget värd certifikat.

2.  Operatörer kan få aviseringar öppnar och stänger automatiskt under rotation av Azure Stack-hemligheter.  Det här beteendet är förväntat och aviseringar kan ignoreras.  Operatörer kan verifiera giltigheten för de här aviseringarna genom att köra **Test-AzureStack**.  Operatörer med SCOM för att övervaka Azure Stack-system, placera ett system i underhållsläge hindrar dessa aviseringar från att nå sina ITSM-system, men fortsätter att varna om Azure Stack-system blir oåtkomlig. 
3. Meddela användarna om eventuella underhållsåtgärder. Schemalägga normala underhållsfönster, så mycket som möjligt, under tider utanför kontorstid. Underhåll kan påverka både användaren arbetsbelastningar och åtgärder.

    > [!Note]  
    > Nästa steg gäller endast när du roterar externa hemligheter i Azure Stack.

4. Kör **[Test-AzureStack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)** och bekräfta att alla test utdata är felfria före rotera hemligheter.
5. Förbered en ny uppsättning ersättning externa certifikat. Den nya uppsättningen matchar certifikat-specifikationer som beskrivs i den [Azure Stack PKI-certifikatkrav](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs). Du kan generera en certifikatsigneringsförfrågan (CSR) för att köpa eller skapa nya certifikat genom att följa stegen i [generera PKI-certifikat](https://docs.microsoft.com/azure/azure-stack/azure-stack-get-pki-certs) och förbereda dem för användning i Azure Stack-miljön med hjälp av stegen i [ Förbered PKI-certifikat för Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-prepare-pki-certs). Glöm inte att verifiera de certifikat som du förbereder med de steg som beskrivs i [Validera PKI-certifikat](https://docs.microsoft.com/azure/azure-stack/azure-stack-validate-pki-certs). 
6.  Store en tillbaka upp till de certifikat som används för rotation på ett säkert ställe. Om din rotation körs och misslyckas, ersätter du certifikat i filresursen med säkerhetskopiorna innan du kör rotationen. Observera, göra säkerhetskopior i säker säkerhetskopieringsplatsen.
7.  Skapa en filresurs som du kan komma åt från de ERCS virtuella datorerna. Filresursen måste vara Läs- och skrivbara för den **CloudAdmin** identitet.
8.  Öppna ett PowerShell ISE-konsolen från en dator där du har åtkomst till filresursen. Gå till din filresurs. 
9.  Kör **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** att skapa de nödvändiga katalogerna för ditt externa certifikat.

## <a name="rotating-external-secrets"></a>Rotera externa hemligheter

Så här roterar externa hemligheter:

1. I den nyligen skapade **certifikat** katalog som skapades i steg före placera den nya uppsättningen ersättning externa certifikat i katalogstrukturen enligt det format som beskrivs i avsnittet obligatoriska certifikat av den [Azure Stack PKI-certifikatkrav](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).
2. Skapa en PowerShell-Session med den [privilegierad slutpunkt](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) med hjälp av den **CloudAdmin** konto och lagra sessioner som en variabel. Du använder den här variabeln som parameter i nästa steg.

    > [!IMPORTANT]  
    > Ange sessionen inte, lagra sessionen som en variabel.
    
3. Kör  **[anropskommandot](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**. Skicka privilegierad slutpunkt PowerShell-sessionsvariabeln som den **Session** parametern. 
4. Kör **Start SecretRotation** med följande parametrar:
    - **PfxFilesPath**  
    Anger du nätverkssökvägen till din katalog för certifikat som skapades tidigare.  
    - **PathAccessCredential**  
    Ett PSCredential-objekt för autentiseringsuppgifter till resursen. 
    - **CertificatePassword**  
    En säker sträng med det lösenord som används för alla pfx-certifikat-filer som skapas.
4. Vänta medan din hemliga rotera. Externa hemliga rotation tar vanligtvis ungefär en timme. När hemlig rotation har slutförts, visar konsolen **övergripande Åtgärdsstatus: Lyckade**. 
    > [!Note]  
    > Om det inte går att hemliga rotation, följ instruktionerna i felmeddelandet och kör **start secretrotation** med den **-kör** parametern. 

    ```PowerShell  
    Start-SecretRotation -Rerun
    ```
    Kontakta supporten om du får upprepas hemliga rotation fel.
5. När installationen har slutförts hemliga rotationsvinkel, ta bort certifikaten från filresursen som skapades i tidigare steg och lagra dem i deras säker plats för säkerhetskopia. 

## <a name="walkthrough-of-secret-rotation"></a>Genomgång av hemliga rotation

Följande PowerShell-exempel visar cmdlets och parametrar för att rotera din hemliga.

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>Rotera endast interna hemligheter

> [!Note]  
> Intern hemlighet Rotation bör endast göras om du misstänker att en intern hemlighet har drabbats av skadliga, eller om du har tagit emot en avisering (på build 1811 eller senare) som anger interna certifikat snart upphör att gälla. Azure Stack-miljöer i förväg 1811 versioner kan se aviseringar för väntande internt certifikat eller Hemlig förfallodatum. Dessa aviseringar är felaktig och bör ignoreras utan att köra interna hemliga rotation. Felaktiga utgångsdatum för interna hemliga aviseringar är ett känt problem som löses i 1811 – intern hemligheter inte upphör att gälla om miljön har varit aktiva under två år.

Så här roterar endast hemligheter som är interna i Azure Stack:

1. Skapa en PowerShell-session med den [privilegierad slutpunkt](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. Privilegierad slutpunkt-session kör **Start SecretRotation-interna**.

    > [!Note]  
    > Azure Stack-miljöer i förväg 1811 versioner kräver inte den **-interna** flaggan. **Start-SecretRotation** roteras endast interna hemligheter.

3. Vänta medan din hemliga rotera.  
När hemlig rotation har slutförts, visar konsolen **övergripande Åtgärdsstatus: Lyckade**. 
    > [!Note]  
    > Om det inte går att hemliga rotation, följ instruktionerna i felmeddelandet och kör **start secretrotation** med den **– internt** och **-köra** parametrar.  

    ```PowerShell  
    Start-SecretRotation -Internal -Rerun
    ```
    Kontakta supporten om du får upprepas hemliga rotation fel.

## <a name="start-secretrotation-reference"></a>Start-SecretRotation referens

Roterar hemligheterna i ett Azure Stack-System. Körs bara mot Azure Stack privilegierad slutpunkt.

### <a name="syntax"></a>Syntax

#### <a name="for-external-secret-rotation"></a>Externa hemliga rotation

```Powershell  
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>För interna hemliga rotation 

```Powershell  
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>Externa hemliga rotation kör 

```Powershell  
Start-SecretRotation [-Rerun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>För interna hemliga rotation kör 

```Powershell  
Start-SecretRotation [-Rerun] [-Internal]
```
### <a name="description"></a>Beskrivning

Den **Start SecretRotation** cmdlet roterar infrastruktur hemligheterna i ett Azure Stack-system. Som standard roteras den endast certifikat för alla externa nätverk infrastrukturslutpunkter. Om det används med - interna flaggan interna roteras infrastruktur hemligheter. När du roterar externt nätverk infrastrukturslutpunkter, **Start SecretRotation** ska köras med en **Invoke-Command** skriptblock med Azure Stack-miljön är privilegierad slutpunkt session skickades som sessionsparameter.
 
### <a name="parameters"></a>Parametrar

| Parameter | Typ | Krävs | Position | Standard | Beskrivning |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | Sträng  | False  | med namnet  | Ingen  | Sökvägen till filresursen i **\Certificates** katalog som innehåller alla externa nätverk endpoint certifikat. Krävs endast när du roterar externa hemligheter eller alla hemligheter. End-katalogen måste vara **\Certificates**. |
| CertificatePassword | SecureString | False  | med namnet  | Ingen  | Lösenordet för alla certifikat som anges i - PfXFilesPath. Obligatoriskt värde om PfxFilesPath tillhandahålls vid både interna och externa hemligheter roteras. |
| Intern | Sträng | False | med namnet | Ingen | Intern flaggan måste användas när som helst Azure Stack-operatör önskar att rotera interna infrastrukturfel hemligheter. |
| PathAccessCredential | PSCredential | False  | med namnet  | Ingen  | PowerShell-autentiseringsuppgift för filresursen i den **\Certificates** katalog som innehåller alla externa nätverk endpoint certifikat. Krävs endast när du roterar externa hemligheter eller alla hemligheter.  |
| Kör om | SwitchParameter | False  | med namnet  | Ingen  | Kör måste användas när som helst hemliga rotation återförsöks efter ett misslyckat försök. |

### <a name="examples"></a>Exempel
 
#### <a name="rotate-only-internal-infrastructure-secrets"></a>Rotera endast interna infrastrukturfel hemligheter

Detta måste köras via Azure Stack [miljö är privilegierad slutpunkt](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).

```powershell  
PS C:\> Start-SecretRotation  -Internal
```

Det här kommandot roterar alla infrastruktur-hemligheter som är exponerade för Azure Stack interna nätverket. 

#### <a name="rotate-only-external-infrastructure-secrets"></a>Rotera endast infrastruktur för externa hemligheter  

```powershell  
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock {  

Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }  

Remove-PSSession -Session $PEPSession
```

Det här kommandot roterar TLS-certifikat som används för infrastrukturslutpunkter för Azure Stack externt nätverk.   

**Rotera infrastruktur för interna och externa hemligheter**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

Det här kommandot roterar alla infrastruktur-hemligheter som är exponerade för Azure Stack internt nätverk samt TLS-certifikat som används för infrastrukturslutpunkter för Azure Stack externt nätverk. Start-SecretRotation roterar alla stack-genererade hemligheter och eftersom det finns certifikat, roteras också extern slutpunkt certifikat.  


## <a name="update-the-baseboard-management-controller-bmc-credential"></a>Uppdatera hanteringsstyrenheten för baskort (BMC) hanteringsautentiseringsuppgifter

Huvudkortshantering (BMC) övervakar det fysiska tillståndet för dina servrar. Information och anvisningar om hur du uppdaterar användarens kontonamn och lösenord för BMC varierar beroende på din maskinvaruleverantör för OEM-tillverkare (original equipment manufacturer). Du bör uppdatera ditt lösenord för Azure Stack-komponenter med jämna mellanrum.

1. Uppdatera bmc-Styrenheten på de fysiska Azure Stack-servrarna med OEM-instruktioner. Användarens kontonamn och lösenord för varje BMC-enhet i din miljö måste vara samma.
2. Öppna en privilegierad slutpunkt i Azure Stack-sessioner. Anvisningar finns i [med hjälp av privilegierad slutpunkt i Azure Stack](azure-stack-privileged-endpoint.md).
3. När din PowerShell-prompten har ändrats till **[IP-adress eller ERCS VM name]: PS >** eller **[azs-ercs01]: PS >**, beroende på miljön, kör `Set-BmcCredemtial` genom att köra `invoke-command`. Skicka din privilegierad slutpunkt sessionsvariabeln som en parameter. Exempel:

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBMCuser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BMCPassword is mandatory, while the BMCUser parameter is optional.
        Set-Bmccredential -bmcpassword $using:NewBMCpwd -bmcuser $using:NewBMCuser
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    Du kan också använda den statiska PowerShell-versionen med lösenord som kodrader:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -Force
    $NewBMCuser = "<New BMC User name>" 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmccredential -bmcpassword $using:NewBMCpwd -bmcuser $using:NewBMCuser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Nästa steg

[Läs mer om Azure Stack-säkerhet](azure-stack-security-foundations.md)
