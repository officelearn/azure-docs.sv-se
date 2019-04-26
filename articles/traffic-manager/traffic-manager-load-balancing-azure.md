---
title: Med belastningsutjämning tjänster i Azure | Microsoft Docs
description: 'Den här självstudien visar hur du skapar ett scenario med hjälp av Azure-portföljen belastningsutjämning: Traffic Manager, Application Gateway och belastningsutjämnare.'
services: traffic-manager
documentationcenter: ''
author: liumichelle
manager: dkays
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: limichel
ms.openlocfilehash: 906e1840f35ab14997c727551b893a0219eb78d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60330574"
---
# <a name="using-load-balancing-services-in-azure"></a>Med belastningsutjämning tjänster i Azure

## <a name="introduction"></a>Introduktion

Microsoft Azure tillhandahåller flera tjänster för att hantera hur nätverkstrafiken distribueras och belastningsutjämning. Du kan använda dessa tjänster separat eller kombinera deras metoder, beroende på dina behov för att skapa den bästa lösningen.

I de här självstudierna vi först definiera ett kund-användningsfall och se hur det kan göras mer robust och har bättre prestanda genom att använda följande Azure-belastningsutjämning-portföljen: Traffic Manager, Application Gateway och belastningsutjämnare. Vi ger sedan stegvisa instruktioner för att skapa en distribution som är geografiskt redundant, distribuerar trafik till virtuella datorer och hjälper dig att hantera olika typer av begäranden.

På en konceptuell nivå spelar var och en av dessa tjänster en specifik roll i hierarkin för belastningsutjämning.

* **Traffic Manager** tillhandahåller globala DNS belastningsutjämning. Den tittar på DNS-förfrågningar och svarar med en felfri slutpunkt, i enlighet med routningsprincip som kunden har valts. Alternativ för routningsmetoder är:
  * Prestanda routning för att skicka begäranden till närmaste slutpunkten när det gäller svarstider.
  * Prioritet routning för att dirigera all trafik till en slutpunkt med andra slutpunkter som reserv.
  * Viktad resursallokering routning, som distribuerar trafik baserat på det värde som är tilldelad till varje slutpunkt.
  * Geografisk plats-baserad routning för att distribuera trafik till slutpunkterna för din baserat på användarens geografiska plats.
  * Undernät-baserad routning för att distribuera trafik till slutpunkterna för din baserat på undernät (IP-adressintervall) för användaren.
  * Kan ha flera värden routning som gör att du kan skicka IP-adresserna för slutpunkterna för fler än en i ett enda DNS-svar.

  Klienten ansluter direkt till den slutpunkt som returneras av Traffic Manager. Med Azure Traffic Manager identifierar när en slutpunkt har dåligt hälsotillstånd och sedan omdirigeras klienterna till en annan felfri instans. Referera till [dokumentation om Azure Traffic Manager](traffic-manager-overview.md) mer information om tjänsten.
* **Application Gateway** erbjuder application Deliver controller (ADC) som en tjänst, som erbjuder olika Layer 7 belastningsutjämningsfunktioner för ditt program. Den låter kunder optimera webbservergruppens produktivitet genom att avlasta CPU-intensiva SSL-avslutning till application gateway. Andra Layer 7-routningsfunktioner omfattar resursallokeringsdistribution av inkommande trafik, Cookiebaserad sessionstillhörighet, URL-sökvägsbaserad Routning och möjligheten att vara värd för flera webbplatser bakom en enda application gateway. Application Gateway kan konfigureras som en internetuppkopplad gateway, en endast intern gateway eller en kombination av båda. Application Gateway är helt Azure hanterad, skalbar och högtillgänglig. För att få en bättre hantering ingår en omfattande uppsättning diagnostik- och loggningsfunktioner.
* **Belastningsutjämnare** är en del av Azure SDN-stacken, vilket ger hög prestanda och låg latens Layer 4-belastningsutjämning tjänster för alla UDP och TCP-protokoll. Den hanterar inkommande och utgående anslutningar. Du kan konfigurera offentliga och interna belastningsutjämnade slutpunkter och definiera regler för att mappa inkommande anslutningar till backend-poolen mål med hjälp av TCP- och HTTP-avsökning av hälsotillstånd alternativ för att hantera tjänstens tillgänglighet.

## <a name="scenario"></a>Scenario

I det här exemplet använder vi en enkel webbplats som arbetar för två typer av innehåll: bilder och dynamiskt återgivna webbsidor. Webbplatsen måste vara geografiskt redundant och den bör hantera dess användare från den närmaste (lägsta svarstiden) plats till dem. Programutvecklare har beslutat som de webbadresser som matchar mönstret/bilder / * hanteras från en dedikerad pool med virtuella datorer som skiljer sig från resten av gruppen.

