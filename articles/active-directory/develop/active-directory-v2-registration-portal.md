---
title: Referens för appregistreringsportal | Azure
titleSuffix: Microsoft identity platform
description: En beskrivning av funktionerna i Microsofts appregistreringsportal.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ms.openlocfilehash: 26ebee446523c138569b9d5379c9a5e1b9e93e7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76698449"
---
# <a name="app-registration-reference"></a>Referens för registrering av appar

Det här dokumentet innehåller kontext och beskrivningar av olika funktioner som finns i [appregistreringsupplevelsen](https://aka.ms/appregistrations) i Azure-portalen.

## <a name="my-applications-or-converged-applications"></a>Mina program eller konvergerade program

Den här listan innehåller alla dina program som har registrerats för användning med slutpunkten för Microsoft identity platform (v2.0). Dessa program har möjlighet att logga in användare med både personliga Microsoft-konton och arbets-/skolkonton från Azure Active Directory. Mer information om slutpunkten för identitetsplattformen finns i [översikten v2.0](active-directory-appmodel-v2-overview.md). Dessa program kan också användas för att integrera `https://login.live.com`med slutpunkten för Microsoft-kontoautentisering.

## <a name="azure-ad-only-applications"></a>Azure AD-program

Den här listan innehåller alla dina program som har registrerats för användning med slutpunkten Azure AD v1.0. Dessa program har bara möjlighet att logga in användare med arbets-/skolkonton från Azure Active Directory. Den här listan innehåller program som har registrerats med hjälp av **appregistreringsupplevelsen** i [Azure-portalen](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Live SDK-program

Den här listan innehåller alla dina program som är registrerade för användning endast med Microsoft-konto. De är inte aktiverade för användning med Azure Active Directory. Här hittar du alla program som tidigare har registrerats `https://account.live.com/developers/applications`hos MSA-utvecklarportalen på . Alla funktioner som du `https://account.live.com/developers/applications` tidigare utfört på kan nu utföras i [Appregistreringar](https://aka.ms/appregistrations).

## <a name="application-secrets"></a>Program Hemligheter

Programhemligheter är autentiseringsuppgifter som gör att ditt program kan utföra tillförlitlig [klientautentisering](https://tools.ietf.org/html/rfc6749#section-2.3) med Azure AD. I OAuth & OpenID Connect kallas en programhemlighet ofta `client_secret`för en . I v2.0-protokollet måste alla program som tar emot en säkerhetstoken på en webbplatsadresserbar plats (med hjälp av ett `https` schema) använda en programhemlighet för att identifiera sig till Azure AD vid inlösen av säkerhetstoken. Dessutom kommer alla inbyggda klienter som tar emot token på en enhet att förbjudas att använda en programhemlighet för att utföra klientautentisering. Detta avskräcker lagring av hemligheter i osäkra miljöer.

Varje app kan innehålla två giltiga programhemligheter när som helst. Genom att upprätthålla två hemligheter har du möjlighet att utföra periodisk nyckelvältning över hela programmets miljö. När du har migrerat hela programmet till en ny hemlighet kan du ta bort den gamla hemligheten och etablera en ny.

För närvarande tillåts endast två typer av programhemligheter i appregistreringsportalen. Om du väljer **Generera nytt lösenord** genereras och lagras en delad hemlighet i respektive datalager, som du kan använda i ditt program. Om du väljer **Generera nytt nyckelpar** skapas ett nytt offentligt/privat nyckelpar som kan hämtas och användas för klientautentisering till Azure AD. Om du väljer **Ladda upp offentlig nyckel** kan du använda ditt eget offentliga/privata nyckelpar.
Du måste ladda upp ett certifikat som innehåller en offentlig nyckel.

## <a name="profile"></a>Profil

Profilavsnittet på appregistreringsportalen kan användas för att anpassa inloggningssidan för ditt program. För närvarande kan du ändra inloggningssidans programlogotyp, villkoren för tjänstens WEBBADRESS och webbadressen till sekretesspolicyn. Logotypen måste vara en genomskinlig bild på 48 x 48 eller 50 x 50 pixlar i en GIF-, PNG- eller JPEG-fil som är 15 kB eller mindre. Prova att ändra värdena och visa den resulterande inloggningssidan!

## <a name="live-sdk-support"></a>Live SDK Support

När du aktiverar "Live SDK-support" etableras alla programhemligheter som du skapar i både Azure AD- och Microsoft-kontodatalager. På så sätt kan programmet integreras direkt med Tjänsten Microsoft Account (login.live.com). Om du vill skapa en app med Microsoft-konto direkt (i stället för att använda v2.0-slutpunkten) bör du se till att Live SDK-supporten är aktiverad.

Inaktivera Live SDK-stöd säkerställer att programhemligheten endast skrivs in i Azure AD-datalagret. Azure AD-datalagret innehåller regler i företagsklass som gör att det uppfyller vissa standarder, till exempel FISMA-efterlevnad. Om du aktiverar Live SDK-stöd kanske ditt program inte uppfyller vissa av dessa standarder.

Om du bara planerar att använda v2.0-slutpunkten kan du på ett säkert sätt inaktivera Live SDK-stöd.
