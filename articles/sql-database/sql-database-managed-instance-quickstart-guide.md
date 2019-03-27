---
title: Snabbstart – Hanterad Azure SQL Database-instans | Microsoft Docs
description: Lär dig att snabbt komma igång med Azure SQL Database – hanterad instans
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 08b7870000b38d782e9282eef7ec9c0d551094dd
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444321"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Komma igång med hanterad Azure SQL Database-instans

Alternativet för distribution av [hanterad instans](sql-database-managed-instance-index.yml) skapar en databas med nästan 100 % kompatibilitet med den senaste lokala SQL Server-databasmotorn (Enterprise Edition), vilket ger en ursprunglig implementering av [virtuellt nätverk (VNet)](../virtual-network/virtual-networks-overview.md) som åtgärdar vanliga säkerhetsproblem samt en [affärsmodell](https://azure.microsoft.com/pricing/details/sql-database/) som är fördelaktig för kunder med lokal SQL Server. I den här artikeln lär du dig att snabbt konfigurera och skapa en hanterad instans och migrera dina databaser.

## <a name="quickstart-overview"></a>Snabbstart, översikt

Med följande snabbstarter kan du snabbt skapa en hanterad instans, konfigurera en virtuell dator eller punkt-till-plats-VPN-anslutning för klientprogram samt återställa en databas till din nya hanterade instans med hjälp av en `.bak`-fil.

### <a name="configure-environment"></a>Konfigurera miljön

Som ett första steg behöver du skapa din första hanterade instans med nätverksmiljön där den kommer att placeras, och aktivera anslutning från datorn eller den virtuella datorn där du kör frågor till hanterad instans. Du kan använda följande guider:

- [Skapa en hanterad instans med hjälp av Azure-portalen](sql-database-managed-instance-get-started.md). I Azure-portalen konfigurerar du nödvändiga parametrar (användarnamn/lösenord, antal kärnor och maximalt lagringsutrymme) och skapar automatiskt Azure-nätverksmiljön utan att behöva känna till nätverksinformation och infrastrukturskrav. Du behöver bara se till att ha en [prenumerationstyp](sql-database-managed-instance-resource-limits.md#supported-subscription-types) som för närvarande tillåts att skapa en hanterad instans. Om du har ett eget nätverk som du vill använda eller om du vill anpassa nätverket kan du läsa [Konfigurera ett befintligt virtuellt nätverk för Azure SQL Database Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md) eller [Skapa ett virtuellt nätverk för Azure SQL Database Managed Instance](sql-database-managed-instance-create-vnet-subnet.md).
- En hanterad instans skapas i ett eget virtuellt nätverk utan offentlig slutpunkt. För klientprogramåtkomst kan du skapa antingen **en virtuell dator i samma virtuella nätverk (olika undernät)** eller **en punkt-till-plats-VPN-anslutning till det virtuella nätverket från klientdatorn** med hjälp av någon av dessa snabbstarter:

  - Skapa [virtuell Azure-dator i det virtuella nätverket för hanterad instans](sql-database-managed-instance-configure-vm.md) för klientprogrammets anslutning, inklusive SQL Server Management Studio.
  - Konfigurera [punkt-till-plats-VPN-anslutning till din hanterade instans](sql-database-managed-instance-configure-p2s.md) från den klientdator där du har SQL Server Management Studio och andra program för klientanslutningar. Det här är det andra av två alternativ för anslutning till din hanterade instans och till dess virtuella nätverk.

  > [!NOTE]
  > Du kan även använda ExpressRoute- eller plats-till-plats-anslutning från ditt lokala nätverk, men dessa metoder ligger utanför det område som behandlas i de här snabbstarterna.

### <a name="migrate-your-databases"></a>Migrera dina databaser

När du har skapat en hanterad instans och konfigurerat åtkomst kan du börja migrera dina databaser från lokal SQL Server eller virtuella Azure-datorer. Migreringen misslyckas om du har vissa funktioner som inte stöds i den källdatabas som du vill migrera. För att undvika fel och kontrollera kompatibilitet kan du installera [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595), som analyserar dina databaser på SQL Server och hittar eventuella problem som blockerar migrering till en hanterad instans, till exempel förekomsten av [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) eller flera loggfiler. Om du löser de här problemen är dina databaser redo att migreras till hanterad instans. [Database Experimentation Assistant](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) är ett annat bra verktyg som kan registrera din arbetsbelastning på SQL Server och spela upp den på hanterad instans så att du kan fastställa huruvida det kommer att finnas prestandaproblem om du migrerar till hanterad instans.

När du är säker på att du kan migrera din databas till en hanterad instans kan du använda inbyggda SQL Server-funktioner för återställning för att återställa en databas till en hanterad instans från en `.bak`-fil. Du kan använda den här metoden för att migrera databaser från en SQL Server-databasmotor som installerats lokalt eller en virtuell Azure-dator. En snabbstart finns i [Återställ från säkerhetskopia till en hanterad instans](sql-database-managed-instance-get-started-restore.md). I den här snabbstarten återställer du från en `.bak`-fil som lagras i Azures bloblagring med hjälp av Transact-SQL-kommandot `RESTORE`.

> [!TIP]
> Mer information om användning av Transact-SQL-kommandot `BACKUP` för att skapa en säkerhetskopia av din databas i Azures bloblagring finns i [SQL Server-säkerhetskopiering till URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Med de här snabbstarterna kan du snabbt skapa, konfigurera och återställa databassäkerhetskopior till en hanterad instans. I vissa scenarier behöver du anpassa eller automatisera distributionen av hanterade instanser och den nödvändiga nätverksmiljön. De här scenarierna beskrivs nedan.

## <a name="customize-network-environment"></a>Anpassa nätverksmiljön

Även om det virtuella nätverket/undernätet kan konfigureras automatiskt när instansen skapas med hjälp av [Azure-portalen](sql-database-managed-instance-get-started.md) kan det vara bra att skapa det innan du börjar skapa hanterade instanser eftersom du kan konfigurera parametrarna för virtuellt nätverk och undernät. Det enklaste sättet att skapa och konfigurera nätverksmiljön är att använda mallen för [Azure-resursdistribution](sql-database-managed-instance-create-vnet-subnet.md), som skapar och konfigurerar det nätverk och undernät där instansen ska placeras. Du behöver bara trycka på knappen för Azure Resource Manager-distribution och fylla i formuläret med parametrar.

Alternativt kan du använda [PowerShell-skript](https://www.powershellmagazine.com/20../../configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) för att automatisera skapandet av nätverket.

Alternativt kan du använda det här [PowerShell-skriptet](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) för att automatisera skapandet av nätverket.

Om du redan har ett virtuellt nätverk och undernät där du vill distribuera din hanterade instans behöver du se till att ditt virtuella nätverk och undernät uppfyller [nätverkskraven](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Använd det här [PowerShell-skriptet för att kontrollera att undernätet är korrekt konfigurerat](sql-database-managed-instance-configure-vnet-subnet.md). Det här skriptet verifierar nätverket och rapporterar eventuella problem. Det talar även om för dig vad som bör ändras och erbjuder att genomföra de nödvändiga ändringarna i det virtuella nätverket/undernätet. Kör det här skriptet om du inte vill konfigurera ditt virtuella nätverk/undernät manuellt. Du kan även köra det efter större omkonfigurationer av nätverksinfrastrukturen. Om du vill skapa och konfigurera ett eget nätverk kan du läsa [anslutningsarkitektur](sql-database-managed-instance-connectivity-architecture.md) och den här[ultimata guiden för att skapa och konfigurera en miljö för hanterad instans](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="automating-creation-of-a-managed-instance"></a>Automatisera skapandet av en hanterad instans

 Om du inte har skapat nätverksmiljön enligt beskrivningen i föregående steg kan Azure-portalen göra det åt dig – den enda nackdelen är att den konfigurerar den med vissa standardparametrar som du inte kan ändra senare. Alternativt kan du använda:

- [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)
- [PowerShell med Resource Manager-mall](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)
- [Azure CLI](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/).
- [Resource Manager-mall](sql-database-single-database-get-started-template.md)

## <a name="migrating-to-a-managed-instance-with-minimal-downtime"></a>Migrera till en hanterad instans med minimal avbrottstid

Med artiklarna i de här snabbstarterna kan du snabbt konfigurera en hanterad instans och flytta databaserna med hjälp av inbyggd `RESTORE`-funktion. Men med inbyggd `RESTORE` måste du vänta på att databaserna återställts (och kopieras till Azure-bloblagring om de inte redan lagras där). Detta leder till viss avbrottstid för ditt program, särskilt för större databaser. Om du vill flytta produktionsdatabasen använder du [Data Migration Service (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) för att migrera din databas med minimal avbrottstid. DMS åstadkommer detta genom att inkrementellt överföra ändringar som gjorts i källdatabasen till den databas för hanterad instans som återställs. På så sätt kan du snabbt växla programmet från käll- till måldatabas med minimal avbrottstid.

## <a name="next-steps"></a>Nästa steg

- [Här finns en lista på hög nivå över funktioner som stöds i hanterade instanser](sql-database-features.md), och [här finns information och kända problem](sql-database-managed-instance-transact-sql-information.md).
- Lär dig mer om [tekniska egenskaper för hanterad instans](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).
- Det finns mer avancerade anvisningar i [så använder du en hanterad instans i Azure SQL Database](sql-database-howto-managed-instance.md).
