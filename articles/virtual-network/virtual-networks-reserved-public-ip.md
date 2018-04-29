---
title: Hantera Azure reserverade IP-adresser (klassisk) - PowerShell | Microsoft Docs
description: Förstå reserverade IP-adresser (klassisk) och hur du hantera dem med hjälp av PowerShell.
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
ms.date: 02/10/2016
ms.author: genli
ms.openlocfilehash: 25fe3c5361ff58f8d62d5d083b7a69f517d2a267
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="reserved-ip-addresses-classic"></a>Den reserverade IP-adresser (klassisk)

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (klassisk)](virtual-networks-reserved-public-ip.md)

IP-adresser i Azure är indelade i två kategorier: dynamiska och reserverade. Offentliga IP-adresser som hanteras av Azure är dynamiska som standard. Det innebär att IP-adressen används för en viss molntjänst (VIP) eller för att få åtkomst till en virtuell dator eller rollinstans direkt (går) kan du ändra från tid till annan när resurserna är avstängd eller stoppats (frigjorts).

Du kan reservera en IP-adress för att förhindra att IP-adresser ändras. Reserverade IP-adresser kan endast användas som en VIP, se till att IP-adressen för Molntjänsten förblir detsamma, även när resurserna är avstängd eller stoppats (frigjorts). Dessutom kan du konvertera befintliga dynamiska IP-adresser används som en VIP-till-en reserverad IP-adress.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig att reservera en statisk offentlig IP-adress med hjälp av den [Resource Manager-distributionsmodellen](virtual-network-ip-addresses-overview-arm.md).

Om du vill veta mer om IP-adresser i Azure kan du läsa den [IP-adresser](virtual-network-ip-addresses-overview-classic.md) artikel.

## <a name="when-do-i-need-a-reserved-ip"></a>När behöver en reserverad IP?
* **Du vill se till att IP-Adressen är reserverad i din prenumeration**. Om du vill reservera en IP-adress som inte getts ut från prenumerationen under några omständigheter bör du använda en reserverade offentliga IP-adress.  
* **Du vill att behålla Molntjänsten även tvärs över stoppas eller frigöra tillstånd (VM) IP-**. Om du vill att tjänsten kan nås med hjälp av en IP-adress ändras som inte, även när virtuella datorer i Molntjänsten är avstängd eller stoppa (frigjorts).
* **Du vill kontrollera att utgående trafik från Azure använder en förutsägbar IP-adress**. Du kan ha lokala brandväggen konfigurerad för att endast tillåta trafik från särskilda IP-adresser. Genom att reservera en IP-adress du vet källans IP-adress och behöver inte uppdatera brandväggsreglerna på grund av en förändring av IP.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
1. Kan jag använda en reserverad IP-adress för alla Azure-tjänster? <br>
    Nej. Reserverade IP-adresser kan bara användas för virtuella datorer och instans molntjänstroller exponeras via en VIP.
