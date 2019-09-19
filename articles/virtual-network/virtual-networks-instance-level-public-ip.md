---
title: Offentliga IP-adresser (Classic) i Azure Instance level | Microsoft Docs
description: Förstå offentliga IP-adresser på instans nivå (ILPIP) och hur du hanterar dem med hjälp av PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: genli
ms.openlocfilehash: d92832d1eee995e8883dc6c8ed0f58c9755e40f8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058412"
---
# <a name="instance-level-public-ip-classic-overview"></a>Översikt över offentliga IP-adresser på instans nivå (klassisk)
En offentlig IP-adress (ILPIP) på instans nivå är en offentlig IP-adress som du kan tilldela direkt till en virtuell dator eller Cloud Services roll instans, i stället för den moln tjänst som din virtuella dator eller roll instans finns i. En ILPIP tar inte platsen för den virtuella IP-adressen (VIP) som är tilldelad till moln tjänsten. I stället är det en ytterligare IP-adress som du kan använda för att ansluta direkt till den virtuella datorn eller roll instansen.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser:  [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att du skapar virtuella datorer via Resource Manager. Se till att du förstår hur [IP-adresser](virtual-network-ip-addresses-overview-classic.md) fungerar i Azure.

![Skillnad mellan ILPIP och VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Som du ser i bild 1 nås moln tjänsten med hjälp av en VIP, medan de enskilda virtuella datorerna normalt används med VIP:&lt;port nummer.&gt; Genom att tilldela en ILPIP till en speciell virtuell dator kan den virtuella datorn nås direkt med den IP-adressen.

När du skapar en moln tjänst i Azure skapas motsvarande DNS-poster automatiskt för att tillåta åtkomst till tjänsten via ett fullständigt kvalificerat domän namn (FQDN) i stället för att använda den faktiska VIP: en. Samma process inträffar för en ILPIP, vilket ger åtkomst till den virtuella datorn eller roll instansen av FQDN i stället för ILPIP. Om du till exempel skapar en moln tjänst med namnet *contosoadservice*och du konfigurerar en webbroll med namnet *contosoweb* med två instanser, och i. cscfg `domainNameLabel` är inställt på *WebPublicIP*, registrerar Azure följande poster för pipe


* WebPublicIP.0.contosoadservice.cloudapp.net
* WebPublicIP.1.contosoadservice.cloudapp.net
* ...


> [!NOTE]
> Du kan endast tilldela en ILPIP för varje virtuell dator eller roll instans. Du kan använda upp till 5 ILPIPs per prenumeration. ILPIPs stöds inte för virtuella datorer med flera nätverkskort.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Varför skulle jag begära en ILPIP?
Om du vill kunna ansluta till din virtuella dator eller roll instans med en IP-adress som tilldelats direkt till den, i stället för att använda moln tjänstens&lt;VIP:&gt;port nummer, begär en ILPIP för din virtuella dator eller din roll instans.

* **Aktiv FTP** – genom att tilldela en ILPIP till en virtuell dator kan den ta emot trafik på alla portar. Slut punkter krävs inte för att den virtuella datorn ska ta emot trafik.  Se [Översikt över FTP-protokoll](https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview) för mer information om FTP-protokollet.
* **Utgående IP** -utgående trafik från den virtuella datorn mappas till ILPIP som källa och ILPIP identifierar den virtuella datorn till externa entiteter unikt.

> [!NOTE]
> Tidigare kallades en ILPIP-adress för en offentlig IP-adress (PIP).
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Hantera en ILPIP för en virtuell dator
Med följande uppgifter kan du skapa, tilldela och ta bort ILPIPs från virtuella datorer:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Så här begär du en ILPIP när du skapar virtuella datorer med hjälp av PowerShell
Följande PowerShell-skript skapar en moln tjänst med namnet *FTPService*, hämtar en avbildning från Azure, skapar en virtuell dator med namnet *FTPInstance* med hjälp av den hämtade avbildningen, anger att den virtuella datorn ska använda en ILPIP och lägger till den virtuella datorn i den nya tjänsten:

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
Om du vill ange ett annat lagrings konto som plats för den nya virtuella dator disken kan du använda parametern **MediaLocation** :

```powershell
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
     -MediaLocation https://management.core.windows.net/<SubscriptionID>/services/storageservices/<StorageAccountName> `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Hämta ILPIP-information för en virtuell dator
Om du vill visa ILPIP-information för den virtuella datorn som har skapats med föregående skript kör du följande PowerShell-kommando och visar värdena för *PublicIPAddress* och *PublicIPName*:

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

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Så här tar du bort en ILPIP från en virtuell dator
Om du vill ta bort ILPIP som har lagts till i den virtuella datorn i föregående skript kör du följande PowerShell-kommando:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Så här lägger du till en ILPIP i en befintlig virtuell dator
Om du vill lägga till en ILPIP till den virtuella datorn som skapats med skriptet tidigare kör du följande kommando:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Hantera en ILPIP för en Cloud Services roll instans

Utför följande steg för att lägga till en ILPIP till en Cloud Services roll instans:

1. Hämta. cscfg-filen för moln tjänsten genom att följa stegen i artikeln [så här konfigurerar du Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) .
2. Uppdatera. cscfg-filen genom att lägga `InstanceAddress` till elementet. Följande exempel lägger till en ILPIP med namnet *MyPublicIP* till en roll instans med namnet *WebRole1*: 

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
3. Överför. cscfg-filen för moln tjänsten genom att följa stegen i artikeln [så här konfigurerar du Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) .

### <a name="how-to-retrieve-ilpip-information-for-a-cloud-service"></a>Hämta ILPIP-information för en moln tjänst
Om du vill visa information om ILPIP per roll instans kör du följande PowerShell-kommando och observerar värdena för *PublicIPAddress*, *PublicIPName*, *PublicIPDomainNameLabel* och *PublicIPFqdns*:

```powershell
Add-AzureAccount

$roles = Get-AzureRole -ServiceName <Cloud Service Name> -Slot Production -RoleName WebRole1 -InstanceDetails

$roles[0].PublicIPAddress
$roles[1].PublicIPAddress
```

Du kan också använda `nslookup` för att fråga under domänens A-post:

```batch
nslookup WebPublicIP.0.<Cloud Service Name>.cloudapp.net
``` 

## <a name="next-steps"></a>Nästa steg
* Förstå hur [IP-adresser](virtual-network-ip-addresses-overview-classic.md) fungerar i den klassiska distributions modellen.
* Lär dig mer om [reserverade IP-adresser](virtual-networks-reserved-public-ip.md).
