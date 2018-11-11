---
title: Felsöka problem med anslutningen till Azure Database for MariaDB | Microsoft Docs
description: Lär dig hur du felsöker problem med anslutningen till Azure Database for MariaDB.
keywords: mariadb-anslutning, anslutningssträngen, problem med nätverksanslutningen, tillfälligt fel, anslutningsfel
services: mariadb
author: janeng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: mariadb
ms.topic: article
ms.date: 11/09/2018
ms.openlocfilehash: 63ccb2fe73a411ac7e4fca663f0d6b47647c02f4
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51347729"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mariadb"></a>Felsöka problem med anslutningen till Azure Database for MariaDB

Anslutningsproblem kan orsakas av många olika saker, inklusive:

* Brandväggsinställningar
* Anslutnings-timeout
* Felaktig inloggningsinformation
* Maxgränsen har nåtts för vissa Azure Database for MariaDB-resurser
* Problem med infrastrukturen i tjänsten
* Underhåll utförs i tjänsten
* Compute-allokeringen av servern ändras genom att skala antalet virtuella kärnor eller flyttar till en annan tjänstnivå

Problem med anslutningen till Azure Database for MariaDB kan i allmänhet bör klassificeras på följande sätt:

* Tillfälliga fel (tillfällig eller återkommande)
* Permanenta eller icke tillfälliga fel (fel regelbundet återkommande)

## <a name="troubleshoot-transient-errors"></a>Felsök tillfälliga fel

Tillfälligt fel uppstår när underhåll utförs, systemet påträffar ett fel med maskinvara eller programvara eller du ändrar nivå för virtuella kärnor eller tjänsten för din server. Tjänsten Azure Database for MariaDB har inbyggd hög tillgänglighet och är utformad för att minimera sådana problem automatiskt. Men förlorar programmet anslutningen till servern för en kort tidsperiod, vanligtvis mindre än 60 sekunder högst. Vissa händelser kan ibland ta längre tid att lösa, till exempel när en stor transaktion gör en tidskrävande återställning.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Steg för att lösa tillfälliga anslutningsproblem

1. Kontrollera den [Microsoft Azure-instrumentpanelen](https://azure.microsoft.com/status) för alla kända avbrott som inträffat under den tid som fel rapporterades av programmet.
2. Program som ansluter till en molntjänst som Azure Database for MariaDB bör förvänta dig tillfälliga fel och implementera logik för omprövning för att hantera de här felen i stället för att visa dessa som programfel för användare. Granska [hantering av tillfälliga anslutningsfel för Azure Database for MariaDB](concepts-connectivity.md) om bästa metoder och designriktlinjer för hantering av tillfälliga fel.
3. När en server närmar sig sin resursbegränsningar, verkar fel vara tillfälligt anslutningsproblem. Se [begränsningar i Azure Database for MariaDB](concepts-limits.md).
4. Om problem med nätverksanslutningen fortsätta, eller om den varaktighet som ditt program påträffar felet överstiger 60 sekunder eller om du ser upprepade förekomster av felet i en viss dag fil en supportförfrågan för Azure genom att välja **få stöd för**på den [Azure-supporten](https://azure.microsoft.com/support/options) plats.

## <a name="troubleshoot-persistent-errors"></a>Felsöka permanenta fel

Om programmet misslyckas med ett beständigt sätt att ansluta till Azure Database for MariaDB, indikerar vanligtvis ett problem med något av följande:

* Brandväggskonfiguration: Azure Database för MariaDB-servern eller på klientsidan brandvägg blockerar anslutningar.
* Nätverksomkonfigurering på klientsidan: en ny IP-adress eller en proxyserver har lagts till.
* Användarfel: till exempel du kanske skrev du fel anslutningsparametrar, som servernamnet i anslutningssträngen eller en saknad *@servername* suffix i användarnamnet.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Steg för att lösa beständiga anslutningsproblem

1. Konfigurera [brandväggsregler](howto-manage-firewall-portal.md) som tillåter klientens IP-adress. Konfigurera en brandväggsregel med 0.0.0.0 som den inledande IP-adress och använda 255.255.255.255 som den sista IP-adressen för tillfälliga endast för testning. Servern för att alla IP-adresser öppnas. Om det löser problem med nätverksanslutningen, ta bort den här regeln och skapa en brandväggsregel för en korrekt begränsad IP-adressen eller adressintervallet.
2. Kontrollera att port 3306 är öppen för utgående anslutningar på alla brandväggar mellan klienten och internet.
3. Kontrollera anslutningssträngen och andra anslutningsinställningar. Granska [så här ansluter du program till Azure Database for MariaDB](howto-connection-string.md).
4. Kontrollera tjänstehälsa i instrumentpanelen. Om du tror att det finns ett regionalt strömavbrott kan du se [översikt över affärskontinuitet med Azure Database for MariaDB](concepts-business-continuity.md) anvisningar för hur du återställer till en ny region.

## <a name="next-steps"></a>Nästa steg

* [Hantering av tillfälliga anslutningsfel för Azure Database for MariaDB](concepts-connectivity.md)
