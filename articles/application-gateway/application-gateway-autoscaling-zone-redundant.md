---
title: Automatisk skalning och zonredundant Application Gateway v2
description: Den här artikeln introducerar Azure Application Standard_v2 och WAF_v2 SKU, som innehåller funktioner för automatisk skalning och zon redundant.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 7feb0f00c5431048d19d4ad6cb3860f6eb8ed052
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312709"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Automatisk skalning och zonredundant Application Gateway v2 

Waf (Application Gateway and Web Application Firewall) finns också under en Standard_v2 och WAF_v2 SKU. V2 SKU erbjuder prestandaförbättringar och lägger till stöd för viktiga nya funktioner som automatisk skalning, zonredundans och stöd för statiska VIP-adresser. Befintliga funktioner under standard- och WAF SKU fortsätter att stödjas i den nya [comparison](#differences-with-v1-sku) v2 SKU, med några få undantag listade i jämförelseavsnittet.

Den nya v2 SKU innehåller följande förbättringar:

- **Automatisk skalning**: Application Gateway- eller WAF-distributioner under den automatiska skalnings-SKU:n kan skalas upp eller ned baserat på ändrade trafikbelastningsmönster. Automatisk skalning tar även bort behovet av att välja distributionsstorlek eller instansantal under etablering. Denna SKU erbjuder sann elasticitet. I Standard_v2 och WAF_v2 SKU kan Application Gateway fungera både i fast kapacitet (automatisk skalning inaktiverad) och i automatiskt skalningsaktiverat läge. Läget fast kapacitet är användbart för scenarier med konsekventa och förutsägbara arbetsbelastningar. Automatiskt skalningsläge är fördelaktigt i program som ser varians i programtrafik.
- **Zonredundans:** En Application Gateway- eller WAF-distribution kan sträcka sig över flera tillgänglighetszoner och ta bort behovet av att etablera separata Application Gateway-instanser i varje zon med en Traffic Manager. Du kan välja en zon eller flera zoner där Application Gateway-instanser distribueras, vilket gör den mer motståndskraftig mot zonfel. Backend-poolen för program kan distribueras på samma sätt mellan tillgänglighetszoner.

  Zonredundans är endast tillgängligt där Azure Zoner är tillgängliga. I andra regioner stöds alla andra funktioner. Mer information finns [i Vad är tillgänglighetszoner i Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **Statisk VIP:** Application Gateway v2 SKU stöder statisk VIP-typ exklusivt. Detta säkerställer att VIP som är associerat med programgatewayen inte ändras för distributionens livscykel, även efter en omstart.  Det finns inte en statisk VIP i v1, så du måste använda programgateway-URL:en i stället för IP-adressen för domännamnsroutning till App Services via programgatewayen.
- **Rubrik skriva om:** Application Gateway kan du lägga till, ta bort eller uppdatera HTTP begäran och svar rubriker med v2 SKU. Mer information finns i [Skriva om HTTP-huvuden med Application Gateway](rewrite-http-headers.md)
- **Key Vault Integration**: Application Gateway v2 stöder integrering med Key Vault för servercertifikat som är kopplade till HTTPS-aktiverade lyssnare. Mer information finns i [TLS-avslutning med Key Vault-certifikat](key-vault-certs.md).
- **Azure Kubernetes Service Ingress Controller**: Application Gateway v2 Ingress Controller gör att Azure Application Gateway kan användas som inträngning för en Azure Kubernetes Service (AKS) som kallas AKS Cluster. Mer information finns [i Vad är Ingress Controller för programgateway?](ingress-controller-overview.md).
- **Prestandaförbättringar:** V2 SKU erbjuder upp till 5X bättre TLS avlastningsprestanda jämfört med Standard/WAF SKU.
- **Snabbare driftsättnings- och uppdateringstid** V2 SKU ger snabbare distribution och uppdateringstid jämfört med Standard/WAF SKU. Detta inkluderar även KONFIGURATIONSÄNDRINGAR FÖR WAF.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regioner som stöds

Standard_v2 och WAF_v2 SKU finns i följande regioner: Norra centrala USA, Södra centrala USA, Västra USA, Västra USA 2, Östra USA, Östra USA 2, Centrala USA, Norra Europa, Västeuropa, Sydostasien, Frankrike Centrala, Västra Storbritannien, Japan Öst, Japan Öst, Västra Australien, Östra Australien, Sydöstran, Södra Brasilien, Kanada Central, Kanada Öst, Östra Asien, Sydkorea Central, Korea Syd, Sydkorea , Södra Storbritannien, Centrala Indien, Västra Indien, Södra Indien.

## <a name="pricing"></a>Prissättning

Med v2 SKU drivs prismodellen av förbrukning och är inte längre kopplad till instansantal eller storlekar. V2 SKU-prissättningen har två komponenter:

- **Fast pris** - Detta är timpris (eller partiell timme) pris för att etablera en Standard_v2 eller WAF_v2 Gateway. Observera att ytterligare 0 fall fortfarande garanterar hög tillgänglighet för tjänsten som alltid ingår i fast pris.
- **Kapacitet A-pris** - Detta är en förbrukningsbaserad kostnad som debiteras utöver den fasta kostnaden. Kapacitetsenhetsladdning beräknas också per timme eller delvis per timme. Det finns tre dimensioner till kapacitetsenhet - beräkningsenhet, beständiga anslutningar och dataflöde. Beräkningsenhet är ett mått på processorkapacitet som förbrukas. Faktorer som påverkar beräkningsenhet är TLS-anslutningar/sek, URL Skriv om beräkningar och WAF-regelbearbetning. Beständig anslutning är ett mått på etablerade TCP-anslutningar till programgatewayen i ett givet faktureringsintervall. Dataflöde är genomsnittligt Megabit/sek som bearbetas av systemet under ett givet faktureringsintervall.  Faktureringen görs på en kapacitetsenhetsnivå för allt som ligger över antalet reserverade instanser.

Varje kapacitetsenhet består av högst: 1 beräkningsenhet eller 2500 beständiga anslutningar, eller 2,22 Mbit/s dataflöde.

Vägledning för beräkningsenheter:

- **Standard_v2** - Varje beräkningsenhet kan hantera cirka 50 anslutningar per sekund med RSA 2048-bitars TLS-certifikat.
- **WAF_v2** - Varje beräkningsenhet kan stödja cirka 10 samtidiga begäranden per sekund för 70-30% blandning av trafik med 70% begäranden mindre än 2 KB GET/POST och återstående högre. WAF-prestanda påverkas inte av svarsstorleken för närvarande.

> [!NOTE]
> Varje instans kan för närvarande stödja cirka 10 kapacitetsenheter.
> Antalet begäranden som en beräkningsenhet kan hantera beror på olika kriterier som TLS-certifikatnyckelstorlek, nyckelutbytesalgoritm, omskrivningar av huvuden och i händelse av WAF-inkommande begäranstorlek. Vi rekommenderar att du utför programtester för att fastställa begäranden per beräkningsenhet. Både kapacitetsenhet och beräkningsenhet kommer att göras tillgängliga som ett mått innan faktureringen startar.

I följande tabell visas exempelpriser och endast för illustrationsändamål.

**Prissättning i ÖSTRA USA:**

|              SKU-namn                             | Fast pris ($/timme)  | Kapacitet A-pris ($/CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0.20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

Mer prisinformation finns på [prissidan](https://azure.microsoft.com/pricing/details/application-gateway/). 

**Exempel 1**

En Application Gateway-Standard_v2 etableras utan automatisk skalning i manuellt skalningsläge med fast kapacitet på fem instanser.

Fast pris = 744(timmar) * $0.20 = $148.8 <br>
Kapacitetsenheter = 744 (timmar) * 10 kapacitetsenhet per instans * 5 instanser * $0,008 per kapacitetsenhettimme = $297.6

Totalt pris = $148.8 + $297.6 = $446.4

**Exempel 2**

En Application Gateway standard_v2 är etablerad för en månad, med noll minsta instanser, och under denna tid tar emot 25 nya TLS-anslutningar/sek, i genomsnitt 8,88 Mbit/s dataöverföring. Förutsatt anslutningar är kortlivade, skulle ditt pris vara:

Fast pris = 744(timmar) * $0.20 = $148.8

Kapacitetspris = 744(timmar) * Max (25/50 beräkningsenhet för anslutningar/sek, 8,88/2,22 kapacitetsenhet för dataflöde) * $0.008 = 744 * 4 * 0.008 = $23.81

Totalt pris = $148.8+23.81 = $172.61

Som du kan se debiteras du bara för fyra kapacitetsenheter, inte för hela instansen. 

> [!NOTE]
> Funktionen Max returnerar det största värdet i ett par värden.


**Exempel 3**

En Application Gateway standard_v2 är etablerad för en månad, med minst fem instanser. Förutsatt att det inte finns någon trafik och anslutningar är kortlivade, skulle ditt pris vara:

Fast pris = 744(timmar) * $0.20 = $148.8

Kapacitetspris = 744(timmar) * Max (0/50 beräkningsenhet för anslutningar/sek, 0/2,22 kapacitetsenhet för dataflöde) * $0.008 = 744 * 50 * 0.008 = $297.60

Totalt pris = $148.80+297.60 = $446.4

I det här fallet faktureras du för hela de fem instanserna även om det inte finns någon trafik.

**Exempel 4**

En Application Gateway-standard_v2 har etablerats för en månad, med minst fem instanser, men den här gången finns det ett genomsnitt på 125 Mbit/s dataöverföring och 25 TLS-anslutningar per sekund. Förutsatt att det inte finns någon trafik och anslutningar är kortlivade, skulle ditt pris vara:

Fast pris = 744(timmar) * $0.20 = $148.8

Kapacitetspris = 744(timmar) * Max (25/50 beräkningsenhet för anslutningar/sek, 125/2,22 kapacitetsenhet för dataflöde) * $0.008 = 744 * 57 * 0.008 = $339.26

Totalt pris = $148.80+339.26 = $488.06

I det här fallet debiteras du för hela fem instanser, plus sju kapacitetsenheter (vilket är 7/10 av en instans).  

**Exempel 5**

En application gateway-WAF_v2 har etablerats för en månad. Under denna tid tar den emot 25 nya TLS-anslutningar/sek, i genomsnitt 8,88 Mbit/s dataöverföring och gör 80 begäranden per sekund. Förutsatt att anslutningarna är kortlivade och att beräkningsenhetsberäkningen för programmet stöder 10 RPS per beräkningsenhet, skulle ditt pris vara:

Fast pris = 744(timmar) * $0.36 = $267.84

Kapacitetspris = 744(timmar) * Max (beräkningsenhet Max(25/50 för anslutningar/sek, 80/10 WAF RPS), 8,88/2,22 kapacitetsenhet för dataflöde) * $0.0144 = 744 * 8 * 0.0144 = $85.71

Totalt pris = $267.84 + $85.71 = $353.55

> [!NOTE]
> Funktionen Max returnerar det största värdet i ett par värden.

## <a name="scaling-application-gateway-and-waf-v2"></a>Skalning av programgateway och WAF v2

Application Gateway och WAF kan konfigureras för att skalas i två lägen:

- **Automatisk skalning** – Med automatisk skalning aktiverat skalas Application Gateway och WAF v2 SKU:er upp eller ned baserat på programtrafikkrav. Det här läget ger bättre elasticitet till ditt program och eliminerar behovet av att gissa programmet gateway storlek eller instans räknas. I det här läget kan du också spara kostnader genom att inte kräva att gatewayen körs med maximal etableringskapacitet för förväntad maximal trafikbelastning. Du måste ange ett minsta och eventuellt maximalt antal instanser. Minsta kapacitet säkerställer att Application Gateway och WAF v2 inte faller under det angivna minsta antalet instanser, även i avsaknad av trafik. Varje instans räknas som ytterligare 10 reserverade kapacitetsenheter. Zero betyder ingen reserverad kapacitet och är rent autoscaling i naturen. Observera att inga ytterligare minsta instanser fortfarande säkerställer hög tillgänglighet för tjänsten som alltid ingår i fast pris. Du kan också ange ett maximalt antal instanser, vilket säkerställer att Programgatewayen inte skalas utöver det angivna antalet instanser. Du fortsätter att faktureras för mängden trafik som betjänas av gatewayen. Instansantalet kan variera från 0 till 125. Standardvärdet för maximalt antal instanser är 20 om det inte anges.
- **Manuell** - Du kan alternativt välja manuellt läge där gatewayen inte automatiskt ska skalas. I det här läget, om det finns mer trafik än vad Application Gateway eller WAF kan hantera, kan det resultera i trafikförlust. Med manuellt läge är det obligatoriskt att ange instansantal. Antalet instanser kan variera från 1 till 125 instanser.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Funktionsjämnad mellan v1 SKU och v2 SKU

I följande tabell jämförs de funktioner som är tillgängliga med varje SKU.

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Automatisk skalning                                       |          | &#x2713; |
| Zon redundans                                   |          | &#x2713; |
| Statisk VIP                                        |          | &#x2713; |
| Aks-ingress-styrenhet (Azure Kubernetes Service) |          | &#x2713; |
| Azure Key Vault-integrering                       |          | &#x2713; |
| Skriv om HTTP(S)-huvuden                           |          | &#x2713; |
| URL-baserad routning                                 | &#x2713; | &#x2713; |
| Värd för flera platser                             | &#x2713; | &#x2713; |
| Omdirigering av trafik                               | &#x2713; | &#x2713; |
| Brandvägg för webbaserade program (WAF)                    | &#x2713; | &#x2713; |
| Anpassade WAF-regler                                  |          | &#x2713; |
| Avslutande av transportlagersäkerhet (TLS)/SSL-avslutning (Secure Sockets Layer)            | &#x2713; | &#x2713; |
| TLS-kryptering från slutpunkt till slutpunkt                         | &#x2713; | &#x2713; |
| Sessionstillhörighet                                  | &#x2713; | &#x2713; |
| Anpassade felsidor                                | &#x2713; | &#x2713; |
| WebSocket-stöd                                 | &#x2713; | &#x2713; |
| Stöd för HTTP/2                                    | &#x2713; | &#x2713; |
| Anslutningstömning                               | &#x2713; | &#x2713; |

> [!NOTE]
> Den automatiska skalning v2 SKU stöder nu [standard hälsa sonder](application-gateway-probe-overview.md#default-health-probe) för att automatiskt övervaka hälsotillståndet för alla resurser i backend pool och markera de serverdel medlemmar som anses vara ohälsosamt. Standardhälsoavsökningen konfigureras automatiskt för serveringar som inte har någon anpassad avsökningskonfiguration. Mer information finns [i hälsoavsökningar i programgateway](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Skillnader med v1 SKU

|Skillnad|Information|
|--|--|
|Autentiseringscertifikat|Stöds inte.<br>Mer information finns i [Översikt över TLS från till med Application Gateway](ssl-overview.md#end-to-end-tls-with-the-v2-sku).|
|Blanda Standard_v2 och standardprogramgateway i samma undernät|Stöds inte|
|UDR (User-Defined Route) i undernätet Application Gateway|Stöds (specifika scenarier). I förhandsgranskning.<br> Mer information om scenarier som stöds finns i [konfigurationsöversikt för Programgateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet).|
|NSG för inkommande portområde| - 65200 till 65535 för Standard_v2 SKU<br>- 65503 till 65534 för Standard SKU.<br>Mer information finns i [vanliga frågor](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet)och svar .|
|Prestandaloggar i Azure-diagnostik|Stöds inte.<br>Azure-mått bör användas.|
|Fakturering|Faktureringen beräknas starta den 1 juli 2019.|
|FIPS-läge|Dessa stöds för närvarande inte.|
|Endast ILB-läge|Detta stöds för närvarande inte. Offentliga och ILB-läge tillsammans stöds.|
|Netwatcher integration|Stöds inte.|
|Azure Security Center-integration|Ännu inte tillgänglig.

## <a name="migrate-from-v1-to-v2"></a>Migrera från v1 till v2

Ett Azure PowerShell-skript finns i PowerShell-galleriet som hjälper dig att migrera från din v1 Application Gateway/WAF till v2 Autoscaling SKU. Det här skriptet hjälper dig att kopiera konfigurationen från din v1-gateway. Trafikmigration är fortfarande ditt ansvar. Mer information finns i [Migrera Azure Application Gateway från v1 till v2](migrate-v1-v2.md).

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure Portal](quick-create-portal.md)
- [Skapa en zon redundant programgateway med en reserverad virtuell IP-adress med Azure PowerShell](tutorial-autoscale-ps.md)
- Läs mer om [Application Gateway](overview.md).
- Läs mer om [Azure-brandväggen](../firewall/overview.md).
