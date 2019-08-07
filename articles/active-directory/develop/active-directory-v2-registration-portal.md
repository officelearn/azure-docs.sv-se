---
title: Hjälp avsnitt för appens registrerings Portal | Microsoft Docs
description: En beskrivning av olika funktioner i registrerings portalen för Microsoft-appar.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2018
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 357ef290bd24d1d0f48830913511b8cc8944a239
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835227"
---
# <a name="app-registration-reference"></a>Referens för registrering av appar
Det här dokumentet innehåller kontext och beskrivningar av olika funktioner som finns på [program registrerings portalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/).

> [!NOTE]
> Vi kommer inte längre att ha stöd för registrering och hantering av konvergerade och Azure AD-program i [program registrerings portalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/) från maj 2019. Vi rekommenderar att du hanterar dina befintliga program och registrerar nya program med hjälp av [Appregistreringars](https://aka.ms/appregistrations) upplevelsen i Azure Portal.

## <a name="my-applications-or-converged-applications"></a>Mina program eller konvergerade program
Den här listan innehåller alla dina program som har registrerats för användning med Azure AD v 2.0-slutpunkten. Dessa program kan logga in användare med både personliga Microsoft-konton och arbets-eller skol konton från Azure Active Directory. Mer information om Azure AD v 2.0-slutpunkten finns i [Översikt över v 2.0](active-directory-appmodel-v2-overview.md). Dessa program kan också användas för att integrera med slut punkten för Microsoft-konto- `https://login.live.com`autentisering.

## <a name="azure-ad-only-applications"></a>Endast Azure AD-program
Den här listan innehåller alla dina program som har registrerats för användning med Azure AD v 1.0-slutpunkten. Dessa program kan bara logga in användare med arbets-eller skol konton från Azure Active Directory. Den här listan innehåller program som har registrerats med hjälp av Appregistreringars upplevelsen i [Azure-portalen](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Live SDK-program
Den här listan innehåller alla dina program som är registrerade för användning enbart med Microsoft-konto. De är inte aktiverade för användning med Azure Active Directory. Här hittar du några program som tidigare har registrerats med MSA Developer-portalen på `https://account.live.com/developers/applications`. Alla funktioner som du tidigare utförde `https://account.live.com/developers/applications` på kan nu utföras i den nya `https://apps.dev.microsoft.com`portalen.

## <a name="application-secrets"></a>Programhemligheter
Program hemligheter är autentiseringsuppgifter som gör att ditt program kan utföra tillförlitlig [klientautentisering](https://tools.ietf.org/html/rfc6749#section-2.3) med Azure AD. I OAuth & OpenID Connect kallas en program hemlighet vanligt vis som en `client_secret`. I v 2.0-protokollet måste alla program som tar emot en säkerhetstoken på en plats med webb adresser (med hjälp `https` av ett schema) använda en program hemlighet för att identifiera sig själv för Azure AD vid inlösen av denna säkerhetstoken. Dessutom kommer alla interna klienter som tar emot token på en enhet att förbjudas att använda en program hemlighet för att utföra klientautentisering. Detta förhindrar lagring av hemligheter i osäkra miljöer.

Varje app kan innehålla två giltiga program hemligheter vid en specifik tidpunkt. Genom att underhålla två hemligheter kan du utföra regelbunden nyckel förnyelse i hela programmets hela miljö. När du har migrerat hela programmet till en ny hemlighet kan du ta bort den gamla hemligheten och etablera en ny.

Just nu är det bara två typer av program hemligheter som tillåts i appens registrerings Portal. Om du väljer **Skapa nytt lösen ord** skapas och lagras en delad hemlighet i respektive data lager, som du kan använda i ditt program. Om du väljer **generera nytt nyckel par** skapas ett nytt offentligt/privat nyckel par som kan hämtas och användas för klientautentisering till Azure AD. Om du väljer **överför offentlig nyckel** kan du använda ett eget offentligt/privat nyckel par.
Du måste ladda upp ett certifikat som innehåller en offentlig nyckel.

## <a name="profile"></a>Profil
Profil avsnittet i appens registrerings Portal kan användas för att anpassa inloggnings sidan för ditt program. Nu kan du ändra inloggnings sidans program logo typ, villkor för tjänste-URL och URL för sekretess policy. Logotypen måste vara en transparent bild på 48 x 48 eller 50 x 50 bildpunkter i en GIF-, PNG- eller JPEG-fil som är på högst 15 kB. Försök att ändra värdena och visa den resulterande inloggnings Sidan!

## <a name="live-sdk-support"></a>Live SDK-support
När du aktiverar Live SDK-stöd kommer alla program hemligheter som du skapar att tillhandahållas i data lager för både Azure AD och Microsoft-kontot. Detta gör att ditt program kan integreras direkt med Microsoft-login.live.com (Service Account service). Om du vill skapa en app med Microsoft-konto direkt (i stället för att använda Azure AD v 2.0-slutpunkten) bör du kontrol lera att Live SDK-stödet är aktiverat.

Genom att inaktivera Live SDK-stöd ser du till att program hemligheten bara skrivs till Azure AD-datalagret. Azure AD-dataarkivet införlivar regler i företags klass som gör det möjligt att uppfylla vissa standarder, t. ex. FISMA efterlevnad. Om du aktiverar Live SDK-stöd kanske programmet inte uppfyller vissa av dessa standarder.

Om du bara planerar att använda Azure AD v 2.0-slutpunkten kan du inaktivera Live SDK-stödet på ett säkert sätt.

