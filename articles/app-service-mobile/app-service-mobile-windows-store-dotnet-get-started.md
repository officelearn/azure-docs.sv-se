---
title: "Skapa en universell Windowsplattform (UWP) som använder Mobile Apps | Microsoft Docs"
description: "Följ den här kursen och kom igång med att använda serverdelar för mobilappar i Azure för utveckling av appar med den universella Windowsplattformen (UWP) i C#, Visual Basic eller JavaScript."
services: app-service\mobile
documentationcenter: windows
author: adrianhall
manager: erikre
editor: 
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 88f9a6b7e1f49ad58e91165a772e8453163d224c


---
# <a name="create-a-windows-app"></a>Skapa en Windows-app
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Översikt
I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en app i den universella Windowsplattformen (UWP). Mer information om Mobile Apps finns [här](app-service-mobile-value-prop.md). Nedan visas skärmdumpar från den färdiga appen:

![Färdig skrivbordsapp](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)   
Visad på ett skrivbord 

![Färdig mobilapp](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)  
Visad på en telefon

Du måste slutföra den här kursen innan du går någon annan kurs om Mobilappar för UWP-appar. 

## <a name="prerequisites"></a>Krav
För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till tio mobilappar utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [här](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio Community 2015] eller senare version.

> [!NOTE]
> Om du vill komma igång med Azure Apptjänst innan du registrerar dig för ett Azure-konto kan du gå till [Prova Apptjänst](https://tryappservice.azure.com/?appServiceName=mobile). Där kan du direkt skapa en tillfällig startmobilapp i Apptjänst. Inget kreditkort behövs och du gör inga åtaganden.
> 
> 

## <a name="create-a-new-azure-mobile-app-backend"></a>Skapa en ny mobilappsserverdel i Azure
Skapa en ny mobilappsserverdel genom att följa instruktionerna nedan.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Du har nu skapat en mobilsappsserverdel i Azure som kan användas av dina mobilklientprogram. I nästa steg får du ladda ned ett serverprojekt för en enkel todo-list-serverdel och publicera den på Azure.

## <a name="configure-the-server-project"></a>Konfigurera serverprojektet
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-client-project"></a>Hämta och kör klientprojektet
När du har konfigurerat serverdelen för mobilappen kan du antingen skapa en ny klientapp eller modifiera en befintlig app som ansluts till Azure. I det här avsnittet får du ladda du ned en UWP-appsmall som är särskilt anpassad för att ansluta till din mobilappsserverdel.

1. Gå tillbaka till bladet **Snabbstart** för din mobilappsserverdel och klicka på **Skapa ny app** > **Hämta**. Extrahera sedan den komprimerade filen lokalt på din dator.
   
    ![Hämta ett snabbstartsprojekt för Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)
2. (Valfritt) Lägg UWP-appsprojektet i samma lösning som serverprojektet. På så sätt blir det enklare att felsöka och testa både appen och serverdelen i samma Visual Studio-lösning om du skulle vilja det. För att kunna lägga in ett UWP-appsprojekt i lösningen måste du ha Visual Studio 2015 eller en senare version.
3. Med UWP-appen som startprojekt trycker du på F5-tangenten för att distribuera och köra appen.
4. I appen anger du en beskrivande text i textrutan **Infoga TodoItem**, till exempel *Slutför kursen* och sedan klickar du på **Spara**.
   
    ![Färdig Windows-snabbstartsapp på skrivbord](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)
   
    En POST-begäran skickas till den nya appserverdelen som finns på Azure.
5. (Valfritt) Stäng av appen och starta om den på en annan enhet eller mobilemulator.
   
    ![Färdig Windows-snabbstartsapp på telefon](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)
   
    Observera att data som sparades i föregående steg läses in från Azure när UWP-appen startar. 

## <a name="next-steps"></a>Nästa steg
* [Lägg till autentisering i appen](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Läs om hur du autentiserar användare i appen med en identitetsleverantör.
* [Lägg till push-meddelanden i appen](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Läs om hur du lägger till stöd för push-meddelanden i appen och konfigurerar serverdelen för mobilappen så att Azure Notification Hubs används för att skicka push-meddelanden.
* [Aktivera offlinesynkronisering av appen](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Läs om hur du lägger till offlinestöd i appen genom en mobilappsserverdel. Med offlinesynkronisering kan slutanvändarna interagera med mobilappen och &mdash;se, lägga till och ändra data&mdash; även när det inte finns någon nätverksanslutning.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: https://go.microsoft.com/fwLink/p/?LinkID=534203



<!--HONumber=Dec16_HO1-->


