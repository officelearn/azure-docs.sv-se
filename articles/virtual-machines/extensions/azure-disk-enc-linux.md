---
title: Azure Disk Encryption för Linux
description: Distribuerar Azure Disk Encryption för Linux till en virtuell dator med ett tillägg för virtuell dator.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: 4fa7f7d1419a8cd1006a632ba67587ab3434bf5a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073815"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption för Linux (Microsoft. Azure. Security. AzureDiskEncryptionForLinux)

## <a name="overview"></a>Översikt

Azure Disk Encryption utnyttjar del systemet dm-crypt i Linux för att tillhandahålla fullständig disk kryptering på [utvalda Azure Linux-distributioner](https://aka.ms/adelinux).  Den här lösningen är integrerad med Azure Key Vault för att hantera disk krypterings nycklar och hemligheter.

## <a name="prerequisites"></a>Krav

En fullständig lista över krav finns i [Azure Disk Encryption för virtuella Linux-datorer](../linux/disk-encryption-overview.md), särskilt i följande avsnitt:

- [Azure Disk Encryption för virtuella Linux-datorer](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Ytterligare krav för virtuell dator](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Nätverks krav](../linux/disk-encryption-overview.md#networking-requirements)

## <a name="extension-schemata"></a>Scheman för tillägg

Det finns två scheman för Azure Disk Encryption: v 1.1, ett senare, rekommenderat schema som inte använder Azure Active Directory (AAD) egenskaper och v 0,1, ett äldre schema som kräver AAD-egenskaper. Du måste använda den schema version som motsvarar det tillägg som du använder: schema v 1.1 för AzureDiskEncryptionForLinux-tillägget version 1,1, schema v 0,1 för AzureDiskEncryptionForLinux-tillägget version 0,1.
### <a name="schema-v11-no-aad-recommended"></a>Schema v 1.1: ingen AAD (rekommenderas)

Schemat v 1.1 rekommenderas och kräver inte Azure Active Directory egenskaper.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultURL": "[keyVaultURL]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        },
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>Schema v 0,1: med AAD 

Schemat för 0,1 kräver `aadClientID` och antingen `aadClientSecret` eller `AADClientCertificate`.

Använda `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
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
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| publisher | Microsoft.Azure.Security | sträng |
| typ | AzureDiskEncryptionForLinux | sträng |
| typeHandlerVersion | 0,1, 1,1 | int |
| (0.1-schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0,1-schema) AADClientSecret | lösenord | sträng |
| (0,1-schema) AADClientCertificate | begäran | sträng |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON-ordlista |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | sträng | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | sträng |
| KeyEncryptionKeyURL | url | sträng |
| valfritt KeyVaultURL | url | sträng |
| Passphrase | lösenord | sträng | 
| SequenceVersion | uniqueidentifier | sträng |
| VolumeType | OS, Data, All | sträng |

## <a name="template-deployment"></a>Malldistribution

Ett exempel på en mall distribution finns i [Aktivera kryptering på en virtuell Linux-dator som körs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Instruktioner finns i den senaste [Azure CLI-dokumentationen](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Information om fel sökning finns i [fel söknings guiden för Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/community/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg

Mer information om VM-tillägg finns i [tillägg och funktioner för virtuella datorer för Linux](features-linux.md).