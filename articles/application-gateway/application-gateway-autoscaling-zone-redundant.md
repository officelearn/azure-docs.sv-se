---
title: Automatisk skalning och zonredundant Application Gateway i Azure
description: Den här artikeln beskriver de Azure-program Standard_v2 och WAF_v2 SKU, som innehåller funktioner för automatisk skalning och zonredundant.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 5/7/2019
ms.author: victorh
ms.openlocfilehash: dfb5b8b69b2ca9bea118603406f4747036d2641c
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510824"
---
# <a name="autoscaling-and-zone-redundant-application-gateway"></a>Automatisk skalning och zonredundant Application Gateway 

Application Gateway och Web Application Firewall (WAF) är också tillgängliga under en Standard_v2 och WAF_v2 SKU. V2-SKU ger prestandaförbättringar och lägger till stöd för viktiga nya funktioner som automatisk skalning, redundans och stöd för statiska virtuella IP-adresser. Befintliga funktioner i Standard- och WAF SKU fortfarande användas i den nya v2-SKU, med några undantag som anges i [jämförelse](#differences-with-v1-sku) avsnittet.

Den nya v2-SKU: N innehåller följande förbättringar:

- **Automatisk skalning**: Application Gateway eller WAF-distributioner under autoskalning SKU kan skala upp eller ned utifrån ändrade trafikmönster belastningen. Automatisk skalning tar även bort behovet av att välja distributionsstorlek eller instansantal under etablering. Den här SKU: N erbjuder SANT elasticitet. I Standard_v2 och WAF_v2 SKU fungerar Application Gateway både fast kapacitet (automatisk skalning inaktiverat) och i läget för automatisk skalning aktiverat. Fast kapacitet läge är användbart för scenarier med konsekventa och förutsägbara arbetsbelastningar. Läget för automatisk skalning är bra i program som finns ser avvikelse i programtrafik.
- **Zon redundans**: En Application Gateway- eller WAF-distribution kan sträcka sig över flera Tillgänglighetszoner, ta bort behovet av att etablera separata Application Gateway-instanser i varje zon med Traffic Manager. Du kan välja en enskild zon eller flera zoner där Application Gateway-instanser har distribuerats, vilket gör det mer robust zon uppstår fel. Serverdelspoolen för program kan distribueras på samma sätt i olika tillgänglighetszoner.

  Redundans är tillgänglig endast om Azure Zones är tillgängligt. Alla andra funktioner som stöds i andra regioner. Mer information finns i [vad är Tillgänglighetszoner i Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **Statisk VIP**: Application gateway v2 SKU: N stöder statisk VIP skriver exklusivt. Detta säkerställer att VIP-Adressen som är associerade med application gateway inte ändras för livscykeln för distributionen, även efter en omstart.
- **Huvud-omskrivning**: Application Gateway kan du lägga till, ta bort eller uppdatera HTTP-huvuden för begäran och svar med v2-SKU. Mer information finns i [skriva om HTTP-huvuden med Application Gateway](rewrite-http-headers.md)
- **Key Vault-integrering (förhandsversion)**: Application Gateway v2 har stöd för integrering med Key Vault (i allmänt tillgänglig förhandsversion) för servercertifikat som är kopplade till HTTPS-aktiverade lyssnare. Mer information finns i [SSL-avslutning med Key Vault-certifikat](key-vault-certs.md).
- **Ingress-kontrollanten för Azure Kubernetes Service (förhandsversion)**: V2 Ingress-kontrollanten för Application Gateway kan Azure Application Gateway som ska användas som inkommande för ett Azure Kubernetes Service (AKS) kallas AKS-kluster. Mer information finns i den [dokumentationssidan](https://azure.github.io/application-gateway-kubernetes-ingress/).
- **Prestandaförbättringar**: V2 SKU: N erbjuder upp till 5 X bättre SSL-avlastning prestandan jämfört med Standard/WAF SKU.
- **Snabbare distribution och uppdatering** v2-SKU ger distribution och uppdatering snabbare jämfört med Standard/WAF SKU. Detta omfattar även WAF konfigurationsändringar.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regioner som stöds

Standard_v2 och WAF_v2 SKU finns i följande regioner: Norra centrala USA, södra centrala USA, västra USA, västra USA 2, östra USA, östra USA 2, centrala USA, Nordeuropa, Västeuropa, Sydostasien, Frankrike, centrala, Storbritannien, västra, östra Japan, västra Japan. Fler regioner kommer att läggas till i framtiden.

## <a name="pricing"></a>Prissättning

Med v2-SKU prismodellen drivs av förbrukning och inte längre är kopplad till antalet instanser eller storlekar. Priser för v2 SKU har två komponenter:

- **Fast pris** – detta är per timme (eller del av en timme) priset för att etablera en Standard_v2 eller WAF_v2 Gateway.
- **Kapacitet enhetspriset** – det här är förbrukningsbaserad kostnaden som debiteras utöver den fasta kostnaden. Kapacitetsenhet kostnad även beräknas per timme eller partiell per timme. Det finns tre dimensioner till Kapacitetsenhet – compute unit, beständiga anslutningar och dataflöde. Compute-enhet är ett mått på processor-kapaciteten som förbrukats. Faktorer som påverkar beräkningsenhet är TLS-anslutningar per sekund, URL-Omskrivningsregler beräkningar och WAF-Regelbearbetning. Beständig anslutning är ett mått på TCP-anslutningar till application gateway i ett givet intervall för fakturering. Dataflödet är genomsnittliga megabit per sekund som bearbetas av systemet i ett givet intervall för fakturering.

Varje Kapacitetsenhet består av högst: 1 compute-enhet, eller 2500 beständiga anslutningar eller 2.22 Mbit/s genomströmning.

Compute a vägledning:

- **Standard_v2** -varje beräkningsenhet klarar av cirka 50 anslutningar per sekund med RSA 2048-bitars nyckel TLS-certifikat.
- **WAF_v2** – varje compute-enhet kan stödja cirka 10 samtidiga begäranden per sekund för 70 – 30% blandning av trafik med 70% begär mindre än 2 KB få/publicera och återstående högre. WAF prestanda påverkas inte av svarsstorlek för närvarande.

> [!NOTE]
> Varje instans stöder för närvarande cirka 10 kapacitetsenheter.
> Antalet begäranden som en beräkningsenhet kan hantera beror på olika kriterier som TLS-certifikatets nyckelstorlek, nyckelutbytesalgoritmen, huvud omskrivningar och vid inkommande storleksförfrågningar med WAF. Vi rekommenderar att du kör tester för att fastställa begäran-pris per beräkningsenhet. Både Kapacitetsenhet och beräkningsenhet kommer att göras tillgängligt som ett mått innan debiteras startar.

**Priserna i USA, Öst**:

|              SKU Name                             | Fast pris ($/ tim)  | Enhetspriset för kapacitet ($/ CU-tim)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0.20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

Den [prissättningssidan](https://azure.microsoft.com/pricing/details/application-gateway/) kommer att uppdateras för att återspegla regionala priserna på den 14 maj 2019. Fakturering är schemalagd att starta den 1 juni 2019.

**Exempel 1**

En Application Gateway Standard_v2 etableras utan automatisk skalning i läget för manuell skalning med fast kapacitet på fem instanser.

Fast pris = 744(hours) * $0.20 = $148.8 <br>
Kapacitetsenheter = 744 (timmar) 10 Kapacitetsenhet per instans * fem instanser * $0.008 per kapacitet enhetstimme = $297.6

Totalt pris = $148.8 + $297.6 = $446.4

**Exempel 2**

En Application Gateway-standard_v2 etableras i en månad och under tiden får 25 nya SSL-anslutningar/sek, genomsnittlig 8.88 Mbit/s data som överförs. Under förutsättning att anslutningarna är korta bott, skulle priset bli:

Fast pris = 744(hours) * $0.20 = $148.8

Enhetspriset för kapacitet = 744(hours) * Max (25/50 beräkningsenhet för anslutningar per sekund, 8.88/2.22 Kapacitetsenhet för dataflöde) * $0.008 = 744 * 4 * 0.008 = $23.81

Totalt pris = $148. 23.81 8 + = $172.61

**Exempel 3**

En Application Gateway WAF_v2 etableras i en månad. Under tiden det tar emot 25 nya SSL-anslutningar/sek, genomsnittlig 8.88 Mbit/s data som överförs och har 80 begäran per sekund. Om vi antar att anslutningarna är kort livslängd och att beräkning enhet beräkning för programmet har stöd för 10 RPS per beräkningsenhet, priset skulle vara:

Fast pris = 744(hours) * $0.36 = $267.84

Enhetspriset för kapacitet = 744(hours) * Max (beräkning enhet Max(25/50 for connections/sec, 80/10 WAF RPS) 8.88/2.22 Kapacitetsenhet för dataflöde) * $0.0144 = 744 * 8 * 0.0144 = $85.71

Totalt pris = $267.84 + $85.71 = $353.55

Den [prissättningssidan](https://azure.microsoft.com/pricing/details/application-gateway/) kommer att uppdateras för att återspegla regionala priserna på den 14 maj 2019. Fakturering är schemalagd att starta den 1 juni 2019.

## <a name="scaling-application-gateway-and-waf-v2"></a>Skala Application Gateway och WAF v2

Application Gateway och WAF kan konfigureras att skala i två lägen:

- **Automatisk skalning** – med automatisk skalning aktiverat, Application Gateway och v2 för WAF SKU: er skala upp eller ned baserat på trafik programkrav. Det här läget ger bättre flexibilitet när det gäller att ditt program och eliminerar behovet av att gissa application gateway-storlek eller instansantal. Det här läget kan du spara kostnader genom att inte kräva att köra gateways på högsta etablerad kapacitet för förväntade maximala belastningen. Kunder måste ange en lägsta och du kan också högsta instansantal. Minimikapacitet säkerställer att Application Gateway och WAF v2 inte faller under det lägsta instansantalet anges även i frånvaron av trafik. Du kommer att faktureras för den här minimikapacitet även i frånvaron av all trafik. Du kan även ange ett maximalt instansantal, vilket garanterar att Application Gateway inte kan skalas bortom det angivna antalet instanser. Fortsätter du att debiteras den mängd trafik som hanteras av gatewayen. Antalet instanser kan vara mellan 0 och 125. Standardvärdet för maximalt instansantal är 20 om inget anges.
- **Manuell** – du kan även välja manuellt läge där gatewayen inte automatisk skalning. I det här läget, om det finns mer trafik än vilka Application Gateway eller WAF som kan hantera, kan det resultera i dataförlust trafik. Med manuell läge är det obligatoriskt att ange instansantal. Instansantal kan skilja sig från 1 till 125 instanser.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Jämförelse mellan SKU: N v1 och v2-SKU

I följande tabell jämförs funktionerna med varje SKU.

|                                                   | V1-SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Automatisk skalning                                       |          | &#x2713; |
| Redundans                                   |          | &#x2713; |
| Statisk VIP                                        |          | &#x2713; |
| Azure Kubernetes Service (AKS) Ingress-kontrollant |          | &#x2713; |
| Azure Key Vault-integrering                       |          | &#x2713; |
| Skriv om rubriker för HTTP (S)                           |          | &#x2713; |
| URL-baserad routning                                 | &#x2713; | &#x2713; |
| Värd för flera platser                             | &#x2713; | &#x2713; |
| Trafik-omdirigering                               | &#x2713; | &#x2713; |
| Brandvägg för webbaserade program (WAF)                    | &#x2713; | &#x2713; |
| Secure Sockets Layer-avslutning (SSL)            | &#x2713; | &#x2713; |
| Slutpunkt till slutpunkt SSL-kryptering                         | &#x2713; | &#x2713; |
| Sessionstillhörighet                                  | &#x2713; | &#x2713; |
| Anpassade felsidor                                | &#x2713; | &#x2713; |
| WebSocket-stöd                                 | &#x2713; | &#x2713; |
| Stöd för HTTP/2                                    | &#x2713; | &#x2713; |
| Anslutningstömning                               | &#x2713; | &#x2713; |

> [!NOTE]
> Automatisk skalning-v2 SKU: N stöder nu [standard hälsoavsökningar](application-gateway-probe-overview.md#default-health-probe) att automatiskt övervaka hälsotillståndet för alla resurser i dess backend-poolen och markera de backend-medlemmar som är anses vara felaktigt. Standard-hälsoavsökning konfigureras automatiskt för servrar som inte har en anpassad avsökningskonfiguration. Mer information finns i [hälsoavsökningar i application gateway](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Skillnader med v1-SKU

|Skillnad|Information|
|--|--|
|Certifikat för serverautentisering|Stöds ej.<br>Mer information finns i [översikt över slutpunkt till slutpunkt-SSL med Programgateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Blanda Standard_v2 och Standard Application Gateway i samma undernät|Stöds ej|
|Användardefinierad väg (UDR) i Application Gateway-undernät|Stöds ej|
|NSG för inkommande portintervall| -65200 till 65535 för Standard_v2 SKU<br>-65503 till 65534 för Standard-SKU.<br>Mer information finns i den [vanliga frågor och svar](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Prestandaloggar i Azure-diagnostik|Stöds ej.<br>Du bör använda Azure-mått.|
|Fakturering|Fakturering som schemalagts att starta den 1 juni 2019.|
|FIPS-läge|Dessa stöds inte för närvarande.|
|ILB läge|Detta stöds för närvarande inte. Offentliga och ILB-läget tillsammans stöds.|
|NetWatcher-integrering|Stöds ej.|
|Azure Support Center-integrering|Ännu inte tillgänglig.

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Direkt Internet-trafik med Azure Application Gateway – Azure-portalen](quick-create-portal.md)
- [Skapa en automatisk skalning, zonen redundant Programgateway med en reserverad virtuell IP-adress med hjälp av Azure PowerShell](tutorial-autoscale-ps.md)
- Läs mer om [Application Gateway](overview.md).
- Läs mer om [Azure brandvägg](../firewall/overview.md).