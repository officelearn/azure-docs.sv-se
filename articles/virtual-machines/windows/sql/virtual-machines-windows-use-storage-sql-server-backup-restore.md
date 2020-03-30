---
title: Så här använder du Azure storage för säkerhetskopiering och återställning av SQL Server | Microsoft-dokument
description: Lär dig hur du säkerhetskopierar SQL Server till Azure Storage. Förklarar fördelarna med säkerhetskopiering av SQL-databaser till Azure Storage.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: cb19dc7262721e672bd3f54b32db9188dad7fee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101896"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Använda Azure Storage för säkerhetskopiering och återställning av SQL Server
## <a name="overview"></a>Översikt
Från och med SQL Server 2012 SP1 CU2 kan du nu skriva SQL Server-säkerhetskopior direkt till Azure Blob-lagringstjänsten. Du kan använda den här funktionen för att säkerhetskopiera till och återställa från Azure Blob-tjänsten med en lokal SQL Server-databas eller en SQL Server-databas på en virtuell Azure-dator. Säkerhetskopiering till moln ger fördelar med tillgänglighet, obegränsad geo-replikerad lagring utanför plats och enkel migrering av data till och från molnet. Du kan utfärda SÄKERHETSKOPIERINGs- eller ÅTERSTÄLLNINGssatser med hjälp av Transact-SQL eller SMO.

