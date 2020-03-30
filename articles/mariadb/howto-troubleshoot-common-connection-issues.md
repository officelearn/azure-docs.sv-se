---
title: Felsöka anslutningsproblem - Azure Database för MariaDB
description: Lär dig hur du felsöker anslutningsproblem till Azure Database för MariaDB, inklusive tillfälliga fel som kräver återförsök, brandväggsproblem och avbrott.
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: d134bcd0c5f9bfde0fdb095122d54848873174be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536368"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mariadb"></a>Felsöka anslutningsproblem till Azure Database for MariaDB

Anslutningsproblem kan orsakas av en mängd olika saker, bland annat:

* Brandväggsinställningar
* Time-out för anslutning
* Felaktig inloggningsinformation
* Högsta gräns som uppnåtts för vissa Azure-databas för MariaDB-resurser
* Problem med tjänstens infrastruktur
* Underhåll som utförs i tjänsten
* Beräkningsallokeringen av servern ändras genom att antalet virtuella kärnor skalas eller flyttas till en annan tjänstnivå

I allmänhet kan anslutningsproblem till Azure Database för MariaDB klassificeras enligt följande:

* Tillfälliga fel (kortlivade eller intermittenta)
* Beständiga eller icke-tillfälliga fel (fel som regelbundet återkommer)

## <a name="troubleshoot-transient-errors"></a>Felsöka tillfälliga fel

Tillfälliga fel uppstår när underhåll utförs, systemet stöter på ett fel med maskinvaran eller programvaran eller du ändrar serverns virtuella kärnor eller tjänstnivå. Azure Database för MariaDB-tjänsten har inbyggd hög tillgänglighet och är utformad för att minska dessa typer av problem automatiskt. Programmet förlorar dock anslutningen till servern under en kort tidsperiod på normalt mindre än 60 sekunder som mest. Vissa händelser kan ibland ta längre tid att minska, till exempel när en stor transaktion orsakar en tidskrävande återställning.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Åtgärder för att lösa tillfälliga anslutningsproblem

1. Kontrollera [Microsoft Azure Service Dashboard](https://azure.microsoft.com/status) för alla kända avbrott som inträffade under den tid då felen rapporterades av programmet.
2. Program som ansluter till en molntjänst som Azure Database för MariaDB bör förvänta sig tillfälliga fel och implementera logik för återförsök för att hantera dessa fel i stället för att visa dessa som programfel för användare. Granska [Hantering av tillfälliga anslutningsfel för Azure Database för MariaDB](concepts-connectivity.md) för metodtips och designriktlinjer för hantering av tillfälliga fel.
3. När en server närmar sig sina resursgränser kan fel verka vara tillfälliga anslutningsproblem. Se [Begränsningar i Azure Database för MariaDB](concepts-limits.md).
4. Om anslutningsproblemen fortsätter, eller om den varaktighet som ditt program stöter på felet överskrider 60 sekunder eller om du ser flera förekomster av felet under en viss dag, lämnar du in en Azure-supportbegäran genom att välja **Hämta support** på [Azure-supportwebbplatsen.](https://azure.microsoft.com/support/options)

## <a name="troubleshoot-persistent-errors"></a>Felsöka beständiga fel

Om programmet beständigt misslyckas med att ansluta till Azure Database för MariaDB, anger det vanligtvis ett problem med något av följande:

* Brandväggskonfiguration: Azure-databasen för MariaDB-servern eller brandväggen på klientsidan blockerar anslutningar.
* Nätverksomkonfiguration på klientsidan: En ny IP-adress eller en proxyserver har lagts till.
* Användarfel: Du kan till exempel ha felskrivna anslutningsparametrar, till exempel servernamnet i anslutningssträngen eller ett * \@saknat servernamnssuffix* i användarnamnet.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Åtgärder för att lösa beständiga anslutningsproblem

1. Ställ in [brandväggsregler](howto-manage-firewall-portal.md) för att tillåta klient-IP-adressen. Endast för tillfälliga tester ställer du in en brandväggsregel med 0.0.0.0 som start-IP-adress och använder 255.255.255.255 som slut-IP-adress. Då öppnas servern för alla IP-adresser. Om detta löser anslutningsproblemet tar du bort den här regeln och skapar en brandväggsregel för en lämpligt begränsad IP-adress eller adressintervall.
2. På alla brandväggar mellan klienten och Internet kontrollerar du att port 3306 är öppen för utgående anslutningar.
3. Kontrollera anslutningssträngen och andra anslutningsinställningar. Granska [Hur du ansluter program till Azure Database för MariaDB](howto-connection-string.md).
4. Kontrollera tjänstens hälsotillstånd på instrumentpanelen. Om du tror att det finns ett regionalt avbrott läser du [Översikt över affärskontinuitet med Azure Database för MariaDB](concepts-business-continuity.md) för steg att återställa till en ny region.

## <a name="next-steps"></a>Nästa steg

* [Hantering av tillfälliga anslutningsfel för Azure Database för MariaDB](concepts-connectivity.md)
