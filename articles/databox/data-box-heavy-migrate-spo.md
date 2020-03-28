---
title: Använda Azure Data Box Heavy för att flytta fildelningsinnehåll till SharePoint Online
description: Använd den här självstudien om du vill lära dig hur du migrerar fildelningsinnehåll till Share Point Online med hjälp av din Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: f97ea17551d4415f7ed6371853172cfde30fe4b6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77560056"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Använda Azure Data Box Heavy för att migrera fildelningsinnehållet till SharePoint Online

Använd din Azure Data Box Heavy och SharePoint Migration Tool (SPMT) för att enkelt migrera fildelningsinnehållet till SharePoint Online och OneDrive. Genom att använda Data Box Heavy kan du ta bort beroendet på wan-länken (Wide-area network) för att överföra data.

Microsoft Azure Data Box är en tjänst som låter dig beställa en enhet från Microsoft Azure-portalen. Du kan sedan kopiera terabyte data från servrarna till enheten. När du har skicka tillbaka dem till Microsoft kopieras dina data till Azure. Beroende på storleken på de data du tänker överföra kan du välja mellan:

- [Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) med 35 TB användbar kapacitet per beställning för små till medelstora datauppsättningar.
- [Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) med 80 TB användbar kapacitet per enhet för medelstora till stora datauppsättningar.
- [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-overview) med 770 TB användbar kapacitet per enhet för stora datamängder.

I den här artikeln beskrivs specifikt hur du använder datarutan Tung för att migrera fildelningsinnehållet till SharePoint Online.

## <a name="requirements-and-costs"></a>Krav och kostnader

### <a name="for-data-box-heavy"></a>För Data Box Heavy

- Data Box Heavy är endast tillgängligt för Enterprise Agreement (EA), Cloud solution provider (CSP) eller Azure sponsorerbjudanden. Om din prenumeration inte faller i någon av ovanstående typer kontaktar du Microsoft Support för att uppgradera prenumerationen eller se [Azure-prenumerationspriser](https://azure.microsoft.com/pricing/).
- Det finns en avgift för att använda Data Box Heavy. Se till att granska [Data Box Tung prissättning](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>För SharePoint Online

- Granska [minimikraven för SPMT (SharePoint Migration Tool).](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool)

## <a name="workflow-overview"></a>Översikt över arbetsflöden

Det här arbetsflödet kräver att du utför steg på Azure Data Box Heavy samt på SharePoint Online.
Följande steg gäller din Azure Data Box Heavy.

1. Beställ Azure Data Box Heavy.
2. Ta emot och konfigurera enheten.
3. Kopiera data från din lokala filresurs till mappen för Azure-filer på din enhet.
4. När kopian är klar skickar du tillbaka enheten enligt instruktionerna.
5. Vänta tills data helt har överförts till Azure.

Följande steg gäller SharePoint Online.

6. Skapa en virtuell dator i Azure-portalen och montera Azure-filresursen på den.
7. Installera SPMT-verktyget på den virtuella Azure-datorn.
8. Kör SPMT-verktyget med Azure-filresursen som *källa*.
9. Slutför de sista stegen i verktyget.
10. Verifiera och bekräfta dina data.

## <a name="use-data-box-heavy-to-copy-data"></a>Använda dataruta tung för att kopiera data

Gör så här för att kopiera data till din Data Box Heavy.

1. [Beställ din Data Box Heavy](data-box-heavy-deploy-ordered.md).
2. När du har fått din Data Box Heavy [ställer du in databoxen Tung](data-box-heavy-deploy-set-up.md). Du kabelrönar och konfigurerar båda noderna på enheten.
3. [Kopiera data till Azure Data Box Heavy](data-box-heavy-deploy-copy-data.md). När du kopierar, se till att:

    - Använd bara *mappen StorageAccountName_AzFile* i datarutan Tung för att kopiera data. Detta beror på att du vill att data ska hamna i en Azure-filresurs, inte i blockblobar eller sidblobar.
    - Kopiera filer till en mapp i *StorageAccountName_AzFile* mapp. En undermapp i *StorageAccountName_AzFile* mapp skapar en filresurs. Filer som kopieras direkt till *StorageAccountName_AzFile* mapp misslyckas och överförs som blockblobar. Det här är filresursen som du monterar på den virtuella datorn i nästa steg.
    - Kopiera data till båda noderna i databoxen tung.
3. Kör [Förbered att levereras](data-box-heavy-deploy-picked-up.md#prepare-to-ship) på din enhet. En lyckad förbereda leverans säkerställer en lyckad överföring av filer till Azure.
4. [Returnera enheten](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Verifiera dataöverföringen till Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Använda SPMT för att migrera data

När du har fått en bekräftelse från Azure-datateamet om att datakopian har slutförts fortsätter du med att migrera dina data till SharePoint Online.

För bästa prestanda och anslutning rekommenderar vi att du skapar en virtuell Azure-dator (VM).

1. Logga in på Azure-portalen och [skapa sedan en virtuell dator](../virtual-machines/windows/quick-create-portal.md).
2. [Montera Azure-filresursen på den virtuella datorn](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Hämta Verktyget För migrering av SharePoint](https://spmtreleasescus.blob.core.windows.net/install/default.htm) och installera det på din Virtuella Azure-dator.
4. Starta Verktyget för migrering i SharePoint. Klicka på **Logga in** och ange ditt användarnamn och lösenord för Office 365.
5. När du uppmanas **File share** **Tillfrågas Var är dina data?** Ange sökvägen till din Azure-filresurs där dina data finns.
6. Följ de återstående anvisningarna som vanligt, inklusive din målplats. Mer information finns i [Så här använder du Verktyget för SharePoint-migrering](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - Hur snabbt data används i SharePoint Online påverkas av flera faktorer, oavsett om du redan har dina data i Azure. Att förstå dessa faktorer hjälper dig att planera och maximera effektiviteten i din migrering.  Mer information finns i [SharePoint Online och OneDrive migrationshastighet](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - Det finns en risk att befintliga behörigheter för filer förloras när data migreras till SharePoint Online. Du kan också förlora vissa metadata, till exempel *Skapad efter* och *Datum som ändrats av*.

## <a name="next-steps"></a>Nästa steg

[Beställ din Data Box Heavy](./data-box-heavy-deploy-ordered.md)