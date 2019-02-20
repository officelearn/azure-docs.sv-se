---
title: Validera en säkerhetskopiering i Azure Stack med hjälp av ASDK | Microsoft Docs
description: Hur du validerar en säkerhetskopiering för Azure Stack-integrerade system som med hjälp av ASDK.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 02/15/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/15/2019
ms.openlocfilehash: 31c5d068c8fcd0b6edea7cff63098131d848a14e
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416386"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>Använd ASDK för att validera en säkerhetskopiering i Azure Stack
När du distribuerar Azure Stack och etablera användarresurser, till exempel erbjudanden, planer, kvoter och prenumerationer, bör du [Aktivera säkerhetskopiering av Azure Stack-infrastruktur](../azure-stack-backup-enable-backup-console.md). Schemaläggning och körning av infrastruktur för regelbundna säkerhetskopieringar säkerställer att infrastrukturen hanteringsdata inte går förlorad om det finns ett oåterkalleligt maskinvaru- eller tjänstfel.

> [!TIP]
> Vi rekommenderar att du [köra en säkerhetskopiering på begäran](../azure-stack-backup-back-up-azure-stack.md) innan du påbörjar den här proceduren för att säkerställa att du har en kopia av den senaste tillgängliga informationen i infrastrukturen. Se till att samla in säkerhetskopie-ID efter att säkerhetskopieringen har slutförts. Detta ID måste utföras under molnåterställning. 

Säkerhetskopieringar i Azure Stack-infrastruktur innehåller viktig information om ditt moln som kan återställas under omdistribution av Azure Stack. Du kan använda ASDK för att verifiera dessa säkerhetskopior utan att påverka ditt moln för produktion. 

Verifiera säkerhetskopieringar på ASDK stöds för följande scenarier:

|Scenario|Syfte|
|-----|-----|
|Verifiera infrastruktur säkerhetskopior från en integrerad lösning.|Kort livslängd verifieringen att data i säkerhetskopian är giltig.|
|Lär dig återställningsarbetsflöde för slutpunkt till slutpunkt.|Använd ASDK för att validera hela säkerhetskopian och återställer upplevelse.|
|     |     |

Följande scenario **är inte** stöds vid verifiering av säkerhetskopior på ASDK:

|Scenario|Syfte|
|-----|-----|
|ASDK skapa för att skapa säkerhetskopiering och återställning.|Återställa säkerhetskopierade data från en tidigare version av ASDK till en nyare version.|
|     |     |


## <a name="cloud-recovery-deployment"></a>Distribution av återställning
Infrastruktur säkerhetskopior från distributionen av integrerade system kan verifieras genom att utföra en molndistribution för återställning av ASDK. I den här typen av distribution har specifik tjänstdata återställts från säkerhetskopia när ASDK har installerats på värddatorn.

### <a name="prereqs"></a>Krav för återställning av molnet
Innan du startar en molndistribution för återställning av ASDK ska du kontrollera att du har följande information:

**Krav för UI-installationsprogrammet**

*Aktuella UI-installationsprogrammet stöder endast krypteringsnyckel*

|Krav|Beskrivning|
|-----|-----|
|Säkerhetskopiering resurssökväg|UNC-sökvägen till filresursen för den senaste Azure Stack-säkerhetskopia som ska användas för att återskapa information för Azure Stack-infrastruktur. Den här lokala resursen skapas under distributionsprocessen för molnet återställning.|
|Säkerhetskopie-ID att återställa|Säkerhetskopie-ID i formuläret alfanumeriska ”xxxxxxxx-xxxx-xxxx-xxxx-XXXXXXXXXXXX” som identifierar säkerhetskopian ska återställas under molnåterställning.|
|IP-adress för server för tid|En giltig tid servern IP-adress, till exempel 132.163.97.2, krävs för Azure Stack-distributioner.|
|Lösenord för externa certifikat|Lösenordet för det externa certifikatet som används av Azure Stack. CA-säkerhetskopiering innehåller externa certifikat som måste återställas med det här lösenordet.|
|Säkerhetskopiera krypteringsnyckel|Krävs om du har uppgraderat till Azure Stack-version 1901 eller senare och säkerhetskopiering inställningar konfigureras fortfarande in med en krypteringsnyckel. Krypteringsnyckeln är inaktuell från och med 1901. Installationsprogrammet stöder krypteringsnyckeln i bakåtkompatibilitet kompatibilitetsläge för minst 3 versioner. När du har uppdaterat inställningar för att använda ett certifikat finns i nästa tabell för nödvändig information.|

