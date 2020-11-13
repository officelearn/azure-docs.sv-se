---
title: Distribuera Traffic Manager för att balansera arbets belastningar i Azure VMware-lösningen (AVS)
description: Lär dig hur du integrerar Traffic Manager med Azure VMware-lösningen (AVS) för att balansera program arbets belastningar över flera slut punkter i olika regioner.
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: 076d9c77d68df3d8acb7b531b3dfbea40fb3cedd
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593144"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-avs-workloads"></a>Distribuera Traffic Manager för att balansera arbets belastningar i Azure VMware-lösningen (AVS)

Den här artikeln vägleder dig genom integrering av Traffic Manager med Azure VMware-lösning (AVS) för att balansera program arbets belastningar över flera slut punkter. Vi ska titta på ett scenario där Traffic Manager dirigerar trafik mellan tre programgatewayer som sträcker sig över flera AVS-regioner: västra USA, Västeuropa och lokalt i USA, östra. 

Azure Traffic Manager är en DNS-baserad trafikbelastnings utjämning som gör att du kan distribuera trafik optimalt till tjänster i globala Azure-regioner. Det kommer att belastningsutjämna program trafik mellan både Azure-arbetsbelastningar och externa offentliga slut punkter. Mer information om Traffic Manager finns i [Vad är Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

Granska [kraven](#prerequisites) först. sedan kommer vi att gå igenom procedurerna för att:

> [!div class="checklist"]
> * Verifiera konfigurationen av dina programgatewayer
> * Verifiera konfigurationen av NSX-T-segmentet
> * Skapa din Traffic Manager-profil
> * Lägg till externa slut punkter i din Traffic Manager-profil

## <a name="topology"></a>Topologi

Som du ser i följande bild tillhandahåller Azure Traffic Manager belastnings utjämning för program på DNS-nivå mellan regionala slut punkter. Programgatewayerna har medlemmar konfigurerade i Server delen som IIS-servrar och refereras till som externa AVS-slutpunkter.

Anslutning över det virtuella nätverket mellan de två offentliga moln regionerna i molnet, västra USA och Västeuropa, och en lokal server i östra USA använder en ExpressRoute-Gateway.   

![Diagram över arkitekturen i Traffic Manager-integrering med Azure VMware-lösning](media/traffic-manager/traffic-manager-topology.png)
 
## <a name="prerequisites"></a>Förutsättningar

- Tre virtuella datorer som har kon figurer ATS som Microsoft IIS-servrar som körs i olika AVS-regioner: västra USA, Västeuropa och lokalt. 

- En Programgateway med externa slut punkter i västra USA, västra Europa och lokalt.

- Värd med Internet anslutning för verifiering. 

## <a name="verify-configuration-of-your-application-gateways"></a>Verifiera konfigurationen av dina programgatewayer

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) är en belastningsutjämnare för Layer 7-webbtrafik som gör att du kan hantera trafik till dina webb program. Mer information om Application Gateway finns i [Vad är Azure Application Gateway?](../application-gateway/overview.md) 

I det här scenariot konfigureras tre Application Gateway-instanser som externa AVS-slutpunkter. Programgatewayerna har AVS-virtuella datorer konfigurerade som medlemmar i Server delen för att belastningsutjämna inkommande Layer 7-begäranden. (Information om hur du konfigurerar Application Gateway med virtuella datorer i AVS-form som backend-pooler finns i [använda Azure Application Gateway för att skydda dina webbappar på Azure VMware-lösningen](protect-azure-vmware-solution-with-application-gateway.md).)  

Följande steg kontrollerar korrekt konfiguration av dina programgatewayer.

1. Öppna Azure Portal och välj **programgatewayer** för att visa en lista över dina aktuella programgatewayer. 

    I det här scenariot har vi konfigurerat tre programgatewayer:
    - AVS-GW-WUS
    - AVS-GW-EUS (lokalt)
    - AVS-GW-WEU

    :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Skärm bild av sidan Application Gateway som visar en lista över konfigurerade programgatewayer." lightbox="media/traffic-manager/app-gateways-list-1.png":::

2. Välj en av dina tidigare distribuerade programgatewayer. Ett fönster med information om programgatewayen visas. Välj **backend-pooler** för att verifiera konfigurationen av en av backend-poolerna.

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Skärm bild av sidan Application Gateway som visar information om den valda programgatewayen." lightbox="media/traffic-manager/backend-pool-config.png":::
 
3. I det här fallet ser vi en virtuell dators Server dels medlemmar som kon figurer ATS som en webb server med en IP-adress för 172.29.1.10.
 
    :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Skärm bild av sidan Redigera server dels bassäng med mål-IP-adress markerad.":::

    Du kan på samma sätt verifiera konfigurationen av de andra programgatewayerna och medlemmar i Server delens pooler. 

## <a name="verify-configuration-of-the-nsx-t-segment"></a>Verifiera konfigurationen av NSX-T-segmentet

Nätverks segment som skapats i NSX-T-hanteraren används som nätverk för virtuella datorer i vCenter. Mer information finns i självstudien [skapa ett NSX-T-nätverks segment i Azure VMware-lösningen (AVS)](tutorial-nsx-t-network-segment.md).

I vårt scenario konfigureras ett NSX-T-segment i den AVS-miljö där den virtuella datorn för Server delens medlemmar är ansluten.

1. Välj **segment** om du vill visa dina konfigurerade segment. I det här fallet ser vi att contoso-SEGMENT1 är anslutet till contoso-T01 Gateway, en flexibel router på nivå 1.

    :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Skärm bild som visar segment profiler i NSX-T-hanteraren.":::    

2. Välj **nivå 1-gatewayer** för att se en lista över dina nivå 1-gatewayer med antalet länkade segment. Välj det segment som är kopplat till contoso-T01. Ett fönster öppnas som visar det logiska gränssnitt som kon figurer ATS på nivån-01-routern. Detta fungerar som en gateway till den virtuella datorns medlem i Server delen som är ansluten till segmentet.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Skärm bild som visar gateway-adressen för det valda segmentet.":::    

3. I VM vSphere-klienten väljer du den virtuella datorn för att visa information om den. Observera att IP-adressen matchar vad vi såg i steg 3 i föregående avsnitt: 172.29.1.10.

    :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Skärm bild som visar information om virtuella datorer i VSphere-klienten.":::    

4. Välj den virtuella datorn och klicka sedan på **åtgärder > redigera inställningar** för att kontrol lera anslutningen till NSX-T-segmentet.

## <a name="create-your-traffic-manager-profile"></a>Skapa din Traffic Manager-profil

1. Logga in på [Azure Portal](https://rc.portal.azure.com/#home). Under **Azure-tjänster > nätverk** väljer du **Traffic Manager profiler**.

2. Välj **+ Lägg** till för att skapa en ny Traffic Manager-profil.
 
3. Ange profil namn, routningsmetod (routningsmetod i det här scenariot kommer vi att använda viktat i det här scenariot. se [Traffic Manager routningsmetoder](../traffic-manager/traffic-manager-routing-methods.md)), prenumeration och resurs grupp och välj **skapa**.

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Lägg till externa slut punkter i Traffic Manager profilen

1. Välj Traffic Manager profil i rutan Sök resultat, Välj **slut punkter** och sedan **+ Lägg till**.

2. Ange nödvändig information: typ, namn, fullständigt kvalificerat domän namn (FQDN) eller IP och vikt (i det här scenariot tilldelar vi en vikt på 1 till varje slut punkt). Välj **Lägg till**. Detta skapar den externa slut punkten. Övervaknings statusen måste vara **online**. Upprepa samma steg för att skapa två fler externa slut punkter, en i en annan region och andra lokala. När det har skapats visas alla tre i Traffic Manager profilen och status för alla tre ska vara **online**.

3. Välj **Översikt**. Kopiera webb adressen under **DNS-namn**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Skärm bild som visar en översikt över en Traffic Manager-slutpunkt med DNS-namn markerat."::: 

4. Klistra in DNS-namn-URL: en i en webbläsare. Följande skärm bild visar trafik som leder till regionen Europa, västra.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Skärm bild av webbläsarfönstret som visar trafik som dirigeras till Västeuropa."::: 

5. Uppdatera webbläsaren. Följande skärm bild visar trafik som nu dirigeras till en annan uppsättning medlemmar i Server delen i regionen USA, västra.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Skärm bild av webbläsarfönstret som visar trafik som skickas till västra USA."::: 

6. Uppdatera webbläsaren igen. Följande skärm bild visar trafik som nu dirigeras till den slutliga uppsättningen medlemmar i backend-poolen lokalt.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Skärm bild av webbläsarfönstret med trafik dirigerad till lokalt.":::

## <a name="next-steps"></a>Nästa steg

Läs mer om:

- [Använda Azure Application Gateway på Azure VMware-lösning (AVS)](protect-azure-vmware-solution-with-application-gateway.md)
- [Traffic Manager-dirigeringsmetoder](../traffic-manager/traffic-manager-routing-methods.md)
- [Kombinera tjänster för belastnings utjämning i Azure](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Mäta Traffic Manager prestanda](../traffic-manager/traffic-manager-performance-considerations.md)
