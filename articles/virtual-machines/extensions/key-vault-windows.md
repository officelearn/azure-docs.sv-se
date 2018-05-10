---
title: Nyckeln valvet VM-tillägget för Windows | Microsoft Docs
description: Distribuera en agent som utför automatisk uppdatering av Key Vault-hemligheter på virtuella datorer med ett tillägg för virtuell dator.
services: virtual-machines-windows
documentationcenter: ''
author: dragav
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: dragosav
ms.openlocfilehash: 19e177f086ea9fa817a36e67ace77aed39ee89b5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Key Vault tillägg för virtuell dator för Windows

## <a name="overview"></a>Översikt

Key Vault VM-tillägg ger automatisk uppdatering av hemligheter som lagras i en Azure key vault. Mer specifikt tillägget övervakarna en lista över observerats certifikat som lagras i nyckelvalv och, vid identifiering av en ändring, hämtar och installerar motsvarande certifikat. Key Vault VM-tillägget publiceras och stöds av Microsoft för närvarande på virtuella Windows-datorer. Det här dokumentet beskriver de plattformar som stöds, konfigurationer och distributionsalternativ för Key Vault VM-tillägget för Windows. 

## <a name="prerequisites"></a>Förutsättningar

Key Vault VM-tillägget beror på hanterade tjänsten identitet VM-tillägget för att kunna autentisera sig Key Vault-tjänsten. Se dokumentationen för MSI VM-tillägg för mer information.

### <a name="operating-system"></a>Operativsystem

Key Vault VM-tillägg stöder för närvarande alla versioner av Windows.

| Distribution | Version |
|---|---|
| Windows | Kärna |

### <a name="internet-connectivity"></a>Internetanslutning

Key Vault VM-tillägget för Windows kräver att den virtuella måldatorn är ansluten till internet. 

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för Key Vault VM-tillägget. Tillägget måste inte skyddade inställningarna - information utan Säkerhetspåverkan anses vara dess inställningar. Tillägget kräver en lista med övervakade hemligheter och Frågefrekvens för avsökningsfrekvens certifikatarkivet mål. Närmare bestämt:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2016-10-01 | datum |
| Publisher | Microsoft.Azure.KeyVault.Edp | sträng |
| typ | KeyVaultForWindows | sträng |
| typeHandlerVersion | 0.0 | int |
| pollingIntervalInS | 3600 | int |
| Certifikatarkiv | MIN | sträng |
| certificateStoreLocation  | LOCAL_MACHINE | sträng |
| observedCertificates  | ["https://myvault.vault.azure.net:443/secrets/mycertificate"] | Strängmatris


## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Mallar är perfekt när du distribuerar en eller flera virtuella datorer som kräver efter distributionen uppdatera av certifikat. Tillägget kan distribueras till enskilda virtuella datorer eller VM skala anger. Schemat och konfigurationen är gemensamma för både typer. 

JSON-konfiguration för ett tillägg för virtuell dator måste vara kapslad inuti virtuella resource fragment av mallen, särskilt `"resources": []` objekt av den virtuella datorn.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

## <a name="azure-powershell-deployment"></a>Azure PowerShell-distribution

Azure PowerShell kan användas för att distribuera Key Vault VM-tillägget till en befintlig virtuell dator eller virtuella datorns skaluppsättning. 

* Så här distribuerar tillägg på en virtuell dator:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMName> + '/KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Set-AzureRmVmExtension -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Så här distribuerar tillägg på en skalningsuppsättning, till exempel som en del av ett Service Fabric-kluster:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMSSName> + '_KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Add-AzureRmVmssExtension -VirtualMachineScaleSet <VMSS> -Name $extName -Publisher $extPublisher -Type $extType -Setting $settings
    
    ```

Tänk på följande begränsningar/krav:
- Distributionen måste göras i region södra centrala USA
- Key Vault begränsningar:
    - Det måste finnas vid tidpunkten för distributionen 
    - Måste finnas i samma region och resursgruppen som distributionen
    - Måste vara aktiverat för distribution och för malldistribution

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Exempel för distribution tangent valvet VM-tillägget i Azure CLI kommer snart att vara tillgänglig. 

## <a name="troubleshoot-and-support"></a>Felsöka och stöd

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och genom att använda Azure PowerShell. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando med hjälp av Azure PowerShell.

```powershell
Get-AzureRMVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

Tillägget utförande-utdatan loggas till följande fil:

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.Edp.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välja Get support. Information om hur du använder Azure stöder finns i [vanliga frågor om Microsoft Azure-supporten](https://azure.microsoft.com/support/faq/).
