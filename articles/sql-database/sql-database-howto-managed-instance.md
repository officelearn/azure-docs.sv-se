---
title: Konfigurera en hanterad instans
description: Lär dig hur du konfigurerar och hanterar Azure SQL Database-hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: 906ae2a970ce1d5b82302d0277ca45bd93c23011
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256918"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Så här använder du en hanterad instans i Azure SQL Database

I den här artikeln hittar du olika guider, skript och förklaringar som kan hjälpa dig att hantera och konfigurera din hanterade instans.

## <a name="migration"></a>Migrering

- [Migrera till en hanterad instans](sql-database-managed-instance-migrate.md) – Lär dig mer om den rekommenderade migreringsprocessen och verktygen för migrering till en hanterad instans.

- [Migrera TDE-certifikat till en hanterad instans](sql-database-managed-instance-migrate-tde-certificate.md) – Om SQL Server-databasen är skyddad med transparent datakryptering (TDE) måste du migrera certifikat som en hanterad instans kan använda för att dekryptera säkerhetskopian som du vill återställa i Azure.

## <a name="network-configuration"></a>Nätverkskonfiguration

- [Fastställ storleken på ett hanterat instansundernät](sql-database-managed-instance-determine-size-vnet-subnet.md) – Hanterad instans placeras i undernätet dedikeringar som inte kan ändra storlek när du lägger till resurserna inuti. Därför måste du beräkna vilket IP-adressintervall som skulle krävas för undernätet beroende på antalet och typerna av instanser som du vill distribuera i undernätet.
- [Skapa nytt VNet och undernät för en hanterad instans](sql-database-managed-instance-create-vnet-subnet.md) – Azure VNet och undernät där du vill distribuera dina hanterade instanser måste konfigureras enligt de [nätverkskrav som beskrivs här](sql-database-managed-instance-connectivity-architecture.md#network-requirements). I den här guiden hittar du det enklaste sättet att skapa ditt nya virtuella nätverk och undernät som är korrekt konfigurerade för hanterade instanser.
- [Konfigurera befintliga virtuella nätverk och undernät för en hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md) – om du vill konfigurera ditt befintliga virtuella nätverk och undernät för att distribuera hanterade instanser inuti, hittar du skriptet som kontrollerar [nätverkskraven](sql-database-managed-instance-connectivity-architecture.md#network-requirements) och konfigurerar undernätet enligt kraven.
- [Konfigurera anpassad DNS](sql-database-managed-instance-custom-dns.md) – du måste konfigurera anpassad DNS om du vill komma åt externa resurser på anpassade domäner från din hanterade instans via länkad server med db-e-postprofiler.
- [Synkroniseringsnätverkskonfiguration](sql-database-managed-instance-sync-network-configuration.md) – Det kan hända att även om du [har integrerat din app med ett Virtuellt Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md)kan du&#39;inte upprätta anslutningen till en hanterad instans. En sak du kan prova är att uppdatera nätverkskonfigurationen för din serviceplan.
- [Hitta IP-adress för hanteringsslutpunkter](sql-database-managed-instance-find-management-endpoint-ip-address.md) – Hanterad instans använder offentlig slutpunkt för hanteringsändamål. Du kan bestämma IP-adressen för hanteringsslutpunkten med hjälp av skriptet som beskrivs här.
- [Verifiera inbyggt brandväggsskydd](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – Hanterad instans är skyddad med inbyggd brandvägg som endast tillåter trafik på nödvändiga portar. Du kan kontrollera och verifiera de inbyggda brandväggsreglerna med hjälp av skriptet som beskrivs i den här guiden.
- [Connect-program](sql-database-managed-instance-connect-app.md) – Hanterad instans placeras i ditt eget privata Azure VNet med privat IP-adress. Lär dig mer om olika mönster för att ansluta programmen till din hanterade instans.

## <a name="feature-configuration"></a>Funktionskonfiguration

- [Med transaktionsreplikering](replication-with-sql-database-managed-instance.md) kan du replikera data mellan hanterade instanser eller från lokal SQL Server till en hanterad instans och vice versa. Hitta mer information om hur du använder och konfigurerar transaktionsreplikering i den här guiden.
- [Konfigurera hotidentifiering](sql-database-managed-instance-threat-detection.md) – [hotidentifiering](sql-database-threat-detection-overview.md) är en inbyggd Azure SQL Database-funktion som identifierar olika potentiella attacker som SQL-injektion eller åtkomst från misstänkta platser. I den här guiden kan du lära dig hur du aktiverar och konfigurerar [hotidentifiering](sql-database-threat-detection-overview.md) för en hanterad instans.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [instruktioner för enskilda databaser](sql-database-howto-single-database.md)