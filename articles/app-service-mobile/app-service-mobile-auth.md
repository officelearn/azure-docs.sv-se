---
title: Autentisering och auktorisering i Azure Mobile Apps | Microsoft Docs
description: "Konceptuell referens- och översikt över autentisering / auktorisering funktion för Azure Mobile Apps"
services: app-service\mobile
documentationcenter: 
author: mattchenderson
manager: cfowler
editor: 
ms.assetid: a46dbf70-867d-48f6-8885-7f5207ad102e
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 90c11b09351f019c45f5f1b025d67947b69b3b0a
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="authentication-and-authorization-in-azure-mobile-apps"></a>Autentisering och auktorisering i Azure Mobile Apps
## <a name="what-is-app-service-authentication--authorization"></a>Vad är App Service autentisering / auktorisering?
> [!NOTE]
> Det här avsnittet kommer att migreras till en konsoliderad [App Service autentisering / auktorisering](../app-service/app-service-authentication-overview.md) avsnittet, som omfattar webb-, mobil- och API Apps.
> 
> 

App Service autentisering / auktorisering är en funktion som gör att programmet att logga in användare med några kodändringar på appens serverdel. Det ger ett enkelt sätt att skydda ditt program och arbeta med data per användare.

Apptjänst använder federerad identitet som 3 parts **identitetsleverantör** (”IDP”) lagrar konton och autentiserar användare och programmet använder den här identiteten i stället för en egen. Apptjänst fem identitetsleverantörer ur lådan: *Azure Active Directory*, *Facebook*, *Google*, *Account*, och *Twitter*. Du kan också expandera det här stödet för dina appar genom att integrera en annan identitetsleverantör eller din egen lösning för anpassad identitet.

Din app kan använda valfritt antal dessa identitetsleverantörer, så du kan ge slutanvändarna alternativ för hur de logga in.

Om du vill komma igång nu direkt kan se något av följande kurser:

* [Lägg till autentisering i din iOS-app]
* [Lägg till autentisering i Xamarin.iOS-app]
* [Lägg till autentisering i Xamarin.Android-app]
* [Lägg till autentisering i Windows-appen]

## <a name="how-authentication-works"></a>Så här fungerar autentisering
För att kunna autentisera med någon av identitetsleverantörer först måste du konfigurera identitetsleverantören som du behöver veta om ditt program. Identitetsleverantören som ger dig sedan med ID: N och hemligheter som du anger till programmet. Det har slutförts förtroenderelationen och gör Apptjänst att verifiera identiteter angivna.

Dessa steg beskrivs i följande avsnitt:

* [Så här konfigurerar du appen för att använda Azure Active Directory-inloggning]
* [Så här konfigurerar du appen för att använda Facebook-inloggning]
* [Så här konfigurerar du appen för att använda Google-inloggning]
* [Så här konfigurerar du appen för att använda Account inloggning]
* [Så här konfigurerar du appen för att använda Twitter-inloggning]

Du kan ändra din klient för att logga in när allting har konfigurerats på serverdelen. Det finns två tillvägagångssätt här:

* Med en enda rad kod kan Mobilappar klient-SDK logga in användare.
* Använda en SDK som publicerats av en viss identitetsleverantör upprätta identitet och få åtkomst till App Service.

> [!TIP]
> De flesta program ska använda en provider SDK att få en mer intern känslan inloggningsupplevelse och utnyttja uppdatera support och andra provider-specifik fördelar.
> 
> 

### <a name="how-authentication-without-a-provider-sdk-works"></a>Hur autentisering utan en provider SDK fungerar
Om du inte vill konfigurera en provider SDK kan du tillåta Mobile Apps att utföra inloggningen för dig. Mobile Apps klient-SDK öppnar en webbvy till leverantören av du väljer och logga in. Ibland på bloggar och forum visas detta som anges som ”server flödet” eller ”server-riktade flödet” sedan servern hanterar inloggningen och klient-SDK får aldrig provider-token.