Standard-VM-pool som betjänar dynamiskt innehåll måste dessutom prata med en backend-databas som finns på ett kluster med hög tillgänglighet. Hela distributionen har ställts in via Azure Resource Manager.

Med Traffic Manager, Application Gateway och belastningsutjämnare kan den här webbplatsen att åstadkomma detta design:

* **Flera geo-redundans**: Om en region kraschar dirigerar Traffic Manager trafik sömlöst till den närmaste regionen utan någon åtgärd från programmets ägare.
* **Snabbare svarstid**: Eftersom Traffic Manager dirigerar automatiskt kunden till den närmaste regionen, kundupplevelse lägre fördröjning när du begär innehållet webbsidan.
* **Oberoende skalbarhet**: Eftersom web programbelastningen avgränsas av typ av innehåll, kan programmets ägare skala på begäran-arbetsbelastningar som är oberoende av varandra. Application Gateway säkerställer att trafiken dirigeras till rätt poolerna baserat på de angivna reglerna och hälsotillståndet för programmet.
* **Intern belastningsutjämning**: Eftersom det är belastningsutjämnare framför klustret hög tillgänglighet, visas endast aktiva och felfri slutpunkt för en databas till programmet. Dessutom kan en databasadministratör optimera arbetsbelastningen genom att distribuera aktiva och passiva repliker över kluster oberoende av frontend-programmet. Load Balancer levererar anslutningar till kluster med hög tillgänglighet och säkerställer att endast felfria databaser får anslutningsbegäranden.

Följande diagram illustrerar arkitekturen i det här scenariot:

