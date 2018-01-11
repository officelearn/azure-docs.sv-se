---
title: "Konfigurera Azure App Service Environment för tvingande dirigering"
description: "Gör det möjligt för App Service Environment att fungera när utgående trafik har tvingande dirigering"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/10/2017
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 4caaf0df3f1dd4b2cb9b76283a6beed897531c1c
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Konfigurera App Service Environment med tvingande dirigering

Azure App Service Environment är en distribution av Azure App Service i en kunds instans av ett virtuellt Azure-nätverk. Många kunder konfigurerar sina virtuella nätverk så att de är förlängningar av de lokala nätverken med VPN eller Azure ExpressRoute-anslutningar. Till följd av företagsprinciper eller andra säkerhetsbegränsningar konfigurerar de vägar för att skicka all utgående trafik lokalt innan den kan komma ut på internet. Att ändra vägen för det virtuella nätverket så att den utgående trafiken från det virtuella nätverket går genom VPN eller ExpressRoute-anslutningen lokalt kallas tvingande dirigering. 

Tvingande dirigering kan leda till problem för en App Service Environment-miljö. App Service Environment-miljön har ett antal externa beroenden, som anges i dokumentet [Nätverksarkitektur i App Service-miljö][network]. App Service Environment-miljön kräver som standard att all utgående kommunikation går igenom den VIP som har etablerats med App Service Environment.

Vägar är en viktig aspekt för vad tvingande dirigering är och hur man hanterar det. I ett virtuellt Azure-nätverk sker routning baserat på den längsta prefix-matchningen (LPM). Om det finns fler än en väg med samma LPM-matchning väljs en väg baserat på dess ursprung i följande ordning:

* Användardefinierad väg (UDR)
* BGP-väg (när ExpressRoute används)
* Systemväg

Mer information om routning i ett virtuellt nätverk finns i [Användardefinierade vägar och IP-vidarebefordring][routes]. 

Om du vill att App Service Environment ska arbeta i ett nätverk med tvingande händelsedirigering har du två valmöjligheter:

* Aktivera App Service Environment-miljön så att den har direktåtkomst till internet.
* Ändra den utgående slutpunkten för App Service Environment.

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Aktivera App Service Environment-miljön så att den har direktåtkomst till internet

För att App Service Environment ska fungera när det virtuella nätverket är konfigurerat med en ExpressRoute-anslutning kan du:

* Konfigurera ExpressRoute att annonsera 0.0.0.0/0. Som standard dirigeras all utgående trafik lokalt tvingande.
* Skapa en UDR. Använd den på det undernät som innehåller App Service Environment med adressprefixet 0.0.0.0/0 och Nexthop-typen Internet.

Om du gör dessa två ändringar kommer trafik med internet som mål och som kommer från undernätet i App Service Environment inte att tvingas till ExpressRoute-anslutningen, och App Service Environment fungerar.

> [!IMPORTANT]
> Vägarna som definieras i en UDR måste vara tillräckligt specifika för att få företräde framför eventuella vägar som annonseras av ExpressRoute-konfigurationen. Föregående exempel använder det breda adressintervallet 0.0.0.0/0. Det kan eventuellt åsidosättas av misstag av vägannonseringar som använder mer specifika adressintervall.
>
> App Service Environment-miljöer stöds inte med ExpressRoute-konfigurationer som korsannonserar vägar från den offentliga peering-vägen till den privata peering-vägen. ExpressRoute-konfigurationer med offentlig peering har konfigurerats för att få vägannonseringar från Microsoft. Annonseringarna innehåller ett stort antal IP-adressintervall för Microsoft Azure. Om adressintervallen korsannonseras på den privata peering-vägen kommer alla utgående nätverkspaket från undernätet för App Service Environment att dirigeras tvingande till en kunds lokala nätverksinfrastruktur. Det här nätverksflödet stöds för närvarande inte som standard med App Service Environment-miljöer. En lösning på detta problem är att stoppa korsannonsering av vägar från den offentliga peering-vägen till den privata peering-vägen. En annan lösning är att göra det möjligt för App Service Environment att arbeta i en konfiguration med tvingande dirigering.

## <a name="change-the-egress-endpoint-for-your-app-service-environment"></a>Ändra den utgående slutpunkten för App Service Environment ##

