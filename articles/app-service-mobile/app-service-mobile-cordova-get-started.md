<properties
    pageTitle="Skapa en Cordova-app i Azure Apptjänst Mobilappar | Microsoft Azure"
    description="Följ den här kursen och kom igång med att använda mobilappserverdelar i Azure för Apache Cordova-utveckling"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""
    tags=""
    keywords="cordova,javascript,mobile,client" />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="05/02/2016"
    ms.author="glenga"/>

#Skapa en Apache Cordova-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Översikt


I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Apache Cordova-mobilapp med hjälp av en mobilappserverdel i Azure.  Du skapar både en ny mobilappserverdel och en enkel  Apache Cordova-app med en _att göra-lista_ som lagrar appdata i Azure.

Du måste slutföra den här kursen innan du påbörjar någon annan Apache Cordova-kurs om att använda funktionen Mobilappar i Azure Apptjänst.

## Förutsättningar

För att kunna genomföra den här kursen behöver du följande:

* En PC-dator med [Visual Studio Community 2015] eller senare.
* [Visual Studio Tools för Apache Cordova].
* Ett [aktivt Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

Du kan också kringgå Visual Studio och direkt använda kommandoraden i Apache Cordova.  Det är till nytta när du går igenom kursen på en Mac-dator.  I den här kursen ingår inte att kompilera klientprogram i Apache Cordova med hjälp av kommandoraden.

## Skapa en ny mobilappserverdel i Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Konfigurera serverprojektet

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Hämta och köra Apache Cordova-appen

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## Nästa steg

Nu när du har slutfört den här snabbstartskursen går du vidare till en av följande kurser:

* [Lägg till autentisering] i din Apache Cordova-app.
* [Lägg till push-meddelanden] i din Apache Cordova-app.

Läs mer om nyckelbegrepp i Azure Apptjänst.

* [Autentisering]
* [Push-meddelanden]

Lär dig hur du använder SDK: er.

* [Apache Cordova-SDK]
* [ASP.NET Server-SDK]
* [Node.js Server-SDK]

<!-- Images. -->

<!-- URLs -->
[Azure Portal]: https://portal.azure.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools för Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Lägg till autentisering]: app-service-mobile-cordova-get-started-users.md
[Lägg till push-meddelanden]: app-service-mobile-cordova-get-started-push.md
[Autentisering]: app-service-mobile-auth.md
[Push-meddelanden]: ../notification-hubs/notification-hubs-overview.md
[Apache Cordova-SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server-SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server-SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md




<!--HONumber=Jun16_HO2-->


