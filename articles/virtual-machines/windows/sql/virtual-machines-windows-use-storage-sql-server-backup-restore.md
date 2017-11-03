---
title: "Använd Azure storage för SQL Server-säkerhetskopiering och återställning | Microsoft Docs"
description: "Lär dig mer om att säkerhetskopiera SQL Server till Azure Storage. Förklarar fördelarna med att säkerhetskopiera SQL-databaser till Azure Storage."
services: virtual-machines-windows
documentationcenter: 
author: MikeRayMSFT
manager: jhubbard
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: d3df6b25fe524c500cf1a1333ac136e8a29d1484
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Använd Azure Storage för SQL Server-säkerhetskopiering och återställning
## <a name="overview"></a>Översikt
Från och med SQL Server 2012 SP1 CU2 kan skriva du nu säkerhetskopieringar för SQL Server direkt till Azure Blob storage-tjänst. Du kan använda den här funktionen för att säkerhetskopiera till och återställa från Azure Blob-tjänsten med en lokal SQL Server-databas eller en SQL Server-databas i en virtuell Azure-dator. Säkerhetskopiering till moln erbjuder fördelarna med tillgänglighet, obegränsad georeplikerad extern lagring och enkel migrering av data till och från molnet. Du kan utfärda instruktioner för säkerhetskopiering eller återställning med hjälp av Transact-SQL eller SMO.

