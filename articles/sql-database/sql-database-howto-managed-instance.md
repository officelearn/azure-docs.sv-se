---
title: Så här konfigurerar du Azure SQL Database Managed Instance | Microsoft Docs
description: Lär dig hur du konfigurerar och hanterar Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: 10a33ac09b5cfa588bef88e6c1587d67036b1aef
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53440494"
---
# <a name="how-to-use-managed-instance"></a>Hur du använder Managed Instance

I det här avsnittet kan du hitta olika guider, skript och som kan hjälpa dig att hantera och konfigurera Azure SQL Database - hanterad instans.

## <a name="migration"></a>Migrering

- [Migrera till Managed instance](sql-database-managed-instance-migrate.md) – Lär dig mer om rekommenderade migreringsprocessen och verktyg för migrering till Managed Instance.

- [Migrera TDE-certifikat till den hanterade instansen](sql-database-managed-instance-migrate-tde-certificate.md) – om din SQL Server-databas är skyddad med transparent datakryptering (TDE), skulle du behöva migrera certifikat som hanterad instans kan använda för att dekryptera den säkerhetskopia som du vill återställa i Azure.

## <a name="network-configuration"></a>Nätverkskonfiguration

- [Avgöra storleken på hanterad instans undernät](sql-database-managed-instance-determine-size-vnet-subnet.md) – Managed Instance är placerad i dedikerar undernät som inte kan ändras när du lägger till resurser i. Därför skulle du behöva beräkna vilka IP-adressintervall skulle krävas för undernät beroende på antalet och typerna av instanser som du vill distribuera i undernätet.
- [Skapa nya VNet och undernät för hanterad instans](sql-database-managed-instance-create-vnet-subnet.md) – Azure VNet och undernät där du vill distribuera dina hanterade instanser måste konfigureras enligt den [krav som beskrivs här](sql-database-managed-instance-connectivity-architecture.md#network-requirements). I den här guiden hittar du det enklaste sättet att skapa ditt nya VNet och undernät som har konfigurerats korrekt för hanterade instanser.
- [Konfigurera befintliga VNet och undernät för hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md) – om du vill konfigurera din befintliga VNet och undernät för att distribuera hanterade instanser i här hittar du det skript som kontrollerar den [krav på](sql-database-managed-instance-connectivity-architecture.md#network-requirements) och Kontrollera konfigurerar ditt undernät enligt kraven.
- [Konfigurera anpassad DNS](sql-database-managed-instance-custom-dns.md) – du måste konfigurera anpassad DNS om du vill komma åt externa resurser på anpassade domäner från din hanterade instans via länkade servern av db e-postprofiler.
- [Synkronisera nätverkskonfiguration](sql-database-managed-instance-sync-network-configuration.md) – det kan hända att även om du [integrerat din app med Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), kan du&#39;t upprätta anslutning till hanterad instans. En sak som du ska uppdatera nätverkskonfigurationen för din serviceplan.
- [Hitta IP-adress för hantering av slutpunkten](sql-database-managed-instance-find-management-endpoint-ip-address.md) – Managed Instance använder offentlig slutpunkt management-endast för. Du kan fastställa IP-adressen för hanteringsslutpunkten med hjälp av skript som beskrivs här.
- [Kontrollera inbyggda brandväggsskydd](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – Managed Instance är skyddat med inbyggda brandväggen som tillåter trafik på portar som behövs. Du kan kontrollera och de inbyggda brandväggsregler med hjälp av skript som beskrivs i den här guiden.
- [Anslut program](sql-database-managed-instance-connect-app.md) – Managed Instance placeras i ditt eget privata virtuella Azure-nätverket med privata IP-adress. Läs mer om olika mönster för att ansluta program till din hanterade instans.

## <a name="feature-configuration"></a>Funktionskonfiguration

- [Transaktionsreplikering](replication-with-sql-database-managed-instance.md) gör det möjligt att replikera data mellan hanterade instanser eller från en lokal SQL Server till Managed Instance och vice versa. Hitta mer information hur du använder och konfigurerar Transaktionsreplikering i den här guiden.
- [Konfigurera hotidentifiering](sql-database-managed-instance-threat-detection.md) – [hotidentifiering](sql-database-threat-detection-overview.md) är en inbyggd funktion i Azure SQL Database som identifierar olika potentiella attacker, till exempel SQL-inmatning eller åtkomst från misstänkta platser. I den här guiden kan du lära dig hur du aktiverar och konfigurerar [hotidentifiering](sql-database-threat-detection-overview.md) för hanterad instans.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [anvisningar hjälper i enkel databas](sql-database-howto-single-database.md)