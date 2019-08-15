---
title: Säkerhets kopiering och återställning av Azure Analysis Services databasen | Microsoft Docs
description: Beskriver hur du säkerhetskopierar och återställer en Azure Analysis Services databas.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2e751d45e4b76852426d454f8d29196c01396504
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932460"
---
# <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Att säkerhetskopiera tabell modell databaser i Azure Analysis Services är ungefär samma som för lokala Analysis Services. Den främsta skillnaden är den plats där du lagrar dina säkerhets kopior. Säkerhetskopierade filer måste sparas i en behållare i ett [Azure Storage-konto](../storage/common/storage-create-storage-account.md). Du kan använda ett lagrings konto och en behållare som du redan har, eller så kan de skapas när du konfigurerar lagrings inställningar för servern.

> [!NOTE]
> Att skapa ett lagrings konto kan resultera i en ny fakturerbar tjänst. Mer information finns i [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Säkerhets kopior sparas med tillägget. ABF. För i-minnes tabell modeller lagras både modell data och metadata. För DirectQuery-tabell modeller lagras bara modellens metadata. Säkerhets kopior kan komprimeras och krypteras beroende på vilka alternativ du väljer.


## <a name="configure-storage-settings"></a>Konfigurera lagrings inställningar
Innan du säkerhetskopierar måste du konfigurera lagrings inställningarna för servern.


### <a name="to-configure-storage-settings"></a>Konfigurera lagrings inställningar
1.  Klicka på **säkerhetskopiera**i Azure Portal > **Inställningar**.

    ![Säkerhets kopieringar i inställningar](./media/analysis-services-backup/aas-backup-backups.png)

2.  Klicka på **aktive rad**och sedan på **lagrings inställningar**.

    ![Aktivera](./media/analysis-services-backup/aas-backup-enable.png)

3. Välj ditt lagrings konto eller skapa ett nytt.

4. Välj en behållare eller skapa en ny.

    ![Välj container](./media/analysis-services-backup/aas-backup-container.png)

5. Spara inställningarna för säkerhets kopiering.

    ![Spara inställningar för säkerhets kopiering](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Säkerhetskopiera

### <a name="to-backup-by-using-ssms"></a>Säkerhetskopiera med hjälp av SSMS

1. I SSMS högerklickar du på en databas > **säkerhets kopiering**.

2. I > säkerhets kopierings databasens**säkerhets kopia**klickar du på **Bläddra**.

3. I dialog rutan **Spara filen som** kontrollerar du mappsökvägen och skriver sedan ett namn för säkerhets kopian. 

4. I dialog rutan **Säkerhetskopiera databas** väljer du alternativ.

    **Tillåt att filen skrivs över** – Välj det här alternativet om du vill skriva över säkerhetskopierade filer med samma namn. Om det här alternativet inte är markerat kan filen du sparar inte ha samma namn som en fil som redan finns på samma plats.

    **Tillämpa komprimering** – Välj det här alternativet om du vill komprimera säkerhets kopierings filen. Komprimerade säkerhets kopior sparar disk utrymme, men kräver lite högre processor belastning. 

    **Kryptera säkerhets kopierings fil** – Välj det här alternativet om du vill kryptera säkerhets kopian. För det här alternativet krävs ett användar lösen ord för att skydda säkerhets kopierings filen. Lösen ordet förhindrar läsning av säkerhets kopierings data på annat sätt än en återställnings åtgärd. Om du väljer att kryptera säkerhets kopior lagrar du lösen ordet på en säker plats.

5. Klicka på **OK** för att skapa och spara säkerhets kopian.


### <a name="powershell"></a>PowerShell
Använd [backup-databas-](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase) cmdlet.

## <a name="restore"></a>Återställ
När du återställer måste säkerhets kopian finnas i det lagrings konto som du har konfigurerat för servern. Om du behöver flytta en säkerhets kopia från en lokal plats till ditt lagrings konto använder du [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) eller kommando rads verktyget [AzCopy](../storage/common/storage-use-azcopy.md) . 



> [!NOTE]
> Om du återställer från en lokal server måste du ta bort alla domän användare från modellens roller och lägga tillbaka dem till rollerna som Azure Active Directory användare.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Återställa med hjälp av SSMS

1. I SSMS högerklickar du på en databas > **återställning**.

2. I dialog rutan **Säkerhetskopiera databas** i **säkerhets kopia**klickar du på **Bläddra**.

3. I dialog rutan **Leta upp databasfiler** väljer du den fil som du vill återställa.

4. I **restore Database**väljer du databasen.

5. Ange alternativ. Säkerhets alternativen måste matcha de säkerhets kopierings alternativ som du använde när du säkerhetskopierade.


### <a name="powershell"></a>PowerShell

Använd [restore-Database-](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase) cmdlet.


## <a name="related-information"></a>Relaterad information

[Azure Storage-konton](../storage/common/storage-create-storage-account.md)  
[Hög tillgänglighet](analysis-services-bcdr.md)     
[Hantera Azure Analysis Services](analysis-services-manage.md)
