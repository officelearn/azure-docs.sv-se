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
ms.openlocfilehash: 355fa90113e931fa3e21df1ccca5736622475bb3
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810388"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption för Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Översikt

Azure Disk Encryption använder BitLocker för att ge fullständig diskkryptering på virtuella Azure-datorer som kör Windows.  Den här lösningen är integrerad med Azure Key Vault för att hantera diskkrypteringsnycklarna och hemligheter i key vault-prenumeration. 

## <a name="prerequisites"></a>Förutsättningar

En fullständig lista över krav, se [krävs för Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Operativsystem

En lista över versioner som för närvarande Windows finns i [krävs för Azure Disk Encryption](../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="internet-connectivity"></a>Internetanslutning

Azure Disk Encryption kräver en Internetanslutning för åtkomst till Active Directory, Key Vault, lagring och hanteringsslutpunkter för paketet.  Mer information om inställningarna för nätverkssäkerhet finns i [krävs för Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schema"></a>Tilläggsschema

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
| apiVersion | 2015-06-15 | datum |
| utgivare | Microsoft.Azure.Security | sträng |
| typ | AzureDiskEncryptionForWindows| sträng |
| typeHandlerVersion | 1.0, 2.2 (VMSS) | int |
| (valfritt) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (valfritt) AADClientSecret | lösenord | sträng |
| (valfritt) AADClientCertificate | thumbprint | sträng |
| EncryptionOperation | EnableEncryption | sträng | 
| KeyEncryptionAlgorithm | RSA-OAEP | sträng |
| KeyEncryptionKeyURL | url | sträng |
| KeyVaultURL | url | sträng |
| SequenceVersion | uniqueidentifier | sträng |
| VolumeType | OS-, Data, alla | sträng |

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