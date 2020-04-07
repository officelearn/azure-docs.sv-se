---
title: Använda belastningsutjämningstjänster i Azure | Microsoft-dokument
description: 'Den här självstudien visar hur du skapar ett scenario med hjälp av Azure load-balancing portfolio: Traffic Manager, Application Gateway och Load Balancer.'
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: rohink
ms.openlocfilehash: cccd4a6b0b52608a6a17b73688e18f27088df5b0
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757201"
---
# <a name="using-load-balancing-services-in-azure"></a>Använda belastningsutjämningstjänster i Azure

## <a name="introduction"></a>Introduktion

Microsoft Azure tillhandahåller flera tjänster för att hantera hur nätverkstrafiken distribueras och belastningsutjämnas. Du kan använda dessa tjänster individuellt eller kombinera deras metoder, beroende på dina behov, för att bygga den optimala lösningen.

I den här självstudien definierar vi först ett kundansökningsfall och ser hur det kan göras mer robust och högpresterande med hjälp av följande Azure-belastningsutjämningsportfölj: Traffic Manager, Application Gateway och Load Balancer. Vi tillhandahåller sedan steg-för-steg-instruktioner för att skapa en distribution som är geografiskt redundant, distribuerar trafik till virtuella datorer och hjälper dig att hantera olika typer av begäranden.

På konceptnivå spelar var och en av dessa tjänster en särskild roll i belastningsutjämningshierarkin.

* **Traffic Manager** ger global DNS-belastningsutjämning. Den tittar på inkommande DNS-begäranden och svarar med en felfri slutpunkt, i enlighet med den routningsprincip som kunden har valt. Alternativ för routningsmetoder är:
  * Prestandaroutning för att skicka beställaren till närmaste slutpunkt när det gäller svarstid.
  * Prioriterad routning för att dirigera all trafik till en slutpunkt, med andra slutpunkter som säkerhetskopiering.
  * Viktad rundhakardirigering, som distribuerar trafik baserat på den viktning som tilldelas varje slutpunkt.
  * Geografibaserad routning för att distribuera trafiken till programslutpunkterna baserat på användarens geografiska plats.
  * Undernätsbaserad routning för att distribuera trafiken till programslutpunkterna baserat på användarens undernät (IP-adressintervall).
  * Routning med flera värden som gör att du kan skicka IP-adresser för mer än en programslutpunkter i ett enda DNS-svar.

  Klienten ansluter direkt till slutpunkten som returneras av Traffic Manager. Azure Traffic Manager identifierar när en slutpunkt är felaktig och omdirigerar sedan klienterna till en annan felfri instans. Läs [Dokumentationen](traffic-manager-overview.md) till Azure Traffic Manager om du vill veta mer om tjänsten.
* **Application Gateway** tillhandahåller ADC (Application Delivery Controller) som en tjänst som erbjuder olika load-balancing-funktioner för Layer 7 för ditt program. Det gör det möjligt för kunder att optimera webbgruppens produktivitet genom att avlasta CPU-intensiva TLS-avslutning till programgatewayen. Andra routningsfunktioner för Layer 7 inkluderar distribution av round-robin av inkommande trafik, cookie-baserad sessionstillhörighet, URL-sökvägsbaserad routning och möjligheten att vara värd för flera webbplatser bakom en enda programgateway. Application Gateway kan konfigureras som en Internet-vänd gateway, en intern-bara gateway, eller en kombination av båda. Application Gateway är helt Azure-hanterad, skalbar och högtillgänglig. För att få en bättre hantering ingår en omfattande uppsättning diagnostik- och loggningsfunktioner.
* **Belastningsutjämnare** är en integrerad del av Azure SDN-stacken, vilket ger högpresterande layer 4-belastningsutjämningstjänster med låg latens för alla UDP- och TCP-protokoll. Den hanterar inkommande och utgående anslutningar. Du kan konfigurera offentliga och interna belastningsutjämnade slutpunkter och definiera regler för att mappa inkommande anslutningar till serverdelspooldestinationer med TCP- och HTTP-avsökningsalternativ för att hantera tjänstens tillgänglighet.

## <a name="scenario"></a>Scenario

