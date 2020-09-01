---
title: Visa tjänstens huvud namn för en hanterad identitet i Azure Portal – Azure AD
description: Stegvisa instruktioner för att Visa tjänstens huvud namn för en hanterad identitet i Azure Portal.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: c88f3cdc880888162301ace5c896161f669d2ede
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269207"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Visa tjänstens huvud namn för en hanterad identitet i Azure Portal

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du visar tjänstens huvud namn för en hanterad identitet med hjälp av Azure Portal.

 > [!NOTE] 
 > Tjänstens huvud namn är företags program. 

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md).
- Om du inte redan har ett Azure-konto kan du [Registrera dig för ett kostnads fritt konto](https://azure.microsoft.com/free/).
- Aktivera [systemtilldelad identitet på en virtuell dator eller ett](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) [program](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Visa tjänstens huvud namn

Den här proceduren visar hur du visar tjänstens huvud namn för en virtuell dator med systemtilldelad identitet aktive rad (samma steg gäller för ett program).

1. Klicka på **Azure Active Directory** och sedan på **företags program**.
2. Under **program typ**väljer du **alla program** och klickar sedan på **Använd**.
3. I rutan Sök filter skriver du namnet på den virtuella dator eller det program som har hanterad identitet aktive rad eller väljer den i listan som visas.

   ![Visa Managed Identity service-huvudobjektet i portalen](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Nästa steg

[Hanterade identiteter för Azure-resurser](./overview.md)