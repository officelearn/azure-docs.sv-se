---
title: "Vanliga frågor om Azure Programgateway | Microsoft Docs"
description: "Den här sidan innehåller svar på vanliga frågor och svar om Azure Programgateway"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: d54ee7ec-4d6b-4db7-8a17-6513fda7e392
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: davidmu
ms.openlocfilehash: f92af44df9863bbf48abb4afcf9b1505c843fadc
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Vanliga frågor för Programgateway

## <a name="general"></a>Allmänt

**FRÅGOR. Vad är Application Gateway?**

Azure Application Gateway är programmet leverans domänkontrollant (ADC) som en tjänst med olika layer 7 belastningsutjämning för dina program. Det ger hög tillgänglighet och skalbarhet tjänsten, som är fullständigt hanterade av Azure.

**FRÅGOR. Vilka funktioner stöder Programgateway?**

Programgateway stöder SSL-avlastning och SSL för slutpunkt till slutpunkt, Brandvägg för webbaserade program, cookie-baserad session tillhörighet, url sökväg-baserade routning, värd för flera plats och andra. En fullständig lista över funktioner som stöds finns [introduktion till Programgateway](application-gateway-introduction.md)

**FRÅGOR. Vad är skillnaden mellan Programgateway och Azure belastningsutjämnare?**

Programgateway är en lager 7 belastningsutjämnare, vilket innebär att den fungerar med webbtrafik endast (WebSocket-HTTP/HTTPS). Det stöder funktioner som SSL-avslutning, cookie-baserad session tillhörighet och resursallokering för trafik för belastningsutjämning. Belastningsutjämnare, Läs in saldon trafik på layer 4 (TCP/UDP).

**FRÅGOR. Vilka protokoll stöder Programgateway?**

Application Gateway har stöd för HTTP, HTTPS och WebSocket.

**FRÅGOR. Vilka resurser som stöds i dag som en del av serverdelspool?**

Serverdelspooler kan bestå av nätverkskort, skalningsuppsättningar i virtuella datorer, offentliga IP-adresser, trots att interna IP-adresser, fullständigt kvalificerade domännamnet (FQDN) och flera innehavare-servrar som Azure Web Apps. Programmet Gateway backend-poolmedlemmar är inte kopplad till en tillgänglighetsuppsättning. Medlemmar i serverdelspooler kan vara över kluster, Datacenter eller utanför Azure så länge som de har IP-anslutning.

**FRÅGOR. Vilka regioner är tjänsten?**

