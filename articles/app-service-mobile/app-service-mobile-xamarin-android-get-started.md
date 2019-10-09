---
title: Kom igång med Azure Mobile Apps för Xamarin.Android-appar
description: I den här kursen får du hjälp att komma igång med Azure Mobile Apps för Xamarin Android-utveckling
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: a94b302ad813094cb4ce67e4bffc3dd43586366b
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027037"
---
# <a name="create-a-xamarinandroid-app"></a>Skapa en Xamarin.Android-app
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av mobila appar från slut punkt till slut punkt och integrerade tjänster. Utvecklare kan använda **bygge**-, **test** -och **distributions** tjänster för att konfigurera kontinuerlig integrering och leverans pipeliner. När appen har distribuerats kan utvecklare övervaka status och användning av appen med hjälp av **analys** -och **diagnos** tjänster och engagera med användare med **push** -tjänsten. Utvecklare kan också utnyttja **auth** för att autentisera sina användare och **data** tjänster för att spara och synkronisera AppData i molnet.
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

## <a name="overview"></a>Översikt
I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Xamarin.Android-app. Mer information om Mobile Apps finns [här](app-service-mobile-value-prop.md).

En skärmbild från den färdiga appen:

![][0]

Du måste slutföra den här kursen innan du börjar någon annan kurs om Mobile Apps för Xamarin.Android-appar.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här självstudien, finns följande förhandskrav:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till tio mobilappar utan kostnad. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio med Xamarin. Instruktioner finns i avsnittet om [konfiguration och installation av Visual Studio och Xamarin](/visualstudio/cross-platform/setup-and-install).

## <a name="create-an-azure-mobile-app-backend"></a>Skapa en mobilapp-serverdel i Azure
Följ de här stegen för att skapa en mobilapp-serverdel.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Du har nu skapat en mobilsappsserverdel i Azure som kan användas av dina mobilklientprogram. Därefter, hämtar du ett serverprojekt för en enkel ”att göra lista”-serverdel och publicerar den till Azure.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Skapa en databas anslutning och konfigurera klient-och Server projektet
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>Kör Xamarin. Android-appen
1. Öppna Xamarin. Android-projektet.

2. Gå till [Azure Portal](https://portal.azure.com/) och navigera till den mobilapp som du har skapat. På bladet `Overview` letar du efter URL: en som är den offentliga slut punkten för mobilappen. Exempel – webbplats namnet för mitt program namn "test123" kommer att https://test123.azurewebsites.net.

3. Öppna filen `ToDoActivity.cs` i den här mappen – Xamarin. Android/ZUMOAPPNAME/ToDoActivity. cs. Program namnet är `ZUMOAPPNAME`.

4. I `ToDoActivity`-klassen ersätter du `ZUMOAPPURL`-variabeln med den offentliga slut punkten ovan.

    `const string applicationURL = @"ZUMOAPPURL";`

    bli
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Tryck på F5 för att distribuera och köra appen.

6. Ange en beskrivande text i appen, till exempel *Slutför kursen*, och klicka sedan på **Lägg till**.

    ![][10]

    Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobilappsserverdelen och data visas i listan.

   > [!NOTE]
   > Du kan se koden som ansluter till mobilappsserverdelen för att fråga efter och infoga data i C#-filen ToDoActivity.cs.
   
## <a name="troubleshooting"></a>Felsökning
Om du får problem med att skapa lösningen kan du köra pakethanteraren för NuGet och uppdatera `Xamarin.Android`-supportpaketen. Snabbstartsprojekt innehåller inte alltid de senaste versionerna.

Observera att alla supportpaket som det refereras till i ditt projekt måste ha samma version. [Azure Mobile Apps NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) är `Xamarin.Android.Support.CustomTabs` beroende av Android-plattformen, så om projektet använder nyare supportpaket behöver du installera det här paketet i erfordrad version direkt för att undvika konflikter.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
