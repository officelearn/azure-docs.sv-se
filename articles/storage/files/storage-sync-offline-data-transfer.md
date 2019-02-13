---
title: Använd Data Box och andra metoder för offline samlats in till Azure File Sync.
description: Processen och bästa praxis för att aktivera synkronisering kompatibla bulk migrering stöder.
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: fauhse
ms.component: files
ms.openlocfilehash: a184e0563d1ad26671c38cabe07f42d97cbe2885
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213622"
---
# <a name="migrate-to-azure-file-sync"></a>Migrera till Azure File Sync
Det finns flera alternativ för att flytta till Azure File Sync:

### <a name="uploading-files-via-azure-file-sync"></a>Överföra filer via Azure File Sync
Det enklaste alternativet är att flytta dina filer lokalt på en Windows Server 2012 R2 eller senare och installerar du Azure File Sync-agenten. När synkronisering har konfigurerats kommer att överföra dina filer från servern. Vi får en genomsnittlig uppladdning hastighet mellan alla våra kunder på 1 TB om varje 2 dagar.
Överväg en [bandbreddsbegränsning schema](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter) att se till att din server är en bra medborgare i ditt datacenter.

### <a name="offline-bulk-transfer"></a>Offline bulk överföring
Laddar upp är verkligen det enklaste alternativet, fungera den inte för dig om den tillgängliga bandbredden inte tillåter för att synkronisera dina filer till Azure inom en rimlig tid. Utmaning att övervinna här är den första synkroniseringen av hela uppsättning filer. Azure File Sync flyttas därefter endast ändringar när de inträffar på det namnområde som du vanligtvis använder mycket mindre bandbredd.
Att lösa den här inledande överföring utmaning, offline bulk Migreringsverktyg, till exempel Azure [Data Box-familjen](https://azure.microsoft.com/services/storage/databox) kan användas. I följande artikel fokuserar på processen som du måste följa för att gynnas av offline-migrering i ett kompatibelt Azure File Sync-sätt. Den beskriver metodtips som hjälper dig att undvika konfliktfiler och bevara dina filer och mappar ACL: er och tidsstämplar när du har aktiverat synkronisering.

### <a name="online-migration-tools"></a>Online Migreringsverktyg
Processen som beskrivs nedan fungerar inte bara för Data Box. Den fungerar för en migrering offline-verktyg (till exempel Data Box) eller online-verktyg, till exempel AzCopy, Robocopy eller tredje parts verktyg och tjänster. Så oavsett vilken metod att lösa inledande överföring utmaningen, det är fortfarande är viktigt att du följer stegen som beskrivs nedan kan använda dessa verktyg i en synkronisering kompatibla sätt.


## <a name="offline-data-transfer-benefits"></a>Förmåner för överföring av data offline
De främsta fördelarna med migrering offline när du använder en Data Box är följande:

- När du migrerar filer till Azure via en process för offline bulk överföring, till exempel Data Box har du inte överför alla filer från din server i nätverket. Detta kan innebära avsevärda besparingar i nätverkets bandbredd och tid för stora namnområden.
- När du använder Azure File Sync och sedan oavsett transportsätt används (Data Box, Azure Import osv.), överför din live-server endast de filer som har ändrats sedan du levererade data till Azure.
- Azure File Sync säkerställer att dina filer och mappar ACL: er har synkroniserats samt - även om produkten offline bulk migrering inte transporterar ACL: er.
- När du använder Azure Data Box och Azure File Sync, finns det någon avbrottstid. Om du använder Data Box för att överföra data i till Azure blir effektiv användning av nätverksbandbredd och behålla fil-återgivning. Det håller också namnområdet uppdaterad genom att ladda upp endast de filer som har ändrats sedan Data Box har skickats.

## <a name="plan-your-offline-data-transfer"></a>Planera din överföring av data offline
Innan du börjar bör du granska följande information:

- Slutför din grupp migrering till en eller flera Azure-filresurser innan du aktiverar synkronisering med Azure File Sync.
- Om du planerar att använda Data Box för bulk-migrering: Granska den [distributionskraven för Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planera din slutliga Azure File Sync-topologi: [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- Välj Azure storage-konton som ska innehålla de filresurser som du vill synkronisera med. Se till att migreringen bulk händer med tillfällig mellanlagring resurser i samma lagringskonton. Massinläsning migrering kan bara aktiveras genom att använda ett sista- och en mellanlagrings-resurs som finns i samma lagringskonto.
- En bulk-migrering kan endast användas när du skapar en ny synkroniseringsrelation med en plats på servern. Du kan inte aktivera en bulk-migrering med en befintlig synkronisering.

## <a name="offline-data-transfer-process"></a>Processen för överföring av data offline
Det här avsnittet beskriver processen för att ställa in Azure File Sync på ett sätt kompatibel med bulk Migreringsverktyg, till exempel Azure Data Box.

![Visualisera processtegen som också beskrivs i detalj i följande punkt](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Steg | Information |
|---|---------------------------------------------------------------------------------------|
| ![Processen steg 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Beställa Data Box](../../databox/data-box-deploy-ordered.md). Det finns [flera erbjudanden i Data Box-familjen](https://azure.microsoft.com/services/storage/databox/data) att matcha dina behov. Ta emot Data Box och följ Data Box [dokumentationen för att kopiera dina data](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box). Se till att data har kopierats till den här UNC-sökväg på Data Box: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>` där den `ShareName` är namnet på den tillfälliga resursen. Skicka Data Box tillbaka till Azure. |
| ![Processen steg 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Vänta tills filerna som visas i de Azure-filresurser som du angett som tillfällig mellanlagring resurser. **Aktivera inte synkronisera till dessa resurser!** |
| ![Processen steg 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Skapa en ny resurs som är tomt för varje resurs som Data Box har skapat för dig. Se till att den här nya resursen är i samma lagringskonto som Data Box-resursen. [Så här skapar du en ny Azure-filresurs](storage-how-to-create-file-share.md). |
| ![Processen steg 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Skapa en synkroniseringsgrupp](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) i en tjänst för lagringssynkronisering och referens tom resursen som en molnslutpunkt. Upprepa det här steget för varje Data Box-filresurs. Granska den [distribuera Azure File Sync](storage-sync-files-deployment-guide.md) guide och följ anvisningarna för att ställa in Azure File Sync. |
| ![Processen steg 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Lägg till ditt live serverkatalogen som en serverslutpunkt](storage-sync-files-deployment-guide.md#create-a-server-endpoint). Ange i processen för att du redan flyttade filer till Azure och referera till mellanlagrings-resurser. Det är att aktivera eller inaktivera molnlagringsnivåer efter behov. När du skapar en serverslutpunkt på live-servern, måste du referera till mellanlagring resursen. Aktivera ”Offline för överföring av Data” (se bilden nedan) i bladet för nya server-slutpunkt och referera till den tillfälliga resurs som måste finnas i samma lagringskonto som molnslutpunkten. Listan över tillgängliga resurser filtreras efter storage-konto och resurser som inte redan synkroniseras. |

![Visualisera dina Azure portal användargränssnittet för att aktivera Offline för överföring av Data när du skapar en ny serverslutpunkt.](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Synkronisera resursen
När du har skapat din serverslutpunkt, påbörjas synkronisering. För varje fil som finns på servern, avgör synkronisera om den här filen finns även i mellanlagring resursen där Data Box deponerats filerna och om så synkronisering kommer att kopiera filen från mellanlagring resursen i stället för att ladda upp den från servern. Om filen finns inte på den tillfälliga resursen eller en nyare version är tillgänglig på den lokala servern, sedan synkronisering kommer att överföra filen från den lokala servern.

> [!IMPORTANT]
> Du kan bara aktivera massinfogning migrering läge under genereringen av en serverslutpunkt. När en serverslutpunkt har upprättats, det finns för närvarande inget sätt att integrera bulk migrerat data från en redan synkroniserar server till namnområdet.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACL: er och tidsstämplar för filer och mappar
Azure File Sync säkerställer att fil- och ACL: er har synkroniserats från live-servern även om Migreringsverktyget för massinläsning som användes för inte transporterar ACL: er från början. Det innebär att det är OK för mellanlagring resursen som inte innehåller några ACL: er för filer och mappar. När du aktiverar funktionen offlinedata migrering när du skapar en ny serverslutpunkt kan kommer ACL: er att synkroniseras vid den tiden för alla filer på servern. Detsamma gäller för skapa - och ändrade-tidsstämplar.

## <a name="shape-of-the-namespace"></a>Formen på namnområdet
Formen på namnområdet som bestäms av vad som finns på servern när synkronisering har aktiverats. Om filer tas bort på den lokala servern efter Data Box ”-ögonblicksbild” och - migrering och de här filerna inte anslutas till live, synkroniserar namnområdet. De kan fortfarande i tillfälliga resursen men aldrig kopieras. Det är önskat beteende som synkronisering håller namnområdet enligt live servern. Data Box ”ögonblicksbild” är en mellanlagrings grunden för effektiv filkopiering och inte utfärdaren av formen för live-namnområdet.

## <a name="finishing-bulk-migration-and-clean-up"></a>Slutföra bulk-migrering och rensa
Skärmbilden nedan visar egenskapsbladet för server-slutpunkt i Azure-portalen. Du kan se statusen för processen under offline för överföring av Data. Den visas antingen ”pågår” eller ”slutfört”.

När servern är klar dess den första synkroniseringen av hela namnområdet det kommer är klar med att använda den tillfälliga filen filresursen med Data Box-grupp migrerade filer. Observera i egenskaperna för slutpunkten för överföring av data offline som status ändras till ”Slutför”. Du kan rensa upp mellanlagring resursen kan minska kostnaderna för tillfället:

1. Tryck på ”inaktivera offline dataöverföring” i egenskaperna för server-slutpunkt när status är ”slutfört”.
2. Överväg att ta bort tillfälliga resursen för att minska kostnaderna. Mellanlagringsplatser resursen är sannolikt inte kommer att innehålla filer och mappar ACL: er och är därför begränsad användbarhet. För ”peka i tiden” säkerhetskopiering, i stället skapa ett reellt [ögonblicksbild av synkroniserar Azure-filresursen](storage-snapshots-files.md). Du kan [aktivera Azure Backup för att ta ögonblicksbilder]( ../../backup/backup-azure-files.md) enligt ett schema.

![Visualisera dina Azure portal användargränssnittet för egenskaper för serverslutpunkter där status och inaktivera kontrollerna för Offline för överföring av Data finns.](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Du bör bara inaktivera det här läget när tillståndet är ”slutfört” eller du verkligen vill avbryta pga en felkonfiguration. Om du inaktiverar läge förtid en distribution av legitima börjar filer ladda upp från servern, även om din tillfälliga filresurs är fortfarande tillgänglig.

> [!IMPORTANT]
> När du har inaktiverat offline dataöverföring går det inte att aktivera den igen, även om mellanlagring resursen från bulk migreringen fortfarande är tillgänglig.

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
