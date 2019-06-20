---
title: Skapa en Cordova-app i Azure Apptjänst Mobile Apps | Microsoft Docs
description: Följ den här kursen och kom igång med att använda mobilappserverdelar i Azure för Apache Cordova-utveckling
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
tags: ''
keywords: cordova, javascript, mobil, klient
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: ac6c2b0f93c56de6e0a2b559645884b60d761ba8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240255"
---
# <a name="create-an-apache-cordova-app"></a>Skapa en Apache Cordova-app
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Översikt
I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Apache Cordova-mobilapp med hjälp av en mobilappserverdel i Azure.  Du skapar både en ny serverdel för mobilappen och en enkel Apache Cordova-app med en *att göra-lista* som lagrar appdata i Azure.

Du måste slutföra den här kursen innan du påbörjar någon annan Apache Cordova-kurs om att använda funktionen Mobile Apps i Azure Apptjänst.

## <a name="prerequisites"></a>Nödvändiga komponenter
För att slutföra den här självstudien, finns följande förhandskrav:

* En PC-dator med [Visual Studio Community 2017] eller senare.
* [Visual Studio Tools för Apache Cordova].
* Ett [aktivt Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

Du kan också kringgå Visual Studio och direkt använda kommandoraden i Apache Cordova.  Det är praktiskt att använda kommandoraden om du går självstudiekursen på en Mac-dator.  I den här kursen ingår inte att kompilera klientprogram i Apache Cordova med hjälp av kommandoraden.

## <a name="create-an-azure-mobile-app-backend"></a>Skapa serverdelen för en Azure-mobilapp
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Skapa en databasanslutning och konfigurera projektet klient och server
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Hämta och köra Apache Cordova-appen
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]
<!-- URLs -->
[Azure-portalen]: https://portal.azure.com/

[Visual Studio Community 2017]: https://www.visualstudio.com/
[Visual Studio Tools för Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx