---
title: Haveriberedskap med hjälp av Azure DNS och Traffic Manager | Microsoft Docs
description: Översikt över de lösningar för katastrofåterställning med hjälp av Azure DNS och Traffic Manager.
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
ms.openlocfilehash: 6c46ada7fc95d5789512f8f7c7842852e6a86b69
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35249417"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Haveriberedskap med hjälp av Azure DNS och Traffic Manager

Katastrofåterställning fokuserar på att återställa från en allvarlig funktionsförlust program. För att kunna välja en lösning för katastrofåterställning, affärs- och teknikbehov ägare måste först ta reda på vilken funktionalitet som krävs under katastrofåterställning, exempelvis - otillgänglig, delvis tillgänglig via nedsatt funktionalitet eller fördröjd tillgänglighet eller helt tillgängligt.
De flesta enterprise-kunder väljer en flera regioner arkitektur för återhämtning mot ett program eller failover på infrastrukturen. Kunder kan välja flera metoder i begäran om förnyelse att uppnå redundans och hög tillgänglighet via redundanta arkitektur. Här följer några vanliga metoder:

- **Aktivt-passivt med kalla vänteläge**: I den här lösningen för växling vid fel, virtuella datorer och andra enheter som kör i vänteläge region inte är aktiva tills det behövs för redundans. Dock replikeras produktionsmiljön i form av säkerhetskopior, VM-bilder eller Resource Manager-mallar till en annan region. Den här mekanismen för växling vid fel är kostnadseffektiv, men tar längre tid att genomföra en slutföra växling vid fel.
 
    ![Aktiv/passiv med kalla vänteläge](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Bild - aktiv/passiv med kalla vänteläge katastrofberedskapskonfigurationen*

- **Aktiv/passiv pilot ljus**: I den här lösningen för växling vid fel, vänteläge miljön är konfigurerad med en minimal konfiguration. Installationsprogrammet har bara de nödvändiga tjänsterna körs så att den stöder endast en minimal och kritiska uppsättning program. I sin inbyggda form kan det här scenariot endast köra minimal funktioner men kan skala upp och starta ytterligare tjänster att vidta bulk produktion belastningen om det uppstår redundans.
    
    ![Aktiv/passiv pilot ljus](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Bild: Aktiv/passiv med pilot ljus katastrofberedskapskonfigurationen*

- **Aktiv/passiv med varmt vänteläge**: I den här lösningen för växling vid fel, standby regionen före värmas och är redo att ta den grundläggande belastningen automatisk skalning är aktiverad och alla instanser som är igång. Den här lösningen skalas inte för att dra full produktion belastningen men fungerar, och alla tjänster som är igång. Den här lösningen är en förhöjd version av piloten ljus-metoden.
    
    ![Aktiv/passiv med varmt vänteläge](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Bild: Aktiv/passiv med varmt vänteläge katastrofberedskapskonfigurationen*
    
Mer information om växling vid fel och hög tillgänglighet finns [katastrofåterställning för Azure-program](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Planera din disaster recovery-arkitekturen

Det finns två tekniska aspekter för att konfigurera din disaster recovery-arkitekturen:
-  Med hjälp av en mekanism för distributionen för att replikera instanser, data och konfigurationer mellan primär och vänteläge miljöer. Den här typen av återställning kan göras internt via Azure-Webbplatsåterställning via Microsoft Azure installationer/partnertjänster som Veritas eller NetApp. 
- Utveckla en lösning för att omdirigera nätverk/webbtrafik från den primära platsen till webbplatsen vänteläge. Den här typen av återställning kan ske via Azure DNS, Azure trafik Manager(DNS) eller globala belastningsutjämnare från tredje part.

Den här artikeln är begränsad till sätt via nätverks- och omdirigering av trafik. Anvisningar att ställa in Azure Site Recovery finns [Azure Site Recovery-dokumentationen](https://docs.microsoft.com/azure/site-recovery/).
DNS är ett av de mest effektiva mekanismerna för att omdirigera nätverkstrafik eftersom DNS ofta globala och externa till datacentret och är isolerat från regionala eller tillgänglighet zonen (AZ) nivå fel. Kan använda en mekanism för DNS-baserade redundans och i Azure, två DNS-tjänster uppnå samma på något sätt - Azure DNS (auktoritära DNS) och Azure Traffic Manager (DNS-baserade smarta trafikroutning). 

Det är viktigt att förstå några begrepp i DNS som ofta används för att diskutera lösningar som tillhandahålls i den här artikeln:
- **DNS A Record** – A-poster är pekare som pekar en domän till en IPv4-adress. 
- **CNAME-eller Canonical** -posttypen används för att peka till en annan DNS-post. CNAME svarar inte med ett IP-svar utan i stället pekaren till den post som innehåller IP-adressen. 
- **Viktade routning** – kan du välja att koppla en vikt till Tjänsteslutpunkter och sedan distribuera trafiken baserat på de tilldelade vikterna. Den här routningsmetoden är en av fyra trafik routning metoder tillgängliga i Traffic Manager. Mer information finns i [viktas routningsmetoden](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Prioritet routning** – prioritet routning baseras på hälsokontroller slutpunkter. Azure Traffic manager skickar all trafik till högsta prioritet slutpunkten som standard och när ett fel eller haveri Traffic Manager dirigerar trafik till sekundär slutpunkten. Mer information finns i [prioritet routningsmetoden](../traffic-manager/traffic-manager-routing-methods.md#priority).

## <a name="manual-failover-using-azure-dns"></a>Manuell redundans med Azure DNS
Azure DNS-manuell växling lösning för katastrofåterställning använder mekanismen för standard DNS för redundans till säkerhetskopieringsplatsen. Det manuella alternativet via Azure DNS fungerar bäst när de används tillsammans med kalla vänteläge eller pilot ljus-metoden. 

![Manuell redundans med Azure DNS](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Bild - manuell växling vid fel med hjälp av Azure DNS*

Antaganden som gjordes under lösningen är:
-   Både primär och sekundär slutpunkter har statiska IP-adresser som inte ändras ofta. Är Säg IP-Adressen för den primära platsen 100.168.124.44 och IP-Adressen för den sekundära platsen är 100.168.124.43.
-   Det finns ett Azure DNS-zonen för både primär och sekundär plats. Säg för den primära platsen slutpunkten är prod.contoso.com och för säkerhetskopieringsplatsen dr.contoso.com. Det finns också en DNS-post för programmets huvudsakliga kallas www.contoso.com.   
-   TTL-värdet är vid eller under RTO SLA i organisationen. Om exempelvis ett företag anger Återställningstidsmålet av programmet katastrofåterställning svaret ska 60 minuter TTL-värdet ska vara mindre än 60 minuter, helst nedre desto bättre. Du kan ställa in Azure DNS för manuell växling på följande sätt:
1. Skapa en DNS-zon
2. Skapa DNS-zonen poster
3. Uppdatera CNAME-post

### <a name="step-1-create-a-dns"></a>Steg 1: Skapa en DNS-
Skapa en DNS-zon (t.ex, www.contoso.com) enligt nedan:

![Skapa en DNS-zon i Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Bild - skapa en DNS-zon i Azure*

### <a name="step-2-create-dns-zone-records"></a>Steg 2: Skapa DNS-zonen poster

Skapa tre poster (till exempel - www.contoso.com, prod.contoso.com och dr.consoto.com) som visas nedan i den här zonen.

![Skapa DNS-zonen poster](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Bild - skapa DNS-zonen poster i Azure*

I det här scenariot, plats, www.contoso.com har ett TTL-värde på 30 minuter, som är betydligt lägre än den angivna RTO och pekar till produktion plats prod.contoso.com. Den här konfigurationen är under normala verksamheten. TTL-värde på prod.contoso.com och dr.contoso.com har ställts in på 300 sekunder eller 5 minuter. Du kan använda en Azure övervakningstjänsten som Azure-Monitor eller Azure App Insights eller valfri partner övervakningslösningar, till exempel Dynatrace, du kan även använda home odlas lösningar som kan övervaka eller identifiera program eller virtuell infrastruktur nivån fel.

### <a name="step-3-update-the-cname-record"></a>Steg 3: Uppdatera CNAME-post

När fel upptäcks ändra Postvärde att peka till dr.contoso.com enligt nedan:
       
![Uppdatera CNAME-post](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Bild - uppdatera CNAME-post i Azure*

Inom 30 minuter, då de flesta matchare uppdateras cachelagrade zonfilen omdirigeras alla www.contoso.com frågan till dr.contoso.com.
Du kan också köra följande Azure CLI-kommando för att ändra värdet för CNAME:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Det här steget kan köras manuellt eller via automation. Det kan göras manuellt via konsolen eller genom Azure CLI. Azure SDK och API kan användas för att automatisera CNAME-uppdatering så att ingen manuell åtgärd krävs. Automatisering kan vara inbyggt via Azure functions eller i ett övervakning program från tredje part eller även från lokalt.

### <a name="how-manual-failover-works-using-azure-dns"></a>Hur manuell redundans fungerar med Azure DNS
Eftersom DNS-servern är utanför zonen växling vid fel eller katastrof, är det isolerade mot driftavbrott. Detta gör att användare kan skapa ett scenario med enkel växling vid fel som är kostnadseffektiv och fungerar alla den tid under förutsättning att operatören har nätverksanslutning under katastrofåterställning och kan göra Vänd. Om lösningen skripta, måste en Kontrollera att servern eller -tjänsten körs skriptet bör skyddas mot detta att påverka produktionsmiljön. Dessutom Kom ihåg låg TTL-värdet som har ställts in mot zonen så att ingen lösare runtom i världen för att hålla den slutpunkt som cachelagrats för lång och kunder kan komma åt platsen i Återställningstidsmålet. För cold vänteläge och pilot ljus, eftersom vissa prewarming och andra administrativa aktiviteter kan krävas – bör en också innehålla tillräckligt med tid innan du gör Vänd.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatisk redundans med Azure Traffic Manager
När du har komplexa arkitektur och flera uppsättningar av resurser som kan utföra samma funktion, kan du konfigurera Azure Traffic Manager (baserat på DNS) för att kontrollera hälsotillståndet för dina resurser och vidarebefordra trafiken från icke hälsosamma resursen till den felfri resurs. I följande exempel har en fullständig distribution av både den primära regionen och den sekundära regionen. Den här distributionen omfattar cloud services och synkroniserade databasen. 

![Automatisk redundans med Azure Traffic Manager](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Bild - automatisk redundans med Azure Traffic Manager*

Endast den primära regionen är aktivt hanterar nätverksbegäranden från användarna. Den sekundära regionen aktiveras endast när den primära regionen påträffar ett avbrott i tjänsten. I så fall kan vidarebefordra alla nya nätverksbegäranden till den sekundära regionen. Sedan säkerhetskopieringen av databasen är nästan omedelbar, både belastningsutjämnare har IP-adresser som kan vara hälsa alternativet är markerat och instanserna är alltid igång och körs, den här topologin innehåller ett alternativ för att gå för låga RTO och redundans utan någon manuell åtgärd. Den sekundära växling vid fel regionen måste vara redo att gå live omedelbart efter fel på den primära regionen.
Det här scenariot är perfekt för användning av Azure Traffic Manager som har inbyggda avsökningar för olika typer av hälsokontroller inklusive http / https- och TCP. Azure Traffic manager har också en regel motor som kan konfigureras för växling vid fel när ett fel inträffar enligt beskrivningen nedan. Nu ska vi titta på följande lösning med Traffic Manager:
- Kunden har Region #1 slutpunkten kallas prod.contoso.com med en statisk IP-adress som 100.168.124.44 och en Region #2-slutpunkt som kallas dr.contoso.com med en statisk IP-adress som 100.168.124.43. 
-   Var och en av dessa miljöer är fronted via en offentlig Internetriktade egenskap som en belastningsutjämnare. Belastningsutjämnaren kan konfigureras så att den har en slutpunkt för DNS-baserade eller ett fullständigt kvalificerat domännamn (FQDN) som du ser ovan.
-   Alla instanser i Region 2 finns i nära realtid replikering med Region 1. Dessutom datoravbildningar är uppdaterade och all programvara/konfigurationsdata är korrigeras och följer Region 1.  
-   Autoskalning förkonfigureras på förhand. 

Åtgärder för att konfigurera växling vid fel med Azure Traffic Manager är följande:
1. Skapa en ny Azure Traffic Manager-profil
2. Skapa slutpunkter i Traffic Manager-profilen
3. Konfiguration av health check och redundans

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Steg 1: Skapa en ny Azure Traffic Manager-profil
Skapa en ny Azure Traffic manager-profil med namnet contoso123 och välj routningsmetod som prioritet. Om du har en befintlig resursgrupp som du vill associera med, kan du markera en befintlig resursgrupp, annars kan skapa en ny resursgrupp.

![Skapa Traffic Manager-profilen](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)
*bild – skapa en trafikhanterarprofil*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Steg 2: Skapa slutpunkter inom Traffic Manager-profilen

I det här steget skapar du slutpunkter som pekar på produktions- och katastrofåterställningsplatser. Här, välja den **typen** som en extern slutpunkt, men om resursen finns i Azure, kan du välja **Azure endpoint** samt. Om du väljer **Azure endpoint**och välj en **mål resurs** som är antingen en **Apptjänst** eller en **offentliga IP-Adressen** som har allokerats av Azure. Prioriteten ställs in som **1** eftersom det är den primära tjänsten för Region 1.
På liknande sätt skapa disaster recovery slutpunkten i Traffic Manager samt.

![Skapa disaster recovery-slutpunkter](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Bild - och skapa disaster recovery-slutpunkter*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Steg 3: Ställ in health kontroll och redundans-konfiguration

I det här steget kan du ställa in DNS TTL till 10 sekunder som hanteras av de flesta internet-riktade rekursiv matchare. Den här konfigurationen innebär att ingen DNS-matchare cachelagrar information för fler än 10 sekunder. Sökvägen för övervakaren slutpunktsinställningar är aktuella uppsättningen med / eller rot, men du kan anpassa inställningarna för slutpunkten att utvärdera en sökväg, till exempel prod.contoso.com/index. Exemplet nedan visar den **https** som avsöknings protokoll. Du kan dock **http** eller **tcp** samt. Val av protokoll som är beroende av programmet. Avsöknings intervallet anges till 10 sekunder, vilket innebär att snabb avsökning och det nya försöket är inställd på 3. Traffic Manager kommer därför växling till den andra slutpunkten om tre på varandra följande intervall registrera ett fel. Följande formel definierar den sammanlagda tiden för en automatisk redundans: tid för växling vid fel = TTL + försök * intervall för sökning och i så fall värdet är 10 + 3 * 10 = 40 sekunder (Max).
Om det nya försöket har angetts till 1 och TTL anges till 10 sekunder sedan tiden för växling vid fel 10 + 1 * 10 = 20 sekunder. Ange det nya försöket till ett värde större än **1** eliminera risken för växling vid fel på grund av falska positiva identifieringar eller några mindre nätverk signaler. 


![Ställ in hälsokontroll](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Bild - konfiguration av health check och redundans*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Hur automatisk redundans fungerar med Traffic Manager

Vid en katastrof primära slutpunkten hämtar avsöktes och status ändras till **försämrad** och disaster recovery platsen förblir **Online**. Som standard skickar Traffic Manager all trafik till den primära (högsta prioritet)-slutpunkten. Om den primära slutpunkten visas försämrad, dirigerar Traffic Manager trafik till andra slutpunkten så länge det är felfri. Har du möjlighet att konfigurera flera slutpunkter i Traffic Manager som kan fungerar som slutpunkter för ytterligare redundans eller, som belastningsutjämnare delning belastningen mellan slutpunkter.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Lär dig mer om [Azure DNS](../dns/dns-overview.md).









