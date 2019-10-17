---
title: Hantera en användardefinierad hanterad identitet med hjälp av Azure CLI
description: Steg för steg-anvisningar om hur du skapar, visar och tar bort en användardefinierad hanterad identitet med hjälp av Azure CLI.
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
ms.openlocfilehash: 6f6e7f220e48a856e55315ab9e4f865caaf27c99
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389032"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Skapa, Visa eller ta bort en användardefinierad hanterad identitet med hjälp av Azure CLI


Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva ange autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du skapar, visar och tar bort en användardefinierad hanterad identitet med hjälp av Azure CLI.

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en tilldelad och användardefinierad hanterad identitet](overview.md#how-does-it-work)** .
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Det finns tre alternativ för att köra CLI-skript exempel:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure Portal (se nästa avsnitt).
    - Använd den inbäddade Azure Cloud Shell via knappen "prova", som finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda en lokal CLI-konsol. Logga in på Azure med hjälp av `az login`, med ett konto som är associerat med den Azure-prenumeration under vilken du vill distribuera den tilldelade hanterade identiteten.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet 

För att skapa en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitets deltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Använd kommandot [AZ Identity Create](/cli/azure/identity#az-identity-create) för att skapa en användardefinierad hanterad identitet. Parametern `-g` anger den resurs grupp där den tilldelade hanterade identiteten ska skapas och parametern `-n` anger dess namn. Ersätt parameter värden `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista användare-tilldelade hanterade identiteter

Om du vill visa en lista över/läsa en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitet](/azure/role-based-access-control/built-in-roles#managed-identity-operator) eller [hanterad identitets deltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Om du vill visa en lista över användarspecifika hanterade identiteter använder du kommandot [AZ Identity List](/cli/azure/identity#az-identity-list) . Ersätt `<RESOURCE GROUP>` med ditt eget värde:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
I JSON-svaret har användare tilldelade hanterade identiteter `"Microsoft.ManagedIdentity/userAssignedIdentities"`-värdet returnerat för nyckeln `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en användare som tilldelats en hanterad identitet

För att ta bort en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitets deltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Om du vill ta bort en användardefinierad hanterad identitet använder du kommandot [AZ Identity Delete](/cli/azure/identity#az-identity-delete) .  Parametern-n anger dess namn och parametern-g anger resurs gruppen där den användare som tilldelats hanterade identiteten skapades. Ersätt värdena för parametern `<USER ASSIGNED IDENTITY NAME>` och `<RESOURCE GROUP>` med dina egna värden:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Om du tar bort en tilldelad hanterad identitet tas inte referensen bort från alla resurser som den har tilldelats. Ta bort de från VM/VMSS med kommandot `az vm/vmss identity remove`

## <a name="next-steps"></a>Nästa steg

En fullständig lista över Azure CLI Identity-kommandon finns i [AZ-identitet](/cli/azure/identity).

Information om hur du tilldelar en användardefinierad hanterad identitet till en virtuell Azure-dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
