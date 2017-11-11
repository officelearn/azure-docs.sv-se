---
title: "Konfigurera din Azure Apptjänst-miljö för att framtvinga tunneldata"
description: "Aktivera din ASE ska fungera när utgående trafik är kraft tunneldata"
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
ms.openlocfilehash: cd89bd23074dec1de6fa0e8784d42a5c539938b1
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Konfigurera din Apptjänst-miljö med Tvingad tunneltrafik

App Service miljö (ASE) är en distribution av Azure App Service i kundens Azure Virtual Network (VNet). Många kunder konfigurera sina Vnet som tillägg i sina lokala nätverk med VPN eller ExpressRoute anslutningar. På grund av företagets principer eller andra villkor för säkerhet konfigurera de vägar för att skicka alla utgående trafik lokala innan det kan gå till internet. Ändra routning av VNet så att utgående trafik från VNet flödar via VPN eller ExpressRoute-anslutning till lokalt kallas Tvingad tunneling.  

Tvingad tunneling kan orsaka problem för en ASE. ASE har ett antal externa beroenden, som räknas upp i det här [ASE nätverksarkitektur] [ network] dokumentet. ASE, kräver som standard att alla utgående kommunikation går igenom VIP som har etablerats med ASE.

Flöden är en viktig aspekt vad Tvingad tunneltrafik är och hur du hanterar med den. I ett Azure Virtual Network sköts routningen baserat på längsta Prefix-matchning (LPM).  Om mer än en väg med samma LPM-matchning väljs en väg baserat på dess ursprung i följande ordning:

1. Användardefinierad väg
1. BGP-väg (när ExpressRoute används)
1. Systemväg

Mer information om routning i ett VNet [användardefinierade vägar och IP-vidarebefordring][routes]. 

Om du vill att din ASE ska fungera i en framtvingad tunnel VNet har du två alternativ:

1. Aktivera din ASE ha direkt tillgång till internet
1. Ändra utgång slutpunkten för din ASE

## <a name="enable-your-ase-to-have-direct-internet-access"></a>Aktivera din ASE ha direkt tillgång till internet

För din ASE arbeta medan ditt virtuella nätverk har konfigurerats med en ExpressRoute kan du:

* Konfigurera ExpressRoute att annonsera 0.0.0.0/0. Som standard den tvinga tunnlar alla utgående trafik lokalt.
* Skapa en UDR. Tillämpa den på det undernät som innehåller ASE med ett adressprefix 0.0.0.0/0 och en nästa hopptyp över Internet.

Om du ändrar de här två, är inte avsedda internet trafik från undernätet ASE expressroute har skapats och ASE tvingas fungerar.

> [!IMPORTANT]
> De vägar som definierats i en UDR måste vara specifikt nog att företräde framför alla vägarna som annonseras av ExpressRoute-konfigurationen. Föregående exempel använder adressintervallet bred 0.0.0.0/0. Den kan potentiellt av misstag åsidosättas av flödet annonser som används mer specifika adressintervall.
>
> ASEs stöds inte med ExpressRoute-konfigurationer som cross-annonserar vägar från offentlig peering-sökvägen till privat peering-sökväg. ExpressRoute-konfigurationer med offentlig peering konfigurerats få vägannonser från Microsoft. Annonserna innehåller ett stort antal Microsoft Azure IP-adressintervall. Om adressintervallen mellan annonseras i privat peering-sökvägen, är alla utgående paket från den ASE undernät kraft tunneldata kundens lokala nätverkets infrastruktur. Det här flödet i nätverk stöds för närvarande inte som standard med ASEs. En lösning på problemet är att stoppa mellan reklam vägar från offentlig peering-sökvägen till privat peering-sökväg.  Andra lösningen är att aktivera din ASE ska fungera i en konfiguration framtvingad tunnel.

## <a name="change-the-egress-endpoint-for-your-ase"></a>Ändra utgång slutpunkten för din ASE ##

