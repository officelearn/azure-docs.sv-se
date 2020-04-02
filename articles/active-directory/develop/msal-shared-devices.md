---
title: Översikt över delat enhetsläge
titleSuffix: Microsoft identity platform | Azure
description: Lär dig mer om läget för delade enheter för att aktivera enhetsdelning för dina Firstline-arbetare.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 514782d62f117af5bfff4a5d2b3354c4e263eece
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550245"
---
# <a name="overview-of-shared-device-mode"></a>Översikt över läget för delade enheter

Delat enhetsläge är en funktion i Azure Active Directory som gör att du kan skapa program som stöder Firstline Workers och aktivera delat enhetsläge på de enheter som distribueras till dem.

> [!NOTE]
> Den här funktionen är en allmänt tillgänglig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-are-firstline-workers"></a>Vad är Firstline Workers?

Firstline Workers är butiksanställda, underhålls- och fältagenter, medicinsk personal och andra användare som inte sitter framför en dator eller använder företagets e-post för samarbete. I följande avsnitt beskrivs aspekterna och utmaningarna med att stödja Firstline Workers, följt av en introduktion till de funktioner som tillhandahålls av Microsoft som gör det möjligt för ditt program att användas av en organisations Firstline Workers.

### <a name="challenges-of-supporting-firstline-workers"></a>Utmaningar med att stödja firstline workers

Aktivera Firstline Worker-arbetsflöden omfattar utmaningar som vanligtvis inte presenteras av vanliga informationsarbetare. Sådana utmaningar kan omfatta hög omsättningshastighet och mindre förtrogenhet med en organisations grundläggande produktivitetsverktyg. För att ge sina Firstline Workers, organisationer antar olika strategier. Vissa antar en byod-strategi (bring-your-own-device) där deras anställda använder affärsappar på sin personliga telefon, medan andra förser sina anställda med delade enheter som iPads eller Android-surfplattor.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Stöd för flera användare på enheter som är utformade för en användare

Eftersom mobila enheter som kör iOS eller Android har utformats för enskilda användare optimerar de flesta program sin upplevelse för användning av en enskild användare. En del av den här optimerade upplevelsen innebär att aktivera enkel inloggning mellan program och hålla användarna inloggade på sin enhet. När en användare tar bort sitt konto från ett program ser appen vanligtvis inte det som en säkerhetsrelaterad händelse. Många appar behåller till och med en användares inloggning för snabb inloggning. Du kan till och med ha upplevt detta själv när du har tagit bort ett program från din mobila enhet och sedan installerat om det, bara för att upptäcka att du fortfarande är inloggad.

### <a name="global-sign-in-and-sign-out-sso"></a>Global inloggning och ut logga ut (SSO)

För att en organisations anställda ska kunna använda sina appar på en pool med enheter som delas av dessa anställda måste utvecklare aktivera den motsatta upplevelsen. Anställda bör kunna välja en enhet från poolen och utföra en enda gest för att "göra den till sin" under hela skiftet. I slutet av skiftet bör de kunna utföra en annan gest för att logga ut globalt på enheten, med all personlig information och företagsinformation borttagen så att de kan returnera den till enhetspoolen. Om en anställd glömmer att logga ut ska enheten dessutom automatiskt loggas ut i slutet av skiftet och/eller efter en period av inaktivitet.

Azure Active Directory aktiverar dessa scenarier med en funktion som kallas **delat enhetsläge**.

## <a name="introducing-shared-device-mode"></a>Introduktion till delat enhetsläge

Som nämnts är delat enhetsläge en funktion i Azure Active Directory som gör att du kan:

* Skapa program som stöder Firstline Workers
* Distribuera enheter till Firstline Workers och aktivera läget för delad enhet

### <a name="build-applications-that-support-firstline-workers"></a>Skapa program som stöder Firstline Workers

Du kan stödja Firstline-arbetare i dina program genom att använda APPEN Microsoft Authentication Library (MSAL) och [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) för att aktivera ett enhetstillstånd som kallas *delat enhetsläge*. När en enhet är i delat enhetsläge förser Microsoft ditt program med information så att den kan ändra dess beteende baserat på användarens tillstånd på enheten och skydda användardata.

Funktioner som stöds är:

* **Logga in en användarenhet hela** tiden via alla program som stöds.
* **Logga ut en användarenhet hela** genom alla program som stöds.
* **Fråga enhetens tillstånd** för att avgöra om ditt program finns på en enhet som är i delat enhetsläge.
* **Fråga enhetens tillstånd** på enheten på enheten för att avgöra om något har ändrats sedan programmet senast användes.

Stöd för delad enhet läge bör betraktas både en säkerhetsförbättring och funktion uppgradering för ditt program, och kan bidra till att öka dess antagande i starkt reglerade miljöer som sjukvård och finansiering.

Användarna är beroende av att du ser till att deras data inte läcker ut till en annan användare. Dela enhetsläge ger användbara signaler som visar ditt program att en ändring som du bör hantera har inträffat. Ditt program ansvarar för att kontrollera användarens tillstånd på enheten varje gång appen används och rensa den tidigare användarens data. Detta inkluderar om den laddas om från bakgrunden i multi-tasking. Vid en användarändring bör du se till att både den föregående användarens data rensas och att alla cachelagrade data som visas i ditt program tas bort. Vi rekommenderar att du alltid utför en grundlig säkerhetsgranskningsprocess när du har lagt till funktioner för delat enhetsläge i din app.

Mer information om hur du ändrar dina program så att de stöder läget för delade enheter finns i avsnittet [Nästa steg](#next-steps) i slutet av den här artikeln.

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>Distribuera enheter till Firstline Workers och aktivera läget för delad enhet

När dina program har stöd för delat enhetsläge och inkludera nödvändiga data- och säkerhetsändringar kan du annonsera dem som användbara av Firstline Workers.

En organisations enhetsadministratörer kan distribuera sina enheter och dina program till sina butiker och arbetsplatser via en MDM-lösning (Mobile Device Management) som Microsoft Intune. En del av etableringsprocessen är att markera enheten som en *delad enhet*. Administratörer konfigurerar delat enhetsläge genom att distribuera [Microsoft Authenticator-appen](../user-help/user-help-auth-app-overview.md) och ställa in delat enhetsläge via konfigurationsparametrar. När du har utfört dessa steg använder alla program som stöder delat enhetsläge Microsoft Authenticator-programmet för att hantera användartillståndet och tillhandahålla säkerhetsfunktioner för enheten och organisationen.

## <a name="next-steps"></a>Nästa steg

Vi stöder iOS- och Android-plattformar för läget för delade enheter. Läs dokumentationen nedan för att din plattform ska börja stödja Firstline Workers i dina program.

* [Stöd för delat enhetsläge för iOS](msal-ios-shared-devices.md)
* [Stöd för delat enhetsläge för Android](msal-android-shared-devices.md)
