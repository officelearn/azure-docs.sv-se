---
title: Migrera data till Azure File Sync med hjälp av Azure Data Box och andra metoder
description: Migrera stora mängder data på ett sätt som är kompatibel med Azure File Sync.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d1ec5168b898d0aa75c12e6eb435e20c09de1929
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64700276"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>Migrera stora mängder data till Azure File Sync
Du kan migrera stora mängder data till Azure File Sync på två sätt:

* **Överför filer med hjälp av Azure File Sync.** Det här är den enklaste metoden. Flytta dina filer lokalt på Windows Server 2012 R2 eller senare och installerar du Azure File Sync-agenten. När du har konfigurerat synkroniseringen, överförs dina filer från servern. (Våra kunder för närvarande uppleva en genomsnittlig ladda upp hastigheten på 1 TiB om varannan dag.) För att säkerställa att din server inte använder för mycket av bandbredd för ditt datacenter, kanske du vill konfigurera en [bandbreddsbegränsning schema](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Överföra dina filer offline.** Om du inte har tillräckligt med bandbredd kan kanske du inte kan ladda upp filer till Azure inom en rimlig tid. Utmaningen är den första synkroniseringen av hela uppsättning filer. Om du vill lösa denna utmaning att använda Migreringsverktyg för offline samtidigt som den [Azure Data Box-familjen](https://azure.microsoft.com/services/storage/databox). 

Den här artikeln beskrivs hur du migrerar filer offline på ett sätt som är kompatibel med Azure File Sync. Följ instruktionerna för att undvika filkonflikter och att bevara din fil- och mappen åtkomstkontrollistor (ACL) och tidsstämplar när du har aktiverat synkroniseringen.

## <a name="online-migration-tools"></a>Online Migreringsverktyg
Processen som beskrivs i den här artikeln fungerar inte bara för Data Box, men också för andra verktyg för offline-migrering. Den fungerar även för online-verktyg, till exempel AzCopy, Robocopy, eller partner verktyg och tjänster. Men du kan lösa första överför utmaning, följer du stegen i den här artikeln om du vill använda dessa verktyg på ett sätt som är kompatibel med Azure File Sync.


## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Fördelarna med att använda ett verktyg för att överföra data offline
Här är de främsta fördelarna med att använda ett verktyg för överföring som Data Box för offline-migrering:

- Du behöver att överföra alla dina filer över nätverket. För stora namnområden kan det här verktyget spara tid och betydande nätverksbandbredd.
- När du använder Azure File Sync, oavsett vilket överföring verktyg du använder (Data Box, tjänsten Azure Import/Export och så vidare), din realtidsserver överför endast de filer som ändras när du flyttar data till Azure.
- Azure File Sync synkroniserar dina filer och mappar ACL: er, även om Migreringsverktyget offline bulk inte transport ACL: er.
- Data Box och Azure File Sync kräver inget avbrott. När du använder Data Box för att överföra data till Azure, använda bandbredd i nätverket effektivt och bevara filen återgivningen. Du också hålla ditt namnområde uppdaterade genom att ladda upp endast de filer som ändras när du flyttar data till Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Krav för överföringen offlinedata
Du bör inte aktivera synkronisering på den server som du migrerar innan du slutför din överföring av data offline. Andra saker att tänka på innan du börjar är följande:

- Om du planerar att använda Data Box för bulk-migrering: Granska den [distributionskraven för Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planera din slutliga Azure File Sync-topologi: [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- Välj Azure storage-konton som ska innehålla de filresurser som du vill synkronisera med. Se till att migreringen bulk händer med tillfällig mellanlagring resurser i samma lagringskonton. Massinläsning migrering kan bara aktiveras genom att använda ett sista- och en mellanlagrings-resurs som finns i samma lagringskonto.
- En bulk-migrering kan endast användas när du skapar en ny synkroniseringsrelation med en plats på servern. Du kan inte aktivera en bulk-migrering med en befintlig synkronisering.


## <a name="process-for-offline-data-transfer"></a>Processen för att överföra data offline
Här är hur du konfigurerar Azure File Sync på ett sätt som är kompatibel med bulk Migreringsverktyg, till exempel Azure Data Box:

![Diagram som visar hur du konfigurerar Azure File Sync](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Steg | Information |
|---|---------------------------------------------------------------------------------------|
| ![Steg 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Beställa Data Box](../../databox/data-box-deploy-ordered.md). Data Box family erbjudanden [flera produkter](https://azure.microsoft.com/services/storage/databox/data) som uppfyller dina behov. När du får din Data box-enhet kan du följa dess [dokumentationen för att kopiera dina data](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) till UNC-sökvägen på Data Box:  *\\< DeviceIPAddres\>\<StorageAccountName_AzFile\> \<ShareName\>*. Här kan *ShareName* är namnet på den tillfälliga resursen. Skicka Data Box tillbaka till Azure. |
| ![Steg 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Vänta tills filerna som visas i Azure-filresurser som du har valt som tillfällig mellanlagring resurser. *Aktivera inte synkroniseras till dessa resurser.* |
| ![Steg 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Skapa en ny tom resurs för varje resurs som Data Box har skapat för dig. Den här nya resursen måste vara i samma lagringskonto som Data Box-resursen. [Så här skapar du en ny Azure-filresurs](storage-how-to-create-file-share.md). |
| ![Steg 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Skapa en synkroniseringsgrupp](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) i en lagringssynkroniseringstjänst. Referens till tom resursen som en molnslutpunkt. Upprepa det här steget för varje Data Box-filresurs. [Konfigurera Azure File Sync](storage-sync-files-deployment-guide.md). |
| ![Steg 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Lägg till ditt live serverkatalogen som en serverslutpunkt](storage-sync-files-deployment-guide.md#create-a-server-endpoint). Ange att du flyttar filerna till Azure i processen och referera till mellanlagrings-resurser. Du kan aktivera eller inaktivera molnlagringsnivåer efter behov. När du skapar en serverslutpunkt på live-servern måste referera till mellanlagring resursen. På den **Lägg till serverslutpunkt** bladet under **Offline dataöverföring**väljer **aktiverad**, och välj sedan den tillfälliga resurs som måste finnas i samma lagringskonto som molnet slutpunkt. Här kan filtreras listan över tillgängliga resurser efter storage-konto och resurser som redan inte är synkroniseras. |

![Skärmbild av Azure portal användargränssnittet, som visar hur för överföring av data offline när du skapar en ny serverslutpunkt](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Synkronisera resursen
När du har skapat din serverslutpunkt startar synkroniseringen. Synkroniseringen avgör om varje fil på servern också finns i tillfälliga resursen där Data Box deponerats filerna. Om filen finns det, kopieras filen från mellanlagring resursen i stället för att ladda upp den från servern i synkroniseringen. Om filen finns inte i tillfälliga resursen eller om det finns en nyare version på den lokala servern synkroniseringen Överför fil från den lokala servern.

> [!IMPORTANT]
> Du kan aktivera bulk migrering läget endast när du skapar en serverslutpunkt. När du har skapat en serverslutpunkt kan du integrera bulk-migrerade data från en redan synkroniserar server i namnområdet.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACL: er och tidsstämplar för filer och mappar
Azure File Sync ser till att fil- och ACL: er har synkroniserats från live-servern även om Migreringsverktyget samtidigt som du använde inte inledningsvis transport ACL: er. Därför behöver inte tillfälliga resursen som innehåller alla ACL: er för filer och mappar. När du aktiverar funktionen offlinedata migrering när du skapar en ny serverslutpunkt, synkroniseras alla ACL på servern. Nyligen skapade och ändrade tidsstämplar synkroniseras också.

## <a name="shape-of-the-namespace"></a>Formen på namnområdet
När du aktiverar synkroniseringen avgör serverns innehållet formen på namnområdet. Om filer tas bort från den lokala servern när Data Box ögonblicksbild och migreringen är klar, flytta inte filerna i live, synkroniserar namnrymd. De förblir i tillfälliga resursen, men de kopieras inte. Detta är nödvändigt eftersom synkroniseringen håller namnområdet enligt live servern. Data Box *ögonblicksbild* är en mellanlagrings grunden för effektiv filkopiering. Det är inte utfärdaren av formen för live-namnområdet.

## <a name="cleaning-up-after-bulk-migration"></a>Rensa upp efter bulk-migrering 
Som servern är klar dess den första synkroniseringen av namnområdet, Använd Data Box bulk-migrerade filer mellanlagrings-filresursen. På den **egenskaper för Serverslutpunkter** bladet i Azure-portalen i den **Offline dataöverföring** avsnittet status ändras från **pågår** till **slutförd** . 

![Skärmbild av bladet egenskaper för Serverslutpunkter där status och inaktivera kontroller för offline-dataöverföring finns](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Du kan nu Rensa mellanlagring resursen att sänka kostnaderna:

1. På den **egenskaper för Serverslutpunkter** bladet när status är **slutförd**väljer **inaktivera offline dataöverföring**.
2. Överväg att ta bort tillfälliga resursen för att spara kostnader. Mellanlagringsplatser resursen innehåller förmodligen inte filer och mappar ACL: er, så att det inte är mycket användbart. För point-in-time-säkerhetskopiering, skapa en verklig [ögonblicksbild av synkroniserar Azure-filresursen](storage-snapshots-files.md). Du kan [konfigurera Azure Backup för att ta ögonblicksbilder]( ../../backup/backup-azure-files.md) enligt ett schema.

Inaktivera överföringsläge offlinedata bara när tillståndet är **slutförd** eller när du vill avbryta på grund av en felaktig konfiguration. Om du inaktiverar läge under en distribution, startar filer att ladda upp från servern, även om din tillfälliga filresurs är fortfarande tillgänglig.

> [!IMPORTANT]
> När du har inaktiverat överföringsläge data offline, kan inte du aktivera den igen, även om mellanlagring resursen från bulk migreringen fortfarande är tillgänglig.

## <a name="next-steps"></a>Nästa steg
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
