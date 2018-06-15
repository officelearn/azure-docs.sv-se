---
title: Med belastningsutjämning tjänster i Azure | Microsoft Docs
description: 'Den här kursen visar hur du skapar ett scenario med hjälp av Azure belastningsutjämning portfölj: Traffic Manager Application Gateway och belastningsutjämnare.'
services: traffic-manager
documentationcenter: ''
author: liumichelle
manager: vitinnan
editor: ''
ms.assetid: f89be3be-a16f-4d47-bcae-db2ab72ade17
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: limichel
ms.openlocfilehash: 86867a9d6d2c43e6505b1a06672546a017172bfe
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
ms.locfileid: "29401114"
---
# <a name="using-load-balancing-services-in-azure"></a>Med belastningsutjämning tjänster i Azure

## <a name="introduction"></a>Introduktion

Microsoft Azure tillhandahåller flera tjänster för att hantera hur nätverkstrafik distribueras och Utjämning av nätverksbelastning. Du kan använda dessa tjänster individuellt eller kombinera deras metoder, beroende på dina behov för att skapa den bästa lösningen.

I den här självstudiekursen kommer vi först definiera kunden användningsfall och se hur det kan göras mer robusta och performant genom att använda följande Azure belastningsutjämning portfölj: Traffic Manager Application Gateway och belastningsutjämnare. Vi ger sedan stegvisa instruktioner för hur du skapar en distribution som är geografiskt redundant, distribuerar trafik till virtuella datorer och hjälper dig att hantera olika typer av begäranden.

På en konceptuell nivå spelar var och en av dessa tjänster en specifik roll i hierarkin för belastningsutjämning.

* **Traffic Manager** innehåller globala DNS belastningsutjämning. Det ser ut på DNS-förfrågningar och svarar med en felfri slutpunkt i enlighet med routningsprincip kunden har valts. Alternativ för routningsmetoder är:
  * Prestanda routning för att skicka begäranden till närmaste slutpunkten med avseende på latens.
  * Prioritet för routning för att dirigera om all trafik till en slutpunkt med andra slutpunkter som säkerhetskopia.
  * Viktad resursallokering routning, som distribuerar trafik baserat på det värde som är tilldelad till varje slutpunkt.

  Klienten ansluter direkt till denna slutpunkt. Azure Traffic Manager identifierar när en slutpunkt har dåligt hälsotillstånd och omdirigerar sedan klienterna till en annan instans av felfritt. Referera till [Azure Traffic Manager-dokumentationen](traffic-manager-overview.md) lära dig mer om tjänsten.
* **Programgateway** ger programmet leverans domänkontrollant (ADC) som en tjänst som erbjuder olika Layer 7 belastningsutjämning funktioner för ditt program. Möjligheten för kunder att optimera web servergruppen produktivitet genom att avlasta processorintensiva SSL-avslutning för programgatewayen. Andra lager 7 routningsfunktionerna inkluderar resursallokering distribution av inkommande trafik, cookie-baserad session tillhörighet, URL-sökväg-baserad Routning och möjligheten att vara värd för flera webbplatser bakom en enda Programgateway. Programgateway kan konfigureras som en mot Internet-gateway, en endast internt-gateway eller en kombination av båda. Programgateway är fullständigt Azure hanterade, skalbara och hög tillgänglighet. För att få en bättre hantering ingår en omfattande uppsättning diagnostik- och loggningsfunktioner.
* **Belastningsutjämnaren** är en integrerad del av Azure SDN stacken, hög prestanda, låg latens lager 4 belastningsutjämning tjänster för alla UDP och TCP-protokoll. Den hanterar inkommande och utgående anslutningar. Du kan konfigurera offentliga och interna belastningsutjämnade slutpunkter och definiera regler för att mappa inkommande anslutningar till backend-adresspool mål med hjälp av TCP- och HTTP-avsökning av hälsotillstånd alternativ för att hantera tjänsttillgänglighet.

## <a name="scenario"></a>Scenario

I det här exemplet använder vi en enkel webbplats som har två typer av innehåll: bilder och dynamiskt återgivna webbsidor. Webbplatsen måste vara geografiskt redundant och den bör hantera sina användare från den närmaste (lägsta svarstiden) plats till dem. Programutvecklaren har bestämt att eventuella webbadresser som matchar mönstret/bilder / * hanteras från en dedikerad pool för virtuella datorer som skiljer sig från resten av gruppen.

