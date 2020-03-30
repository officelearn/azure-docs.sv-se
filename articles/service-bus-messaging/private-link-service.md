---
title: Integrera Azure Service Bus med Azure Private Link Service
description: Lär dig hur du integrerar Azure Service Bus med Azure Private Link Service
services: service-bus-messaging
author: spelluru
ms.author: spelluru
ms.date: 03/13/2020
ms.service: service-bus-messaging
ms.topic: article
ms.openlocfilehash: b8c4248b7275ac96acce96f890f6ff0148116f48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478011"
---
# <a name="integrate-azure-service-bus-with-azure-private-link-preview"></a>Integrera Azure Service Bus med Azure Private Link (förhandsversion)

Azure Private Link Service gör att du kan komma åt Azure-tjänster (till exempel Azure Service Bus, Azure Storage och Azure Cosmos DB) och Azure-värdbaserade kund-/partnertjänster via en **privat slutpunkt** i ditt virtuella nätverk.

En privat slutpunkt är ett nätverksgränssnitt som ansluter dig privat och säkert till en tjänst som drivs av Azure Private Link. Den privata slutpunkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt för in tjänsten i ditt virtuella nätverk. All trafik till tjänsten kan dirigeras via den privata slutpunkten, så inga gateways, NAT-enheter, ExpressRoute- eller VPN-anslutningar eller offentliga IP-adresser behövs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta detaljnivån i åtkomstkontrollen.

Mer information finns i [Vad är Azure Private Link?](../private-link/private-link-overview.md)

> [!NOTE]
> Den här funktionen stöds med **premiumnivån** för Azure Service Bus. Mer information om premiumnivån finns i artikeln [Service Bus Premium och Standard meddelandenivåer.](service-bus-premium-messaging.md)
>
> Den här funktionen är för närvarande i **förhandsgranskning**. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Lägga till en privat slutpunkt med Azure-portalen

### <a name="prerequisites"></a>Krav

Om du vill integrera ett servicebussnamnområde med Azure Private Link behöver du följande entiteter eller behörigheter:

- Ett namnområde för servicebuss.
- Ett virtuellt Azure-nätverk.
- Ett undernät i det virtuella nätverket.
- Ägare- eller deltagarbehörigheter för både servicebussnamnområdet och det virtuella nätverket.

Din privata slutpunkt och virtuella nätverk måste finnas i samma region. När du väljer en region för den privata slutpunkten med hjälp av portalen filtreras endast virtuella nätverk som finns i den regionen automatiskt. Namnet på servicebussen kan finnas i en annan region. Och, Din privata slutpunkt använder en privat IP-adress i ditt virtuella nätverk.

### <a name="steps"></a>steg

Om du redan har ett befintligt namnområde kan du skapa en privat slutpunkt genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Skriv in **Service Bus**i sökfältet .
3. Markera **namnområdet** i listan där du vill lägga till en privat slutpunkt.
4. Välj fliken **Nätverk** under **Inställningar**.
5. Välj fliken **Privata slutpunktsanslutningar (förhandsgranskning)** högst upp på sidan
6. Välj knappen **+ Privat slutpunkt** högst upp på sidan.

    ![Knappen Lägg till privat slutpunkt](./media/private-link-service/private-link-service-3.png)