Det här avsnittet beskriver hur du aktiverar en ASE ska fungera i en konfiguration framtvingad tunnel genom att ändra utgång-slutpunkt som används av ASE. Om utgående trafik från ASE tvingas tunneldata till ett lokalt nätverk, måste du tillåta att trafik till datakälla från IP-adresser än ASE VIP-adressen.

En ASE inte bara har externa beroenden, men den också måste lyssna efter inkommande trafik att hantera ASE. ASE måste kunna svara på sådan trafik och svaren kan inte skickas tillbaka från en annan adress som som bryter TCP.  Det finns därför tre steg som krävs för att ändra slutpunkten utgång för ASE.

1. Ange en routningstabell så att inkommande hantering av trafik kan gå tillbaka från samma IP-adress
1. Lägg till IP-adresser som ska användas för utgående trafik till ASE-brandväggen
1. Ange vägarna för utgående trafik från ASE att vara tunneldata

![Framtvingad tunnel nätverket flöde][1]

Du kan konfigurera ASE med olika utgående adresser när ASE är redan och kan användas eller de kan anges under distributionen av ASE.  

### <a name="changing-the-egress-address-after-the-ase-is-operational"></a>Ändra adress för utgående trafik när ASE fungerar ###
1. Hämta IP-adresser som du vill använda som utgång IP-adresser för dina ASE. Om du gör Tvingad tunneltrafik detta skulle vara NAT- eller gateway IP-adresser.  Om du vill dirigera ASE utgående trafik via en NVA skulle utgång adressen vara en NVA offentlig IP.
2. Ange utgående adresser i ditt ASE konfigurationsinformation. Gå till resource.azure.com och gå till: prenumeration /<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name> visas json som beskriver ditt ASE.  Kontrollera att det står läsning och skrivning på överst.  Klicka på Redigera Rulla längst ned och ändra userWhitelistedIpRanges från  

       "userWhitelistedIpRanges": null 
      
  till något som liknar följande. Använda adresser som du vill ange som utgång adressintervallet. 

      ”userWhitelistedIpRanges”: [”11.22.33.44/32”, ”55.66.77.0/24”] 

  Klicka på PLACERA längst upp. Detta utlöser en skalningsåtgärden på din ASE och justera brandväggen.
   
3. Skapa eller redigera en routingtabell och fylla i regler för att tillåta åtkomst till eller från management-adresser som motsvarar din ASE plats.  Management-adresser är här, [Apptjänstmiljö management-adresser][management] 

4. Justera vägar som tillämpas på ASE undernät med en routningstabell eller BGP-vägar.  

Om ASE slutar svara från portalen är ett problem med dina ändringar.  Det kan vara att din lista över utgående adresser var ofullständig trafiken gick förlorad eller trafiken har blockerats.  

### <a name="create-a-new-ase-with-a-different-egress-address"></a>Skapa en ny ASE med en annan utgång adress  ###

I händelse av att ditt VNet har redan konfigurerats för att framtvinga tunnel all trafik, behöver du vidta ytterligare åtgärder för att skapa din ASE så att den kan starta korrekt. Det innebär att du måste aktivera användningen av en annan utgång slutpunkt när ASE skapas.  Om du vill göra detta måste du skapa ASE med en mall som anger de tillåtna utgående adresserna.

1. Hämta IP-adresser som ska användas som utgående adresser för dina ASE.
1. Skapa undernätet som ska användas av ASE. Detta krävs för att du kan ange vägar och även eftersom mallen kräver.  
1. Skapa en routingtabell med hantering av IP-adresser som mappas till ASE plats och tilldela den till din ASE
1. Följ anvisningarna här, [skapar en ASE med en mall][template], och hämtar lämplig mall
1. Redigera avsnittet azuredeploy.json ”resurser”. Innehåller en rad för **userWhitelistedIpRanges** med värdena som:

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

Om detta konfigureras korrekt ska ASE startas utan problem.  


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
