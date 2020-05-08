---
title: Konfigurera slutpunkter
description: Hur du hittar autentiserings slut punkter för ett anpassat program som du utvecklar eller registrerar med Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 043dc4f6e57620f58a1cf5f76db755703421800f
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82778744"
---
# <a name="how-to-configure-endpoints"></a>Konfigurera slut punkter

Du kan hitta autentiserings slut punkter för ditt program i [Azure Portal](https://portal.azure.com).

-   Navigera till [Azure Portal](https://portal.azure.com).

-   Klicka på **Azure Active Directory**i det vänstra navigerings fönstret.

-   Klicka på **app-registreringar** och välj **slut punkter**.

-   Då öppnas sidan **slut punkter** , som visar en lista över alla autentiserings slut punkter för din klient.

-   Använd den slut punkt som är specifik för det autentiseringsprotokoll som du använder, tillsammans med program-ID: t för att skapa en autentiserings förfrågan som är specifik för ditt program.

**Nationella moln** (till exempel Azure AD Kina, Tyskland och amerikanska myndigheter) har sin egen app registrerings Portal och Azure AD-autentiserings-slutpunkter. Läs mer i [Översikt över nationella moln](authentication-national-cloud.md).

## <a name="next-steps"></a>Nästa steg
[Utvecklarhandbok för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)
