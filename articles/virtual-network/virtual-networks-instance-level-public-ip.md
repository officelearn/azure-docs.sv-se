---
title: Azure Instance-level (klassisk) för offentliga IP-adresser | Microsoft Docs
description: Förstå instans på offentliga IP (ILPIP)-adresser och hur du hanterar dem med hjälp av PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: genli
ms.openlocfilehash: 2f6db23e02c836dea6d640757d12275b654ad468
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024638"
---
# <a name="instance-level-public-ip-classic-overview"></a>Instans offentliga IP (klassisk) översikt
En instans på offentliga IP (ILPIP) är en offentlig IP-adress som du kan tilldela direkt till en virtuell dator eller Cloud Services-rollinstans i stället för till Molntjänsten som din instans av virtuell dator eller rollen finns i. En ILPIP äga inte rum för den virtuella IP (VIP) som är tilldelad till din molntjänst. Det är snarare ytterligare IP-adress som du kan använda för att ansluta direkt till din instans av virtuell dator eller roll.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att du skapar virtuella datorer via Resource Manager. Kontrollera att du förstår hur [IP-adresser](virtual-network-ip-addresses-overview-classic.md) arbets i Azure.

![Skillnaden mellan ILPIP och VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

I bild 1 visas Molntjänsten får åtkomst till med en VIP medan enskilda virtuella datorer används vanligtvis med VIP:&lt;portnummer&gt;. Genom att tilldela en ILPIP till en specifik virtuell dator, kan den virtuella datorn nås direkt med hjälp av IP-adress.

När du skapar en molnbaserad tjänst i Azure, skapas motsvarande DNS A-poster automatiskt för att tillåta åtkomst till tjänsten via ett fullständigt kvalificerat domännamn (FQDN), istället för att använda faktiska VIP. Samma process som sker för en ILPIP, att tillåta åtkomst till den virtuella dator eller rollinstansen instansen efter FQDN i stället för ILPIP. Till exempel om du skapar en molntjänst med namnet *contosoadservice*, och du konfigurerar en webbroll med namnet *contosoweb* med två instanser och i .cscfg `domainNameLabel` är inställd på  *WebPublicIP*Azure registrerar följande A registrerar för instanser:


* WebPublicIP.0.contosoadservice.cloudapp.net
* WebPublicIP.1.contosoadservice.cloudapp.net
* ...


> [!NOTE]
> Du kan tilldela endast en ILPIP för varje virtuell dator eller roll-instans. Du kan använda upp till 5 ILPIPs per prenumeration. ILPIPs stöds inte för virtuella datorer med flera nätverkskort.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Varför skulle jag för att begära en ILPIP?
Om du vill kunna ansluta till din virtuella datorn eller rollinstansen instans av en IP-adress som tilldelats till den direkt i stället för molnet tjänsten VIP:&lt;portnummer&gt;, begär en ILPIP för den virtuella datorn eller rollinstansen.

* **Aktiva FTP** -genom att tilldela en ILPIP till en virtuell dator kan den ta emot trafik på alla portar. Slutpunkter krävs inte för den virtuella datorn ska ta emot trafik.  Se [FTP-Protokollöversikt](https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview) mer information om FTP-protokollet.
* **Utgående IP** – utgående trafik från den virtuella datorn har mappats till ILPIP som källa och ILPIP unikt identifierar den virtuella datorn till externa enheter.

> [!NOTE]
> Tidigare var som en ILPIP-adress kallas en offentlig adress IP (PIP).
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Hantera en ILPIP för en virtuell dator
Följande aktiviteter kan du skapa, tilldela och ta bort ILPIPs från virtuella datorer:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Hur du begär en ILPIP under skapandet av VM med hjälp av PowerShell
Följande PowerShell-skript skapar en molntjänst med namnet *FTPService*, hämtar du en avbildning från Azure, skapar en virtuell dator med namnet *FTPInstance* med den hämtade avbildningen anger den virtuella datorn att använda en ILPIP och lägger till den virtuella datorn till den nya tjänsten:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

#Set "current" storage account for the subscription. It will be used as the location of new VM disk

Set-AzureSubscription -SubscriptionName <SubName> -CurrentStorageAccountName <StorageAccountName>

#Create a new VM configuration object

New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

```
Om du vill ange ett annat lagringskonto som platsen för den nya Virtuella disken kan du använda **MediaLocation** parameter:

```powershell
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
     -MediaLocation https://management.core.windows.net/<SubscriptionID>/services/storageservices/<StorageAccountName> `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Hur du hämtar ILPIP information för en virtuell dator
Kör följande PowerShell-kommando för att visa ILPIP-information för den virtuella datorn skapas med föregående skript, och notera värdena för *PublicIPAddress* och *PublicIPName*:

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

Förväntad utdata:
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Ta bort en ILPIP från en virtuell dator
Kör följande PowerShell-kommando för att ta bort ILPIP som lagts till i den virtuella datorn i föregående skript:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Lägga till en ILPIP i en befintlig virtuell dator
Kör följande kommando för att lägga till en ILPIP till den virtuella datorn skapas med hjälp av föregående skript:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Hantera en ILPIP för en rollinstans för Cloud Services

Om du vill lägga till en ILPIP i en rollinstans för Cloud Services, gör du följande:

1. Ladda ned .cscfg-filen för Molntjänsten genom att följa stegen i den [hur du konfigurerar Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) artikeln.
2. Uppdatera .cscfg-filen genom att lägga till den `InstanceAddress` element. I följande exempel lägger till en ILPIP med namnet *MyPublicIP* en rollinstans med namnet *WebRole1*: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ILPIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
          <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
          </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <InstanceAddress roleName="WebRole1">
        <PublicIPs>
          <PublicIP name="MyPublicIP" domainNameLabel="WebPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. Ladda upp .cscfg-filen för Molntjänsten genom att följa stegen i den [hur du konfigurerar Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) artikeln.

### <a name="how-to-retrieve-ilpip-information-for-a-cloud-service"></a>Hur du hämtar ILPIP information för en molntjänst
Kör följande PowerShell-kommando för att visa ILPIP information per rollinstans, och notera värdena för *PublicIPAddress*, *PublicIPName*, *PublicIPDomainNameLabel* och *PublicIPFqdns*:

```powershell
Add-AzureAccount

$roles = Get-AzureRole -ServiceName <Cloud Service Name> -Slot Production -RoleName WebRole1 -InstanceDetails

$roles[0].PublicIPAddress
$roles[1].PublicIPAddress
```

Du kan också använda `nslookup` att fråga underdomän är en post:

```batch
nslookup WebPublicIP.0.<Cloud Service Name>.cloudapp.net
``` 

## <a name="next-steps"></a>Nästa steg
* Förstå hur [IP-adressering](virtual-network-ip-addresses-overview-classic.md) fungerar i den klassiska distributionsmodellen.
* Lär dig mer om [reserverade IP-adresser](virtual-networks-reserved-public-ip.md).