![Diagram över belastningsutjämning arkitektur](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Det här exemplet är endast ett av många möjliga konfigurationerna belastningsutjämning tjänster som Azure erbjuder. Traffic Manager och Application Gateway Load Balancer kan blandas och matchas som bäst passar dina behov för belastningsutjämning. Om SSL-avlastning eller Layer 7-bearbetning behövs inte, till exempel användas belastningsutjämnare i stället för Application Gateway.

## <a name="setting-up-the-load-balancing-stack"></a>Konfigurera-belastningsutjämning-stack

### <a name="step-1-create-a-traffic-manager-profile"></a>Steg 1: Skapa en Traffic Manager-profil

1. I Azure-portalen klickar du på **skapa en resurs** > **nätverk** > **Traffic Manager-profil**  >   **Skapa**.
2. Ange följande information:

   * **Namn**: Ge din Traffic Manager-profil ett DNS prefixet.
   * **Routningsmetod**: Välj princip för routning av nätverkstrafik metod. Mer information om metoderna som finns i [om Traffic Manager trafikroutningsmetoder](traffic-manager-routing-methods.md).
   * **Prenumeration**: Välj den prenumeration som innehåller profilen.
   * **Resursgrupp**: Välj den resursgrupp som innehåller profilen. Det kan vara en ny eller befintlig resursgrupp.
   * **Resursgruppsplats**: Traffic Manager-tjänsten är global och inte knuten till en plats. Du måste dock ange en region för gruppen där metadata som tillhör Traffic Manager-profilen finns. Den här platsen påverkar inte tillgängligheten för körning av profilen.

3. Klicka på **skapa** att generera Traffic Manager-profilen.

   ![Bladet ”skapa Traffic Manager”](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Steg 2: Skapa dina application gateways

1. I Azure-portalen, i den vänstra rutan klickar du på **skapa en resurs** > **nätverk** > **Application Gateway**.
2. Ange följande information om application gateway:

   * **Namn**: Namnet på application gateway.
   * **SKU-storlek**: Storleken på application gateway tillgänglig som liten, medel eller stor.
   * **Antal instanser**: Antalet instanser, ett värde mellan 2 och 10.
   * **Resursgrupp**: Den resursgrupp som innehåller application gateway. Det kan vara en befintlig resursgrupp eller en ny.
   * **Plats**: Region för application gateway, som är på samma plats som resursgruppen. Platsen är viktig, eftersom det virtuella nätverket och en offentlig IP-adress måste vara på samma plats som en gateway.
3. Klicka på **OK**.
4. Definiera virtuellt nätverk, undernät, frontend-IP och konfigurationer för lyssnare för programgatewayen. I det här scenariot frontend IP-adressen är **offentliga**, vilket gör att det måste läggas till som en slutpunkt i Traffic Manager-profilen vid ett senare tillfälle.
5. Konfigurera lyssnaren med något av följande alternativ:
    * Om du använder HTTP, finns det inget att konfigurera. Klicka på **OK**.
    * Om du använder HTTPS ytterligare krävs konfiguration. Referera till [skapa en Programgateway](../application-gateway/application-gateway-create-gateway-portal.md), från steg 9. När du har slutfört konfigurationen, klickar du på **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Konfigurera URL-routning för programgatewayer

När du väljer en serverdelspool tar en Programgateway som är konfigurerad med en sökvägsbaserad regel en sökväg mönstret för URL: en förutom resursallokering. I detta scenario lägger vi till en sökvägsbaserad regel för att dirigera alla URL: en med ”/images/\*” i serverpoolen i avbildningen. Mer information om hur du konfigurerar URL-sökvägsbaserad routning för en Programgateway avser [skapa en sökvägsbaserad regel för en Programgateway](../application-gateway/application-gateway-create-url-route-portal.md).

![Application Gateway webbnivå diagram](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Från resursgruppen, går du till instansen av application gateway som du skapade i föregående avsnitt.
2. Under **inställningar**väljer **serverdelspooler**, och välj sedan **Lägg till** att lägga till de virtuella datorerna som du vill associera med webbnivå backend-adresspooler.
3. Ange namnet på backend poolen och alla IP-adresser för de datorer som finns i poolen. I det här scenariot ansluter vi två backend-adresspooler för virtuella datorer.

   ![Application Gateway ”Lägg till serverdelspool”](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Under **inställningar** application gateway, väljer du **regler**, och klicka sedan på den **Sökvägsbaserad** för att lägga till en regel.

   ![Application Gateway regler ”sökvägsbaserad”-knappen](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Konfigurera regeln genom att ange följande information.

   Grundläggande inställningar:

   + **Namn**: Det egna namnet för regeln som är tillgänglig i portalen.
   + **Lyssnaren**: Lyssnaren som används för regeln.
   + **Standard serverdelspool**: Backend-poolen som ska användas med Standardregeln.
   + **Standardinställningar för HTTP**: HTTP-inställningar som ska användas med Standardregeln.

   Sökvägsbaserad regler:

   + **Namn**: Det egna namnet för sökvägsbaserad regel.
   + **Sökvägar**: Sökvägsregeln som används för att vidarebefordra trafik.
   + **Serverdelspool**: Backend-poolen som ska användas med den här regeln.
   + **HTTP-inställning**: HTTP-inställningar som ska användas med den här regeln.

   > [!IMPORTANT]
   > Sökvägar: Giltiga sökvägar måste börja med ”/”. Jokertecknet ”\*” tillåts endast i slutet. Giltiga exempel är /xyz /xyz\*, eller /xyz/\*.

   ![Programmet gatewaybladet ”Lägg till sökvägsbaserad regel”](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Steg 3: Lägg till application gateway slutpunkter i Traffic Manager

I det här scenariot är Traffic Manager ansluten till application gateway (som konfigurerats i föregående steg) som finns i olika regioner. Nu när programgatewayerna har konfigurerats, är nästa steg att ansluta till Traffic Manager-profilen.

1. Öppna din Traffic Manager-profil. Du gör detta genom att titta i din resursgrupp eller Sök efter namnet på Traffic Manager-profil från **alla resurser**.
2. I den vänstra rutan väljer **slutpunkter**, och klicka sedan på **Lägg till** lägga till en slutpunkt.

   ![Traffic Manager-slutpunkter ”Lägg till”-knappen](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Skapa en slutpunkt genom att ange följande information:

   * **Typ**: Välj typ av slutpunkt för att utjämna belastningen. I det här scenariot väljer **Azure-slutpunkt** eftersom vi ansluter den till application gateway-instanser som har konfigurerats tidigare.
   * **Namn**: Ange namnet på slutpunkten.
   * **Målresurstyp**: Välj **offentliga IP-adressen** och under **målresurs**, Välj offentlig IP-Adressen för application gateway som konfigurerats tidigare.

   ![Traffic Manager ”Lägg till slutpunkten”](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Nu kan du testa din konfiguration genom att öppna den med DNS för din Traffic Manager-profil (i det här exemplet: TrafficManagerScenario.trafficmanager.net). Du kan skicka begäranden, ta fram eller påverkar virtuella datorer och servrar som har skapats i olika regioner och ändra profilinställningarna för Traffic Manager-för att testa din konfiguration.

### <a name="step-4-create-a-load-balancer"></a>Steg 4: Skapa en lastbalanserare

I det här scenariot distribuerar belastningsutjämnaren anslutningar från webbnivån till databaserna i ett kluster med hög tillgänglighet.

Om ditt kluster med hög tillgänglighet databasen använder SQL Server AlwaysOn, referera till [konfigurera en eller flera alltid Tillgänglighetsgrupplyssnare](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) stegvisa instruktioner.

Läs mer om hur du konfigurerar en intern belastningsutjämnare, [skapa en intern belastningsutjämnare i Azure-portalen](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. I Azure-portalen, i den vänstra rutan klickar du på **skapa en resurs** > **nätverk** > **belastningsutjämnaren**.
2. Välj ett namn för belastningsutjämnaren.
3. Ange den **typ** till **internt**, och väljer rätt virtuellt nätverk och undernät för att belastningsutjämnaren ska finnas i.
4. Under **IP-adresstilldelning**, väljer du antingen **dynamisk** eller **statiska**.
5. Under **resursgrupp**, Välj resursgruppen för belastningsutjämnaren.
6. Under **plats**, väljer du rätt region för belastningsutjämnaren.
7. Klicka på **skapa** att generera belastningsutjämnaren.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Ansluta en backend-databasnivå till belastningsutjämnaren

1. Hitta från resursgruppen, belastningsutjämnaren som skapades i föregående steg.
2. Under **inställningar**, klickar du på **serverdelspooler**, och klicka sedan på **Lägg till** att lägga till en backend-adresspoolen.

   ![Belastningsutjämnaren ”Lägg till serverdelspool”](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Ange namnet på backend poolen.
4. Lägga till enskilda datorer eller en tillgänglighetsuppsättning till backend-poolen.

#### <a name="configure-a-probe"></a>Konfigurera en avsökning

1. I en belastningsutjämnare, under **inställningar**väljer **avsökningar**, och klicka sedan på **Lägg till** att lägga till en avsökning.

   ![Belastningsutjämnaren ”Lägg till avsökning”](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Ange namnet för avsökningen.
3. Välj den **protokollet** för avsökningen. Du kanske vill en TCP-avsökning i stället för en HTTP-avsökning för en databas. Läs mer om belastningsutjämnare avsökningar [förstå avsökningar av belastningsutjämnare](../load-balancer/load-balancer-custom-probe-overview.md).
4. Ange den **Port** av databasen som ska användas för åtkomst till avsökningen.
5. Under **intervall**, anger du hur ofta avsökning av programmet ska.
6. Under **tröskelvärde för ej felfri**, ange antalet kontinuerlig avsökning fel som måste äga rum för backend-virtuell dator för att anses vara felaktiga.
7. Klicka på **OK** skapar du avsökningen.

#### <a name="configure-the-load-balancing-rules"></a>Konfigurera regler för belastningsutjämning

1. Under **inställningar** för belastningsutjämnaren, Välj **belastningsutjämningsregler**, och klicka sedan på **Lägg till** att skapa en regel.
2. Ange den **namn** för regeln för belastningsutjämning.
3. Välj den **Klientdelens IP-adress** för belastningsutjämnaren, **protokollet**, och **Port**.
4. Under **serverdelsport**, ange porten som ska användas i backend poolen.
5. Välj den **serverdelspool** och **avsökning** som skapades i föregående steg och regeln ska.
6. Under **sessionspermanens**, väljer du hur du vill att sessioner ska sparas.
7. Under **inaktiv tidsgränser**, ange hur många minuter innan en timeout för inaktivitet.
8. Under **flytande IP**, väljer du antingen **inaktiverad** eller **aktiverad**.
9. Skapa regeln genom att klicka på **OK**.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Steg 5: Anslut webbnivå virtuella datorer till belastningsutjämnaren

Nu kan vi konfigurera IP-adress och belastningsutjämnare frontend-porten i program som körs på din webbnivå virtuella datorer för alla anslutningar. Den här konfigurationen är specifik för de program som körs på dessa virtuella datorer. Om du vill konfigurera IP-måladress och port, finns i dokumentationen för programmet. För att hitta IP-adressen för klientdelen i Azure-portalen, gå till IP-adresspoolen på klientsidan på den **inställningar för belastningsutjämnare**.

![Load Balancer ”Frontend IP-pool” navigeringsfönstret](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Nästa steg

* [Översikt över Traffic Manager](traffic-manager-overview.md)
* [Översikt över Application Gateway](../application-gateway/application-gateway-introduction.md)
* [Översikt över Azure Load Balancer](../load-balancer/load-balancer-overview.md)