Standard VM-poolen som betjänar dynamiskt innehåll måste dessutom tala med en backend-databas som finns på ett kluster med hög tillgänglighet. Hela distributionen har ställts in via Azure Resource Manager.

Med hjälp av Traffic Manager Application Gateway och belastningsutjämnare kan den här webbplatsen att uppnå dessa mål:

* **Flera geo-redundans**: om en region kraschar Traffic Manager dirigerar trafik sömlöst till närmaste region utan någon åtgärd från program-ägare.
* **Kortare svarstider**: eftersom Traffic Manager dirigerar automatiskt kund till närmaste region, kunden inträffar kortare svarstid när du begär innehållet för webbsidan.
* **Oberoende skalbarhet**: eftersom web application arbetsbelastningen avgränsas av typ av innehåll, programägaren kan skalas oberoende av varandra begäran arbetsbelastningar. Programgateway säkerställer att trafiken dirigeras till rätt poolerna baserat på de angivna reglerna och hälsotillståndet för programmet.
* **Intern belastningsutjämning**: eftersom belastningsutjämnare är framför kluster med hög tillgänglighet, endast aktiva och felfri slutpunkten för en databas är exponerad för programmet. Dessutom kan en databasadministratör optimera arbetsbelastningen genom att distribuera aktiva och passiva repliker över kluster oberoende av klientprogrammet. Belastningsutjämnaren levererar anslutningar till kluster med hög tillgänglighet och garanterar att endast felfri databaser tar emot anslutningsbegäranden.

Följande diagram visar arkitekturen för det här scenariot:

