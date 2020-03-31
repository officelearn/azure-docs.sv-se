---
title: Azure Diskkryptering för Windows
description: Distribuerar Azure Disk Encryption till en virtuell Windows-dator med hjälp av ett tillägg för virtuella datorer.
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
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: e975e1757b77b4aab52a59d1f0709ef9cadae94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066872"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Diskkryptering för Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Översikt

Azure Disk Encryption utnyttjar BitLocker för att tillhandahålla fullständig diskkryptering på virtuella Azure-datorer som kör Windows.  Den här lösningen är integrerad med Azure Key Vault för att hantera diskkrypteringsnycklar och hemligheter i din nyckelvalvsprenumeration. 

## <a name="prerequisites"></a>Krav

En fullständig lista över förutsättningar finns i [Azure Disk Encryption för virtuella datorer i Windows](../windows/disk-encryption-overview.md), särskilt följande avsnitt:

- [Virtuella datorer och operativsystem som stöds](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Krav på nätverk](../windows/disk-encryption-overview.md#networking-requirements)
- [Krav på grupprincip](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Schema för tillägg

Det finns två versioner av tilläggsschema för Azure Disk Encryption (ADE):
- v2.2 - Ett nyare rekommenderat schema som inte använder Azure Active Directory (AAD) egenskaper.
- v1.1 - Ett äldre schema som kräver Azure Active Directory (AAD) egenskaper. 

Om du vill välja `typeHandlerVersion` ett målschema måste egenskapen anges som är lika med den version av schemat som du vill använda.

### <a name="schema-v22-no-aad-recommended"></a>Schema v2.2: Ingen AAD (rekommenderas)

V2.2-schemat rekommenderas för alla nya virtuella datorer och kräver inte Azure Active Directory-egenskaper.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KekVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Schema v1.1: med AAD 

1.1-schemat `aadClientID` kräver `aadClientSecret` och `AADClientCertificate` antingen eller och rekommenderas inte för nya virtuella datorer.

Använda: `aadClientSecret`

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Använda: `AADClientCertificate`

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Egenskapsvärden

| Namn | Värde / Exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| utgivare | Microsoft.Azure.Security | sträng |
| typ | AzureDiskEncryption | sträng |
| typHandlerVersion | 2.2, 1.1 | sträng |
| (1.1 schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Guid | 
| (1.1 schema) AADClientSecret | password | sträng |
| (1.1 schema) AADClientCertificate | Stämpel | sträng |
| KrypteringOperation | EnableEncryption, EnableEncryptionFormatAll | sträng | 
| (valfritt - standard RSA-OAEP) KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | sträng |
| KeyVaultURL | url | sträng |
| KeyVaultResourceId | url | sträng |
| (valfritt) KeyEncryptionKeyURL | url | sträng |
| (valfritt) KekVaultResourceId | url | sträng |
| (valfritt) SequenceVersion (SequenceVersion) | uniqueidentifier | sträng |
| VolumeType (Volymtyp) | OS, Data, Alla | sträng |

## <a name="template-deployment"></a>Malldistribution

Ett exempel på malldistribution baserat på schema v2.2 finns i Azure QuickStart Template [201-encrypt-running-windows-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).

Ett exempel på malldistribution baserat på schema v1.1 finns i Azure QuickStart Template [201-encrypt-running-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

>[!NOTE]
> Även `VolumeType` om parametern är inställd på Alla krypteras datadiskar endast om de är korrekt formaterade. 

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Felsökning finns i [felsökningsguiden](../windows/disk-encryption-troubleshooting.md)för Azure Disk Encryption .

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/community/). 

Du kan också arkivera en Azure-supportincident. Gå till [Azure-supporten](https://azure.microsoft.com/support/options/) och välj Hämta support. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure Support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg

* Mer information om tillägg finns i [Tillägg och funktioner för virtuella datorer för Windows](features-windows.md).
* Mer information om Azure Disk Encryption för Windows finns i [virtuella Windows-datorer](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines).
