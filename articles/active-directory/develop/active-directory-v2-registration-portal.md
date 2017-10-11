---
title: "Registrering av appen företagsportal hjälpavsnitt | Microsoft Docs"
description: "En beskrivning av olika funktioner i portalen för registrering av Microsoft-app."
services: active-directory
documentationcenter: 
author: lnalepa
manager: mbaldwin
editor: 
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: lenalepa
ms.custom: aaddev
ms.openlocfilehash: c60499c425a7fd800f7ca9a5bac1fed5af73b801
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="app-registration-reference"></a>Referens för registrering
Det här dokumentet innehåller kontexten och beskrivningar av olika funktioner som finns i portalen för registrering av Microsoft App [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).

## <a name="my-applications"></a>Mina program
Den här listan innehåller alla dina program som har registrerats för användning med Azure AD v2.0-slutpunkten.  Programmen har möjlighet att logga in användare med både personliga konton från Microsoft-konto och arbete/skolkonton från Azure Active Directory.  Läs mer om Azure AD v2.0-slutpunkten i vår [v2.0 översikt](active-directory-appmodel-v2-overview.md).  Dessa program kan också användas för att integrera med Microsoft-konto autentisering endpoint `https://login.live.com`.

## <a name="live-sdk-applications"></a>Live SDK-program
Den här listan innehåller alla dina program som har registrerats för eget bruk med Microsoft-konto.  De är inte aktiverade för användning med Azure Active Directory som.  Det är där du hittar alla program som tidigare hade registrerats med MSA developer-portalen på `https://account.live.com/developers/applications`.  Alla funktioner som du tidigare har utförts på `https://account.live.com/developers/applications` nu kan utföras i den här nya portalen `https://apps.dev.microsoft.com`.  Om du har fler frågor om dina program för Microsoft-konto kontaktar du oss.

## <a name="application-secrets"></a>Programmet hemligheter
Programmet hemligheter är autentiseringsuppgifter som tillåter programmet att utföra tillförlitliga [klientautentisering](http://tools.ietf.org/html/rfc6749#section-2.3) med Azure AD.  OAuth-& OpenID Connect ett program hemligheter är vanligtvis kallas ett `client_secret`.  I version 2.0-protokollet är alla program som tar emot en säkerhetstoken på en webbplats för adresserbara (med hjälp av en `https` schemat) måste använda en programhemlighet identifiera sig själv till Azure AD på inlösning för att säkerhetstoken.  Dessutom alla interna klienter som får token på en enhet ska vara förbjudet från att använda en apphemligheten för klientautentisering, motverka lagring av hemligheter i osäker miljöer.

Varje app kan innehålla två giltigt program hemligheter vid en viss tidpunkt.  Genom att upprätthålla två hemligheter har ablilty att utföra regelbundna nyckelförnyelse över hela miljön i ditt program.  När du har migrerat i sin helhet ditt program till en ny hemlighet, kan du ta bort den gamla hemligheten och etablera en ny.

För tillfället tillåts bara två typer av program hemligheter i portalen för registrering av app.  Om du väljer **generera nya lösenord** ska generera och lagra en delad hemlighet i respektive datalagret, som du kan använda i ditt program.  Om du väljer **Generera en ny nyckel** skapar ett nytt offentligt/privat nyckelpar som kan hämtas och används för klientautentisering till Azure AD.

## <a name="profile"></a>Profil
Avsnittet profil i portalen för registrering av appen kan användas för att anpassa inloggningssidan för ditt program.  Du kan ändra inloggning sidans programmet logotyp, villkor för tjänst-URL och sekretesspolicy för tillfället.  Logotypen måste vara en transparent bild på 48 x 48 eller 50 x 50 bildpunkter i en GIF-, PNG- eller JPEG-fil som är på högst 15 kB.  Försök ändra värdena och visa den resulterande logga i sidan!

## <a name="live-sdk-support"></a>Live SDK-Support
När du aktiverar ”Live SDK stöd” alla hemligheter för program som du skapar kommer att tillhandahållas till Azure AD och Account dataarkiv.  Detta gör att programmet kan integreras direkt med tjänsten Account (login.live.com).  Om du vill skapa en app med Account direkt (i stället för med Azure AD v2.0-slutpunkten) bör du kontrollera att Live SDK-stöd är aktiverat.

Inaktivera stöd för Live SDK säkerställer att programmet hemlighet bara skrivs till Azure AD-data lagras.  Azure AD-data store omfattar företagsklass regler som tillåter att uppfylla vissa krav, till exempel FISMA kompatibilitet.  Om du aktiverar stöd för Live SDK, kan programmet inte uppnå kompatibilitet med vissa av dessa standarder.

Om du bara tänker använda Azure AD v2.0-slutpunkten kan du inaktivera stöd för Live SDK på ett säkert sätt.