SQL Server 2016 introducerar nya funktioner. Du kan använda [säkerhetskopiering av filögonblicksbilder](https://msdn.microsoft.com/library/mt169363.aspx) för att utföra nästan omedelbara säkerhetskopior och otroligt snabba återställningar.

Det här avsnittet förklarar varför du kan välja att använda Azure-lagring för SQL-säkerhetskopior och sedan beskriver de inblandade komponenterna. Du kan använda de resurser som anges i slutet av artikeln för att komma åt genomgångar och ytterligare information för att börja använda den här tjänsten med dina SQL Server-säkerhetskopior.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Fördelar med att använda Azure Blob-tjänsten för SÄKERHETSKOPIERing av SQL Server
Det finns flera utmaningar som du står inför när du säkerhetskopierar SQL Server. Dessa utmaningar inkluderar lagringshantering, risk för lagringsfel, åtkomst till lagring utanför platsen och maskinvarukonfiguration. Många av dessa utmaningar åtgärdas med hjälp av Azure Blob Store-tjänsten för SQL Server-säkerhetskopior. Tänk på följande fördelar:

* **Användarvänlighet:** Att lagra dina säkerhetskopior i Azure-blobbar kan vara ett bekvämt, flexibelt och enkelt att komma åt off-site-alternativ. Det kan vara lika enkelt att skapa lagring på annan plats för dina SQL Server-säkerhetskopior som att ändra dina befintliga skript/jobb så att de använder syntaxen **för SÄKERHETSKOPIERING TILL URL.** Lagring på annan plats bör vanligtvis vara tillräckligt långt från produktionsdatabasens plats för att förhindra en enda katastrof som kan påverka både platser utanför platsen och produktionsdatabasen. Genom att välja att [geo-replikera dina Azure-blobbar](../../../storage/common/storage-redundancy.md)har du ett extra skyddslager i händelse av en katastrof som kan påverka hela regionen.
* **Säkerhetskopieringsarkiv**: Azure Blob Storage-tjänsten erbjuder ett bättre alternativ till det ofta använda bandalternativet för att arkivera säkerhetskopior. Tapelagring kan kräva fysisk transport till en anläggning utanför anläggningen och åtgärder för att skydda media. Att lagra dina säkerhetskopior i Azure Blob Storage är ett omedelbart, högtillgängligt och ett varaktigt arkiveringsalternativ.
* **Hanterad maskinvara:** Det finns ingen overhead för maskinvaruhantering med Azure-tjänster. Azure-tjänster hanterar maskinvaran och tillhandahåller geo-replikering för redundans och skydd mot maskinvarufel.
* **Obegränsad lagring:** Genom att aktivera en direkt säkerhetskopiering till Azure-blobbar har du tillgång till praktiskt taget obegränsad lagring. Alternativt har säkerhetskopiering till en Azure-disk för virtuella datorer gränser baserat på datorns storlek. Det finns en gräns för hur många diskar du kan koppla till en virtuell Azure-dator för säkerhetskopior. Den här gränsen är 16 diskar för en extra stor instans och färre för mindre instanser.
* **Tillgänglighet för säkerhetskopiering:** Säkerhetskopior som lagras i Azure-blobbar är tillgängliga var som helst och när som helst och kan enkelt nås för återställningar till antingen en lokal SQL Server eller en annan SQL Server som körs i en virtuell Azure-dator, utan att databasen behöver bifoga/koppla från eller hämta och bifoga den virtuella hårddisken.
* **Kostnad**: Betala endast för den tjänst som används. Kan vara kostnadseffektivt som en off-site och backup arkiv alternativ. Mer information finns i [Azure-priskalkylatorn](https://go.microsoft.com/fwlink/?LinkId=277060 "Priskalkylator")och [azure-prisartikeln.](https://go.microsoft.com/fwlink/?LinkId=277059 "Artikel om prissättning")
* **Ögonblicksbilder av lagring:** När databasfiler lagras i en Azure-blob och du använder SQL Server 2016 kan du använda [säkerhetskopiering av filögonblicksbilder](https://msdn.microsoft.com/library/mt169363.aspx) för att utföra nästan omedelbara säkerhetskopior och otroligt snabba återställningar.

Mer information finns i [SQL Server Backup and Restore med Azure Blob Storage Service](https://go.microsoft.com/fwlink/?LinkId=271617).

I följande två avsnitt introduceras Azure Blob-lagringstjänsten, inklusive de NÖDVÄNDIGA SQL Server-komponenterna. Det är viktigt att förstå komponenterna och deras interaktion för att framgångsrikt använda säkerhetskopiering och återställning från Azure Blob-lagringstjänsten.

## <a name="azure-blob-storage-service-components"></a>Azure Blob Storage Service-komponenter
Följande Azure-komponenter används vid säkerhetskopiering till Azure Blob-lagringstjänsten.

| Komponent | Beskrivning |
| --- | --- |
| **Lagringskonto** |Lagringskontot är startpunkten för alla lagringstjänster. Skapa först ett Azure Storage-konto om du vill komma åt en Azure Blob Storage-tjänst. Mer information om Azure Blob storage-tjänsten finns i [Så här använder du Azure Blob Storage Service](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Container** |En behållare innehåller en gruppering av en uppsättning blobbar och kan lagra ett obegränsat antal Blobbar. Om du vill skriva en SQL Server-säkerhetskopia till en Azure Blob-tjänst måste du ha minst rotbehållaren skapad. |
| **Blob** |En fil av alla typer och storlekar. Blobbar kan adresseras med följande URL-format: **https://[storage account].blob.core.windows.net/[container]/[blob]**. Mer information om sidblobar finns i [Förstå block- och sidblobar](https://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>SQL Server-komponenter
Följande SQL Server-komponenter används vid säkerhetskopiering till Azure Blob storage-tjänsten.

| Komponent | Beskrivning |
| --- | --- |
| **URL** |En URL anger en URI (Uniform Resource Identifier) till en unik säkerhetskopia. URL:en används för att ange platsen och namnet på sql server-säkerhetskopian. URL:en måste peka på en verklig blob, inte bara en behållare. Om bloben inte finns skapas den. Om en befintlig blob har angetts misslyckas säkerhetskopieringen, såvida inte alternativet > MED FORMAT har angetts. Följande är ett exempel på den URL som du vill ange i kommandot BACKUP: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS rekommenderas men krävs inte. |
| **Autentiseringsuppgift** |Den information som krävs för att ansluta och autentisera till Azure Blob storage-tjänsten lagras som en autentiseringsanslutning.  För att SQL Server ska kunna skriva säkerhetskopior till en Azure Blob eller återställa från den måste en SQL Server-autentiseringsuppgifter skapas. Mer information finns i [SQL Server-autentiseringsuppgifter](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Sql Server 2016 har uppdaterats för att stödja blockblobar. Läs [självstudiekurs: Använda lagringstjänsten för Microsoft Azure Blob med SQL Server 2016-databaser](https://msdn.microsoft.com/library/dn466438.aspx) för mer information.
> 
> 

## <a name="next-steps"></a>Nästa steg
1. Skapa ett Azure-konto om du inte redan har ett. Om du utvärderar Azure bör du överväga den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/free/).
2. Gå sedan igenom en av följande självstudier som går igenom att skapa ett lagringskonto och utföra en återställning.
   
   * **SQL Server 2014**: [Självstudiekurs: SQL Server 2014 Säkerhetskopiering och återställning till Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [Självstudiekurs: Använda microsoft Azure Blob-lagringstjänsten med SQL Server 2016-databaser](https://msdn.microsoft.com/library/dn466438.aspx)
3. Granska ytterligare dokumentation som börjar med [SQL Server Backup and Restore med Microsoft Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx).

Om du har några problem kan du läsa avsnittet [SQL Server Backup till metodtips och felsökning av URL.](https://msdn.microsoft.com/library/jj919149.aspx)

Andra alternativ för säkerhetskopiering och återställning av SQL Server finns i [Säkerhetskopiering och återställning för SQL Server i Virtuella Azure-datorer](virtual-machines-windows-sql-backup-recovery.md).