|     |     | 

**Installationsprogrammet för PowerShell-krav**

*Den aktuella PowerShell installationsprogrammet stöder nyckel eller dekryptering krypteringscertifikat*

|Krav|Beskrivning|
|-----|-----|
|Säkerhetskopiering resurssökväg|UNC-sökvägen till filresursen för den senaste Azure Stack-säkerhetskopia som ska användas för att återskapa information för Azure Stack-infrastruktur. Den här lokala resursen skapas under distributionsprocessen för molnet återställning.|
|Säkerhetskopie-ID att återställa|Säkerhetskopie-ID i formuläret alfanumeriska ”xxxxxxxx-xxxx-xxxx-xxxx-XXXXXXXXXXXX” som identifierar säkerhetskopian ska återställas under molnåterställning.|
|IP-adress för server för tid|En giltig tid servern IP-adress, till exempel 132.163.97.2, krävs för Azure Stack-distributioner.|
|Lösenord för externa certifikat|Lösenordet för det externa certifikatet som används av Azure Stack. CA-säkerhetskopiering innehåller externa certifikat som måste återställas med det här lösenordet.|
|Krypteringslösenord för certifiering|Valfri. Krävs endast om säkerhetskopieringen har krypterats med ett certifikat. Lösenordet är för självsignerade certifikatets (.pfx) som innehåller den privata nyckeln som krävs för att dekryptera säkerhetskopierade data.|
|Säkerhetskopiera krypteringsnyckel|Valfri. Krävs om du har uppgraderat till Azure Stack-version 1901 eller senare och säkerhetskopiering inställningar konfigureras fortfarande in med en krypteringsnyckel. Installationsprogrammet stöder krypteringsnyckeln i bakåtkompatibilitet kompatibilitetsläge för minst 3 versioner. När du har uppdaterat inställningar för att använda ett certifikat måste du ange lösenordet för dekrypteringscertifikat.|
|     |     | 

## <a name="prepare-the-host-computer"></a>Förbereda värddatorn 
Som en normal distribution av ASDK måste ASDK system värdmiljön förberedas för installation. När värddatorn development kit har förberetts, att den startas från CloudBuilder.vhdx virtuella hårddisken till börjar ASDK distributionen.

Ladda ned en ny cloudbuilder.vhdx som motsvarar samma version av Azure Stack som har säkerhetskopierats på värddatorn ASDK och följ anvisningarna för [förbereda värddatorn ASDK](asdk-prepare-host.md).

När värdservern har startats om från cloudbuilder.vhdx måste du skapa en filresurs och kopiera dina säkerhetskopierade data till. Filresursen ska vara tillgänglig för det konto som kör installationen. Administratör i dessa exempel PowerShell-kommandon: 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

