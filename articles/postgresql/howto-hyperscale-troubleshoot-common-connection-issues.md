---
title: Felsöka anslutningar – Hyperskala (Citus) - Azure-databas för PostgreSQL
description: Lär dig hur du felsöker anslutningsproblem till Azure Database for PostgreSQL - Hyperscale (Citus)
keywords: postgresql-anslutning,anslutningssträng,anslutningsproblem,tillfälligt fel,anslutningsfel
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: c064aca484f85c44dada9888012140784a96863f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977513"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Felsöka anslutningsproblem till Azure Database för PostgreSQL - Hyperskala (Citus)

Anslutningsproblem kan orsakas av flera saker, till exempel:

* Brandväggsinställningar
* Time-out för anslutning
* Felaktig inloggningsinformation
* Anslutningsgränsen har nåtts för servergrupp
* Problem med tjänstens infrastruktur
* Underhåll av service
* Koordinatorn noden misslyckas över till ny hårdvara

I allmänhet kan anslutningsproblem till hyperskala klassificeras enligt följande:

* Tillfälliga fel (kortlivade eller intermittenta)
* Beständiga eller icke-tillfälliga fel (fel som regelbundet återkommer)

## <a name="troubleshoot-transient-errors"></a>Felsöka tillfälliga fel

Tillfälliga fel uppstår av flera skäl. Den vanligaste är systemunderhåll, fel med maskinvara eller programvara och koordinatornod vCore-uppgraderingar.

Om du aktiverar hög tillgänglighet för hyperskala servergruppsnoder kan dessa typer av problem minskas automatiskt. Din ansökan bör dock fortfarande vara beredd att förlora anslutningen en kort stund. Även andra händelser kan ta längre tid att minska, till exempel när en stor transaktion orsakar en tidskrävande återställning.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Åtgärder för att lösa tillfälliga anslutningsproblem

1. Kontrollera [microsoft Azure Service Dashboard](https://azure.microsoft.com/status) för alla kända avbrott som inträffade under den tid då programmet rapporterade fel.
2. Program som ansluter till en molntjänst som Hyperscale (Citus) bör förvänta sig tillfälliga fel och reagera smidigt. Program bör till exempel implementera logik för återförsök för att hantera dessa fel i stället för att visa dem som programfel för användare.
3. När servergruppen närmar sig sina resursgränser kan fel verka som tillfälliga anslutningsproblem. Om du ökar nod-RAM-minnet eller lägger till arbetsnoder och omfördelningsdata kan det hjälpa.
4. Om anslutningsproblemen fortsätter, eller varar längre än 60 sekunder, eller inträffar mer än en gång per dag, lämnar du in en Azure-supportbegäran genom att välja **Hämta support** på [Azure Support-webbplatsen.](https://azure.microsoft.com/support/options)

## <a name="troubleshoot-persistent-errors"></a>Felsöka beständiga fel

Om programmet beständigt misslyckas med att ansluta till Hyperscale (Citus), är de vanligaste orsakerna brandväggsfelkonfiguration eller användarfel.

* Konfiguration av koordinatornodens brandvägg: Kontrollera att brandväggen för hyperskalaserver är konfigurerad för att tillåta anslutningar från klienten, inklusive proxyservrar och gateways.
* Klientbrandväggskonfiguration: Brandväggen på klienten måste tillåta anslutningar till databasservern. Vissa brandväggar kräver att inte bara program efter namn, men tillåter IP-adresser och portar på servern.
* Användarfel: Dubbelkolla anslutningssträngen. Du kan ha felskrivna parametrar som servernamnet. Du kan hitta anslutningssträngar för olika språkramverk och psql i Azure-portalen. Gå till sidan **Anslutningssträngar** i servergruppen Hyperscale (Citus). Tänk också på att Citus-kluster (Hyperscale) bara har en databas och att dess fördefinierade namn är **citus**.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Åtgärder för att lösa beständiga anslutningsproblem

1. Ställ in [brandväggsregler](howto-hyperscale-manage-firewall-using-portal.md) för att tillåta klient-IP-adressen. Endast för tillfälliga tester ställer du in en brandväggsregel med 0.0.0.0 som start-IP-adress och använder 255.255.255.255 som slut-IP-adress. Den regeln öppnar servern för alla IP-adresser. Om regeln löser anslutningsproblemet tar du bort den och skapar en brandväggsregel för en lämpligt begränsad IP-adress eller ett adressintervall.
2. På alla brandväggar mellan klienten och Internet kontrollerar du att port 5432 är öppen för utgående anslutningar.
3. Kontrollera anslutningssträngen och andra anslutningsinställningar.
4. Kontrollera tjänstens hälsotillstånd på instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

* Lär dig begreppen [brandväggsregler i Azure Database för PostgreSQL - Hyperskala (Citus)](concepts-hyperscale-firewall-rules.md)
* Se hur du [hanterar brandväggsregler för Azure Database för PostgreSQL - Hyperscale (Citus)](howto-hyperscale-manage-firewall-using-portal.md)
