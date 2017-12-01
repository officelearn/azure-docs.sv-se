---
title: "Konfigurera din Azure Apptjänst-miljö för att framtvinga tunneldata"
description: "Aktivera din Apptjänst-miljö ska fungera när utgående trafik är kraft tunneldata"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: ccompy
ms.openlocfilehash: f5f099042cefe666e22a9d561afeb4584db3d92c
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Konfigurera din Apptjänst-miljö med Tvingad tunneltrafik

Apptjänst-miljön är en distribution av Azure App Service i kundens instans av Azure Virtual Network. Många kunder konfigurera sina virtuella nätverk som tillägg i sina lokala nätverk med VPN eller Azure ExpressRoute anslutningar. På grund av företagets principer eller andra villkor för säkerhet konfigurera de vägar för att skicka alla utgående trafik lokala innan det kan gå till internet. Ändra routning av det virtuella nätverket så att utgående trafik från det virtuella nätverket flödar via VPN eller ExpressRoute-anslutning till lokalt kallas Tvingad tunneling. 

Tvingad tunneling kan orsaka problem för en Apptjänstmiljö. Apptjänst-miljön har ett antal externa beroenden, som räknas upp i den [Apptjänstmiljö nätverksarkitektur] [ network] dokumentet. Apptjänst-miljön, kräver som standard att alla utgående kommunikation går igenom VIP som har etablerats med Apptjänst-miljön.

Flöden är en viktig aspekt för vilka Tvingad tunneling är och hur du arbetar med den. I Azure-nätverk, så sköts routningen baserat på längsta prefixmatchning (LPM). Om det finns fler än en väg med samma LPM-matchning, väljs en väg baserat på dess ursprung i följande ordning:

* Användardefinierad väg (UDR)
* BGP-väg (när ExpressRoute används)
* Systemväg

Mer information om routning i ett virtuellt nätverk [användardefinierade vägar och IP-vidarebefordring][routes]. 

Om du vill att din Apptjänst-miljö att arbeta i ett virtuellt nätverk i framtvingat tunnel har du två alternativ:

* Aktivera din Apptjänst-miljö ha direkt tillgång till internet.
* Ändra utgång slutpunkten för din Apptjänst-miljö.

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Aktivera din Apptjänst-miljö ha direkt tillgång till internet

För din Apptjänst-miljö att arbeta medan det virtuella nätverket konfigureras med en ExpressRoute-anslutning, kan du:

* Konfigurera ExpressRoute att annonsera 0.0.0.0/0. Som standard den tvinga tunnlar alla utgående trafik lokalt.
* Skapa en UDR. Tillämpa den på det undernät som innehåller Apptjänst-miljön med ett adressprefix 0.0.0.0/0 och en nästa hopptyp över Internet.

Om du ändrar de här två, internet-avsedda trafik från undernätet Apptjänstmiljö tvingas inte ned ExpressRoute-anslutningen och fungerar Apptjänst-miljön.

> [!IMPORTANT]
> De vägar som definierats i en UDR måste vara specifikt nog att företräde framför alla vägarna som annonseras av ExpressRoute-konfigurationen. Föregående exempel använder adressintervallet bred 0.0.0.0/0. Den kan potentiellt av misstag åsidosättas av flödet annonser som används mer specifika adressintervall.
>
> Apptjänstmiljöer stöds inte med ExpressRoute-konfigurationer som cross-annonserar vägar från offentlig peering-sökvägen till privat peering-sökväg. ExpressRoute-konfigurationer med offentlig peering konfigurerats få vägannonser från Microsoft. Annonserna innehåller ett stort antal Microsoft Azure IP-adressintervall. Om adressintervallen mellan annonseras i privat peering-sökvägen, är alla utgående paket från den Apptjänstmiljö undernät kraft tunneldata kundens lokala nätverkets infrastruktur. Det här flödet i nätverk stöds för närvarande inte som standard med Apptjänstmiljöer. En lösning på problemet är att stoppa mellan reklam vägar från offentlig peering-sökvägen till privat peering-sökväg. En annan lösning är att aktivera din Apptjänst-miljö att arbeta i en konfiguration framtvingad tunnel.

## <a name="change-the-egress-endpoint-for-your-app-service-environment"></a>Ändra utgång slutpunkten för din Apptjänst-miljö ##

