---
title: "Azure instansnivå offentlig IP-adress (klassisk)-adresser | Microsoft Docs"
description: "Förstå instans nivån offentliga IP-går adresser och hantera dem med hjälp av PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
ms.openlocfilehash: 773043f2841ec7539b0d49357dec6bcb9f4f78a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="instance-level-public-ip-classic-overview"></a>Instansen offentlig IP (klassisk): översikt
En instans nivån offentliga IP-går är en offentlig IP-adress som kan tilldelas direkt till en virtuell dator eller molntjänster rollinstans i stället för till Molntjänsten som din Virtuella eller roll instans finns i. En går äga inte rum för den virtuella IP (VIP) som är tilldelad till Molntjänsten. Det är en ytterligare IP-adress som du kan använda för att ansluta direkt till din Virtuella eller roll-instans.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att skapa virtuella datorer via Resource Manager. Kontrollera att du förstår hur [IP-adresser](virtual-network-ip-addresses-overview-classic.md) fungerar i Azure.

![Skillnaden mellan att går och VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

I bild 1 visas Molntjänsten används med en VIP samtidigt som de enskilda virtuella datorerna används normalt med VIP:&lt;portnummer&gt;. Genom att tilldela en går till en specifik virtuell dator kan kan den virtuella datorn nås direkt med den IP-adressen.

När du skapar en molnbaserad tjänst i Azure skapas motsvarande DNS A-poster automatiskt för att tillåta åtkomst till tjänsten via ett fullständigt kvalificerat domännamn (FQDN), i stället för verkliga VIP. Samma process som sker för en går att tillåta åtkomst till virtuell dator eller roll-instansen av FQDN i stället för i går. Till exempel om du skapar en molntjänst med namnet *contosoadservice*, och du konfigurerar en webbroll med namnet *contosoweb* med två instanser Azure registrerar följande A-poster för instanserna:

* contosoweb\_IN_0.contosoadservice.cloudapp.net
* contosoweb\_IN_1.contosoadservice.cloudapp.net 

> [!NOTE]
> Du kan tilldela en enda går för varje virtuell dator eller roll-instans. Du kan använda upp till 5 ILPIPs per prenumeration. ILPIPs stöds inte för virtuella datorer som flera nätverkskort.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Varför skulle jag för att begära en går?
Om du vill kunna ansluta till din Virtuella eller roll-instans med en IP-adress som tilldelats till den i stället för att använda molnet tjänsten VIP:&lt;portnummer&gt;, begär en går för din virtuella dator eller din rollinstans.

* **Aktiva FTP** -genom att tilldela en går till en virtuell dator kan den ta emot trafik på alla portar. Slutpunkter krävs inte för den virtuella datorn tar emot trafik.  Information om FTP-protokollet finns i (https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview) [FTP-Protokollöversikt].
* **Utgående IP** – utgående trafik från den virtuella datorn är mappad till går som källa och går identifierar den virtuella datorn till externa enheter.

> [!NOTE]
> Tidigare som går-adress kallas en offentlig IP (PIP)-adress.
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Hantera en går för en virtuell dator
Följande aktiviteter kan du skapa, tilldela och ta bort ILPIPs från virtuella datorer:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Hur du begär ett går under Skapa en virtuell dator med hjälp av PowerShell
Följande PowerShell-skript skapar en molntjänst med namnet *FTPService*, hämtar en avbildning från Azure, skapar en virtuell dator med namnet *FTPInstance* använder den hämtade avbildningen anger den virtuella datorn att använda en går och lägger till den virtuella datorn till den nya tjänsten:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"} `
New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Hur du hämtar går information för en virtuell dator
Kör följande PowerShell-kommando för att visa går informationen för den virtuella datorn som skapats med föregående skript, och notera att värdena för *PublicIPAddress* och *PublicIPName*:

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

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Ta bort en går från en virtuell dator
Kör följande PowerShell-kommando för att ta bort går till den virtuella datorn i föregående skript:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Hur du lägger till en går till en befintlig virtuell dator
Om du vill lägga till en går till den virtuella datorn skapas med hjälp av skript som tidigare, kör du följande kommando:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Hantera en går för en roll Cloud Services-instans

Utför följande steg för att lägga till en går till en roll Cloud Services-instans:

1. Hämta .cscfg-filen för Molntjänsten genom att slutföra stegen i den [hur du konfigurerar molntjänster](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) artikel.
2. Uppdatera .cscfg-filen genom att lägga till den `InstanceAddress` element. I följande exempel lägger till en går med namnet *MyPublicIP* till en roll med namnet *WebRole1*: 

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
          <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. Överför .cscfg-filen för Molntjänsten genom att slutföra stegen i den [hur du konfigurerar molntjänster](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) artikel.

## <a name="next-steps"></a>Nästa steg
* Förstå hur [IP-adressering](virtual-network-ip-addresses-overview-classic.md) fungerar i den klassiska distributionsmodellen.
* Lär dig mer om [reserverade IP-adresser](virtual-networks-reserved-public-ip.md).
