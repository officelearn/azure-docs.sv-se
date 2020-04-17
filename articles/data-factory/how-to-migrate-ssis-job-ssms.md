---
title: Migrera lokala SQL Server Integration Services (SSIS) jobb till Azure Data Factory
description: I den hÃ¤r artikeln beskrivs hur du migrerar SSIS-jobb (SQL Server Integration Services) till Azure Data Factory pipelines/-aktiviteter/utlösare med hjälp av SQL Server Management Studio.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: 6e357e98d6c5190c6dfef675dc1ab9cf30a717c1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455095"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>Migrera SQL Server Agent-jobb till ADF med SSMS

När [du migrerar lokala SQL Server Integration Services (SSIS) arbetsbelastningar till SSIS i ADF](scenario-ssis-migration-overview.md), efter SSIS-paket har migrerats, kan du göra batchmigrering av SQL Server Agent-jobb med jobbstegstypen SQL Server Integration Services Package till Azure Data Factory (ADF) pipelines/aktiviteter/schemautlösare via SQL Server Management Studio (SSMS) **SSIS Job Migration Wizard**.

För valda SQL-agentjobb med tillämpliga jobbstegstyper kan **guiden SSIS-jobbmigrering** i allmänhet:

- karta på lokal SSIS-paketplats till den plats där paketen migreras till, som är tillgängliga för SSIS i ADF.
    > [!NOTE]
    > Paketplats för filsystemet stöds bara.
- migrera tillämpliga jobb med tillämpliga jobbsteg till motsvarande ADF-resurser enligt nedan:

|SQL Agent-jobbobjekt  |ADF-resurs  |Anteckningar|
|---------|---------|---------|
|SQL Agent-jobb|Rörledning     |Pipelinens namn *genereras för \<>*. <br> <br> Inbyggda agentjobb är inte tillämpliga: <li> Underhållsjobb för SSIS-server <li> syspolicy_purge_history <li> collection_set_* <li> mdw_purge_data_* <li> sysutility_*|
|SSIS jobbsteg|Kör SSIS-paketaktivitet|<li> Namnet på aktiviteten \<är stegnamn>. <li> Proxykonto som används i jobbsteget migreras som Windows-autentisering av den här aktiviteten. <li> *Körningsalternativ* utom *Använd 32-bitars körning* som definierats i jobbsteget ignoreras i migreringen. <li> *Verifiering* som definierats i jobbsteget ignoreras i migreringen.|
|schedule      |schema-utlösare        |Namnet på schemautlösaren *genereras för \<schemanamn>*. <br> <br> Nedan ignoreras alternativen i SQL Agent-jobbschemat i migreringen: <li> Andra nivån intervall. <li> *Starta automatiskt när SQL Server Agent startar* <li> *Starta när processorerna blir inaktiva* <li> *veckodag* och *helgdag*<time zone> <br> Nedan visas skillnaderna efter att SQL Agent-jobbschemat har migrerats till ADF-schemautlösaren: <li> ADF Schedule Trigger efterföljande körning är oberoende av körningstillståndet för den föregående utlösta körningen. <li> ADF Schedule Trigger återkommande konfiguration skiljer sig från daglig frekvens i SQL-agent jobb.|

- generera AZURE Resource Manager-mallar (ARM) i den lokala utdatamappen och distribuera till datafabriken direkt eller senare manuellt. Mer information om ADF Resource Manager-mallar finns i [Microsoft.DataFactory-resurstyper](https://docs.microsoft.com/azure/templates/microsoft.datafactory/allversions).

## <a name="prerequisites"></a>Krav

Funktionen som beskrivs i den här artikeln kräver SQL Server Management Studio version 18.5 eller senare. Den senaste versionen av SSMS finns i [Hämta SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).

## <a name="migrate-ssis-jobs-to-adf"></a>Migrera SSIS-jobb till ADF

1. I SSMS i Objektutforskaren väljer du SQL Server Agent, väljer Jobb och högerklickar och väljer **Migrera SSIS-jobb till ADF**.
![Menyn](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Logga in Azure, välj Azure-prenumeration, Data Factory och Integration Runtime. Azure Storage är valfritt, som används i paketplatsmappningssteget om SSIS-jobb som ska migreras har SSIS-filsystempaket.
![Menyn](media/how-to-migrate-ssis-job-ssms/step1.png)

1. Mappa sökvägarna för SSIS-paket och konfigurationsfiler i SSIS-jobb till målsökvägar där migrerade pipelines kan komma åt. I det här mappningssteget kan du:

    1. Markera en källmapp och lägg sedan **till mappning**.
    1. Uppdatera sökvägen till källmappen. Giltiga sökvägar är mappsökvägar eller överordnade mappsökvägar för paket.
    1. Uppdatera sökvägen till målmappen. Standard är relativ sökväg till standardlagringskontot, som väljs i steg 1.
    1. Ta bort en markerad mappning via **Ta bort mappning**.
![steg2](media/how-to-migrate-ssis-job-ssms/step2.png)
![steg2-1](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Välj tillämpliga jobb att migrera och konfigurera inställningarna för motsvarande *utförda SSIS-paketaktivitet*.

    - *Standardinställning*, gäller för alla markerade steg som standard. Mer information om varje egenskap finns på *fliken Inställningar* för aktiviteten [Kör SSIS-paket](how-to-invoke-ssis-package-ssis-activity.md) när paketplatsen är *Filsystem (Paket)*.
    ![steg3-1](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Steginställning*, konfigurera inställningen för ett valt steg.
        
        **Använd standardinställning:** standard är markerat. Avmarkera för att konfigurera inställningen för valt steg.  
        Mer information om andra egenskaper finns på *fliken Inställningar* för aktiviteten [Kör SSIS-paket](how-to-invoke-ssis-package-ssis-activity.md) när paketplatsen är *Filsystem (Paket)*.
    ![steg3-2](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. Generera och distribuera ARM-mall.
    1. Markera eller mata in utdatasökvägen för ARM-mallarna för de migrerade ADF-pipelines. Mappen skapas automatiskt om det inte finns.
    2. Välj alternativet **Distribuera ARM-mallar till din datafabrik:**
        - Standard är omarkerat. Du kan distribuera genererade ARM-mallar senare manuellt.
        - Välj det här om du vill distribuera genererade ARM-mallar till datafabriken direkt.
    ![steg4](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Migrera och kontrollera sedan resultaten.
![steg5](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>Nästa steg

[Kör och övervaka pipeline](how-to-invoke-ssis-package-ssis-activity.md)
