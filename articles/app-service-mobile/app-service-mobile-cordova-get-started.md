---
title: "Skapa en Cordova-app i Azure Apptjänst Mobile Apps | Microsoft Docs"
description: "Följ den här kursen och kom igång med att använda mobilappserverdelar i Azure för Apache Cordova-utveckling"
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: 
tags: 
keywords: cordova, javascript, mobil, klient
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: hero-article
ms.date: 07/07/2017
ms.author: crdun
ms.openlocfilehash: 223e9e35fcab347f9b5b8db01a9fd667b9f5d55d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="create-an-apache-cordova-app"></a>Skapa en Apache Cordova-app
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Översikt

I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Apache Cordova-mobilapp med hjälp av en mobilappserverdel i Azure.  Du skapar både en ny serverdel för mobilappen och en enkel Apache Cordova-app med en *att göra-lista* som lagrar appdata i Azure.

Du måste slutföra den här kursen innan du påbörjar någon annan Apache Cordova-kurs om att använda funktionen Mobile Apps i Azure Apptjänst.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här självstudien, finns följande förhandskrav:

* En PC-dator med [Visual Studio Community 2017] eller senare.
* [Visual Studio Tools för Apache Cordova].
* Ett [aktivt Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

Du kan också kringgå Visual Studio och direkt använda kommandoraden i Apache Cordova.  Det är praktiskt att använda kommandoraden om du går självstudiekursen på en Mac-dator.  I den här kursen ingår inte att kompilera klientprogram i Apache Cordova med hjälp av kommandoraden.

## <a name="create-an-azure-mobile-app-backend"></a>Skapa serverdelen för en Azure-mobilapp
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[Titta på en video som visar liknande steg](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## <a name="configure-the-server-project"></a>Konfigurera serverprojektet
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Hämta och köra Apache Cordova-appen
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du har slutfört den här snabbstartskursen går du vidare till en av följande kurser:

* [Add Offline Data](app-service-mobile-cordova-get-started-offline-data.md) (Lägga till offlinedata) i en Apache Cordova-app.
* [Lägg till autentisering](app-service-mobile-cordova-get-started-users.md) i din Apache Cordova-app.
* [Lägg till push-meddelanden](app-service-mobile-cordova-get-started-push.md) i din Apache Cordova-app.

Läs mer om nyckelbegrepp i Azure Apptjänst.

* [Offlinedata]
* [Autentisering]
* [Push-meddelanden]

Lär dig hur du använder SDK: er.

* [Apache Cordova-SDK]
* [ASP.NET Server-SDK]
* [Node.js Server-SDK]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2017]: http://www.visualstudio.com/
[Visual Studio Tools för Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Offlinedata]: app-service-mobile-offline-data-sync.md
[Autentisering]: app-service-mobile-auth.md
[Push-meddelanden]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova-SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server-SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server-SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
