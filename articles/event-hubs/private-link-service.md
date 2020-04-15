---
title: Integrera Azure-händelsehubbar med Azure Private Link Service
description: Lär dig hur du integrerar Azure Event Hubs med Azure Private Link Service
services: event-hubs
author: spelluru
ms.author: spelluru
ms.date: 03/12/2020
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: bcc360bbe4dd58200993b9377317ccb608b3529d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383652"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link-preview"></a>Integrera Azure-händelsehubbar med Azure Private Link (förhandsversion)
Azure Private Link Service kan du komma åt Azure Services (till exempel Azure Event Hubs, Azure Storage och Azure Cosmos DB) och Azure värd kund / partner tjänster via en **privat slutpunkt** i ditt virtuella nätverk.

En privat slutpunkt är ett nätverksgränssnitt som ansluter dig privat och säkert till en tjänst som drivs av Azure Private Link. Den privata slutpunkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt för in tjänsten i ditt virtuella nätverk. All trafik till tjänsten kan dirigeras via den privata slutpunkten, så inga gateways, NAT-enheter, ExpressRoute- eller VPN-anslutningar eller offentliga IP-adresser behövs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta detaljnivån i åtkomstkontrollen.

Mer information finns i [Vad är Azure Private Link?](../private-link/private-link-overview.md)

> [!NOTE]
> Den här funktionen stöds endast med den **dedikerade** nivån. Mer information om den dedikerade nivån finns i [Översikt över dedikerade eventhubbar](event-hubs-dedicated-overview.md). 
>
> Den här funktionen är för närvarande i **förhandsgranskning**. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Lägga till en privat slutpunkt med Azure-portalen

### <a name="prerequisites"></a>Krav

Om du vill integrera ett namnområde för eventhubbar med Azure Private Link behöver du följande entiteter eller behörigheter:

- Ett namnområde för händelsehubbar.
- Ett virtuellt Azure-nätverk.
- Ett undernät i det virtuella nätverket.
- Ägare- eller deltagarbehörigheter för både namnområdet och det virtuella nätverket.

Din privata slutpunkt och virtuella nätverk måste finnas i samma region. När du väljer en region för den privata slutpunkten med hjälp av portalen filtreras endast virtuella nätverk som finns i den regionen automatiskt. Namnområdet kan finnas i en annan region.

Din privata slutpunkt använder en privat IP-adress i det virtuella nätverket.

### <a name="steps"></a>Steg
Om du redan har ett namnområde för händelsehubbar kan du skapa en privat länkanslutning genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Skriv in **händelsehubbar**i sökfältet .
3. Markera **namnområdet** i listan där du vill lägga till en privat slutpunkt.
4. Välj fliken **Nätverk** under **Inställningar**.
5. Välj fliken **Privata slutpunktsanslutningar (förhandsgranskning)** högst upp på sidan. Om du inte använder en dedikerad nivå av händelsehubbar visas ett meddelande: **Privata slutpunktsanslutningar på eventhubbar stöds bara av namnområden som skapats under ett dedikerat kluster**.
6. Välj knappen **+ Privat slutpunkt** högst upp på sidan.

    ![Bild](./media/private-link-service/private-link-service-3.png)
