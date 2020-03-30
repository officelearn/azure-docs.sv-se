---
title: B2B-användare för samarbete – Azure Active Directory
description: Anpassa användaranspråk som utfärdas i SAML-token för Azure Active Directory (Azure AD) B2B-användare.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3508865d9f89501cf70e09087c6a609beb6662b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273190"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>B2B-samarbetsanvändaranspråksmappning i Azure Active Directory

Azure Active Directory (Azure AD) stöder anpassning av anspråk som utfärdas i SAML-token för B2B-samarbetsanvändare. När en användare autentiserar till programmet utfärdar Azure AD en SAML-token till appen som innehåller information (eller anspråk) om användaren som unikt identifierar dem. Som standard inkluderar detta användarens användarnamn, e-postadress, förnamn och efternamn.

I [Azure-portalen](https://portal.azure.com)kan du visa eller redigera anspråk som skickas i SAML-token till programmet. Om du vill komma åt inställningarna väljer du **Azure Active Directory** > **Enterprise-program** > programmet som är konfigurerat för enkel inloggning > **Enkel inloggning**. Se INSTÄLLNINGARNA för SAML-token i avsnittet **Användarattribut.**

![Visar SAML-tokenattributen i användargränssnittet](media/claims-mapping/view-claims-in-saml-token.png)

Det finns två möjliga orsaker till varför du kan behöva redigera anspråk som utfärdas i SAML-token:

1. Programmet kräver en annan uppsättning anspråks-URI:er eller anspråksvärden.

2. Programmet kräver att NameIdentifier-anspråket är något annat än användarens huvudnamn (UPN) som lagras i Azure AD.

Information om hur du lägger till och redigerar anspråk finns [i Anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

För B2B-samarbetsanvändare förhindras mappning av NameID och UPN-korsklientering av säkerhetsskäl.

## <a name="next-steps"></a>Nästa steg

- Information om användaregenskaper för B2B-samarbete finns i [Egenskaper för en Azure Active Directory B2B-samarbetsanvändare](user-properties.md).
- Information om användartoken för användare av B2B-samarbete finns [i Förstå användartoken i Azure AD B2B-samarbete](user-token.md).

