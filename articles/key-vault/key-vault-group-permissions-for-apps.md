---
title: Bevilja behörighet till många program för att få åtkomst till ett Azure key vault | Microsoft Docs
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
ms.devlang: na
ms.topic: conceptual
ms.date: 12/01/2016
ms.author: ambapat
ms.openlocfilehash: 421ceca1453b9e3b97c5ede520ec92372baf2020
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299669"
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>Ge behörighet till många program för att komma åt ett nyckelvalv

## <a name="q-i-have-several-applications-that-need-to-access-a-key-vault-how-can-i-give-these-applications-up-to-1024-access-to-key-vault"></a>F: Jag har flera program som behöver komma åt ett nyckelvalv, hur kan jag ge programmen (upp till 1024) åtkomst till Key Vault?

Principer för åtkomstkontroll Key Vault har stöd för upp till 1024 poster. Du kan dock skapa en Azure Active Directory-säkerhetsgrupp. Lägg till alla associerade huvudnamn i den här säkerhetsgruppen och bevilja åtkomst till den här säkerhetsgruppen till Key Vault.

Här följer krav:
* [Installera Azure Active Directory PowerShell V2-modulen](https://www.powershellgallery.com/packages/AzureAD).
* [Installera Azure PowerShell](/powershell/azure/overview).
* För att köra följande kommandon, behöver du behörighet att skapa eller redigera grupper i Azure Active Directory-klient. Om du inte har behörighet, kan du behöva kontakta Azure Active Directory-administratören.

Nu kör följande kommandon i PowerShell.

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
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId -PermissionsToKeys all –PermissionsToSecrets all –PermissionsToCertificates all 
 
# Of course you can adjust the permissions as required 
```

Om du vill ge en annan uppsättning behörigheter till en grupp av program kan du skapa en separat Azure Active Directory-säkerhetsgrupp för sådana program.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du [säkra ditt nyckelvalv](key-vault-secure-your-key-vault.md).