![Diagram över belastningsutjämning arkitektur](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Det här exemplet är endast ett av många möjliga konfigurationer belastningsutjämning tjänster som Azure erbjuder. Traffic Manager Application Gateway och belastningsutjämnare kan blandas och matchas som bäst passar dina behov för belastningsutjämning. Till exempel om SSL-avlastning eller Layer 7 bearbetning behövs inte kan belastningsutjämnaren användas i stället för Programgateway.

## <a name="setting-up-the-load-balancing-stack"></a>Konfigurera belastningsutjämning-stacken

### <a name="step-1-create-a-traffic-manager-profile"></a>Steg 1: Skapa en trafikhanterarprofil

1. I Azure-portalen klickar du på **skapar du en resurs** > **nätverk** > **trafikhanterarprofil**  >   **Skapa**.
2. Ange följande information:

  * **Namnet**: ge din Traffic Manager-profil en DNS-Prefixnamn.
  * **Routningsmetoden**: Välj principen för routning av nätverkstrafik metod. Mer information om metoder finns [om Traffic Manager-trafikroutningsmetoder](traffic-manager-routing-methods.md).
  * **Prenumerationen**: Välj den prenumeration som innehåller profilen.
  * **Resursgruppen**: Markera den resursgrupp som innehåller profilen. Det kan vara en ny eller befintlig resursgrupp.
  * **Resursgruppens plats**: Traffic Manager-tjänsten är global och inte knuten till en plats. Du måste dock ange en region för gruppen där metadata som associeras med Traffic Manager-profilen finns. Den här platsen påverkar inte tillgängligheten för körning av profilen.

3. Klicka på **skapa** att generera Traffic Manager-profilen.

  !["Create Traffic Manager" blade](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Steg 2: Skapa programmet gateways

1. I Azure-portalen i den vänstra rutan klickar du på **skapar du en resurs** > **nätverk** > **Programgateway**.
2. Ange följande information om Programgateway:

  * **Namnet**: namnet på programgatewayen.
  * **SKU-storleken**: storleken på Programgateway tillgängliga som små, medelstora eller stora.
  * **Instansen antal**: antalet instanser, ett värde mellan 2 och 10.
  * **Resursgruppen**: resursgruppen som innehåller programgatewayen. Det kan vara en befintlig resursgrupp eller en ny.
  * **Plats**: region för Programgateway, vilket är samma plats som resursgruppen. Platsen är viktig, eftersom virtuella nätverk och offentliga IP-Adressen måste vara på samma plats som gateway.
3. Klicka på **OK**.
4. Definiera virtuella nätverk, undernät, frontend IP och konfigurationer för lyssnare för programgatewayen. I det här scenariot frontend IP-adressen är **offentliga**, vilket gör att det som ska läggas till som en slutpunkt i Traffic Manager-profilen vid ett senare tillfälle.
5. Konfigurera lyssnaren med något av följande alternativ:
    * Om du använder HTTP, finns det inget att konfigurera. Klicka på **OK**.
    * Om du använder HTTPS ytterligare krävs konfiguration. Referera till [skapa en Programgateway](../application-gateway/application-gateway-create-gateway-portal.md)början från steg 9. När du har slutfört konfigurationen, klickar du på **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Konfigurera URL routning för programgatewayer

När du väljer en backend-adresspool, tar en Programgateway som är konfigurerad med en sökväg-baserade regel en sökvägar för URL: en förutom resursallokering. I det här scenariot vi lägger till en sökväg-baserade regler för att dirigera alla URL: en med ”/images/\*” i serverpoolen i bilden. Mer information om hur du konfigurerar URL sökväg-baserade routning för en Programgateway avser [skapa en sökväg-baserade regel för en Programgateway](../application-gateway/application-gateway-create-url-route-portal.md).

![Diagram över webbnivå Gateway](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Gå till instansen av programgatewayen som du skapade i föregående avsnitt från din resursgrupp.
2. Under **inställningar**väljer **serverdelspooler**, och välj sedan **Lägg till** att lägga till de virtuella datorerna som du vill associera med webbnivå backend-pooler.
3. Ange namnet på backend-adresspool och IP-adresser för datorer som finns i poolen. I det här scenariot ansluter vi två backend-server-pooler med virtuella datorer.

  ![Programgateway ”Lägg till serverdelspoolen”](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Under **inställningar** för Programgateway, Välj **regler**, och klicka sedan på den **sökväg baserat** för att lägga till en regel.

  ![Programmet Gateway regler ”sökväg baserad” knappen](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Konfigurera att regeln genom att ange följande information.

   Grundläggande inställningar:

   + **Namnet**: det egna namnet för regeln som är tillgänglig i portalen.
   + **Lyssnare**: lyssnaren som används för regeln.
   + **Standard serverdelspool**: backend-poolen som ska användas med Standardregeln.
   + **Standardinställningarna för HTTP-**: HTTP-inställningar som ska användas med Standardregeln.

   Sökväg-baserade regler:

   + **Namnet**: det egna namnet för regeln baserat på sökvägen.
   + **Sökvägar**: sökvägsregeln som används för att vidarebefordra trafik.
   + **Serverdelspool**: backend-poolen som ska användas med den här regeln.
   + **HTTP-inställningen**: HTTP-inställningar som ska användas med den här regeln.

   > [!IMPORTANT]
   > Sökvägar: Giltiga sökvägar måste inledas med ”/”. Jokertecknet ”\*” tillåts endast i slutet. Giltiga exempel är /xyz /xyz\*, eller /xyz/\*.

   ![Programmet Gateway ”Lägg till sökväg-baserade regeln” bladet](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Steg 3: Lägg till programgatewayer till Traffic Manager-slutpunkter

I det här scenariot är Traffic Manager ansluten till programgatewayer (som konfigurerats i föregående steg) som finns i olika regioner. Nu när programmet gateway har konfigurerats, är nästa steg att ansluta till din Traffic Manager-profil.

1. Öppna Traffic Manager-profilen. Om du vill göra det, titta i resursgruppen eller Sök efter namnet på Traffic Manager-profilen från **alla resurser**.
2. I den vänstra rutan, Välj **slutpunkter**, och klicka sedan på **Lägg till** att lägga till en slutpunkt.

  ![Traffic Manager slutpunkter ”Lägg till”-knappen](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Skapa en slutpunkt genom att ange följande information:

  * **Typen**: Välj typ av slutpunkten för att belastningsutjämna. I det här scenariot väljer **Azure endpoint** eftersom vi ansluter den till programmet gateway-instanser som har konfigurerats tidigare.
  * **Namnet**: Ange namnet på slutpunkten.
  * **Rikta resurstypen**: Välj **offentliga IP-adressen** och under **mål resurs**, Välj offentlig IP-Adressen för Programgateway som konfigurerats tidigare.

   ![Traffic Manager ”Lägg till slutpunkten”](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Nu kan du testa din konfiguration genom att öppna den med DNS för din Traffic Manager-profil (i det här exemplet: TrafficManagerScenario.trafficmanager.net). Du kan skicka begäranden registreringspunkter eller avslutar virtuella datorer och servrar som har skapats i olika regioner och ändra profilinställningarna för Traffic Manager-om du vill testa din konfiguration.

### <a name="step-4-create-a-load-balancer"></a>Steg 4: Skapa en belastningsutjämnare

I det här scenariot distribuerar belastningsutjämnare anslutningar från webbnivån till databaser i ett kluster med hög tillgänglighet.

Om ditt kluster med hög tillgänglighet databasen använder SQL Server AlwaysOn, referera till [konfigurera en eller flera alltid på tillgänglighet Tillgänglighetsgruppslyssnarnas](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) stegvisa instruktioner.

Mer information om hur du konfigurerar en intern belastningsutjämnare finns [skapa en intern belastningsutjämnare i Azure portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. I Azure-portalen i den vänstra rutan klickar du på **skapar du en resurs** > **nätverk** > **belastningsutjämnaren**.
2. Välj ett namn för din belastningsutjämnare.
3. Ange den **typen** till **internt**, och välj lämplig virtuellt nätverk och undernät för belastningsutjämnaren finns i.
4. Under **IP-adresstilldelning**, väljer du antingen **dynamiska** eller **statiska**.
5. Under **resursgruppen**, väljer du resursgrupp för belastningsutjämnaren.
6. Under **plats**, Välj lämplig region för belastningsutjämnaren.
7. Klicka på **skapa** att generera belastningsutjämnaren.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Ansluta en backend-databasnivå till belastningsutjämnaren

1. Hitta belastningsutjämnaren som skapades i föregående steg från din resursgrupp.
2. Under **inställningar**, klickar du på **serverdelspooler**, och klicka sedan på **Lägg till** att lägga till en backend-adresspool.

  ![Belastningsutjämnaren ”Lägg till serverdelspoolen”](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Ange namnet på backend-poolen.
4. Lägga till enskilda datorer eller en tillgänglighetsuppsättning till backend-poolen.

#### <a name="configure-a-probe"></a>Konfigurera en avsökning

1. I din belastningsutjämnare under **inställningar**väljer **avsökningar**, och klicka sedan på **Lägg till** att lägga till en avsökning.

 ![Belastningsutjämnaren ”Lägg till avsökning”](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Ange namn för avsökningen.
3. Välj den **protokollet** för avsökningen. Du kanske vill en TCP-avsökning i stället för en HTTP-avsökningen för en databas. Mer information om belastningsutjämnare avsökningar avser [förstå load balancer avsökningar](../load-balancer/load-balancer-custom-probe-overview.md).
4. Ange den **Port** av databasen som ska användas för att komma åt avsökningen.
5. Under **intervall**, ange hur ofta avsökning för programmet.
6. Under **tröskelvärde för ohälsosamt värde**, ange antalet kontinuerlig avsökningen fel som måste inträffa för backend-VM för att ses som ohälsosamt.
7. Klicka på **OK** att skapa avsökningen.

#### <a name="configure-the-load-balancing-rules"></a>Konfigurera regler för belastningsutjämning

1. Under **inställningar** din belastningsutjämnare väljer **belastningsutjämningsregler**, och klicka sedan på **Lägg till** att skapa en regel.
2. Ange den **namn** för regeln för belastningsutjämning.
3. Välj den **Klientdelens IP-adress** belastningsutjämningens, **protokollet**, och **Port**.
4. Under **serverdelsport**, ange porten som ska användas i backend-poolen.
5. Välj den **serverdelspool** och **avsökning** som skapades i föregående steg och regeln ska.
6. Under **sessionspersistence**, väljer du hur sessioner att spara.
7. Under **timeout vid inaktivitet**, ange antalet minuter innan en timeout för inaktivitet.
8. Under **flytande IP**, väljer du antingen **inaktiverad** eller **aktiverad**.
9. Skapa regeln genom att klicka på **OK**.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Steg 5: Anslut webbnivå virtuella datorer till belastningsutjämnaren

Vi nu konfigurera IP-adress och belastningsutjämnare frontend porten i program som körs på dina webbnivå virtuella datorer för alla anslutningar. Den här konfigurationen är specifika för de program som körs på dessa virtuella datorer. Om du vill konfigurera IP-adressen och porten finns i dokumentationen för programmet. För att hitta IP-adressen för klientdelen i Azure-portalen, går du till frontend IP-adresspool på det **inställningar för belastningsutjämnare**.

![Läsa in belastningsutjämning ”Frontend IP-pool” navigeringsfönstret](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Nästa steg

* [Översikt över Traffic Manager](traffic-manager-overview.md)
* [Översikt över Gateway](../application-gateway/application-gateway-introduction.md)
* [Översikt över Azure Load Balancer](../load-balancer/load-balancer-overview.md)
