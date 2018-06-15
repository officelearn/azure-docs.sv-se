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
ms.openlocfilehash: f2617fb79fc20fcb385c7e2fc3596245ab18caba
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260297"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>B2B-samarbete användaranspråk mappning i Azure Active Directory

Azure Active Directory (AD Azure) stöder anpassa anspråk som utfärdats i SAML-token för B2B-samarbete användare. När en användare autentiseras för utfärdar Azure AD en SAML-token till appen som innehåller information (eller anspråk) om användaren som unikt identifierar dem. Som standard innehåller detta användarens användarnamn, e-postadress, Förnamn och efternamn.

I den [Azure-portalen](https://portal.azure.com), du kan visa eller redigera de anspråk som skickas i en SAML-token till programmet. Om du vill komma åt inställningarna, Välj **Azure Active Directory** > **företagsprogram** > det program som har konfigurerats för enkel inloggning > **enkel inloggning** . SAML-token inställningar i den **användarattribut** avsnitt.

![Visar attributen för SAML-token i Användargränssnittet](media/claims-mapping/view-claims-in-saml-token.png)

Det finns två möjliga orsaker till varför du kan behöva redigera anspråk som utfärdats i SAML-token:

1. Programmet kräver en annan uppsättning anspråk URI: er eller anspråksvärden.

2. Programmet kräver att NameIdentifier anspråket blir något annat än användarens huvudnamn (UPN) som lagras i Azure AD.

Information om hur du lägger till och redigera anspråk finns i [anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Användare, mappning NameID och UPN cross-klient som inte av säkerhetsskäl för B2B-samarbete.

## <a name="next-steps"></a>Nästa steg

- Information om B2B-samarbete användaregenskaper finns [egenskaperna för en användare för Azure Active Directory B2B-samarbete](user-properties.md).
- Information om användartoken för B2B-samarbete användare finns [förstå användartoken i Azure AD B2B-samarbete](user-token.md).

