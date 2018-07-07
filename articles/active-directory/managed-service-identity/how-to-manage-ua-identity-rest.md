---
title: Så här hanterar du Azure-användare tilldelas hanterade identiteter med hjälp av REST
description: Steg för steg tilldelas instruktioner om hur du skapar, lista och ta bort en användare hanterad identitet för att göra REST API-anrop.
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
ms.date: 06/26/2018
ms.author: daveba
ms.openlocfilehash: afeac0cdb24593f5b7614a145021eefd7b376be9
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37904033"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-rest-api-calls"></a>Skapa, visa eller ta bort en Användartilldelad identitet med hjälp av REST API-anrop

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterad identitet ger Azure-tjänster för autentisering till tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du skapar, lista, och ta bort en användare som tilldelats hanterad identitet med CURL för att göra REST API-anrop.

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en systemtilldelad och Användartilldelad identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto, [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du använder Windows kan du installera den [Windows-undersystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) eller Använd den [Azure Cloud Shell](../../cloud-shell/overview.md) i Azure-portalen.
- Om du använder den [Windows-undersystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) eller en [Linux-distributionsoperativsystem](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [installera den lokala konsolen i Azure CLI](/azure/install-azure-cli).
- Om du använder Azure CLI lokalt, logga in på Azure med hjälp av `az login` med ett konto som är associerad med den Azure-prenumeration du vill distribuera eller hämta användaren som tilldelats hanterade identitetsinformation.
- Hämta en ägar-token med `az account get-access-token` tilldelade hanterad identitet åtgärder att utföra följande användare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användare som tilldelats hanterad identitet 

Använd följande CURL-begäran till Azure Resource Manager-API för att skapa en användare som tilldelats hanterad identitet. Ersätt den `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>`,`<LOCATION>`, och `<ACCESS TOKEN>` värdena med dina egna värden:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="list-user-assigned-managed-identities"></a>Lista över användare som tilldelats hanterade identiteter

Använd följande CURL-begäran till Azure Resource Manager-API för att lista användartilldelade hanterade identiteter.  Ersätt den `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, och `<ACCESS TOKEN>` värdena med dina egna värden:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```
## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en användare som tilldelats hanterad identitet

Använd följande CURL-begäran till Azure Resource Manager-API för att ta bort en användare som tilldelats hanterad identitet. Ersätt den `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, och `<ACCESS TOKEN>` parametervärdena med dina egna värden:

> [!NOTE]
> Tar bort en Användartilldelad identitet kommer inte att ta bort referensen från alla resurser som den tilldelats. Ta bort en användare som tilldelats hanteras från en virtuell dator med hjälp av CURL Se [ta bort en Användartilldelad identitet från en Azure VM](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Nästa steg

Information om hur du tilldelar en Användartilldelad identitet till en Azure VM/VMSS med CURL i, [konfigurera hanterade identiteter på en Azure virtuell dator med CURL](qs-configure-rest-vm.md#user-assigned-identity) och [konfigurera hanterade identiteter på en VM-skalningsuppsättning med CURL ](qs-configure-rest-vmss.md#user-assigned-identity).