Det här avsnittet beskriver hur du gör det möjligt för App Service Environment att fungera i en konfiguration med tvingande dirigering genom att ändra den utgående slutpunkten som används av App Service Environment. Om den utgående trafiken från App Service Environment dirigeras tvingande till ett lokalt nätverk måste du göra det möjligt för den trafiken att komma från andra IP-adresser än VIP-adressen för App Service Environment.

App Service Environment har inte bara externa beroenden, utan måste även lyssna efter inkommande trafik och svara på sådan trafik. Svaren kan inte skickas tillbaka från en annan adress eftersom det bryter TCP. Tre steg krävs för att ändra den utgående slutpunkten för App Service Environment:

1. Konfigurera en routningstabell för att se till att inkommande hanteringstrafik kan gå tillbaka ut från samma IP-adress.

2. Lägg till de IP-adresser som ska användas för utgående trafik till App Service Environment-brandväggen.

3. Ange vägarna dit utgående trafik från App Service Environment ska dirigeras.

   ![Nätverksflöde med tvingande dirigering][1]

Du kan konfigurera App Service Environment med olika utgående adresser efter att App Service Environment-miljön redan är igång, eller så kan de konfigureras under App Service Environment-distributionen.

### <a name="change-the-egress-address-after-the-app-service-environment-is-operational"></a>Ändra den utgående adressen efter att App Service Environment är igång ###
1. Hämta IP-adresserna som du vill använda som utgående IP-adresser för App Service Environment. Om du använder tvingande dirigering kommer dessa adresser från NAT eller gateway-IP-adresser. Om du vill dirigera den utgående trafiken för App Service Environment genom en NVA är den utgående adressen den offentliga IP-adressen för NVA.

2. Ange utgående adresser i konfigurationsinformationen för App Service Environment. Gå till resource.azure.com och gå till Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>. Du kan då se den JSON som beskriver App Service Environment. Kontrollera att det står **läsa/skriva** längst upp. Välj **Redigera**. Bläddra längst ned och ändra värdet **userWhitelistedIpRanges** från **null** till något som liknar följande. Använd de adresser som du vill ange som utgående adressintervall. 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   Välj **PUT** längst upp. Det här alternativet utlöser en skalningsåtgärd i App Service Environment och justerar brandväggen.
 
3. Skapa eller redigera en routningstabell och fyll i reglerna för att tillåta åtkomst till/från hanteringsadresserna som mappar platsen för App Service Environment. Information om hur du hittar hanteringsadresserna finns i [App Service Environment management addresses][management] (Hanteringsadresser för App Service Environment).

4. Anpassa vägarna som används i undernätet för App Service Environment med en routningstabell eller BGP-vägar. 

Om App Service Environment slutar svara från portalen är det något problem med ändringarna. Problemet kan vara att listan med utgående adresser var ofullständig, att trafiken har förlorats eller att trafiken blockerades. 

### <a name="create-a-new-app-service-environment-with-a-different-egress-address"></a>Skapa en ny App Service Environment-miljö med en annan utgående adress ###

Om det virtuella nätverket redan har konfigurerats för tvingande dirigering av trafiken måste du följa några extra steg för att skapa App Service Environment-miljön så att den kan fungera korrekt. Du måste aktivera användning av en annan utgående slutpunkt när du skapar App Service Environment. Det gör du genom att skapa App Service Environment med en mall som anger den tillåtna utgående adressen.

1. Hämta de IP-adresser som ska användas som utgående adresser för App Service Environment.

2. Skapa undernätet som ska användas av App Service Environment i förväg. Du behöver ha det så att du kan ange vägar, samt för att mallen kräver det.

3. Skapa en routningstabell med de hanterings-IP-adresser som mappar till platsen för App Service Environment. Tilldela den till App Service Environment.

4. Följ anvisningarna i [Create App Service Environment with a template][template] (Skapa App Service Environment med en mall). Hämta lämplig mall.

5. Redigera avsnittet "resources" i filen azuredeploy.json. Inkludera en rad för **userWhitelistedIpRanges** med följande värden:

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

Om det här avsnittet konfigureras korrekt kommer App Service Environment att starta utan problem. 


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
