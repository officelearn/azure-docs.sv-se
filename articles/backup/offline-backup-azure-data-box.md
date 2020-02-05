---
title: Säkerhets kopiering offline med Azure Data Box
description: Lär dig hur du kan använda Azure Data Box för att dirigera stora första säkerhets kopierings data offline från MARS-agenten till ett Azure Recovery Services-valv.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 553939df8aa7a1b097b2cd6c7f29365302101324
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027035"
---
# <a name="azure-backup-offline-backup-using-azure-data-box"></a>Azure Backup offline – säkerhets kopiering med Azure Data Box

Du kan använda tjänsten [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) för att dirigera dina stora inledande mars-säkerhetskopieringar offline (utan att använda nätverket) i ett Azure Recovery Services-valv.  Detta sparar både tids-och nätverks bandbredd, som annars skulle förbrukas flytta stora mängder säkerhetskopierade data online över ett nätverk med hög latens. Den här förbättringen är för närvarande en för hands version. Azure Data Box-baserad säkerhets kopiering offline ger två distinkta fördelar jämfört med [Azure import/export service-baserade säkerhets kopiering offline](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

1. Du behöver inte skaffa dina egna Azure-kompatibla diskar och anslutningar. Azure Data Box tjänst levererar de diskar som är associerade med den valda [data Box-enhet SKU: n](https://azure.microsoft.com/services/databox/data/)

2. Azure Backup (MARS-agent) kan skriva säkerhets kopierings data direkt till de SKU: er som stöds av Azure Data Box. Detta eliminerar behovet av att tillhandahålla en mellanlagringsplats för dina första säkerhets kopierings data och behovet av att använda verktyg för att formatera och kopiera dessa data till diskarna.

## <a name="azure-data-box-with-mars-agent"></a>Azure Data Box med MARS-agenten

Den här artikeln förklarar hur du kan använda Azure Data Box för att dirigera stora inledande säkerhets kopierings data offline från MARS-agenten till ett Azure Recovery Services-valv. Artikeln är indelad i följande delar:

* Plattformar som stöds
* Data Box-enhet SKU: er som stöds
* Förutsättningar
* Konfigurera MARS-agenten
* Installations Azure Data Box
* Säkerhetskopiera data överföring till Azure Data Box
* Steg efter säkerhets kopiering

## <a name="supported-platforms"></a>Plattformar som stöds

Processen för att dirigera data från MARS-agenten med hjälp av Azure Data Box stöds i följande Windows SKU: er:

| **OS**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Arbets Station**                        |                                                              |
| Windows 10 64-bit                     | Enterprise, Pro, Home                                       |
| Windows 8,1 64-bit                    | Enterprise, Pro                                             |
| Windows 8 64-bit                      | Enterprise, Pro                                             |
| Windows 7 64-bit                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, starter |
| **Server**                             |                                                              |
| Windows Server 2019 64-bitars            | Standard, data Center, Essentials                            |
| Windows Server 2016 64-bitars            | Standard, data Center, Essentials                            |
| Windows Server 2012 R2 64-bit         | Standard, data Center, Foundation                            |
| Windows Server 2012 64-bitars            | Data Center, Foundation, standard                            |
| Windows Storage Server 2016 64 bitar    | Standard, arbets grupp                                         |
| Windows Storage Server 2012 R2 64 bit | Standard, arbets grupp, viktigt                              |
| Windows Storage Server 2012 64 bitar    | Standard, arbets grupp                                         |
| Windows Server 2008 R2 SP1 64 bitar     | Standard, Enterprise, data Center, Foundation                |
| Windows Server 2008 SP2 64 bit        | Standard, Enterprise, data Center                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Säkerhetskopiera data storlek och Data Box-enhet SKU: er som stöds

| Säkerhets kopierings data storlek (post komprimering per MARS) * per server | Azure Data Box SKU som stöds                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| < = 7,2 TB                                                    | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7,2 TB och < = 80 TB * *                                      | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\* Vanliga komprimerings hastigheter varierar mellan 10-20% <br>
\* * Kontakta [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) om du förväntar dig att ha mer än 80 TB första säkerhets kopierings data för en enskild mars-Server.

>[!IMPORTANT]
>Första säkerhets kopierings data från en enskild server måste finnas i en enda Azure Data Box eller Azure Data Box Disk och kan inte delas mellan flera enheter av samma eller olika SKU: er. En Azure Data Box enhet kan dock innehålla första säkerhets kopiering från flera servrar.

## <a name="pre-requisites"></a>Förutsättningar

### <a name="azure-subscription-and-required-permissions"></a>Azure-prenumeration och nödvändiga behörigheter

* Processen kräver en Azure-prenumeration
* Processen kräver att användaren som har angetts för att utföra säkerhets kopierings principen är "ägare" av Azure-prenumerationen
* Data Box-enhet-jobbet och Recovery Services-valvet (som data måste dirigeras till) måste finnas i samma prenumerationer.
* Vi rekommenderar att mål lagrings kontot som är kopplat till Azure Data Box-jobbet och Recovery Services-valvet finns i samma region. Detta är dock inte nödvändigt.

### <a name="get-azure-powershell-370"></a>Hämta Azure PowerShell 3.7.0

**Detta är de viktigaste kraven för processen**. Innan du installerar Azure PowerShell (ver. 3.7.0) utför du följande kontroller * *

#### <a name="step-1-check-powershell-version"></a>Steg 1: kontrol lera PowerShell-versionen

* Öppna Windows PowerShell och kör följande kommando:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

* Om utdata visar en högre version än **3.7.0**utför du steg 2 nedan. Annars går du till steg 3.

#### <a name="step-2-uninstall-the-powershell-version"></a>Steg 2: Avinstallera PowerShell-versionen

Avinstallera den aktuella versionen av PowerShell genom att utföra följande åtgärder:

* Ta bort de beroende modulerna genom att köra följande kommando i PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

* Kör följande kommando för att säkerställa att alla beroende moduler tas bort:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Steg 3: installera PowerShell-versionen 3.7.0

När du har kontrollerat att det inte finns några AzureRM-moduler, installerar du sedan version 3.7.0 med någon av följande metoder:

* Från GitHub, [länk](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

ELLER

* Kör följande kommando i PowerShell-fönstret:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell kan också ha installerats med en MSI-fil. Ta bort den genom att avinstallera den med alternativet Avinstallera program på kontroll panelen.

### <a name="order-and-receive-the-data-box-device"></a>Beställ och ta emot den Data Box-enhet enheten

Offline-säkerhetskopiering med MARS och Azure Data Box kräver att de nödvändiga Data Box-enhet enheterna är i "levererat" läge innan säkerhets kopiering offline aktive ras med MARS-agenten. Referera till den [säkerhetskopierade data storleken och data Box-enhet SKU: er](#backup-data-size-and-supported-data-box-skus) för att beställa den lämpligaste SKU: n för ditt krav. Följ stegen i [den här artikeln](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) för att beställa och ta emot dina data Box-enhet enheter.

>[!IMPORTANT]
>Välj inte BlobStorage för konto typen. MARS-agenten kräver ett konto som stöder Page blobbar som inte stöds när BlobStorage har valts. Vi rekommenderar starkt att du väljer *Storage v2* (*generell användning v2*) som konto typ när du skapar mål lagrings kontot för ditt Azure Data Box-jobb.

![Välj konto typ i instans information](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-setup-the-mars-agent"></a>Installera och konfigurera MARS-agenten

* Se till att avinstallera alla tidigare installationer av MARS-agenten.

* Hämta den senaste MARS-agenten [härifrån.](https://aka.ms/azurebackup_agent)

* Kör *MARSAgentInstaller. exe* och utför**bara** de steg som krävs för att [Installera och registrera agenten](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) på det Recovery Services valv som du vill att dina säkerhets kopior ska lagras i.

  >[!NOTE]
  > Recovery Services-valvet måste finnas i samma prenumeration som Azure Data Boxs jobbet.

* När agenten har registrerats i Recovery Services-valvet följer du stegen i avsnitten nedan.

## <a name="setup-azure-data-box-devices"></a>Installations Azure Data Box enhet (er)

Beroende på Azure Data Box SKU: n som du har beställt, utför du stegen i lämpliga avsnitt nedan för att konfigurera och förbereda Data Box-enhet enheter för MARS-agenten för att identifiera och överföra de första säkerhets kopierings data.

### <a name="setup-azure-data-box-disk"></a>Installations Azure Data Box Disk

Om du har ordnat en eller flera Azure Data Box diskar (upp till 8 TB var och en) följer du stegen som beskrivs här för att [packa upp, ansluta och låsa upp data Box disk](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up).

>[!NOTE]
>Det är möjligt att servern med MARS-agenten inte har någon USB-port. I så fall kan du ansluta Azure Data Box Disk till en annan server/klient och exponera enhetens rot som en nätverks resurs.

### <a name="setup-azure-data-box"></a>Installations Azure Data Box

Om du beställde en Azure Data Box (upp till 100 TB) följer du stegen som beskrivs här [för att konfigurera din data Box-enhet](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up).

#### <a name="mount-your-azure-data-box-as-local-system"></a>Montera Azure Data Box som lokalt system

MARS-agenten fungerar i det lokala system sammanhanget och kräver därför att samma behörighets nivå anges för monterings Sök vägen där Azure Data Box är ansluten. Följ stegen nedan för att se till att du kan montera din Data Box-enhet-enhet som lokalt system med hjälp av NFS-protokollet:

* Aktivera funktionen klient för NFS på Windows Server (som har MARS-agenten installerad).<br>
  Ange alternativ källa: *WIM: D: \Sources\Install.wim: 4*

* Hämta PSExec från <https://download.sysinternals.com/files/PSTools.zip> till servern med MARS-agenten installerad.

* Öppna en kommando tolk med förhöjd behörighet och kör följande kommando med katalogen som innehåller PSExec. exe som den aktuella katalogen:

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

* Kommando fönstret som öppnas som ett resultat av kommandot ovan är i det lokala systemets kontext. Använd det här kommando fönstret för att utföra stegen för att montera Azure-sidans BLOB-resurs som en nätverks enhet på Windows Server.

* Följ stegen [här](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) för att ansluta servern till mars-agenten till den data Box-enhet enheten via NFS och kör följande kommando i kommando tolken för den lokala datorn för att montera Azure Page blobs-resursen:

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

* När du har monterat kontrollerar du om du kan komma åt X: från servern. Om du väljer Ja fortsätter du med nästa avsnitt i den här artikeln.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Överför första säkerhets kopierings data till Azure Data Box enhet (er)

* Öppna programmet **Microsoft Azure Backup** på servern.

* Klicka på **Schemalägg säkerhets kopiering** i **Åtgärds** fönstret.

    ![Klicka på Schemalägg säkerhets kopiering](./media/offline-backup-azure-data-box/schedule-backup.png)

* Följ stegen i **guiden Schemalägg säkerhets kopiering**

* **Lägg till objekt** så att den totala storleken på objekten ligger inom [storleks gränser som stöds av den Azure Data Box SKU: n](#backup-data-size-and-supported-data-box-skus) som du beställde och mottog.

    ![Lägg till objekt i säkerhets kopian](./media/offline-backup-azure-data-box/add-items.png)

* Välj lämplig säkerhets kopierings schema och bevarande princip för filer och mappar och system tillstånd (system tillstånd gäller endast för Windows-servrar och inte för Windows-klienter)

* På skärmen **Välj första säkerhets kopierings typ (filer och mappar)** i guiden väljer du den alternativ **överföring som använder Microsoft Azure Data Box-enhet diskar** och klickar på **Nästa**

    ![Välj första säkerhets kopierings typ](./media/offline-backup-azure-data-box/initial-backup-type.png)

* Logga in på Azure när du uppmanas att använda de användarautentiseringsuppgifter som har ägar åtkomst till Azure-prenumerationen. När du har lyckats göra det ser du en skärm som liknar den som visas nedan:

    ![Skapa resurser och tillämpa nödvändiga behörigheter](./media/offline-backup-azure-data-box/creating-resources.png)

* MARS-agenten hämtar sedan Data Box-enhet jobb i prenumerationen som har statusen "levererat".

    ![Hämtar data i jobb för prenumerations-ID](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

* Välj rätt data Box ordning som du har packat upp, anslutit och olåst Data Box-enhet disken. Klicka på **Next**.

    ![Välj Data Box-enhet ordning (er)](./media/offline-backup-azure-data-box/select-databox-order.png)

* Klicka på **identifiera enhet** på skärmen **data Box-enhet enhets identifiering** . Detta gör att MARS-agenten söker efter lokalt anslutna Azure Data Box diskar och identifierar dem enligt nedan:

    ![Identifiering av Data Box-enhet enhet](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Om du har anslutit Azure Data Box som en nätverks resurs (på grund av otillgängligheten hos USB-portar eller om du beställde och monterat Data Box-enhet enheten på 100 TB) Miss känner identifieringen först, men du får möjlighet att ange nätverks Sök vägen till Data Box-enhet enheten som en hown nedan:

    ![Ange Nätverks Sök vägen](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Ange Nätverks Sök vägen till rot katalogen på den Azure Data Box disken. Katalogen måste innehålla en katalog med namnet *PageBlob* som visas nedan:
    >
    >![Azure Data Box diskens rot Katalog](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Till exempel om sökvägen till disken är `\\mydomain\myserver\disk1\` och *Disk1* innehåller en katalog med namnet *PageBlob*, är sökvägen som ska anges i guiden för MARS-agenten `\\mydomain\myserver\disk1\`
    >
    >Om du [installerar en Azure Data Box 100 TB-enhet](#setup-azure-data-box)anger du följande som nätverks Sök väg till enheten `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`

* Klicka på **Nästa** och sedan på **Slutför** på nästa skärm för att spara säkerhets kopierings-och bevarande principen med konfigurationen av offline-säkerhetskopiering med Azure Data box.

* Följande skärm bild visar att principen har sparats:

    ![Principen har sparats](./media/offline-backup-azure-data-box/policy-saved.png)

* Klicka på **Stäng** på skärmen ovan.

* Klicka på ***Säkerhetskopiera nu** i fönstret **åtgärder** i mars agent-konsolen och klicka på **säkerhetskopiera** på Guide skärmen enligt nedan:

    ![Guiden Säkerhetskopiera nu](./media/offline-backup-azure-data-box/backup-now.png)

* MARS-agenten kommer att börja säkerhetskopiera de data som du har valt till den Azure Data Box enheten. Detta kan ta från flera timmar till några dagar, beroende på antalet filer och anslutnings hastighet mellan servern med MARS-agenten och Azure Data Box Disk.

* När säkerhets kopieringen av data har slutförts visas en skärm på MARS-agenten som liknar den nedan:

    ![Säkerhets kopierings förlopp visas](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Steg efter säkerhets kopiering

I det här avsnittet beskrivs de steg som du vidtar när säkerhets kopieringen av data till Azure Data Box Disk har slutförts.

* Följ stegen i den här artikeln för att [leverera Azure Data Box-disken till Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up). Om du använde en Azure Data Box 100-TB-enhet följer du stegen nedan för att [leverera Azure Data Box till Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

* [Övervaka data Box-enhet jobb](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) i Azure Portal. När Azure Data Box jobbet har slutförts flyttar MARS-agenten automatiskt data från lagrings kontot till Recovery Services-valvet vid nästa schemalagda säkerhets kopiering. Då markeras säkerhets kopierings jobbet som "jobb slutfört" om en återställnings punkt har skapats.

    >[!NOTE]
    >MARS-agenten utlöser säkerhets kopieringar vid de tidpunkter som schemaläggs när principen skapas. Dessa jobb kommer dock att flagga "väntar på att Azure Data Box jobb ska slutföras" tills jobbet är klart.

* När MARS-agenten har skapat en återställnings punkt som motsvarar den första säkerhets kopieringen kan du ta bort lagrings kontot (eller det angivna innehållet) som är kopplat till Azure Data Box jobbet.

## <a name="troubleshooting"></a>Felsöka

Agenten Microsoft Azure Backup (monoklonal) skapar ett Azure AD-program åt dig i din klient organisation. Det här programmet kräver ett certifikat för autentisering som skapas och laddas upp när du konfigurerar Dirigerings principen offline. Vi använder Azure PowerShell för att skapa och ladda upp certifikatet till Azure AD-programmet.

### <a name="issue"></a>Problem

När du konfigurerar säkerhets kopiering offline kan du stöta på problem, där det beror på ett fel i Azure PowerShell-cmdleten att du inte kan lägga till flera certifikat i samma Azure AD-program som skapats av monoklonal-agenten. Detta påverkar dig om du har konfigurerat en seeding-princip som är offline för samma eller en annan server.

### <a name="how-to-verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Så här kontrollerar du om problemet orsakas av denna grundläggande orsak

Utför något av följande steg för att säkerställa att felet beror på problemet ovan:

#### <a name="step-1"></a>Steg 1

Kontrol lera om följande fel meddelande visas i monoklonal-konsolen vid konfigurering av säkerhets kopiering offline:

![Det gick inte att skapa en princip för offline-säkerhetskopiering för det aktuella Azure-kontot](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>Steg 2

* Öppna **Temp** -mappen i installations Sök vägen (standard Sök vägen för temp är *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*). Leta efter **CBUICurr** -filen och öppna filen.

* Bläddra till den sista raden i **CBUICurr** -filen och kontrol lera om problemet beror på `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`.

### <a name="workaround"></a>Lösning

Som ett sätt att lösa det här problemet, utför följande steg och försök att utföra princip konfigurationen igen.

#### <a name="first-step"></a>Första steget

Logga in på PowerShell som visas i användar gränssnittet för användar gränssnittet med hjälp av ett annat konto med administratörs behörighet för den prenumeration som ska användas för att importera export jobb.

#### <a name="second-step"></a>Andra steget

Om ingen annan server har offline-dirigering konfigurerad och ingen annan server är beroende av `AzureOfflineBackup_<Azure User Id>` programmet, tar du bort det här programmet från **Azure Portal** > **Azure Active Directory** > **Appregistreringar.**

>[!NOTE]
> Kontrol lera om program `AzureOfflineBackup_<Azure User Id>` inte har någon annan offlineadressbok konfigurerad och att ingen annan server är beroende av det här programmet. Gå till **inställningar** > **nycklar** under avsnittet **offentliga nycklar** den ska inte ha några andra offentliga nycklar tillagda. Se följande skärm bild för referens:
>
>![Offentliga nycklar](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="third-step"></a>Tredje steget

Utför följande åtgärder från servern som du försöker konfigurera offline-säkerhetskopiering:

1. Öppna fliken **Hantera dator certifikats program** > **personliga** och leta efter certifikatet med namnet `CB_AzureADCertforOfflineSeeding_<ResourceId>`

2. Välj certifikatet ovan, högerklicka på **alla aktiviteter** och **Exportera** utan privat nyckel i. cer-format.

3. Gå till säkerhets kopierings programmet för Azure offline som anges i **punkt 2**. I **inställningarna** > **nycklar** > **överför offentlig nyckel** laddar du upp det exporterade certifikatet i steget ovan.

    ![Ladda upp offentlig nyckel](./media/offline-backup-azure-data-box/upload-public-key.png)

4. På-servern öppnar du registret genom att skriva **regedit** i körnings fönstret.

5. Gå till register *datorn \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider.* Högerklicka på **CloudBackupProvider** och Lägg till ett nytt sträng värde med namnet `AzureADAppCertThumbprint_<Azure User Id>`

    >[!NOTE]
    > Gör något av följande för att hämta Azure-användar-ID:
    >
    >1. Kör kommandot `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as defined in the portal”` från Azure Connected PowerShell.
    > 2. Navigera till register Sök vägen: *dator \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\DbgSettings\OnlineBackup*; Namn: *CurrentUserId*

6. Högerklicka på den sträng som har lagts till i steget ovan och välj **ändra**. I värdet anger du tumavtrycket för det certifikat som du exporterade i **punkt 2** och klickar på **OK**.

7. För att hämta värdet för tumavtryck, dubbelklicka på certifikatet, Välj fliken **information** och rulla nedåt tills du ser fältet tumavtryck. Klicka på **tumavtryck** och kopiera värdet.

    ![Tumavtryck för certifikat](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Frågor

Om du har frågor eller vill veta mer om eventuella problem som har nåtts kan du kontakta [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)
