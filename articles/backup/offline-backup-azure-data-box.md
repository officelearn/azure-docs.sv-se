---
title: Offlinesäkerhetskopiering med hjälp av Azure Data Box
description: Lär dig hur du kan använda Azure Data Box för att dirigera stora inledande säkerhetskopierade data offline från MARS-agenten till ett Recovery Services-valv.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a031a8cac357e7d212f8f6a3a5dbec749fbccc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672971"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Azure Backup offline säkerhetskopiering med hjälp av Azure Data Box

Du kan använda [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) för att dirigera dina stora första Microsoft Azure Recovery Services (MARS) säkerhetskopior offline (utan att använda nätverk) till ett Recovery Services-valv. Den här processen sparar tid och nätverksbandbredd som annars skulle förbrukas flytta stora mängder säkerhetskopieringsdata online över ett nätverk med hög latens. Den här förbättringen är för närvarande i förhandsversion. Offline säkerhetskopiering baserad på Azure Data Box ger två olika fördelar jämfört med [offline säkerhetskopiering baserat på Azure Import/Export-tjänsten:](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)

* Du behöver inte skaffa egna Azure-kompatibla diskar och kopplingar. Azure Data Box levereras diskarna som är associerade med den valda [datarute-SKU](https://azure.microsoft.com/services/databox/data/).
* Azure Backup (MARS Agent) kan direkt skriva säkerhetskopierade data till de SKU:er som stöds av Azure Data Box. Den här funktionen eliminerar behovet av att etablera en mellanlagringsplats för dina första säkerhetskopierade data. Du behöver inte heller verktyg för att formatera och kopiera dessa data till diskarna.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure-dataruta med MARS-agenten

I den här artikeln beskrivs hur du kan använda Azure Data Box för att dirigera stora inledande säkerhetskopierade data offline från MARS-agenten till ett Recovery Services-valv.

## <a name="supported-platforms"></a>Plattformar som stöds

Processen för att dirigera data från MARS-agenten med hjälp av Azure Data Box stöds på följande Windows SKU:er.

| **Operativsystem**                                 | **Sku**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Arbetsstation**                        |                                                              |
| Windows 10 64-bitars                     | Enterprise, Pro, Home                                       |
| Windows 8.1 64-bitars                    | Enterprise, Pro                                             |
| Windows 8 64-bitars                      | Enterprise, Pro                                             |
| Windows 7 64-bitars                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Server**                             |                                                              |
| Windows Server 2019 64-bitars            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 64-bitars            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 64-bitars         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 64-bitars            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 64-bitars    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 64-bitars | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 64-bitars    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1 64-bitars     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 bitar        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Säkerhetskopior för datastorlek och datarutesscku:er som stöds

| Datastorlek för säkerhetskopiering (efterkomprimering av MARS)* per server | SKU som stöds av Azure Data Box                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <=7,2 TB                                                    | [Azure Data Box-disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| >7,2 TB och <=80 TB**                                      | [Azure-dataruta (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

*Typiska komprimeringshastigheter varierar mellan 10 % och 20 %. <br>
**Om du förväntar dig att ha mer än 80 TB [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)initiala säkerhetskopieringsdata för en enda MARS-server kontaktar du .

>[!IMPORTANT]
>Inledande säkerhetskopieringsdata från en enda server måste finnas i en enda Azure Data Box-instans eller Azure Data Box-disk och kan inte delas mellan flera enheter av samma eller olika SKU:er. Men en Azure Data Box-enhet kan innehålla inledande säkerhetskopior från flera servrar.

## <a name="prerequisites"></a>Krav

### <a name="azure-subscription-and-required-permissions"></a>Azure-prenumeration och nödvändiga behörigheter

* Processen kräver en Azure-prenumeration.
* Processen kräver att användaren som utsetts att utföra offline-säkerhetskopieringsprincipen är ägare till Azure-prenumerationen.
* Data Box-jobbet och recovery services-valvet (som data måste dirigeras till) måste finnas i samma prenumerationer.
* Vi rekommenderar att mållagringskontot som är associerat med Azure Data Box-jobbet och Recovery Services-valvet finns i samma region. Detta är dock inte nödvändigt.

### <a name="get-azure-powershell-370"></a>Skaffa Azure PowerShell 3.7.0

*Detta är den viktigaste förutsättningen för processen*. Innan du installerar Azure PowerShell, version 3.7.0, utför du följande kontroller.

#### <a name="step-1-check-the-powershell-version"></a>Steg 1: Kontrollera PowerShell-versionen

1. Öppna Windows PowerShell och kör följande kommando:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1.  Om utdata visar en version som är högre än 3.7.0 gör du "Steg 2". Annars hoppar du till "Steg 3".

#### <a name="step-2-uninstall-the-powershell-version"></a>Steg 2: Avinstallera PowerShell-versionen

Avinstallera den aktuella versionen av PowerShell.

1. Ta bort de beroende modulerna genom att köra följande kommando i PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Kör följande kommando för att säkerställa att alla beroende moduler har tagits bort:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Steg 3: Installera PowerShell version 3.7.0

När du har verifierat att det inte finns några AzureRM-moduler installerar du version 3.7.0 med någon av följande metoder:

* Från GitHub använder du [den här länken](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).

Eller så kan du:

* Kör följande kommando i PowerShell-fönstret:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell kunde också ha installerats med hjälp av en msi-fil. Om du vill ta bort den avinstallerar du den med alternativet **Avinstallera program** på Kontrollpanelen.

### <a name="order-and-receive-the-data-box-device"></a>Beställ och ta emot Data Box-enheten

Offline säkerhetskopieringsprocessen med MARS och Azure Data Box kräver att Data Box-enheterna är i ett levererat tillstånd innan du utlöser offline säkerhetskopiering med hjälp av MARS-agenten. Information om hur du beställer den lämpligaste SKU:n för ditt behov finns i [Dataräner för säkerhetskopiering och datarutessku:er som stöds](#backup-data-size-and-supported-data-box-skus). Följ stegen i [självstudiekurs: Beställ en Azure Data Box-disk](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) för att beställa och ta emot dina Data Box-enheter.

> [!IMPORTANT]
> Välj inte *BlobStorage* för **typen Konto**. MARS-agenten kräver ett konto som stöder sidblobar, vilket inte stöds när *BlobStorage* är markerat. Välj **Lagring V2 (allmänt v2)** som **kontoslag** när du skapar mållagringskontot för ditt Azure Data Box-jobb.

![Välj kontosort i instansinformation](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>Installera och konfigurera MARS-agenten

1. Se till att du avinstallerar alla tidigare installationer av MARS-agenten.
1. Ladda ner den senaste MARS Agent från [denna webbplats](https://aka.ms/azurebackup_agent).
1. Kör *MARSAgentInstaller.exe*och gör *bara* stegen för att installera och [registrera agenten](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent) i Recovery Services-valvet där du vill att säkerhetskopiorna ska lagras.

   > [!NOTE]
   > Valvet för återställningstjänster måste finnas i samma prenumeration som Azure Data Box-jobbet.

   När agenten har registrerats i valvet för återställningstjänster följer du stegen i nästa avsnitt.

## <a name="set-up-azure-data-box-devices"></a>Konfigurera Azure Data Box-enheter

Beroende på azure data box SKU du beställde, gör stegen som beskrivs i lämpliga avsnitt som följer. Stegen visar hur du ställer in och förbereder Data Box-enheterna för MARS-agenten för att identifiera och överföra de första säkerhetskopierade data.

### <a name="set-up-azure-data-box-disks"></a>Konfigurera Azure Data Box-diskar

Om du har beställt en eller flera Azure Data Box-diskar (upp till 8 TB vardera) följer du stegen som nämns här för att [packa upp, ansluta och låsa upp databoxdisken](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up).

>[!NOTE]
>Det är möjligt att servern med MARS-agenten inte har någon USB-port. I det fallet kan du ansluta din Azure Data Box-disk till en annan server eller klient och exponera enhetens rot som en nätverksresurs.

### <a name="set-up-azure-data-box"></a>Konfigurera Azure Data Box

Om du har beställt en Azure Data Box-instans (upp till 100 TB) följer du stegen här [för att konfigurera databoxinstansen](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up).

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Montera din Azure Data Box-instans som ett lokalt system

MARS-agenten fungerar i kontexten Lokalt system, så det kräver samma behörighetsnivå som ska tillhandahållas till monteringssökvägen där Azure Data Box-instansen är ansluten. 

Så här ser du till att du kan montera databoxenheten som ett lokalt system med hjälp av NFS-protokollet:

1. Aktivera klienten för NFS-funktionen på Windows-servern som har MARS-agenten installerad. Ange den alternativa källan *WIM:D:\Sources\Install.wim:4*.
1. Ladda ner PSExec från <https://download.sysinternals.com/files/PSTools.zip> till servern med MARS Agent installerad.
1. Öppna en upphöjd kommandotolk och kör följande kommando med katalogen som innehåller *PSExec.exe* som aktuell katalog.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   Kommandofönstret som öppnas som ett resultat av föregående kommando finns i kontexten Lokalt system. Använd det här kommandofönstret för att köra stegen för att montera Azure-sidblobbresursen som en nätverksenhet på din Windows-server.
1. Följ stegen i [Connect to Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) för att ansluta servern till MARS-agenten till Data Box-enheten via NFS. Kör följande kommando i kommandotolken Lokalt system för att montera azure-sidblobar.Run the following command on the Local System command prompt to mount the Azure page blobs share.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   När resursen har monterats kontrollerar du om du kan komma åt X: från servern. Om du kan fortsätter du med nästa avsnitt i den här artikeln.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Överföra initiala säkerhetskopierade data till Azure Data Box-enheter

1. Öppna **Microsoft Azure Backup-programmet** på servern.
1. Välj **Schemalägg säkerhetskopiering** **i** åtgärdsfönstret .

    ![Välj Schemasäkerhetskopiering](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Följ stegen i **guiden Schemalägg säkerhetskopiering**.

1. Lägg till objekt genom att välja knappen **Lägg till objekt.** Behåll den totala storleken på objekten inom de [storleksgränser som stöds av Azure Data Box SKU](#backup-data-size-and-supported-data-box-skus) som du beställde och tog emot.

    ![Lägga till objekt i säkerhetskopia](./media/offline-backup-azure-data-box/add-items.png)

1. Välj lämplig plan och bevarandeprincip för **säkerhetskopiering** för filer och mappar och **systemtillstånd**. Systemtillståndet gäller endast för Windows-servrar och inte för Windows-klienter.
1. På sidan **Välj inledande säkerhetskopieringstyp (filer och mappar)** i guiden väljer du alternativet **Överför med Microsoft Azure Data Box-diskar** och väljer **Nästa**.

    ![Välj inledande typ av säkerhetskopiering](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Logga in på Azure när du uppmanas att använda användarautentiseringsuppgifterna som har ägarbehörighet på Azure-prenumerationen. När du har lyckats med det bör du se en sida som liknar den här.

    ![Skapa resurser och tillämpa nödvändiga behörigheter](./media/offline-backup-azure-data-box/creating-resources.png)

   MARS-agenten hämtar sedan databox-jobben i prenumerationen som är i tillståndet Levererad.

    ![Hämta databox-jobb för prenumerations-ID](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Välj rätt databoxordning som du har packat upp, anslutit och låst upp databoxdisken för. Välj **Nästa**.

    ![Välj order i datarutan](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Välj **Identifiera enhet** på sidan Identifiering av **databoxenheter.** Den här åtgärden gör att MARS-agenten söker efter lokalt anslutna Azure Data Box-diskar och identifierar dem.

    ![Identifiering av databoxenheter](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Om du har anslutit Azure Data Box-instansen som en nätverksresurs (på grund av otillgänglighet för USB-portar eller på grund av att du beställde och monterade databox-enheten på 100 TB) misslyckas identifieringen först. Du får möjlighet att ange nätverkssökvägen till Data Box-enheten.

    ![Ange nätverkssökvägen](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Ange nätverkssökvägen till rotkatalogen för Azure Data Box-disken. Den här katalogen måste innehålla en katalog med namnet *PageBlob*.
    >
    >![Rotkatalog för Azure Data Box-disk](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Om till exempel diskens `\\mydomain\myserver\disk1\` sökväg finns och *disk1* innehåller en katalog som heter *PageBlob,* är `\\mydomain\myserver\disk1\`sökvägen som du anger på marsagentguidens guidesida .
    >
    >Om du [konfigurerar en Azure Data Box 100-TB-enhet](#set-up-azure-data-box-devices)anger du `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` som nätverkssökväg till enheten.

1. Välj **Nästa**och välj **Slutför** på nästa sida för att spara principen för säkerhetskopiering och kvarhållning med konfigurationen av offline säkerhetskopiering med hjälp av Azure Data Box.

   Följande sida bekräftar att principen har sparats.

    ![Principen sparas framgångsrikt](./media/offline-backup-azure-data-box/policy-saved.png)

1. Välj **Stäng** på föregående sida.

1. Välj **Säkerhetskopiera nu** i **åtgärdsfönstret** på MARS-agentkonsolen. Välj **Säkerhetskopiera** på guidesidan.

    ![Guiden Säkerhetskopiera nu](./media/offline-backup-azure-data-box/backup-now.png)

MARS-agenten börjar säkerhetskopiera de data som du har valt till Azure Data Box-enheten. Den här processen kan ta från flera timmar till några dagar. Hur lång tid det beror på antalet filer och anslutningshastighet mellan servern med MARS-agenten och Azure Data Box-disken.

När säkerhetskopieringen av data är klar visas en sida på MARS-agenten som liknar den här.

![För säkerhetskopieringsstatus som visas](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Steg efter säkerhetskopiering

I det här avsnittet beskrivs stegen efter att säkerhetskopieringen av data till Azure Data Box Disk har slutförts.

* Följ stegen i den här artikeln för att [leverera Azure Data Box-disken till Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up). Om du har använt en Azure Data Box 100-TB-enhet följer du dessa steg för att [leverera Azure Data Box-enheten till Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

* [Övervaka databox-jobbet](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) i Azure-portalen. När Azure Data Box-jobbet är klart flyttar MARS-agenten automatiskt data från lagringskontot till Recovery Services-valvet vid tidpunkten för nästa schemalagda säkerhetskopiering. Sedan markeras säkerhetskopieringsjobbet som *slutfört jobb* om en återställningspunkt har skapats.

    >[!NOTE]
    >MARS-agenten utlöser säkerhetskopior vid de tider som schemalagts när principen skapas. Dessa jobb flaggar "Väntar på att Azure Data Box-jobbet ska slutföras" tills jobbet är klart.

* När MARS-agenten har skapat en återställningspunkt som motsvarar den första säkerhetskopian kan du ta bort lagringskontot eller det specifika innehållet som är associerat med Azure Data Box-jobbet.

## <a name="troubleshooting"></a>Felsökning

MAB-agenten (Microsoft Azure Backup) skapar ett Azure Active Directory-program (Azure AD) åt dig i din klientorganisation. Det här programmet kräver ett certifikat för autentisering som skapas och överförs när du konfigurerar en offline-dirigeringsprincip. Vi använder Azure PowerShell för att skapa och överföra certifikatet till Azure AD-programmet.

### <a name="problem"></a>Problem

När du konfigurerar offline säkerhetskopiering kan du få problem på grund av ett fel i Azure PowerShell-cmdleten. Du kanske inte kan lägga till flera certifikat i samma Azure AD-program som skapats av MAB-agenten. Det här problemet påverkar dig om du har konfigurerat en offline-dirigeringsprincip för samma eller en annan server.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Kontrollera om problemet orsakas av den här specifika grundorsaken

Om du vill se om problemet är detsamma som det som tidigare beskrivits gör du något av följande steg.

#### <a name="step-1"></a>Steg 1

Kontrollera om följande felmeddelande visas i MAB-konsolen när du konfigurerade offlinesäkerhetskopiering.

![Det går inte att skapa principen för offlinesäkerhetskopiering för det aktuella Azure-kontot](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>Steg 2

1. Öppna mappen **Temp** i installationssökvägen. Standardsökvägen för temporära mappar är *C:\Program\Microsoft Azure Recovery Services Agent\Temp*. Leta efter *CBUICurr-filen* och öppna filen.

1. I *filen CBUICurr* bläddrar du till den sista raden och kontrollerar om problemet `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`är detsamma som det i det här felmeddelandet: .

### <a name="workaround"></a>Lösning

Som en lösning för att lösa problemet gör du följande steg och försöker igen principkonfigurationen.

#### <a name="step-1"></a>Steg 1

Logga in på PowerShell som visas i MAB-användargränssnittet med hjälp av ett annat konto med administratörsåtkomst på prenumerationen som kommer att skapa import- eller exportjobbet.

#### <a name="step-2"></a>Steg 2

Om ingen annan server har offline-seedning konfigurerad `AzureOfflineBackup_<Azure User Id>` och ingen annan server är beroende av programmet, tar du bort det här programmet. Välj **Azure portal** > **Azure Active Directory** > **App registreringar**.

>[!NOTE]
> Kontrollera om `AzureOfflineBackup_<Azure User Id>` programmet inte har någon annan offline-seedning konfigurerad och även om ingen annan server är beroende av det här programmet. Gå till **Inställningsnycklar** > **Keys** under avsnittet **Offentliga nycklar.** Det bör inte ha några andra offentliga nycklar till. Se följande skärmdump för referens.
>
>![Offentliga nycklar](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Steg 3

Utför följande åtgärder från den server som du försöker konfigurera för offlinesäkerhetskopiering.

1. Gå till fliken **Hantera datorcertifikatprogram** > **Personligt** och leta `CB_AzureADCertforOfflineSeeding_<ResourceId>`efter certifikatet med namnet .

2. Markera certifikatet, högerklicka på **Alla aktiviteter**och välj **Exportera** utan en privat nyckel i .cer-formatet.

3. Gå till Azure offline backup ansökan som nämns i steg 2. Välj **Inställningar Nycklar** > **Keys** > **Ladda upp offentlig nyckel**. Ladda upp certifikatet som du exporterade i föregående steg.

    ![Ladda upp offentlig nyckel](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Öppna registret på servern genom att ange **regedit** i körningsfönstret.

5. Gå till *registerdatorn\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Högerklicka på **CloudBackupProvider**och lägg till ett `AzureADAppCertThumbprint_<Azure User Id>`nytt strängvärde med namnet .

    >[!NOTE]
    > Så här hämtar du Azure-användar-ID:et:
    >
    >* Kör `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` kommandot från Azure-anslutna PowerShell.
    > * Gå till registersökvägen *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup* med namnet *CurrentUserId*.

6. Högerklicka på strängen som lagts till i föregående steg och välj **Ändra**. I värdet anger du tumavtrycket för det certifikat som du exporterade i steg 2. Välj **OK**.

7. Om du vill hämta tumavtryckets värde dubbelklickar du på certifikatet. Välj fliken **Detaljer** och rulla nedåt tills du ser tumavtrycksfältet. Välj **Tumavtryck**och kopiera värdet.

    ![Tumavtrycksfält för certifikat](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Frågor

För eventuella frågor eller förtydliganden om [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)eventuella problem du ställts inför, kontakta .