Programgateway är tillgänglig i alla regioner för globala Azure. Det är också tillgängliga i [Azure Kina](https://www.azure.cn/) och [Azure Government](https://azure.microsoft.com/en-us/overview/clouds/government/)

**FRÅGOR. Är detta en dedikerad distribution för min prenumeration eller delas mellan kunder?**

Programgateway är en särskild distribution i ditt virtuella nätverk.

**FRÅGOR. Är HTTP -> HTTPS omdirigering stöds?**

Omdirigering stöds. Besök [Programgateway omdirigering översikt](application-gateway-redirect-overview.md) vill veta mer.

**FRÅGOR. I vilken ordning bearbetas lyssnare?**

Lyssnare bearbetas i den ordning som de visas. Därför om en grundläggande lyssnare matchar en inkommande begäran bearbetas först.  Lyssnare för flera platser bör konfigureras innan en grundläggande lyssnare så trafik dirigeras till rätt serverdel.

**FRÅGOR. Var hittar jag Application Gateway IP- och DNS?**

När du använder en offentlig IP-adress som en slutpunkt måste kan den här informationen hittas på den offentliga IP-adressresursen eller på sidan Översikt för Programgateway i portalen. För interna IP-adresser finns detta på sidan Översikt.

**FRÅGOR. IP- eller DNS-ändras under livslängden för Programgatewayen?**

Ändra VIP om gatewayen stoppas och startas av kunden. DNS-servern som är associerade med Programgateway ändras inte under hela livscykeln för gateway. Därför rekommenderas att använda en CNAME-alias och peka på DNS-serveradress för Programgatewayen.

**FRÅGOR. Application Gateway har stöd för statisk IP-adress?**

Nej, Application Gateway har inte stöd för statiska offentliga IP-adresser, men det stöder statiska interna IP-adresser.

**FRÅGOR. Stöder Programgateway flera offentliga IP-adresser på gateway?**

Endast en offentlig IP-adress stöds på en Programgateway.

**FRÅGOR. Stöder Programgateway x vidarebefordras för huvuden?**

Ja, infogar Programgateway x vidarebefordras för x vidarebefordras proto och x vidarebefordras port huvuden i begäran vidarebefordras till serverdelen. Formatet för x vidarebefordras för huvudet är en kommaavgränsad lista över IP:Port. Giltiga värden för x vidarebefordras proto är http eller https. X vidarebefordras port Anger vilken port som begäran uppnått vid Programgatewayen.

**FRÅGOR. Hur lång tid tar det innan du distribuerar en Programgateway? Min Programgateway fortfarande fungerar när uppdateras?**

Nya Programgateway distributioner kan ta upp till 20 minuter att etablera. Ändringar i storlek/instansantal är inte störande och gatewayen förblir aktiv under denna tid.

## <a name="configuration"></a>Konfiguration

**FRÅGOR. Programgateway alltid distribueras i ett virtuellt nätverk?**

Ja, distribueras alltid Application Gateway i ett undernät för virtuellt nätverk. Det här undernätet kan endast innehålla Programgatewayer.

**FRÅGOR. Kontakta Programgateway till instanser utanför dess virtuellt nätverk?**

Programgateway kan kommunicera instanser utanför det virtuella nätverket som det är så länge det finns en IP-anslutning. Om du planerar att använda interna IP-adresser som medlemmar i serverdelen och det kräver [VNET-Peering](../virtual-network/virtual-network-peering-overview.md) eller [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

**FRÅGOR. Kan jag distribuera något annat i Programgateway undernät?**

Nej, men du kan distribuera andra programgatewayer i undernätet.

**FRÅGOR. Stöds Nätverkssäkerhetsgrupper i undernät Programgateway?**

Nätverkssäkerhetsgrupper stöds i Application Gateway-undernät med följande begränsningar:

* Undantag ställas på inkommande trafik på portarna 65503 65534 för backend-hälsotillstånd ska fungera korrekt.

* Utgående internet-anslutningen blockeras inte.

* Trafik från taggen AzureLoadBalancer måste tillåtas.

**FRÅGOR. Vad är begränsningar i Programgateway? Kan jag öka dessa gränser?**

Besök [programmet Gateway gränser](../azure-subscription-service-limits.md#application-gateway-limits) att visa gränserna.

**FRÅGOR. Kan jag använda Programgateway för både externa och interna trafik samtidigt?**

Ja, stöder Application Gateway har en intern IP-adress och en extern IP-adress per Application Gateway.

**FRÅGOR. VNet-peering stöds?**

Ja, VNet-peering stöds och är bra för trafik i andra virtuella nätverk för belastningsutjämning.

**FRÅGOR. Kan jag tala med lokala servrar när de är anslutna via ExpressRoute eller VPN-tunnlar?**

Ja, förutsatt att trafik tillåts.

**FRÅGOR. Kan jag har en serverdelspool som betjänar många program på olika portar?**

Arkitektur för Micro stöds. Du behöver flera http-inställningar som konfigurerats för avsökning på olika portar.

**FRÅGOR. Stöder anpassade avsökningar jokertecken/regex på svarsdata?**

Anpassade avsökningar stöder inte jokertecken eller regex på svarsdata. 

**FRÅGOR. Hur bearbetas regler?**

Regler bearbetas i den ordning som de är konfigurerade. Du rekommenderas att regler för flera platser konfigureras innan grundläggande regler för att minska risken att trafik dirigeras till olämpliga serverdelen som grundläggande regeln matchar baserat på port innan regeln flera platser utvärderas.

**FRÅGOR. Hur bearbetas regler?**

Regler bearbetas i den ordning de skapades. Du rekommenderas att regler för flera platser konfigureras före grundläggande regler. Genom att konfigurera flera platser lyssnare först den här konfigurationen minskar risken att trafik dirigeras till olämpliga serverdelen. Den här routning kan inträffa eftersom grundläggande regeln matchar baserat på port innan regeln flera platser utvärderas.

**FRÅGOR. Vad fältet värden för anpassade avsökningar obestämd?**

Värdfältet namnet att skicka avsökningen till. Gäller endast när flera platser har konfigurerats på Application Gateway, Använd annars ”127.0.0.1”. Det här värdet skiljer sig från den virtuella datorns värdnamn och har formatet \<protokollet\>://\<värden\>:\<port\>\<sökvägen\>.

**FRÅGOR. Kan jag godkända Programgateway åtkomst till några käll-IP-adresser?**

Det här scenariot kan göras med hjälp av NSG: er i Application Gateway-undernät. Följande begränsningar ska placeras i undernät i listan prioritetsordning:

* Tillåt inkommande trafik från käll-IP-/ IP-intervall.

* Tillåt inkommande begäranden från alla källor till portar 65503 65534 för [backend hälsa kommunikation](application-gateway-diagnostics.md).

* Tillåt inkommande Azure belastningsutjämnare avsökningar (taggen AzureLoadBalancer) och inkommande trafik i virtuella nätverk (taggen VirtualNetwork) på den [NSG](../virtual-network/virtual-networks-nsg.md).

* Blockera alla andra inkommande trafik med en neka alla regeln.

* Tillåt utgående trafik till internet för alla mål.

## <a name="performance"></a>Prestanda

**FRÅGOR. Hur stöder Programgateway hög tillgänglighet och skalbarhet?**

Application Gateway har stöd för scenarier med hög tillgänglighet när du har två eller fler instanser som har distribuerats. Azure distribuerar dessa instanser uppdatering och feltolerans domäner så att alla instanser inte växlar på samma gång. Programgateway stöder skalbarhet genom att lägga till flera instanser av samma gateway att dela belastningen.

**FRÅGOR. Hur jag uppnå katastrofåterställning över datacenter med Application Gateway?**

Kunder kan använda Traffic Manager distribuerar trafik över flera Programgatewayer i olika datacenter.

**FRÅGOR. Automatisk skalning stöds?**

Nej, men Application Gateway har ett mått på genomflödet som kan användas för att meddela dig när ett tröskelvärde uppnås. Lägga till instanser manuellt eller ändrar storlek startar inte om gatewayen och påverkar inte befintliga trafiken.

**FRÅGOR. Stöder manuell skala upp eller ned orsak driftavbrott?**

Det finns inget driftstopp, instanser är fördelade på uppgraderingsdomäner och feldomäner.

**FRÅGOR. Kan jag ändra instansstorleken från medelstora till stora utan avbrott?**

Ja, distribuerar Azure instanser uppdatering och feltolerans domäner så att alla instanser inte växlar på samma gång. Application Gateway har stöd för skalning genom att lägga till flera instanser av samma gateway att dela belastningen.

## <a name="ssl-configuration"></a>SSL-konfiguration

**FRÅGOR. Vilka certifikat som stöds på Programgateway?**

Automatiskt signerat certifikat, CA-certifikat och certifikat jokertecken stöds. EV certifikat stöds inte.

**FRÅGOR. Vilka är de aktuella krypteringssviter som stöds av Application Gateway?**

Följande är de aktuella krypteringssviter som stöds av Programgateway. Besök: [Konfigurera SSL princip versioner och krypteringssviter på Programgateway](application-gateway-configure-ssl-policy-powershell.md) att lära dig hur du anpassar SSL-alternativ.

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

**FRÅGOR. Stöder Programgateway också omkryptering av trafik till serverdelen?**

Ja, stöder Programgateway avlastning SSL och SSL för slutpunkt till slutpunkt, som krypterar trafik till serverdelen.

**FRÅGOR. Kan jag konfigurera SSL-policy för att styra SSL-protokoll versioner?**

Ja, kan du konfigurera Programgateway att neka TLS1.0 och TLS1.1 TLS1.2. SSL 2.0 och 3.0 är redan inaktiverad som standard och kan inte konfigureras.

**FRÅGOR. Kan jag konfigurera chiffersviter och principen ordning?**

Ja, [konfiguration av krypteringssviter](application-gateway-ssl-policy-overview.md) stöds. När du definierar en anpassad princip måste minst en av de följande krypteringssviter aktiveras. Programgateway använder SHA256 till för hantering av backend.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

**FRÅGOR. Hur många SSL-certifikat som stöds?**

Upp till 20 SSL som certifikat stöds.

**FRÅGOR. Hur många certifikat för klientautentisering för backend-omkryptering stöds?**

Upp till 10 autentisering stöds certifikat med ett standardvärde på 5.

**FRÅGOR. Kan Programgateway integreras med Azure Key Vault internt?**

Nej, det inte är integrerat med Azure Key Vault.

## <a name="web-application-firewall-waf-configuration"></a>Konfiguration av webbprogram Firewall (Brandvägg)

**FRÅGOR. Erbjuder Brandvägg SKU alla funktioner som är tillgängliga med Standard-SKU?**

Ja, Brandvägg stöder alla funktioner i Standard-SKU.

**FRÅGOR. Vad är versionen som CR Application Gateway har stöd för?**

Programgateway stöder CR [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) och CR [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

**FRÅGOR. Hur övervakar Brandvägg?**

Brandvägg övervakas via diagnostikloggning, mer information om diagnostikloggning kan hittas på [diagnostik loggning och mått för Programgateway](application-gateway-diagnostics.md)

**FRÅGOR. Blockerar identifieringsläget trafik?**

Nej, loggar identifieringsläget endast trafik som utlöses av en Brandvägg regel.

**FRÅGOR. Hur anpassar Brandvägg regler?**

Ja, Brandvägg regler kan anpassas för mer information om hur du anpassar dem besök [anpassa Brandvägg regelgrupper och regler](application-gateway-customize-waf-rules-portal.md)

**FRÅGOR. Vilka regler som är tillgängliga?**

Brandvägg stöder för närvarande CR [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) och [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), som ger grundläggande säkerhet mot de flesta av de översta 10 säkerhetsrisker som identifieras av den öppna Web Application säkerhet projekt (OWASP) finns här [ OWASP topp 10 säkerhetsrisker](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* Skydd mot SQL-inmatning

* Skydd mot skriptkörning över flera webbplatser

* Skydd mot vanliga webbattacker, som kommandoinmatning, dold HTTP-begäran, delning av HTTP-svar och attack genom införande av fjärrfil

* Skydd mot åtgärder som inte följer HTTP-protokollet

* Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas

* Skydd mot robotar, crawlers och skannrar

 * Identifiering av program vanliga felinställningar (det vill säga Apache, IIS osv.)

**FRÅGOR. Stöder Brandvägg också DDoS förebyggande?**

Nej, Brandvägg ger inte några DDoS förebyggande.

## <a name="diagnostics-and-logging"></a>Diagnostik- och loggning

**FRÅGOR. Vilka typer av loggar är tillgängliga med Application Gateway?**

Det finns tre loggarna för Programgateway. Mer information om dessa loggar och andra diagnostiska funktioner finns [Backend hälsa och diagnostik loggar mått för Programgateway](application-gateway-diagnostics.md).

- **ApplicationGatewayAccessLog** -åtkomst-loggen innehåller varje förfrågan har skickats till Programgateway klientdel. Innehåller information som anroparens IP begärd, URL svar svarstid, returkod, byte in och ut. Åtkomstlogg samlas in varje 300 sekunder. Den här loggfilen innehåller en post per instans av Application Gateway.
- **ApplicationGatewayPerformanceLog** -prestanda-loggen innehåller prestandainformation om för per instanser inklusive totala begäran skickades, genomflöde i byte, totalt antal begäranden som betjänats, antalet misslyckade begäranden, felfria och feltillstånd backend- instansantal.
- **ApplicationGatewayFirewallLog** -brandväggsloggen innehåller begäranden som loggas via identifiering eller förhindra läget för en Programgateway som är konfigurerad med Brandvägg för webbaserade program.

**FRÅGOR. Hur vet jag om min backend poolmedlemmar felfri?**

Du kan använda PowerShell-cmdleten `Get-AzureRmApplicationGatewayBackendHealth` eller verifiera hälsotillstånd via portalen genom att besöka [Gateway Programdiagnostik](application-gateway-diagnostics.md)

**FRÅGOR. Vad är bevarandeprincip på diagnostik loggarna?**

Diagnostikloggar flöde till kunder storage-konto och kunder kan ange den bevarandeprincip baserat på deras inställningar. Diagnostikloggar kan också skickas till en Händelsehubb eller logganalys. Besök [Gateway Programdiagnostik](application-gateway-diagnostics.md) för mer information.

**FRÅGOR. Hur skaffar jag granskningsloggarna för Programgateway**

Granskningsloggar är tillgängliga för Application Gateway. I portalen klickar du på **aktivitetsloggen** i bladet menyn för en Programgateway åtkomst till händelseloggen. 

**FRÅGOR. Kan jag ställa in aviseringar med Application Gateway?**

Ja, Application Gateway har stöd för varningar, aviseringar konfigureras av mått.  Application Gateway har för närvarande ”dataflöde”, vilket kan konfigureras för mått till aviseringen. Läs mer om aviseringar om [få aviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

**FRÅGOR. Backend-hälsa returnerar okänd status, vilka kan vara orsaken till denna status?**

Den vanligaste orsaken är åtkomst till serverdelen blockeras av en NSG eller anpassade DNS. Besök [Backend hälsa, diagnostikloggning och mått för Programgateway](application-gateway-diagnostics.md) vill veta mer.

## <a name="next-steps"></a>Nästa steg

Mer information om Programgateway besök [introduktion till Programgateway](application-gateway-introduction.md).
