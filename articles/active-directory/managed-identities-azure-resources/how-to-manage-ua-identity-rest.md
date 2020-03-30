---
title: Hantera användartilldelade hanterade identiteter med REST - Azure AD
description: Steg för steg instruktioner om hur du skapar, lista och ta bort en användartilldelade hanterad identitet för att göra REST API-anrop.
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
ms.date: 06/26/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39e108451e4c19e77e01b5bcc5d8dd21e86ad73a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547413"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Skapa, lista eller ta bort en användartilldelad hanterad identitet med REST API-anrop

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster möjlighet att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du skapar, listar och tar bort en användartilldelad hanterad identitet med CURL för att ringa REST API-anrop.

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Var noga med att granska [skillnaden mellan en systemtilldelad och användartilldelad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du använder Windows installerar du [Windows-undersystemet för Linux](https://msdn.microsoft.com/commandline/wsl/about) eller använder [Azure Cloud Shell](../../cloud-shell/overview.md) i Azure-portalen.
- Om du använder [Windows-undersystemet för Linux](https://msdn.microsoft.com/commandline/wsl/about) eller ett [Linux-distributionsoperativsystem](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) [installerar du den lokala Azure CLI-konsolen](/cli/azure/install-azure-cli).
- Om du använder den lokala Azure CLI-konsolen loggar du in på Azure med `az login` ett konto som är kopplat till Azure-prenumerationen som du vill distribuera eller hämta användartilldelade hanterade identitetsinformation.
- Hämta en bärare `az account get-access-token` åtkomsttoken med att utföra följande användartilldelade hanterade identitetsåtgärder.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet 

Om du vill skapa en användartilldelad hanterad identitet behöver ditt konto rolltilldelningen [Managed Identity Contributor.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Begär rubriker**

|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehållstyp*     | Krävs. Ange till `application/json`.        |
|*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.        |

**Begäran kropp**

|Namn  |Beskrivning  |
|---------|---------|
|location     | Krävs. Resursplats.        |

## <a name="list-user-assigned-managed-identities"></a>Lista användartilldelade hanterade identiteter

Om du vill lista/läsa en användartilldelad hanterad identitet behöver ditt konto rolltilldelningen [Managed Identity Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) eller Managed Identity [Contributor.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehållstyp*     | Krävs. Ange till `application/json`.        |
|*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en användartilldelad hanterad identitet

Om du vill ta bort en användartilldelad hanterad identitet behöver kontot rolltilldelningen [Managed Identity Contributor.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

> [!NOTE]
> Om du tar bort en användartilldelad hanterad identitet tas inte referensen bort från någon resurs som den har tilldelats. Så här tar du bort en användartilldelade hanterad identitet från en virtuell dator med CURL och läser [Ta bort en användartilldelad identitet från en Azure VM](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehållstyp*     | Krävs. Ange till `application/json`.        |
|*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.        |

## <a name="next-steps"></a>Nästa steg

Information om hur du tilldelar en användartilldelade hanterad identitet till en Azure VM/VMSS med CURL finns i [Konfigurera hanterade identiteter för Azure-resurser på en Azure VM med REST API-anrop](qs-configure-rest-vm.md#user-assigned-managed-identity) och [Konfigurera hanterade identiteter för Azure-resurser på en virtuell datorskalauppsättning med REST API-anrop](qs-configure-rest-vmss.md#user-assigned-managed-identity).
