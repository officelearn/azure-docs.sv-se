---
title: Felsöka anslutningar – Azure Database för PostgreSQL - Single Server
description: Lär dig hur du felsöker anslutningsproblem till Azure Database för PostgreSQL - Single Server.
keywords: postgresql-anslutning,anslutningssträng,anslutningsproblem,tillfälligt fel,anslutningsfel
author: jasonwhowell
ms.author: jasonh
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 593dc060ff92c7f4bc338e235dd1a0dd9f303ab3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767612"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---single-server"></a>Felsöka anslutningsproblem till Azure Database för PostgreSQL - Single Server

Anslutningsproblem kan orsakas av en mängd olika saker, bland annat:

* Brandväggsinställningar
* Time-out för anslutning
* Felaktig inloggningsinformation
* Maximal gräns som uppnåtts för vissa Azure-databas för PostgreSQL-resurser
* Problem med tjänstens infrastruktur
* Underhåll som utförs i tjänsten
* Beräkningsallokeringen av servern ändras genom att antalet virtuella kärnor skalas eller flyttas till en annan tjänstnivå

I allmänhet kan anslutningsproblem till Azure Database för PostgreSQL klassificeras enligt följande:

* Tillfälliga fel (kortlivade eller intermittenta)
* Beständiga eller icke-tillfälliga fel (fel som regelbundet återkommer)

## <a name="troubleshoot-transient-errors"></a>Felsöka tillfälliga fel

Tillfälliga fel uppstår när underhåll utförs, systemet stöter på ett fel med maskinvaran eller programvaran eller du ändrar serverns virtuella kärnor eller tjänstnivå. Azure Database for PostgreSQL-tjänsten har inbyggd hög tillgänglighet och är utformad för att minska dessa typer av problem automatiskt. Programmet förlorar dock anslutningen till servern under en kort tidsperiod på normalt mindre än 60 sekunder som mest. Vissa händelser kan ibland ta längre tid att minska, till exempel när en stor transaktion orsakar en tidskrävande återställning.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Åtgärder för att lösa tillfälliga anslutningsproblem

1. Kontrollera [Microsoft Azure Service Dashboard](https://azure.microsoft.com/status) för alla kända avbrott som inträffade under den tid då felen rapporterades av programmet.
2. Program som ansluter till en molntjänst som Azure Database for PostgreSQL bör förvänta sig tillfälliga fel och implementera logik för återförsök för att hantera dessa fel i stället för att visa dessa som programfel för användare. Granska [Hantering av tillfälliga anslutningsfel för Azure Database för PostgreSQL](concepts-connectivity.md) för metodtips och designriktlinjer för hantering av tillfälliga fel.
3. När en server närmar sig sina resursgränser kan fel verka vara tillfälliga anslutningsproblem. Se [Begränsningar i Azure Database för PostgreSQL](concepts-limits.md).
4. Om anslutningsproblemen fortsätter, eller om den varaktighet som ditt program stöter på felet överskrider 60 sekunder eller om du ser flera förekomster av felet under en viss dag, lämnar du in en Azure-supportbegäran genom att välja **Hämta support** på [Azure-supportwebbplatsen.](https://azure.microsoft.com/support/options)

## <a name="troubleshoot-persistent-errors"></a>Felsöka beständiga fel

Om programmet beständigt misslyckas med att ansluta till Azure Database för PostgreSQL, anger det vanligtvis ett problem med något av följande:

* Konfiguration av serverbrandvägg: Kontrollera att Azure Database for PostgreSQL-serverbrandväggen är konfigurerad för att tillåta anslutningar från klienten, inklusive proxyservrar och gateways.
* Klientbrandväggskonfiguration: Brandväggen på klienten måste tillåta anslutningar till databasservern. IP-adresser och portar på servern som du inte kan tillåtas samt programnamn som PostgreSQL i vissa brandväggar.
* Användarfel: Du kan ha felskrivna anslutningsparametrar, till exempel servernamnet i anslutningssträngen eller ett * \@saknat servernamnssuffix* i användarnamnet.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Åtgärder för att lösa beständiga anslutningsproblem

1. Ställ in [brandväggsregler](howto-manage-firewall-using-portal.md) för att tillåta klient-IP-adressen. Endast för tillfälliga tester ställer du in en brandväggsregel med 0.0.0.0 som start-IP-adress och använder 255.255.255.255 som slut-IP-adress. Då öppnas servern för alla IP-adresser. Om detta löser anslutningsproblemet tar du bort den här regeln och skapar en brandväggsregel för en lämpligt begränsad IP-adress eller adressintervall.
2. På alla brandväggar mellan klienten och Internet kontrollerar du att port 5432 är öppen för utgående anslutningar.
3. Kontrollera anslutningssträngen och andra anslutningsinställningar.
4. Kontrollera tjänstens hälsotillstånd på instrumentpanelen. Om du tror att det finns ett regionalt avbrott läser du [Översikt över affärskontinuitet med Azure Database för PostgreSQL](concepts-business-continuity.md) för steg att återställa till en ny region.

## <a name="next-steps"></a>Nästa steg

* [Hantering av tillfälliga anslutningsfel för Azure Database för PostgreSQL](concepts-connectivity.md)
