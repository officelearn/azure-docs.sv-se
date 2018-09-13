---
title: Lägg till din organisations sekretess information i Azure AD | Microsoft Docs
description: Beskriver hur du lägger till din organisations sekretess info i området Azure Active Directory (Azure AD)-egenskaper.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: lizross
ms.reviewer: bpham
ms.custom: it-pro
ms.openlocfilehash: a34fa2b8c2d966af108664c219a222fb9a5b7abc
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35963977"
---
# <a name="how-to-add-your-organizations-privacy-info-in-azure-active-directory"></a>Anvisningar: Lägga till din organisations sekretess information i Azure Active Directory
Den här artikeln beskrivs hur en administratör kan lägga till sekretessrelaterade information till en organisations Azure Active Directory (Azure AD)-klient, via Azure portal.

Vi rekommenderar starkt att du lägger till både dina global sekretesskontakt och sekretesspolicyn för din organisation så att interna anställda och externa gäster kan granska dina principer. Eftersom sekretesspolicy unikt skapas och skräddarsydd för varje företag, rekommenderar vi du kontakta en jurist om du behöver hjälp.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="access-the-properties-area-to-add-your-privacy-info"></a>Åtkomst till egenskaper område för att lägga till din sekretess-information

1.  Logga in på Azure-portalen som en Innehavaradministratör.

2.  I det vänstra navigeringsfältet väljer **Azure Active Directory**, och välj sedan **egenskaper**.

    Den **egenskaper** området visas.

    ![Azure AD-egenskaper området markering info sekretessområdet](./media/active-directory-properties-area/properties-area.png)

3.  Lägg till din sekretess-information för dina anställda:

    - **Teknisk kontakt.** Ange e-postadressen för personen att kontakta teknisk support i din organisation.
    
    - **Global sekretesskontakt.** Ange e-postadress på kontaktpersonen för frågor om sekretess av personuppgifter. Den här personen finns också som Microsoft kontaktar om det finns en överträdelse av data. Om det finns ingen person som visas här, kontaktar Microsoft dina globala administratörer.

    - **URL till sekretesspolicy.** Ange en länk till din organisations dokument som beskriver hur din organisation hanterar både interna och externa gästens datasekretess.

        >[!Important]
        >Om du inte anger en anpassad sekretesspolicy eller din sekretesskontakt för, din externa gäster visas texten i den **granska behörigheter** rutan,  **< _ditt organisationsnamn_> tillhandahöll inte länkat till sina villkor som du kan granska**. Till exempel visas en gästanvändare det här meddelandet när de får en inbjudan att få åtkomst till en organisation via B2B-samarbete.

        ![B2B-samarbete granska behörigheter box med meddelande](./media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Välj **Spara**.

## <a name="next-steps"></a>Nästa steg
- [Inlösning av inbjudan Azure Active Directory B2B-samarbete](https://aka.ms/b2bredemption)
- [Lägga till eller ändra profilinformationen för en användare i Azure Active Directory](fundamentals/active-directory-users-profile-azure-portal.md)