SQL Server 2016 innehåller nya funktioner; Du kan använda [fil – ögonblicksbildsäkerhetskopia](http://msdn.microsoft.com/library/mt169363.aspx) att utföra nästan omedelbar säkerhetskopior och otroligt snabb återställning.

Det här avsnittet förklarar varför du kan välja att använda Azure storage för SQL-säkerhetskopiering och beskriver komponenterna som ingår. Du kan använda resurserna i slutet av artikeln för att komma åt genomgång och ytterligare information för att börja använda den här tjänsten med SQL Server-säkerhetskopiering.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Fördelarna med att använda Azure Blob-tjänsten för SQL Server-säkerhetskopieringar
Det finns flera utmaningar som kan uppstå när du säkerhetskopierar SQL Server. De här utmaningarna inkluderar lagringshantering risken för lagring, åtkomst till extern lagring och maskinvarukonfiguration. Många av dessa problem åtgärdas genom att använda Azure Blob store-tjänsten för SQL Server-säkerhetskopiering. Överväg följande fördelar:

* **Användarvänlighet**: kan vara ett praktiskt att lagra säkerhetskopiorna i Azure BLOB, flexibel och enkel åtkomst till extern alternativet. Skapa extern lagring för dina säkerhetskopieringar för SQL Server kan vara lika enkelt som att ändra din befintliga skript/jobb att använda den **säkerhetskopiering till URL** syntax. Extern lagring bör vara tillräckligt långt från databasplatsen produktion för att förhindra att en enda katastrof som kan påverka både externt och produktion databasen platserna. Genom att välja [geo-replikering i Azure-blobbar](../../../storage/common/storage-redundancy.md), du har ett extra skyddslager vid en katastrof som kan påverka hela region.
* **Säkerhetskopiering Arkiv**: I Azure Blob Storage-tjänst är ett bättre alternativ till alternativet används ofta band för att arkivera säkerhetskopieringar. Lagring på band kan kräva fysiska transport till en extern anläggning och åtgärder för att skydda mediet. Lagra säkerhetskopiorna i Azure Blob Storage tillhandahåller ett ögonblick, hög tillgänglighet, och en varaktiga arkivering alternativet.
* **Hanterad maskinvara**: det finns inga omkostnaderna för maskinvaruhantering av med Azure-tjänster. Azure-tjänster hantera maskinvara och ange geo-replikering för redundans och skydd mot maskinvarufel.
* **Obegränsad lagring**: genom att aktivera en direkt säkerhetskopiering till Azure blob som du har åtkomst till praktiskt taget obegränsade lagring. Du kan också har säkerhetskopiera till en virtuell dator i Azure-disken begränsningar baserat på storleken på datorn. Det finns en gräns för antalet diskar som du kan koppla till en virtuell Azure-dator för säkerhetskopiering. Den här gränsen är 16 diskar för en extra stor instans och färre för mindre instanser.
* **Säkerhetskopiera tillgänglighet**: säkerhetskopior som lagras i Azure BLOB är tillgängliga från valfri plats och när som helst och enkelt kan användas för återställning till en lokal SQL Server eller en annan SQL Server som körs i en Azure-dator, utan att behöva Koppla/frånkoppla databasen eller ladda ned och koppla den virtuella Hårddisken.
* **Kostnad**: betalar bara för den tjänst som används. Kan vara kostnadseffektiva som ett externt och säkerhetskopiering Arkiv-alternativ. Finns det [Azure prisnivå Kalkylatorn](http://go.microsoft.com/fwlink/?LinkId=277060 "Priskalkylatorn"), och [priser för Azure artikel](http://go.microsoft.com/fwlink/?LinkId=277059 "priser artikel") för mer information.
* **Lagring ögonblicksbilder**: när databasfilerna i en Azure blob och du använder SQL Server 2016, kan du använda [fil – ögonblicksbildsäkerhetskopia](http://msdn.microsoft.com/library/mt169363.aspx) att utföra nästan omedelbar säkerhetskopior och otroligt snabb återställning.

Mer information finns i [SQL Server-säkerhetskopiering och återställning med Azure Blob Storage-tjänsten](http://go.microsoft.com/fwlink/?LinkId=271617).

I följande två avsnitt introduceras Azure Blob storage-tjänsten, inklusive de nödvändiga komponenterna för SQL Server. Det är viktigt att förstå komponenterna och deras interaktion för att använda säkerhetskopiering och återställning från Azure Blob storage-tjänst.

## <a name="azure-blob-storage-service-components"></a>Azure Blob Storage tjänstkomponenter
Följande Azure komponenter används när du säkerhetskopierar till Azure Blob storage-tjänst.

| Komponent | Beskrivning |
| --- | --- |
| **Storage-konto** |Lagringskontot är startpunkten för alla lagringstjänster. För att komma åt en Azure Blob Storage-tjänst måste du först skapa ett Azure Storage-konto. Mer information om Azure Blob storage-tjänst finns [hur du använder Azure Blob Storage-tjänsten](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Behållaren** |En behållare grupperar en uppsättning blobbar och kan lagra ett obegränsat antal Blobbar. Om du vill skriva en SQL Server att säkerhetskopiera till Azure Blob-tjänsten måste du ha minst Rotbehållare som skapats. |
| **BLOB** |En fil av valfri typ och storlek. Blobbar är adresserbara via följande URL-format: **https://[storage account].blob.core.windows.net/[container]/[blob]**. Läs mer om Sidblobbar [förstå Block och Sidblobbar](http://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>SQL Server-komponenter
Följande SQL Server-komponenter används när du säkerhetskopierar till Azure Blob storage-tjänst.

| Komponent | Beskrivning |
| --- | --- |
| **URL: EN** |En URL anger en identifierare URI (Uniform Resource) till en unik säkerhetskopia. URL: en används för att ange plats och namn för SQL Server-säkerhetskopia. URL: en måste peka till faktiska blob, inte bara en behållare. Om blob inte finns, skapas. Om en befintlig blob anges SÄKERHETSKOPIERINGEN misslyckas, såvida inte den > med formatalternativ har angetts. Följande är ett exempel på den URL som du anger i kommandot BACKUP: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS rekommenderas men krävs inte. |
| **Autentiseringsuppgifter** |Den information som krävs för att ansluta och autentisera till Azure Blob storage-tjänst lagras som en autentiseringsuppgift.  En SQL Server-autentiseringsuppgift måste skapas för SQL Server för att skriva säkerhetskopieringar på Azure Blob- eller Återställ från den. Mer information finns i [autentiseringsuppgifter för SQL Server](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Om du väljer att kopiera och överföra en säkerhetskopia till Azure Blob storage-tjänst måste du använda en sida blob-datatyp som din lagringsalternativ om du planerar att använda den här filen för återställning. ÅTERSTÄLLNING från en block-blob-datatyp misslyckas med felmeddelandet.
> 
> 

## <a name="next-steps"></a>Nästa steg
1. Skapa ett Azure-konto om du inte redan har ett. Om du utvärderar Azure bör den [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).
2. Sedan gå igenom någon av följande kurser som beskriver hur du skapar ett lagringskonto och utföra en återställning.
   
   * **SQLServer 2014**: [Självstudier: SQL Server 2014 säkerhetskopiering och återställning till Microsoft Azure Blob Storage-tjänst](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [Självstudier: använda tjänsten Microsoft Azure Blob storage med SQL Server 2016-databaser](https://msdn.microsoft.com/library/dn466438.aspx)
3. Granska ytterligare dokumentation från och med [SQL Server-säkerhetskopiering och återställning med Microsoft Azure Blob Storage-tjänsten](https://msdn.microsoft.com/library/jj919148.aspx).

Om du har några problem kan du läsa igenom avsnittet [SQL Server-säkerhetskopiering till URL: en bästa praxis och felsöka](https://msdn.microsoft.com/library/jj919149.aspx).

Andra SQL-Server säkerhetskopiera och återställa alternativ, se [säkerhetskopiering och återställning av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

