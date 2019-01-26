---
title: B2B-användare Anspråksmappning i Azure Active Directory | Microsoft Docs
description: Anpassa användaranspråk som har utfärdats i SAML-token för Azure Active Directory (Azure AD) B2B-användare.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: dd6be92bcd519dac369c5c21bb57b0b64ef6bfd1
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075980"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>B2B-användare Anspråksmappning i Azure Active Directory

Azure Active Directory (Azure AD) stöder anpassa anspråk som utfärdats i SAML-token för användare i B2B-samarbetet. När en användare autentiseras till programmet utfärdar Azure AD en SAML-token till appen som innehåller information (eller anspråk) om användaren som unikt identifierar dem. Som standard innehåller detta användarens användarnamn, e-postadress, Förnamn och efternamn.

I den [Azure-portalen](https://portal.azure.com), du kan visa eller redigera de anspråk som skickas i SAML-token för programmet. Om du vill komma åt inställningarna, Välj **Azure Active Directory** > **företagsprogram** > det program som har konfigurerats för enkel inloggning > **enkel inloggning** . SAML-token-inställningar i den **användarattribut** avsnittet.

![Visar SAML-tokenattribut i Användargränssnittet](media/claims-mapping/view-claims-in-saml-token.png)

Det finns två möjliga orsaker till varför du kan behöva redigera de anspråk som utfärdats i SAML-token:

1. Programmet kräver en annan uppsättning anspråk URI: er eller anspråksvärden.

2. Programmet kräver NameIdentifier-anspråket ska vara något annat än användarens huvudnamn (UPN) som lagras i Azure AD.

Information om hur du lägger till och redigera anspråk finns i [anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Användare, mappa NameID- och UPN flera klienter som inte av säkerhetsskäl för B2B-samarbete.

## <a name="next-steps"></a>Nästa steg

- Läs om hur egenskaper för användare av B2B-samarbete [egenskaperna för en användare för Azure Active Directory B2B-samarbete](user-properties.md).
- Läs om hur användartoken för B2B-samarbetare [förstå användartoken i Azure AD B2B-samarbete](user-token.md).

