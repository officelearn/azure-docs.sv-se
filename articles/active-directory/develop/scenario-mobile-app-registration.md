---
title: 'Mobilapp som anropar webb-API: er – appens kod konfiguration | Microsoft Identity Platform'
description: 'Lär dig hur du skapar en mobilapp som anropar webb-API: er (appens kod konfiguration)'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43ae9da51f68c9765a36d27c993d1c9935d61fa
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326129"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Mobilapp som anropar webb-API: er – app-registrering

Den här artikeln innehåller anvisningar för appens registrering för att skapa ett mobil program.

## <a name="supported-account-types"></a>Kontotyper som stöds

De konto typer som stöds i mobila program beror på vilken upplevelse du vill aktivera och vilka användare som är mål för din app.

## <a name="platform-configuration-and-redirect-uris"></a>Plattforms konfiguration och omdirigering av URI: er  

När du skapar en mobilapp är det mest viktiga registrerings steget den omdirigerings-URI: n. Detta kan ställas in via [plattforms konfigurationen på bladet autentisering](https://aka.ms/MobileAppReg).

Den här funktionen gör det möjligt för din app att få enkel inloggning (SSO) via Microsoft Authenticator (och Intune-företagsportal på Android) samt stöd för enhets hanterings principer.

Om du föredrar att konfigurera omdirigerings-URI: n manuellt kan du göra det via applikations manifestet. Det rekommenderade formatet är följande:

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Hash-signaturens hash kan genereras med hjälp av versions-eller fel söknings nycklarna via kommando kommandot.

## <a name="api-permissions"></a>API-behörigheter

Mobil program anropar API: er å den inloggade användarens vägnar. Appen måste begära delegerade behörigheter, även kallat omfång. Beroende på den önskade upplevelsen kan detta göras statiskt via Azure Portal eller dynamiskt vid körning. Med tillstånd för att registrera statiskt kan administratörer enkelt godkänna din app och rekommenderas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämtar en token](scenario-mobile-acquire-token.md)
