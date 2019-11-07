---
title: Azure Key Vault VM-tillägg för Linux | Microsoft Docs
description: Distribuera en agent som utför automatisk uppdatering av Key Vault certifikat på virtuella datorer med ett tillägg för virtuell dator.
services: virtual-machines-linux
author: msmbaldwin
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 09/23/2018
ms.author: mbaldwin
ms.openlocfilehash: 5991d3d2197822b239b946de66f020dd258f835a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584371"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Key Vault tillägg för virtuell dator för Linux

Key Vault VM-tillägget ger automatisk uppdatering av certifikat som lagras i ett Azure Key Vault. Mer specifikt övervakar tillägget en lista över observerade certifikat som lagras i nyckel valv.  Vid identifiering av en ändring hämtas och installeras motsvarande certifikat i omfattningen. Key Vault VM-tillägget publiceras och stöds av Microsoft, för närvarande på virtuella Linux-datorer. Det här dokumentet innehåller information om plattformar, konfigurationer och distributions alternativ som stöds för Key Vault VM-tillägget för Linux. 

### <a name="operating-system"></a>Operativsystem

Key Vault VM-tillägget stöder dessa Linux-distributioner:

- Ubuntu – 1604
- Ubuntu – 1804
- Debian – 9
- SUSE-15 

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för Key Vault VM-tillägget. Tillägget kräver inte skyddade inställningar. alla dess inställningar betraktas som information utan att säkerheten påverkas. Tillägget kräver en lista över övervakade hemligheter, avsöknings frekvens och mål certifikat arkivet. Mer specifikt:  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForLinux",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "linkOnRenewal": <Not available on Linux e.g.: false>,
                    "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
                    "requireInitialSync": <initial synchronization of certificates e..g: true>,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
                }         
            }
      }
    }
```

> [!NOTE]
> URL: er för dina observerade certifikat bör ha formatet `https://myVaultName.vault.azure.net/secrets/myCertName`.
> 
> Detta beror på att `/secrets` sökvägen returnerar det fullständiga certifikatet, inklusive den privata nyckeln, medan `/certificates` Sök vägen inte gör det. Mer information om certifikat hittar du här: [Key Vault certifikat](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)


### <a name="property-values"></a>Egenskaps värden

| Namn | Värde/exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | datum |
| Förläggare | Microsoft. Azure. EdP | sträng |
| typ | KeyVaultForLinux | sträng |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | sträng |
| Certifikat Arkiv | MY | sträng |
| linkOnRenewal | false | boolesk |
| certificateStoreLocation  | LocalMachine | sträng |
| requiredInitialSync | true | boolesk |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | sträng mat ris


## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager mallar. Mallar är idealiska när du distribuerar en eller flera virtuella datorer som kräver uppdatering av certifikat. Tillägget kan distribueras till enskilda virtuella datorer eller skalnings uppsättningar för virtuella datorer. Schemat och konfigurationen är gemensamma för båda typerna av mallar. 

JSON-konfigurationen för ett tillägg för virtuell dator måste kapslas i den virtuella datorns resurs fragment, särskilt `"resources": []` objekt för mallen för den virtuella datorn och i händelse av skalnings uppsättning för virtuella datorer under `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` objekt.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForLinux",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
                }         
            }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Azure PowerShell distribution

Azure PowerShell kan användas för att distribuera Key Vault VM-tillägget till en befintlig virtuell dator eller skalnings uppsättning för virtuella datorer. 

* Så här distribuerar du tillägget på en virtuell dator:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Distribuera tillägget på en skalnings uppsättning för virtuella datorer:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera Key Vault VM-tillägget till en befintlig virtuell dator eller skalnings uppsättning för virtuella datorer. 
 
* Så här distribuerar du tillägget på en virtuell dator:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* Distribuera tillägget på en skalnings uppsättning för virtuella datorer:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

Observera följande begränsningar/krav:
- Key Vault begränsningar:
    - Det måste finnas vid tidpunkten för distributionen 
    - Key Vault åtkomst princip har angetts för VM/VMSS-identitet med MSI


## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillstånd för tilläggs distributioner kan hämtas från Azure Portal och genom att använda Azure PowerShell. Om du vill se distributions statusen för tillägg för en virtuell dator kör du följande kommando med hjälp av Azure PowerShell.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).