7. Gör så här på sidan **Grunderna:** 
    1. Välj den **Azure-prenumeration** där du vill skapa den privata slutpunkten. 
    2. Välj **resursgruppen** för den privata slutpunktsresursen.
    3. Ange ett **namn** för den privata slutpunkten. 
    5. Välj en **region** för den privata slutpunkten. Din privata slutpunkt måste finnas i samma region som det virtuella nätverket, men kan finnas i en annan region tillbaka till den privata länkresurs som du ansluter till. 
    6. Välj **Nästa: Knappen Resurs >** längst ned på sidan.

        ![Skapa privat slutpunkt - sidan Grunderna](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Gör så här på sidan **Resurs:**
    1. Gör så här om du väljer **Anslut till en Azure-resurs i min katalog:** 
        1. Välj den **Azure-prenumeration** där **namnområdet Event Hubs** finns. 
        2. För **resurstyp**väljer du **Microsoft.EventHub/namespaces** för **resurstypen**.
        3. För **Resurs**väljer du ett namnområde för händelsehubbar i listrutan. 
        4. Bekräfta att **underresursen Mål** är inställd på **namnområde**.
        5. Välj **Nästa: Knappen Konfiguration >** längst ned på sidan. 
        
            ![Skapa privat slutpunkt - resurssida](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Om du väljer **Anslut till en Azure-resurs efter resurs-ID eller alias**gör du så här:
        1. Ange **resurs-ID** eller **alias**. Det kan vara det resurs-ID eller alias som vissa har delat med dig.
        2. För **Underresurs för mål**anger du **namnområde**. Det är den typ av underresurs som din privata slutpunkt kan komma åt.
        3. (valfritt) Ange ett **meddelande om begäran**. Resursägaren ser det här meddelandet när du hanterar privat slutpunktsanslutning.
        4. Välj sedan **Nästa: Knappen Konfiguration >** längst ned på sidan.

            ![Skapa privat slutpunkt - Anslut med hjälp av resurs-ID](./media/private-link-service/connect-resource-id.png)
9. På sidan **Konfiguration** väljer du undernätet i ett virtuellt nätverk dit du vill distribuera den privata slutpunkten. 
    1. Välj ett **virtuellt nätverk**. Endast virtuella nätverk i den valda prenumerationen och platsen visas i listrutan. 
    2. Markera ett **undernät** i det virtuella nätverk som du har valt. 
    3. Välj **Nästa: Taggar >** knappen längst ned på sidan. 

        ![Skapa privat slutpunkt - konfigurationssida](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. På sidan **Taggar** skapar du alla taggar (namn och värden) som du vill associera med den privata slutpunktsresursen. Välj sedan **knappen Granska + skapa** längst ned på sidan. 
11. Granska alla inställningar i **granskning + skapa**och välj **Skapa** för att skapa den privata slutpunkten.
    
    ![Skapa privat slutpunkt - sidan Granska och skapa](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Bekräfta att den privata slutpunktsanslutningen som du skapade visas i listan över slutpunkter. I det här exemplet godkänns den privata slutpunkten automatiskt eftersom du är ansluten till en Azure-resurs i katalogen och du har tillräcklig behörighet. 

    ![Privat slutpunkt skapad](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Lägga till en privat slutpunkt med PowerShell
I följande exempel visas hur du använder Azure PowerShell för att skapa en privat slutpunktsanslutning. Det skapar inte ett dedikerat kluster för dig. Följ stegen i [den här artikeln](event-hubs-dedicated-cluster-create-portal.md) för att skapa ett dedikerat eventhubbarkluster. 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>Konfigurera den privata DNS-zonen
Skapa en privat DNS-zon för eventhubbar-domän och skapa en associationslänk med det virtuella nätverket:

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>Hantera privata slutpunkter med Azure-portalen

När du skapar en privat slutpunkt måste anslutningen godkännas. Om resursen som du skapar en privat slutpunkt för finns i katalogen kan du godkänna anslutningsbegäran förutsatt att du har tillräcklig behörighet. Om du ansluter till en Azure-resurs i en annan katalog måste du vänta på att resursägaren godkänner din anslutningsbegäran.

Det finns fyra etableringstillstånd:

| Åtgärd för tjänst | Privat slutpunktstillstånd för tjänstkonsument | Beskrivning |
|--|--|--|
| Inget | Väntande åtgärder | Anslutningen skapas manuellt och väntar på godkännande från private link-resursägaren. |
| Godkänn | Godkända | Anslutningen godkändes automatiskt eller manuellt och är klar att användas. |
| Avvisa | Avvisad | Anslutningen avvisades av ägaren till den privata länkresursen. |
| Ta bort | Frånkopplad | Anslutningen togs bort av ägaren till den privata länkresursen, den privata slutpunkten blir informativ och bör tas bort för rensning. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Godkänna, avvisa eller ta bort en privat slutpunktsanslutning

1. Logga in på Azure Portal.
2. Skriv in **händelsehubbar**i sökfältet .
3. Markera det **namnområde** som du vill hantera.
4. Välj fliken **Nätverk.**
5. Gå till lämpligt avsnitt nedan baserat på den åtgärd du vill: godkänna, avvisa eller ta bort.

### <a name="approve-a-private-endpoint-connection"></a>Godkänna en privat slutpunktsanslutning
1. Om det finns några anslutningar som väntar visas en anslutning som visas med **Väntande** i etableringstillståndet. 
2. Välj den **privata slutpunkt** som du vill godkänna
3. Välj knappen **Godkänn.**

    ![Bild](./media/private-link-service/approve-private-endpoint.png)
4. Lägg till en kommentar (valfritt) på sidan **Godkänn anslutning** och välj **Ja**. Om du väljer **Nej**händer ingenting. 
5. Du bör se status för den privata slutpunktsanslutningen i listan **ändras**till Godkänd . 

### <a name="reject-a-private-endpoint-connection"></a>Avvisa en privat slutpunktsanslutning

1. Om det finns några privata slutpunktsanslutningar som du vill avvisa, oavsett om det är en väntande begäran eller befintlig anslutning, markerar du anslutningen och klickar på knappen **Avvisa.**

    ![Bild](./media/private-link-service/private-endpoint-reject-button.png)
2. På sidan **Avvisa anslutning** anger du en kommentar (valfritt) och väljer **Ja**. Om du väljer **Nej**händer ingenting. 
3. Du bör se status för den privata slutpunktsanslutningen i listan ändras till **Avvisad**. 

### <a name="remove-a-private-endpoint-connection"></a>Ta bort en privat slutpunktsanslutning

1. Om du vill ta bort en privat slutpunktsanslutning markerar du den i listan och väljer **Ta bort** i verktygsfältet.
2. På sidan **Ta bort anslutning** väljer du **Ja** för att bekräfta borttagningen av den privata slutpunkten. Om du väljer **Nej**händer ingenting.
3. Du bör se statusen ändras till **Frånkopplad**. Sedan ser du slutpunkten försvinna från listan.

## <a name="validate-that-the-private-link-connection-works"></a>Verifiera att den privata länkanslutningen fungerar

Du bör verifiera att resurserna i samma undernät till den privata slutpunktsresursen ansluter till namnområdet Event Hubs via en privat IP-adress och att de har rätt integrering av privata DNS-zoner.

Skapa först en virtuell dator genom att följa stegen i [Skapa en virtuell Windows-dator i Azure-portalen](../virtual-machines/windows/quick-create-portal.md)

På fliken **Nätverk:**

1. Ange **virtuellt nätverk** och **undernät**. Du kan skapa ett nytt virtuellt nätverk eller välja ett befintligt nätverk. Om du väljer en befintlig kontrollerar du att regionen matchar.
1. Ange en **offentlig IP-resurs.**
1. Välj **Ingen**i **säkerhetsgruppen för nätverkskort**.
1. Välj **Nej**i **belastningsutjämningen**.

Öppna kommandoraden och kör följande kommando:

```console
nslookup <your-event-hubs-namespace-name>.servicebus.windows.net
```

Om du kör kommandot ns lookup för att matcha IP-adressen för ett namnområde för händelsehubbar över en offentlig slutpunkt visas ett resultat som ser ut så här:

```console
c:\ >nslookup <your-event-hubs-namespae-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-event-hubs-namespace-name>.servicebus.windows.net
```

Om du kör kommandot ns lookup för att matcha IP-adressen för ett namnområde för händelsehubbar över en privat slutpunkt visas ett resultat som ser ut så här:

```console
c:\ >nslookup your_event-hubs-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-event-hub-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Begränsningar och designöverväganden

**Priser**: För prisinformation finns i [Azure Private Link-priser](https://azure.microsoft.com/pricing/details/private-link/).

**Begränsningar**: Private Endpoint for Azure Event Hubs är i offentlig förhandsversion. Den här funktionen är tillgänglig i alla offentliga Azure-regioner.

**Maximalt antal privata slutpunkter per händelsehubbarnamnområde:** 120.

Mer information finns i [Azure Private Link-tjänsten: Begränsningar](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Private Link](../private-link/private-link-service-overview.md)
- Läs mer om [Azure Event Hubs](event-hubs-about.md)
