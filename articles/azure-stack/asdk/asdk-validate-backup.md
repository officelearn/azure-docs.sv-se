---
title: Validera en säkerhetskopiering i Azure Stack med hjälp av ASDK | Microsoft Docs
description: Hur du validerar en säkerhetskopiering för Azure Stack-integrerade system som med hjälp av ASDK.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: 027d4a9f93032bfdd0f4cda96df74c92b5679540
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251579"
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



### <a name="cloud-recovery-prerequisites"></a>Krav för återställning av molnet
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

