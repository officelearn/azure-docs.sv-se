---
title: Hantera autentisering
titleSuffix: Azure Maps
description: Använd Azure Portal för att hantera autentisering i Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 69dda537beda1d1bec4f019e1d5cadd16bdd5b39
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126864"
---
# <a name="manage-authentication-in-azure-maps"></a>Hantera autentisering i Azure Maps

När du har skapat ett Azure Maps-konto skapas ett klient-ID och nycklar som stöder Azure Active Directory (Azure AD)-autentisering och autentisering med delad nyckel.

## <a name="view-authentication-details"></a>Visa information om autentisering

När du har skapat ett Azure Maps-konto genereras de primära och sekundära nycklarna. Vi rekommenderar att du använder en primär nyckel som en prenumerations nyckel när du [använder autentisering med delad nyckel för att anropa Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Du kan använda en sekundär nyckel i scenarier som löpande nyckel ändringar. Mer information finns i [autentisering i Azure Maps](https://aka.ms/amauth).

Du kan visa information om autentiseringen i Azure Portal. I ditt konto går du till menyn **Inställningar** och väljer **autentisering**.

> [!div class="mx-imgBorder"]
> ![Information om autentisering](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>Identifiera kategori och scenario

Beroende på programmets behov finns det specifika vägar för att skydda programmet. Azure AD definierar kategorier för att stödja en mängd olika autentiserings flöden. Se [program kategorier](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios#application-categories) för att förstå vilken kategori programmet passar.

> [!NOTE]
> Även om du använder autentisering med delad nyckel kan du använda kategorier och scenarier för att skydda programmet.

## <a name="determine-authentication-and-authorization"></a>Fastställa autentisering och auktorisering

I följande tabell beskrivs vanliga scenarier för autentisering och auktorisering i Azure Maps. Tabellen innehåller en jämförelse av de typer av skydd som varje scenario erbjuder.

> [!IMPORTANT]
> Microsoft rekommenderar att du implementerar Azure Active Directory (Azure AD) med rollbaserad åtkomst kontroll (RBAC) för produktions program.

| Scenario                                                                                    | Autentisering | Auktorisering | Utvecklings arbete | Drift ansträngning |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Trusted daemon/icke-interaktiv klient program](./how-to-secure-daemon-app.md)        | Delad nyckel     | Ej tillämpligt           | Medium             | Högt               |
| [Trusted daemon/icke-interaktiv klient program](./how-to-secure-daemon-app.md)        | Azure AD       | Högt          | Lågt                | Medium             |
| [Webb program med en enda sida med interaktiv enkel inloggning](./how-to-secure-spa-users.md) | Azure AD       | Högt          | Medel             | Medium             |
| [Webb program med en enda sida med icke-interaktiv inloggning](./how-to-secure-spa-app.md)      | Azure AD       | Högt          | Medel             | Medium             |
| [Webb program med interaktiv enkel inloggning](./how-to-secure-webapp-users.md)          | Azure AD       | Högt          | Högt               | Medel             |
| [IoT-enhet/indatamängds begränsad enhet](./how-to-secure-device-code.md)                     | Azure AD       | Högt          | Medel             | Medium             |

Länkarna i tabellen tar dig till detaljerad konfigurations information för varje scenario.

## <a name="view-role-definitions"></a>Visa roll definitioner

Om du vill visa RBAC-roller som är tillgängliga för Azure Maps går du till **åtkomst kontroll (IAM)**. Välj **roller**och Sök sedan efter roller som börjar med *Azure Maps*. Dessa Azure Maps roller är de roller som du kan bevilja åtkomst till.

> [!div class="mx-imgBorder"]
> ![Visa tillgängliga roller](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>Visa rolltilldelningar

Om du vill visa användare och appar som har beviljats RBAC för Azure Maps går du till **Access Control (IAM)**. Där väljer du **roll tilldelningar**och filtrerar sedan efter **Azure Maps**.

> [!div class="mx-imgBorder"]
> ![Visa användare och appar som har beviljats RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Begär token för Azure Maps

Begär en token från Azure AD-token-slutpunkten. Använd följande information i din Azure AD-begäran:

| Azure-miljö      | Azure AD-token-slutpunkt             | Resurs-ID för Azure              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Offentligt Azure-moln     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government molnet | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Mer information om hur du begär åtkomsttoken från Azure AD för användare och tjänst huvud namn finns i [scenarier för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios) och visa särskilda scenarier i tabellen med [scenarier](./how-to-manage-authentication.md#determine-authentication-and-authorization).

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Azure AD och Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Hitta API-användnings mått för ditt Azure Maps-konto:
> [!div class="nextstepaction"]
> [Visa användningsstatistik](how-to-view-api-usage.md)

Utforska exempel som visar hur du integrerar Azure AD med Azure Maps:

> [!div class="nextstepaction"]
> [Exempel på Azure AD-autentisering](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
