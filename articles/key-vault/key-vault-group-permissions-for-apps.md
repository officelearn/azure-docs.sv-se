---
title: "Bevilja behörighet till många program till en Azure key vault | Microsoft Docs"
description: "Lär dig att tilldela behörighet för många program för att komma åt ett nyckelvalv"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: ambapat
ms.openlocfilehash: 14da9256def60d678ef5cae795fef1c373914b5c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>Bevilja behörighet till många program för att komma åt ett nyckelvalv

## <a name="q-i-have-several-over-16-applications-that-need-to-access-a-key-vault-since-key-vault-only-allows-16-access-control-entries-how-can-i-achieve-that"></a>F: Jag har flera (över 16) program som behöver åtkomst till en nyckelvalvet. Hur kan jag få det med tanke på att Key Vault endast tillåter 16 åtkomstkontrollposter?

Key Vault principer för åtkomstkontroll har endast stöd för 16-poster. Du kan dock skapa en Azure Active Directory-säkerhetsgrupp. Lägg till alla associerade tjänster säkerhetsobjekt i den här säkerhetsgruppen och sedan ge åtkomst till den här säkerhetsgruppen att Nyckelvalvet.

Här följer kraven:
* [Installera Azure Active Directory PowerShell V2-modulen](https://www.powershellgallery.com/packages/AzureAD).
* [Installera Azure PowerShell](/powershell/azure/overview).
* För att köra följande kommandon, behöver du behörigheter att skapa eller redigera grupper i Azure Active Directory-klient. Om du inte har behörighet, kan du behöva kontaktar du administratören Azure Active Directory.

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
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId -PermissionToKeys all –PermissionToSecrets all –PermissionToCertificates all 
 
# Of course you can adjust the permissions as required 
```

Om du behöver ge en annan uppsättning behörigheter till en grupp av program kan du skapa en separat Azure Active Directory-säkerhetsgrupp för dessa program.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du [Secure nyckelvalvet](key-vault-secure-your-key-vault.md).
