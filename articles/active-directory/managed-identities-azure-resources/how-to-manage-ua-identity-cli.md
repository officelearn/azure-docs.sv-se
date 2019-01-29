---
title: Så här hanterar du en Användartilldelad hanterad identitet med hjälp av Azure CLI
description: Steg för steg-anvisningar om hur du skapar, lista och ta bort en Användartilldelad hanterad identitet med hjälp av Azure CLI.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: priyamo
ms.openlocfilehash: 47ad46bfb89efd11ea19386c2f2e26fc11a229c5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175510"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Skapa, visa eller ta bort en Användartilldelad hanterad identitet med hjälp av Azure CLI

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du skapar, lista och ta bort en Användartilldelad hanterad identitet med hjälp av Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill köra CLI-exempelskript, finns det tre alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
    - Använd inbäddad Azure Cloud Shell via ”Prova” knappen, finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda den lokala CLI-konsolen. Logga in på Azure med `az login`, med ett konto som är associerad med den Azure-prenumeration som du vill distribuera den användartilldelade hanterad identitet.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet 

Om du vill skapa en hanterad Användartilldelad identitet, ditt konto måste den [hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) rolltilldelning.

Använd den [az identitet skapa](/cli/azure/identity#az-identity-create) kommando för att skapa en Användartilldelad hanterad identitet. Den `-g` parametern anger resursgruppens namn var du vill skapa en hanterad identitet användartilldelade och `-n` parametern anger dess namn. Ersätt den `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` parametervärden med dina egna värden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista användartilldelade hanterade identiteter

Om du vill lista/läsa en hanterad Användartilldelad identitet, ditt konto måste den [hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) eller [hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) rolltilldelning.

Använd om du vill visa användartilldelade hanterade identiteter på [az identitet lista](/cli/azure/identity#az-identity-list) kommando. Ersätt den `<RESOURCE GROUP>` med ditt eget värde:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
I json-responsen användartilldelade hanterade identiteter har `"Microsoft.ManagedIdentity/userAssignedIdentities"` värdet som returneras för nyckel `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en hanterad Användartilldelad identitet

Om du vill ta bort en hanterad Användartilldelad identitet måste ditt konto måste den [hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) rolltilldelning.

Ta bort en hanterad Användartilldelad identitet genom att använda den [az identitet ta bort](/cli/azure/identity#az-identity-delete) kommando.  Parametern - n anger dess namn och -g-parametern anger resursgruppen där den hanterade Användartilldelad identitet har skapats. Ersätt den `<USER ASSIGNED IDENTITY NAME>` och `<RESOURCE GROUP>` parametervärdena med dina egna värden:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Tar bort en hanterad Användartilldelad identitet kommer inte att ta bort referensen, från alla resurser som tilldelades till. Ta bort dem från VM/VMSS med den `az vm/vmss identity remove` kommando

## <a name="next-steps"></a>Nästa steg

En fullständig lista över Azure CLI-kommandon för identitet, se [az identitet](/cli/azure/identity).

Information om hur du tilldelar en Användartilldelad-hanterad identitet till en virtuell dator i Azure-Se [konfigurera hanterade identiteter för Azure-resurser på en Azure virtuell dator med Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
