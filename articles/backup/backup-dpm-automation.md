---
title: Azure Backup - Använd PowerShell för att säkerhetskopiera DPM-arbetsbelastningar
description: Lär dig hur du distribuerar och hanterar Azure Backup för Data Protection Manager (DPM) med hjälp av PowerShell
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 1/23/2017
ms.author: adigan
ms.openlocfilehash: 5ef9d61e880d3252eae2d8ef924ff39a5d2f6acf
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497918"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Distribuera och hantera säkerhetskopiering till Azure för DPM-servrar (Data Protection Manager) med PowerShell
Den här artikeln visar hur du använder PowerShell för att konfigurera Azure Backup på en DPM-server och för att hantera säkerhetskopiering och återställning.

## <a name="setting-up-the-powershell-environment"></a>Konfigurera PowerShell-miljö
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Innan du kan använda PowerShell för att hantera säkerhetskopior från Data Protection Manager till Azure, måste du ha rätt miljö i PowerShell. Se till att du kör följande kommando för att importera modulerna som är rätt och gör att du kan referera till rätt DPM-cmdletar i början av PowerShell-sessionen:

```
PS C:> & "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"

Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Installation och registrering
Börja:

1. [Hämta senaste PowerShell](https://github.com/Azure/azure-powershell/releases) (lägsta versionen är: 1.0.0)
2. Aktivera Azure Backup-commandlets genom att växla till *AzureResourceManager* läge med hjälp av den **Switch-AzureMode** kommandot:

```
PS C:\> Switch-AzureMode AzureResourceManager
```

Följande uppgifter för installation och registrering kan automatiseras med PowerShell:

* skapar ett Recovery Services-valv
* Installera Azure Backup-agenten
* Registrera med Azure Backup-tjänsten
* Nätverksinställningar
* Krypteringsinställningar

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv
Följande steg vägleder dig genom att skapa ett Recovery Services-valv. Recovery Services-valvet skiljer sig från ett säkerhetskopieringsvalv.

1. Om du använder Azure Backup för första gången, måste du använda den **Register-AzureRMResourceProvider** cmdlet för att registrera Azure Recovery Services-providern med din prenumeration.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. Recovery Services-valvet är en ARM-resurs, så du måste placera den i en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en ny. När du skapar en ny resursgrupp måste du ange namn och plats för resursgruppen.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. Använd den **New-AzureRmRecoveryServicesVault** cmdlet för att skapa ett nytt valv. Glöm inte att ange samma plats för valvet som användes för resursgruppen.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. Ange vilken typ av lagringsredundans ska användas. Du kan använda [lokalt Redundant lagring (LRS)](../storage/common/storage-redundancy-lrs.md) eller [geografiskt Redundant lagring (GRS)](../storage/common/storage-redundancy-grs.md). I följande exempel visas alternativet - BackupStorageRedundancy för testVault anges till GeoRedundant.

   > [!TIP]
   > Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata. Därför är det praktiskt att lagra säkerhetskopians Recovery Services-valvobjekt i en variabel.
   >
   >

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testVault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Visa valv i en prenumeration
Använd **Get-AzureRmRecoveryServicesVault** att visa en lista över alla valv i den aktuella prenumerationen. Du kan använda det här kommandot för att kontrollera att ett nytt valv har skapats eller för att se vilka valv är tillgängliga i prenumerationen.

Kör kommandot Get-AzureRmRecoveryServicesVault, och alla valv i prenumerationen visas.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Installera Azure Backup-agenten på en DPM-Server
Innan du installerar Azure Backup-agenten som du behöver ha installationsprogrammet hämtade och finns på Windows Server. Du kan hämta den senaste versionen av installationsprogrammet från den [Microsoft Download Center](https://aka.ms/azurebackup_agent) eller från Recovery Services-valvet instrumentpanelssidan. Spara installationsprogrammet i en lättillgänglig plats som * C:\Downloads\*.

Kör följande kommando i en upphöjd PowerShell-konsol för att installera agenten **på DPM-servern**:

```
PS C:\> MARSAgentInstaller.exe /q
```

Detta installerar agent med alla standardalternativ. Installationen tar några minuter i bakgrunden. Om du inte anger den */nu* alternativet den **Windows Update** öppnas i slutet av installationen att söka efter uppdateringar.

Agenten visas i listan över installerade program. Om du vill se listan över installerade program, gå till **Kontrollpanelen** > **program** > **program och funktioner**.

![Agenten har installerats](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Installationsalternativ
Om du vill se alla alternativ som är tillgängliga via kommandoraden använder du följande kommando:

```
PS C:\> MARSAgentInstaller.exe /?
```

De tillgängliga alternativen är:

| Alternativ | Information | Standard |
| --- | --- | --- |
| /q |Tyst installation |- |
| / p: ”plats” |Sökvägen till installationsmappen för Azure Backup-agenten. |C:\Program Files\Microsoft Azure Recovery Services-agenten |
| / s: ”plats” |Sökväg till cachemappen för Azure Backup-agenten. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Delta i Microsoft Update |- |
| /nu |Inte söka efter uppdateringar när installationen är klar |- |
| /d |Avinstallerar Microsoft Azure Recovery Services-agenten |- |
| /pH |Proxyadress för värd |- |
| /po |Portnummer för proxyservern värd |- |
| /Pu |Proxyanvändarnamnet för värd |- |
| /PW |Lösenord för proxy |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Registrera DPM till ett Recovery Services-valv
När du har skapat Recovery Services-valvet kan ladda ned den senaste agenten och autentiseringsuppgifterna för valvet och lagra den på en lämplig plats som C:\Downloads.

```
PS C:\> $credspath = "C:\downloads"
PS C:\> $credsfilename = Get-AzureRmRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
PS C:\> $credsfilename
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

