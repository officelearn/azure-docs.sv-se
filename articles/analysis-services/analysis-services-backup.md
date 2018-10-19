---
title: Azure Analysis Services-databassäkerhetskopiering och återställning | Microsoft Docs
description: Beskriver hur du säkerhetskopierar och återställer en Azure Analysis Services-databas.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6e382e57473cd1461b97e742546a3b621f2fd4c8
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426710"
---
# <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Säkerhetskopiera tabellmodell databaser i Azure Analysis Services är ungefär samma sätt som för den lokala Analysis Services. Den viktigaste skillnaden är där du kan lagra säkerhetskopiorna. Säkerhetskopiera filer måste sparas till en behållare i ett [Azure storage-konto](../storage/common/storage-create-storage-account.md). Du kan använda en lagringskontot och behållaren som du redan har och de kan skapas när du konfigurerar inställningarna för din server.

> [!NOTE]
> Skapa ett lagringskonto kan resultera i en ny fakturerbar tjänst. Mer information finns i [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Säkerhetskopior sparas med ett abf-tillägg. För InMemory-tabellmodeller lagras både modelldata och metadata. För DirectQuery-tabellmodeller lagras endast modellmetadata. Säkerhetskopieringar kan komprimeras och krypteras, beroende på vilka alternativ du väljer. 



## <a name="configure-storage-settings"></a>Konfigurera lagringsinställningar för
Innan du säkerhetskopierar, måste du konfigurera inställningarna för din server.


### <a name="to-configure-storage-settings"></a>Konfigurera lagringsinställningarna
1.  I Azure portal > **inställningar**, klickar du på **Backup**.

    ![Säkerhetskopior i inställningar](./media/analysis-services-backup/aas-backup-backups.png)

2.  Klicka på **aktiverad**, klicka sedan på **Lagringsinställningar**.

    ![Aktivera](./media/analysis-services-backup/aas-backup-enable.png)

3. Välj ditt lagringskonto eller skapa en ny.

4. Välj en behållare eller skapa en ny.

    ![Välj container](./media/analysis-services-backup/aas-backup-container.png)

5. Spara inställningarna för säkerhetskopiering.

    ![Spara inställningar för säkerhetskopiering](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Att säkerhetskopiera med hjälp av SSMS

1. I SSMS högerklickar du på en databas > **säkerhetskopiera**.

2. I **Backup Database** > **säkerhetskopian**, klickar du på **Bläddra**.

3. I den **spara fil som** dialogrutan Kontrollera sökvägen till mappen och skriv sedan ett namn för den säkerhetskopiera filen. 

4. I den **Backup Database** dialogrutan, Välj alternativ.

    **Tillåt skriva över** – Välj det här alternativet om du vill skriva över säkerhetskopior med samma namn. Om det här alternativet inte väljs, kan inte du sparar filen ha samma namn som en fil som redan finns på samma plats.

    **Gäller komprimering** – Välj det här alternativet för att komprimera säkerhetskopian. Komprimerade säkerhetskopieringsfiler spara diskutrymme, men kräver något högre CPU-användning. 

    **Kryptera säkerhetskopian** – Välj det här alternativet för att kryptera säkerhetskopian. Det här alternativet kräver ett lösenord som användaren har angett att skydda filen med säkerhetskopian. Lösenordet går inte att läsa av säkerhetskopieringsdata annat sätt än en återställningsåtgärd. Om du väljer att kryptera säkerhetskopior, lagra lösenordet på en säker plats.

5. Klicka på **OK** att skapa och spara säkerhetskopian.


### <a name="powershell"></a>PowerShell
Använd [Backup ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet) cmdlet.

## <a name="restore"></a>Återställ
När du återställer, måste din säkerhetskopia vara i storage-konto som du har konfigurerat för din server. Om du vill flytta en säkerhetskopia från en lokal plats till ditt lagringskonto kan använda [Microsoft Azure Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) eller [AzCopy](../storage/common/storage-use-azcopy.md) kommandoradsverktyget. 



> [!NOTE]
> Om du återställa från en lokal server, måste du ta bort alla domänanvändare från modellens roller och lägga till dem till rollerna som Azure Active Directory-användare.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Återställa med hjälp av SSMS

1. I SSMS högerklickar du på en databas > **återställa**.

2. I den **Backup Database** dialogrutan i **säkerhetskopian**, klickar du på **Bläddra**.

3. I den **Leta upp databasfiler** dialogrutan, Välj den fil som du vill återställa.

4. I **återställningsdatabasen**, väljer du databasen.

5. Ange alternativ. Säkerhetsalternativ måste matcha de alternativ som du använde när du säkerhetskopierar.


### <a name="powershell"></a>PowerShell

Använd [återställning ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet) cmdlet.


## <a name="related-information"></a>Relaterad information

[Azure storage-konton](../storage/common/storage-create-storage-account.md)  
[Hög tillgänglighet](analysis-services-bcdr.md)     
[Hantera Azure Analysis Services](analysis-services-manage.md)
