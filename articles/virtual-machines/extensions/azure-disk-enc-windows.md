---
title: Azure Disk Encryption för Windows | Microsoft Docs
description: Distribuerar Azure Disk Encryption till en Windows-dator med hjälp av tillägg för virtuell dator.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: ff77f9fc017627143b14544af03d0d5e80813db9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051706"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption för Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Översikt

Azure Disk Encryption använder BitLocker för att ge fullständig diskkryptering på virtuella Azure-datorer som kör Windows.  Den här lösningen är integrerad med Azure Key Vault för att hantera diskkrypteringsnycklarna och hemligheter i key vault-prenumeration. 

## <a name="prerequisites"></a>Nödvändiga komponenter

En fullständig lista över krav, se [krävs för Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Operativsystem

En lista över versioner som för närvarande Windows finns i [krävs för Azure Disk Encryption](../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="internet-connectivity"></a>Internetanslutning

Azure Disk Encryption kräver en Internetanslutning för åtkomst till Active Directory, Key Vault, lagring och hanteringsslutpunkter för paketet.  Mer information om inställningarna för nätverkssäkerhet finns i [krävs för Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schemata"></a>Tillägget scheman

Det finns två scheman för Azure Disk Encryption: v1.1, ett nyare, rekommenderas schema som inte använder Azure Active Directory (AAD)-egenskaper och v0.1, ett äldre schema som kräver AAD egenskaper. Du måste använda schemaversion som motsvarar det tillägget som du använder: schemat v1.1 för AzureDiskEncryption tilläggsversion 1.1, schemat v0.1 för AzureDiskEncryption tilläggsversion 0.1.

### <a name="schema-v11-no-aad-recommended"></a>Schemat v1.1: Ingen AAD (rekommenderas)

V1.1 schemat bör och kräver inte Azure Active Directory-egenskaper.

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


### <a name="schema-v01-with-aad"></a>Schemat v0.1: med AAD 

0,1 schemat kräver `aadClientID` och antingen `aadClientSecret` eller `AADClientCertificate`.

Med hjälp av `aadClientSecret`:

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

Med hjälp av `AADClientCertificate`:

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


### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0.1, 1.1 | int |
| (0,1 schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (0,1 schema) AADClientSecret | password | string |
| (0,1 schema) AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON-ordlista |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyEncryptionKeyURL | url | string |
| KeyVaultURL | url | string |
| (valfritt) Passphrase | password | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | OS, Data, All | string |

## <a name="template-deployment"></a>Malldistribution
Ett exempel på för malldistribution, se [ skapa en ny krypterade Windows virtuell dator från galleriet bilden](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Anvisningar finns i senast [Azure CLI-dokumentationen](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Referera till den [felsökningsguide för Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/community/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
Mer information om tillägg finns i [VM-tillägg och funktioner i Windows](features-windows.md).
