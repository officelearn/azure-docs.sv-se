---
title: "Kom igång med Azure Mobile Apps för Xamarin.Android-appar"
description: "I den här kursen får du hjälp att komma igång med Azure Mobile Apps för Xamarin Android-utveckling"
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: adrianha
editor: 
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 4d34bb29df95ae83952d8f421f3f2a9118ad5e1d
ms.lasthandoff: 01/20/2017


---
# <a name="create-a-xamarinandroid-app"></a>Skapa en Xamarin.Android-app
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Översikt
I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Xamarin.Android-app. Mer information om Mobile Apps finns [här](app-service-mobile-value-prop.md).

En skärmbild från den färdiga appen:

![][0]

Du måste slutföra den här kursen innan du börjar någon annan kurs om Mobile Apps för Xamarin.Android-appar.

## <a name="prerequisites"></a>Krav
För att slutföra den här självstudien, finns följande förhandskrav:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till tio mobilappar utan kostnad. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio med Xamarin. Instruktioner finns i avsnittet om [konfiguration och installation av Visual Studio och Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto, kan du gå till [Testa App Service](https://azure.microsoft.com/try/app-service/mobile/).  Du kan skapa en kortvarig startmobilapp omedelbart i App Service. Inget kreditkort krävs, och du gör inga åtaganden.
> 
> 

## <a name="create-an-azure-mobile-app-backend"></a>Skapa en mobilapp-serverdel i Azure
Följ de här stegen för att skapa en mobilapp-serverdel.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Du har nu skapat en mobilsappsserverdel i Azure som kan användas av dina mobilklientprogram. Därefter, hämtar du ett serverprojekt för en enkel ”att göra lista”-serverdel och publicerar den till Azure.

## <a name="configure-the-server-project"></a>Konfigurera serverprojektet
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinandroid-app"></a>Hämta och kör Xamarin.Android-appen
1. Under **Hämta och kör ditt Xamarin.Android-projekt** klickar du på **Hämta**.
   
      Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.
2. Tryck på **F5** för att skapa projektet och starta appen.
3. Ange en beskrivande text i appen, till exempel *Slutför kursen*, och klicka sedan på **Lägg till**.
   
    ![][10]
   
    Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobilappsserverdelen och data visas i listan.
   
   > [!NOTE]
   > Du kan se koden som ansluter till mobilappsserverdelen för att fråga efter och infoga data i C#-filen ToDoActivity.cs.
   > 
   > 

## <a name="next-steps"></a>Nästa steg
* [Lägg till offlinesynkronisering till din app](app-service-mobile-xamarin-android-get-started-offline-data.md)
* [Lägg till autentisering till din app ](app-service-mobile-xamarin-android-get-started-users.md)
* [Lägg till push-meddelanden till din Xamarin.Android-app](app-service-mobile-xamarin-android-get-started-push.md)
* [Så här använder du den hanterade klienten för Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203

