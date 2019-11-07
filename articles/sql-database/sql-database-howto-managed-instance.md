---
title: Så här konfigurerar du en Azure SQL Database Hanterad instans
description: Lär dig hur du konfigurerar och hanterar Azure SQL Database Hanterad instans.
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
ms.openlocfilehash: 22bad381a59a74237a5539cd4294a08797c017b1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689918"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Så här använder du en hanterad instans i Azure SQL Database

I den här artikeln kan du hitta olika guider, skript och förklaringar som kan hjälpa dig att hantera och konfigurera din hanterade instans.

## <a name="migration"></a>Migrering

- [Migrera till en hanterad instans](sql-database-managed-instance-migrate.md) – Läs mer om den rekommenderade migreringsprocessen och verktyg för migrering till en hanterad instans.

- [MIGRERA TDE-certifikat till en hanterad instans](sql-database-managed-instance-migrate-tde-certificate.md) – om SQL Server databasen skyddas med transparent data kryptering (TDE) måste du migrera certifikatet som en hanterad instans kan använda för att dekryptera den säkerhets kopia som du vill återställa i Azure.

## <a name="network-configuration"></a>Nätverkskonfiguration

- [Bestäm storleken på under nätet för hanterade instanser](sql-database-managed-instance-determine-size-vnet-subnet.md) – den hanterade instansen placeras i ett dedikerat undernät som inte kan ändras när du lägger till resurserna i. Därför måste du beräkna vilka IP-adressintervall som krävs för under nätet beroende på antalet och typerna av instanser som du vill distribuera i under nätet.
- [Skapa nytt VNet och undernät för en hanterad instans](sql-database-managed-instance-create-vnet-subnet.md) – Azure VNet och undernät där du vill distribuera dina hanterade instanser måste konfigureras enligt de [nätverks krav som beskrivs här](sql-database-managed-instance-connectivity-architecture.md#network-requirements). I den här guiden hittar du det enklaste sättet att skapa ditt nya VNet och undernät korrekt konfigurerat för hanterade instanser.
- [Konfigurera befintligt VNet och undernät för en hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md) – om du vill konfigurera ditt befintliga VNet och undernät för att distribuera hanterade instanser i, hittar du det skript som kontrollerar [nätverks kraven](sql-database-managed-instance-connectivity-architecture.md#network-requirements) och konfigurerar undernät enligt kraven.
- [Konfigurera anpassad DNS](sql-database-managed-instance-custom-dns.md) – du måste konfigurera anpassad DNS om du vill komma åt externa resurser på de anpassade domänerna från den hanterade instansen via länkad server för DB mail-profiler.
- [Synkronisera nätverks konfiguration](sql-database-managed-instance-sync-network-configuration.md) – det kan hända att även om du [integrerar din app med en Azure](../app-service/web-sites-integrate-with-vnet.md)-Virtual Network&#39;kan du skapa en anslutning till en hanterad instans. En sak som du kan prova är att uppdatera nätverks konfigurationen för din tjänst plan.
- [Hitta hanterings slut punktens IP-adress](sql-database-managed-instance-find-management-endpoint-ip-address.md) – hanterad instans använder offentlig slut punkt för hanterings ändamål. Du kan fastställa IP-adressen för hanterings slut punkten med hjälp av skriptet som beskrivs här.
- [Verifiera inbyggd brand Väggs skydd](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – hanterad instans skyddas med inbyggd brand vägg som endast tillåter trafik på nödvändiga portar. Du kan kontrol lera och verifiera de inbyggda brand Väggs reglerna med hjälp av skriptet som beskrivs i den här hand boken.
- [Anslut program](sql-database-managed-instance-connect-app.md) – hanterad instans placeras i ditt eget privata Azure VNet med privat IP-adress. Lär dig mer om olika mönster för att ansluta program till din hanterade instans.

## <a name="feature-configuration"></a>Funktions konfiguration

- Med [Transaktionsreplikering kan du](replication-with-sql-database-managed-instance.md) replikera data mellan hanterade instanser eller från lokala SQL Server till en hanterad instans och tvärtom. Hitta mer information om hur du använder och konfigurerar transaktions replikering i den här guiden.
- [Konfigurera hot identifiering](sql-database-managed-instance-threat-detection.md) – [hot identifiering](sql-database-threat-detection-overview.md) är en inbyggd Azure SQL Database funktion som identifierar olika potentiella attacker som SQL-inmatning eller åtkomst från misstänkta platser. I den här guiden får du lära dig hur du aktiverar och konfigurerar [hot identifiering](sql-database-threat-detection-overview.md) för en hanterad instans.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [instruktions guider för enskilda databaser](sql-database-howto-single-database.md)