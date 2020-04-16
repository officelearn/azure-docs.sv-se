---
title: Integrera med Azure Private Link-tjänsten
description: Lär dig hur du integrerar Azure Key Vault med Azure Private Link Service
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 03/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.openlocfilehash: 678e91126c04d5b299d9234a1602580260c5aee6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425093"
---
# <a name="integrate-key-vault-with-azure-private-link"></a>Integrera nyckelvalv med Privat Azure-länk

Azure Private Link Service kan du komma åt Azure Services (till exempel Azure Key Vault, Azure Storage och Azure Cosmos DB) och Azure värd kund / partner tjänster över en privat slutpunkt i ditt virtuella nätverk.

En Azure Private Endpoint är ett nätverksgränssnitt som ansluter dig privat och säkert till en tjänst som drivs av Azure Private Link. Den privata slutpunkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt för in tjänsten i ditt virtuella nätverk. All trafik till tjänsten kan dirigeras via den privata slutpunkten, så inga gateways, NAT-enheter, ExpressRoute- eller VPN-anslutningar eller offentliga IP-adresser behövs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta detaljnivån i åtkomstkontrollen.

Mer information finns i [Vad är Azure Private Link?](../../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Krav

Om du vill integrera ett nyckelvalv med Azure Private Link behöver du följande:

- Ett nyckelvalv.
- Ett virtuellt Azure-nätverk.
- Ett undernät i det virtuella nätverket.
- Ägare- eller deltagarbehörigheter för både nyckelvalvet och det virtuella nätverket.

Din privata slutpunkt och virtuella nätverk måste finnas i samma region. När du väljer en region för den privata slutpunkten med hjälp av portalen filtreras endast virtuella nätverk som finns i den regionen automatiskt. Ditt nyckelvalv kan finnas i en annan region.

Din privata slutpunkt använder en privat IP-adress i det virtuella nätverket.

## <a name="establish-a-private-link-connection-to-key-vault-using-the-azure-portal"></a>Upprätta en privat länkanslutning till Key Vault med Azure-portalen 

Skapa först ett virtuellt nätverk genom att följa stegen i [Skapa ett virtuellt nätverk med Azure-portalen](../../virtual-network/quick-create-portal.md)

Du kan sedan antingen skapa ett nytt nyckelvalv eller upprätta en privat länkanslutning till ett befintligt nyckelvalv.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Skapa ett nytt nyckelvalv och upprätta en privat länkanslutning

Du kan skapa ett nytt nyckelvalv genom att följa stegen i [Ange och hämta en hemlighet från Azure Key Vault med Azure-portalen](../secrets/quick-create-portal.md)

När du har konfigurerat grunderna i nyckelvalvet väljer du fliken Nätverk och gör så här:

1. Välj alternativknappen Privat slutpunkt på fliken Nätverk.
1. Klicka på knappen "+ Lägg till" om du vill lägga till en privat slutpunkt.

    ![Bild](../media/private-link-service-1.png)
 
1. I fältet "Plats" i Bladet Skapa privat slutpunkt väljer du den region där det virtuella nätverket finns. 
1. Skapa ett beskrivande namn i fältet "Namn" som gör att du kan identifiera den här privata slutpunkten. 
1. Välj det virtuella nätverk och det virtuella undernät som du vill att den här privata slutpunkten ska skapas på den nedrullningsbara menyn. 
1. Lämna alternativet "integrera med den privata zonen DNS" oförändrad.  
1. Välj "Ok".

    ![Bild](../media/private-link-service-8.png)
 
Du kommer nu att kunna se den konfigurerade privata slutpunkten. Nu har du möjlighet att ta bort och redigera den här privata slutpunkten. Välj knappen "Granska + Skapa" och skapa nyckelvalvet. Det tar 5-10 minuter innan distributionen slutförs. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Upprätta en privat länkanslutning till ett befintligt nyckelvalv

Om du redan har ett nyckelvalv kan du skapa en privat länkanslutning genom att följa dessa steg:

1. Logga in på Azure Portal. 
1. Skriv in "nyckelvalv" i sökfältet
1. Välj nyckelvalvet i listan som du vill lägga till en privat slutpunkt till.
1. Välj fliken "Nätverk" under Inställningar
1. Välj fliken Privata slutpunktsanslutningar högst upp på sidan
1. Välj knappen "+ Privat slutpunkt" högst upp på sidan.

    ![Bildbild](../media/private-link-service-3.png) ![](../media/private-link-service-4.png)

Du kan välja att skapa en privat slutpunkt för alla Azure-resurser i att använda det här bladet. Du kan antingen använda de rullgardinsmenyerna för att välja en resurstyp och välja en resurs i katalogen, eller så kan du ansluta till valfri Azure-resurs med hjälp av ett resurs-ID. Lämna alternativet "integrera med den privata zonen DNS" oförändrad.  

![Bildbild](../media/private-link-service-3.png)
![](../media/private-link-service-4.png)

## <a name="establish-a-private-link-connection-to-key-vault-using-cli"></a>Upprätta en privat länkanslutning till Key Vault med CLI

### <a name="login-to-azure-cli"></a>Logga in på Azure CLI
```console
az login 
```
### <a name="select-your-azure-subscription"></a>Välj din Azure-prenumeration 
```console
az account set --subscription {AZURE SUBSCRIPTION ID}
```
### <a name="create-a-new-resource-group"></a>Skapa en ny resursgrupp 
```console
az group create -n {RG} -l {AZURE REGION}
```
### <a name="register-microsoftkeyvault-as-a-provider"></a>Registrera Microsoft.KeyVault som leverantör 
```console
az provider register -n Microsoft.KeyVault
```
### <a name="create-a-new-key-vault"></a>Skapa ett nytt nyckelvalv
```console
az keyvault create --name {KEY VAULT NAME} --resource-group {RG} --location {AZURE REGION}
```
### <a name="turn-on-key-vault-firewall"></a>Aktivera Nyckelvalvsbrandväggen
```console
az keyvault update --name {KEY VAULT NAME} --resource-group {RG} --location {AZURE REGION} --default-action deny
```
### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
```console
az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
```
### <a name="add-a-subnet"></a>Lägga till ett undernät
```console
az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
```
### <a name="disable-virtual-network-policies"></a>Inaktivera principer för virtuella nätverk 
```console
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
```
### <a name="add-a-private-dns-zone"></a>Lägga till en privat DNS-zon 
```console
az network private-dns zone create --resource-group {RG} --name privatelink.vaultcore.azure.net
```
### <a name="link-private-dns-zone-to-virtual-network"></a>Länka privat DNS-zon till virtuellt nätverk 
```console
az network private-dns link vnet create --resoruce-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.vaultcore.azure.net --name {dnsZoneLinkName} --registration-enabled true
```
### <a name="create-a-private-endpoint-automatically-approve"></a>Skapa en privat slutpunkt (godkänn automatiskt) 
```console
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/ {KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION}
```
### <a name="create-a-private-endpoint-manually-request-approval"></a>Skapa en privat slutpunkt (godkännande för begäran om manuellt begäran) 
```console
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/ {KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```
### <a name="show-connection-status"></a>Visa anslutningsstatus 
```console
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
```
## <a name="manage-private-link-connection"></a>Hantera privat länkanslutning

När du skapar en privat slutpunkt måste anslutningen godkännas. Om resursen som du skapar en privat slutpunkt för finns i katalogen kan du godkänna anslutningsbegäran förutsatt att du har tillräcklig behörighet. Om du ansluter till en Azure-resurs i en annan katalog måste du vänta på att resursägaren godkänner din anslutningsbegäran.

Det finns fyra etableringstillstånd:

| Tjänst tillhandahålla åtgärd | Privat slutpunktstillstånd för tjänstkonsument | Beskrivning |
|--|--|--|
| Ingen | Väntande åtgärder | Anslutningen skapas manuellt och väntar på godkännande från private link-resursägaren. |
| Godkänn | Godkända | Anslutningen godkändes automatiskt eller manuellt och är klar att användas. |
| Avvisa | Avvisad | Anslutningen avvisades av ägaren till den privata länkresursen. |
| Ta bort | Frånkopplad | Anslutningen togs bort av ägaren till den privata länkresursen, den privata slutpunkten blir informativ och bör tas bort för rensning. |
 
###  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-the-azure-portal"></a>Hantera en privat slutpunktsanslutning till Key Vault med Azure-portalen 

1. Logga in på Azure Portal.
1. Skriv in "nyckelvalv" i sökfältet
1. Markera det nyckelvalv som du vill hantera.
1. Välj fliken "Nätverk".
1. Om det finns några anslutningar som väntar visas en anslutning med "Väntande" i etableringstillståndet. 
1. Välj den privata slutpunkt som du vill godkänna
1. Välj knappen godkänn.
1. Om det finns några privata slutpunktsanslutningar som du vill avvisa, oavsett om det är en väntande begäran eller befintlig anslutning, markerar du anslutningen och klickar på knappen "Avvisa".

    ![Bild](../media/private-link-service-7.png)

##  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-azure-cli"></a>Hantera en privat slutpunktsanslutning till Key Vault med Azure CLI

### <a name="approve-a-private-link-connection-request"></a>Godkänna en begäran om anslutning till en privat länk
```console
az keyvault private-endpoint-connection approve --approval-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}
```

### <a name="deny-a-private-link-connection-request"></a>Neka en begäran om privat länkanslutning
```console
az keyvault private-endpoint-connection reject --rejection-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}
```

### <a name="delete-a-private-link-connection-request"></a>Ta bort en begäran om privat länkanslutning
```console
az keyvault private-endpoint-connection delete --resource-group {RG} --vault-name {KEY VAULT NAME} --name {PRIVATE LINK CONNECTION NAME}
```

## <a name="validate-that-the-private-link-connection-works"></a>Verifiera att den privata länkanslutningen fungerar

Du bör verifiera att resurserna inom samma undernät till den privata slutpunktsresursen ansluter till nyckelvalvet via en privat IP-adress och att de har rätt integrering av privata DNS-zoner.

Skapa först en virtuell dator genom att följa stegen i [Skapa en virtuell Windows-dator i Azure-portalen](../../virtual-machines/windows/quick-create-portal.md)

På fliken Nätverk:

1. Ange virtuellt nätverk och undernät. Du kan skapa ett nytt virtuellt nätverk eller välja ett befintligt nätverk. Om du väljer en befintlig kontrollerar du att regionen matchar.
1. Ange en offentlig IP-resurs.
1. Välj "Ingen" i säkerhetsgruppen "NIC Network".
1. Välj "Nej" i "Belastningsutjämning".

Öppna kommandoraden och kör följande kommando:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Om du kör kommandot ns lookup för att lösa IP-adressen för ett nyckelvalv över en offentlig slutpunkt visas ett resultat som ser ut så här:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Om du kör kommandot ns lookup för att matcha IP-adressen för ett nyckelvalv över en privat slutpunkt visas ett resultat som ser ut så här:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="limitations-and-design-considerations"></a>Begränsningar och designöverväganden

**Priser**: För prisinformation finns i [Azure Private Link-priser](https://azure.microsoft.com/pricing/details/private-link/).

**Begränsningar**: Privat slutpunkt för Azure Key Vault är endast tillgänglig i offentliga Azure-regioner.

**Maximalt antal privata slutpunkter per nyckelvalv:** 64.

**Maximalt antal nyckelvalv med privata slutpunkter per prenumeration:** 64.

Mer information finns i [Azure Private Link-tjänsten: Begränsningar](../../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Efterföljande moment

- Läs mer om [Azure Private Link](../../private-link/private-link-service-overview.md)
- Läs mer om [Azure Key Vault](overview.md)
