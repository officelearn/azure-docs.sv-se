---
title: Delat enhets läge för Android-enheter | Azure
description: Lär dig mer om läget för delad enhet, vilket gör att firstline-arbetare kan dela en Android-enhet
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: twhitney
ms.reviwer: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 53fa82cf6eaaba09353ba21a12ae9677b9264b1a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701475"
---
# <a name="shared-device-mode-for-android-devices"></a>Läge för delad enhet för Android-enheter

> [!NOTE]
> Den här funktionen är en allmänt tillgänglig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Firstline-arbetskrafter, till exempel detalj handels företag, flyg besättnings medlemmar och fält tjänst arbetare, använder ofta en delad mobil enhet för att utföra sitt arbete. Det är problematiskt när de börjar dela lösen ord eller PIN-nummer för att komma åt kund-och affärs data på den delade enheten.

Med delad enhets läge kan du konfigurera en Android-enhet så att den enkelt kan delas av flera anställda. Anställda kan snabbt logga in och komma åt kund information. När de har slutförts med Skift eller uppgift kan de logga ut från enheten och det är omedelbart klart för nästa medarbetare att använda.

Delad enhets läge tillhandahåller även Microsoft-identitet för hantering av enheten.

För att skapa en app för delad enhets läge fungerar utvecklare och moln enhets administratörer tillsammans:

- Utvecklare skriver en app med ett enda konto (flera konton stöds inte i läget för delad enhet), Lägg till `"shared_device_mode_supported": true` i appens konfiguration och skriv kod för att hantera saker som delad enhets utloggning.
- Enhets administratörer förbereder enheten för delning genom att installera Authenticator-appen och ställa in enheten till delat läge med hjälp av Authenticator-appen. Endast användare som är i rollen som [moln enhets administratör](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#cloud-device-administrator) kan lagra en enhet i delat läge med hjälp av [Authenticator-appen](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview). Du kan konfigurera medlemskap för dina organisations roller i Azure Portal via: **Azure Active Directory** > **roller och administratörer** > **moln enhets administratör**.

 Den här artikeln fokuserar främst på vad utvecklare bör tänka på.

## <a name="single-vs-multiple-account-applications"></a>Program med en vs-flera konton

Program som har skrivits med Microsoft Authentication Library SDK (MSAL) kan hantera ett enda konto eller flera konton. Mer information finns i läge för [enskilda konton eller flera konton](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account). Vilka Microsoft Identity Platform-funktioner som är tillgängliga för din app beror på om programmet körs i enanvändarläge eller i läget för flera konton.

**Delade enhets läge appar fungerar bara i enanvändarläge**.

> [!IMPORTANT]
> Program som endast stöder läge med flera konton kan inte köras på en delad enhet. Om en medarbetare läser in en app som inte har stöd för enanvändarläge, körs den inte på den delade enheten.
>
> Appar som skrivits innan MSAL SDK lanserades i läget för flera konton och måste uppdateras för att stödja enanvändarläge innan de kan köras på en enhet i delat läge.

**Stöd för både ett konto och flera konton**

Din app kan skapas för att stödja körning på både personliga enheter och delade enheter. Om din app för närvarande stöder flera konton och du vill ha stöd för delad enhets läge, lägger du till stöd för enskilt konto läge.

Du kanske också vill att appen ska ändra beteendet beroende på vilken typ av enhet som den körs på. Använd `ISingleAccountPublicClientApplication.isSharedDevice()` för att avgöra när du ska köra i enanvändarläge.

Det finns två olika gränssnitt som representerar den typ av enhet som ditt program är på. När du begär en program instans från MSALs program fabrik anges rätt program objekt automatiskt.

Följande objekt modell visar vilken typ av objekt som du kan ta emot och vad det innebär i kontexten för en delad enhet:

![offentlig klient program arvs modell](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

Du måste göra en typ kontroll och omvandla till lämpligt gränssnitt när du får `PublicClientApplication`-objektet. Följande kod söker efter läge för flera konton eller enskilt konto och skickar programobjektet korrekt:

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

Följande skillnader gäller beroende på om din app körs på en delad eller personlig enhet:

|  | Enhet för delat läge  | Personlig enhet |
|---------|---------|---------|
| **Konton**     | Enskilt konto | Flera konton |
| **Inloggning** | Globalt | Globalt |
| **Logga ut** | Globalt | Varje program kan styra om utloggningen är lokal i appen eller för program familjen. |
| **Konto typer som stöds** | Endast arbets konton | Personliga konton och arbets konton stöds  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>Därför kanske du bara vill ha stöd för enanvändarläge

Om du skriver en app som endast ska användas för firstline-anställda som använder en delad enhet rekommenderar vi att du skriver ditt program så att det endast stöder enanvändarläge. Detta inkluderar de flesta program som är aktiviteter fokuserade som medicinska register appar, faktura program och de flesta branschspecifika appar. Endast stöd för enskilda konton fören klar utvecklingen eftersom du inte behöver implementera de ytterligare funktioner som ingår i flera konton.

## <a name="what-happens-when-the-device-mode-changes"></a>Vad händer när enhets läget ändras

Om ditt program körs i ett läge med flera konton och en administratör försätter enheten i delad enhets läge, raderas alla konton på enheten från programmet och program över gångar till läge för enkel konto.

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>Delad enhets utloggning och den övergripande appens livs cykel

När en användare loggar ut måste du vidta åtgärder för att skydda användarens sekretess och data. Om du till exempel skapar en app för medicinska uppgifter vill du se till att när användaren loggar ut tidigare visade patient poster rensas. Ditt program måste förberedas för detta och kontrol lera varje gång det går in i förgrunden.

När din app använder MSAL för att logga ut användaren i en app som körs på enheten i delat läge, tas det inloggade kontot och cachelagrade token bort från både appen och enheten.

Följande diagram visar den övergripande livs cykeln för appar och vanliga händelser som kan uppstå när din app körs. Diagrammet gäller från den tidpunkt då en aktivitet startas, inloggning och utloggning av ett konto, samt hur händelser som att pausa, återuppta och stoppa aktiviteten får plats i.

![Program livs cykel för delad enhet](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>Nästa steg

Prova [läget Använd delad enhet i din Android-](tutorial-v2-shared-device-mode.md) programsjälvstudie som visar hur du kör en firstline Worker-app på en Android-enhet i delat läge.
