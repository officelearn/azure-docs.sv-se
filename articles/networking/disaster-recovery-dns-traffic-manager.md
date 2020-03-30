---
title: Haveriberedskap med Azure DNS och Traffic Manager | Microsoft-dokument
description: Översikt över katastrofåterställningslösningarna med Azure DNS och Traffic Manager.
services: dns
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/08/2018
ms.author: kumud
ms.openlocfilehash: 6eab1803bf5adab42be87b5f8567682c6d75947e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74483528"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Haveriberedskap med hjälp av Azure DNS och Traffic Manager

Haveriberedskap handlar om att återställa från en betydande förlust av programfunktion. För att kunna välja en katastrofåterställningslösning måste företags- och teknikägare först bestämma vilken funktionalitetsnivå som krävs vid en katastrof, till exempel - otillgänglig, delvis tillgänglig via nedsatt funktionalitet eller fördröjd tillgänglighet, eller fullt tillgängliga.
De flesta företagskunder väljer en arkitektur med flera regioner för återhämtning mot en redundans på program- eller infrastrukturnivå. Kunderna kan välja flera metoder i strävan att uppnå redundans och hög tillgänglighet via redundant arkitektur. Här är några av de populära metoderna:

- **Aktiv-passiv med kall standby:** I den här redundanslösningen är de virtuella datorerna och andra apparater som körs i väntelägesregionen inte aktiva förrän det finns behov av redundans. Produktionsmiljön replikeras dock i form av säkerhetskopior, VM-avbildningar eller Resource Manager-mallar till en annan region. Denna redundansmekanism är kostnadseffektiv men tar längre tid att genomföra en fullständig redundans.
 
    ![Aktiv/passiv med kallt standby](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Bild - Aktiv/passiv med konfiguration för återställning av kall standby-katastrof*

- **Aktiv/Passiv med pilotlampa**: I den här redundanslösningen ställs standby-miljön in med en minimal konfiguration. Installationen har bara de tjänster som körs för att stödja endast en minimal och kritisk uppsättning program. I sin ursprungliga form kan det här scenariot bara köra minimal funktionalitet men kan skala upp och skapa ytterligare tjänster för att ta huvuddelen av produktionsbelastningen om en redundans inträffar.
    
    ![Aktiv/Passiv med pilotljus](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Figur: Aktiv/passiv med pilotkonfiguration för ljuskatastrofåterställning*

- **Aktiv/Passiv med varmt vänteläge**: I den här redundanslösningen är vänteregionen förvärmd och är redo att ta basbelastningen, automatisk skalning är påslagen och alla instanser är igång. Den här lösningen skalas inte för att ta hela produktionsbelastningen men fungerar och alla tjänster är igång. Denna lösning är en förstärkt version av pilotljusmetoden.
    
    ![Aktiv/passiv med varm standby](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Bild: Aktiv/passiv med konfiguration för återställning av varma standby-katastrofer*
    
Mer information om redundans och hög tillgänglighet finns i [Disaster Recovery for Azure Applications](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Planera arkitekturen för katastrofåterställning

Det finns två tekniska aspekter på hur du konfigurerar arkitekturen för haveriberedskap:
-  Använda en distributionsmekanism för att replikera instanser, data och konfigurationer mellan primära miljöer och väntemiljöer. Den här typen av haveriberedskap kan göras internt via Azure Site-Recovery via Microsoft Azure-partnerinstallationer/-tjänster som Veritas eller NetApp. 
- Utveckla en lösning för att vidarekoppla nätverks-/webbtrafik från den primära platsen till vänteplatsen. Den här typen av haveriberedskap kan uppnås via Azure DNS, Azure Traffic Manager(DNS) eller globala belastningsutjämnare från tredje part.

Den här artikeln är begränsad till metoder via omdirigering av nätverks- och webbtrafik. Instruktioner för hur du konfigurerar Azure Site Recovery finns i [Dokumentation för Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).
DNS är en av de mest effektiva mekanismerna för att avleda nätverkstrafik eftersom DNS ofta är globalt och externt till datacentret och är isolerad från alla regionala eller tillgänglighetszoner (AZ) nivåfel. Man kan använda en DNS-baserad redundansmekanism och i Azure kan två DNS-tjänster utföra samma på något sätt - Azure DNS (auktoritär DNS) och Azure Traffic Manager (DNS-baserad smart trafikroutning). 

Det är viktigt att förstå få begrepp i DNS som används i stor utsträckning för att diskutera de lösningar som tillhandahålls i den här artikeln:
- **DNS A Record** – A Records är pekare som pekar en domän till en IPv4-adress. 
- **CNAME eller Canonical name** - Den här posttypen används för att peka på en annan DNS-post. CNAME svarar inte med en IP-adress utan pekaren till posten som innehåller IP-adressen. 
- **Viktad routning** – man kan välja att associera en vikt till tjänstslutpunkter och sedan fördela trafiken baserat på de tilldelade vikterna. Den här routningsmetoden är en av de fyra trafikroutningsmekanismer som är tillgängliga i Traffic Manager. Mer information finns i [Viktad routningsmetod](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Prioriterad routning** – Prioritetsdirigering baseras på hälsokontroller av slutpunkter. Som standard skickar Azure Traffic Manager all trafik till slutpunkten med högsta prioritet och vid ett fel eller en katastrof dirigerar Traffic Manager trafiken till den sekundära slutpunkten. Mer information finns i [Prioritetsroutningsmetod](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method).

## <a name="manual-failover-using-azure-dns"></a>Manuell redundans med Azure DNS
Azure DNS manuell redundanslösning för haveriberedskap använder standard-DNS-mekanismen för att växla över till säkerhetskopieringsplatsen. Det manuella alternativet via Azure DNS fungerar bäst när det används tillsammans med den kalla standby- eller pilotljusmetoden. 

![Manuell redundans med Azure DNS](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Bild - Manuell redundans med Azure DNS*

De antaganden som gjorts för lösningen är:
- Både primära och sekundära slutpunkter har statiska IP-adresser som inte ändras ofta. Säg för den primära platsen IP är 100.168.124.44 och IP för den sekundära platsen är 100.168.124.43.
- Det finns en Azure DNS-zon för både den primära och den sekundära platsen. Säg för den primära platsen slutpunkten är prod.contoso.com och för säkerhetskopieringsplatsen är dr.contoso.com. Det finns också en DNS-post för huvudprogrammet som kallas www\.contoso.com.   
- TTL är på eller under RTO SLA som i organisationen. Till exempel, om ett företag ställer in RTO för ansökan katastrofsvar till 60 minuter, då TTL bör vara mindre än 60 minuter, helst ju lägre desto bättre. 
  Du kan ställa in Azure DNS för manuell redundans enligt följande:
- Skapa en DNS-zon
- Skapa DNS-zonposter
- Uppdatera CNAME-post

### <a name="step-1-create-a-dns"></a>Steg 1: Skapa en DNS
Skapa en DNS-zon (till exempel www\.contoso.com) enligt nedan:

![Skapa en DNS-zon i Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Bild - Skapa en DNS-zon i Azure*

### <a name="step-2-create-dns-zone-records"></a>Steg 2: Skapa DNS-zonposter

Inom den här zonen skapa tre\.poster (till exempel - www contoso.com, prod.contoso.com och dr.consoto.com) som visas nedan.

![Skapa DNS-zonposter](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Bild - Skapa DNS-zonposter i Azure*

I det här scenariot har webbplatsen, www\.contoso.com en TTL på 30 minuter, vilket är långt under den angivna RTO, och pekar på produktionsanläggningen prod.contoso.com. Den här konfigurationen är under normala affärsåtgärder. TTL för prod.contoso.com och dr.contoso.com har ställts in på 300 sekunder eller 5 minuter. Du kan använda en Azure-övervakningstjänst som Azure Monitor eller Azure App Insights, eller, alla partnerövervakningslösningar som Dynatrace, Du kan till och med använda hemodlade lösningar som kan övervaka eller identifiera fel på program- eller virtuell infrastrukturnivå.

### <a name="step-3-update-the-cname-record"></a>Steg 3: Uppdatera CNAME-posten

När fel har upptäckts ändrar du postvärdet så att det pekar på dr.contoso.com som visas nedan:
       
![Uppdatera CNAME-post](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Bild - Uppdatera CNAME-posten i Azure*

Inom 30 minuter, under vilken de flesta resolvers kommer att\.uppdatera den cachelagrade zonfilen, omdirigeras alla frågor till www contoso.com till dr.contoso.com.
Du kan också köra följande Azure CLI-kommando för att ändra CNAME-värdet:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Det här steget kan utföras manuellt eller via automatisering. Det kan göras manuellt via konsolen eller av Azure CLI. Azure SDK och API kan användas för att automatisera CNAME-uppdateringen så att ingen manuell åtgärd krävs. Automatisering kan byggas via Azure-funktioner eller inom ett tredjepartsövervakningsprogram eller till och med från lokala.

### <a name="how-manual-failover-works-using-azure-dns"></a>Så här fungerar manuell redundans med Azure DNS
Eftersom DNS-servern ligger utanför redundans- eller katastrofzonen är den isolerad mot eventuella driftstopp. Detta gör det möjligt för användaren att skapa ett enkelt redundansscenario som är kostnadseffektivt och kommer att fungera hela tiden förutsatt att operatören har nätverksanslutning under katastrof och kan göra luckan. Om lösningen är skriptad, måste man se till att servern eller tjänsten som kör skriptet ska isoleras mot problemet som påverkar produktionsmiljön. Tänk också på den låga TTL som ställdes in mot zonen så att ingen resolver runt om i världen håller slutpunkten cachelagrad för länge och kunder kan komma åt webbplatsen inom RTO. För en kall standby och pilot ljus, eftersom vissa förvärmande och annan administrativ verksamhet kan krävas - man bör också ge tillräckligt med tid innan du gör luckan.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatisk redundans med Azure Traffic Manager
När du har komplexa arkitekturer och flera uppsättningar resurser som kan utföra samma funktion kan du konfigurera Azure Traffic Manager (baserat på DNS) för att kontrollera hälsotillståndet för dina resurser och dirigera trafiken från den icke-felfria resursen till den felfria resursen Resurs. I följande exempel har både den primära regionen och den sekundära regionen en fullständig distribution. Den här distributionen omfattar molntjänster och en synkroniserad databas. 

![Automatisk redundans med Azure Traffic Manager](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Bild - Automatisk redundans med Azure Traffic Manager*

Det är dock bara den primära regionen som aktivt hanterar nätverksbegäranden från användarna. Den sekundära regionen aktiveras endast när den primära regionen drabbas av ett avbrott i tjänsten. I så fall dirigerar alla nya nätverksbegäranden till den sekundära regionen. Eftersom säkerhetskopian av databasen är nära ögonblicklig, har både belastningsutjämnare IP-adresser som kan kontrolleras av hälsotillstånd, och instanserna är alltid igång, ger den här topologin ett alternativ för att gå in för en låg RTO och redundans utan manuella åtgärder. Den sekundära redundansregionen måste vara redo att gå live direkt efter fel i den primära regionen.
Det här scenariot är idealiskt för användning av Azure Traffic Manager som har inbyggda avsökningar för olika typer av hälsokontroller, inklusive http / https och TCP. Azure Traffic Manager har också en regelmotor som kan konfigureras för redundans när ett fel inträffar enligt beskrivningen nedan. Låt oss överväga följande lösning med Traffic Manager:
- Kunden har region #1 slutpunkt som kallas prod.contoso.com med en statisk IP som 100.168.124.44 och en region #2 slutpunkt som kallas dr.contoso.com med en statisk IP som 100.168.124.43. 
-   Var och en av dessa miljöer är fronted via en offentlig inför egendom som en belastningsutjämnare. Belastningsutjämnaren kan konfigureras för att ha en DNS-baserad slutpunkt eller ett fullständigt kvalificerat domännamn (FQDN) som visas ovan.
-   Alla instanser i region 2 är i nära realtidsreplikering med region 1. Dessutom är maskinavbildningarna uppdaterade och alla programvaru-/konfigurationsdata korrigeras och är i linje med region 1.  
-   Automatisk skalning är förkonfigurerad i förväg. 

De åtgärder som vidtagits för att konfigurera redundansen med Azure Traffic Manager är följande:
1. Skapa en ny Azure Traffic Manager-profil
2. Skapa slutpunkter i Traffic Manager-profilen
3. Konfigurera hälsokontroll och redundanskonfiguration

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Steg 1: Skapa en ny Azure Traffic Manager-profil
Skapa en ny Azure Traffic Manager-profil med namnet contoso123 och välj routningsmetoden som prioritet. Om du har en befintlig resursgrupp som du vill associera med kan du välja en befintlig resursgrupp, annars skapa en ny resursgrupp.

![Skapa Traffic Manager-profil](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Bild - Skapa en Traffic Manager-profil*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Steg 2: Skapa slutpunkter i Traffic Manager-profilen

I det här steget skapar du slutpunkter som pekar på produktions- och haveriberedskapsplatserna. Här väljer du **typ** som en extern slutpunkt, men om resursen finns i Azure kan du välja **Azure-slutpunkt** också. Om du väljer **Azure-slutpunkt**väljer du en **målresurs** som antingen är en **App-tjänst** eller en **offentlig IP** som allokeras av Azure. Prioriteten anges som **1** eftersom den är den primära tjänsten för region 1.
På samma sätt skapar du slutpunkten för haveriberedskap i Traffic Manager.

![Skapa slutpunkter för haveriberedskap](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Bild - Skapa slutpunkter för haveriberedskap*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Steg 3: Konfigurera hälsokontroll och redundanskonfiguration

I det här steget ställer du in DNS TTL till 10 sekunder, vilket hedras av de flesta internet-vända rekursiva resolvers. Den här konfigurationen innebär att ingen DNS-matcharen cachelagrar informationen i mer än 10 sekunder. För slutpunktsövervakarinställningarna är sökvägen aktuell inställd på / eller rot, men du kan anpassa slutpunktsinställningarna för att utvärdera en sökväg, till exempel prod.contoso.com/index. Exemplet nedan visar **https** som sonderingsprotokoll. Du kan dock välja **http** eller **tcp** också. Valet av protokoll beror på slutprogrammet. Avsökningsintervallet är inställt på 10 sekunder, vilket möjliggör snabb avsökning och återförsöket är inställt på 3. Därför redundans traffic manager till den andra slutpunkten om tre på varandra följande intervall registrerar ett fel. Följande formel definierar den totala tiden för en automatisk redundans: Tid för redundans = TTL + Försök * Sonderingsintervall Och i det här fallet är värdet 10 + 3 * 10 = 40 sekunder (Max).
Om återförsöket är inställt på 1 och TTL är inställt på 10 sekunder, då tiden för redundans 10 + 1 * 10 = 20 sekunder. Ställ in återförsöket på ett värde som är större än **1** för att eliminera risken för redundans på grund av falska positiva identifieringar eller mindre nätverksvälpsar. 


![Ställ in hälsokontroll](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Bild - Ställ in hälsokontroll och redundanskonfiguration*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Så här fungerar automatisk redundans med Traffic Manager

Under en katastrof avsöks den primära slutpunkten och statusen ändras till **försämrad** och platsen för haveriberedskap förblir **online**. Som standard skickar Traffic Manager all trafik till den primära slutpunkten (högsta prioritet). Om den primära slutpunkten verkar försämrad dirigerar Traffic Manager trafiken till den andra slutpunkten så länge den förblir felfri. Man har möjlighet att konfigurera fler slutpunkter i Traffic Manager som kan fungera som ytterligare redundansslutpunkter, eller, som belastningsutjämnare som delar belastningen mellan slutpunkter.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Läs mer om [Azure DNS](../dns/dns-overview.md).









