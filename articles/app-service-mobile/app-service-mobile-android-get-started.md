<properties
    pageTitle="Skapa en Android-app i Azure Apptjänst Mobilappar | Microsoft Azure"
    description="Följ den här kursen och kom igång med att använda mobilappserverdelar i Azure för Android-utveckling"
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="05/03/2016"
    ms.author="yuaxu;ricksal"/>

#Skapa en Android-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Översikt

I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Android-mobilapp genom att använda en mobilappsserverdel i Azure.  Du skapar både en ny mobilappsserverdel och en enkel Android-app med en _att göra-lista_ som lagrar appdata i Azure.

Du måste slutföra den här kursen innan du påbörjar någon annan Androidkurs om att använda funktionen Mobilappar i Azure Apptjänst.

## Förutsättningar

För att kunna genomföra den här kursen behöver du följande:

* [Utvecklingsverktyg för Android](https://developer.android.com/sdk/index.html), vilket omfattar Android Studio Integrated Development Environment och den senaste Android-plattformen.
* Azure Mobile Android SDK, som refereras automatiskt som en del av det snabbstartsprojekt du hämtar.
* En dator med [Visual Studio Community 2013] eller senare &mdash; krävs inte för Node.js-serverdel.
* Ett [aktivt Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

## Skapa en ny mobilappsserverdel i Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Konfigurera serverprojektet

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Hämta och köra Android-appen

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure Portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203



<!--HONumber=Jun16_HO2-->


