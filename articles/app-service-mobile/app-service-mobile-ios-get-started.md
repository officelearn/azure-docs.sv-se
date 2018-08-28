---
title: Skapa en iOS-app i Azure App Service Mobile Apps | Microsoft Docs
description: Följ den här kursen och kom igång med att använda mobilappserverdelar i Azure för iOS-utveckling i Objective-C eller Swift
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 08/17/2018
ms.author: crdun
ms.openlocfilehash: 9809b51f1279c99de69cd1c219ffc57351ff21ef
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "41919608"
---
# <a name="create-an-ios-app"></a>Skapa en iOS-app

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Översikt

Den här kursen visar hur du lägger till [Azure App Service Mobile Apps](app-service-mobile-value-prop.md), en molnbaserad serverdelstjänst, i en iOS-app. Det första steget är att skapa en ny mobilserverdel på Azure. Ladda sedan ned en enkel exempelapp, *att göra-lista*, i iOS som lagrar data i Azure.

Du behöver [ett Azure-konto](https://azure.microsoft.com/pricing/free-trial/) och en Mac-dator för att slutföra den här självstudien.

## <a name="step-i-create-a-new-azure-mobile-app-backend"></a>Steg I: skapa en ny mobilappserverdel i Azure

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="step-ii-configure-the-backend-project"></a>Steg II: konfigurera serverdelsprojektet

[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="step-iii-download-and-run-the-ios-app"></a>Steg III: hämta och kör iOS-appen

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