Det här avsnittet beskriver hur du aktiverar en Apptjänst-miljö att arbeta i en konfiguration med framtvingad tunnel genom att ändra utgång-slutpunkt som används av Apptjänst-miljön. Om utgående trafik från Apptjänst-miljön är Tvingad Tunneling skickas till ett lokalt nätverk, måste du tillåta att trafik till datakälla från IP-adresser än adressen för App Service-miljö.

En Apptjänst-miljö måste inte bara har externa beroenden, men även lyssna efter inkommande trafik och svara på sådan trafik. Svaren kan inte skickas tillbaka från en annan adress eftersom som bryter TCP. Det finns tre steg som krävs för att ändra utgång slutpunkten för Apptjänst-miljön:

1. Ange en routningstabell så att inkommande hantering av trafik kan gå tillbaka från samma IP-adress.

2. Lägg till IP-adresser som ska användas för utgående trafik till Apptjänstmiljö-brandväggen.

3. Ange vägarna för utgående trafik från Apptjänst-miljön till att tunneldata.

   ![Framtvingad tunnel nätverket flöde][1]

Du kan konfigurera Apptjänst-miljön med olika utgående adresser efter Apptjänst-miljön är redan och kan användas och de kan anges under distributionen av Apptjänst-miljö.

### <a name="change-the-egress-address-after-the-app-service-environment-is-operational"></a>Ändra adress för utgående trafik när Apptjänst-miljön är i drift ###
1. Hämta IP-adresser som du vill använda som utgång IP-adresser för din Apptjänst-miljö. Om du gör Tvingad tunneling, kommer dessa adresser från NAT- eller gateway IP-adresser. Om du vill dirigera Apptjänstmiljö utgående trafik via en NVA är utgång adressen en NVA offentlig IP.

2. Ange adresser för utgående trafik i dina Apptjänst-miljö konfigurationsinformation. Gå till resource.azure.com och gå till prenumerationen /<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>. Du kan se JSON som beskriver din Apptjänst-miljö. Kontrollera att det står **läsning och skrivning** längst upp. Välj **redigera**. Rulla ned och ändra den **userWhitelistedIpRanges** värde från **null** till något som liknar följande. Använda adresser som du vill ange som utgång adressintervallet. 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   Välj **PLACERA** längst upp. Det här alternativet utlöser en skalningsåtgärden på din Apptjänst-miljö och justerar brandväggen.
 
3. Skapa eller redigera en routingtabell och fylla i regler för att tillåta åtkomst till eller från management-adresser som motsvarar din Apptjänstmiljö plats. Management-adresser finns [Apptjänstmiljö management adresser][management].

4. Justera vägar tillämpad på undernätet för Apptjänst-miljön med en routningstabell eller BGP-vägar. 

Om Apptjänst-miljön slutar svara från portalen, finns det ett problem med dina ändringar. Problemet kan vara att din lista över utgående adresser var ofullständig trafiken gick förlorad eller trafiken har blockerats. 

### <a name="create-a-new-app-service-environment-with-a-different-egress-address"></a>Skapa en ny Apptjänstmiljö med en annan utgång-adress ###

Om det virtuella nätverket har redan konfigurerats för att framtvinga tunnel all trafik, måste du vidta ytterligare åtgärder för att skapa din Apptjänst-miljö så att den kan starta korrekt. Du måste aktivera användningen av en annan utgång slutpunkt under skapande av Apptjänst-miljö. Om du vill göra detta måste du skapa Apptjänst-miljön med en mall som anger de tillåtna utgående adresserna.

1. Hämta IP-adresser som ska användas som utgående adresser för din Apptjänst-miljö.

2. Skapa undernätet som ska användas av Apptjänst-miljön. Du behöver den så att du kan konfigurera vägar, och även eftersom mallen kräver.

3. Skapa en routingtabell med hantering av IP-adresser som mappar till din Apptjänst-miljö plats. Tilldela den till din Apptjänst-miljö.

4. Följ anvisningarna i [skapa en Apptjänst-miljö med en mall][template]. Hämta lämplig mall.

5. Redigera avsnittet ”resurser” i filen azuredeploy.json. Innehåller en rad för **userWhitelistedIpRanges** med värdena så här:

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

Om det här avsnittet är korrekt konfigurerat ska Apptjänst-miljön startas utan problem. 


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
