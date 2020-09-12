---
title: Använda SQL Database DACPAC och BACPAC-paket – Azure SQL Edge (för hands version)
description: Lär dig mer om att använda DACPACs och BacPacs i Azure SQL Edge (för hands version)
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 52c8e9586d8ee53cdaac28cb1c48d2927d82c2ed
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462767"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>SQL Database DACPAC-och BACPAC-paket i SQL Edge

Azure SQL Edge (för hands version) är en optimerad Relations databas motor som är avsedd för IoT-och Edge-distributioner. Den bygger på de senaste versionerna av Microsoft SQL Server Database Engine, som ger branschledande prestanda-, säkerhets-och fråge bearbetnings funktioner. Tillsammans med de branschledande Relations databas hanterings funktionerna i SQL Server ger Azure SQL Edge inbyggd strömnings kapacitet för analys i real tid och komplex händelse bearbetning.

Azure SQL Edge tillhandahåller också en inbyggd implementering av SqlPackage.exe som gör att du kan distribuera ett [SQL Database DACPAC-och BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) -paket under distributionen av SQL Edge. SQL Database DACPACs kan distribueras till SQL Edge med SqlPackage-parametern som exponeras via `module twin's desired properties` alternativet för SQL Edge-modulen:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Fält | Beskrivning |
|------|-------------|
| SqlPackage | Azure Blob Storage-URI för *zip* -filen som innehåller SQL Database DAC-eller BACPAC-paketet. Zip-filen kan innehålla både flera DAC-paket eller BACPAC-filer.
| ASAJobInfo | Azure Blob Storage URI för jobbet ASA Edge.

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Använda ett SQL Database DAC-paket med SQL Edge

Följ dessa steg om du vill använda ett SQL Database DAC `(*.dacpac)` -paket eller en BACPAC `(*.bacpac)` -fil med SQL Edge:

1. Skapa/extrahera ett DAC-paket eller exportera en BACPAC-fil med hjälp av mekanismen nedan. 
    - Skapa eller extrahera ett SQL Database DAC-paket. Mer information om hur du skapar ett DAC-paket för en befintlig SQL Server databas finns i [extrahera en DAC från en databas](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) .
    - Exportera ett distribuerat DAC-paket eller en databas. Se [Exportera ett data skikts program](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) för information om hur du skapar en BACPAC-fil för en befintlig SQL Server databas.

2. Zip- `*.dacpac` eller `*.bacpac` File-filen och ladda upp den till ett Azure Blob Storage-konto. Mer information om hur du laddar upp filer till Azure Blob Storage finns i [Ladda upp, ladda ned och lista blobar med Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Generera en signatur för delad åtkomst för zip-filen med hjälp av Azure Portal. Mer information finns i [Delegera åtkomst med signaturer för delad åtkomst (SAS)](../storage/common/storage-sas-overview.md).

4. Uppdatera konfigurationen av SQL Edge-modulen för att inkludera URI för delad åtkomst för DAC-paketet. Gör så här för att uppdatera SQL Edge-modulen:

    1. I Azure Portal går du till din IoT Hub-distribution.

    2. I den vänstra rutan väljer du **IoT Edge**.

    3. På sidan **IoT Edge** söker du efter och väljer den IoT Edge där SQL Edge-modulen har distribuerats.

    4. På sidan **IoT Edge enhets** enhet väljer du **ange modul**.

    5. På sidan **Ange moduler** väljer du **Konfigurera** mot SQL Edge-modulen.

    6. I fönstret **IoT Edge anpassade moduler** väljer du **Ange önskade egenskaper för modulens dubbla**. Uppdatera önskade egenskaper för att inkludera URI för `SQLPackage` alternativet, som visas i följande exempel.

        > [!NOTE]
        > SAS-URI: n i följande JSON är bara ett exempel. Ersätt URI: n med faktisk URI från distributionen.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "<<<SAS URL for the *.zip file containing the dacpac and/or the bacpac files",
                }
            }
        ```

    7. Välj **Spara**.

    8. På sidan **Ange moduler** väljer du **Nästa**.

    9. På sidan **Ange moduler** väljer du **Nästa** och **skickar**sedan.

5. Efter uppdateringen laddas paket filen ned, zippas upp och distribueras mot SQL Edge-instansen.

Vid varje omstart av Azure SQL Edge-behållaren `*.dacpac` laddas och utvärderas fil paketet för ändringar. Om en ny version av DACPAC-filen påträffas distribueras ändringarna till databasen i SQL Edge. BACPAC-filer 

## <a name="next-steps"></a>Nästa steg

- [Distribuera SQL Edge via Azure Portal](deploy-portal.md).
- [Strömma data](stream-data.md)
- [Machine Learning och AI med ONNX i SQL Edge (för hands version)](onnx-overview.md)
