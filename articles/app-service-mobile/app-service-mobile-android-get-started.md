---
title: "Skapa en Android-app i Azure Apptjänst Mobile Apps | Microsoft Docs"
description: "Följ den här kursen och kom igång med att använda mobilappserverdelar i Azure för Android-utveckling"
services: app-service\mobile
documentationcenter: android
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 7752cd091d7952a905001f14414eba84b90f0d51
ms.contentlocale: sv-se
ms.lasthandoff: 03/09/2017

---
# <a name="create-an-android-app"></a>Skapa en Android-app
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Översikt
I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Android-mobilapp genom att använda en mobilappsserverdel i Azure.  Du skapar både en ny mobilappsserverdel och en enkel Android-app med en *att göra-lista* som lagrar appdata i Azure.

Du måste slutföra den här kursen innan du påbörjar någon annan Androidkurs om att använda funktionen Mobile Apps i Azure Apptjänst.

## <a name="prerequisites"></a>Krav
För att kunna genomföra den här kursen behöver du följande:

* [Utvecklingsverktyg för Android](https://developer.android.com/sdk/index.html), vilket omfattar Android Studio Integrated Development Environment och den senaste Android-plattformen.
* Azure Mobile Android SDK, som refereras automatiskt som en del av det snabbstartsprojekt du hämtar.
* Ett [aktivt Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Skapa en ny mobilappsserverdel i Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Konfigurera serverprojektet
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-android-app"></a>Hämta och köra Android-appen
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