2. Hur många reserverade IP-adresser kan ha? <br>
    Mer information finns i [Azure begränsar](../azure-subscription-service-limits.md#networking-limits) artikel.
3. Finns det en avgift för reserverade IP-adresser? <br>
    Ibland. Prisinformation, finns det [reserverade IP-adress prisinformation](http://go.microsoft.com/fwlink/?LinkID=398482) sidan.
4. Hur jag för att reservera en IP-adress? <br>
    Du kan använda PowerShell, den [Azure Management REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx), eller [Azure-portalen](https://portal.azure.com) att reservera en IP-adress i en Azure-region. En reserverad IP-adress är kopplad till din prenumeration.
5. Kan jag använda en reserverad IP med tillhörighetsgrupp-baserade Vnet? <br>
    Nej. Reserverade IP-adresser stöds endast i regionala virtuella nätverk. Reserverade IP-adresser stöds inte för Vnet som är associerade med tillhörighetsgrupper. Mer information om hur du kopplar ett VNet med en region eller affinitetsgrupp finns i [om Regional Vnet och Tillhörighetsgrupper](virtual-networks-migrate-to-regional-vnet.md) artikel.

## <a name="manage-reserved-vips"></a>Hantera reserverade virtuella IP-adresser

Se till att du har installerat och konfigurerat PowerShell genom att slutföra stegen i den [installerar och konfigurerar PowerShell](/powershell/azure/overview) artikel. 

Innan du kan använda reserverade IP-adresser, måste du lägga till den prenumerationen. Så här skapar du en reserverad IP-adress från poolen med offentliga IP-adresser som är tillgängliga i den *centrala USA* plats, kör du följande kommando:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
```

Observera att du kan ange vilka IP som reserverats. Kör följande PowerShell-kommando för att visa vilka IP-adresser är reserverade i din prenumeration, och Lägg märke till värdena för *ReservedIPName* och *adressen*:

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
>När du skapar en reserverad IP-adress med PowerShell kan ange du inte en resursgrupp för att skapa den reserverade IP-Adressen i. Azure platser till en resursgrupp med namnet *standard-nätverk* automatiskt. Om du skapar en reserverad IP med hjälp av den [Azure-portalen](http://portal.azure.com), du kan ange valfri resursgrupp som du väljer. Om du skapar den reserverade IP: N i en resursgrupp än *standard-nätverk* dock när du refererar till den reserverade IP: N med kommandon som `Get-AzureReservedIP` och `Remove-AzureReservedIP`, måste du referera till namnet *gruppnamn resursgruppnamn-reserverad-ip*.  Till exempel om du skapar en reserverad IP med namnet *myReservedIP* i en resursgrupp med namnet *myResourceGroup*, måste du referera till namnet på den reserverade IP-Adressen som *grupp myResourceGroup myReservedIP*.   

När en IP-adress är reserverat fortfarande det kopplad till prenumerationen förrän du tar bort den. Kör följande PowerShell-kommando för att ta bort den reserverade IP:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Reserverad IP-adressen för en befintlig molntjänst
Du kan reservera IP-adressen för en befintlig molntjänst genom att lägga till den `-ServiceName` parameter. Att reservera IP-adressen för en molnbaserad tjänst *TestService* i den *centrala USA* plats, kör följande PowerShell-kommando:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Associera en reserverad IP-adress till en ny molntjänst
Följande skript skapar en ny reserverad IP-adress och sedan kopplar den till en ny molntjänst med namnet *TestService*.

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```

> [!NOTE]
> När du skapar en reserverad IP-adress ska användas med en molnbaserad tjänst kan du fortfarande hänvisar till den virtuella datorn med *VIP:&lt;portnummer >* för inkommande kommunikation. Reservera en IP-innebär inte kan ansluta till den virtuella datorn direkt. Den reserverade IP: N har tilldelats den molntjänst som den virtuella datorn har distribuerats till. Om du vill ansluta direkt till en virtuell dator efter IP måste du konfigurera en offentlig IP på instansnivå. En offentlig IP på instansnivå är en typ av offentlig IP-adress (kallas en går) som tilldelas direkt till den virtuella datorn. Det går inte att reservera. Mer information finns i [instansnivå offentliga IP-går](virtual-networks-instance-level-public-ip.md) artikel.
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Ta bort en reserverad IP-adress från en aktiv distribution
Om du vill ta bort en reserverad IP-adress till en ny molntjänst, kör du följande PowerShell-kommando:

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

> [!NOTE]
> Om du tar bort en reserverad IP-adress från en aktiv distribution tas inte bort reservationen från prenumerationen. Den helt enkelt Frigör IP-Adressen som ska användas av en annan resurs i din prenumeration.
> 

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Associera en reserverad IP-adress till en aktiv distribution
Följande kommandon skapar en molntjänst med namnet *TestService2* med en ny virtuell dator med namnet *TestVM2*. Befintlig reserverad IP med namnet *MyReservedIP* är sedan som är kopplade till Molntjänsten.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Associera en reserverad IP-adress till en molntjänst med hjälp av en konfigurationsfil för tjänsten
Du kan även associera en reserverad IP-adress till en molntjänst med hjälp av en tjänst-konfigurationsfil (CSCFG). XML-koden för följande exempel visar hur du konfigurerar en molnbaserad tjänst om du vill använda ett reserverat VIP med namnet *MyReservedIP*:

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

## <a name="next-steps"></a>Nästa steg
* Förstå hur [IP-adressering](virtual-network-ip-addresses-overview-classic.md) fungerar i den klassiska distributionsmodellen.
* Lär dig mer om [reserverade privata IP-adresser](virtual-networks-reserved-private-ip.md).
* Lär dig mer om [instans nivå offentliga IP-går adresser](virtual-networks-instance-level-public-ip.md).

