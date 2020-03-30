---
title: Migrera data till Azure File Sync med Azure Data Box
description: Migrera massdata på ett sätt som är kompatibelt med Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9398aceeb7465392e82aeaa5760f6c0504f8e33d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159531"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Migrera massdata till Azure File Sync med Azure DataBox
Du kan migrera massdata till Azure File Sync på två sätt:

* **Ladda upp dina filer med hjälp av Azure File Sync.** Detta är den enklaste metoden. Flytta dina filer lokalt till Windows Server 2012 R2 eller senare och installera Azure File Sync-agenten. När du har konfigurerat synkroniseringen överförs filerna från servern. (Våra kunder upplever för närvarande en genomsnittlig uppladdningshastighet på 1 TiB ungefär varannan dag.) Om du vill vara säkra på att servern inte använder för mycket av bandbredden för ditt datacenter kanske du vill konfigurera ett schema för [bandbreddsbegränsning](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Överför dina filer offline.** Om du inte har tillräckligt med bandbredd kanske du inte kan ladda upp filer till Azure på en rimlig tid. Utmaningen är den första synkroniseringen av hela uppsättningen filer. Använd massmigreringsverktyg offline för att lösa den här utmaningen, till exempel [Azure Data Box.](https://azure.microsoft.com/services/storage/databox) 

I den här artikeln beskrivs hur du migrerar filer offline på ett sätt som är kompatibelt med Azure File Sync. Följ dessa instruktioner för att undvika filkonflikter och för att bevara dina kontrolllistor för fil- och mappåtkomst (ACL: er) och tidsstämplar när du har aktiverat synkroniseringen.

## <a name="migration-tools"></a>Migreringsverktyg
Processen som vi beskriver i den här artikeln fungerar inte bara för Data Box utan även för andra offlinemigreringsverktyg. Det fungerar också för verktyg som AzCopy, Robocopy, eller partner verktyg och tjänster som fungerar rakt över Internet. Men för att övervinna den första uppladdningsutmaningen följer du stegen i den här artikeln för att använda dessa verktyg på ett sätt som är kompatibelt med Azure File Sync.

I vissa fall måste du flytta från en Windows Server till en annan Windows Server innan du antar Azure File Sync. [Sms (Storage Migration Service)](https://aka.ms/storagemigrationservice) kan hjälpa till med det. Oavsett om du behöver migrera till en Server OS-version som stöds av Azure File Sync (Windows Server 2012R2 och uppåt) eller om du bara behöver migrera eftersom du köper ett nytt system för Azure File Sync, har SMS många funktioner och fördelar som hjälper dig att få din migrationen sker smidigt.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Fördelar med att använda ett verktyg för att överföra data offline
Här är de största fördelarna med att använda ett överföringsverktyg som Data Box för offlinemigrering:

- Du behöver inte ladda upp alla dina filer över nätverket. För stora namnområden kan det här verktyget spara betydande nätverksbandbredd och tid.
- När du använder Azure File Sync, oavsett vilket överföringsverktyg du använder (Data Box, Azure Import/Export-tjänsten och så vidare), överför din aktiva server bara de filer som ändras när du har flyttat data till Azure.
- Azure File Sync synkroniserar filen och mappen ACL: er även om offline bulkmigreringsverktyget inte transporterar ACL: er.
- Data Box och Azure File Sync kräver inga driftstopp. När du använder Data Box för att överföra data till Azure använder du nätverksbandbredden effektivt och bevarar filåtergivningen. Du håller också ditt namnområde uppdaterat genom att bara ladda upp de filer som ändras när du har flyttat data till Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Förutsättningar för dataöverföring offline
Du bör inte aktivera synkronisering på den server du migrerar innan du slutför överföringen av offlinedata. Andra saker att tänka på innan du börjar är följande:

- Om du planerar att använda Data Box för massmigrering: Granska [distributionsförutsättningarna för Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Planera din slutliga Azure File Sync-topologi: [Planera för en Azure File Sync-distribution](storage-sync-files-planning.md)
- Välj Azure-lagringskonto som ska innehålla de filresurser som du vill synkronisera med. Kontrollera att massmigreringen sker till tillfälliga mellanlagringsresurser i samma lagringskonto. Massmigrering kan bara aktiveras med hjälp av en slutlig och en mellanlagringsresurs som finns i samma lagringskonto.
- En massmigrering kan bara användas när du skapar en ny synkroniseringsrelation med en serverplats. Du kan inte aktivera en massmigrering med en befintlig synkroniseringsrelation.


## <a name="process-for-offline-data-transfer"></a>Process för överföring av offlinedata
Så här konfigurerar du Azure File Sync på ett sätt som är kompatibelt med massmigreringsverktyg som Azure Data Box:

![Diagram som visar hur du konfigurerar Synkronisering av Azure-filer](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Steg | Information |
|---|---------------------------------------------------------------------------------------|
| ![Steg 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Beställ din dataruta](../../databox/data-box-deploy-ordered.md). Data Box-familjen erbjuder [flera produkter](https://azure.microsoft.com/services/storage/databox/data) som uppfyller dina behov. När du får datarutan följer du [dess dokumentation för att kopiera data](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) till den här UNC-sökvägen i datarutan: * \\<DeviceIPAddres\>\<StorageAccountName_AzFile\>\<\>ShareName*. Här är *ShareName* namnet på mellanlagringsresursen. Skicka datarutan tillbaka till Azure. |
| ![Steg 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Vänta tills dina filer visas i Azure-filresurserna som du har valt som tillfälliga mellanlagringsresurser. *Aktivera inte synkronisering till dessa resurser.* |
| ![Steg 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Skapa en ny tom resurs för varje filresurs som Data Box har skapat åt dig. Den här nya resursen ska finnas i samma lagringskonto som dataruteresursen. [Så här skapar du en ny Azure-filresurs](storage-how-to-create-file-share.md). |
| ![Steg 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Skapa en synkroniseringsgrupp](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) i en lagringssynkroniseringstjänst. Referera till den tomma resursen som en molnslutpunkt. Upprepa det här steget för varje filresurs för Data Box. [Konfigurera Synkronisering av Azure-filer](storage-sync-files-deployment-guide.md). |
| ![Steg 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Lägg till din liveserverkatalog som en serverslutpunkt](storage-sync-files-deployment-guide.md#create-a-server-endpoint). I processen anger du att du har flyttat filerna till Azure och referera till mellanlagringsresurserna. Du kan aktivera eller inaktivera molnnivåhantering efter behov. När du skapar en serverslutpunkt på din liveserver refererar du till mellanlagringsresursen. På **slutpunktsbladet Lägg till server** under **Offlinedataöverföring**väljer du Aktiverad och väljer sedan den mellanlagringsresurs som måste finnas i samma lagringskonto som molnslutpunkten. **Enabled** Här filtreras listan över tillgängliga resurser efter lagringskonto och resurser som inte redan synkroniseras. |

![Skärmbild av Användargränssnittet i Azure Portal som visar hur du aktiverar överföring av offlinedata samtidigt som en ny serverslutpunkt skapas](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Synkronisera resursen
När du har skapat serverslutpunkten startar synkroniseringen. Synkroniseringsprocessen avgör om varje fil på servern också finns i mellanlagringsresursen där Data Box deponerade filerna. Om filen finns där kopieras filen från mellanlagringsresursen i stället för att överföra den från servern. Om filen inte finns i mellanlagringsresursen, eller om en nyare version är tillgänglig på den lokala servern, överför synkroniseringsprocessen filen från den lokala servern.

> [!IMPORTANT]
> Du kan bara aktivera massmigreringsläget när du skapar en serverslutpunkt. När du har upprättat en serverslutpunkt kan du inte integrera massrendererade data från en redan synkroniserande server till namnområdet.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACL och tidsstämplar på filer och mappar
Azure File Sync säkerställer att fil- och mapp-ÅTKOMSTkontrollistor synkroniseras från liveservern även om massmigreringsverktyget som du använde inte ursprungligen transporterade ACL:er. På grund av detta behöver mellanlagringsresursen inte innehålla några ACL:er för filer och mappar. När du aktiverar offline-datamigreringsfunktionen när du skapar en ny serverslutpunkt synkroniseras alla åtkomstpunkter för filer på servern. Nyskapade och ändrade tidsstämplar synkroniseras också.

## <a name="shape-of-the-namespace"></a>Namnområdets form
När du aktiverar synkroniseringen bestämmer serverns innehåll namnområdets form. Om filer tas bort från den lokala servern när ögonblicksbilden och migreringen av datarutan har avslutats flyttas inte dessa filer till det aktiva, synkroniserande namnområdet. De stannar kvar i mellanlagringsresursen, men de kopieras inte. Detta är nödvändigt eftersom synkroniseringen behåller namnområdet enligt live-servern. *Ögonblicksbilden* av datarutan är bara en mellanlagringsplats för effektiv filkopiering. Det är inte myndigheten för formen på det levande namnområdet.

## <a name="cleaning-up-after-bulk-migration"></a>Städa upp efter massmigrering 
När servern slutför sin första synkronisering av namnområdet använder de massrenaterade filerna i datarutan mellanlagringsfilresursen. På bladet Egenskaper för **serverslutpunkt** i Azure-portalen ändras statusen från **Pågår** till **Slutförd**i avsnittet **Offlinedataöverföring** . 

![Skärmbild av bladet Egenskaper för serverslutpunkt, där status och inaktivera kontroller för dataöverföring offline finns](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Nu kan du rensa mellanlagringsresursen för att spara kostnader:

1. När statusen är **Slutförd**på bladet **Egenskaper för serverslutpunkt** väljer du **Inaktivera överföring av offlinedata**.
2. Överväg att ta bort mellanlagringsresursen för att spara kostnader. Mellanlagringsresursen innehåller förmodligen inte fil- och mapp-ACL:er, så det är osannolikt att den är användbar. Skapa en ögonblicksbild [av synkroniseringen av Azure-filresursen för säkerhetskopiering.](storage-snapshots-files.md) Du kan [ställa in Azure Backup för att ta ögonblicksbilder]( ../../backup/backup-afs.md) enligt ett schema.

Inaktivera offlinedataöverföringsläget endast när tillståndet är **slutfört** eller när du vill avbryta på grund av en felkonfiguration. Om du inaktiverar läget under en distribution börjar filerna laddas upp från servern även om mellanlagringsresursen fortfarande är tillgänglig.

> [!IMPORTANT]
> När du har inaktiverat offline-dataöverföringsläget kan du inte aktivera det igen, även om mellanlagringsresursen från massmigreringen fortfarande är tillgänglig.

## <a name="next-steps"></a>Nästa steg
- [Planera för en Azure File Sync-distribution](storage-sync-files-planning.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
