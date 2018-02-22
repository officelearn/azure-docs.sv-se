---
title: "Azure Analysis Services-databassäkerhetskopiering och återställning | Microsoft Docs"
description: "Beskriver hur du säkerhetskopierar och återställer en Azure Analysis Services-databas."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: 52db3916392c3ff304311717b12a163213197717
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Säkerhetskopiera tabellmodell databaser i Azure Analysis Services är ungefär desamma som för lokala Analysis Services. Den viktigaste skillnaden är där du vill lagra säkerhetskopiorna. Säkerhetskopieringsfilerna måste sparas till en behållare i en [Azure storage-konto](../storage/common/storage-create-storage-account.md). Du kan använda ett lagringskonto och behållare som du redan har eller de kan skapas när du konfigurerar inställningarna för servern.

> [!NOTE]
> Skapa ett lagringskonto kan resultera i en ny fakturerbar tjänst. Läs mer i [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Säkerhetskopieringar sparas med tillägget abf. För InMemory-tabellmodeller lagras både modelldata och metadata. Endast modellmetadata lagras för DirectQuery-tabellmodeller. Säkerhetskopieringar kan komprimeras och krypteras, beroende på vilka alternativ du väljer. 



## <a name="configure-storage-settings"></a>Konfigurera inställningar för lagring
Innan du säkerhetskopierar, måste du konfigurera inställningarna för servern.


### <a name="to-configure-storage-settings"></a>Så här konfigurerar du inställningar för lagring
1.  I Azure-portalen > **inställningar**, klickar du på **säkerhetskopiering**.

    ![Säkerhetskopieringar i inställningarna](./media/analysis-services-backup/aas-backup-backups.png)

2.  Klicka på **aktiverad**, klicka på **Lagringsinställningar**.

    ![Aktivera](./media/analysis-services-backup/aas-backup-enable.png)

3. Välj ditt lagringskonto eller skapa en ny.

4. Välj en behållare eller skapa en ny.

    ![Välj behållare](./media/analysis-services-backup/aas-backup-container.png)

5. Spara inställningarna för säkerhetskopiering.

    ![Spara inställningar för säkerhetskopiering](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Säkerhetskopiera med hjälp av SSMS

1. Högerklicka på en databas i SSMS, > **säkerhetskopiera**.

2. I **Backup Database** > **säkerhetskopian**, klickar du på **Bläddra**.

3. I den **spara fil som** dialogrutan verifiera mappsökvägen och skriv sedan ett namn för säkerhetskopian. 

4. I den **Backup Database** dialogrutan, Välj alternativ.

    **Tillåt skriva över** – Välj det här alternativet för att skriva över den säkerhetskopierade filer med samma namn. Om inte det här alternativet väljs kan inte du sparar filen ha samma namn som en fil som redan finns på samma plats.

    **Tillämpa komprimering** – Välj det här alternativet om du vill komprimera säkerhetskopian. Komprimerade säkerhetskopieringsfiler spara diskutrymme, men kräver något högre CPU-användning. 

    **Kryptera säkerhetskopian** – Välj det här alternativet för att kryptera säkerhetskopian. Det här alternativet kräver ett användardefinierat lösenord för att skydda filen med säkerhetskopian. Lösenordet går inte att läsa av säkerhetskopierade data för något annat sätt än en återställning. Om du väljer att kryptera säkerhetskopiorna lagra lösenordet på en säker plats.

5. Klicka på **OK** att skapa och spara säkerhetskopian.


### <a name="powershell"></a>PowerShell
Använd [säkerhetskopiering ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet) cmdlet.

## <a name="restore"></a>Återställ
När du återställer, måste din säkerhetskopia vara i storage-konto som du har konfigurerat för din server. Om du behöver flytta en säkerhetskopia från en lokal plats till ditt lagringskonto använder [Microsoft Azure Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) eller [AzCopy](../storage/common/storage-use-azcopy.md) kommandoradsverktyg. 



> [!NOTE]
> Om du återställa från en lokal server, måste du ta bort alla domänanvändare från modellens roller och lägga till dem igen rollerna som Azure Active Directory-användare.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Återställa med hjälp av SSMS

1. Högerklicka på en databas i SSMS, > **återställa**.

2. I den **Backup Database** dialogrutan i **säkerhetskopian**, klickar du på **Bläddra**.

3. I den **Sök efter databasfiler** dialogrutan, Välj den fil som du vill återställa.

4. I **återställningsdatabasen**, välj sedan databasen.

5. Ange alternativ. Säkerhetsalternativ måste matcha de alternativ som du använde när du säkerhetskopierar.


### <a name="powershell"></a>PowerShell

Använd [återställning ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet) cmdlet.


## <a name="related-information"></a>Relaterad information

[Azure storage-konton](../storage/common/storage-create-storage-account.md)  
[Hög tillgänglighet](analysis-services-bcdr.md)     
[Manage Azure Analysis Services](analysis-services-manage.md)
