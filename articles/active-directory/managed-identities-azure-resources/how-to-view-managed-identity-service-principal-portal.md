---
title: Visa tjänstens huvudnamn för en hanterad identitet i Azure-portalen - Azure AD
description: Steg-för-steg-instruktioner för visning av tjänstens huvudnamn för en hanterad identitet i Azure-portalen.
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
ms.openlocfilehash: c12f15cc79d5329d028239ade4e18a853000bf01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298605"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>Visa tjänstens huvudnamn för en hanterad identitet i Azure-portalen

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du visar tjänstens huvudnamn för en hanterad identitet med Hjälp av Azure-portalen.

 > [!NOTE] 
 > Huvudnamn för tjänsten är Företagsprogram. 

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).
- Aktivera [systemtilldelade identitet på en virtuell dator](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) eller ett [virtuellt program](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>Visa tjänstens huvudnamn

Den här proceduren visar hur du visar tjänstens huvudnamn för en virtuell dator med systemtilldelad identitet aktiverad (samma steg gäller för ett program).

1. Klicka på **Azure Active Directory** och sedan på **Företagsprogram**.
2. Under **Programtyp**väljer du **Alla program** och klickar sedan på **Använd**.
3. I rutan Sökfilter skriver du namnet på den virtuella datorn eller programmet som har aktiverad hanterad identitet eller väljer det i listan som visas.

   ![Visa huvudnamn för hanterade identitetstjänsten i portalen](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Nästa steg

[Hanterade identiteter för Azure-resurser](/azure/active-directory/managed-identities-azure-resources/overview)