På DPM-servern och kör den [Start OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) cmdlet för att registrera datorn med valvet.

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration-VaultCredentials $cred -Confirm:$false
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Inledande konfigurationsinställningar
När DPM-servern har registrerats med Recovery Services-valvet är börjar det med standardinställningarna för prenumerationen. Inställningarna prenumeration omfattar nätverk, kryptering och mellanlagringsområdet. Ändra inställningar för måste du först få grepp om de befintliga inställningarna för (standard) med hjälp av den [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793) cmdlet:

```
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Alla ändringar som görs till den här lokala PowerShell-objekt ```$setting``` och sedan objektet fullständig strävar efter att DPM och Azure Backup för att spara dem med hjälp av den [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) cmdlet. Du måste använda den ```–Commit``` flagga för att se till att ändringarna har sparats. Inställningarna kommer inte tillämpas och används av Azure Backup, såvida inte genomförts.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Nätverk
Om det är anslutningen av den DPM-datorn till Azure Backup-tjänsten på internet via en proxyserver, bör inställningarna för proxyservern anges för säkerhetskopiering. Detta görs med hjälp av den ```-ProxyServer```och ```-ProxyPort```, ```-ProxyUsername``` och ```ProxyPassword``` parametrar med den [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) cmdlet. I det här exemplet finns ingen proxyserver så att vi uttryckligen Rensa alla proxy-relaterad information.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Användning av nätverksbandbredd kan även kontrolleras med alternativ för ```-WorkHourBandwidth``` och ```-NonWorkHourBandwidth``` för en given uppsättning dagar i veckan. I det här exemplet anger vi inte någon begränsning.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Konfigurera mellanlagringsområdet
Azure Backup-agenten som körs på DPM-servern behöver tillfällig lagring för data som återställs från molnet (lokalt mellanlagringsområde). Konfigurera mellanlagringsområdet med hjälp av den [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) cmdlet och ```-StagingAreaPath``` parametern.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

I exemplet ovan mellanlagringsområdet anges till *C:\StagingArea* i PowerShell-objektet ```$setting```. Se till att den angivna mappen finns redan, annars slutligt genomförande av prenumerationsinställningar misslyckas.

### <a name="encryption-settings"></a>Krypteringsinställningar
Säkerhetskopierade data skickas till Azure Backup krypteras för att skydda sekretessen för data. Krypteringslösenfrasen är ”password” att dekryptera data vid tidpunkten för återställningen. Det är viktigt att skydda den här informationen och säker när den har angetts.

I exemplet nedan är det första kommandot konverterar strängen ```passphrase123456789``` till en säker sträng och tilldelar säker sträng till variabeln med namnet ```$Passphrase```. Det andra kommandot anger säker sträng i ```$Passphrase``` som lösenord för att kryptera säkerhetskopiorna.

```
PS C:\> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Spara lösenfrasen informationen säker när den har angetts. Du kommer inte att kunna återställa data från Azure utan lösenfrasen.
>
>

Nu bör du har gjort alla nödvändiga ändringar i den ```$setting``` objekt. Kom ihåg att genomföra ändringarna.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Skydda data till Azure Backup
I det här avsnittet ska du lägga till en produktionsservern till DPM och skydda sedan data till lokala DPM-lagring och sedan till Azure Backup. I exemplen är visar vi hur du säkerhetskopierar filer och mappar. Logiken kan enkelt utökas för att säkerhetskopiera alla datakällor som stöds av DPM. Alla DPM-säkerhetskopieringar regleras av en Protection grupp (PG) med fyra delar:

