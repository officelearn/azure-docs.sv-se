---
title: Haveriberedskap med hjälp av Azure DNS och Traffic Manager | Microsoft Docs
description: Översikt över de lösningar för haveriberedskap med hjälp av Azure DNS och Traffic Manager.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
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
ms.openlocfilehash: ec252c1f45e5c27f17b725f6ab68cc94f67897c4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120745"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Haveriberedskap med hjälp av Azure DNS och Traffic Manager

Haveriberedskap handlar om att återställa från en betydande förlust av programfunktion. Om du ska välja en lösning för haveriberedskap, affärs- och ägare först bestämma nivå av funktioner som krävs vid ett haveri som – inte tillgänglig, delvis tillgängliga via nedsatt funktionalitet eller fördröjd tillgänglighet eller helt tillgängligt.
De flesta enterprise-kunder väljer en arkitektur med flera regioner för skydd mot ett program eller en infrastruktur på redundans. Kunderna kan välja flera metoder i begäran om att uppnå redundans och hög tillgänglighet via redundanta arkitektur. Här följer några av de populära metoderna:

- **Aktiv-passiv med kallt vänteläge**: I den här lösningen failover är de virtuella datorerna och andra enheter som körs i vänteläge region inte aktiva tills det finns ett behov av redundans. Dock replikeras produktionsmiljön i form av säkerhetskopior, avbildningar av Virtuella datorer eller Resource Manager-mallar till en annan region. Den här mekanismen för växling vid fel är kostnadseffektiv, men tar längre tid att genomföra en slutföra växling vid fel.
 
    ![Aktiv/passiv med kallt vänteläge](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Bild - aktiv/passiv med kallt vänteläge haveriberedskapskonfiguration*

- **Aktiv/passiv med pilot ljus**: I den här lösningen failover, har standby-miljön konfigurerats med en minimal konfiguration. Installationen har endast nödvändiga tjänster som körs för att stödja endast en minimal och kritiska uppsättning program. I sin ursprungliga form kan det här scenariot endast köra minimal funktioner men kan skala upp och skapa ytterligare tjänster ska lejonparten av produktionsbelastningen om det uppstår redundans.
    
    ![Aktiv/passiv med pilot ljus](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Figur: Aktiv/passiv med pilot ljus haveriberedskapskonfiguration*

- **Aktiv/passiv med varmt vänteläge**: I den här redundanslösning regionen vänteläge är före uppvärmning och är redo att ta den grundläggande belastningen, automatisk skalning är aktiverat och alla instanser är igång och körs. Den här lösningen inte skalas för att dra den fullständiga produktionsdistributionen belastningen men fungerar, och alla tjänster som är igång och körs. Den här lösningen är en förhöjd version av piloten light-metoden.
    
    ![Aktiv/passiv med varmt vänteläge](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Figur: Aktiv/passiv med varmt vänteläge haveriberedskapskonfiguration*
    
Läs mer om redundans och hög tillgänglighet i [Haveriberedskap för Azure-program](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Planera din disaster recovery-arkitektur

Det finns två tekniska aspekter för att konfigurera din disaster recovery-arkitektur:
-  Använda en mekanism för att replikera instanser, data och konfigurationer mellan primär och vänteläge miljöer. Den här typen av disaster recovery kan göra det internt via Azure-Webbplatsåterställning via Microsoft Azure-partner installationer/tjänster som Veritas eller NetApp. 
- Utveckla en lösning för att använda ett annat nätverk/webbtrafik från den primära platsen till webbplatsen vänteläge. Den här typen av haveriberedskap kan ske via Azure DNS, Azure trafik Manager(DNS) eller globala belastningsutjämnare från tredje part.

Den här artikeln är begränsad till metoder via nätverket och Web traffic omdirigering. Anvisningar för att ställa in Azure Site Recovery finns i [dokumentation om Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).
DNS är ett av de mest effektiva mekanismerna för att använda ett annat nätverkstrafik eftersom DNS är ofta globala och externa till datacentret och är isolerade från regionala eller tillgänglighet zon (AZ) på fel. Kan använda en mekanism för DNS-baserade redundans och i Azure, två DNS-tjänster utför samma på något sätt – Azure DNS (auktoritativ DNS) och Azure Traffic Manager (DNS-baserad smart trafikroutning). 

Det är viktigt att förstå några begrepp i DNS som stor utsträckning används för att berätta om lösningar i den här artikeln:
- **DNS A Record** – A-poster är pekare som pekar en domän på en IPv4-adress. 
- **CNAME-eller Canonical** – den här posttypen används för att peka till en annan DNS-post. På den post som innehåller IP-adressen svarar med en IP-adress men i stället pekaren inte CNAME. 
- **Viktat routning** – kan du välja att koppla en vikt till Tjänsteslutpunkter och sedan distribuera trafik baserat på de tilldelade vikterna. Den här routningsmetod är en av fyra trafik routning metoder tillgängliga i Traffic Manager. Mer information finns i [viktad routningsmetod](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Prioritet routning** – prioritet routning baseras på hälsokontroller av slutpunkter. Azure Traffic manager skickar all trafik till den högsta priority slutpunkten som standard, och när ett fel eller haveri Traffic Manager dirigerar trafiken till den sekundära slutpunkten. Mer information finns i [prioriterad routningsmetod](../traffic-manager/traffic-manager-routing-methods.md#priority).

## <a name="manual-failover-using-azure-dns"></a>Manuell redundans med hjälp av Azure DNS
Azure DNS manuell redundans-lösningen för haveriberedskap använder mekanismen standard DNS för att redundansväxla till säkerhetskopieringsplatsen. Det manuella alternativet via Azure DNS fungerar bäst när de används tillsammans med kallt vänteläge eller pilot light-metoden. 

![Manuell redundans med hjälp av Azure DNS](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Bild - manuell redundans med hjälp av Azure DNS*

Antaganden som gjordes under lösningen är:
- Både den primära och sekundära slutpunkter har statiska IP-adresser som inte ändras ofta. Är exempelvis den IP-Adressen för den primära platsen 100.168.124.44 och IP-Adressen för den sekundära platsen är 100.168.124.43.
- Det finns en Azure DNS-zon för både primär och sekundär plats. Exempel för den primära platsen slutpunkten är prod.contoso.com och för säkerhetskopieringsplatsen dr.contoso.com. En DNS-post för det huvudsakliga programmet kallas www\.contoso.com finns också.   
- TTL är vid eller under RTO serviceavtal (SLA) som angetts i organisationen. Exempel: om företaget anger RTO av programmet katastrof svaret ska vara 60 min TTL-värdet bör vara mindre än 60 min, helst lägre desto bättre. 
  Du kan ställa in Azure DNS för manuell växling på följande sätt:
- Skapa en DNS-zon
- Skapa DNS-zon-poster
- Update CNAME record

### <a name="step-1-create-a-dns"></a>Steg 1: Skapa en DNS-
Skapa en DNS-zon (till exempel www\.contoso.com) enligt nedan:

![Skapa en DNS-zon i Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Bild – skapa en DNS-zon i Azure*

### <a name="step-2-create-dns-zone-records"></a>Steg 2: Skapa DNS-zon-poster

Skapa tre poster i den här zonen (till exempel - www\.contoso.com, prod.contoso.com och dr.consoto.com) som visas nedan.

![Skapa DNS-zon-poster](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Bild – skapa DNS-zonposter i Azure*

I det här scenariot, plats, www\.contoso.com har ett TTL-värde på 30 minuter som är betydligt lägre än den angivna RTO och pekar på prod.contoso.com för produktion-plats. Den här konfigurationen är under normal verksamheten. TTL-värde på prod.contoso.com och dr.contoso.com har ställts in på 300 sekunder eller 5 minuter. Du kan använda en Azure övervakningstjänsten som Azure Monitor eller Azure App Insights eller valfria partnerorganisationer övervakningslösningar, till exempel Dynatrace, du kan även använda home växt lösningar som kan övervaka eller identifiera program eller en virtuell infrastruktur på fel.

### <a name="step-3-update-the-cname-record"></a>Steg 3: Uppdatera CNAME-post

När fel upptäcks, ändra Postvärde så att den pekar till dr.contoso.com enligt nedan:
       
![Update CNAME record](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Bild – uppdatera CNAME-post i Azure*

Inom 30 minuter, då de flesta matchare uppdateras den cachelagrade zonfilen någon fråga till www\.contoso.com omdirigeras till dr.contoso.com.
Du kan också köra följande Azure CLI-kommando för att ändra värdet för CNAME:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Det här steget kan köras manuellt eller via automation. Det kan göras manuellt via konsolen eller genom att Azure CLI. Azure SDK och API: et kan användas för att automatisera CNAME-uppdatering så att inga manuella åtgärder krävs. Automatisering kan vara inbyggt via Azure functions eller inom en övervakning program från tredje part eller även från en lokal plats.

### <a name="how-manual-failover-works-using-azure-dns"></a>Hur manuell redundans fungerar med Azure DNS
Eftersom DNS-servern är utanför zonen växling vid fel eller en katastrof, är det isolerade mot driftavbrott. Detta gör att användare om arkitekturen i ett scenario med enkel växling vid fel är kostnadseffektiv och fungerar alla den tid förutsatt att operatören har nätverksanslutning under katastrof och kan göra andra. Om lösningen är Skriptdefinierad måste en Kontrollera att servern eller -tjänsten körs skriptet bör skyddas mot problem som påverkar produktionsmiljön. Tänk också på låg TTL-värdet som har ställts in mot zonen så att ingen lösare över hela världen ser till att den slutpunkt som cachelagras för långvariga och kunder kan komma åt webbplatsen inom RTO. För kallt vänteläge och pilotprojekt ljus, eftersom vissa prewarming och andra administrativa aktiviteter kan krävas – bör en också innehålla tillräckligt med tid innan du gör andra.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatisk redundans med Azure Traffic Manager
När du har komplexa arkitekturer och flera uppsättningar av resurser som kan utföra samma funktion kan kan du konfigurera Azure Traffic Manager (baserat på DNS) för att kontrollera hälsotillståndet för dina resurser och dirigera trafik från icke hälsosamma resursen till den felfritt resursen. I följande exempel har en fullständig distribution av både den primära regionen och den sekundära regionen. Den här distributionen omfattar cloud services och en synkroniserade databasen. 

![Automatisk redundans med Azure Traffic Manager](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Bild - automatisk växling vid fel med Azure Traffic Manager*

Dock hanterar bara den primära regionen aktivt nätverksbegäranden från användare. Den sekundära regionen aktiveras endast när den primära regionen påträffar ett avbrott i tjänsten. I så fall kan vidarebefordra alla nya nätverksbegäranden till den sekundära regionen. Sedan säkerhetskopieringen av databasen är nästan omedelbar, båda belastningsutjämnarna har IP-adresser som kan vara hälsotillstånd kontrolleras, och instanserna är alltid igång och körs, den här topologin innehåller ett alternativ för att gå för ett låga RTO och redundans utan några manuella åtgärder. Sekundär redundansväxlingsregionen måste vara redo att gå live omedelbart efter fel för den primära regionen.
Det här scenariot är perfekt att använda i Azure Traffic Manager som har inbyggd avsökningar för olika typer av hälsokontroller av slutpunkter, inklusive http / https- och TCP. Med Azure Traffic manager har också en innehållsleveransmotor som kan konfigureras för redundans när ett fel inträffar enligt beskrivningen nedan. Anta att du har följande lösning med Traffic Manager:
- Kunden har Region #1-slutpunkt som kallas prod.contoso.com med en statisk IP-adress som 100.168.124.44 och en Region nr 2-slutpunkt som kallas dr.contoso.com med en statisk IP-adress som 100.168.124.43. 
-   Var och en av dessa miljöer är fronted via en offentlig riktade egenskap som en belastningsutjämnare. Belastningsutjämnaren kan konfigureras så att den har en DNS-baserade slutpunkt eller ett fullständigt kvalificerat domännamn (FQDN) som ovan.
-   Alla instanser i regionen 2 finns i nära realtid replikering med regionen 1. Dessutom datorn avbildningarna är uppdaterade och alla program/konfigurationsdata är uppdaterad och är i enlighet med regionen 1.  
-   Automatisk skalning är förkonfigurerade i förväg. 

Åtgärder för att konfigurera redundans med Azure Traffic Manager är följande:
1. Skapa en ny Azure Traffic Manager-profil
2. Skapa slutpunkter i Traffic Manager-profilen
3. Ställ in health kontroll och redundans-konfiguration

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Steg 1: Skapa en ny Azure Traffic Manager-profil
Skapa en ny Azure Traffic manager-profil med namnet contoso123 och välj routningsmetod som prioritet. Om du har en befintlig resursgrupp som du vill associera med, kan du markera en befintlig resursgrupp eller skapa en ny resursgrupp.

![Skapa Traffic Manager-profil](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)
*bild – skapa en Traffic Manager-profil*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Steg 2: Skapa slutpunkter i Traffic Manager-profilen

I det här steget skapar du slutpunkter som pekar på produktions- och katastrofåterställningsplatser. Här kan välja den **typ** som en extern slutpunkt, men om resursen ligger i Azure, kan du välja **Azure-slutpunkt** samt. Om du väljer **Azure-slutpunkt**och välj sedan en **målresurs** som är antingen en **Apptjänst** eller en **offentlig IP-adress** som har allokerats av Azure. Prioriteten ställs in som **1** eftersom det är den primära tjänsten för regionen 1.
På samma sätt ska disaster recovery slutpunkten i Traffic Manager samt.

![Skapa disaster recovery-slutpunkter](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Bild - och skapa disaster recovery-slutpunkter*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Steg 3: Ställ in health kontroll och redundans-konfiguration

I det här steget ska ange du TTL för DNS till 10 sekunder, som hanteras av de flesta internet-riktade rekursiva matchare. Den här konfigurationen innebär att ingen DNS-matchare cachelagrar information för mer än 10 sekunder. För Övervakare slutpunktsinställningarna sökvägen är aktuella uppsättningen på / eller rot, men du kan anpassa slutpunktsinställningarna för att utvärdera en sökväg, till exempel prod.contoso.com/index. Exemplet nedan visar den **https** som avsöknings protokoll. Du kan dock välja **http** eller **tcp** samt. Valet av protokollet beror på slutprogrammet. Avsöknings interval är inställt på 10 sekunder, vilket gör att snabb avsökning och återförsök är inställd på 3. Därför Traffic Manager redundansväxlas till den andra slutpunkten om tre på varandra följande intervall registrerar ett fel. Följande formel definierar den totala tiden för automatisk växling: Tid för redundans = TTL + försök * Probing intervall och i så fall värdet är 10 + 3 * 10 = 40 sekunder (Max).
Om återförsök har angetts till 1 och TTL-värdet anges till 10 sekunder sedan tiden för redundans 10 + 1 * 10 = 20 sekunder. Ange återförsök till ett värde större än **1** att eliminera risken för växling vid fel på grund av falska positiva identifieringar eller några mindre nätverkssignaler. 


![Ställa in hälsokontrollen](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Bild – Ställ in health kontroll och redundans-konfiguration*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Hur automatisk redundans fungerar med Traffic Manager

Vid ett haveri hämtar den primära slutpunkten avlästes och status ändras till **försämrad** och katastrofåterställningsplatsen förblir **Online**. Traffic Manager skickar all trafik till den primära slutpunkten (högsta prioritet) som standard. Om den primära slutpunkten visas försämrad, dirigerar Traffic Manager trafiken till den andra slutpunkten så länge som den är felfri. En har möjlighet att konfigurera fler slutpunkter i Traffic Manager som kan fungera som slutpunkter för ytterligare redundans eller, som belastningsutjämnare dela belastningen mellan slutpunkter.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Läs mer om [Azure DNS](../dns/dns-overview.md).









