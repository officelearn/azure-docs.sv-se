<properties
    pageTitle="Skapa en iOS-app i Azure Apptjänst Mobilappar | Microsoft Azure"
    description="Följ den här kursen och kom igång med att använda mobilappserverdelar i Azure för iOS-utveckling i Objective-C eller Swift"
    services="app-service\mobile"
    documentationCenter="ios"
    authors="krisragh"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="04/19/2016"
    ms.author="krisragh"/>

#Skapa en iOS-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Översikt

Den här kursen visar hur du lägger till [Azure Mobilappar](app-service-mobile-value-prop.md), en molnbaserad serverdelstjänst, i en iOS-app. Vi måste först skapa en ny mobilserverdel. Sedan använder vi en enkel  iOS-app med en _att göra-lista_ för att lagra data i Azure.

## Förutsättningar

För att kunna genomföra den här kursen behöver du följande:

* Ett [aktivt Azure-konto](https://azure.microsoft.com/pricing/free-trial/)
* En PC-dator med [Visual Studio Community 2013] eller senare 
* En Mac med Xcode 7.3 eller senare

## Steg I: skapa en ny mobilappserverdel i Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Steg II: konfigurera serverdelsprojektet

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Steg III: hämta och kör iOS-appen

[AZURE.INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure Portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203



<!--HONumber=Jun16_HO2-->


