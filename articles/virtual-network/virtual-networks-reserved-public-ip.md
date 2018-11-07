---
title: Hantera Azure reserverade IP-adresser (klassisk) | Microsoft Docs
description: Förstå reserverade IP-adresser (klassisk) och hur du hanterar dem med hjälp av Azure PowerShell och Azure CLI.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: genli
ms.openlocfilehash: bd2b28a7f8d0a765e10ffa58b5a72b4bd5bc47b0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228189"
---
# <a name="reserved-ip-addresses-classic-deployment"></a>Reserverade IP-adresser (klassisk distribution)

 IP-adresser i Azure är indelade i två kategorier: dynamiska och reserverade. Offentliga IP-adresser som hanteras av Azure är dynamiska som standard. Det innebär att IP-adress används för en viss molntjänst (VIP) eller för att få åtkomst till en virtuell dator eller rollinstans direkt (ILPIP) kan ändras från tid till annan när resurser stängdes av eller Stoppad (frigjord).

Förhindra att IP-adresser ändras, kan du reservera en IP-adress. Reserverade IP-adresser kan endast användas som en VIP, se till att IP-adressen för Molntjänsten förblir detsamma även när resurser stängdes av eller Stoppad (frigjord). Du kan dessutom konvertera befintliga dynamiska IP-adresser används som en VIP-adress till en reserverad IP-adress.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig att reservera en statisk offentlig IP-adress med hjälp av den [Resource Manager-distributionsmodellen](virtual-network-ip-addresses-overview-arm.md).

Mer information om IP-adresser i Azure den [IP-adresser](virtual-network-ip-addresses-overview-classic.md) artikeln.

## <a name="when-do-i-need-a-reserved-ip"></a>När behöver jag en reserverad IP
* **Du vill säkerställa att den IP-Adressen är reserverad i din prenumeration**. Om du vill reservera en IP-adress som inte har släppts från prenumerationen under några omständigheter, bör du använda en reserverade offentliga IP-adress.  
* **Du vill att din IP-adress att hålla din molntjänst även över stoppas eller frigöras tillstånd (VM)**. Om du vill att din tjänst som kan nås med hjälp av en IP-adress ändras som inte, även när virtuella datorer i Molntjänsten stängdes av eller stoppa (frigjord).
* **Du vill kontrollera att utgående trafik från Azure använder en förutsägbar IP-adress**. Du kan ha den lokala brandväggen konfigurerade, så att endast trafik från specifika IP-adresser. Genom att reservera en IP-adress du vet källans IP-adress och behöver inte uppdatera brandväggsreglerna på grund av ett IP-ändring.

## <a name="faqs"></a>Vanliga frågor och svar
- Kan jag använda en reserverad IP-adress för alla Azure-tjänster?
    Nej. Reserverade IP-adresser kan endast användas för virtuella datorer och instans molntjänstroller exponeras via en VIP.
