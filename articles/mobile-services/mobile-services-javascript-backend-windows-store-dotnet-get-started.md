---
title: Komma igång med Mobile Services för Windows Store-appar (C#) | Microsoft Docs
description: Den här kursen hjälper dig att komma igång med Azure Mobile Services för Windows Store-utveckling i C#.
services: mobile-services
documentationcenter: windows
author: ggailey777
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 07/21/2016
ms.author: glenga

---
# <a name="getting-started"> </a>Komma igång med Mobile Services
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Motsvarande avsnitt för Mobile Apps finns i [Skapa en Windows-app](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md).
> 
> 

Den här kursen visar hur du lägger till en molnbaserad serverdelstjänst för en universell Windows-app med Azure Mobile Services. Universella Windows-applösningar omfattar projekt för både Windows Store 8.1- och Windows Phone Store 8.1-appar och ett gemensamt delat projekt. Mer information finns i [Skapa universella Windows-appar för Windows och Windows Phone](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx).

I kursen får du skapa både en ny mobiltjänst och en enkel *To do list*-app (att göra-lista) som lagrar appdata i den nya mobiltjänsten. I mobiltjänsten du skapar används JavaScript för affärslogik på serversidan. Om du vill skriva affärslogik på serversidan i .NET-språk som stöds med Visual Studio kan du läsa .NET-serverdelsversion i det här avsnittet.

[!INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

Följande krävs för att kunna genomföra kursen:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till 10 mobiltjänster utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information finns i [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Visual Studio 2013 Express för Windows]

## Skapa en ny mobiltjänst
[!INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Skapa en ny universell Windows-app
När du har skapat din mobiltjänst finns en snabbguide i den klassiska Azure-portalen om hur du skapar en ny universell Windows-app eller ändrar en befintlig Windows Store- eller Windows Phone-app som är ansluten till mobiltjänsten.

I det här avsnittet skapar du en ny universell Windows-app som är ansluten till din mobiltjänst.

1. Gå till den [Klassisk Azure-portal], klicka på **Mobile Services** och sedan på den mobiltjänst som du nyss skapade.
2. Välj snabbstartsfliken, klicka på **Windows** under **Choose platform** (Välj plattform) och expandera **Create a new Windows Store app** (Skapa en ny app för Windows Store).
   
    Därmed visas tre enkla steg för att skapa en Windows Store-app som är ansluten till din mobiltjänst.
   
    ![Snabbstartsguide för Mobile Services](./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)
3. Ladda ned och installera [Visual Studio 2013 Express för Windows] på den lokala datorn eller den virtuella datorn, om du inte redan gjort det.
4. Klicka på **Create TodoItem table** (Skapa ToDoItem-tabell) för att skapa en tabell för lagring av appdata.
5. Välj ett språk för appen under **Download and run your app (Ladda ned och kör appen)** och klicka sedan på **Download (Ladda ned)**.
   
    Projektet för *To do list*-appen som är ansluten till din mobiltjänst laddas ned. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.

## Kör Windows-appen
[!INCLUDE [mobile-services-javascript-backend-run-app](../../includes/mobile-services-javascript-backend-run-app.md)]

> [!NOTE]
> Du kan granska koden som ansluter till mobiltjänsten för att fråga efter och infoga data i filen MainPage.xaml.cs.
> 
> 

## Nästa steg
Nu när du har slutfört snabbstartskursen kan du gå vidare och lära dig hur du utför fler viktiga uppgifter i Mobile Services:

* [Komma igång med datasynkronisering offline] Läs om hur du kan använda datasynkronisering offline för att ge appen kortare svarstid och göra den mer robust. 
* [Lägga till autentisering i Mobile Services-appen ][Komma igång med autentisering]  
  Läs om hur du autentiserar användare i appen med en identitetsleverantör.
* [Lägga till push-meddelanden i appen][Komma igång med push-meddelanden]  
  Läs om hur du skickar väldigt enkla push-meddelanden till appen.
* [Använda .NET-klientbiblioteket](mobile-services-dotnet-how-to-use-client-library.md)  
  Läs om hur du frågar mobiltjänsten, arbetar med data och kommer åt anpassade API: er.

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Komma igång med Mobile Services]:#getting-started
[Skapa en ny mobiltjänst]:#create-new-service
[Definiera mobiltjänstinstansen]:#define-mobile-service-instance
[Nästa steg]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[Komma igång med datasynkronisering offline]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[Komma igång med autentisering]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md
[Komma igång med push-meddelanden]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md
[Visual Studio 2013 Express för Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Klassisk Azure-portal]: https://manage.windowsazure.com/



<!--HONumber=sep16_HO1-->


