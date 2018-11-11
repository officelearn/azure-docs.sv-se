---
title: Hur du använder Azure storage för SQL Server-säkerhetskopiering och återställning | Microsoft Docs
description: Lär dig mer om att säkerhetskopiera SQL Server till Azure Storage. Förklarar fördelarna med att säkerhetskopiera SQL-databaser till Azure Storage.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: 0fdf768008161fbb72e48dae937a4172222dbb63
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239754"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Använda Azure Storage för SQL Server-säkerhetskopiering och återställning
## <a name="overview"></a>Översikt
Från och med SQL Server 2012 SP1 CU2 kan skriva du SQL Server säkerhetskopieras direkt till Azure Blob storage-tjänsten. Du kan använda den här funktionen för att säkerhetskopiera till och återställa från Azure Blob-tjänsten med en lokal SQL Server-databas eller en SQL Server-databas i Azure-datorer. Säkerhetskopieringen till molnet ger fördelarna med tillgänglighet, obegränsad georeplikerad annan lagring och enkel migrering av data till och från molnet. Du kan utfärda BACKUP eller RESTORE-uttryck med hjälp av Transact-SQL eller SMO.

SQL Server 2016 innehåller nya funktioner; Du kan använda [av säkerhetskopiering](https://msdn.microsoft.com/library/mt169363.aspx) att utföra stort sett ögonblickliga säkerhetskopior och otroligt snabba återställningar.

Det här avsnittet förklarar varför du kan välja att använda Azure storage för SQL-säkerhetskopior och beskriver komponenterna som ingår. Du kan använda de resurser som finns i slutet av artikeln för att komma åt genomgångar och ytterligare information för att börja använda den här tjänsten med SQL Server-säkerhetskopieringar.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Fördelarna med att använda Azure Blob-tjänsten för SQL Server-säkerhetskopior
Det finns flera utmaningar som du stöter på när du säkerhetskopierar SQL Server. De här utmaningarna med att inkludera lagringshantering, risken för fel för lagring, åtkomst till externa lagrings- och maskinvarukonfiguration. Många av dessa problem åtgärdas genom att använda Azure Blob store-tjänsten för SQL Server-säkerhetskopieringar. Överväg följande fördelar:

* **Användarvänlighet**: lagra säkerhetskopiorna i Azure-blobs kan vara ett enkelt, flexibelt och enkelt att komma åt externa alternativet. Skapa lagerplats för SQL Server-säkerhetskopiering kan vara lika enkelt som att ändra dina befintliga skript/jobb att använda den **säkerhetskopiering till URL** syntax. Lagerplats bör vara tillräckligt långt från produktion databasens plats att förhindra att en enda katastrof som kan påverka både databasplatser på annan plats och produktion. Genom att välja att [geo-replikera din Azure-blobbar](../../../storage/common/storage-redundancy.md), du har ett extra skyddslager vid ett haveri som kan påverka hela regionen.
* **Säkerhetskopiering Arkiv**: The Azure Blob Storage-tjänsten är ett bättre alternativ till ofta använda band kan arkivera säkerhetskopior. Lagring på band kan kräva fysiska transport till en annan resurs och åtgärder för att skydda mediet. Lagra säkerhetskopiorna i Azure Blob Storage tillhandahåller ett ögonblick med hög tillgänglighet och en durable arkivering alternativet.
* **Hanterade maskinvara**: det finns inga kostnader för maskinvara med Azure-tjänster. Azure-tjänster som hanterar maskinvaran och ange geo-replikering för redundans och skydd mot maskinvarufel.
* **Obegränsad lagring**: genom att aktivera en direkt säkerhetskopiering till Azure blob som du har åtkomst till nästan obegränsad lagring. Du kan också har säkerhetskopiera till en virtuell dator i Azure-disk gränser som baseras på storleken på datorn. Det finns en gräns för antalet diskar som du kan koppla till en Azure virtuell dator för säkerhetskopiering. Den här gränsen är 16 diskar för en extra stor instans och färre för mindre instanser.
* **Säkerhetskopiera tillgänglighet**: säkerhetskopior som lagras i Azure BLOB-objekt är tillgängliga från var som helst och när som helst och enkelt kan kommas åt för återställningar till en lokal SQL Server eller en annan SQL Server som körs i en Azure-dator, utan att behöva Koppla/frånkoppla databasen eller ladda ned och koppla den virtuella Hårddisken.
* **Kostnaden**: betala bara för den tjänst som används. Vara kostnadseffektivt som ett alternativ för arkivet på annan plats och säkerhetskopiering. Finns i den [Azures priskalkylator](https://go.microsoft.com/fwlink/?LinkId=277060 "Priskalkylatorn"), och [priser för Azure artikeln](https://go.microsoft.com/fwlink/?LinkId=277059 "priser artikeln") för mer information.
* **Ögonblicksbilder av lagring**: när databasfilerna i en Azure-blob och du använder SQL Server 2016, kan du använda [av säkerhetskopiering](https://msdn.microsoft.com/library/mt169363.aspx) att utföra stort sett ögonblickliga säkerhetskopior och otroligt snabba återställningar.

Mer information finns i [SQL Server-säkerhetskopiering och återställning med Azure Blob Storage-tjänsten](https://go.microsoft.com/fwlink/?LinkId=271617).

Följande två avsnitt introducerar Azure Blob storage-tjänsten, inklusive de nödvändiga komponenterna för SQL Server. Det är viktigt att förstå komponenterna och samspelet mellan att använda säkerhetskopiering och återställning från Azure Blob storage-tjänsten.

## <a name="azure-blob-storage-service-components"></a>Komponenterna i Azure Blob Storage-tjänsten
Följande Azure-komponenterna används när du säkerhetskopierar till Azure Blob storage-tjänsten.

| Komponent | Beskrivning |
| --- | --- |
| **Lagringskonto** |Lagringskontot är startpunkten för alla lagringstjänster. Om du vill få åtkomst till en Azure Blob Storage-tjänst måste du först skapa ett Azure Storage-konto. Läs mer om Azure Blob storage-tjänsten, [hur du använder Azure Blob Storage-tjänsten](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Behållare** |En behållare grupperar en uppsättning blobar och kan lagra ett obegränsat antal Blobbar. Om du vill skriva en SQL Server säkerhetskopiering till en Azure Blob-tjänst, du måste ha minst Rotbehållare skapas. |
| **Blob** |En fil av valfri typ och storlek. BLOB-lagring är adresserbara via följande URL-format: **https://[storage account].blob.core.windows.net/[container]/[blob]**. Mer information om Sidblobar finns [Understanding Block- och Sidblobbar](https://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>SQL Server-komponenter
Följande SQL Server-komponenter som används när du säkerhetskopierar till Azure Blob storage-tjänsten.

| Komponent | Beskrivning |
| --- | --- |
| **URL** |En URL anger en identifierare URI (Uniform Resource) till en unik säkerhetskopia. URL: en används för att ange plats och namn för SQL Server-säkerhetskopia. URL: en måste peka på en faktiska blob, inte bara en behållare. Om blobben som inte finns skapas den. Om en befintlig blob har angetts, inte det går att säkerhetskopiera, såvida inte den > med formatalternativ har angetts. Följande är ett exempel på den URL som du anger i BACKUP-kommandot: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS rekommenderas men krävs inte. |
| **Autentiseringsuppgifter** |Den information som krävs för att ansluta och autentisera till tjänsten Azure Blob storage lagras som en autentiseringsuppgift.  En SQL Server-autentiseringsuppgift måste skapas för SQL Server för att skriva säkerhetskopior till en Azure Blob eller Återställ från den. Mer information finns i [SQL Server-Autentiseringsuppgiften](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Om du väljer att kopiera och ladda upp en säkerhetskopia till Azure Blob storage-tjänsten, måste du använda en blob sidtyp som din lagringsalternativ om du planerar att använda den här filen för återställningsåtgärder. ÅTERSTÄLLNING från en block blob-typ misslyckas med ett fel.
> 
> 

## <a name="next-steps"></a>Nästa steg
1. Skapa ett Azure-konto om du inte redan har ett. Om du utvärderar Azure kan du överväga att den [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).
2. Gå sedan igenom något av följande självstudier som beskriver hur du skapar ett lagringskonto och utför en återställning.
   
   * **SQLServer 2014**: [självstudie: SQL Server 2014-säkerhetskopiering och återställning till Microsoft Azure Blob Storage-tjänsten](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [självstudie: använda Microsoft Azure Blob storage-tjänsten med SQL Server 2016-databaser](https://msdn.microsoft.com/library/dn466438.aspx)
3. Granska ytterligare dokumentation från och med [SQL Server-säkerhetskopiering och återställning med Microsoft Azure Blob Storage-tjänsten](https://msdn.microsoft.com/library/jj919148.aspx).

Om du har några problem, läser du igenom avsnittet [SQL Server-säkerhetskopiering till URL: en metodtips och felsökning](https://msdn.microsoft.com/library/jj919149.aspx).

För andra SQL-Server för säkerhetskopiering och återställningsalternativ, se [säkerhetskopiering och återställning av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