- Hur många reserverade IP-adresser kan jag ha?
    Mer information finns i [Azure-begränsningar](../azure-subscription-service-limits.md#networking-limits) artikeln.
- Finns det någon kostnad för reserverad IP-adresser?
    Ibland. Information om prissättning finns i den [reserverade IP-adress prisinformation om](https://go.microsoft.com/fwlink/?LinkID=398482) sidan.
- Hur jag för att reservera en IP-adress?
    Du kan använda PowerShell, den [Azure Management REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx), eller [Azure-portalen](https://portal.azure.com) att reservera en IP-adress i en Azure-region. En reserverad IP-adress är kopplad till din prenumeration.
- Kan jag använda en reserverad IP med tillhörighetsgrupp-baserade virtuella nätverk?
    Nej. Reserverade IP-adresser stöds endast i regionala virtuella nätverk. Reserverade IP-adresser stöds inte för virtuella nätverk som är associerade med tillhörighetsgrupper. Mer information om hur du kopplar ett virtuellt nätverk med en region eller tillhörighetsgrupp finns i den [om regionala virtuella nätverk och Tillhörighetsgrupper](virtual-networks-migrate-to-regional-vnet.md) artikeln.

## <a name="manage-reserved-vips"></a>Hantera reserverade virtuella IP-adresser

### <a name="using-azure-powershell-classic"></a>Med Azure PowerShell (klassisk)

Innan du kan använda reserverade IP-adresser, måste du lägga till den prenumerationen. Skapa en reserverad IP-adress från poolen med offentliga IP-adresser som är tillgängliga i den *centrala USA* plats på följande sätt:

> [!NOTE]
> För klassiska distributionsmodellen måste du installera Service Management-versionen av Azure PowerShell. Läs mer i informationen om hur du [installerar Azure PowerShell Service Management-modulen](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0). 

  ```powershell
    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
  ```
Observera att du inte kan ange vad IP som reserverats. Kör följande PowerShell-kommando om du vill visa vilka IP-adresser är reserverade i din prenumeration, och Lägg märke till värdena för *ReservedIPName* och *adress*:

```powershell
Get-AzureReservedIP
```

Förväntad utdata:

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

>[!NOTE]
>När du skapar en reserverad IP-adress med PowerShell kan ange du inte en resursgrupp för att skapa den reserverade IP-Adressen i. Azure platser som den i en resursgrupp med namnet *standard-nätverk* automatiskt. Om du skapar en reserverad IP med hjälp av den [Azure-portalen](http://portal.azure.com), du kan ange valfri resursgrupp som du väljer. Om du skapar den reserverade IP-Adressen i en resursgrupp än *standard-nätverk* dock när du refererar till den reserverade IP-Adressen med kommandon som `Get-AzureReservedIP` och `Remove-AzureReservedIP`, måste du referera till namnet  *Gruppera Resursgruppsnamn-reserverade ip-namn*.  Exempel: Om du skapar en reserverad IP med namnet *myReservedIP* i en resursgrupp med namnet *myResourceGroup*, måste du referera till namnet på den reserverade IP-Adressen som *grupp myResourceGroup myReservedIP*.   


När en IP-adress är reserverat fortfarande det kopplad till prenumerationen tills du tar bort den. Ta bort en reserverad IP-adress enligt följande:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

### <a name="using-azure-cli-classic"></a>Med Azure CLI (klassisk)
Skapa en reserverad IP-adress från poolen med offentliga IP-adresser som är tillgängliga i den *centrala USA* plats som använder Azure klassiskt CLI följer:

> [!NOTE]
> Du måste använda Azure klassiskt CLI för klassisk distribution. Information om hur du installerar Azure klassiskt CLI finns i [installera den klassiska Azure CLI](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest)
  
 Kommandot:
 
```azurecli
azure network reserved-ip create <name> <location>
```
Exempel:
 ```azurecli
 azure network reserved-ip create MyReservedIP centralus
 ```

Du kan visa vilka IP-adresser är reserverade i din prenumeration med Azure CLI på följande sätt: 

Kommandot:
```azurecli
azure network reserved-ip list
```
När en IP-adress är reserverat fortfarande det kopplad till prenumerationen tills du tar bort den. Ta bort en reserverad IP-adress enligt följande:

Kommandot:

 ```azurecli
 azure network reserved-ip delete <name>
 ```
  Exempel:  
 ```azurecli
 azure network reserved-ip delete MyReservedIP
 ```
## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Reserverad IP-adressen för en befintlig molntjänst
Du kan reservera IP-adressen för en befintlig molntjänst genom att lägga till den `-ServiceName` parametern. Reservera IP-adressen för en molntjänst *TestService* i den *centrala USA* plats på följande sätt:

- Med Azure PowerShell (klassisk):

  ```powershell
  New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
  ```

- Med Azure CLI (klassisk):
  
    Kommandot:

    ```azurecli
     azure network reserved-ip create <name> <location> -r <service-name> -d <deployment-name>
    ```
    Exempel:

    ```azurecli
      azure network reserved-ip create MyReservedIP centralus -r TestService -d asmtest8942
    ```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Associera en reserverad IP-adress till en ny molntjänst
Följande skript skapar en ny reserverad IP och sedan kopplar det till en ny molntjänst med namnet *TestService*.

### <a name="using-azure-powershell-classic"></a>Med Azure PowerShell (klassisk)
```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```
> [!NOTE]
> När du skapar en reserverad IP-adress ska användas med en molnbaserad tjänst kan du fortfarande referera till den virtuella datorn genom att använda *VIP:&lt;portnummer >* för inkommande kommunikation. Reservera en IP-adress betyder inte kan ansluta till den virtuella datorn direkt. Den reserverade IP-Adressen tilldelas till den molntjänst som den virtuella datorn har distribuerats till. Om du vill ansluta direkt till en virtuell dator efter IP måste du konfigurera en offentlig IP på instansnivå. En offentlig IP på instansnivå är en typ av offentlig IP-adress (kallas en ILPIP) som tilldelas direkt till den virtuella datorn. Det går inte att reservera. Mer information finns i [på instansnivå offentliga IP (ILPIP)](virtual-networks-instance-level-public-ip.md) artikeln.
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Ta bort en reserverad IP-adress från en distribution som körs

Ta bort en reserverad IP-adress som läggs till en ny molntjänst på följande sätt: 
### <a name="using-azure-powershell-classic"></a>Med Azure PowerShell (klassisk)

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

### <a name="using-azure-cli-classic"></a>Med Azure CLI (klassisk)
Kommandot:

```azurecli
azure network reserved-ip disassociate <name> <service-name> <deployment-name>
```

Exempel:

```azurecli
azure network reserved-ip disassociate MyReservedIP TestService asmtest8942
```

> [!NOTE]
> Ta bort en reserverad IP-adress från en distribution som körs tar inte bort reservationen från prenumerationen. Det helt enkelt Frigör IP-Adressen som ska användas av en annan resurs i din prenumeration.
> 

Om du vill ta bort en reserverad IP helt från en prenumeration, kör du följande kommando:

Kommandot:

```azurecli
azure network reserved-ip delete <name>
```
Exempel:

```azurecli
azure network reserved-ip delete MyReservedIP
```

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Associera en reserverad IP-adress till en distribution som körs

### <a name="using-azure-powershell-classic"></a>Med Azure PowerShell (klassisk)

Följande kommandon för att skapa en molntjänst med namnet *TestService2* med en ny virtuell dator med namnet *TestVM2*. Den befintliga reserverade IP med namnet *MyReservedIP* är sedan som är kopplad till Molntjänsten.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

### <a name="using-azure-cli-classic"></a>Med Azure CLI (klassisk)
Du kan associera en ny reserverad IP för att köra tjänsten molndistributionen med Azure CLI på följande sätt:

Kommandot:
```azurecli
azure network reserved-ip associate <name> <service-name> <deployment-name>
```
Exempel:
```azurecli
azure network reserved-ip associate MyReservedIP TestService asmtest8942
```
## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Associera en reserverad IP-adress till en molntjänst med hjälp av en tjänstkonfigurationsfil
Du kan även associera en reserverad IP-adress till en molntjänst med hjälp av en tjänstkonfigurationsfilen (CSCFG). Följande exempel xml visar hur du konfigurerar en molnbaserad tjänst för att använda en reserverade VIP-adress med namnet *MyReservedIP*:
```
   <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
```
## <a name="next-steps"></a>Nästa steg
* Förstå hur [IP-adressering](virtual-network-ip-addresses-overview-classic.md) fungerar i den klassiska distributionsmodellen.
* Lär dig mer om [reserverade privata IP-adresser](virtual-networks-reserved-private-ip.md).
* Lär dig mer om [instans på offentliga IP (ILPIP) adresser](virtual-networks-instance-level-public-ip.md).

