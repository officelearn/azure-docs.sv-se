---
title: B2B-samarbete användaranspråk mappning i Azure Active Directory | Microsoft Docs
description: Anpassa användaranspråk som har utfärdats i SAML-token för Azure Active Directory (AD Azure) B2B-användare.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/06/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: bab339a07503a042f459c0933e92e0ed4a4fb27e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>B2B-samarbete användaranspråk mappning i Azure Active Directory

Azure Active Directory (AD Azure) stöder anpassa anspråk som utfärdats i SAML-token för B2B-samarbete användare. När en användare autentiseras för utfärdar Azure AD en SAML-token till appen som innehåller information (eller anspråk) om användaren som unikt identifierar dem. Som standard innehåller detta användarens användarnamn, e-postadress, Förnamn och efternamn.

I den [Azure-portalen](https://portal.azure.com), du kan visa eller redigera de anspråk som skickas i en SAML-token till programmet. Om du vill komma åt inställningarna, Välj **Azure Active Directory** > **företagsprogram** > det program som har konfigurerats för enkel inloggning > **enkel inloggning** . SAML-token inställningar i den **användarattribut** avsnitt.

![Visar attributen för SAML-token i Användargränssnittet](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

Det finns två möjliga orsaker till varför du kan behöva redigera anspråk som utfärdats i SAML-token:

1. Programmet kräver en annan uppsättning anspråk URI: er eller anspråksvärden.

2. Programmet kräver att NameIdentifier anspråket blir något annat än användarens huvudnamn (UPN) som lagras i Azure AD.

Information om hur du lägger till och redigera anspråk finns i [anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory](develop/active-directory-saml-claims-customization.md).

Användare, mappning NameID och UPN cross-klient som inte av säkerhetsskäl för B2B-samarbete.

## <a name="next-steps"></a>Nästa steg

- Information om B2B-samarbete användaregenskaper finns [egenskaperna för en användare för Azure Active Directory B2B-samarbete](active-directory-b2b-user-properties.md).
- Information om användartoken för B2B-samarbete användare finns [förstå användartoken i Azure AD B2B-samarbete](active-directory-b2b-user-token.md).

