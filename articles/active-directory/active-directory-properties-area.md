---
title: Lägg till din organisations sekretess information i Azure AD | Microsoft Docs
description: Beskriver hur du lägger till din organisations sekretess info området Azure Active Directory (AD Azure) egenskaper.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: lizross
ms.reviewer: bpham
ms.custom: it-pro
ms.openlocfilehash: 8cdf30ed09601a31529073eaedd4ab53780157d5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077552"
---
# <a name="how-to-add-your-organizations-privacy-info-in-azure-active-directory"></a>Anvisningar: Lägga till din organisations sekretess information i Azure Active Directory
Den här artikeln beskrivs hur en klientadministratör kan lägga till sekretessrelaterade information i organisationens Azure Active Directory (Azure AD)-klient via Azure-portalen.

Vi rekommenderar starkt att du lägger till både globala sekretess kontakten och din organisations sekretesspolicy så internt anställda och externa gäster kan granska dina principer. Eftersom sekretessavtal unikt skapas och skräddarsydda för varje företag, rekommenderar vi du kontakta en jurist för hjälp.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="access-the-properties-area-to-add-your-privacy-info"></a>Åtkomst till egenskaper område för att lägga till information om din sekretess

1.  Logga in på Azure-portalen som en Innehavaradministratör.

2.  På den vänstra navigeringsfält för, väljer **Azure Active Directory**, och välj sedan **egenskaper**.

    Den **egenskaper** område.

    ![Azure AD området syntaxmarkering informationsområdet sekretess](./media/active-directory-properties-area/properties-area.png)

3.  Lägg till din sekretess-information för dina anställda:

    - **Tekniska kontakt.** Skriv e-postadressen för personen att kontakta teknisk support i din organisation.
    
    - **Kontakta en global sekretess.** Skriv e-postadress på kontaktpersonen för frågor om sekretess personliga data. Den här personen är också som Microsoft kontaktar om det finns en överträdelse av data. Om det finns ingen person som listas här, kontaktar Microsoft dina globala administratörer.

    - **URL till sekretesspolicy.** Ange en länk till din organisations dokument som beskriver hur din organisation hanterar både interna och externa gästens datasekretess.

        >[!Important]
        >Om du inte anger en egen sekretesspolicy eller kontakta din sekretess, externa gästerna ser texten i den **granska behörigheter** rutan,  **< _ditt organisationsnamn_> inte innehåller länkar till villkor som du kan granska**. Till exempel visas en gästanvändare det här meddelandet när de får en inbjudan åtkomst till en organisation via B2B-samarbete.

        ![B2B-samarbete granska behörigheter rutan med meddelandet](./media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Välj **Spara**.

## <a name="next-steps"></a>Nästa steg
- [Azure Active Directory B2B-samarbete inbjudan inlösning](https://aka.ms/b2bredemption)
- [Lägga till eller ändra profilinformation för en användare i Azure Active Directory](/active-directory-users-profile-azure-portal.md)