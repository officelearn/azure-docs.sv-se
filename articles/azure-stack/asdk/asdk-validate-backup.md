---
title: Validera en säkerhetskopiering i Azure Stack med hjälp av ASDK | Microsoft Docs
description: Hur du validerar en säkerhetskopiering för Azure Stack-integrerade system som med hjälp av ASDK.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 02/06/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: 02ecb3cdec9ddb07bf48dfe77d1ed5fbf07975e0
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55965332"
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

|Krav|Beskrivning|
|-----|-----|
|Säkerhetskopiering resurssökväg.|UNC-sökvägen till filresursen för den senaste Azure Stack-säkerhetskopia som ska användas för att återskapa information för Azure Stack-infrastruktur. Den här lokala resursen skapas under distributionsprocessen för molnet återställning.|
|Säkerhetskopiera krypteringsnyckel.|Krypteringsnyckeln som användes för att schemalägga infrastruktur för säkerhetskopiering ska köras med Azure Stack-administrationsportalen.|
|Säkerhetskopie-ID att återställa.|Säkerhetskopie-ID i formuläret alfanumeriska ”xxxxxxxx-xxxx-xxxx-xxxx-XXXXXXXXXXXX” som identifierar säkerhetskopian ska återställas under molnåterställning.|
|Tid IP-adress.|En giltig tid servern IP-adress, till exempel 132.163.97.2, krävs för Azure Stack-distributioner.|
|Externt certifikatlösenord.|Lösenordet för det externa certifikatet som används av Azure Stack. CA-säkerhetskopiering innehåller externa certifikat som måste återställas med det här lösenordet.|
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

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>Distribuera ASDK i återställningsläge för molnet
Den **InstallAzureStackPOC.ps1** skript används för att initiera molnåterställning. 

> [!IMPORTANT]
> ASDK installation har stöd för exakt ett nätverkskort (NIC) för nätverk. Om du har flera nätverkskort kan du kontrollera att endast ett är aktiverat (och alla andra har inaktiverats) innan du kör skriptet för distribution.

### <a name="use-the-installer-to-deploy-the-asdk-in-recovery-mode"></a>Använd installationsprogrammet för att distribuera ASDK i återställningsläge
Stegen i det här avsnittet visar hur du distribuerar ASDK med ett grafiskt användargränssnitt (GUI) som tillhandahålls av ladda ned och köra den **asdk installer.ps1** PowerShell-skript.

> [!NOTE]
> Installationsprogrammet användargränssnittet för Azure Stack Development Kit är en öppen källkod-skript som baseras på WCF- och PowerShell.

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

```powershell
cd C:\CloudDeployment\Setup     
$adminPass = Get-Credential Administrator
$key = ConvertTo-SecureString "<Your backup encryption key>" -AsPlainText -Force ` 
$certPass = Read-Host -AsSecureString  

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
-BackupEncryptionKeyBase64 $key -BackupStoreCredential $adminPass -BackupId "<Backup ID to restore>" `
-TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="restore-infrastructure-data-from-backup"></a>Återställa infrastrukturdata från en säkerhetskopia
Efter en lyckad återställning molndistribution, du behöver för att slutföra en återställning med hjälp av den **återställning AzureStack** cmdlet. 

När du loggar in som Azure Stack-operatör [installera Azure Stack PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) och sedan ersätta din Backup-ID för den `Name` parameter, kör du följande kommando:

```powershell
Restore-AzsBackup -Name "<BackupID>"
```
Vänta 60 minuter efter att anropa denna cmdlet om du vill starta verifiering av säkerhetskopierade data på molnet återställts ASDK.

## <a name="next-steps"></a>Nästa steg
[Registrera Azure Stack](asdk-register.md)

