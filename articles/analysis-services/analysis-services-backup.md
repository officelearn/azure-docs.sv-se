---
title: Säkerhetskopiering och återställning av Azure Analysis Services-databasen | Microsoft-dokument
description: I den här artikeln beskrivs hur du säkerhetskopierar och återställer modellmetadata och data från en Azure Analysis Services-databas.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 83da2024ab74b705b45a5891f6b40251020dad31
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408661"
---
# <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Säkerhetskopiering av tabellmodelldatabaser i Azure Analysis Services är ungefär samma som för lokala Analysis Services. Den primära skillnaden är var du lagrar dina säkerhetskopior. Säkerhetskopior måste sparas i en behållare i ett [Azure-lagringskonto](../storage/common/storage-create-storage-account.md). Du kan använda ett lagringskonto och en behållare som du redan har, eller så kan de skapas när du konfigurerar lagringsinställningar för servern.

> [!NOTE]
> Om du skapar ett lagringskonto kan det leda till en ny fakturerbar tjänst. Mer information finns i [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Säkerhetskopior sparas med tillägget .abf. För tabellmodeller i minnet lagras både modelldata och metadata. För Tabellmodeller i DirectQuery lagras endast modellmetadata. Säkerhetskopior kan komprimeras och krypteras, beroende på vilka alternativ du väljer.


## <a name="configure-storage-settings"></a>Konfigurera lagringsinställningar
Innan du säkerhetskopierar måste du konfigurera lagringsinställningar för servern.


### <a name="to-configure-storage-settings"></a>Så här konfigurerar du lagringsinställningar
1.  Klicka på **Säkerhetskopiering**i Azure portal > **Inställningar**.

    ![Säkerhetskopior i Inställningar](./media/analysis-services-backup/aas-backup-backups.png)

2.  Klicka på **Aktiverad**och sedan på **Lagringsinställningar**.

    ![Aktivera](./media/analysis-services-backup/aas-backup-enable.png)

3. Välj ditt lagringskonto eller skapa ett nytt.

4. Markera en behållare eller skapa en ny.

    ![Välj container](./media/analysis-services-backup/aas-backup-container.png)

5. Spara inställningarna för säkerhetskopiering.

    ![Spara inställningar för säkerhetskopiering](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Så här säkerhetskopierar du med hjälp av SSMS

1. Högerklicka på en databas > **säkerhetskopiera**i SSMS.

2. I Säkerhetskopia **av** > **databasen**klickar du på **Bläddra.**

3. Kontrollera mappsökvägen i dialogrutan **Spara som** och skriv sedan ett namn på säkerhetskopian. 

4. Välj alternativ i dialogrutan **Säkerhetskopieringsdatabas.**

    **Tillåt filöverskrivning** - Välj det här alternativet om du vill skriva över säkerhetskopior med samma namn. Om det här alternativet inte är markerat kan filen du sparar inte ha samma namn som en fil som redan finns på samma plats.

    **Använd komprimering** - Välj det här alternativet för att komprimera säkerhetskopian. Komprimerade säkerhetskopieringsfiler sparar diskutrymme, men kräver något högre CPU-användning. 

    **Kryptera säkerhetskopia** - Välj det här alternativet för att kryptera säkerhetskopian. Det här alternativet kräver ett lösenord som tillhandahålls av användaren för att säkra säkerhetskopian. Lösenordet förhindrar läsning av säkerhetskopieringsdata på något annat sätt än en återställningsåtgärd. Om du väljer att kryptera säkerhetskopior lagrar du lösenordet på en säker plats.

5. Klicka på **OK** om du vill skapa och spara säkerhetskopian.


### <a name="powershell"></a>PowerShell
Använd [Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase) cmdlet.

## <a name="restore"></a>Återställ
När du återställer måste säkerhetskopian finnas i det lagringskonto som du har konfigurerat för servern. Om du behöver flytta en säkerhetskopia från en lokal plats till ditt lagringskonto använder du [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) eller kommandoradsverktyget [AzCopy.](../storage/common/storage-use-azcopy.md) 



> [!NOTE]
> Om du återställer från en lokal server måste du ta bort alla domänanvändare från modellens roller och lägga till dem i rollerna som Azure Active Directory-användare.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Så här återställer du med hjälp av SSMS

1. Högerklicka på en databas > **Restore**i SSMS.

2. Klicka **på** **Bläddra**i i säkerhetskopia i säkerhetskopian i dialogrutan **Säkerhetskopieringsdatabas.**

3. Markera den fil som du vill återställa i dialogrutan **Leta efter databasfiler.**

4. Markera databasen i **Återställ databas.**

5. Ange alternativ. Säkerhetsalternativen måste matcha de säkerhetskopieringsalternativ som du använde när du säkerhetskopierade.


### <a name="powershell"></a>PowerShell

Använd [Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase) cmdlet.


## <a name="related-information"></a>Relaterad information

[Azure-lagringskonton](../storage/common/storage-create-storage-account.md)  
[Hög tillgänglighet](analysis-services-bcdr.md)     
[Hantera Azure Analysis Services](analysis-services-manage.md)
