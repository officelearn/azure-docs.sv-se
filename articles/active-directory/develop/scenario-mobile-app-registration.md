---
title: 'Mobilapp att anrop webb-API: er – appkonfiguration kod | Microsoft identity-plattformen'
description: 'Lär dig att skapa en mobilapp att anrop webb-API: er (konfiguration av appens kod)'
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eac9d1dfce79ac4ad9d49a6cfe6b7dee7f6681a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075167"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Mobilapp att anrop webb-API: er – appregistrering

Den här artikeln innehåller anvisningar för app-registrering för att skapa en hanteringsprincip för mobilprogram.

## <a name="supported-account-types"></a>Kontotyper som stöds

Kontotyper stöds i mobila program är beroende av upplevelse som du vill aktivera och användare som är mål för din app.

## <a name="platform-configuration-and-redirect-uris"></a>Plattformskonfiguration och omdirigerings-URI: er  

När du skapar en mobil app, är det viktigaste registreringssteget omdirigerings-URI. Detta kan ställas in via den [plattformskonfiguration på bladet autentisering](https://aka.ms/MobileAppReg).

Den här upplevelsen aktiverar din app för att få enkel inloggning (SSO) via Microsoft Authenticator (och Intune-Företagsportalen för Android) samt stöd för principer för enhetshantering.

Om du föredrar att manuellt konfigurera omdirigeringen-URI kan göra du det via programmets Manifest. Rekommenderat format är följande:

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Signatur för Android-hash kan genereras med hjälp av versions- eller debug-nycklar med kommandot KeyTool.

## <a name="api-permissions"></a>API-behörigheter

Mobilprogram anropa API: er åt den inloggade användaren. Din app behöver begära delegerade behörigheter, även kallat omfång. Beroende på den önskade upplevelsen, kan detta göras statiskt via Azure portal eller dynamiskt vid körning. Statiskt registrering behörigheter kan administratörer enkelt godkänna din app och rekommenderas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [En token hämtades](scenario-mobile-acquire-token.md)
