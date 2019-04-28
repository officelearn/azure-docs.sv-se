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
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: crdun
ms.openlocfilehash: d0d6a3d9da2768c2d7b04bd9c4a7c24fba9eb65e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129016"
---
# <a name="create-an-ios-app"></a>Skapa en iOS-app

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Översikt

Den här kursen visar hur du lägger till [Azure App Service Mobile Apps](app-service-mobile-value-prop.md), en molnbaserad serverdelstjänst, i en iOS-app. Det första steget är att skapa en ny mobilserverdel på Azure. Ladda sedan ned en enkel exempelapp, *att göra-lista*, i iOS som lagrar data i Azure.

Du behöver [ett Azure-konto](https://azure.microsoft.com/pricing/free-trial/) och en Mac-dator för att slutföra den här självstudien.

## <a name="step-i-create-a-new-azure-mobile-app-backend"></a>Steg I: Skapa en ny mobilappsserverdel i Azure

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="step-ii-configure-the-backend-project"></a>Steg II: Konfigurera serverdelsprojektet

[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="step-iii-download-and-run-the-ios-app"></a>Steg III: Ladda ned och kör iOS-appen

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
