---
title: Visa tjänstens huvudnamn för en hanterad identitet med PowerShell - Azure AD
description: Steg-för-steg-instruktioner för visning av tjänstens huvudnamn för en hanterad identitet med PowerShell.
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
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33fc6fbfd7c47b5809e8aff8ee9806c9eeac1162
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298690"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Visa tjänstens huvudnamn för en hanterad identitet med PowerShell

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du visar tjänstens huvudnamn för en hanterad identitet med PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).
- Aktivera [systemtilldelade identitet på en virtuell dator](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) eller ett [virtuellt program](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).
- Installera den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Visa tjänstens huvudnamn

Följande kommando visar hur du visar tjänstens huvudnamn för en virtuell dator eller ett program med systemtilldelad identitet aktiverad. Ersätt `<VM or application name>` med dina egna värderingar.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du visar Azure AD-tjänsthuvudnamn med PowerShell finns i [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


