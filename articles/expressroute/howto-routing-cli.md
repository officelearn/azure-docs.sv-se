---
title: 'Konfigurera peering för en krets – ExpresssRoute: Azure CLI | Microsoft Docs'
description: Den här artikeln hjälper dig att skapa och etablera privat, offentlig och Microsoft-peering av en ExpressRoute-krets. I artikeln får du även se hur man kontrollerar status, uppdaterar eller tar bort peerings för din krets.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 8d6b361bf7e1b18c44719a8cdbe2e958ac63006d
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965773"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Skapa och ändra peering för en ExpressRoute-krets med CLI

Den här artikeln hjälper dig att skapa och hantera konfiguration/peering av routning för en ExpressRoute-krets i distributions modellen för Resource Manager med CLI. Du kan också kontrol lera status, uppdatera eller ta bort och avetablera peer-kopplingar för en ExpressRoute-krets. Om du vill använda en annan metod för att arbeta med din krets väljer du en artikel i följande lista:

> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video-privat peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – offentlig peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video – Microsoft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Innan du börjar ska du installera den senaste versionen av CLI-kommandona (2.0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI](/cli/azure/install-azure-cli).
* Kontrol lera att du har granskat [](expressroute-prerequisites.md) [kraven, Dirigerings kraven](expressroute-routing.md)och [arbets flödes](expressroute-workflows.md) sidorna innan du påbörjar konfigurationen.
* Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](howto-circuit-cli.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett tillhandahållet och aktiverat tillstånd för att du ska kunna köra kommandona i den här artikeln.

Dessa anvisningar gäller endast för kretsar som skapats med tjänstleverantörer som erbjuder tjänster för Layer 2-anslutning. Om du använder en tjänst leverantör som erbjuder hanterade Layer 3-tjänster (vanligt vis en IPVPN, t. ex. MPLS), kommer anslutnings leverantören konfigurera och hantera routning åt dig.

Du kan konfigurera en, två eller alla tre peer-datorer (Azure Private, Azure Public och Microsoft) för en ExpressRoute-krets. Du kan konfigurera peerings i valfri ordning. Dock måste du se till att du slutför konfigurationen av en peering i taget. Mer information om routningsdomäner och peering-domäner finns i [ExpressRoute](expressroute-circuit-peerings.md).

## <a name="msft"></a>Microsoft-peering

Det här avsnittet hjälper dig att skapa, Hämta, uppdatera och ta bort Microsofts peering-konfiguration för en ExpressRoute-krets.

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som har kon figurer ATS före den 1 augusti 2017 kommer att ha alla tjänst prefix som annonseras via Microsoft-peering, även om det inte finns några väg filter definierade. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inga prefix som annonseras förrän ett flödes filter är kopplat till kretsen. Mer information finns i [Konfigurera ett flödes filter för Microsoft-peering](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Så här skapar du Microsoft-peering

1. Installera den senaste versionen av Azure CLI. Använd den senaste versionen av Azures kommando rads gränssnitt (CLI). * granska [nödvändiga komponenter](expressroute-prerequisites.md) och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

   ```azurecli-interactive
   az login
   ```

   Välj den prenumeration som du vill skapa ExpressRoute-krets för.

   ```azurecli-interactive
   az account set --subscription "<subscription ID>"
   ```
2. Skapa en ExpressRoute-krets. Följ anvisningarna för att skapa en [ExpressRoute-krets](howto-circuit-cli.md) och etablera den med anslutningsprovidern. Om din anslutnings leverantör erbjuder hanterade Layer 3-tjänster kan du be din anslutnings leverantör att aktivera Microsoft-peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om din anslutnings leverantör inte hanterar routning åt dig, kan du, när du har skapat din krets, fortsätta med konfigurationen med hjälp av nästa steg. 

3. Kontrol lera ExpressRoute-kretsen för att se till att den är etablerad och också aktive rad. Använd följande exempel:

   ```azurecli-interactive
   az network express-route list
   ```

   Svaret liknar följande exempel:

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Konfigurera Microsoft-peering för kretsen. Kontrollera att du har följande information innan du fortsätter.

   * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR.
   * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * Annonserade prefix: Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Om du planerar att skicka en uppsättning prefix kan du skicka en kommaavgränsad lista. Dessa prefix måste vara registrerade åt dig i ett RIR/IR.
   * **Valfritt-** Kund-ASN: om du annonserar prefix som inte har registrerats för peering som ett nummer kan du ange det AS-nummer som de är registrerade för.
   * Routningens registernamn: Du kan ange den RIR/IR mot vilken AS-numret och prefixet är registrerade.
   * **Valfritt-** En MD5-hash om du väljer att använda en.

   Kör följande exempel för att konfigurera Microsoft-peering för din krets:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="getmsft"></a>Visa Microsofts peering-information

Du kan hämta konfigurations information med hjälp av följande exempel:

```azurecli-interactive
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> Microsoft kontrollerar om de angivna annonserade offentliga prefixen och peer-ASN (eller kund-ASN) är tilldelade till dig i Internet-routningstabellen. Om du hämtar de offentliga prefixen från en annan entitet och om tilldelningen inte är registrerad i routningstabellen, slutförs inte den automatiska verifieringen och kräver manuell verifiering. Om den automatiska valideringen Miss lyckas, visas "AdvertisedPublicPrefixesState" som "verifiering krävs" i utdata för kommandot ovan. 
> 
> Om du ser meddelandet "validering krävs" samlar du in dokumenten som visar att de offentliga prefixen har tilldelats till din organisation av den entitet som anges som ägare till prefixen i routningstabellen och skickar dessa dokument för manuell verifiering med öppna ett support ärende som visas nedan. 
> 
>

Utdata ser ut ungefär så här:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzureMicrosoftPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": {
    "advertisedPublicPrefixes": [
        ""
      ],
     "advertisedPublicPrefixesState": "",
     "customerASN": ,
     "routingRegistryName": ""
  }
  "name": "AzureMicrosoftPeering",
  "peerAsn": ,
  "peeringType": "AzureMicrosoftPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updatemsft"></a>Så här uppdaterar du Microsofts peering-konfiguration

Du kan uppdatera valfri del av konfigurationen. De annonserade prefixen för kretsen uppdateras från 123.1.0.0/24 till 124.1.0.0/24 i följande exempel:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="addIPv6msft"></a>Lägga till IPv6 Microsoft-peering-inställningar i en befintlig IPv4-konfiguration

```azurecli-interactive
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="deletemsft"></a>Ta bort Microsoft-peering

Du kan ta bort peering-konfigurationen genom att köra följande exempel:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="private"></a>Azure privat peering

Det här avsnittet hjälper dig att skapa, Hämta, uppdatera och ta bort Azures konfiguration för privata peering för en ExpressRoute-krets.

### <a name="to-create-azure-private-peering"></a>Så här skapar du Azures privata peering

1. Installera den senaste versionen av Azure CLI. Du måste använda den senaste versionen av Azures kommando rads gränssnitt (CLI). * granska [nödvändiga komponenter](expressroute-prerequisites.md) och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

   ```azurecli-interactive
   az login
   ```

   Välj den prenumeration som du vill skapa ExpressRoute-kretsen för

   ```azurecli-interactive
   az account set --subscription "<subscription ID>"
   ```
2. Skapa en ExpressRoute-krets. Följ anvisningarna för att skapa en [ExpressRoute-krets](howto-circuit-cli.md) och etablera den med anslutningsprovidern. Om din anslutnings leverantör erbjuder hanterade Layer 3-tjänster kan du be din anslutnings leverantör att aktivera Azures privata peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om din anslutnings leverantör inte hanterar routning åt dig, kan du, när du har skapat din krets, fortsätta med konfigurationen med hjälp av nästa steg.

3. Kontrol lera ExpressRoute-kretsen för att se till att den är etablerad och också aktive rad. Använd följande exempel:

   ```azurecli-interactive
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
   ```

   Svaret liknar följande exempel:

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
   "bandwidthInMbps": 200,
   "peeringLocation": "Silicon Valley",
   "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Konfigurera Azures privata peering för kretsen. Kontrollera att du har följande objekt innan du fortsätter med nästa steg:

   * Ett /30 undernät för den primära länken. Under nätet får inte ingå i något adress utrymme som är reserverat för virtuella nätverk.
   * Ett /30 undernät för den sekundära länken. Under nätet får inte ingå i något adress utrymme som är reserverat för virtuella nätverk.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal. Du kan använda ett privat AS-tal för den här peeringen. Se till att du inte använder 65515.
   * **Valfritt-** En MD5-hash om du väljer att använda en.

   Använd följande exempel för att konfigurera Azures privata peering för din krets:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   Om du väljer att använda en MD5-hash använder du följande exempel:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.
   > 
   > 

### <a name="getprivate"></a>Så här visar du information om Azure privat peering

Du kan hämta konfigurations information med hjälp av följande exempel:

```azurecli-interactive
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

Utdata ser ut ungefär så här:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updateprivate"></a>Så här uppdaterar du Azures konfiguration för privat peering

Du kan uppdatera valfri del av konfigurationen med hjälp av följande exempel. I det här exemplet uppdateras VLAN-ID: t för kretsen från 100 till 500.

```azurecli-interactive
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="deleteprivate"></a>Ta bort privat Azure-peering

Du kan ta bort peering-konfigurationen genom att köra följande exempel:

> [!WARNING]
> Du måste se till att alla virtuella nätverk och ExpressRoute Global Reach-anslutningar tas bort innan du kör det här exemplet. 
> 
> 

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

## <a name="public"></a>Offentlig Azure-peering

Det här avsnittet hjälper dig att skapa, Hämta, uppdatera och ta bort den offentliga Azure-peering-konfigurationen för en ExpressRoute-krets.

> [!Note]
> Offentlig Azure-peering är inaktuell för nya kretsar. Mer information finns i [ExpressRoute-peering](expressroute-circuit-peerings.md).
>

### <a name="to-create-azure-public-peering"></a>Så här skapar du Azures offentliga peering

1. Installera den senaste versionen av Azure CLI. Du måste använda den senaste versionen av Azures kommando rads gränssnitt (CLI). * granska [nödvändiga komponenter](expressroute-prerequisites.md) och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

   ```azurecli-interactive
   az login
   ```

   Välj den prenumeration som du vill skapa ExpressRoute-krets för.

   ```azurecli-interactive
   az account set --subscription "<subscription ID>"
   ```
2. Skapa en ExpressRoute-krets.  Följ anvisningarna för att skapa en [ExpressRoute-krets](howto-circuit-cli.md) och etablera den med anslutningsprovidern. Om din anslutnings leverantör erbjuder hanterade Layer 3-tjänster kan du be anslutnings leverantören att aktivera Azures offentliga peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Om din anslutnings leverantör inte hanterar routning åt dig, kan du, när du har skapat din krets, fortsätta med konfigurationen med hjälp av nästa steg.

3. Kontrol lera ExpressRoute-kretsen för att säkerställa att den är etablerad och också aktive rad. Använd följande exempel:

   ```azurecli-interactive
   az network express-route list
   ```

   Svaret liknar följande exempel:

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Konfigurera Azures offentliga peering för kretsen. Se till att du har följande information innan du fortsätter.

   * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
   * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * **Valfritt-** En MD5-hash om du väljer att använda en.

   Kör följande exempel för att konfigurera Azures offentliga peering för din krets:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Om du väljer att använda en MD5-hash använder du följande exempel:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.

### <a name="getpublic"></a>Så här visar du information om offentliga Azure-peering

Du kan hämta konfigurations information med hjälp av följande exempel:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

Utdata ser ut ungefär så här:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updatepublic"></a>Så här uppdaterar du Azures offentliga peering-konfiguration

Du kan uppdatera valfri del av konfigurationen med hjälp av följande exempel. I det här exemplet uppdateras VLAN-ID: t för kretsen från 200 till 600.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="deletepublic"></a>Ta bort offentlig Azure-peering

Du kan ta bort peering-konfigurationen genom att köra följande exempel:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="next-steps"></a>Nästa steg

Nästa steg [Länka ett VNet till en ExpressRoute-krets](howto-linkvnet-cli.md).

* Mer information om ExpressRoute-arbetsflöden finns i [ExpressRoute-arbetsflöden](expressroute-workflows.md).
* Mer information om krets-peering finns i [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).
* Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md).
