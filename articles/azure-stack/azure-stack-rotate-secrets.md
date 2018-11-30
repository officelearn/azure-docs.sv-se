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
ms.date: 09/06/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 8d84801aacfc60bb11aac4c9046a433378a59b79
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314481"
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
    - ACS (inklusive blob-, tabell- och queue storage) 
    - ADFS<sup>*</sup>
    - Graph<sup>*</sup>

   <sup>*</sup> Gäller endast om den miljön identitetsprovider är Active Directory Federation Services (AD FS).

> [!NOTE]
> Alla andra skydda nycklar och strängar, inklusive BMC och byta lösenord, lösenord för användare och administratör uppdateras manuellt av administratören. 

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

<sup>*</sup> Här är offentliga certifikatutfärdare som de som ingår i Windows Trusted Root Program. Du hittar en fullständig lista [Microsoft Trusted Root Certificate Program: deltagare (från och med 27 juni 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>Reparation av aviseringar

När hemligheter är inom 30 dagar från upphör att gälla, genereras följande aviseringar i Administratörsportalen: 

- Lösenordets giltighetstid för väntande tjänster-konto 
- Väntar på interna certifikatet upphör att gälla 
- Väntande externt certifikat upphör att gälla 

Kör hemliga rotation med hjälp av anvisningarna nedan kommer att åtgärda dessa aviseringar.

## <a name="pre-steps-for-secret-rotation"></a>Steg före hemliga rotation

   > [!IMPORTANT]  
   > Se till att hemliga rotation har inte körts på din miljö. Om hemlig rotation har redan gjorts kan du uppdatera Azure Stack till version 1807 eller senare innan du kan köra hemliga rotation. 

1.  Operatörer kan få aviseringar öppnar och stänger automatiskt under rotation av Azure Stack-hemligheter.  Det här beteendet är förväntat och aviseringar kan ignoreras.  Operatörer kan kontrollera giltigheten för de här aviseringarna genom att köra testet AzureStack.  Operatörer med SCOM för att övervaka Azure Stack-system, placera ett system i underhållsläge hindrar dessa aviseringar från att nå sina ITSM-system, men fortsätter att varna om Azure Stack-system blir oåtkomlig. 
2. Meddela användarna om eventuella underhållsåtgärder. Schemalägga normala underhållsfönster, så mycket som möjligt, under tider utanför kontorstid. Underhåll kan påverka både användaren arbetsbelastningar och åtgärder.
    > [!note]  
    > Nästa steg gäller endast när du roterar externa hemligheter i Azure Stack.

3. Kör **[Test-AzureStack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)** och bekräfta att alla test utdata är felfria före rotera hemligheter.
4. Förbered en ny uppsättning ersättning externa certifikat. Den nya uppsättningen matchar certifikat-specifikationer som beskrivs i den [Azure Stack PKI-certifikatkrav](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs).
5.  Store en tillbaka upp till de certifikat som används för rotation på ett säkert ställe. Om din rotation körs och misslyckas, ersätter du certifikat i filresursen med säkerhetskopiorna innan du kör rotationen. Observera, göra säkerhetskopior i säker säkerhetskopieringsplatsen.
6.  Skapa en filresurs som du kan komma åt från de ERCS virtuella datorerna. Filresursen måste vara Läs- och skrivbara för den **CloudAdmin** identitet.
7.  Öppna ett PowerShell ISE-konsolen från en dator där du har åtkomst till filresursen. Gå till din filresurs. 
8.  Kör **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** att skapa de nödvändiga katalogerna för ditt externa certifikat.

## <a name="rotating-external-and-internal-secrets"></a>Rotera externa och interna hemligheter

Så här roterar både externa en intern hemlighet:

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
4. Vänta medan din hemliga rotera.  
När hemlig rotation har slutförts, visar konsolen **övergripande Åtgärdsstatus: lyckad**. 
    > [!note]  
    > Om det inte går att hemliga rotation, följ instruktionerna i felmeddelandet och kör start-secretrotation med den **-kör** parametern. Kontakta supporten om du får upprepas hemliga rotation fel. 
5. När installationen har slutförts hemliga rotationsvinkel, ta bort certifikaten från filresursen som skapades i tidigare steg och lagra dem i deras säker plats för säkerhetskopia. 

## <a name="walkthrough-of-secret-rotation"></a>Genomgång av hemliga rotation

Följande PowerShell-exempel visar cmdlets och parametrar för att rotera din hemliga.

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -AsPlainText -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```
## <a name="rotating-only-internal-secrets"></a>Rotera endast interna hemligheter

Så här roterar endast hemligheter som är interna i Azure Stack:

1. Skapa en PowerShell-session med den [privilegierad slutpunkt](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. Privilegierad slutpunkt-session kör **Start SecretRotation** utan argument.
3. Vänta medan din hemliga rotera.  
När hemlig rotation har slutförts, visar konsolen **övergripande Åtgärdsstatus: lyckad**. 
    > [!note]  
    > Om det inte går att hemliga rotation, följ instruktionerna i felmeddelandet och kör start-secretrotation med den **-köra** parametern. Kontakta supporten om du får upprepas hemliga rotation fel. 

## <a name="start-secretrotation-reference"></a>Start-SecretRotation referens

Roterar hemligheterna i ett Azure Stack-System. Körs bara mot Azure Stack privilegierad slutpunkt.

### <a name="syntax"></a>Syntax

Sökvägen (standard)

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>Beskrivning

Cmdleten Start-SecretRotation roterar infrastruktur hemligheterna i ett Azure Stack-system. Som standard den roterar alla hemligheter som är exponerade för interna infrastrukturnätverket med indata-roterar den också certifikat för alla externa nätverk infrastrukturslutpunkter. När du roterar externt nätverk infrastrukturslutpunkter ska Start SecretRotation köras via ett Invoke-Command-skriptblock med Azure Stack-miljön privilegierad slutpunkt session skickas som sessionsparameter.
 
### <a name="parameters"></a>Parametrar

| Parameter | Typ | Krävs | Position | Standard | Beskrivning |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | Sträng  | False  | med namnet  | Ingen  | Sökvägen till filresursen i **\Certificates** katalog som innehåller alla externa nätverk endpoint certifikat. Krävs endast när du roterar externa hemligheter eller alla hemligheter. End-katalogen måste vara **\Certificates**. |
| CertificatePassword | SecureString | False  | med namnet  | Ingen  | Lösenordet för alla certifikat som anges i - PfXFilesPath. Obligatoriskt värde om PfxFilesPath tillhandahålls vid både interna och externa hemligheter roteras. |
| PathAccessCredential | PSCredential | False  | med namnet  | Ingen  | PowerShell-autentiseringsuppgift för filresursen i den **\Certificates** katalog som innehåller alla externa nätverk endpoint certifikat. Krävs endast när du roterar externa hemligheter eller alla hemligheter.  |
| Kör om | SwitchParameter | False  | med namnet  | Ingen  | Kör måste användas när som helst hemliga rotation är nytt försök efter ett misslyckat försök. |

### <a name="examples"></a>Exempel
 
**Rotera endast interna infrastrukturfel hemligheter**

```powershell  
PS C:\> Start-SecretRotation  
```

Det här kommandot roterar alla infrastruktur-hemligheter som är exponerade för Azure Stack interna nätverket. Start-SecretRotation roterar alla stack-genererade hemligheter, men eftersom det finns inga angivna certifikat kan roteras inte extern slutpunkt certifikat.  

**Rotera infrastruktur för interna och externa hemligheter**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

Det här kommandot roterar alla infrastruktur-hemligheter som är exponerade för Azure Stack internt nätverk samt TLS-certifikat som används för infrastrukturslutpunkter för Azure Stack externt nätverk. Start-SecretRotation roterar alla stack-genererade hemligheter och eftersom det finns certifikat, roteras också extern slutpunkt certifikat.  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>Uppdatera baseboard management controller (BMC) lösenord

Huvudkortshantering (BMC) övervakar det fysiska tillståndet för dina servrar. Information och anvisningar om hur du uppdaterar lösenordet för BMC varierar beroende på din maskinvaruleverantör för OEM-tillverkare (original equipment manufacturer). Du bör uppdatera ditt lösenord för Azure Stack-komponenter på en vanlig takt.

1. Uppdatera bmc-Styrenheten på Azure Stack fysiska servrar med OEM-instruktioner. Lösenord för varje BMC-enhet i din miljö måste vara samma.
2. Öppna en privilegierad slutpunkt i Azure Stack-sessioner. Anvisningar finns i [med hjälp av privilegierad slutpunkt i Azure Stack](azure-stack-privileged-endpoint.md).
3. När din PowerShell-prompten har ändrats till **[IP-adress eller ERCS VM name]: PS >** eller **[azs-ercs01]: PS >**, beroende på miljön, kör `Set-BmcPassword` genom att köra `invoke-command`. Skicka din privilegierad slutpunkt sessionsvariabeln som en parameter. Exempel:

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    Du kan också använda den statiska PowerShell-versionen med lösenord som kodrader:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Nästa steg

[Läs mer om Azure Stack-säkerhet](azure-stack-security-foundations.md)
