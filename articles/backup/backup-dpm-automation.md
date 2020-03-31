---
title: Använda PowerShell för att säkerhetskopiera DPM-arbetsbelastningar
description: Lär dig hur du distribuerar och hanterar DPM (Azure Backup for Data Protection Manager) med PowerShell
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 06c138a4015a0b730369e091fc57a34d2190051d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616740"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Distribuera och hantera säkerhetskopiering till Azure för DPM-servrar (Data Protection Manager) med PowerShell

Den här artikeln visar hur du använder PowerShell för att konfigurera Azure Backup på en DPM-server och för att hantera säkerhetskopiering och återställning.

## <a name="setting-up-the-powershell-environment"></a>Konfigurera PowerShell-miljön

Innan du kan använda PowerShell för att hantera säkerhetskopior från Data Protection Manager till Azure måste du ha rätt miljö i PowerShell. I början av PowerShell-sessionen kontrollerar du att du kör följande kommando för att importera rätt moduler och att du kan referera till DPM-cmdlets på rätt sätt:

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Installation och registrering

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Börja [med att hämta den senaste Azure PowerShell](/powershell/azure/install-az-ps).

Följande inställnings- och registreringsuppgifter kan automatiseras med PowerShell:

* skapar ett Recovery Services-valv
* Installera Azure Backup-agenten
* Registrera dig med Azure Backup-tjänsten
* Inställningar för nätverk
* Krypteringsinställningar

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

Följande steg leder dig genom att skapa ett Recovery Services-valv. Ett Recovery Services-valv skiljer sig från ett valv för säkerhetskopiering.

1. Om du använder Azure Backup för första gången måste du använda cmdleten **Register-AzResourceProvider** för att registrera Azure Recovery Service-providern med din prenumeration.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Valvet för återställningstjänster är en ARM-resurs, så du måste placera det i en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en ny. När du skapar en ny resursgrupp anger du namn och plats för resursgruppen.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Använd cmdleten **New-AzRecoveryServicesVault** för att skapa ett nytt valv. Var noga med att ange samma plats för valvet som användes för resursgruppen.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Ange vilken typ av lagringsredundans som ska användas. Du kan använda [Lokalt redundant lagring (LRS)](../storage/common/storage-redundancy-lrs.md) eller [GEO Redundant Storage (GRS)](../storage/common/storage-redundancy-grs.md). I följande exempel visas alternativet -BackupStorageRedundancy för testVault är inställt på GeoRedundant.

   > [!TIP]
   > Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata. Därför är det praktiskt att lagra säkerhetskopians Recovery Services-valvobjekt i en variabel.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Visa valven i en prenumeration

Använd **Get-AzRecoveryServicesVault** om du vill visa listan över alla valv i den aktuella prenumerationen. Du kan använda det här kommandot för att kontrollera att ett nytt valv har skapats eller för att se vilka valv som är tillgängliga i prenumerationen.

Kör kommandot Get-AzRecoveryServicesVault och alla valv i prenumerationen visas.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Installera Azure Backup-agenten på en DPM-server

