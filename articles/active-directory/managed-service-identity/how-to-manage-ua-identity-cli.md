---
title: Så här hanterar du en användare som tilldelats hanterade tjänsten identitet (MSI) med hjälp av Azure CLI
description: Steg för steg hanterade instruktioner om hur du skapar, lista och tar bort en användare som tilldelats identitet med hjälp av Azure CLI.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: a333d111b3f88183648f8a51185c245430c0533f
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-cli"></a>Skapa, visa eller ta bort en användare som tilldelats identitet med hjälp av Azure CLI

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln får lära du att skapa, visa och ta bort en som tilldelats användaridentitet med hjälp av Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade tjänstidentiteten kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan ett system som har tilldelats och användaren som har tilldelats identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

- Om du vill köra skriptexempel CLI har tre alternativ:

    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
    - Använd inbäddade Azure Cloud Shell via försök ”knappen”, finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda den lokala CLI-konsolen. Logga in på Azure med hjälp av `az login`, med ett konto som är kopplat till den Azure-prenumeration som du vill distribuera den tilldelade användaridentiteten.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användare som tilldelats hanterade identitet 

Använd för att skapa en identitet för användaren som har tilldelats den [az identitet skapa](/cli/azure/identity#az-identity-create) kommando. Den `-g` parametern anger resursgruppen var att skapa den tilldelade identitet och `-n` parametern anger dess namn. Ersätt den `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` parametervärden med egna värden:

> [!IMPORTANT]
> Skapa tilldelade användaridentiteter stöder endast alfanumeriska och bindestreck (0-9 eller a-z eller A-Z eller -) tecken. Namnet bör dessutom begränsas till 24 tecken för tilldelning till VM/VMSS ska fungera korrekt. Sök igen efter uppdateringar. Mer information finns i [vanliga frågor och kända problem](known-issues.md)

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Lista över användare som tilldelats identiteter

Använd om du vill visa en lista med användaridentiteter tilldelas den [az identitetslistan](/cli/azure/identity#az-identity-list) kommando.  Den `-g` parametern anger resursgruppen där användaren som har tilldelats identitet har skapats.  Ersätt den `<RESOURCE GROUP>` med ett eget värde:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
I json-svar användaridentiteter har `"Microsoft.ManagedIdentity/userAssignedIdentities"` returvärdet för nyckeln, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-identity"></a>Tar bort en användare som tilldelats identitet

Om du vill ta bort en som tilldelats användaridentitet, Använd den [az identitet ta bort](/cli/azure/identity#az-identity-delete) kommando.  Parametern - n anger namnet och parametern -g anger resursgruppen där användaren som har tilldelats identitet har skapats.  Ersätt den `<USER ASSIGNED IDENTITY NAME>` och `<RESOURCE GROUP>` värden för parametrar med egna värden:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Ta bort en användare som tilldelats identitet tas inte bort referens, från alla resurser som den tilldelats. Ta bort dem från VM/VMSS använder den `az vm/vmss identity remove` kommando

## <a name="related-content"></a>Relaterat innehåll

En fullständig lista över Azure CLI-kommandona för identitet, se [az identitet](/cli/azure/identity).

Mer information om hur du tilldelar en identitet för användaren som har tilldelats till en Azure VM Se [konfigurera hanterade tjänsten identitet (MSI) med hjälp av Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-identity)


 
