---
title: Så här använder du Azure Storage för SQL Server säkerhets kopiering och återställning | Microsoft Docs
description: Lär dig hur du säkerhetskopierar SQL Server till Azure Storage. Förklarar fördelarna med att säkerhetskopiera SQL-databaser till Azure Storage.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "70101896"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Använd Azure Storage för säkerhets kopiering och återställning av SQL Server
## <a name="overview"></a>Översikt
Från och med SQL Server 2012 SP1-CU2 kan du nu skriva SQL Server säkerhets kopieringar direkt till Azure Blob Storage-tjänsten. Du kan använda den här funktionen för att säkerhetskopiera till och återställa från Azure-Blob Service med en lokal SQL Server-databas eller en SQL Server-databas på en virtuell Azure-dator. Säkerhets kopiering till molnet erbjuder fördelar med tillgänglighet, obegränsad geo-replikerad lagring utanför platsen och enkel migrering av data till och från molnet. Du kan utfärda säkerhets kopierings-eller återställnings instruktioner med hjälp av Transact-SQL eller SMO.

SQL Server 2016 introducerar nya funktioner; Du kan använda [säkerhets kopiering med fil-och ögonblicks bilder](https://msdn.microsoft.com/library/mt169363.aspx) för att utföra nästan momentan säkerhets kopieringar och otroligt snabba återställningar.

Det här avsnittet förklarar varför du kan välja att använda Azure Storage för SQL-säkerhetskopieringar och beskriver de komponenter som ingår. Du kan använda de resurser som finns i slutet av artikeln för att komma åt genom gångar och ytterligare information för att börja använda tjänsten med dina SQL Server säkerhets kopior.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Fördelar med att använda Azure Blob service för SQL Server säkerhets kopieringar
Det finns flera utmaningar som du möter när du säkerhetskopierar SQL Server. Dessa utmaningar omfattar lagrings hantering, risk för lagrings problem, åtkomst till lagring utanför platsen och maskin varu konfiguration. Många av dessa utmaningar åtgärdas med hjälp av Azure Blob Store-tjänsten för SQL Server säkerhets kopieringar. Tänk på följande fördelar:

* **Enkel användning**: att lagra dina säkerhets kopior i Azure-blobbar kan vara ett bekvämt, flexibelt och enkelt sätt att få åtkomst till alternativ för andra platser. Det kan vara lika enkelt att skapa lagrings platser för dina SQL Server säkerhets kopior som att ändra befintliga skript/jobb för att använda syntaxen för **säkerhets kopiering till URL** . Lagring på annan plats bör normalt vara tillräckligt långt från produktions databasens plats för att förhindra en enstaka katastrof som kan påverka både platser utanför platsen och produktions databasen. Genom att välja att [geo-replikera dina Azure-blobbar](../../../storage/common/storage-redundancy.md)har du ett extra skydds lager i händelse av en katastrof som kan påverka hela regionen.
* **Säkerhets kopierings Arkiv**: Azure Blob Storage-tjänsten erbjuder ett bättre alternativ till det band alternativ som ofta används för att arkivera säkerhets kopior. Band lagring kan kräva fysisk transport till en lokal plats och åtgärder för att skydda mediet. Genom att lagra dina säkerhets kopior i Azure Blob Storage får du en snabb och mycket tillgänglig och ett alternativ för varaktig arkivering.
* **Hanterad maskin vara**: det finns ingen kapacitet för maskin varu hantering med Azure-tjänster. Azure-tjänster hanterar maskin varan och tillhandahåller geo-replikering för redundans och skydd mot maskin varu problem.
* **Obegränsad lagring**: genom att aktivera en direkt säkerhets kopiering till Azure-blobbar har du åtkomst till praktiskt taget obegränsad lagring. Ett annat sätt är att säkerhetskopiera till en virtuell Azure-dator disk baserat på datorns storlek. Det finns en gräns för hur många diskar du kan ansluta till en virtuell Azure-dator för säkerhets kopieringar. Den här gränsen är 16 diskar för en extra stor instans och färre för mindre instanser.
* **Tillgänglighet för säkerhets kopiering**: säkerhets kopior som lagras i Azure-blobbar är tillgängliga överallt och när som helst och kan enkelt nås för återställningar till antingen en lokal SQL Server eller en annan SQL Server som körs på en virtuell Azure-dator, utan att det krävs någon databas anslutning/från koppling eller hämtning och anslutning till den virtuella hård disken.
* **Kostnad**: betala endast för den tjänst som används. Kan vara kostnads effektivt som ett arkiv med säkerhets kopiering utanför platsen och. Se [pris Kalkylatorn för Azure](https://go.microsoft.com/fwlink/?LinkId=277060 "Priskalkylator")och [pris artikeln för Azure](https://go.microsoft.com/fwlink/?LinkId=277059 "Pris artikel") för mer information.
* **Ögonblicks bilder av lagring**: när databasfiler lagras i en Azure-blob och du använder SQL Server 2016 kan du använda [säkerhets kopiering för fil-och ögonblicks bilder](https://msdn.microsoft.com/library/mt169363.aspx) för att utföra nästan omedelbara säkerhets kopieringar och otroligt snabba återställningar.

Mer information finns i [SQL Server säkerhets kopiering och återställning med Azure Blob Storage-tjänsten](https://go.microsoft.com/fwlink/?LinkId=271617).

I följande två avsnitt introduceras Azure Blob Storage-tjänsten, inklusive nödvändiga SQL Server-komponenter. Det är viktigt att förstå komponenterna och deras interaktion för att kunna använda säkerhets kopiering och återställning från Azure Blob Storage-tjänsten.

## <a name="azure-blob-storage-service-components"></a>Azure Blob Storage Service-komponenter
Följande Azure-komponenter används när du säkerhetskopierar till Azure Blob Storage-tjänsten.

| Komponent | Beskrivning |
| --- | --- |
| **Lagrings konto** |Lagrings kontot är start punkten för alla lagrings tjänster. För att få åtkomst till en Azure Blob Storage-tjänst måste du först skapa ett Azure Storage-konto. Mer information om Azure Blob Storage-tjänsten finns i [så här använder du tjänsten azure Blob Storage](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Container** |En behållare ger gruppering av en uppsättning blobbar och kan lagra ett obegränsat antal blobbar. Om du vill skriva en SQL Server säkerhets kopiering till en Azure-Blob Service måste du ha minst en rot behållare skapad. |
| **BLOB** |En fil av valfri typ och storlek. Blobbar är adresser bara med följande URL-format: **https://[Storage Account]. blob. Core. Windows. net/[container]/[BLOB]**. Mer information om sid-blobar finns i [förstå block-och sid-blobar](https://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>SQL Server-komponenter
Följande SQL Servers komponenter används när du säkerhetskopierar till Azure Blob Storage-tjänsten.

| Komponent | Beskrivning |
| --- | --- |
| **URL** |En URL anger en Uniform Resource Identifier (URI) till en unik säkerhets kopierings fil. URL: en används för att ange plats och namn för den SQL Server säkerhets kopierings filen. URL: en måste peka på en faktisk BLOB, inte bara en behållare. Om blobben inte finns skapas den. Om en befintlig BLOB anges, Miss lyckas säkerhets kopieringen, om inte alternativet > med FORMAT anges. Följande är ett exempel på den URL som du anger i säkerhets kopierings kommandot: **http [s]://[storageaccount]. blob. Core. Windows. net/[container]/[filename. bak]**. HTTPS rekommenderas, men krävs inte. |
| **Autentiseringsuppgift** |Den information som krävs för att ansluta och autentisera till Azure Blob Storage-tjänsten lagras som en autentiseringsuppgift.  För att SQL Server ska kunna skriva säkerhets kopior till en Azure-Blob eller återställa från den, måste du skapa en SQL Server autentiseringsuppgift. Mer information finns i [SQL Server Credential](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> SQL Server 2016 har uppdaterats för att stödja block-blobar. Mer information finns i [Självstudier: använda Microsoft Azure Blob Storage-tjänsten med SQL Server 2016-databaser](https://msdn.microsoft.com/library/dn466438.aspx) .
> 
> 

## <a name="next-steps"></a>Nästa steg
1. Skapa ett Azure-konto om du inte redan har ett. Om du utvärderar Azure bör du tänka på den [kostnads fria utvärderings versionen](https://azure.microsoft.com/free/).
2. Gå sedan igenom någon av följande självstudier som vägleder dig genom att skapa ett lagrings konto och utföra en återställning.
   
   * **SQL Server 2014**: [självstudier: SQL Server 2014 säkerhets kopiering och återställning till Microsoft Azure Blob Storage-tjänsten](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [Självstudier: använda Microsoft Azure Blob Storage-tjänsten med SQL Server 2016-databaser](https://msdn.microsoft.com/library/dn466438.aspx)
3. Granska ytterligare dokumentation som börjar med [SQL Server säkerhets kopiering och återställning med Microsoft Azure Blob Storage-tjänsten](https://msdn.microsoft.com/library/jj919148.aspx).

Om du har problem kan du läsa avsnittet [SQL Server säkerhets kopiering till URL metod tips och fel sökning](https://msdn.microsoft.com/library/jj919149.aspx).

Andra SQL Server alternativ för säkerhets kopiering och återställning finns i [säkerhets kopiering och återställning för SQL Server i Azure-Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

