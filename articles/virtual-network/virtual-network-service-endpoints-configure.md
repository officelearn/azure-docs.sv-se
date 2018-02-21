---
title: "Konfigurera Azure-tjänstslutpunkter för virtuella nätverk | Microsoft Docs"
description: "Lär dig hur du aktiverar och inaktiverar tjänstslutpunkter från det virtuella nätverket"
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2018
ms.author: anithaa
ms.custom: 
ms.openlocfilehash: e705946dca839e6b8e11872ad5431f125b9fb41d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="configure-virtual-network-service-endpoints"></a>Konfigurera tjänstslutpunkter för virtuellt nätverk

Tjänstslutpunkter för virtuella nätverk (VNet) gör det möjligt att skydda Azure-tjänstresurser för Azure Virtual Network genom att helt ta bort Internetåtkomst för dessa resurser. Tjänstslutpunkter ger direkt anslutning från det virtuella nätverket till en Azure-tjänst, vilket gör att du kan använda det virtuella nätverkets privata adressutrymme för att få åtkomst till Azure-tjänster. Trafik till Azure-tjänster genom tjänstslutpunkterna finns alltid kvar i Microsoft Azure-stamnätverket. Läs mer om [Slutpunkter för virtuellt nätverk](virtual-network-service-endpoints-overview.md)

Den här artikeln innehåller steg för att aktivera och inaktivera tjänstslutpunkter. När slutpunkterna har inaktiverats i ett delnät för en Azure-tjänst kan du skydda specifika tjänsteresurser i ett virtuellt nätverk.

