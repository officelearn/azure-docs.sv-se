---
title: Hantera användartilldelade hanterade identitet - Azure CLI - Azure AD
description: Steg för steg instruktioner om hur du skapar, lista och ta bort en användartilldelade hanterad identitet med hjälp av Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/15/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1e8587562ff452373fe2ee3b98fa20309e77cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266590"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Skapa, lista eller ta bort en användartilldelad hanterad identitet med Hjälp av Azure CLI


Hanterade identiteter för Azure-resurser ger Azure-tjänster en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du skapar, listar och tar bort en användartilldelad hanterad identitet med Azure CLI.

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Var noga med att granska [skillnaden mellan en systemtilldelad och användartilldelad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill köra CLI-skriptexemplen har du tre alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
    - Använd det inbäddade Azure Cloud Shell via knappen "Prova", som finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda en lokal CLI-konsol. Logga in på `az login`Azure med ett konto som är associerat med Azure-prenumerationen där du vill distribuera den användartilldelade hanterade identiteten.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet 

Om du vill skapa en användartilldelad hanterad identitet behöver ditt konto rolltilldelningen [Managed Identity Contributor.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Använd kommandot [az identity create](/cli/azure/identity#az-identity-create) för att skapa en användartilldelad hanterad identitet. Parametern `-g` anger resursgruppen var den användartilldelade hanterade identiteten `-n` ska skapas och parametern anger dess namn. Ersätt `<RESOURCE GROUP>` värdena och parametern med `<USER ASSIGNED IDENTITY NAME>` dina egna värden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista användartilldelade hanterade identiteter

Om du vill lista/läsa en användartilldelad hanterad identitet behöver ditt konto rolltilldelningen [Managed Identity Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) eller Managed Identity [Contributor.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Om du vill visa användartilldelade hanterade identiteter använder du kommandot [az identity list.](/cli/azure/identity#az-identity-list) Ersätt `<RESOURCE GROUP>` med ditt eget värde:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
I json-svaret har `"Microsoft.ManagedIdentity/userAssignedIdentities"` användartilldelade hanterade identiteter returnerat för nyckel, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en användartilldelad hanterad identitet

Om du vill ta bort en användartilldelad hanterad identitet behöver kontot rolltilldelningen [Managed Identity Contributor.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Om du vill ta bort en användartilldelad hanterad identitet använder du kommandot [az identity delete.](/cli/azure/identity#az-identity-delete)  Parametern -n anger dess namn och parametern -g anger den resursgrupp där den användartilldelade hanterade identiteten skapades. Ersätt `<USER ASSIGNED IDENTITY NAME>` värdena och `<RESOURCE GROUP>` parametrarna med dina egna värden:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Om du tar bort en användartilldelad hanterad identitet tas inte referensen bort från någon resurs som den har tilldelats. Ta bort dem från VM/VMSS med `az vm/vmss identity remove` kommandot

## <a name="next-steps"></a>Nästa steg

En fullständig lista över Azure CLI-identitetskommandon finns i [az identity](/cli/azure/identity).

Information om hur du tilldelar en användartilldelad hanterad identitet till en Azure VM finns i [Konfigurera hanterade identiteter för Azure-resurser på en Azure VM med Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
