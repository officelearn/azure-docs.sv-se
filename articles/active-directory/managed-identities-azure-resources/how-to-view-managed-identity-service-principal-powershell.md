---
title: Visa tjänstens huvud namn för en hanterad identitet med PowerShell – Azure AD
description: Stegvisa instruktioner för att Visa tjänstens huvud namn för en hanterad identitet med hjälp av PowerShell.
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
ms.openlocfilehash: 9ac2d1d4fc5e669f63bff49a7948db74ec1baa62
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184114"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Visa tjänstens huvud namn för en hanterad identitet med hjälp av PowerShell

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du visar tjänstens huvud namn för en hanterad identitet med hjälp av PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto kan du [Registrera dig för ett kostnads fritt konto](https://azure.microsoft.com/free/).
- Aktivera [systemtilldelad identitet på en virtuell dator eller ett](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) [program](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Installera den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Visa tjänstens huvud namn

Följande kommando visar hur du visar tjänstens huvud namn för en virtuell dator eller ett program med systemtilldelad identitet aktive rad. Ersätt `<VM or application name>` med dina egna värden.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du visar Azure AD-tjänstens huvud namn med hjälp av PowerShell finns i [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


