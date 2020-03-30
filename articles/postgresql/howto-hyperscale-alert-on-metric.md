---
title: Konfigurera aviseringar – hyperskala (Citus) - Azure-databas för PostgreSQL
description: I den här artikeln beskrivs hur du konfigurerar och kommer åt måttaviseringar för Azure Database for PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: 80c061f72ce827df8f8354a5881c032c6f874fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063148"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Använd Azure-portalen för att ställa in aviseringar om mått för Azure Database för PostgreSQL - Hyperscale (Citus)

Den här artikeln visar hur du konfigurerar Azure Database för PostgreSQL-aviseringar med Azure-portalen. Du kan få en avisering baserat på [övervakningsmått](concepts-hyperscale-monitoring.md) för dina Azure-tjänster.

Vi ställer in en avisering för att utlösa när värdet för ett angivet mått överskrider ett tröskelvärde. Aviseringen utlöses när villkoret först uppfylls och fortsätter att utlösas efteråt.

Du kan konfigurera en avisering så att du utför följande åtgärder när den utlöses:
* Skicka e-postmeddelanden till tjänstadministratören och coadministratorerna.
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* Ring en webhook.

Du kan konfigurera och få information om varningsregler med hjälp av:
* [Azure-portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [REST-API:et för Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Skapa en aviseringsregel för ett mått från Azure-portalen
1. I [Azure-portalen](https://portal.azure.com/)väljer du den Azure-databas för PostgreSQL-server som du vill övervaka.

2. Under **avsnittet Övervakning** i sidofältet väljer du **Aviseringar** som visas:

   ![Välj varningsregler](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. Välj **Ny varningsregel** (+ ikon).

4. Sidan **Skapa regel** öppnas enligt nedan. Fyll i den information som krävs:

   ![Lägg till måttaviseringsformulär](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. Välj **Lägg till**i avsnittet **Villkor** .

6. Välj ett mått i listan med signaler som ska aviseras. I det här exemplet väljer du "Lagringsprocent".
   
   ![Välj mått](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. Konfigurera varningslogiken:

    * **Operatör** (ex. "Större än")
    * **Tröskelvärde** (t.ex. 85 procent)
    * **Aggregeringsgranularitetsmängden** för måttregeln måste uppfyllas innan aviseringen utlöser (t.ex. "Under de senaste 30 minuterna")
    * och **utvärderingsfrekvens** (t.ex. "1 minut")
   
   Välj **Klar** när du är klar.

   ![Välj mått](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. I avsnittet **Åtgärdsgrupper** väljer du **Skapa ny** om du vill skapa en ny grupp för att ta emot aviseringar i aviseringen.

9. Fyll i formuläret "Lägg till åtgärdsgrupp" med namn, kortnamn, prenumeration och resursgrupp.

    ![Åtgärdsgrupp](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. Konfigurera en åtgärdstyp **för e-post/SMS/Push/Voice.**
    
    Välj "E-post azure Resource Manager-roll" om du vill skicka meddelanden till prenumerationsägare, deltagare och läsare.
   
    Välj **OK** när du är klar.

    ![Åtgärdsgrupp](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. Ange ett aviseringsregelnamn, beskrivning och allvarlighetsgrad.

    ![Åtgärdsgrupp](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. Välj **Skapa aviseringsregel** om du vill skapa aviseringen.

    Inom några minuter är aviseringen aktiv och utlöser som tidigare beskrivits.

### <a name="managing-alerts"></a>Hantera aviseringar

När du har skapat en avisering kan du markera den och göra följande:

* Visa ett diagram som visar tröskelvärdet för mått och de faktiska värdena från föregående dag som är relevanta för den här aviseringen.
* **Redigera** eller **ta bort** varningsregeln.
* **Inaktivera** eller **Aktivera** aviseringen om du tillfälligt vill stoppa eller återuppta mottagningsmeddelanden.

## <a name="suggested-alerts"></a>Föreslagna aviseringar

### <a name="disk-space"></a>Diskutrymme

Övervakning och avisering är viktigt för varje distribution Hyperscale (Citus) servergrupp. Den underliggande PostgreSQL-databasen kräver ledigt diskutrymme för att fungera korrekt. Om disken blir full kopplas databasservernoden från och vägrar att starta förrän det finns ledigt. Då krävs en Microsoft-supportbegäran för att åtgärda situationen.

Vi rekommenderar att du ställer in diskutrymmesaviseringar på varje nod i varje servergrupp, även för icke-produktionsanvändning. Diskutrymmesanvändningsvarningar ger den förvarning som behövs för att ingripa och hålla noder felfria. Bäst resultat får du om du provar en serie aviseringar med 75 %, 85 % och 95 % användning. Hur många procent som ska väljas beror på datainmatningshastigheten, eftersom snabbt datainmatning fyller upp disken snabbare.

När disken närmar sig sin utrymmesgräns kan du prova dessa tekniker för att få mer ledigt utrymme:

* Granska datalagringsprincipen. Flytta äldre data till kyllagring om möjligt.
* Överväg att lägga till [noder](howto-hyperscale-scaling.md#add-worker-nodes) i servergruppen och balansera om shards. Ombalansering distribuerar data över fler datorer.
* Överväg [att öka kapaciteten för](howto-hyperscale-scaling.md#increase-or-decrease-vcores-on-nodes) arbetsnoder. Varje arbetare kan ha upp till 2 TiB lagringsutrymme. Men att lägga till noder bör göras innan du ändrar storlek på noder eftersom lägger till noder slutförs snabbare.

### <a name="cpu-usage"></a>CPU-användning

Övervakning av CPU-användning är användbart för att upprätta en baslinje för prestanda. Du kanske till exempel märker att CPU-användningen vanligtvis är runt 40-60%. Om CPU-användningen plötsligt börjar sväva runt 95%, kan du känna igen en anomali. CPU-användningen kan återspegla organisk tillväxt, men det kan också avslöja en förlupen fråga. När du skapar en CPU-avisering ställer du in en lång aggregeringsgranularitet för att fånga långvariga ökningar och ignorera tillfälliga spikar.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [hur du konfigurerar webhooks i aviseringar](../azure-monitor/platform/alerts-webhooks.md).
* Få en [översikt över statistiksamlingen](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) för att se till att din tjänst är tillgänglig och svarar.
