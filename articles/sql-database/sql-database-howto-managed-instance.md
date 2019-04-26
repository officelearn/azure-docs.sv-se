---
title: Så här konfigurerar du en Azure SQL Database managed instance | Microsoft Docs
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
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 886f06e8640891ac09d1e4624335a7bfebcd3def
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60340799"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Hur du använder en hanterad instans i Azure SQL Database

I den här artikeln kan du hitta olika guider, skript och som kan hjälpa dig att hantera och konfigurera din hanterade instans.

## <a name="migration"></a>Migrering

- [Migrera till en hanterad instans](sql-database-managed-instance-migrate.md) – Lär dig mer om rekommenderade migreringsprocessen och verktyg för migrering till en hanterad instans.

- [Migrera TDE-certifikat till en hanterad instans](sql-database-managed-instance-migrate-tde-certificate.md) – om din SQL Server-databas är skyddad med transparent datakryptering (TDE), skulle du behöva migrera certifikat som en hanterad instans kan använda för att dekryptera den säkerhetskopia som du vill återställa i Azure.

## <a name="network-configuration"></a>Nätverkskonfiguration

- [Avgöra storleken på en hanterad instans-undernätet](sql-database-managed-instance-determine-size-vnet-subnet.md) – den hanterade instansen är placerad i dedikerar undernät som inte kan ändras när du lägger till resurser i. Därför skulle du behöva beräkna vilka IP-adressintervall skulle krävas för undernät beroende på antalet och typerna av instanser som du vill distribuera i undernätet.
- [Skapa nya VNet och undernät för en hanterad instans](sql-database-managed-instance-create-vnet-subnet.md) – Azure VNet och undernät där du vill distribuera dina hanterade instanser måste konfigureras enligt den [krav som beskrivs här](sql-database-managed-instance-connectivity-architecture.md#network-requirements). I den här guiden hittar du det enklaste sättet att skapa ditt nya VNet och undernät som är rätt konfigurerat för hanterade instanser.
- [Konfigurera befintligt virtuellt nätverk och undernät för en hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md) – om du vill konfigurera din befintliga VNet och undernät för att distribuera hanterade instanser i här hittar du det skript som kontrollerar den [krav på](sql-database-managed-instance-connectivity-architecture.md#network-requirements) och kontrollera konfigurerar ditt undernät enligt kraven.
- [Konfigurera anpassad DNS](sql-database-managed-instance-custom-dns.md) – du måste konfigurera anpassad DNS om du vill komma åt externa resurser på anpassade domäner från din hanterade instans via länkade servern av db e-profiler.
- [Synkronisera nätverkskonfiguration](sql-database-managed-instance-sync-network-configuration.md) – det kan hända att även om du [integrerat din app med Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), kan du&#39;t upprätta anslutning till en hanterad instans. En sak som du ska uppdatera nätverkskonfigurationen för din serviceplan.
- [Hitta IP-adress för hantering av slutpunkten](sql-database-managed-instance-find-management-endpoint-ip-address.md) – den hanterade instansen använder offentliga slutpunkten för management-ändamål. Du kan fastställa IP-adressen för hanteringsslutpunkten med hjälp av skript som beskrivs här.
- [Kontrollera inbyggda brandväggsskydd](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – hanterad instans är skyddad med inbyggda brandväggen som tillåter trafik på portar som behövs. Du kan kontrollera och de inbyggda brandväggsregler med hjälp av skript som beskrivs i den här guiden.
- [Anslut program](sql-database-managed-instance-connect-app.md) – den hanterade instansen placeras i ditt eget privata virtuella Azure-nätverket med privata IP-adress. Läs mer om olika mönster för att ansluta program till din hanterade instans.

## <a name="feature-configuration"></a>Funktionskonfiguration

- [Transaktionsreplikering](replication-with-sql-database-managed-instance.md) gör det möjligt att replikera data mellan hanterade instanser, eller från en lokal SQL Server till en hanterad instans, och vice versa. Hitta mer information hur du använder och konfigurerar Transaktionsreplikering i den här guiden.
- [Konfigurera hotidentifiering](sql-database-managed-instance-threat-detection.md) – [hotidentifiering](sql-database-threat-detection-overview.md) är en inbyggd funktion i Azure SQL Database som identifierar olika potentiella attacker, till exempel SQL-inmatning eller åtkomst från misstänkta platser. I den här guiden kan du lära dig hur du aktiverar och konfigurerar [hotidentifiering](sql-database-threat-detection-overview.md) för en hanterad instans.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [anvisningar hjälper för enskilda databaser](sql-database-howto-single-database.md)