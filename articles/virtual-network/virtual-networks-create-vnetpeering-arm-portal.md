---
title: Skapa VNet-peering med hjälp av Azure-portalen | Microsoft Docs
description: Lär dig hur du skapar ett virtuellt nätverk med Azure-portalen i Resource Manager.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayanannamalai;annahar

---
# Skapa VNet-peering med hjälp av Azure-portalen
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Följ stegen nedan för att skapa VNet-peering baserat på scenariot ovan med hjälp av Azure-portalen.

1. Från en webbläsare, navigerar du till http://portal.azure.com och loggar, vid behov, in med ditt Azure-konto.
2. För att etablera VNET-peering måste du skapa två länkar, en för varje riktning, mellan två virtuella nätverk (VNet). Du kan skapa en länk för VNET-peering för VNET1 till VNET2 först. På portalen klickar du på **Bläddra** > **välj Virtual Networks**
   
    ![Skapa VNet-peering på Azure-portalen](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)
3. På bladet Virtual Networks väljer du VNET1, klickar på Peering-sessioner och sedan på Lägg till.
   
    ![Välj Peering.](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)
4. På bladet Lägg till peer-koppling ger du peering-länken namnet LinkToVnet2, väljer prenumerationen och det virtuella peer-nätverket VNET2 och klickar på OK.
   
    ![Länka till VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)
5. När länken för VNET-peering har skapats kan du se länkens status så här:
   
    ![Länkens status](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)
6. Nu ska du skapa länken för VNET2-peering för VNET2 till VNET1. På bladet Virtual Networks väljer du VNET2, klickar på Peering-sessioner och sedan på Lägg till.
   
    ![Peer från ett annat VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)
7. På bladet Lägg till peer-koppling ger du peering-länken namnet LinkToVnet1, väljer prenumerationen och det virtuella peer-nätverket och klickar på OK.
   
    ![Skapa en virtuell nätverksikon](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)
8. När länken för VNET-peering har skapats kan du se länkens status så här:
   
    ![Slutlig länkstatus](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)
9. Kontrollera statusen för LinkToVnet2 och se att den ändras till Ansluten.  
   
    ![Slutlig länkstatus 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)
   
   > [!NOTE]
   > VNET-peering upprättas endast om båda länkarna är anslutna.
   > 
   > 

Det finns några egenskaper som kan konfigureras för varje länk:

| Alternativ | Beskrivning | Standard |
|:--- |:--- |:--- |
| AllowVirtualNetworkAccess |Om adressrymden för peer-VNet som ska tas med som en del av taggen Virtual_network |Ja |
| AllowForwardedTraffic |Tillåter att trafik som inte kommer från ett peer-kopplat VNet godkänns eller utelämnas |Nej |
| AllowGatewayTransit |Tillåter att ett peer VNet använder din VNet-gateway |Nej |
| UseRemoteGateways |Använd din peers VNet-gateway. En gateway måste konfigureras för det peer-kopplade virtuella nätverket och AllowGatewayTransit måste väljas. Du kan inte använda det här alternativet om du har en konfigurerad gateway |Nej |

Varje länk i VNet-peering har en uppsättning av egenskaperna ovan. Från portalen kan du klicka på länken för VNet-peering, ändra de tillgängliga alternativen och klicka på Spara för att ändra effekten.

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. Från en webbläsare, navigerar du till http://portal.azure.com och loggar, vid behov, in med ditt Azure-konto.
2. I det här exemplet ska vi använda två prenumerationer, A och B, och två användare, UserA och UserB, med behörigheter i respektive prenumeration.
3. Klicka på Bläddra på portalen och välj Virtual Networks. Klicka på VNET och sedan på Lägg till.
   
    ![Scenario 2: Bläddra](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)
