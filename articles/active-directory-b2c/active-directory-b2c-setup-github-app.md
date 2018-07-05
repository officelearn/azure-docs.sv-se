---
title: Konfiguration av GitHub identitets-provider i Azure Active Directory B2C | Microsoft Docs
description: Tillhandahålla registrera dig och logga in till kunder med GitHub-konton i dina program som skyddas av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3754a169b301bac97f3e12d10b754222e3cf325d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443349"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-github-accounts"></a>Azure Active Directory B2C: Ge registrera dig och logga in till konsumenter med hjälp av GitHub-konton

> [!NOTE]
> Den här funktionen är en förhandsversion.
> 

Den här artikeln visar hur du aktiverar inloggning för användare med en GitHub-konto.

## <a name="create-a-github-oauth-application"></a>Skapa ett GitHub OAuth-program

Om du vill använda GitHub som identitetsprovider i Azure AD B2C måste du skapa en GitHub OAuth-app och ange rätt parametrar.

1. Gå till den [GitHub utvecklarinställningar](https://github.com/settings/developers) när du loggar in på GitHub.
1. Klicka på **ny OAuth-App**
1. Ange en **programnamn** och din **Hemsides-URL**.
1. För den **Motringnings-URL för auktorisering**, ange `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Ersätt **{klient}** med Azure AD B2C-klientens namn (exempel: contosob2c.onmicrosoft.com).

    >[!NOTE]
    >Värdet för ”klient” måste skrivas med små bokstäver i de **inloggnings-URL**.

1. Klicka på **registrera program**.
1. Spara värdena för **klient-ID** och **Klienthemlighet**. Du måste båda i nästa avsnitt.

## <a name="configure-github-as-an-identity-provider-in-your-azure-ad-b2c-tenant"></a>Konfigurera GitHub som identitetsprovider i din Azure AD B2C-klient

1. Följ dessa steg för att [gå till B2C-funktionsbladet](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure portal.
1. Klicka på B2C-funktionsbladet **identitetsprovidrar**.
1. Klicka på **+Lägg till** överst på bladet.
1. Ange ett eget **namn** för konfigurationen av identity-providern. Till exempel ange ”GitHub”.
1. Klicka på **identifiera providertyp**väljer **GitHub**, och klicka på **OK**.
1. Klicka på **ställa in den här identitetsprovidern** och ange klient-ID och klienthemlighet för GitHub OAuth-programmet som du kopierade tidigare.
1. Klicka på **OK** och klicka sedan på **skapa** att spara din GitHub-konfiguration.

## <a name="next-steps"></a>Nästa steg

Skapa eller redigera en [inbyggd princip](active-directory-b2c-reference-policies.md) och lägga till GitHub som identitetsprovider.