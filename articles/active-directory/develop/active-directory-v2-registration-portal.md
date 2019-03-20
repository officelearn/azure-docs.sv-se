---
title: Appregistrering Portal hjälpavsnitt | Microsoft Docs
description: En beskrivning av olika funktioner i portalen för registrering av Microsoft-app.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77b5185056329113ee1fd17fa3ed3f364380ca2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095882"
---
# <a name="app-registration-reference"></a>Referens för registrering av appar
Det här dokumentet ger kontext och beskrivningar av olika funktioner i den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/).

> [!NOTE]
> Vi kommer inte längre stöd för registrering och hantering av Konvergerad och Azure AD-program i den [Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/) startar maj 2019. Vi rekommenderar att du hantera dina befintliga program och registrera nya program med hjälp av den [appregistreringar (förhandsversion)](https://aka.ms/appregistrations) upplevelse i Azure-portalen.

## <a name="my-applications-or-converged-applications"></a>Mina program eller konvergerade program
Den här listan innehåller alla dina program som har registrerats för användning med Azure AD v2.0-slutpunkten. Dessa program möjlighet att logga in användare med både personliga Microsoft-konton och arbets-/ skolkonton från Azure Active Directory. Läs mer om Azure AD v2.0-slutpunkten i den [v2.0 översikt](active-directory-appmodel-v2-overview.md). Dessa program kan också användas för att integrera med autentiseringsslutpunkt för Microsoft-konto `https://login.live.com`.

## <a name="azure-ad-only-applications"></a>Endast Azure AD-program
Den här listan innehåller alla dina program som har registrerats för användning med Azure AD v1.0-slutpunkten. Dessa program kan bara ha möjlighet att logga in användare med arbets-/ skolkonton från Azure Active Directory. Den här listan innehåller program som har registrerats med hjälp av den **appregistreringar** användarupplevelsen i den [Azure-portalen](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Live SDK-program
Den här listan innehåller alla dina program som har registrerats för eget bruk med Microsoft-konto. De är inte aktiverade för användning med Azure Active Directory. Det här är hittar du alla program som tidigare hade registrerats med MSA-utvecklarportalen på `https://account.live.com/developers/applications`. Alla funktioner som du tidigare har utförts på `https://account.live.com/developers/applications` nu kan utföras i den här nya portalen `https://apps.dev.microsoft.com`.

## <a name="application-secrets"></a>Programhemligheter
Hemligheter är autentiseringsuppgifter som tillåter programmet att utföra tillförlitlig [klientautentisering](https://tools.ietf.org/html/rfc6749#section-2.3) med Azure AD. I OAuth och OpenID Connect, en programhemlighet ofta kallas en `client_secret`. I version 2.0-protokollet, alla program som tar emot en säkerhetstoken på en webbplats för adresserbara (med hjälp av en `https` schema) måste använda en programhemlighet för att identifiera sig för Azure AD vid inlösen av den säkerhetstoken. Alla interna klienter som tar emot token på en enhet kommer dessutom att tillåts inte från att använda en programhemlighet för klientautentisering. Detta minskar sannolikheten för lagringen av hemligheter i osäkert miljöer.

Varje app kan innehålla två giltiga programhemligheter vid en given tidpunkt. Genom att två hemligheter, har du möjligheten att utföra regelbundna nyckelförnyelse över programmets hela miljön. När du har migrerat hela ditt program till en ny hemlighet, kan du ta bort den gamla hemligheten och etablera en ny.

För närvarande tillåts bara två typer av programhemligheter i portalen för registrering av appen. Välja **generera nytt lösenord** genererar och lagrar en delad hemlighet i respektive datalagret, som du kan använda i ditt program. Välja **Generera ny nyckel** skapar ett nytt offentligt/privat nyckelpar som kan hämtas och används för klientautentisering till Azure AD. Välja **ladda upp offentlig nyckel** kan du använda din egen offentligt/privat nyckelpar.
Du måste överföra ett certifikat som innehåller en offentlig nyckel.

## <a name="profile"></a>Profil
Profilavsnittet för portalen för registrering av appen kan användas för att anpassa inloggningssidan för ditt program. Just nu kan du ändra inloggning sidans programlogotyp, villkoren i tjänstens URL och URL för sekretesspolicy. Logotypen måste vara en transparent bild på 48 x 48 eller 50 x 50 bildpunkter i en GIF-, PNG- eller JPEG-fil som är på högst 15 kB. Försök ändra värdena och visa den resulterande inloggningssidan!

## <a name="live-sdk-support"></a>Live SDK-Support
När du aktiverar ”Live SDK stöd” alla programhemligheter som du skapar kommer att tillhandahållas till Azure AD och Account datalager. På så sätt kan dina program för att integrera direkt med Microsoft Account service (login.live.com). Om du vill skapa en app med Account direkt (i stället för med Azure AD v2.0-slutpunkten) bör du kontrollera Live SDK-stöd är aktiverat.

Inaktivera stöd för Live SDK säkerställer att programhemlighet endast har skrivits till Azure AD-data lagras. Azure AD-data store införlivar företagsklass förordningar som tillåter att den uppfyller vissa standarder, till exempel FISMA-efterlevnad. Om du aktiverar Live SDK stöd kan ditt program inte uppnå efterlevnad med några av dessa standarder.

Om du bara planerar att använda Azure AD v2.0-slutpunkten, kan du inaktivera stöd för Live SDK på ett säkert sätt.

