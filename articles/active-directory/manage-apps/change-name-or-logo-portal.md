---
title: Ändra namn eller logo typ för ett företags program i Azure AD
description: Ändra namn eller logo typ för ett anpassat företags program i Azure Active Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/05/2019
ms.author: kenwith
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f8902f306f50659725bc4b12fbbb6fee7563c4b
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763677"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Ändra namn eller logo typ för ett företags program i Azure Active Directory

Det är enkelt att ändra namn eller logo typ för ett anpassat företags program i Azure Active Directory (Azure AD). Du måste ha rätt behörighet för att göra dessa ändringar och du måste vara skaparen av det anpassade programmet.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Hur gör jag för att ändra företags programmets namn eller logo typ?

1. Logga in på [Azure Active Directory Portal](https://aad.portal.azure.com/) med ett konto som är en global administratör för katalogen. Sidan **Azure Active Directory administrations Center** visas.
2. I den vänstra rutan väljer du **Företagsprogram**. Listan med dina företags program visas.
3. Välj ett program. Sidan program översikt visas.
4. I fönstret program översikt väljer du **Egenskaper**under rubriken **Hantera** . Sidan **Egenskaper** visas.
5. Om du vill ändra namnet markerar du rutan **namn** , skriver det nya namnet och trycker på **RETUR**.
6. Om du vill ändra logo typen letar du reda på fältet **logo typ** och väljer mappikonen bredvid rutan **Välj en fil** , som är under programmets aktuella logo typ bild.

   ![Välja egenskaper kommandot](./media/change-name-or-logo-portal/change-logo.png)

   Annars, om du inte ändrar logo typen, går du till steg 8.
7. I fil väljaren väljer du den fil som du vill använda som ny logo typ. Namnet på filen visas i rutan under den aktuella logo typ bilden.

   > [!NOTE]
   > För Azure krävs att logo bilden är en PNG-fil och den använder gränser för bredd, höjd och fil storlek. Anpassade logo typer måste vara exakt 215 &times; 215 bild punkter i storlek och vara i PNG-format. Vi rekommenderar att du använder en solid färg bakgrund utan genomskinlighet i din program logo typ för att visa användare.
8. Välj **Spara**. Om du väljer en ny logo typ, ändras **logo** fältets bild så att den återspeglar den nya logo typ filen.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Visa organisationens grupper och medlemmar i Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en företags app](assign-user-or-group-access-portal.md)
* [Ta bort en användare eller grupp tilldelning från en företags app](remove-user-or-group-access-portal.md)
* [Inaktivera användar inloggningar för en företags app](disable-user-sign-in-portal.md)