I det här exemplet använder vi en enkel webbplats som betjänar två typer av innehåll: bilder och dynamiskt renderade webbsidor. Webbplatsen måste vara geografiskt överflödig och den ska betjäna sina användare från den närmaste (lägsta latensen) till dem. Programutvecklaren har beslutat att alla webbadresser som matchar mönstret /images/* ska serveras från en särskild pool med virtuella datorer som skiljer sig från resten av webbgruppen.

Dessutom måste den virtuella standardpoolen för virtuella datorer som betjänar det dynamiska innehållet prata med en backend-databas som finns i ett kluster med hög tillgänglighet. Hela distributionen har konfigurerats via Azure Resource Manager.

Med Traffic Manager, Application Gateway och Load Balancer kan den här webbplatsen uppnå dessa designmål:

* **Multi-geo redundans**: Om en region går ner, Trafikhanteraren dirigerar trafik sömlöst till närmaste region utan någon åtgärd från programmets ägare.
* **Minskad latens**: Eftersom Traffic Manager automatiskt dirigerar kunden till den närmaste regionen, upplever kunden lägre svarstid när du begär webbsidans innehåll.
* **Oberoende skalbarhet**: Eftersom webbprogramarbetsbelastningen är avgränsad efter typ av innehåll kan programägaren skala arbetsbelastningarna för begäran oberoende av varandra. Application Gateway säkerställer att trafiken dirigeras till rätt pooler baserat på de angivna reglerna och hälsotillståndet för programmet.
* **Intern belastningsutjämning**: Eftersom belastningsutjämnaren ligger framför klustret med hög tillgänglighet exponeras endast den aktiva och felfria slutpunkten för en databas för programmet. Dessutom kan en databasadministratör optimera arbetsbelastningen genom att distribuera aktiva och passiva repliker över klustret oberoende av klientprogrammet. Belastningsutjämnaren levererar anslutningar till klustret med hög tillgänglighet och säkerställer att endast felfria databaser får anslutningsbegäranden.

Följande diagram visar arkitekturen i det här scenariot:

![Diagram över bärande arkitektur](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Det här exemplet är bara en av många möjliga konfigurationer av belastningsutjämningstjänster som Azure erbjuder. Traffic Manager, Application Gateway och Load Balancer kan blandas och matchas för att bäst passa dina belastningsutjämningsbehov. Om TLS-avlastning eller layer 7-bearbetning till exempel inte är nödvändig kan belastningsutjämning användas i stället för Application Gateway.

## <a name="setting-up-the-load-balancing-stack"></a>Ställa in lastbalanseringsstacken

### <a name="step-1-create-a-traffic-manager-profile"></a>Steg 1: Skapa en Traffic Manager-profil

1. Klicka på Skapa **en resurs** > **Nätverksansvarig** > **i Traffic Manager-profilen** > **Skapa**i Azure-portalen.
2. Ange följande grundläggande information:

   * **Namn**: Ge din Traffic Manager-profil ett DNS-prefixnamn.
   * **Routningsmetod**: Välj principen för trafikdirigeringsmetod. Mer information om metoderna finns i [Om trafikhanterarens trafikroutningsmetoder](traffic-manager-routing-methods.md).
   * **Prenumeration**: Välj den prenumeration som innehåller profilen.
   * **Resursgrupp**: Välj den resursgrupp som innehåller profilen. Det kan vara en ny eller befintlig resursgrupp.
   * **Resursgruppsplats**: Traffic Manager-tjänsten är global och inte bunden till en plats. Du måste dock ange en region för den grupp där metadata som är associerade med Traffic Manager-profilen finns. Den här platsen påverkar inte profilens körningstillgänglighet.

3. Klicka på **Skapa** om du vill generera Traffic Manager-profilen.

   !["Skapa Trafikhanteraren"](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Steg 2: Skapa programgateways

1. Klicka på **Skapa en resurs** > **Nätverkstillgång** > **Application Gateway**i Azure-portalen i den vänstra rutan .
2. Ange följande grundläggande information om programgatewayen:

   * **Namn**: Namnet på programgatewayen.
   * **SKU-storlek:** Storleken på programgatewayen, tillgänglig som Small, Medium eller Large.
   * **Antal instanser**: Antalet instanser, ett värde från 2 till 10.
   * **Resursgrupp**: Resursgruppen som innehåller programgatewayen. Det kan vara en befintlig resursgrupp eller en ny.
   * **Plats**: Regionen för programgatewayen, som är samma plats som resursgruppen. Platsen är viktig eftersom det virtuella nätverket och den offentliga IP-adressen måste vara på samma plats som gatewayen.
3. Klicka på **OK**.
4. Definiera konfigurationer för virtuellt nätverk, undernät, klientslut-IP och lyssnare för programgatewayen. I det här fallet är frontend IP-adressen **Offentlig**, vilket gör att den kan läggas till som en slutpunkt i Traffic Manager-profilen senare.
5. Konfigurera lyssnaren med något av följande alternativ:
    * Om du använder HTTP finns det inget att konfigurera. Klicka på **OK**.
    * Om du använder HTTPS krävs ytterligare konfiguration. Se [Skapa en programgateway](../application-gateway/application-gateway-create-gateway-portal.md)med början i steg 9. När du har slutfört konfigurationen klickar du på **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Konfigurera URL-routning för programgateways

När du väljer en backend-pool tar en programgateway som är konfigurerad med en sökvägsbaserad regel ett sökvägsmönster för begärande-URL:en utöver distribution av avrundningsenheter. I det här fallet lägger vi till en sökvägsbaserad\*regel för att dirigera en URL med "/images/ " till bildserverpoolen. Mer information om hur du konfigurerar URL-sökvägsbaserad routning för en programgateway finns i [Skapa en sökvägsbaserad regel för en programgateway](../application-gateway/application-gateway-create-url-route-portal.md).

![Diagram på webbnivå för programgateway](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Gå till instansen av programgatewayen som du skapade i föregående avsnitt från resursgruppen.
2. Under **Inställningar**väljer du **Backend-pooler**och väljer sedan **Lägg till** för att lägga till de virtuella datorer som du vill associera med backend-poolerna på webbnivå.
3. Ange namnet på backend-poolen och alla IP-adresser till de datorer som finns i poolen. I det här fallet ansluter vi två backend-serverpooler med virtuella datorer.

   ![Application Gateway "Lägg till backend pool"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Under **Inställningar** för programgatewayen väljer du **Regler**och klickar sedan på knappen **Sökvägsbaserad** för att lägga till en regel.

   ![Knappen "Sökvägsbaserade" för programgateway](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Konfigurera regeln genom att ange följande information.

   Grundläggande inställningar:

   + **Namn**: Det egna namnet på regeln som är tillgänglig i portalen.
   + **Lyssnare**: Lyssnaren som används för regeln.
   + **Standardpool för backend:** Den backend-pool som ska användas med standardregeln.
   + **Standard-HTTP-inställningar:** DE HTTP-inställningar som ska användas med standardregeln.

   Sökvägsbaserade regler:

   + **Namn**: Det egna namnet på den sökvägsbaserade regeln.
   + **Sökvägar**: Sökvägsregeln som används för att vidarebefordra trafik.
   + **Backend Pool**: Back-end pool som ska användas med den här regeln.
   + **HTTP-inställning**: DE HTTP-inställningar som ska användas med den här regeln.

   > [!IMPORTANT]
   > Sökvägar: Giltiga banor måste börja med "/". Jokertecknet "\*" är endast tillåtet i slutet. Giltiga exempel är /xyz,\*/xyz eller\*/xyz/ .

   ![Bladet "Lägg till sökvägsbaserad regel"](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Steg 3: Lägga till programgateways till Traffic Manager-slutpunkterna

I det här fallet är Traffic Manager ansluten till programgateways (som konfigurerats i föregående steg) som finns i olika regioner. Nu när programgateways är konfigurerade är nästa steg att ansluta dem till traffic manager-profilen.

1. Öppna din Traffic Manager-profil. Det gör du genom att titta i resursgruppen eller söka efter namnet på Traffic Manager-profilen från **Alla resurser**.
2. Välj **Slutpunkter**i den vänstra rutan och klicka sedan på **Lägg** till för att lägga till en slutpunkt.

   ![Knappen Traffic Manager-slutpunkter "Lägg till"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Skapa en slutpunkt genom att ange följande information:

   * **Typ:** Välj vilken typ av slutpunkt som ska belastningssaldo. I det här fallet väljer **Azure slutpunkt** eftersom vi ansluter den till programgateway instanser som har konfigurerats tidigare.
   * **Namn**: Ange namnet på slutpunkten.
   * **Målresurstyp:** Välj **Offentlig IP-adress** och välj sedan den offentliga IP-adressen för den offentliga IP-adressen för den programgateway som konfigurerades tidigare under **Målresurs.**

   ![Traffic Manager "Lägg till slutpunkt"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Nu kan du testa installationen genom att komma åt den med DNS för din Traffic Manager-profil (i det här exemplet: TrafficManagerScenario.trafficmanager.net). Du kan skicka begäranden igen, ta bort eller sänka virtuella datorer och webbservrar som har skapats i olika regioner och ändra profilinställningarna för Traffic Manager för att testa konfigurationen.

### <a name="step-4-create-a-load-balancer"></a>Steg 4: Skapa en belastningsutjämnare

I det här fallet distribuerar Load Balancer anslutningar från webbnivån till databaserna i ett kluster med hög tillgänglighet.

Om databasklustret med hög tillgänglighet använder SQL Server AlwaysOn läser du [Konfigurera en eller flera gruppavlyssnare för](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) stegvisa instruktioner.

Mer information om hur du konfigurerar en intern belastningsutjämnare finns [i Skapa en intern belastningsutjämnare i Azure-portalen](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. Klicka på Skapa en > **belastningsutjämnad** > **Load balancer** **resurs**i Azure-portalen i den vänstra rutan.
2. Välj ett namn på din belastningsutjämnare.
3. Ange **typen** till **Intern**och välj lämpligt virtuellt nätverk och undernät för att belastningsutjämnaren ska finnas i.
4. Under **IP-adresstilldelning**väljer du antingen **Dynamisk** eller **Statisk**.
5. Under **Resursgrupp**väljer du resursgruppen för belastningsutjämnaren.
6. Under **Plats**väljer du lämplig region för belastningsutjämnaren.
7. Klicka på **Skapa** om du vill generera belastningsutjämnaren.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Ansluta en backend-databasnivå till belastningsutjämnaren

1. Leta reda på den belastningsutjämnare som skapades i föregående steg från resursgruppen.
2. Klicka på **Backend-pooler**under **Inställningar**och klicka sedan på **Lägg** till för att lägga till en backend-pool.

   ![Belastningsutjämnaren "Lägg till backend pool"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Ange namnet på backend-poolen.
4. Lägg till antingen enskilda datorer eller en tillgänglighetsuppsättning till backend-poolen.

#### <a name="configure-a-probe"></a>Konfigurera en avsökning

1. Välj **Avsökningar**under **Inställningar**i lastjämnaren och klicka sedan på **Lägg** till för att lägga till en avsökning.

   ![Belastningsutjämnaren "Lägg till sond"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Ange namnet på avsökningen.
3. Välj **protokollet** för avsökningen. För en databas kanske du vill ha en TCP-avsökning i stället för en HTTP-avsökning. Mer information om belastningsutjämnare avsökningar finns i [Förstå belastningsutjämnare](../load-balancer/load-balancer-custom-probe-overview.md).
4. Ange den **port** för databasen som ska användas för att komma åt avsökningen.
5. Under **Intervall**anger du hur ofta programmet ska avsökas.
6. Under **Felfritt tröskelvärde**anger du antalet kontinuerliga avsökningsfel som måste uppstå för att den virtuella datorn för serverdel ska anses vara fel.
7. Klicka på **OK** för att skapa avsökningen.

#### <a name="configure-the-load-balancing-rules"></a>Konfigurera belastningsutjämningsregler

1. Under **Inställningar** för lastjämningsutjämnaren väljer du **Belastningsutjämningsregler**och klickar sedan på **Lägg till** för att skapa en regel.
2. Ange **namnet** för belastningsutjämningsregeln.
3. Välj **ip-adressen för frontend** för belastningsutjämnaren, **protokollet**och **porten**.
4. Under **Backend-port**anger du den port som ska användas i backend-poolen.
5. Välj den **serverdpool** och den **avsökning** som skapades i föregående steg för att tillämpa regeln på.
6. Under **Sessionsbeständighet**väljer du hur du vill att sessionerna ska bestå.
7. Under **Tidsutgång på inaktiv**utskrift anger du antalet minuter före en inaktiv timeout.
8. Under **Flytande IP**väljer du antingen **Inaktiverad** eller **Aktiverad**.
9. Skapa regeln genom att klicka på **OK**.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Steg 5: Anslut virtuella datorer på webbnivå till belastningsutjämnaren

Nu konfigurerar vi IP-adressen och belastningsutjämnarfrontend port i de program som körs på dina virtuella datorer på webbnivå för alla databasanslutningar. Den här konfigurationen är specifik för de program som körs på dessa virtuella datorer. Information om hur du konfigurerar mål-IP-adressen och -porten finns i programdokumentationen. Om du vill hitta IP-adressen för klientdelen går du till ip-poolen i frontend-poolen i **inställningarna för belastningsutjämnare**.

![Läsutjämningsmallen "Frontend IP pool" navigeringsfönstret](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Nästa steg

* [Översikt över Traffic Manager](traffic-manager-overview.md)
* [Översikt över Application Gateway](../application-gateway/application-gateway-introduction.md)
* [Översikt över Azure Load Balancer](../load-balancer/load-balancer-overview.md)
