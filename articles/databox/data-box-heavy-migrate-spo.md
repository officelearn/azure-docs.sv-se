---
title: Använda Azure Data Box tung för att migrera filinnehåll för resursen till SharePoint Online | Microsoft Docs
description: Använd den här självstudien Lär dig hur du migrerar du innehåll från filen resursen till dela punkt Online med din Azure Data Box tung
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 06/05/2019
ms.author: alkohli
ms.openlocfilehash: 1c432ee5851115e029b55722b6b238b4672e8345
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446720"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Använda Azure Data Box tung för att migrera din resurs filinnehåll till SharePoint Online

Använda din Azure Data Box stor och SharePoint Migration Tool (SPMT) för att migrera enkelt dina Dela innehåll till SharePoint Online och OneDrive. Genom att använda Data Box tung kan du ta bort beroendet i Wide area network (WAN) länken att överföra data.

Microsoft Azure Data Box är en tjänst som kan du beställa en enhet från Microsoft Azure-portalen. Du kan sedan kopiera terabyte data från dina servrar till enheten. Dina data har kopierats till Azure efter leverans till Microsoft. Beroende på storleken på data som du ska överföra kan välja du mellan:

- [Data Box-Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) med 35 TB användbar kapacitet per beställning för små till medelstora datauppsättningar.
- [Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) med 80 TB användbar kapacitet per enhet för medelstora till stora datauppsättningar.
- [Data Box tung](https://docs.microsoft.com/azure/databox/data-box-heavy-overview) med 770 TB användbar kapacitet per enhet för stora datauppsättningar. Data Box tung förhandsvisas just nu.

Den här artikeln handlar specifikt om hur du använder Data Box-tung för att migrera din resurs filinnehåll till SharePoint Online.

## <a name="requirements-and-costs"></a>Krav och kostnader

### <a name="for-data-box-heavy"></a>För Data Box-aktiverat

- Data Box tung är bara tillgängligt för Enterprise Agreement (EA), leverantörer av molnlösningar (CSP) eller Azure-sponsring erbjuder. Kontakta Microsoft Support om du vill uppgradera din prenumeration eller se om din prenumeration inte faller inom någon av ovanstående typer, [priser för Azure-prenumeration](https://azure.microsoft.com/pricing/).
- Det finns en avgift för att använda Data Box tung. Se till att granska den [priser för Data Box tung](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>För SharePoint Online

- Granska den [minimikrav för SharePoint Migration Tool (SPMT)](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>Översikt över arbetsflödet

Det här arbetsflödet måste du utföra stegen på Azure Data Box tung samt på SharePoint-online.
Följande steg är relaterade till din Azure Data Box tung.

1. Ordning Azure Data Box-aktiverat.
2. Ta emot och konfigurera din enhet.
3. Kopieringsdata från din lokala fil dela till mappen för Azure Files på din enhet.
4. När kopieringen är klar, kan du skicka enheten tillbaka enligt anvisningarna.
5. Vänta tills data till helt Överför till Azure.

Följande steg relatera till SharePoint-online.

6. Skapa en virtuell dator i Azure-portalen och montera Azure-filresursen på den.
7. Installera verktyget SPMT virtuella Azure-datorn.
8. Kör verktyget SPMT med hjälp av Azure-filresursen som den *källa*.
9. Slutför de sista stegen i verktyget.
10. Kontrollera och bekräfta dina data.

## <a name="use-data-box-heavy-to-copy-data"></a>Använd Data Box tung för att kopiera data

Vidta följande steg för att kopiera data till din Data Box tung.

1. [Ordna din Data Box-aktiverat](data-box-heavy-deploy-ordered.md).
2. När du har fått din Data Box stor, [konfigurera Data Box-tung](data-box-heavy-deploy-set-up.md). Du kabel och konfigurera båda noderna på din enhet.
3. [Kopiera data till Azure Data Box tung](data-box-heavy-deploy-copy-data.md). När du kopierar, se till att:

    - Använd bara den *AzureFile* mapp i Box tung Data att kopiera data. Det beror på att du vill att data ska hamnar i en Azure-filresurs, inte i blockblob-objekt eller sidblobar.
    - Kopiera filer till en mapp i *AzureFile* mapp. En undermapp i *AzureFile* mappen skapar en filresurs. Filer som kopierats direkt till *AzureFile* mappen misslyckas och kan laddas upp som blockblobar. Det här är filresursen som du ska montera på den virtuella datorn i nästa steg.
    - Kopiera data till båda noderna i din Data Box tung.
3. Kör [Förbered för att skicka](data-box-heavy-deploy-picked-up.md#prepare-to-ship) på din enhet. Ett lyckat Förbered för att skicka säkerställer en lyckad överföring av filer till Azure.
4. [Returnera enheten](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Kontrollera ladda upp data till Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Använd SPMT för att migrera data

När du har fått bekräftelse från Azure data teamet att kopiera dina data har slutförts kan du nu fortsätta att migrera data till SharePoint Online.

För bästa prestanda och anslutning rekommenderar vi att du skapar en Azure virtuell dator (VM).

1. Logga in på Azure-portalen och sedan [skapa en virtuell dator](../virtual-machines/windows/quick-create-portal.md).
2. [Montera Azure-filresursen på den virtuella datorn](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Ladda ned SharePoint Migreringsverktyget](https://spmtreleasescus.blob.core.windows.net/install/default.htm) och installera den på din Azure-VM.
4. Starta verktyget SharePoint-migrering. Klicka på **logga in** och ange ditt Office 365-användarnamn och lösenord.
5. När du uppmanas **var är dina data?** väljer **filresurs**. Ange sökvägen till din Azure-filresurs där dina data finns.
6. Följ återstående anvisningar som vanligt, inklusive din målplatsen. Mer information går du till [hur du använder SharePoint-Migreringsverktyget](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - Den hastighet med vilken data matas in i SharePoint Online påverkas av flera faktorer, oavsett om du har dina data redan i Azure. Förstå dessa faktorer hjälper dig att planera och maximera effektiviteten av migreringen.  Mer information går du till [SharePoint Online och OneDrive migrering hastighet](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - Det finns en risk att förlora befintliga behörigheter för filer när du migrerar data till SharePoint Online. Du kan också förlora vissa metadata, till exempel *skapats av* och *ändrades av*.

## <a name="next-steps"></a>Nästa steg

[Ordna din Data Box-aktiverat](./data-box-heavy-deploy-ordered.md)