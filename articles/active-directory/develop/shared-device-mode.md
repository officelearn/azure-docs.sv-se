---
title: Läget Delad enhet för Android-enheter | Azure
description: Lär dig mer om läget för delade enheter, som gör det möjligt för förstalinjens arbetare att dela en Android-enhet
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: marsma
ms.reviwer: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9928b64d286cc5072f28f7cc17e4af3e95662cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085611"
---
# <a name="shared-device-mode-for-android-devices"></a>Läge för delad enhet för Android-enheter

> [!NOTE]
> Den här funktionen är en allmänt tillgänglig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Firstline-arbetare, till exempel butiksmedarbetare, flygbesättningsmedlemmar och fältservicearbetare, använder ofta en delad mobil enhet för att utföra sitt arbete. Det blir problematiskt när de börjar dela lösenord eller pinnummer för att komma åt kund- och affärsdata på den delade enheten.

Med läget delad enhet kan du konfigurera en Android-enhet så att den enkelt kan delas av flera anställda. Medarbetare kan logga in och komma åt kundinformation snabbt. När de är klara med skiftet eller uppgiften kan de logga ut från enheten och den är omedelbart redo för nästa medarbetare att använda.

Läget delad enhet ger också Microsoft identitetsbaserad hantering av enheten.

Så här skapar du en app för delat enhetsläge, utvecklare och administratörer för molnenheter:

- Utvecklare skriver en app med ett konto (appar med flera `"shared_device_mode_supported": true` konton stöds inte i läget delad enhet), lägger till i appens konfiguration och skriver kod för att hantera saker som ut logga ut på delad enhet.
- Enhetsadministratörer förbereder enheten så att den delas genom att installera autentiseringsappen och ställa in enheten i delat läge med hjälp av autentiseringsappen. Endast användare som är med i rollen [Molnenhetsadministratör](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#cloud-device-administrator) kan placera en enhet i delat läge med hjälp av [Authenticator-appen](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview). Du kan konfigurera medlemskapet för dina organisationsroller i Azure-portalen via: **Azure Active Directory** > **Roles och Administrators** > **Cloud Device Administrator**.

 Denna artikel fokuserar främst vad utvecklare bör tänka på.

## <a name="single-vs-multiple-account-applications"></a>Program med ett eller flera konton

Program som skrivs med Microsoft Authentication Library SDK (MSAL) kan hantera ett enda konto eller flera konton. Mer information finns i [läget för ett konto eller läget för flera konton](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account). Microsoft identity-plattformsfunktioner som är tillgängliga för din app varierar beroende på om programmet körs i ett kontoläge eller flera konton.

**Appar med delat enhetsläge fungerar bara i ett kontoläge**.

> [!IMPORTANT]
> Program som bara stöder flera kontoläge kan inte köras på en delad enhet. Om en anställd läser in en app som inte stöder ett kontoläge körs den inte på den delade enheten.
>
> Appar som skrevs innan MSAL SDK släpptes körs i flerkontoläge och måste uppdateras för att stödja enkontoläge innan de kan köras på en enhet med delat läge.

**Stöd för både ett konto och flera konton**

Din app kan byggas för att stödja körning på både personliga enheter och delade enheter. Om din app för närvarande stöder flera konton och du vill ha stöd för delat enhetsläge lägger du till stöd för ett kontoläge.

Du kanske också vill att appen ska ändra sitt beteende beroende på vilken typ av enhet den körs på. Används `ISingleAccountPublicClientApplication.isSharedDevice()` för att bestämma när du ska köras i ett kontoläge.

Det finns två olika gränssnitt som representerar vilken typ av enhet ditt program är på. När du begär en programinstans från MSAL:s programfabrik tillhandahålls rätt programobjekt automatiskt.

Följande objektmodell illustrerar vilken typ av objekt du kan få och vad det innebär i samband med en delad enhet:

![arvsmodell för offentligt klientprogram](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Du måste göra en typ kontroll och kasta till lämpligt `PublicClientApplication` gränssnitt när du får ditt objekt. Följande kod söker efter flera kontoläge eller ett kontoläge och castar programobjektet på lämpligt sätt:

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

Följande skillnader gäller beroende på om appen körs på en delad eller personlig enhet:

|  | Enhet för delat läge  | Personlig enhet |
|---------|---------|---------|
| **Konton**     | Ett konto | Flera konton |
| **Logga in** | Global | Global |
| **Logga ut** | Global | Varje program kan styra om ut logga ut är lokal för appen eller för programfamiljen. |
| **Kontotyper som stöds** | Endast arbetskonton | Personliga konton och arbetskonton stöds  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Varför du kanske bara vill stödja ett kontoläge

Om du skriver en app som bara används för förstahandsarbetare som använder en delad enhet rekommenderar vi att du skriver ditt program för att endast stödja ett kontoläge. Detta inkluderar de flesta program som är uppgiftsfokuserade, till exempel journalappar, fakturaappar och de flesta affärsappar. Endast stöd för ett konto-läge förenklar utvecklingen eftersom du inte behöver implementera ytterligare funktioner som ingår i appar med flera konton.

## <a name="what-happens-when-the-device-mode-changes"></a>Vad händer när enhetsläget ändras

Om programmet körs i flerkontoläge och en administratör placerar enheten i delat enhetsläge, rensas alla konton på enheten från programmet och programmet övergår till ett kontoläge.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Ut logga ut för delad enhet och den övergripande appens livscykel

När en användare loggar ut måste du vidta åtgärder för att skydda användarens integritet och data. Om du till exempel skapar en journalapp vill du vara säker på att patientjournaler rensas när användaren loggar ut som tidigare visades. Din ansökan måste vara förberedd för detta och kontrollera varje gång den kommer in i förgrunden.

När appen använder MSAL för att logga ut användaren i en app som körs på en enhet som är i delat läge tas inloggningskontot och cachelagrade token bort från både appen och enheten.

Följande diagram visar den övergripande appens livscykel och vanliga händelser som kan inträffa medan appen körs. Diagrammet täcker från det att en aktivitet startar, loggar in och signerar ett konto och hur händelser som att pausa, återuppta och stoppa aktiviteten passar in.

![Livscykel för appen Delad enhet](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Nästa steg

Prova [läget Använd delad enhet i självstudien för Android-program](tutorial-v2-shared-device-mode.md) som visar hur du kör en förstalinjearbetsapp på en Android-enhet i delat läge.
