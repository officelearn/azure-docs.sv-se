---
title: Kom igång med Azure Mobile Apps för Xamarin.Android-appar
description: I den här kursen får du hjälp att komma igång med Azure Mobile Apps för Xamarin Android-utveckling
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: f3e8ca4f9736dffe4928fc8920b0890dff87367b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236031"
---
# <a name="create-a-xamarinandroid-app"></a>Skapa en Xamarin.Android-app
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Översikt
I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Xamarin.Android-app. Mer information om Mobile Apps finns [här](app-service-mobile-value-prop.md).

En skärmbild från den färdiga appen:

![][0]

Du måste slutföra den här kursen innan du börjar någon annan kurs om Mobile Apps för Xamarin.Android-appar.

## <a name="prerequisites"></a>Nödvändiga komponenter
För att slutföra den här självstudien, finns följande förhandskrav:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till tio mobilappar utan kostnad. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio med Xamarin. Instruktioner finns i avsnittet om [konfiguration och installation av Visual Studio och Xamarin](/visualstudio/cross-platform/setup-and-install).

## <a name="create-an-azure-mobile-app-backend"></a>Skapa en mobilapp-serverdel i Azure
Följ de här stegen för att skapa en mobilapp-serverdel.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Du har nu skapat en mobilsappsserverdel i Azure som kan användas av dina mobilklientprogram. Därefter, hämtar du ett serverprojekt för en enkel ”att göra lista”-serverdel och publicerar den till Azure.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Skapa en databasanslutning och konfigurera projektet klient och server
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>Kör Xamarin.Android-appen
1. Öppna Xamarin.Android-projektet.

2. Gå till den [Azure-portalen](https://portal.azure.com/) och navigera till den mobila appen som du skapade. På den `Overview` bladet letar du reda på URL: en som är den offentliga slutpunkten för din mobilapp. Exempel – sitename för min app name ”test123” kommer att https://test123.azurewebsites.net.

3. Öppna filen `ToDoActivity.cs` i den här mappen - xamarin.android/ZUMOAPPNAME/ToDoActivity.cs. Programnamnet är `ZUMOAPPNAME`.

4. I `ToDoActivity` klass, Ersätt `ZUMOAPPURL` variabeln med offentlig slutpunkt ovan.

    `const string applicationURL = @"ZUMOAPPURL";`

    blir
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Tryck på F5 för att distribuera och köra appen.

6. Ange en beskrivande text i appen, till exempel *Slutför kursen*, och klicka sedan på **Lägg till**.

    ![][10]

    Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobilappsserverdelen och data visas i listan.

   > [!NOTE]
   >  Du kan se koden som ansluter till mobilappsserverdelen för att fråga efter och infoga data i C#-filen ToDoActivity.cs.
   
## <a name="troubleshooting"></a>Felsökning
Om du får problem med att skapa lösningen kan du köra pakethanteraren för NuGet och uppdatera `Xamarin.Android`-supportpaketen. Snabbstartsprojekt innehåller inte alltid de senaste versionerna.

Observera att alla supportpaket som det refereras till i ditt projekt måste ha samma version. [Azure Mobile Apps NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) är `Xamarin.Android.Support.CustomTabs` beroende av Android-plattformen, så om projektet använder nyare supportpaket behöver du installera det här paketet i erfordrad version direkt för att undvika konflikter.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