4. Klicka på Välj en roll på bladet Lägg till åtkomst och välj Nätverksdeltagare, klicka på Lägg till användare, skriv inloggningsnamnet för UserB och klicka på OK.
   
    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)
   
    Detta är inget krav, peering kan upprättas även om användarna skickar peering-begäranden individuellt för sina respektive virtuella nätverk, så länge begärandena matchar. Om du lägger till det andra virtuella nätverkets privilegierade användare som användare i det lokala virtuella nätverket blir det lättare att konfigurera inställningar på portalen.
5. Logga sedan in på Azure-portalen med UserB som är den privilegierade användaren för SubscriptionB. Lägg till UserA som nätverksdeltagare genom att följa stegen ovan.
   
    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)
   
   > [!NOTE]
   > Du kan logga ut och logga in båda användarsessionerna i webbläsaren för att kontrollera att auktoriseringen har aktiverats.
   > 
   > 
6. Logga in på portalen som UserA, gå till bladet VNET3, klicka på Peering, markera kryssrutan ”Jag känner till mitt resurs-ID” och skriv resurs-ID:t för VNET5 i formatet nedan.
   
    /subscriptions/<prenumerations-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/<VNET name>
   
    ![Resurs-ID](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)
7. Logga in på portalen som UserB och följ steget ovan för att skapa en peering-länk från VNET5 till VNet3.
   
    ![Resurs-ID 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)
8. Peer-kopplingen upprättas och virtuella datorer i VNet3 ska nu kunna kommunicera med alla virtuella datorer i VNet5

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Som ett första steg länkar VNET-peering från HubVnet till VNET1. Observera att alternativet Tillåt vidarebefordrad trafik inte är valt för länken.
   
    ![Enkel peering](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)
2. Som nästa steg kan du skapa peering-länkar från VNET1 till HubVnet. Observera att alternativet Tillåt vidarebefordrad trafik är valt.
   
    ![Enkel peering](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)
3. När peer-kopplingen har upprättats kan du läsa den här [artikeln](virtual-network-create-udr-arm-ps.md) och definiera en användardefinierad väg (UDR) som omdirigerar VNet1-trafik via en virtuell installation för att använda dess funktioner. När du anger ”nästa hopp”-adressen i vägen kan du ange den till IP-adressen för en virtuell enhet i det virtuella peer-nätverket för HubVNet.

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. Från en webbläsare, navigerar du till http://portal.azure.com och loggar, vid behov, in med ditt Azure-konto.
2. För att etablera VNET-peering i detta scenario måste du endast skapa en länk från det virtuella nätverket i Azure Resource Manager till den i classic. Det vill säga från **VNET1** till **VNET2**. På portalen klickar du på **Bläddra** > välj **Virtuella nätverk**
3. I Virtuella nätverksbladet väljer du **VNET1**. Klicka på **Peerings** och klicka därefter på **Lägg till**.
4. I bladet Lägg till peering ger du din länk ett namn. Här kallas den **LinkToVNet2**. Under Peer-information väljer du **Classic**.
5. Välj därefter prenumeration och peer virtuellt nätverk **VNET2**. Klicka sedan på OK.
   
    ![Länka Vnet1 till Vnet2](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)
6. När du har skapat denna VNet-peeringlänk är de två virtuella nätverken är peer-kopplade och du kommer att kunna se följande:
   
    ![Kontrollera peering-anslutning](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)

## Ta bort VNet-peering
1. Från en webbläsare, navigerar du till http://portal.azure.com och loggar, vid behov, in med ditt Azure-konto.
2. Gå till bladet för virtuella nätverk, klicka på Peering-sessioner, klicka på den länk du vill ta bort och sedan på knappen Ta bort.
   
   ![Delete1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)
3. När du tar bort en länk i VNET-peering ändras peer-länkens status till frånkopplad.
   
    ![Delete2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)
4. I det här tillståndet kan du inte återskapa länken förrän peer-länkens status ändras till Initierad. Vi rekommenderar att du tar bort båda länkarna innan du återskapar en VNET-peering.

<!--HONumber=Sep16_HO3-->


