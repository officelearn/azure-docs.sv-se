---
title: "Kom igång med Azure Apptjänst Mobile Apps för Xamarin.iOS-appar | Microsoft Docs"
description: "I den här kursen får du hjälp att komma igång med Mobile Apps för Xamarin.iOS-utveckling."
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: erikre
editor: 
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: ed289d0755bbad08de01b0f311d14f5514ce0631
ms.lasthandoff: 01/20/2017


---
# <a name="create-a-xamarinios-app"></a>Skapa en Xamarin.iOS-app
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Översikt
I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Xamarin.iOS-mobilapp med hjälp av en mobilappsserverdel i Azure.  Du skapar både en ny mobilapp-serverdel och en enkel *Att göra-lista* Xamarin.iOS-app som lagrar app-data i Azure.

Du måste slutföra den här kursen innan du påbörjar någon annan Xamarin.iOS-kurs om att använda funktionen Mobile Apps i Azure Apptjänst.

## <a name="prerequisites"></a>Krav
För att slutföra den här självstudien, finns följande förhandskrav:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till tio mobilappar utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio med Xamarin. Instruktioner finns i avsnittet om [konfiguration och installation av Visual Studio och Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
* En Mac med Xcode v7.0 eller senare och Xamarin Studio Community installerat. Se avsnittet om [konfiguration och installation av Visual Studio och Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) och om [konfiguration, installation och verifieringar för Mac-användare](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

> [!NOTE]
> Om du vill komma igång med Azure Apptjänst innan du registrerar dig för ett Azure-konto kan du gå till [Prova Apptjänst](https://azure.microsoft.com/try/app-service/mobile/). Där kan du direkt skapa en tillfällig startmobilapp i App Service. Det krävs inget kreditkort och du gör inga åtaganden.
> 
> 

## <a name="create-an-azure-mobile-app-backend"></a>Skapa en mobilapp-serverdel i Azure
Följ de här stegen för att skapa en mobilapp-serverdel.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Konfigurera serverprojektet
Du har nu skapat en mobilsappsserverdel i Azure som kan användas av dina mobilklientprogram. Därefter, hämtar du ett serverprojekt för en enkel ”att göra lista”-serverdel och publicerar den till Azure.

Följ följande steg för att konfigurera serverprojektet att använda sig av antingen Node.js- eller .NET-serverdelen.

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>Hämta och kör Xamarin.iOS-appen
1. Öppna [Azure-portalen] i ett webbläsarfönster.
2. På inställningsbladet för mobilappen klickar du på **Kom igång** > **Xamarin.iOS**. Vid steg 3, klickar du på **Skapa en ny app** om det inte redan är valt.  Klicka sedan på **Hämta**.
   
      Ett klientprogram som ansluter till din mobila serverdel hämtas. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.
3. Extrahera projektet som du laddade ned och öppna det i Xamarin Studio (eller Visual Studio).
   
    ![][9]
   
    ![][8]
4. Tryck på F5 för att skapa projektet och starta appen i iPhone-emulatorn.
5. Ange en beskrivande text i appen, till exempel  *Läs om Xamarin*, och klicka sedan på **+**.
   
    ![][10]
   
    Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobilappsserverdelen och data visas i listan.

> [!NOTE]
> Du kan se koden som ansluter till mobilappsserverdelen för att fråga efter och infoga data i C#-filen QSTodoService.cs.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Lägg till offlinesynkronisering till din app](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [Lägg till autentisering till din app ](app-service-mobile-xamarin-ios-get-started-users.md)
* [Lägg till push-meddelanden till din Xamarin.Android-app](app-service-mobile-xamarin-ios-get-started-push.md)
* [Så här använder du den hanterade klienten för Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure-portalen]: https://portal.azure.com/

