---
title: Snabbstart – Azure SQL Database-hanterad instans | Microsoft Docs
description: Lär dig att snabbt komma igång med Azure SQL Database – hanterad instans
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 77deed43c106a451d3de768989233c749e1280e1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468180"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Komma igång med Azure SQL Database-hanterad instans

[Azure SQL Database-hanterad instans](sql-database-managed-instance-index.yml) är en fullständigt hanterad PaaS-version av SQL Server som hanteras i Azure-molnet och placeras i ditt eget virtuella nätverk med den privata IP-adressen. I det här avsnittet lär du dig att snabbt konfigurera och skapa hanterad instans och migrera dina databaser.

## <a name="quickstart-overview"></a>Snabbstart, översikt

I det här avsnittet visas en översikt av tillgängliga artiklar som hjälper dig att snabbt komma igång med hanterade instanser. Det enklaste sättet att skapa din första hanterade instans är att använda [Azure-portalen](sql-database-managed-instance-get-started.md), där du kan konfigurera nödvändiga parametrar (användarnamn/lösenord, antal kärnor, maximal lagring) och automatiskt skapa Azure-nätverksmiljön utan att behöva känna till nätverksinformation och infrastrukturskrav. Du behöver bara se till att ha en [prenumerationstyp](sql-database-managed-instance-resource-limits.md#supported-subscription-types) som tillåts för att skapa instansen.

Om du har ett eget nätverk som du vill använda, eller om du vill anpassa nätverket, kan du läsa om hur du [konfigurerar nätverksmiljön](#configure-network-environment) för hanterad instans.

När du skapar din hanterade instans behöver du ansluta till den instansen med någon av följande metoder:

* Skapa en [virtuell Azure-dator](sql-database-managed-instance-configure-vm.md) med installerat SQL Server Management Studio och andra appar som kan användas för åtkomst till din hanterade instans i ett undernät i samma virtuella nätverk där din hanterade instans är placerad. Den virtuella datorn får inte vara i samma undernät som dina hanterade instanser.
* Konfigurera [Punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md) på datorn som gör att du kan ansluta datorn till det virtuella nätverket där den hanterade instansen finns och använda den hanterade instansen på samma sätt som andra SQL-servrar i nätverket.

Alternativt kan du använda ExpressRoute- eller plats-till-plats-anslutning från ditt lokala nätverk, men dessa metoder ligger utanför det område som behandlas i de här snabbstarterna.

När du skapar en hanterad instans och konfigurerar åtkomst kan du börja migrera dina databaser som placerats på lokal SQL Server eller på virtuella Azure-datorer. Observera att migreringen misslyckas om du har vissa funktioner som inte stöds i den källdatabas som du vill migrera. För att undvika fel och kontrollera kompatibilitet kan du installera [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595), som analyserar dina databaser på SQL Server och hittar eventuella problem som blockerar migrering till hanterad instans, till exempel förekomsten av FileStream eller flera loggfiler. Om du lösa de här problemen är dina databaser redo att föras till hanterad instans. [Database Experimentation Assistant](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) är ett annat bra verktyg som kan registrera din arbetsbelastning på SQL Server och spela upp den på hanterad instans så att du kan fastställa huruvida det kommer att finnas prestandaproblem om du migrerar till hanterad instans.

När du är säker på att du kan migrera din databas till hanterad instans kan du använda funktionen för [inbyggd återställning](sql-database-managed-instance-get-started-restore.md), som gör att du kan skapa en säkerhetskopia av databasen med Transact-SQL-kommandot, ladda upp den till en Azure-bloblagring och återställa databasen från bloblagringen med hjälp av Transact-SQL-kommandot.

Med de här snabbstarterna kan du snabbt konfigurera, skapa och placera databaser i dina hanterade instanser. I vissa scenarier behöver du anpassa eller automatisera distributionen av hanterad instans och den nödvändiga nätverksmiljön. De här scenarierna beskrivs nedan.

## <a name="customizing-network-environment"></a>Anpassa nätverksmiljön

Även om det virtuella nätverket/undernätet kan konfigureras automatiskt när instansen skapas med hjälp av [Azure-portalen](sql-database-managed-instance-get-started.md) kan det vara bra att skapa det innan du börjar skapa hanterade instanser eftersom du kan konfigurera parametrarna för virtuellt nätverk och undernät. Det enklaste sättet att skapa och konfigurera nätverksmiljön är att använda mallen för [Azure-resursdistribution](sql-database-managed-instance-create-vnet-subnet.md), som skapar och konfigurerar det nätverk och undernät där instansen ska placeras. Du behöver bara trycka på knappen för Azure Resource Manager-distribution och fylla i formuläret med parametrar. Alternativt kan du använda [PowerShell-skript](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) för att automatisera skapandet av nätverket.

Om du redan har ett virtuellt nätverk och undernät där du vill distribuera din hanterade instans behöver du se till att ditt virtuella nätverk och undernät uppfyller [nätverkskraven](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Du bör använda det här [PowerShell-skriptet för att kontrollera att undernätet är korrekt konfigurerat](sql-database-managed-instance-configure-vnet-subnet.md). Det här skriptet verifierar inte bara nätverket och rapporterar problemen – det talar även om för dig vad som bör ändras och erbjuder att genomföra de nödvändiga ändringarna i det virtuella nätverket/undernätet. Kör det här skriptet om du inte vill konfigurera ditt virtuella nätverk/undernät manuellt. Du bör även köra det efter alla större omkonfigurationer av nätverksinfrastrukturen. Om du vill skapa och konfigurera ditt eget nätverk läser du [dokumentationen för hanterad instans](sql-database-managed-instance-connectivity-architecture.md) och [den här guiden](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="automating-creation-of-managed-instance"></a>Automatisera skapandet av hanterad instans

 Om du inte har skapat nätverksmiljön enligt beskrivningen i föregående steg kan Azure-portalen göra det åt dig – den enda nackdelen är att den konfigurerar den med vissa standardparametrar som du inte kan ändra senare. Alternativt kan du använda [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/), [PowerShell med Resource Manager-mall](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md) eller [Azure CLI](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/).

## <a name="migrating-to-managed-instance-with-minimal-downtime"></a>Migrera till hanterad instans med minimal avbrottstid

Med artiklarna i de här snabbstarterna kan du snabbt konfigurera hanterad instans och flytta databaserna. Med inbyggd återställning skulle du däremot behöva vänta tills databaserna har återställts, vilket skulle leda till vissa avbrottstid i programmet, särskilt om databasen är förhållandevis stor. Om du flyttar produktionsdatabasen behöver du förmodligen ett bättre sätt att migrera som garanterar minimal avbrottstid för migrering. [Datamigreringstjänsten](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) är en migreringstjänst som kan migrera din databas med minimal avbrottstid genom att inkrementellt överföra ändringar som görs i källdatabasen till en databas som du återställer till den hanterade instansen. På så sätt kan du snabbt växla programmet från käll- till måldatabas med minimal avbrottstid.

## <a name="next-steps"></a>Nästa steg

* [Här finns en lista på hög nivå över funktioner som stöds i hanterade instanser](sql-database-features.md), och [här finns information och kända problem](sql-database-managed-instance-transact-sql-information.md). 
* Lär dig mer om [tekniska egenskaper för hanterad instans](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits). 
* Mer avancerade självstudier finns i [instruktionsavsnittet](sql-database-howto-managed-instance.md). 