Tjänstslutpunkter kan konfigureras med [Azure Portal](#azure-portal), [Azure PowerShell](#azure-powershell), [kommandoradsgränssnitt för Azure](#azure-cli) eller en [mall](#resource-manager-template) för Azure Resource Manager.

>[!NOTE]
Under förhandsgranskningen stöds funktionen med tjänstslutpunkt för virtuella nätverk för specifika regioner. En lista över regioner som stöds finns på sidan [Uppdateringar för Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="service-endpoint-configuration-overview"></a>Översikt över konfiguration av tjänstslutpunkt

- Tjänstslutpunkter kan endast konfigureras i virtuella nätverk som distribueras genom Azure Resource Manager-distributionsmodellen.

- Tjänstslutpunkter konfigureras på varje undernät i ett virtuellt nätverk.

- För ett undernät kan du endast konfigurera en tjänstslutpunkt till en tjänst. Du kan konfigurera flera tjänstslutpunkter till olika tjänster (exempelvis Azure Storage, Azure SQL).

- Du kan aktivera slutpunkterna i ett nytt eller befintligt undernät.

- Platsen konfigureras automatiskt för en slutpunkt. Tjänstslutpunkter konfigureras som standard till det virtuella nätverkets region. För att ge stöd för regionala redundansscenarier i Azure Storage konfigureras slutpunkter automatiskt till [kopplade Azure-regioner.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)

  >[!NOTE]
  Beroende på det virtuella nätverkets/undernätets storlek kan det ta en stund att aktivera tjänstslutpunkten. Se till att inga kritiska aktiviteter pågår när du aktiverar tjänstslutpunkter. Tjänstslutpunkter byter väg på varje NIC i undernätet och kan avsluta eventuella öppna TCP-anslutningar. 

- Tjänstslutpunktens anrop returnerar ”slutförd” när trafikflöden till tjänsten på alla NIC i undernätet har växlats till det virtuella nätverkets privata IP-adresser.

- Gällande routningar för att validera slutpunktskonfiguration:

   I syfte att validera om tjänstslutpunkten är korrekt konfigurerad visar "gällande routningar" på en NIC i undernätet en ny "standardväg" med nextHopType: VirtualNetworkServiceEndpoint, per tjänst och per region. Lär dig mer om [felsökning med gällande routningar](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#using-effective-routes-to-troubleshoot-vm-traffic-flow)

   >[!NOTE]
   Gällande routningar kan endast visas om ett eller flera nätverksgränssnit (NIC) har konfigurerats och kopplats till en virtuell maskin som körs i undernätet.

## <a name="azure-portal"></a>Azure Portal

### <a name="setting-up-service-endpoint-on-a-subnet-during-vnet-create"></a>Konfigurera tjänstslutpunkten i ett undernät under VNet Create

1. Öppna [Azure-portalen](https://portal.azure.com/).
Logga in på Azure med ditt Azure-konto. Om du inte har ett Azure-konto kan du registrera dig för en kostnadsfri utvärderingsversion. Kontot måste ha nödvändiga [behörigheter](#provisioning) för att skapa ett virtuellt nätverk och tjänstslutpunkten.
2. Klicka på + Ny > Nätverk > Virtuellt nätverk > + Lägg till.
3. Ange följande värden under Skapa virtuellt nätverk och klicka på Skapa:

Inställning | Värde
------- | -----
Namn    | myVnet
Adressutrymme | 10.0.0.0/16
Namn på undernät|mySubnet
Adressintervall för undernätet|10.0.0.0/24
Resursgrupp|Låt Skapa ny vara markerat och ange ett namn.
Plats|Valfri region som stöds, t.ex. Östra Australien
Prenumeration|Välj din prenumeration.
__ServiceEndpoints__|Enabled
__Tjänster__ | Välj en eller alla tillgängliga tjänster. Tjänster som stöds: __"Microsoft.Storage", "Microsoft.Sql"__.

Välj tjänster för slutpunkter: ![Select Service Endpoint Services](media/virtual-network-service-endpoints-portal/vnet-create-flow-services.png) (Välj tjänstens slutpunktstjänster)

4. Kontrollera att alla inställningarna är korrekta och klicka på ”Skapa”.

![Ange tjänstslutpunkt](media/virtual-network-service-endpoints-portal/create-vnet-flow.png)

5. Slutför skyddet för Azure-tjänstresurser i det virtuella nätverket genom att klicka på tjänstdokumentationen i [Nästa steg](#Next%20Steps).


### <a name="validating-service-endpoint-configuration"></a>Kontrollera konfiguration av tjänstslutpunkt

Bekräfta att tjänstslutpunkterna har konfigurerats med följande steg:

- I resurserna klickar du på ”Virtuella nätverk”. Sök efter det virtuella nätverket.
- Klicka på det virtuella nätverkets namn och gå till ”Tjänstens slutpunkter”
- Konfigurerade slutpunkter visas som ”Lyckades”. Automatiskt konfigurerade platser kan också visas

![Bekräfta konfiguration av tjänstslutpunkt](media/virtual-network-service-endpoints-portal/vnet-validate-settings.png
)

### <a name="effective-routes-to-validate-endpoint-configuration"></a>Gällande routningar för att validera slutpunktskonfiguration

Om du vill visa gällande routning i ett nätverksgränssnitt (NIC) på undernätet klickar du på valfri NIC i det undernätet. Klicka på ”Gällande routningar” under ”Support och felsökning”. Om slutpunkten är konfigurerad visas en ny ”standardroutning” med adressprefix för tjänsten som mål och nextHopType som ”VirtualNetworkServiceEndpoint”.

![Gällande routningar för tjänstslutpunkter](media/virtual-network-service-endpoints-portal/effective-routes.png)

### <a name="setting-up-service-endpoints-for-existing-subnets-in-a-vnet"></a>Konfigurera tjänstslutpunkter för befintliga undernät i ett virtuellt nätverk

1. Klicka på ”Virtuella nätverk” under resurser och sök efter ett befintligt virtuellt nätverk
2. Klicka på det virtuella nätverkets namn och gå till ”Tjänstens slutpunkter”
3. Klicka på ”Lägg till”. Välj ”Tjänst”. Du kan endast skapa en slutpunkt till en tjänst i taget. 
4. Välj alla undernät där du vill använda slutpunkten. Klicka på ”Lägg till”

![Konfiguration av tjänstslutpunkt i undernät](media/virtual-network-service-endpoints-portal/existing-subnet.png)

### <a name="deleting-service-endpoints"></a>Ta bort tjänstslutpunkter
1. I resurserna klickar du på ”Virtuella nätverk”. Sök efter ett befintligt virtuellt nätverk genom att filtrera på det virtuella nätverkets namn.
2. Klicka på det virtuella nätverkets namn och gå till ”Tjänstens slutpunkter”
3. Klicka på tjänstens namn och högerklicka på tjänstslutpunktens post
4. Välj ”Ta bort”

![Ta bort tjänstslutpunkt](media/virtual-network-service-endpoints-portal/delete-endpoint.png)

## <a name="azure-powershell"></a>Azure PowerShell

Förutsättningar för konfiguration:

- Installera den senaste versionen av PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/)-modulen. Om du inte har använt Azure PowerShell kan du läsa [Översikt över Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Starta en PowerShell-session genom att gå till **Start**, ange **powershell** och sedan klicka på **PowerShell**.
- I PowerShell loggar du in i Azure genom att ange kommandot `login-azurermaccount`. Kontot måste ha nödvändiga [behörigheter](#provisioning) för att skapa ett virtuellt nätverk och tjänstslutpunkten.

### <a name="get-available-service-endpoints-for-azure-region"></a>Hämta tillgängliga tjänstslutpunkter för Azure-regionen

Använd kommandot nedan för att hämta listan över tjänster som stöds för slutpunkter för en Azure-region.
 ```powershell
Get-AzureRmVirtualNetworkAvailableEndpointService -location eastus
```

Resultat: 
Namn | ID | Typ
-----|----|-------
Microsoft.Storage|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage|Microsoft.Network/virtualNetworkEndpointServices
Microsoft.Sql|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql|Microsoft.Network/virtualNetworkEndpointServices

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Lägg till tjänstslutpunkt för Azure Storage i undernätet *mySubnet* när du skapar det virtuella nätverket *myVNet*

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix "10.0.1.0/24" -ServiceEndpoint “Microsoft.Storage”

New-AzureRmVirtualNetwork -Name "myVNet" -AddressPrefix "10.0.0.0/16" -Subnet $subnet -ResourceGroupName "myRG" -Location "WestUS"
```
Du kan aktivera flera tjänster med hjälp av en kommaseparerad lista över tjänstnamn.
Exempel: ”Microsoft.Storage”, ”Microsoft.Sql”

Förväntad utdata:
```
Subnets : [
            {
            "Name": "mySubnet",
             ...
            "ServiceEndpoints": [
              {
                   "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "westus",
                        "eastus"
                                 ]
               }
                                ],
            "ProvisioningState": "Succeeded"
            }
          ]
```

Slutför skyddet för Azure-tjänstresurser i det virtuella nätverket genom att klicka på tjänstdokumentationen i [Nästa steg](#Next%20Steps).

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Lägga till flera tjänstslutpunkter i ett befintligt undernät

```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint "Microsoft.Storage", "Microsoft.Sql" | Set-AzureRmVirtualNetwork
```

Förväntad utdata: 
```
Subnets : [
            {
                "Name": "mySubnet",
                 ...
                "ServiceEndpoints": [
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "eastus",
                        "westus"
                                 ]
                },
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Sql",
                    "Locations": [
                        "eastus"
                                 ]
                }
                ],
               "ProvisioningState": "Succeeded"
            }
         ]
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Visa tjänstslutpunkter som har konfigurerats i ett undernät

```powershell
$subnet=Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"
$subnet.ServiceEndpoints
```
Resultat:
```
ProvisioningState Service           Locations
----------------- -------           ---------
Succeeded         Microsoft.Storage {eastus, westus}
Succeeded         Microsoft.Sql     {eastus}
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Ta bort tjänstslutpunkter i ett undernät
```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint $null | Set-AzureRmVirtualNetwork
```

## <a name="azure-cli"></a>Azure CLI

Förutsättningar för konfiguration:
- Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#az_login) och följ anvisningarna på skärmen. Mer information om att logga in finns i [Kom igång med Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
 - Kontot måste ha nödvändiga [behörigheter](#provisioning) för att skapa ett virtuellt nätverk och tjänstslutpunkten.

 Fullständig lista med kommandon för virtuella nätverk finns i [Azure CLI Virtual Network commands](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) (Kommandon för virtuellt Azure CLI-nätverk)

### <a name="get-available-service-endpoints-for-azure-region"></a>Hämta tillgängliga tjänstslutpunkter för Azure-regionen

Använd kommandot nedan för att hämta listan över tjänster som stöds för slutpunkter för en Azure-region, t.ex. ”EastUS”.
```azure-cli
az network vnet list-endpoint-services -l eastus
```
Resultat:
```
    {
    "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage",
    "name": "Microsoft.Storage",
    "type": "Microsoft.Network/virtualNetworkEndpointServices"
     },
     {
     "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql",
     "name": "Microsoft.Sql",
     "type":   "Microsoft.Network/virtualNetworkEndpointServices"
     }
```

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Lägg till tjänstslutpunkt för Azure Storage i undernätet *mySubnet* när du skapar det virtuella nätverket *myVNet*

```azure-cli
az network vnet create -g myRG -n myVNet --address-prefixes 10.0.0.0/16 -l eastUS

az network vnet subnet create -g myRG -n mySubnet --vnet-name myVNet --address-prefix 10.0.1.0/24 --service-endpoints Microsoft.Storage
```

Lägga till flera slutpunkter: --service-endpoints Microsoft.Storage Microsoft.Sql

Resultat:
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    }
  ]
}
```

Slutför skyddet för Azure-tjänstresurser i det virtuella nätverket genom att klicka på tjänstdokumentationen i [Nästa steg](#Next%20Steps).

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Lägga till flera tjänstslutpunkter i ett befintligt undernät

```azure-cli
az network vnet subnet update -g myRG -n mySubnet2 --vnet-name myVNet --service-endpoints Microsoft.Storage Microsoft.Sql
```

Förväntad utdata:
```
{
  "addressPrefix": "10.0.2.0/24",
  ...
  "name": "mySubnet2",
  ...
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    },
    {
      "locations": [
        "eastus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Sql"
    }
  ]
}
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Visa tjänstslutpunkter som har konfigurerats i ett undernät

```azure-cli
az network vnet subnet show -g myRG -n mySubnet --vnet-name myVNet
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Ta bort tjänstslutpunkter i ett undernät
```azure-cli
az network vnet subnet update -g myRG -n mySubnet --vnet-name myVNet --service-endpoints ""
```

Resultat: 
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": null
}
```

## <a name="resource-manager-template"></a>Resource Manager-mall

### <a name="securing-azure-service-resources-to-vnets"></a>Skydda Azure-tjänstresurser i virtuella nätverk

Du kan skydda specifika Azure-resurser i det virtuella nätverket via tjänstslutpunkter.

Hämta exemplet [Resource Manager-mall](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) för att skydda ett lagringskonto i ett undernät i ett virtuellt nätverk.

Mallen skapar ett virtuellt nätverk med två undernät, en virtuell dator med ett nätverkskort i varje undernät. Aktiverar slutpunkten på ett undernät och skyddar ett lagringskonto i det undernätet.

Du kan hämta mallen och ändra delar av den så att det passar din situation.

Anvisningar medföljer mallen för distribution av mallen med hjälp av Azure-portalen, PowerShell eller Azure CLI. Kontrollera att du har de [behörigheter](#provisioning) som krävs för att konfigurera slutpunkten och skydda kontot.

Skydda Azure-resurser i ett undernät:

- en tjänstslutpunkt ska vara konfigurerad i undernätet.
- resursen ska skyddas i det virtuella nätverket genom att lägga till en regel för virtuella nätverk på resursen.

### <a name="deleting-service-endpoints-with-resources-secured-to-the-subnet"></a>Ta bort tjänstslutpunkter med resurser som är skyddade i undernätet
Om Azure-tjänstresurser skyddas i undernätet och tjänstslutpunkten tas bort kan du inte längre komma åt resursen från undernätet.
Återaktivering av endast slutpunkten kommer inte att återställa åtkomst till resurser som tidigare var skyddade i undernätet.

Om du vill skydda tjänstresursen i det här undernätet igen måste du:

- aktivera slutpunkten igen
- ta bort den gamla regeln för det virtuella nätverket på resursen
- lägga till en ny regel som skyddar resursen i undernätet

## <a name="provisioning"></a>Etablering

Tjänstslutpunkter kan konfigureras på virtuella nätverk separat, av en användare med skrivbehörighet för virtuella nätverk.

För att kunna skydda Azure-tjänstresurser i ett virtuellt nätverk måste behörigheten "Microsoft.Network/JoinServicetoaSubnet" för undernäten läggas till för användaren. Den här behörigheten ingår som standard i de inbyggda tjänstadministratörsrollerna och kan ändras genom att skapa anpassade roller.

Lär dig mer om [inbyggda roller](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) och att tilldela specifika behörigheter till [anpassade roller](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Virtuella nätverk och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om de finns i olika prenumerationer bör resurserna finnas under samma Active Directory-klient (AD).

## <a name="next-steps"></a>Nästa steg

Mer anvisningar för att skydda tjänstresurser i virtuella nätverk finns via länkarna nedan:

[Skydda Azure Storage-konton på virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security)

[Skydda Azure SQL på virtuella nätverk](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)