Kopiera ditt senaste säkerhetskopieringsfilerna för Azure Stack till nyligen skapade resursen. Mappstruktur på resursen ska vara: `\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\`.

Kopiera slutligen dekrypteringscertifikat (.pfx) till katalogen certifikat: `C:\CloudDeployment\Setup\Certificates\` och Byt namn på filen och `BackupDecryptionCert.pfx`.

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>Distribuera ASDK i återställningsläge för molnet

> [!IMPORTANT]
> 1. Aktuella installationsprogrammet Användargränssnittet stöder endast krypteringsnyckeln. Du kan bara verifiera säkerhetskopior från system som fortsätta att använda krypteringsnyckeln. Om säkerhetskopieringen har krypterats på ett integrerat system eller ASDK som använder certifikat, använder du installationsprogrammet för PowerShell (**InstallAzureStackPOC.ps1**). 
> 2. PowerShell-installationsprogrammet (**InstallAzureStackPOC.ps1**) stöder krypteringsnyckeln eller certifikat.
> 3. ASDK installation har stöd för exakt ett nätverkskort (NIC) för nätverk. Om du har flera nätverkskort kan du kontrollera att endast ett är aktiverat (och alla andra har inaktiverats) innan du kör skriptet för distribution.

### <a name="use-the-installer-ui-to-deploy-the-asdk-in-recovery-mode"></a>Använd installationsprogrammet för Användargränssnittet för att distribuera ASDK i återställningsläge
Stegen i det här avsnittet visar hur du distribuerar ASDK med ett grafiskt användargränssnitt (GUI) som tillhandahålls av ladda ned och köra den **asdk installer.ps1** PowerShell-skript.

> [!NOTE]
> Installationsprogrammet användargränssnittet för Azure Stack Development Kit är en öppen källkod-skript som baseras på WCF- och PowerShell.

> [!IMPORTANT]
> Aktuella installationsprogrammet Användargränssnittet stöder endast krypteringsnyckeln.

1. När värddatorn startar i CloudBuilder.vhdx-avbildning, logga in med administratörsautentiseringsuppgifter för anges när du [förberett värddatorn development kit](asdk-prepare-host.md) för ASDK installation. Detta bör vara samma som development kit-autentiseringsuppgifter för lokal administratör på värden.
2. Öppna en upphöjd PowerShell-konsol och kör den  **&lt;enhetsbeteckning > \AzureStack_Installer\asdk-installer.ps1** PowerShell-skript. Skriptet kan nu vara på en annan enhet än C:\ i CloudBuilder.vhdx bild. Klicka på **återställa**.

    ![ASDK installer skript](media/asdk-validate-backup/1.PNG) 

3. Ange din Azure AD directory-information (valfritt) och det lokala administratörslösenordet för ASDK värddatorn på sidan identity-providern och autentiseringsuppgifter. Klicka på **Nästa**.

    ![Sidan för identiteter och autentiseringsuppgifter](media/asdk-validate-backup/2.PNG) 

4. Välj nätverkskortet som ska användas av ASDK värddatorn och klicka på **nästa**. Alla andra nätverksgränssnitt kommer att inaktiveras under installationen av ASDK. 

    ![Nätverkskortet](media/asdk-validate-backup/3.PNG) 

5. Ange giltig tidsservern och DNS-vidarebefordrare IP-adresser på sidan nätverkskonfiguration. Klicka på **Nästa**.

    ![Sidan för konfiguration av nätverk](media/asdk-validate-backup/4.PNG) 

6. När egenskaper för kortet nätverksgränssnitt har verifierats, klickar du på **nästa**. 

    ![Verifiering av nätverket kort inställningar](media/asdk-validate-backup/5.PNG) 

7. Ange nödvändig information som beskrivs tidigare i [krav](#prereqs) på inställningssidan för säkerhetskopiering och användarnamnet och lösenordet som används för att få åtkomst till resursen. Klicka på **nästa**: 

   ![Sidan för inställningar för säkerhetskopiering](media/asdk-validate-backup/6.PNG) 

8. Granska distributionsskriptet som ska användas för att distribuera ASDK på sammanfattningssidan. Klicka på **distribuera** att starta distributionen. 

    ![Sammanfattningssida](media/asdk-validate-backup/7.PNG) 


### <a name="use-powershell-to-deploy-the-asdk-in-recovery-mode"></a>Använd PowerShell för att distribuera ASDK i återställningsläge

Ändra följande PowerShell-kommandon för din miljö och kör dem om du vill distribuera ASDK i återställningsläge för molnet:

**Använd InstallAzureStackPOC.ps1 skript för att starta molnåterställning med krypteringsnyckeln.**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$key = Read-Host -AsSecureString -Prompt "Your backup encryption key"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupEncryptionKeyBase64 $key `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

**Använd InstallAzureStackPOC.ps1 skript för att initiera molnåterställning med dekrypteringscertifikat.**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$decryptioncertpassword  = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupDecryptionCertPassword $decryptioncertpassword `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="complete-cloud-recovery"></a>Fullständig molnåterställning 
Efter en lyckad återställning molndistribution, du behöver för att slutföra en återställning med hjälp av den **återställning AzureStack** cmdlet. 

När du loggar in som Azure Stack-operatör [installera Azure Stack PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) och kör följande kommandon för att ange certifikatet och lösenordet som ska användas när du återställer från en säkerhetskopia:

**Återställningsläge med certifikatfilen**

> [!NOTE] 
> Dekrypteringscertifikat av säkerhetsskäl sparas inte i Azure Stack-distribution. Du måste ange dekrypteringscertifikat och tillhörande lösenord igen.

```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>" `
 -DecryptionCertPath "<path to decryption certificate with file name (.pfx)>" `
 -DecryptionCertPassword $decryptioncertpassword
```

**Återställningsläge med krypteringsnyckeln**
```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>"
```

Vänta 60 minuter efter att anropa denna cmdlet om du vill starta verifiering av säkerhetskopierade data på molnet återställts ASDK.

## <a name="next-steps"></a>Nästa steg
[Registrera Azure Stack](asdk-register.md)

