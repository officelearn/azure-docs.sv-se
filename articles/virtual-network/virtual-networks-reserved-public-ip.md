---
title: Hantera reserverade IP-adresser i Azure (klassisk) | Microsoft Docs
description: Förstå reserverade IP-adresser (klassisk) och hur du hanterar dem med hjälp av Azure PowerShell och Azure CLI.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: genli
ms.openlocfilehash: 5bcbb8c6845928cf21b5b6a1c04f609305bd7983
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979534"
---
# <a name="reserved-ip-addresses-classic-deployment"></a>Reserverad IP adresser (klassisk distribution)

 IP-adresser i Azure hamnar i två kategorier: dynamiska och reserverade. Offentliga IP-adresser som hanteras av Azure är dynamiska som standard. Det innebär att den IP-adress som används för en specifik moln tjänst (VIP) eller åtkomst till en virtuell dator eller roll instans direkt (ILPIP) kan ändras från tiden till en gång, när resurser stängs av eller stoppas (frigörs).

För att förhindra att IP-adresser ändras kan du reservera en IP-adress. Reserverade IP-adresser kan endast användas som VIP-adresser, vilket säkerställer att moln tjänstens IP-adress förblir densamma, även om resurserna stängs av eller stoppas (frigörs). Du kan dessutom konvertera befintliga dynamiska IP-adresser som används som VIP till en reserverad IP-adress.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig hur du reserverar en statisk offentlig IP-adress med hjälp av [distributions modellen för Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Läs mer om IP-adresser i Azure i artikeln [IP-adresser](virtual-network-ip-addresses-overview-classic.md) .

## <a name="when-do-i-need-a-reserved-ip"></a>När behöver jag en reserverad IP-adress?
* **Du vill se till att IP-adressen är reserverad i din prenumeration**. Om du vill reservera en IP-adress som inte har släppts från din prenumeration under någon omständighet bör du använda en reserverad offentlig IP-adress.  
* **Du vill att din IP-adress ska hållas kvar med moln tjänsten även om den har stoppats eller frigjorts (VM)** . Om du vill att tjänsten ska nås med en IP-adress som inte ändras, även när virtuella datorer i moln tjänsten stängs av eller stoppas (frigörs).
* **Du vill säkerställa att utgående trafik från Azure använder en förutsägbar IP-adress**. Du kanske har konfigurerat den lokala brand väggen så att den endast tillåter trafik från vissa IP-adresser. Genom att reservera en IP-adress känner du till Källans IP-adress och behöver inte uppdatera brand Väggs reglerna på grund av en IP-förändring.

## <a name="faqs"></a>Vanliga frågor och svar
- Kan jag använda en reserverad IP-adress för alla Azure-tjänster?
    Nej. Reserverade IP-adresser kan endast användas för virtuella datorer och moln tjänst instans roller som exponeras via en VIP.
- Hur många reserverade IP-adresser kan jag ha?
    Mer information finns i artikeln om [Azure-begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) .
- Debiteras du för reserverade IP-adresser?
    Omständigheter. Pris information finns på sidan med [pris information för reserverad IP-adressen](https://go.microsoft.com/fwlink/?LinkID=398482) .
- Hur gör jag för att reservera en IP-adress?
    Du kan använda PowerShell, [Azures hanterings REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)eller [Azure Portal](https://portal.azure.com) för att reservera en IP-adress i en Azure-region. En reserverad IP-adress är kopplad till din prenumeration.
- Kan jag använda en reserverad IP med tillhörighets grupp baserad virtuella nätverk?
    Nej. Reserverade IP-adresser stöds bara i regionala virtuella nätverk. Reserverade IP-adresser stöds inte för virtuella nätverk som är associerade med tillhörighets grupper. Mer information om hur du kopplar ett VNet med en region eller tillhörighets grupp finns i artikeln [om regional virtuella nätverk och tillhörighets grupper](virtual-networks-migrate-to-regional-vnet.md) .

## <a name="manage-reserved-vips"></a>Hantera reserverade virtuella IP-adresser

### <a name="using-azure-powershell-classic"></a>Använda Azure PowerShell (klassisk)

Innan du kan använda reserverade IP-adresser måste du lägga till det i din prenumeration. Skapa en reserverad IP-adress från poolen med offentliga IP-adresser som är tillgängliga på den *centrala amerikanska* platsen enligt följande:

> [!NOTE]
> För den klassiska distributions modellen måste du installera Service Management-versionen av Azure PowerShell. Läs mer i informationen om hur du [installerar Azure PowerShell Service Management-modulen](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0). 

  ```powershell
    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
  ```
Observera dock att du inte kan ange vilken IP-adress som ska reserveras. Om du vill se vilka IP-adresser som är reserverade i din prenumeration kör du följande PowerShell-kommando och märker värdena för *ReservedIPName* och *address*:

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
>När du skapar en reserverad IP-adress med PowerShell kan du inte ange en resurs grupp för att skapa den reserverade IP-adressen i. Azure placerar det i en resurs grupp med namnet *standard-nätverk* automatiskt. Om du skapar den reserverade IP-adressen med [Azure Portal](https://portal.azure.com)kan du ange vilken resurs grupp du väljer. Om du skapar en reserverad IP-adress i en annan resurs grupp än *standard nätverk* men när du refererar till den reserverade IP-adressen med kommandon som `Get-AzureReservedIP` och `Remove-AzureReservedIP`, måste du referera till namn *gruppen resurs-Group-name reservered-IP-Name*.  Om du till exempel skapar en reserverad IP-adress med namnet *myReservedIP* i en resurs grupp med namnet *myResourceGroup*, måste du referera till namnet på den reserverade IP-adressen som *Group myResourceGroup myReservedIP*.   


När en IP-adress har reserver ATS förblir den kopplad till din prenumeration tills du tar bort den. Ta bort en reserverad IP-adress enligt följande:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

### <a name="using-azure-cli-classic"></a>Använda Azure CLI (klassisk)
Skapa en reserverad IP-adress från poolen med offentliga IP-adresser som är tillgängliga på den *centrala amerikanska* platsen som att använda den klassiska Azure-CLI:

> [!NOTE]
> För klassisk distribution måste du använda Azure klassisk CLI. Information om hur du installerar den klassiska Azure CLI finns i [installera den klassiska Azure-CLI: en](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest)
  
 Kommando:
 
```azurecli
azure network reserved-ip create <name> <location>
```
Exempel:
 ```azurecli
 azure network reserved-ip create MyReservedIP centralus
 ```

Du kan se vilka IP-adresser som är reserverade i din prenumeration med Azure CLI på följande sätt: 

Kommando:
```azurecli
azure network reserved-ip list
```
När en IP-adress har reserver ATS förblir den kopplad till din prenumeration tills du tar bort den. Ta bort en reserverad IP-adress enligt följande:

Kommando:

 ```azurecli
 azure network reserved-ip delete <name>
 ```
  Exempel:  
 ```azurecli
 azure network reserved-ip delete MyReservedIP
 ```
## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Reservera IP-adressen för en befintlig moln tjänst
Du kan reservera IP-adressen för en befintlig moln tjänst genom att lägga till parametern `-ServiceName`. Reservera IP-adressen för en moln tjänst *TestService* på den *centrala amerikanska* platsen enligt följande:

- Använda Azure PowerShell (klassisk):

  ```powershell
  New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
  ```

- Använda Azure CLI (klassisk):
  
    Kommando:

    ```azurecli
     azure network reserved-ip create <name> <location> -r <service-name> -d <deployment-name>
    ```
    Exempel:

    ```azurecli
      azure network reserved-ip create MyReservedIP centralus -r TestService -d asmtest8942
    ```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Koppla en reserverad IP-adress till en ny moln tjänst
Följande skript skapar en ny reserverad IP och kopplar den sedan till en ny moln tjänst med namnet *TestService*.

### <a name="using-azure-powershell-classic"></a>Använda Azure PowerShell (klassisk)
```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```
> [!NOTE]
> När du skapar en reserverad IP-adress som ska användas med en moln tjänst refererar du fortfarande till den virtuella datorn med hjälp av *VIP:&lt;port number >* för inkommande kommunikation. Att reservera en IP-adress innebär inte att du kan ansluta till den virtuella datorn direkt. Den reserverade IP-adressen är tilldelad till den moln tjänst som den virtuella datorn har distribuerats till. Om du vill ansluta till en virtuell dator via IP direkt måste du konfigurera en offentlig IP-adress på instans nivå. En offentlig IP-adress på instans nivå är en typ av offentlig IP-adress (kallas en ILPIP) som tilldelas direkt till den virtuella datorn. Den kan inte reserveras. Mer information finns i artikeln [offentlig IP på instansnivå (ILPIP)](virtual-networks-instance-level-public-ip.md) .
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Ta bort en reserverad IP-adress från en pågående distribution

Ta bort en reserverad IP-adress som lagts till i en ny moln tjänst enligt följande: 
### <a name="using-azure-powershell-classic"></a>Använda Azure PowerShell (klassisk)

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

### <a name="using-azure-cli-classic"></a>Använda Azure CLI (klassisk)
Kommando:

```azurecli
azure network reserved-ip disassociate <name> <service-name> <deployment-name>
```

Exempel:

```azurecli
azure network reserved-ip disassociate MyReservedIP TestService asmtest8942
```

> [!NOTE]
> Att ta bort en reserverad IP-adress från en pågående distribution tar inte bort reservationen från din prenumeration. Den frigör bara IP-adressen som ska användas av en annan resurs i din prenumeration.
> 

Om du vill ta bort en reserverad IP-adress helt från en prenumeration kör du följande kommando:

Kommando:

```azurecli
azure network reserved-ip delete <name>
```
Exempel:

```azurecli
azure network reserved-ip delete MyReservedIP
```

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Koppla en reserverad IP-adress till en distribution som körs

### <a name="using-azure-powershell-classic"></a>Använda Azure PowerShell (klassisk)

Följande kommandon skapar en moln tjänst med namnet *TestService2* med en ny virtuell dator med namnet *TestVM2*. Den befintliga reserverade IP-adressen som heter *MyReservedIP* är sedan kopplad till moln tjänsten.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

### <a name="using-azure-cli-classic"></a>Använda Azure CLI (klassisk)
Du kan associera en ny reserverad IP-adress till moln tjänst distributionen som körs med hjälp av Azure CLI enligt följande:

Kommando:
```azurecli
azure network reserved-ip associate <name> <service-name> <deployment-name>
```
Exempel:
```azurecli
azure network reserved-ip associate MyReservedIP TestService asmtest8942
```
## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Koppla en reserverad IP-adress till en moln tjänst med hjälp av en tjänst konfigurations fil
Du kan också associera en reserverad IP-adress till en moln tjänst genom att använda en tjänst konfigurations fil (CSCFG). Följande XML-exempel visar hur du konfigurerar en moln tjänst för att använda en reserverad VIP med namnet *MyReservedIP*:
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
* Förstå hur [IP-adresser](virtual-network-ip-addresses-overview-classic.md) fungerar i den klassiska distributions modellen.
* Läs mer om [reserverade privata IP-adresser](virtual-networks-reserved-private-ip.md).
* Lär dig mer om [offentliga IP-adresser på instans nivå (ILPIP)](virtual-networks-instance-level-public-ip.md).

