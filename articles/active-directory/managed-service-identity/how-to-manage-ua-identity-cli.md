---
title: Så här hanterar du en användare tilldelas hanterad tjänstidentitet (MSI) med Azure CLI
description: Steg för steg hanterad instruktioner om hur du skapar, lista och ta bort en användare som tilldelats tjänst identitet med hjälp av Azure CLI.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 23567c985f4f9df46ee7d80051c15dc5910a1ea8
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37904070"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-cli"></a>Skapa, visa eller ta bort en Användartilldelad identitet med hjälp av Azure CLI

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig skapa, visa och ta bort en Användartilldelad identitet med hjälp av Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en systemtilldelad och Användartilldelad identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto, [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

- Om du vill köra CLI-exempelskript, finns det tre alternativ:

    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
    - Använd inbäddad Azure Cloud Shell via ”Prova” knappen, finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda den lokala CLI-konsolen. Logga in på Azure med `az login`, med ett konto som är associerad med den Azure-prenumeration som du vill distribuera Användartilldelad identitet.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användare som tilldelats hanterad identitet 

Du kan skapa en Användartilldelad identitet med den [az identitet skapa](/cli/azure/identity#az-identity-create) kommando. Den `-g` parametern anger resursgruppens namn var du vill skapa en Användartilldelad identitet, och `-n` parametern anger dess namn. Ersätt den `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` parametervärden med dina egna värden:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Lista användartilldelade identiteter

Använd om du vill visa användartilldelade identiteter i [az identitet lista](/cli/azure/identity#az-identity-list) kommando.  Den `-g` parametern anger resursgruppen där Användartilldelad identitet har skapats.  Ersätt den `<RESOURCE GROUP>` med ditt eget värde:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
I json-responsen användaridentiteter har `"Microsoft.ManagedIdentity/userAssignedIdentities"` värdet som returneras för nyckel `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-identity"></a>Ta bort Användartilldelad identitet

Ta bort en Användartilldelad identitet genom att använda den [az identitet ta bort](/cli/azure/identity#az-identity-delete) kommando.  Parametern - n anger dess namn och -g-parametern anger resursgruppen där Användartilldelad identitet har skapats.  Ersätt den `<USER ASSIGNED IDENTITY NAME>` och `<RESOURCE GROUP>` parametervärdena med dina egna värden:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Tar bort en Användartilldelad identitet kommer inte att ta bort referensen, från alla resurser som tilldelades till. Ta bort dem från VM/VMSS med den `az vm/vmss identity remove` kommando

## <a name="related-content"></a>Relaterat innehåll

En fullständig lista över Azure CLI-kommandon för identitet, se [az identitet](/cli/azure/identity).

Information om hur du tilldelar en Användartilldelad identitet till en virtuell dator i Azure-Se [konfigurera hanterad tjänstidentitet (MSI) med Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-identity)


 
