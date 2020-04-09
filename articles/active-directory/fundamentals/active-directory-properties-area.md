---
title: Lägg till organisationens sekretessinformation – Azure Active Directory | Microsoft-dokument
description: Instruktioner om hur du lägger till organisationens sekretessinformation i området Egenskaper för Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343671c1d9ee82950a9822648f9831588da7e9f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876192"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Lägga till organisationens sekretessinformation med Azure Active Directory
I den här artikeln beskrivs hur en klientadministratör kan lägga till sekretessrelaterad information till en organisations Azure Active Directory-klientorganisation (Azure AD) via Azure-portalen.

Vi rekommenderar starkt att du lägger till både din globala integritetskontakt och organisationens sekretesspolicy, så att dina interna anställda och externa gäster kan granska dina policyer. Eftersom sekretesspolicyer skapas och skräddarsys unikt för varje företag rekommenderar vi starkt att du kontaktar en advokat för hjälp.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Lägga till din sekretessinformation på Azure AD
Du lägger till din organisations sekretessinformation i området **Egenskaper** i Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Så här öppnar du området Egenskaper och lägger till din sekretessinformation

1.    Logga in på Azure-portalen som klientadministratör.

2.    I det vänstra navigeringsfältet väljer du **Azure Active Directory**och väljer sedan **Egenskaper**.

    Området **Egenskaper** visas.

    ![Azure AD-egenskapsområdet som belyser området för sekretessinformation](media/active-directory-properties-area/properties-area.png)

3.    Lägg till din sekretessinformation för dina anställda:

    - **Teknisk kontakt.** Skriv e-postadressen för den person som ska kontaktas för teknisk support inom organisationen.
    
    - **Global integritetskontakt.** Skriv e-postadressen för den person som ska kontaktas för frågor om personlig datasekretess. Den här personen är också den som Microsoft kontaktar om det finns ett dataintrång. Om det inte finns någon person i listan här kontaktar Microsoft dina globala administratörer.

    - **Url till sekretesspolicyn.** Skriv länken till organisationens dokument som beskriver hur din organisation hanterar både intern och extern gästs datasekretess.

        >[!Important]
        >Om du inte inkluderar din egen sekretesspolicy eller din integritetskontakt ser dina externa gäster text i rutan **Granskningsbehörigheter** som säger ** <att _ditt organisationsnamn_> inte har angett länkar till deras villkor som du kan granska.** En gästanvändare ser till exempel det här meddelandet när de får en inbjudan att komma åt en organisation via B2B-samarbete.

        ![B2B Samarbetsgranskningsbehörigheterruta med meddelande](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.    Välj **Spara**.

## <a name="next-steps"></a>Nästa steg
- [Inlösen av Azure Active Directory B2B-samarbetsinbjudan](../b2b/redemption-experience.md)
- [Lägga till eller ändra profilinformation för en användare i Azure Active Directory](active-directory-users-profile-azure-portal.md)
