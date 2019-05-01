---
title: Ändra namnet eller logotyp i ett företagsprogram i Azure Active Directory | Microsoft Docs
description: Så här ändrar du namnet eller en anpassad företagsprogram i Azure Active Directory-logotyp
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbebc528227ebb06bd66e31b802dd4fd618d6a99
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718751"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Ändra namn eller ett företagsprogram i Azure Active Directory-logotyp

Det är enkelt att ändra namnet eller logotyp för ett anpassade företagsprogram i Azure Active Directory (AD Azure). Du måste ha behörighet att göra dessa ändringar, och du måste vara skaparen av anpassade program.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Hur ändrar jag ett företagsprogram namn eller logotyp?

1. Logga in på den [Azure Active Directory-portalen](https://aad.portal.azure.com/) med ett konto som är en global administratör för katalogen. Den **Azure Active Directory Administrationscenter** visas.
2. I den vänstra rutan väljer du **Företagsprogram**. Lista över dina företagsprogram visas.
3. Välj ett program. Översikt över appen på sidan visas.
4. I översiktsfönstret program under den **hantera** väljer **egenskaper**. Den **egenskaper** visas.
5. Om du vill ändra namnet, väljer du den **namn** rutan, Skriv det nya namnet och tryck på **RETUR**.
6. Om du vill ändra logotypen, hitta den **logotyp** och välj mappikonen bredvid den **Välj en fil** som programmets aktuella logotyp bilden nedan.

   ![Att välja kommandot Egenskaper](./media/change-name-or-logo-portal/change-logo.png)

   Annars, om du inte ändra logotyp, går du till steg 8.
7. Välj den fil du vill i filväljaren, som den nya logotypen. Filens namn visas i rutan under den aktuella fyrkantig logotyp.

   > [!NOTE]
   > Azure kräver med logobilden ska vara en PNG-fil och gränser används på bredden och höjden filstorlek.
8. Välj **Spara**. Om du väljer en ny logotyp och den **logotyp** fältets bild ändras för att återspegla den nya logotypfilen.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Visa din organisations grupper och medlemmar i Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en företagsapp](assign-user-or-group-access-portal.md)
* [Ta bort en användare eller grupp från en företagsapp](remove-user-or-group-access-portal.md)
* [Inaktivera användarinloggningar för en företagsapp](disable-user-sign-in-portal.md)