7. Gör så här på sidan **Grunderna:** 
    1. Välj den **Azure-prenumeration** där du vill skapa den privata slutpunkten. 
    2. Välj **resursgruppen** för den privata slutpunktsresursen.
    3. Ange ett **namn** för den privata slutpunkten. 
    5. Välj en **region** för den privata slutpunkten. Din privata slutpunkt måste finnas i samma region som det virtuella nätverket, men kan finnas i en annan region tillbaka till den privata länkresurs som du ansluter till. 
    6. Välj **Nästa: Knappen Resurs >** längst ned på sidan.

        ![Skapa privat slutpunkt - sidan Grunderna](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Gör så här på sidan **Resurs:**
    1. Gör så här om du väljer **Anslut till en Azure-resurs i min katalog:**   
        1. Välj den **Azure-prenumeration** där **namnområdet för Service Bus** finns. 
        2. För **resurstyp**väljer du **Microsoft.ServiceBus/namespaces** för **resurstypen**.
        3. För **Resurs**väljer du ett servicebussnamnområde i listrutan. 
        4. Bekräfta att **underresursen Mål** är inställd på **namnområde**.
        5. Välj **Nästa: Knappen Konfiguration >** längst ned på sidan. 
        
            ![Skapa privat slutpunkt - resurssida](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. Om du väljer **Anslut till en Azure-resurs efter resurs-ID eller alias**gör du så här:
        1. Ange **resurs-ID** eller **alias**. Det kan vara det resurs-ID eller alias som vissa har delat med dig.
        2. För **Underresurs för mål**anger du **namnområde**. Det är den typ av underresurs som din privata slutpunkt kan komma åt. 
        3. (valfritt) Ange ett **meddelande om begäran**. Resursägaren ser det här meddelandet när du hanterar privat slutpunktsanslutning. 
        4. Välj sedan **Nästa: Knappen Konfiguration >** längst ned på sidan. 

            ![Skapa privat slutpunkt – anslut med resurs-ID](./media/private-link-service/connect-resource-id.png)
9. På sidan **Konfiguration** väljer du undernätet i ett virtuellt nätverk dit du vill distribuera den privata slutpunkten. 
    1. Välj ett **virtuellt nätverk**. Endast virtuella nätverk i den valda prenumerationen och platsen visas i listrutan. 
    2. Markera ett **undernät** i det virtuella nätverk som du har valt. 
    3. Välj **Nästa: Taggar >** knappen längst ned på sidan. 

        ![Skapa privat slutpunkt - konfigurationssida](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. På sidan **Taggar** skapar du alla taggar (namn och värden) som du vill associera med den privata slutpunktsresursen. Välj sedan **knappen Granska + skapa** längst ned på sidan. 
11. Granska alla inställningar i **granskning + skapa**och välj **Skapa** för att skapa den privata slutpunkten.
    
    ![Skapa privat slutpunkt - sidan Granska och skapa](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Bekräfta att den privata slutpunkten har skapats. Om du äger resursen och har valt **Anslut till en Azure-resurs i mitt katalogalternativ** för **anslutningsmetoden**ska slutpunktsanslutningen **godkännas automatiskt**. Om det är i **väntande** tillstånd läser du avsnittet [Hantera privata slutpunkter med hjälp av Azure Portal.](#manage-private-endpoints-using-azure-portal)

    ![Privat slutpunkt skapad](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Lägga till en privat slutpunkt med PowerShell
I följande exempel visas hur du använder Azure PowerShell för att skapa en privat slutpunktsanslutning till ett servicebussnamnområde.

Din privata slutpunkt och virtuella nätverk måste finnas i samma region. Namnet på servicebussen kan finnas i en annan region. Och, Din privata slutpunkt använder en privat IP-adress i ditt virtuella nätverk.

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

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

# create premium service bus namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Sku @{name = "Premium"; capacity = 1} -Properties @{} -ResourceType "Microsoft.ServiceBus/namespaces" -

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


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
1. Skriv in **Service Bus**i sökfältet .
1. Markera det **namnområde** som du vill hantera.
1. Välj fliken **Nätverk.**
5. Gå till lämpligt avsnitt nedan baserat på den åtgärd du vill: godkänna, avvisa eller ta bort. 

### <a name="approve-a-private-endpoint-connection"></a>Godkänna en privat slutpunktsanslutning

1. Om det finns några anslutningar som väntar visas en anslutning som visas med **Väntande** i etableringstillståndet. 
2. Välj den **privata slutpunkt** som du vill godkänna
3. Välj knappen **Godkänn.**

    ![Godkänna privat slutpunkt](./media/private-link-service/private-endpoint-approve.png)
4. På sidan **Godkänn anslutning** anger du en valfri **kommentar**och väljer **Ja**. Om du väljer **Nej**händer ingenting. 

    ![Sidan Godkänn anslutning](./media/private-link-service/approve-connection-page.png)
5. Du bör se status för anslutningen i listan **ändras**till Godkänd . 

    ![Anslutningsstatus - godkänd](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>Avvisa en privat slutpunktsanslutning

1. Om det finns några privata slutpunktsanslutningar som du vill avvisa, oavsett om det är en väntande begäran eller befintlig anslutning som godkändes tidigare, markerar du slutpunktsanslutningen och klickar på knappen **Avvisa.**

    ![Knappen Avvisa](./media/private-link-service/private-endpoint-reject.png)
2. På sidan **Avvisa anslutning** anger du en valfri kommentar och väljer **Ja**. Om du väljer **Nej**händer ingenting. 

    ![Sidan Avvisa anslutning](./media/private-link-service/reject-connection-page.png)
3. Du bör se status för anslutningen i listan **ändrad Avvisad**. 

    ![Slutpunkt avvisad](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>Ta bort en privat slutpunktsanslutning

1. Om du vill ta bort en privat slutpunktsanslutning markerar du den i listan och väljer **Ta bort** i verktygsfältet. 

    ![Knappen Ta bort](./media/private-link-service/remove-endpoint.png)
2. På sidan **Ta bort anslutning** väljer du **Ja** för att bekräfta borttagningen av den privata slutpunkten. Om du väljer **Nej**händer ingenting. 

    ![Ta bort anslutningssida](./media/private-link-service/delete-connection-page.png)
3. Du bör se statusen ändras till **Frånkopplad**. Sedan ser du slutpunkten försvinna från listan. 

## <a name="validate-that-the-private-link-connection-works"></a>Verifiera att den privata länkanslutningen fungerar

Du bör verifiera att resurserna i samma undernät till den privata slutpunktsresursen ansluter till tjänstbussens namnområde via en privat IP-adress och att de har rätt integrering av privata DNS-zoner.

Skapa först en virtuell dator genom att följa stegen i [Skapa en virtuell Windows-dator i Azure-portalen](../virtual-machines/windows/quick-create-portal.md)

På fliken **Nätverk:**

1. Ange **virtuellt nätverk** och **undernät**. Du kan skapa ett nytt virtuellt nätverk eller välja ett befintligt nätverk. Om du väljer en befintlig kontrollerar du att regionen matchar.
1. Ange en **offentlig IP-resurs.**
1. För säkerhetsgrupp för **NIC-nätverk**väljer du **Ingen**.
1. För **Belastningsutjämning**väljer du **Nej**.

Öppna kommandoraden och kör följande kommando:

```console
nslookup <your-service-bus-namespace-name>.servicebus.windows.net
```

Om du kör kommandot ns lookup för att matcha IP-adressen för ett Service Bus-namnområde över en offentlig slutpunkt visas ett resultat som ser ut så här:

```console
c:\ >nslookup <your-service-bus-namespace-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

Om du kör kommandot ns lookup för att matcha IP-adressen för ett Service Bus-namnområde över en privat slutpunkt visas ett resultat som ser ut så här:

```console
c:\ >nslookup your_service-bus-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Begränsningar och designöverväganden

**Priser**: För prisinformation finns i [Azure Private Link-priser](https://azure.microsoft.com/pricing/details/private-link/).

**Begränsningar**: Private Endpoint for Azure Service Bus finns i offentlig förhandsversion. Den här funktionen är tillgänglig i alla offentliga Azure-regioner.

**Maximalt antal privata slutpunkter per servicebussnamnområde:** 120.

Mer information finns i [Azure Private Link-tjänsten: Begränsningar](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Efterföljande moment

- Läs mer om [Azure Private Link](../private-link/private-link-service-overview.md)
- Läs mer om [Azure Service Bus](service-bus-messaging-overview.md)
