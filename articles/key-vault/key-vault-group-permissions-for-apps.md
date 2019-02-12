---
title: Bevilja behörighet till många program för att få åtkomst till ett Azure key vault - Azure Key Vault | Microsoft Docs
description: Lär dig att ge behörighet till många program för att komma åt ett nyckelvalv
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 24f725f7b83c4f043ec2b977917d5bb09665c96d
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990333"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>Bevilja flera program åtkomst till ett nyckelvalv

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Principer för åtkomstkontroll kan användas för att bevilja flera program åtkomst till ett nyckelvalv. En principer för åtkomstkontroll kan stöda upp till 1024 program och konfigureras på följande sätt:

1. Skapa en Azure Active Directory-säkerhetsgrupp. 
2. Lägg till alla program som är associerade tjänstens huvudnamn i gruppen.
3. Bevilja den säkerhetsgrupp åtkomstbehörighet till ditt Nyckelvalv.

## <a name="prerequisites"></a>Förutsättningar

Här följer kraven:
* [Installera Azure PowerShell](/powershell/azure/overview).
* [Installera Azure Active Directory PowerShell V2-modulen](https://www.powershellgallery.com/packages/AzureAD).
* Behörigheter för att skapa/Redigera grupper i Azure Active Directory-klient. Om du inte har behörighet, kan du behöva kontakta Azure Active Directory-administratören. Se [om Azure Key Vault-nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md) mer information om hur Key Vault åtkomstbehörigheter principen.

## <a name="granting-key-vault-access-to-applications"></a>Bevilja Key Vault-åtkomst till program

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

Om du vill ge en annan uppsättning behörigheter till en grupp av program kan du skapa en separat Azure Active Directory-säkerhetsgrupp för sådana program.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du [säkra ditt nyckelvalv](key-vault-secure-your-key-vault.md).
