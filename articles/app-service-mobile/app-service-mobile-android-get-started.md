---
title: Skapa en Android-app i Azure Apptjänst Mobile Apps | Microsoft Docs
description: Följ den här kursen och kom igång med att använda mobilappserverdelar i Azure för Android-utveckling
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
editor: ''
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: emalani
ms.openlocfilehash: 238fde023ba1b5c8b21f181655c9633329c22699
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443615"
---
# <a name="create-an-android-app"></a>Skapa en Android-app
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center investerar i nya och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda **skapa**, **Test** och **fördela** tjänster för att konfigurera pipeline för kontinuerlig integrering och leverans. När appen har distribuerats, utvecklare kan övervaka status och användningen av sin app med hjälp av den **Analytics** och **diagnostik** services och interagera med användare som använder den **Push** tjänsten. Utvecklare kan även utnyttja **Auth** att autentisera användarna och **Data** -tjänsten för att bevara och synkronisera AppData i molnet. Kolla in [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-android-get-started) idag.
>

## <a name="overview"></a>Översikt
I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Android-mobilapp genom att använda en mobilappsserverdel i Azure.  Du skapar både en ny mobilappsserverdel och en enkel Android-app med en *att göra-lista* som lagrar appdata i Azure.

Du måste slutföra den här kursen innan du påbörjar någon annan Androidkurs om att använda funktionen Mobile Apps i Azure Apptjänst.

## <a name="prerequisites"></a>Förutsättningar
För att kunna genomföra den här kursen behöver du följande:

* [Utvecklingsverktyg för Android](https://developer.android.com/sdk/index.html), vilket omfattar Android Studio Integrated Development Environment och den senaste Android-plattformen.
* Azure Mobile Android SDK.
* Ett [aktivt Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Skapa en ny mobilappsserverdel i Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Skapa en databasanslutning och konfigurera projektet klient och server
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-android-app"></a>Köra Android-appen
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