1. **Gruppmedlemmar** är en lista över alla objekt som ska skyddas (kallas även *datakällor* i DPM) som du vill skydda i samma skyddsgrupp. Exempelvis kan du skydda virtuella produktionsdatorer i en skyddsgrupp och SQL Server-databaser i en annan skyddsgrupp som de kan ha olika behov för säkerhetskopiering. Innan du kan säkerhetskopiera alla datakällor på en produktionsserver som du måste se till att DPM-agenten är installerad på servern och hanteras av DPM. Följ anvisningarna för [installerar DPM-agenten](https://technet.microsoft.com/library/bb870935.aspx) och länkas till rätt DPM-servern.
2. **Dataskyddsmetod** anger de säkerhetskopierade målplatserna - band, disk och molnet. I vårt exempel skyddar vi data till den lokala disken och till molnet.
3. En **Säkerhetskopieringsschemat** som anger när säkerhetskopieringar ska vidtas och hur ofta data ska synkroniseras mellan DPM-servern och produktionsservern.
4. En **bevarandeschema** som anger hur lång tid att behålla återställningspunkter i Azure.

### <a name="creating-a-protection-group"></a>Skapa en skyddsgrupp
Börja med att skapa en ny Skyddsgrupp med de [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722) cmdlet.

```
PS C:\> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Cmdleten ovan skapar en Skyddsgrupp med namnet *ProtectGroup01*. En befintlig skyddsgrupp kan också ändras senare för att lägga till säkerhetskopiering till Azure-molnet. Dock göra några ändringar i skyddsgruppen – ny eller befintlig – vi behöver få grepp om en *ändringsbar* objekt med hjälp av den [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713) cmdlet.

```
PS C:\> $MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Att lägga till medlemmar i skyddsgruppen
Varje DPM-agenten vet listan över datakällor på den server som den är installerad på. DPM-agenten måste först skicka en lista över datakällor till DPM-servern för att lägga till en datakälla i skyddsgruppen. En eller flera datakällor har valt sedan och har lagts till i skyddsgruppen. PowerShell-stegen som behövs för att uppnå detta är:

1. Hämta en lista över alla servrar som hanteras av DPM via DPM-agenten.
2. Välj en specifik server.
3. Hämta en lista över alla datakällor på servern.
4. Välj en eller flera datakällor och lägga till dem i Skyddsgruppen

I listan över servrar där DPM-agenten är installerad och hanteras av DPM-servern förvärvas med den [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600) cmdlet. I det här exemplet kommer vi att filtrera och bara konfigurera PS med namnet *productionserver01* för säkerhetskopiering.

```
PS C:\> $server = Get-ProductionServer -DPMServerName "TestingServer" | where {($_.servername) –contains “productionserver01”}
```

Nu hämta listan över datakällor i ```$server``` med hjälp av den [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605) cmdlet. I det här exemplet filtrerar vi för volymen * D:\* som vi vill konfigurera för säkerhetskopiering. Den här datakällan läggs sedan till Skyddsgruppen med hjälp av den [Lägg till DPMChildDatasource](https://technet.microsoft.com/library/hh881732) cmdlet. Kom ihåg att använda den *ändringsbar* protection gruppobjektet ```$MPG``` att göra tilläggen.

```
PS C:\> $DS = Get-Datasource -ProductionServer $server -Inquire | where { $_.Name -contains “D:\” }

PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Upprepa detta steg så många gånger som krävs, tills du har lagt till alla valda datakällor i skyddsgruppen. Du kan även börja med en datakälla och slutföra arbetsflödet för att skapa Skyddsgruppen och senare lägga till flera datakällor i skyddsgruppen.

### <a name="selecting-the-data-protection-method"></a>Att välja en dataskyddsmetod
När datakällor har lagts till i skyddsgruppen, nästa steg är att ange metoden skydd med den [Set-DPMProtectionType](https://technet.microsoft.com/library/hh881725) cmdlet. I det här exemplet är konfigurerad för lokal disk och säkerhetskopiering i molnet i Skyddsgruppen. Du måste också ange datakällan som du vill skydda till molnet med hjälp av den [Lägg till DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) cmdlet: en med - Online flaggan.

```
PS C:\> Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Ange kvarhållningsintervallet
Loggperiod för säkerhetskopieringen återställningspunkter med den [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) cmdlet. Även om det kan verka udda loggperiod innan schemat för säkerhetskopiering har definierats med hjälp av den ```Set-DPMPolicyObjective``` cmdlet: en anger automatiskt ett standardschema för säkerhetskopiering som sedan kan ändras. Det är alltid möjligt att ange säkerhetskopieringen schemalägga först och bevarandeprincipen efter.

I exemplet nedan anger cmdleten kvarhållning parametrarna för säkerhetskopiering till disk. Detta kommer Kvarhåll säkerhetskopior i 10 dagar och synkronisera data var sjätte timme mellan produktionsservern och DPM-servern. Den ```SynchronizationFrequencyMinutes``` inte definierar hur ofta en säkerhetskopieringspunkt skapas, men hur ofta data kopieras till DPM-servern.  Den här inställningen förhindrar att säkerhetskopiorna blir för stort.

```
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

För säkerhetskopieringar kommer att Azure (DPM refererar till dem som onlinesäkerhetskopieringar) kvarhållningsperioder kan konfigureras för [långvariga kvarhållning av säkerhetskopior med ett schema för farfar-far-Son (offentlig sektor GFS)](backup-azure-backup-cloud-as-tape.md). Det innebär att du kan definiera en kombinerad bevarandeprincip som involverar varje dag, varje vecka, månatliga och årliga bevarandeprinciper. I det här exemplet vi skapa en matris som representerar den komplexa kvarhållningsschema som vi vill och konfigurera kvarhållning intervallet med den [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762) cmdlet.

```
PS C:\> $RRlist = @()
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Ange schemat för säkerhetskopiering
DPM anger en standard-Säkerhetskopieringsschemat automatiskt om du anger den skydd objektiva med den ```Set-DPMPolicyObjective``` cmdlet. Du kan ändra standardschemana med den [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749) cmdlet följt av den [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723) cmdlet.

```
PS C:\> $onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

I exemplet ovan ```$onlineSch``` är en matris med fyra element som innehåller det befintliga schemat för online-skydd för Skyddsgruppen i GFS schemat:

1. ```$onlineSch[0]``` innehåller dagligt schema
2. ```$onlineSch[1]``` innehåller veckoschemat
3. ```$onlineSch[2]``` innehåller månadsschema
4. ```$onlineSch[3]``` innehåller årliga schemat

Så om du behöver ändra schema för veckovis, måste du referera till den ```$onlineSch[1]```.

### <a name="initial-backup"></a>Den första säkerhetskopieringen
När en datakälla för första gången, måste DPM säkerhetskopierar skapar första replik som skapar en fullständig kopia av datakällan som ska skyddas på DPM-replikvolymen. Den här aktiviteten kan antingen schemaläggas för en viss tid eller kan aktiveras manuellt, med hjälp av den [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715) cmdlet med parametern ```-NOW```.

```
PS C:\> Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```
### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Ändra storlek på DPM-Replikvolymen och återställningspunktvolymen
Du kan också ändra storleken på DPM-replikvolymen och Shadow Copy volym med [Set-DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) cmdlet som i följande exempel: Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Genomför ändringarna i skyddsgruppen
Slutligen måste ändringarna genomföras innan DPM kan säkerhetskopiera per den nya Skyddsgruppen-konfigurationen. Detta kan uppnås med hjälp av den [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758) cmdlet.

```
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```
## <a name="view-the-backup-points"></a>Visa säkerhetskopieringspunkter
Du kan använda den [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746) cmdlet för att hämta en lista över alla återställningspunkter för en datakälla. I det här exemplet kommer vi att:

* Hämta alla PGs på DPM-servern och lagras i en matris ```$PG```
* Hämta datakällor som motsvarar den ```$PG[0]```
* Hämta alla återställningspunkter för en datakälla.

```
PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Återställa skyddade data på Azure
Återställning av data är en kombination av en ```RecoverableItem``` objekt och en ```RecoveryOption``` objekt. I det föregående avsnittet vi en lista över säkerhetskopieringspunkter för en datakälla.

I exemplet nedan visar vi hur du återställer en virtuell Hyper-V-dator från Azure Backup genom att kombinera säkerhetskopieringspunkter med mål för återställning. Det här exemplet innehåller:

* Skapar en återställning alternativet med den [New DPMRecoveryOption](https://technet.microsoft.com/library/hh881592) cmdlet.
* Hämtar punktmatrisen backup med hjälp av den ```Get-DPMRecoveryPoint``` cmdlet.
* Om du väljer en säkerhetskopieringspunkt att återställa från.

```
PS C:\> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

PS C:\> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Kommandona kan enkelt utökas för någon typ av datakälla.

## <a name="next-steps"></a>Nästa steg
* Läs mer om DPM till Azure Backup [introduktion till DPM-säkerhetskopiering](backup-azure-dpm-introduction.md)
