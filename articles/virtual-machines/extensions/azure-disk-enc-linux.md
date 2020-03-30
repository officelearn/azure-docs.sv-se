---
title: Azure Diskkryptering för Linux
description: Distribuerar Azure Disk Encryption för Linux till en virtuell dator med hjälp av ett tillägg för virtuella datorer.
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
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: 22568c7c23771f143f6cd583114949c380d15e3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066924"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure DiskKryptering för Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Översikt

Azure Disk Encryption utnyttjar undersystemet dm-crypt i Linux för att tillhandahålla fullständig diskkryptering på [utvalda Azure Linux-distributioner](https://aka.ms/adelinux).  Den här lösningen är integrerad med Azure Key Vault för att hantera diskkrypteringsnycklar och hemligheter.

## <a name="prerequisites"></a>Krav

En fullständig lista över förutsättningar finns i [Azure Disk Encryption för virtuella Linux-datorer](../linux/disk-encryption-overview.md), särskilt följande avsnitt:

- [Virtuella datorer och operativsystem som stöds](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Ytterligare vm-krav](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Krav på nätverk](../linux/disk-encryption-overview.md#networking-requirements)
- [Lagringskrav för krypteringsnyckel](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Schema för tillägg

Det finns två versioner av tilläggsschema för Azure Disk Encryption (ADE):
- v1.1 - Ett nyare rekommenderat schema som inte använder Azure Active Directory (AAD) egenskaper.
- v0.1 - Ett äldre schema som kräver Azure Active Directory (AAD) egenskaper. 

Om du vill välja `typeHandlerVersion` ett målschema måste egenskapen anges som är lika med den version av schemat som du vill använda.

### <a name="schema-v11-no-aad-recommended"></a>Schema v1.1: Ingen AAD (rekommenderas)

V1.1-schemat rekommenderas och kräver inte Azure Active Directory (AAD) egenskaper.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Schema v0.1: med AAD 

Schemat 0,1 `AADClientID` kräver `AADClientSecret` och `AADClientCertificate`antingen eller .

Använda: `AADClientSecret`

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
| typ | AzureDiskEncryptionForLinux | sträng |
| typHandlerVersion | 1.1, 0.1 | int |
| (0.1 schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Guid | 
| (0.1 schema) AADClientSecret | password | sträng |
| (0.1 schema) AADClientCertificate | Stämpel | sträng |
| (valfritt) (0.1 schema) Lösenfras | password | sträng |
| DiskFormatQuery | {"dev_path":"","namn":"","file_system":""} | JSON-ordbok |
| KrypteringOperation | EnableEncryption, EnableEncryptionFormatAll | sträng | 
| (valfritt - standard RSA-OAEP) KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | sträng |
| KeyVaultURL | url | sträng |
| KeyVaultResourceId | url | sträng |
| (valfritt) KeyEncryptionKeyURL | url | sträng |
| (valfritt) KekVaultResourceId | url | sträng |
| (valfritt) SequenceVersion (SequenceVersion) | uniqueidentifier | sträng |
| VolumeType (Volymtyp) | OS, Data, Alla | sträng |

## <a name="template-deployment"></a>Malldistribution

Ett exempel på malldistribution baserat på schema v1.1 finns i Azure Quickstart Template [201-encrypt-running-linux-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

Ett exempel på malldistribution baserat på schema v0.1 finns i Azure Quickstart Template [201-encrypt-running-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

>[!WARNING]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta använda det här alternativet för att kryptera din virtuella dator.
> - När du krypterar Linux OS-volymer bör den virtuella datorn anses vara otillgänglig. Vi rekommenderar starkt att undvika SSH-inloggningar medan krypteringen pågår för att undvika problem som blockerar öppna filer som måste nås under krypteringsprocessen. Om du vill kontrollera förloppet använder du cmdleten [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell eller att kommandot [VM-kryptering visar](/cli/azure/vm/encryption#az-vm-encryption-show) CLI. Den här processen kan förväntas ta några timmar för en 30 GB OS-volym, plus ytterligare tid för kryptering av datavolymer. Krypteringstiden för datavolym kommer att stå i proportion till datavolymernas storlek och kvantitet om inte alternativet kryptera format används. 
> - Inaktivera kryptering på virtuella Linux-datorer stöds endast för datavolymer. Det stöds inte på data- eller OS-volymer om OS-volymen har krypterats. 

>[!NOTE]
> Även `VolumeType` om parametern är inställd på Alla krypteras datadiskar endast om de är korrekt monterade.

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Felsökning finns i [felsökningsguiden](../linux/disk-encryption-troubleshooting.md)för Azure Disk Encryption .

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/community/). 

Du kan också arkivera en Azure-supportincident. Gå till [Azure-supporten](https://azure.microsoft.com/support/options/) och välj Hämta support. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure Support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg

* Mer information om vm-tillägg finns i [Tillägg och funktioner för virtuella datorer för Linux](features-linux.md).
* Mer information om Azure Disk Encryption för Linux finns i [virtuella Linux-datorer](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines).
