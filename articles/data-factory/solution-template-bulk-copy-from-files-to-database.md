---
title: Masskopiera från filer till databas
description: Lär dig hur du använder en lösningsmall för att kopiera data i bulk från Azure Data Lake Storage Gen2 till Azure Synapse Analytics / Azure SQL Database.
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: ae250c7d15801789ad22955845cfa535ed91f2c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75921144"
---
# <a name="bulk-copy-from-files-to-database"></a>Masskopiera från filer till databas

I den här artikeln beskrivs en lösningsmall som du kan använda för att kopiera data i bulk från Azure Data Lake Storage Gen2 till Azure Synapse Analytics / Azure SQL Database.

## <a name="about-this-solution-template"></a>Om den här lösningsmallen

Den här mallen hämtar filer från Azure Data Lake Storage Gen2-källa. Därefter itererar den över varje fil i källan och kopierar filen till måldataarkivet. 

För närvarande stöder den här mallen endast kopiering av data i **AvimitedText-format.** Filer i andra dataformat kan också hämtas från källdatalagret, men kan inte kopieras till måldatalagret.  

Mallen innehåller tre aktiviteter:
- **Hämta metadataaktivitet** hämtar filer från Azure Data Lake Storage Gen2 och skickar dem till efterföljande *ForEach-aktivitet.*
- **ForEach-aktivitet** hämtar filer från aktiviteten *Hämta metadata* och itererar varje fil till *kopieringsaktiviteten.*
- **Kopieringsaktivitet** finns i *ForEach-aktiviteten* för att kopiera varje fil från källdatalagret till måldatalagret.

Mallen definierar följande två parametrar:
- *SourceContainer* är rotbehållarens sökväg där data kopieras från i din Azure Data Lake Storage Gen2. 
- *SourceDirectory* är katalogsökvägen under rotbehållaren där data kopieras från i din Azure Data Lake Storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Så här använder du den här lösningsmallen

1. Gå till **masskopiering från filer till databasmall.** Skapa en **ny** anslutning till käll-Gen2-arkivet. Tänk på att "GetMetadataDataset" och "SourceDataset" är referenser till samma anslutning av källfilarkivet.

    ![Skapa en ny anslutning till källdatalagret](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Skapa en **ny** anslutning till det sink-datalager som du kopierar data till.

    ![Skapa en ny anslutning till sink-datalagret](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Välj **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. Du skulle se en pipeline som skapats enligt följande exempel:

    ![Granska pipelinen](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Om du väljer **Azure Synapse Analytics (tidigare SQL DW)** som datamål i **steg 2** som nämns ovan, måste du ange en anslutning till Azure Blob-lagring för mellanlagring, i enlighet med SQL Data Warehouse Polybase. Som följande skärmbild visar genererar mallen automatiskt en *lagringssökväg* för blob-lagring. Kontrollera om behållaren har skapats efter pipelinekörningen.
        
    ![Polybasinställning](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Välj **Felsökning,** ange **parametrar**och välj sedan **Slutför**.

    ![Klicka på **Felsökning**](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. När pipelinekörningen har slutförts visas resultat som liknar följande exempel:

    ![Granska resultatet](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)