Den kod som behövs för att starta det här flödet beskrivs i självstudiekursen autentisering för varje plattform. Klient-SDK har en Apptjänst-token i slutet av flödet och token kopplas automatiskt till alla förfrågningar till serverdelen.

### <a name="how-authentication-with-a-provider-sdk-works"></a>Hur autentisering med provider SDK fungerar
Arbeta med en provider kan SDK inloggning tätare interagerar med plattformens OS appen körs på. Detta ger dig också en provider-token och viss användarinformation på klienten, vilket gör det enklare att använda graph API: er och anpassa användarupplevelsen. Ibland på bloggar och forum visas detta kallas ”klienten flödet” eller ”klienten dirigeras flödet” eftersom kod på klienten hanterar inloggningen och klienten har åtkomst till en provider-token.

När en provider-token hämtas, måste den skickas till App Service för verifiering. Klient-SDK har en Apptjänst-token i slutet av flödet och token kopplas automatiskt till alla förfrågningar till serverdelen. Utvecklare kan också behålla en referens till en provider-token om de vill.

## <a name="how-authorization-works"></a>Så här fungerar auktorisering
App Service autentisering / auktorisering visar flera alternativ för **åtgärd att vidta när begäran inte har autentiserats**. Innan koden tar emot en viss begäran, måste du kontrollera Apptjänst om begäran är autentiserad och om inte, avvisa den och försöker användare logga in innan du försöker igen.

Ett alternativ är att ha oautentiserade begäranden omdirigering till en av leverantörerna för identiteten. I en webbläsare, skulle det faktiskt ta användaren till en ny sida. Dock mobila klienten inte omdirigeras på detta sätt och oautentiserade svar får en HTTP *401 obehörig* svar. Detta den första begäran som gör att klienten ska alltid vara till slutpunkten för inloggning och kan du göra anrop till alla andra API: er. Om du försöker anropa en annan API innan du loggar in, kommer klienten får ett felmeddelande.

Om du vill ha mer detaljerad kontroll över vilka slutpunkter kräver autentisering, du kan också hämta ”ingen åtgärd (Tillåt begäranden)” för oautentiserade begäranden. I det här fallet är alla beslut om autentisering uppskjutna av programkoden. Du kan också tillåta åtkomst till specifika användare baserat på regler för anpassad auktorisering.

## <a name="documentation"></a>Dokumentation
Följande kurser visar hur du lägger till autentisering till dina mobila klienter som använder App Service:

* [Lägg till autentisering i din iOS-app]
* [Lägg till autentisering i Xamarin.iOS-app]
* [Lägg till autentisering i Xamarin.Android-app]
* [Lägg till autentisering i Windows-appen]

Följande kurser visar hur du konfigurerar Apptjänst för att kunna utnyttja olika autentiseringsproviders:

* [Så här konfigurerar du appen för att använda Azure Active Directory-inloggning]
* [Så här konfigurerar du appen för att använda Facebook-inloggning]
* [Så här konfigurerar du appen för att använda Google-inloggning]
* [Så här konfigurerar du appen för att använda Account inloggning]
* [Så här konfigurerar du appen för att använda Twitter-inloggning]

Om du vill använda ett identitetssystem än som anges här, kan du också använda den [Förhandsgranska stöd för anpassad autentisering i server .NET SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth).

[Lägg till autentisering i din iOS-app]: app-service-mobile-ios-get-started-users.md
[Lägg till autentisering i Xamarin.iOS-app]: app-service-mobile-xamarin-ios-get-started-users.md
[Lägg till autentisering i Xamarin.Android-app]: app-service-mobile-xamarin-android-get-started-users.md
[Lägg till autentisering i Windows-appen]: app-service-mobile-windows-store-dotnet-get-started-users.md

[Så här konfigurerar du appen för att använda Azure Active Directory-inloggning]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Så här konfigurerar du appen för att använda Facebook-inloggning]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[Så här konfigurerar du appen för att använda Google-inloggning]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[Så här konfigurerar du appen för att använda Account inloggning]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Så här konfigurerar du appen för att använda Twitter-inloggning]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
