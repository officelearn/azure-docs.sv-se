---
title: Bevilja behörighet till många program för att komma åt ett Azure Key Vault – Azure Key Vault | Microsoft Docs
description: Lär dig hur du beviljar behörighet till många program för att komma åt ett nyckel valv
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 07ee544057ffeb0a5859cc771b124523ec79c9c0
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976405"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>Bevilja flera program åtkomst till ett nyckel valv

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Princip för åtkomst kontroll kan användas för att bevilja flera program åtkomst till ett nyckel valv. En princip för åtkomst kontroll har stöd för upp till 1024 program och konfigureras enligt följande:

1. Skapa en Azure Active Directory säkerhets grupp. 
2. Lägg till alla program associerade tjänstens huvud namn i säkerhets gruppen.
3. Bevilja säkerhets gruppen åtkomst till din Key Vault.

## <a name="prerequisites"></a>Förutsättningar

Följande är förutsättningarna:
* [Installera Azure PowerShell](/powershell/azure/overview).
* [Installera PowerShell-modulen för Azure Active Directory v2](https://www.powershellgallery.com/packages/AzureAD).
* Behörigheter för att skapa/redigera grupper i Azure Active Directory klient organisationen. Om du inte har behörighet kan du behöva kontakta Azure Active Directory administratören. Se [om Azure Key Vault nycklar, hemligheter och certifikat om du](about-keys-secrets-and-certificates.md) vill ha mer information om behörighet för Key Vault åtkomst principer.

## <a name="granting-key-vault-access-to-applications"></a>Bevilja Key Vault åtkomst till program

Kör följande kommandon i PowerShell:

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

Om du behöver bevilja en annan uppsättning behörigheter till en grupp med program skapar du en separat Azure Active Directory säkerhets grupp för dessa program.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du [skyddar nyckel valvet](key-vault-secure-your-key-vault.md).
