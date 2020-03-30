---
title: Ändra namn eller logotyp för ett företagsprogram i Azure AD
description: Så här ändrar du namn eller logotyp för ett anpassat företagsprogram i Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d23849df75d1ab239eb269b462abb21df196e7e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138509"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Ändra namn eller logotyp för ett företagsprogram i Azure Active Directory

Det är enkelt att ändra namn eller logotyp för ett anpassat företagsprogram i Azure Active Directory (Azure AD). Du måste ha rätt behörighet för att kunna göra dessa ändringar och du måste vara skaparen av det anpassade programmet.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Hur ändrar jag ett företags programs namn eller logotyp?

1. Logga in på [Azure Active Directory-portalen](https://aad.portal.azure.com/) med ett konto som är en global administratör för katalogen. Sidan **Azure Active Directory admin center** visas.
2. I den vänstra rutan väljer du **Företagsprogram**. Listan över företagsprogram visas.
3. Välj ett program. Sidan för programöversikt visas.
4. Välj **Egenskaper**under rubriken Hantera i fönstret **Programöversikt.** Sidan **Egenskaper** visas.
5. Om du vill ändra namnet markerar du rutan **Namn,** skriver det nya namnet och trycker på **Retur**.
6. Om du vill ändra logotypen letar du reda på fältet **Logotyp** och väljer mappikonen bredvid rutan **Välj en fil,** som ligger under programmets aktuella logotypbild.

   ![Markera kommandot egenskaper](./media/change-name-or-logo-portal/change-logo.png)

   Annars går du till steg 8 om du inte ändrar logotypen.
7. Markera den fil du vill använda som ny logotyp i filväljaren. Namnet på filen visas i rutan under den aktuella logotypbilden.

   > [!NOTE]
   > Azure kräver att logotypbilden är en PNG-fil och tillämpar begränsningar för bredd, höjd och filstorlek. Anpassade logotyper måste vara exakt &times; 215 215 pixlar i storlek och vara i PNG-format. Vi rekommenderar att du använder en enfärgad bakgrund utan genomskinlighet i programlogotypen för att visas bäst för användarna.
8. Välj **Spara**. Om du väljer en **Logo** ny logotyp ändras logotypens bild för att återspegla den nya logotypfilen.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Visa organisationens grupper och medlemmar i Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en företagsapp](assign-user-or-group-access-portal.md)
* [Ta bort en användar- eller grupptilldelning från en företagsapp](remove-user-or-group-access-portal.md)
* [Inaktivera användarloggningar för en företagsapp](disable-user-sign-in-portal.md)