Innan du installerar Azure Backup-agenten måste du ha installerat om det finns en installationsprogram på Windows Server. Du kan hämta den senaste versionen av installationsprogrammet från [Microsoft Download Center](https://aka.ms/azurebackup_agent) eller från recovery services-valvets instrumentpanelssida. Spara installationsprogrammet på en lättillgänglig plats som *C:\Downloads\*.

Om du vill installera agenten kör du följande kommando i en upphöjd PowerShell-konsol **på DPM-servern:**

```powershell
MARSAgentInstaller.exe /q
```

Detta installerar agenten med alla standardalternativ. Installationen tar några minuter i bakgrunden. Om du inte anger alternativet */nu* öppnas **fönstret Windows Update** i slutet av installationen för att söka efter uppdateringar.

Agenten visas i listan över installerade program. Om du vill se listan över installerade program går du till Program**Programs** > **och funktioner på** **Kontrollpanelen** > .

![Agent installerad](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Installationsalternativ

Om du vill visa alla tillgängliga alternativ via kommandoraden använder du följande kommando:

```powershell
MARSAgentInstaller.exe /?
```

De tillgängliga alternativen är:

| Alternativ | Information | Default |
| --- | --- | --- |
| /q |Tyst installation |- |
| /p:"plats" |Sökväg till installationsmappen för Azure Backup-agenten. |C:\Program\Microsoft Azure Recovery Services Agent |
| /s:"plats" |Sökväg till cachemappen för Azure Backup-agenten. |C:\Program\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Anmäl dig till Microsoft Update |- |
| /nu |Sök inte efter uppdateringar när installationen är klar |- |
| /d |Avinstallerar Microsoft Azure Recovery Services Agent |- |
| /ph (ph) |Proxyvärdadress |- |
| /po (po) |Portnummer för proxyvärdport |- |
| /pu |Användarnamn för proxyvärd |- |
| /pw (pw) |Proxy lösenord |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Registrera DPM i ett Recovery Services Vault

När du har skapat valvet för Återställningstjänster hämtar du den senaste agenten och autentiseringsuppgifterna för valvet och lagrar det på ett bekvämt ställe som C:\Downloads.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

På DPM-servern kör du cmdleten [Start-OBRegistration](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obregistration?view=winserver2012-ps) för att registrera datorn med valvet.

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Inledande konfigurationsinställningar

När DPM-servern har registrerats i valvet för Återställningstjänster börjar den med standardprenumerationsinställningar. Dessa prenumerationsinställningar inkluderar nätverk, kryptering och mellanlagringsområdet. Om du vill ändra prenumerationsinställningarna måste du först få ett handtag på de befintliga (standard)inställningarna med cmdleten [Get-DPMCloudSubscriptionSetting:](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019)

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Alla ändringar görs i det här ```$setting``` lokala PowerShell-objektet och sedan är det fullständiga objektet åta sig att DPM och Azure Backup för att spara dem med [cmdleten Set-DPMCloudSubscriptionSetting.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) Du måste använda ```–Commit``` flaggan för att säkerställa att ändringarna sparas. Inställningarna tillämpas inte och används av Azure Backup om de inte har bekräftats.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Nätverk

Om anslutningen av DPM-datorn till Azure Backup-tjänsten på internet är via en proxyserver, bör proxyserverinställningarna tillhandahållas för lyckade säkerhetskopior. Detta görs med ```-ProxyServer```hjälp ```-ProxyPort``` ```-ProxyUsername``` av ```ProxyPassword``` och , och parametrarna med cmdleten [Set-DPMCloudSubscriptionSetting.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) I det här exemplet finns det ingen proxyserver så vi rensar uttryckligen all proxyrelaterad information.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Bandbreddsanvändning kan också styras med alternativ för ```-WorkHourBandwidth``` och ```-NonWorkHourBandwidth``` för en viss uppsättning dagar i veckan. I det här exemplet anger vi inte någon begränsning.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Konfigurera mellanlagringsområdet

Azure Backup-agenten som körs på DPM-servern behöver tillfällig lagring för data som återställs från molnet (lokalt mellanlagringsområde). Konfigurera mellanlagringsområdet med cmdleten [Set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) och parametern. ```-StagingAreaPath```

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

I exemplet ovan ställs mellanlagringsområdet in på *C:\StagingArea* i ```$setting```PowerShell-objektet . Kontrollera att den angivna mappen redan finns, annars misslyckas det slutliga åtagandet av prenumerationsinställningarna.

### <a name="encryption-settings"></a>Krypteringsinställningar

Säkerhetskopieringsdata som skickas till Azure Backup krypteras för att skydda sekretessen för data. Krypteringslösenfrasen är "lösenordet" för att dekryptera data vid återställningen. Det är viktigt att hålla denna information säker och säker när den är inställd.

I exemplet nedan konverterar det första ```passphrase123456789``` kommandot strängen till en säker sträng ```$Passphrase```och tilldelar den säkra strängen till variabeln med namnet . Det andra kommandot anger ```$Passphrase``` den säkra strängen som lösenord för kryptering av säkerhetskopior.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Skydda lösenfrasinformationen på ett säkert och säkert sätt när den är inställd. Du kommer inte att kunna återställa data från Azure utan den här lösenfrasen.
>
>

Nu borde du ha gjort alla nödvändiga ```$setting``` ändringar i objektet. Kom ihåg att genomföra ändringarna.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Skydda data till Azure Backup

I det här avsnittet lägger du till en produktionsserver i DPM och skyddar sedan data till lokal DPM-lagring och sedan till Azure Backup. I exemplen visar vi hur du säkerhetskopierar filer och mappar. Logiken kan enkelt utökas för att säkerhetskopiera alla DPM-stödda datakällor. Alla dina DPM-säkerhetskopior styrs av en skyddsgrupp (PG) med fyra delar:

1. **Gruppmedlemmar** är en lista över alla skyddsbara objekt (kallas även *Datasources* i DPM) som du vill skydda i samma skyddsgrupp. Du kanske till exempel vill skydda virtuella produktions-datorer i en skyddsgrupp och SQL Server-databaser i en annan skyddsgrupp eftersom de kan ha olika säkerhetskopieringskrav. Innan du kan säkerhetskopiera en datakälla på en produktionsserver måste du se till att DPM-agenten är installerad på servern och hanteras av DPM. Följ stegen för [att installera DPM-agenten](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) och länka den till lämplig DPM-server.
2. **Dataskyddsmetoden** anger målsäkerhetsplatserna - band, disk och moln. I vårt exempel kommer vi att skydda data till den lokala disken och till molnet.
3. Ett **säkerhetskopieringsschema** som anger när säkerhetskopieringar måste göras och hur ofta data ska synkroniseras mellan DPM-servern och produktionsservern.
4. Ett **bevarandeschema** som anger hur länge återställningspunkterna ska behållas i Azure.

### <a name="creating-a-protection-group"></a>Skapa en skyddsgrupp

Börja med att skapa en ny skyddsgrupp med cmdleten [New-DPMProtectionGroup.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/new-dpmprotectiongroup?view=systemcenter-ps-2019)

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Den ovan nämnda cmdleten skapar en skyddsgrupp med namnet *ProtectGroup01*. En befintlig skyddsgrupp kan också ändras senare för att lägga till säkerhetskopiering i Azure-molnet. Men för att göra några ändringar i skyddsgruppen - nya eller befintliga - måste vi få ett handtag på ett *ändringsbart* objekt med hjälp av [Cmdlet get-DPMModifiableProtectionGroup.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmmodifiableprotectiongroup?view=systemcenter-ps-2019)

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Lägga till gruppmedlemmar i skyddsgruppen

Varje DPM-agent känner till listan över datakällor på servern som den är installerad på. Om du vill lägga till en datakälla i skyddsgruppen måste DPM-agenten först skicka tillbaka en lista över datakällorna till DPM-servern. En eller flera datakällor väljs sedan och läggs till i skyddsgruppen. De PowerShell-steg som behövs för att uppnå detta är:

1. Hämta en lista över alla servrar som hanteras av DPM via DPM-agenten.
2. Välj en viss server.
3. Hämta en lista över alla datakällor på servern.
4. Välj en eller flera datakällor och lägg till dem i skyddsgruppen

Listan över servrar där DPM-agenten är installerad och hanteras av DPM-servern förvärvas med Cmdleten [Get-DPMProductionServer.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmproductionserver?view=systemcenter-ps-2019) I det här exemplet filtrerar och konfigurerar vi endast PS med *namnproduktionsserver01* för säkerhetskopiering.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

Hämta nu listan över datakällor på ```$server``` hur du använder cmdleten [Get-DPMDatasource.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmdatasource?view=systemcenter-ps-2019) I det här exemplet filtrerar vi för volymen *D:\\ * som vi vill konfigurera för säkerhetskopiering. Den här datakällan läggs sedan till i skyddsgruppen med cmdleten [Add-DPMChildDatasource.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019) Kom ihåg att använda det ```$MPG``` *ändringsbara* skyddsgruppsobjektet för att göra tilläggen.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Upprepa det här steget så många gånger som krävs, tills du har lagt till alla valda datakällor i skyddsgruppen. Du kan också börja med bara en datakälla och slutföra arbetsflödet för att skapa skyddsgruppen och vid ett senare tillfälle lägga till fler datakällor i skyddsgruppen.

### <a name="selecting-the-data-protection-method"></a>Välja dataskyddsmetod

När datakällorna har lagts till i skyddsgruppen är nästa steg att ange skyddsmetoden med cmdlet [set-DPMProtectionType.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmprotectiontype?view=systemcenter-ps-2019) I det här exemplet är skyddsgruppen inställd för lokal disk- och molnsäkerhetskopiering. Du måste också ange den datakälla som du vill skydda i molnet med cmdleten [Add-DPMChildDatasource](https://docs.microsoft.com/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019) med -Online-flagga.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Ställa in kvarhållningsområdet

Ange kvarhållning för säkerhetskopieringspunkterna med hjälp av [cmdlet set-DPMPolicyObjective.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019) Även om det kan verka konstigt att ställa in kvarhållningen innan säkerhetskopieringsschemat har definierats, ställer ```Set-DPMPolicyObjective``` cmdlet automatiskt in ett standardschema för säkerhetskopiering som sedan kan ändras. Det är alltid möjligt att ställa in säkerhetskopieringsschemat först och bevarandeprincipen efter.

I exemplet nedan anger cmdlet bevarandeparametrarna för säkerhetskopiering av diskar. Detta behåller säkerhetskopior i 10 dagar och synkroniserar data var sjätte timme mellan produktionsservern och DPM-servern. Den ```SynchronizationFrequencyMinutes``` definierar inte hur ofta en säkerhetskopieringspunkt skapas, men hur ofta data kopieras till DPM-servern.  Den här inställningen förhindrar att säkerhetskopior blir för stora.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

För säkerhetskopior som går till Azure (DPM refererar till dem som onlinesäkerhetskopior) kan kvarhållningsintervallen konfigureras för [långsiktig kvarhållning med hjälp av ett Grandfather-Father-Son-schema (GFS).](backup-azure-backup-cloud-as-tape.md) Det innebär att du kan definiera en kombinerad bevarandeprincip som omfattar dagliga, veckovisa, månatliga och årliga bevarandeprinciper. I det här exemplet skapar vi en matris som representerar det komplexa kvarhållningsschema som vi vill ha och konfigurerar sedan kvarhållningsintervallet med hjälp av [cmdlet set-DPMPolicyObjective.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019)

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Ställ in säkerhetskopieringsschemat

DPM anger ett standardschema för säkerhetskopiering automatiskt ```Set-DPMPolicyObjective``` om du anger skyddsmålet med hjälp av cmdleten. Om du vill ändra standardscheman använder du cmdleten [Get-DPMPolicySchedule](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmpolicyschedule?view=systemcenter-ps-2019) följt av cmdleten [Set-DPMPolicySchedule.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyschedule?view=systemcenter-ps-2019)

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

I exemplet ovan ```$onlineSch``` finns en matris med fyra element som innehåller det befintliga onlineskyddsschemat för skyddsgruppen i GFS-schemat:

1. ```$onlineSch[0]```innehåller det dagliga schemat
2. ```$onlineSch[1]```innehåller veckoschemat
3. ```$onlineSch[2]```innehåller månadsschemat
4. ```$onlineSch[3]```innehåller det årliga schemat

Så om du behöver ändra veckoschemat måste ```$onlineSch[1]```du hänvisa till .

### <a name="initial-backup"></a>Den första säkerhetskopieringen

När du säkerhetskopierar en datakälla för första gången skapar DPM-behov en första replik som skapar en fullständig kopia av datakällan som ska skyddas på DPM-replikvolymen. Den här aktiviteten kan antingen schemaläggas för en viss tid eller utlösas manuellt med hjälp av [cmdlet set-DPMReplicaCreationMethod](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmreplicacreationmethod?view=systemcenter-ps-2019) med parametern ```-NOW```.

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Ändra storleken på DPM Replica & återställningspunktsvolym

Du kan också ändra storleken på DPM Replica volym och Shadow Copy volym med [Set-DPMDatasourceDiskAllocation](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmdatasourcediskallocation?view=systemcenter-ps-2019) cmdlet som i följande exempel: Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### <a name="committing-the-changes-to-the-protection-group"></a>Genomföra ändringarna i skyddsgruppen

Slutligen måste ändringarna göras innan DPM kan ta säkerhetskopian enligt den nya skyddsgruppkonfigurationen. Detta kan uppnås med hjälp av [cmdleten Set-DPMProtectionGroup.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmprotectiongroup?view=systemcenter-ps-2019)

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Visa säkerhetskopieringspunkterna

Du kan använda cmdleten [Get-DPMRecoveryPoint](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmrecoverypoint?view=systemcenter-ps-2019) för att få en lista över alla återställningspunkter för en datakälla. I det här exemplet kommer vi att:

* hämta alla PGs på DPM-servern och lagras i en matris```$PG```
* få de datakällor som motsvarar```$PG[0]```
* hämta alla återställningspunkter för en datakälla.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Återställa data som skyddas på Azure

Att återställa data är ```RecoverableItem``` en kombination ```RecoveryOption``` av ett objekt och ett objekt. I föregående avsnitt fick vi en lista över säkerhetskopieringspunkterna för en datakälla.

I exemplet nedan visar vi hur du återställer en Hyper-V virtuell dator från Azure Backup genom att kombinera säkerhetskopieringspunkter med målet för återställning. Det här exemplet innehåller:

* Skapa ett återställningsalternativ med cmdleten [New-DPMRecoveryOption.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/new-dpmrecoveryoption?view=systemcenter-ps-2019)
* Hämtar matrisen med säkerhetskopieringspunkter med hjälp av cmdleten. ```Get-DPMRecoveryPoint```
* Välja en säkerhetskopieringspunkt att återställa från.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation "C:\VMRecovery"

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Kommandona kan enkelt utökas för alla dataresurser.

## <a name="next-steps"></a>Nästa steg

* Mer information om DPM i Azure Backup finns [i Introduktion till DPM-säkerhetskopiering](backup-azure-dpm-introduction.md)
