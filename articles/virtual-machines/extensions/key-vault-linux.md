---
title: VM-tillägg för Azure Key Vault för Linux
description: Distribuera en agent som utför automatisk uppdatering av Key Vault-certifikat på virtuella datorer med hjälp av ett tillägg för virtuella datorer.
services: virtual-machines-linux
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: a4fb3ad2ce6225528910bbda9d98a38001242710
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298996"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Key Vault virtuell dator förlängning för Linux

Tillägget Key Vault VM ger automatisk uppdatering av certifikat som lagras i ett Azure-nyckelvalv. Närmare bestämt övervakar tillägget en lista över observerade certifikat som lagras i nyckelvalv.  När du upptäcker en ändring hämtas tillägget och installerar motsvarande certifikat. Key Vault VM-tillägget publiceras och stöds av Microsoft, som för närvarande finns på virtuella Linux-datorer. I det här dokumentet beskrivs de plattformar, konfigurationer och distributionsalternativ som stöds för VM-tillägget Key Vault för Linux. 

### <a name="operating-system"></a>Operativsystem

VM-tillägget Key Vault stöder dessa Linux-distributioner:

- Ubuntu-1604
- Ubuntu-1804
- Debian-9
- Suse-15 

### <a name="supported-certificate-content-types"></a>Innehållstyper som stöds

- PKCS #12

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för VM-tillägget Key Vault. Tillägget kräver inte skyddade inställningar - alla dess inställningar betraktas som information utan säkerhetspåverkan. Tillägget kräver en lista över övervakade hemligheter, avsökningsfrekvens och målcertifikatarkivet. Mer specifikt:  
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
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
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
> Webbadresserna för de observerade certifikaten `https://myVaultName.vault.azure.net/secrets/myCertName`ska vara av samma form .
> 
> Detta beror `/secrets` på att sökvägen returnerar hela certifikatet, inklusive den privata nyckeln, medan `/certificates` sökvägen inte gör det. Mer information om certifikat hittar du här: [Key Vault-certifikat](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)


### <a name="property-values"></a>Egenskapsvärden

| Namn | Värde / Exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| utgivare | Microsoft.Azure.KeyVault | sträng |
| typ | KeyVaultForLinux | sträng |
| typHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | sträng |
| certifikatStoreName | MY | sträng |
| länkOnRenewal (olikartade) | false | boolean |
| certifikatButiksplats  | LokalMaskin | sträng |
| requiredInitialSync | true | boolean |
| observeradeCertifierar  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | strängmatris


## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Mallar är idealiska när du distribuerar en eller flera virtuella datorer som kräver uppdatering av certifikat efter distributionen. Tillägget kan distribueras till enskilda virtuella datorer eller skaluppsättningar för virtuella datorer. Schemat och konfigurationen är gemensamma för båda malltyperna. 

JSON-konfigurationen för ett tillägg för virtuella datorer måste kapslas inuti `"resources": []` mallens resursfragment för den virtuella `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` datorn, särskilt objekt för den virtuella datorn-mallen och vid skalauppsättning för virtuella datorer under objekt.

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
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Azure PowerShell-distribution

Azure PowerShell kan användas för att distribuera VM-tillägget Key Vault till en befintlig virtuell dator eller skaluppsättning för virtuella datorer. 

* Så här distribuerar du tillägget på en virtuell dator:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Så här distribuerar du tillägget på en skalningsuppsättning för virtuella datorer:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera VM-tillägget Key Vault till en befintlig virtuell dator eller skaluppsättning för virtuell dator. 
 
* Så här distribuerar du tillägget på en virtuell dator:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* Så här distribuerar du tillägget på en skalningsuppsättning för virtuella datorer:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

Tänk på följande begränsningar/krav:
- Begränsningar för Nyckelvalv:
  - Den måste finnas vid tidpunkten för distributionen 
  - Åtkomstprincipen för key vault är inställd för VM/VMSS-identitet med MSI


## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för tilläggsdistributioner kan hämtas från Azure-portalen och med hjälp av Azure PowerShell. Om du vill se distributionstillståndet för tillägg för en viss virtuell dator kör du följande kommando med Azure PowerShell.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj Hämta support. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
