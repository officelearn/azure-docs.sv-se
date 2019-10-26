---
title: Azure Disk Encryption för Windows | Microsoft Docs
description: Distribuerar Azure Disk Encryption till en virtuell Windows-dator med ett tillägg för virtuell dator.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 056bd1293e0593a7fb7f9909cfd85043577686c4
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901338"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption för Windows (Microsoft. Azure. Security. AzureDiskEncryption)

## <a name="overview"></a>Översikt

Azure Disk Encryption utnyttjar BitLocker för att tillhandahålla fullständig disk kryptering på virtuella Azure-datorer som kör Windows.  Den här lösningen är integrerad med Azure Key Vault för att hantera disk krypterings nycklar och hemligheter i Key Vault-prenumerationen. 

## <a name="prerequisites"></a>Krav

En fullständig lista över krav finns i [Azure Disk Encryption för virtuella Linux-datorer](../linux/disk-encryption-overview.md), särskilt i följande avsnitt:

- [Azure Disk Encryption för virtuella Linux-datorer](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Nätverks krav](../windows/disk-encryption-overview.md#networking-requirements)
- [grupprincip krav](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schemata"></a>Scheman för tillägg

Det finns två scheman för Windows AzureDiskEncryption-tillägget: v 2.2, ett nyare, rekommenderat schema som inte använder Azure Active Directory-egenskaper (AAD) och v 1.1, ett äldre schema som kräver AAD-egenskaper. Du måste använda den schema version som motsvarar det tillägg som du använder: schema v 2.2 för AzureDiskEncryption-tillägget version 2,2, schema v 1.1 för AzureDiskEncryption-tillägget version 1,1.

### <a name="schema-v22-no-aad-recommended"></a>Schema v 2.2: ingen AAD (rekommenderas)

V 2.2-schemat rekommenderas för alla nya virtuella datorer och kräver inte Azure Active Directory egenskaper.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v11-with-aad"></a>Schema v 1.1: med AAD 

Schemat för 1,1 kräver `aadClientID` och antingen `aadClientSecret` eller `AADClientCertificate` och rekommenderas inte för nya virtuella datorer.

Använda `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```

Använda `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Egenskaps värden

| Namn | Värde/exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| Förläggare | Microsoft. Azure. Security | sträng |
| typ | AzureDiskEncryptionForLinux | sträng |
| typeHandlerVersion | 1,1, 2,2 | sträng |
| (1,1-schema) AADClientID | XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | LED | 
| (1,1-schema) AADClientSecret | lösenord | sträng |
| (1,1-schema) AADClientCertificate | begäran | sträng |
| DiskFormatQuery | {"dev_path": "", "namn": "", "FILE_SYSTEM": ""} | JSON-ordlista |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | sträng | 
| KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | sträng |
| keyEncryptionKeyURL | url | sträng |
| keyVaultURL | url | sträng |
| valfritt Fraser | lösenord | sträng | 
| sequenceVersion | uniqueidentifier | sträng |
| volumeType | OS, data, alla | sträng |

## <a name="template-deployment"></a>Malldistribution
Ett exempel på en mall distribution finns i [skapa en ny krypterad virtuell Windows-dator från Galleri avbildningen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Instruktioner finns i den senaste [Azure CLI-dokumentationen](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsökning

Läs mer i [fel söknings guiden för Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/community/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
Mer information om tillägg finns i [tillägg för virtuella datorer och funktioner för Windows](features-windows.md).
