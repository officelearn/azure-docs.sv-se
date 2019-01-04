---
title: Så här visar du tjänstens huvudnamn för en hanterad identitet i Azure portal
description: Stegvisa instruktioner för att visa tjänstens huvudnamn för en hanterad identitet i Azure-portalen.
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
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: c61220242a5a2ff84204c84b11aa132d00279e49
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726603"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Visa tjänstens huvudnamn för en hanterad identitet i Azure portal

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får lära du att visa tjänstens huvudnamn för en hanterad identitet med hjälp av Azure portal.

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto, [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).
- Aktivera [systemtilldelade identiteter på en virtuell dator](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) eller [program](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Visa tjänstens huvudnamn

Den här proceduren visar hur du kan visa tjänstens huvudnamn för en virtuell dator med systemtilldelade identiteter aktiverat (samma steg som gäller för ett program).

1. Klicka på **Azure Active Directory** och klicka sedan på **företagsprogram**.
2. Under **programtyp**, Välj **alla program**.
3. I rutan Sök filter skriver du namnet på den virtuella datorn eller program som har hanterad identitet aktiverad eller välj i listan som visas.

   ![Visa hanterad identitet tjänstens huvudnamn i portalen](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Nästa steg

[Hanterade identiteter för Azure-resurser](/azure/active-directory/managed-identities-azure-resources/overview)

