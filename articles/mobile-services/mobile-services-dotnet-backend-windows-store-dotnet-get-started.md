<properties
    pageTitle="Komma igång med Mobile Services för universella Windows-appar | Microsoft Azure"
    description="Den här kursen hjälper dig att komma igång med Azure Mobile Services för utveckling av universella Windows-appar i C#."
    services="mobile-services"
    documentationCenter="windows"
    authors="ggailey777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="07/21/2016"
    ms.author="glenga"/>


# <a name="getting-started"> </a>Komma igång med Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Motsvarande avsnitt för Mobile Apps finns i [Skapa en Windows-app med Mobile Apps](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md).

I den här kursen får du veta hur du lägger till en molnbaserad serverdelstjänst för en universell Windows-app med Azure Mobile Services. Universella Windows-applösningar omfattar projekt för både Windows Store 8.1- och Windows Phone Store 8.1-appar och ett gemensamt delat projekt. Mer information finns i [Skapa universella Windows-appar för Windows och Windows Phone](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx).

I kursen får du skapa både en ny mobiltjänst och en enkel *To do list*-app (att göra-lista) som lagrar appdata i den nya mobiltjänsten. I mobiltjänsten du skapar används .NET-språk som stöds med Visual Studio för affärslogik på serversidan och för att hantera mobiltjänsten. Om du vill skriva affärslogik på serversidan i JavaScript kan du läsa JavaScript-serverdelsversion i det här avsnittet.

>[AZURE.NOTE]I det här avsnittet beskrivs hur du skapar ett nytt mobiltjänstprojekt och en universell Windows-app med den klassiska Azure-portalen. Med Visual Studio 2013 Update 3 kan du också lägga till ett nytt mobiltjänstprojekt i en befintlig Visual Studio-lösning. Mer information finns i [Lägga till Mobile Services i en befintlig app](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md).

>Information om hur du lägger till en mobiltjänst i ett projekt för Windows Phone 8.0 eller Windows Phone Silverlight 8.1-app finns i [Lägga till Mobile Services i en befintlig Windows Phone-app](mobile-services-windows-phone-get-started-data.md).

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till 10 mobiltjänster utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information finns i [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started%2F).
* [Visual Studio 2013].

## Skapa en ny mobiltjänst

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Skapa en ny universell Windows-app

När du har skapat din mobiltjänst finns en snabbstartsguide i den klassiska Azure Portal om hur du skapar en ny app eller ändrar en befintlig app som är ansluten till mobiltjänsten.

I det här avsnittet skapar du en ny universell Windows-app som är ansluten till din mobiltjänst.

1. Gå till den [Klassisk Azure-portal], klicka på **Mobile Services** och sedan på den mobiltjänst som du nyss skapade.

2. Välj snabbstartsfliken, klicka på **Windows** under **Choose platform** (Välj plattform) och expandera **Create a new Windows Store app** (Skapa en ny app för Windows Store).

    Därmed visas tre enkla steg för att skapa en Windows Store-app som är ansluten till din mobiltjänst.

    ![Snabbstartsguide för Mobile Services](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)

3. Ladda ned och installera [Visual Studio 2013] på den lokala eller virtuella datorn, om du inte redan gjort det.

4. Välj ett språk för Windows Store-appen under **Download and run your app and service locally** (Ladda ned och kör appen och tjänsten lokalt) och klicka sedan på **Download** (Ladda ned).

    En fil med projekt för både mobiltjänsten och _To do list_-appen som är ansluten till mobiltjänsten laddas ned. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.

## Testa appen mot den lokala mobila tjänsten

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[AZURE.NOTE]Du kan granska koden som ansluter till mobiltjänsten för att fråga efter och infoga data i filen MainPage.xaml.cs.


## Publicera mobiltjänsten

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


<ol start="4">
<li><p>I projektet med delad kod öppnar du filen App.xaml.cs, letar upp koden som skapar en <a href="http://msdn.microsoft.com/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a>-instans, kommenterar ut den kod som skapar klienten med <em>localhost</em> och tar bort kommentarstecknet för koden som skapar klienten med den mobila fjärrtjänstens URL. Det ser ut så här:</p>

        <pre><code>public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXX-APPLICATION-KEY-XXXXX");</code></pre>

    <p>Klienten har nu åtkomst till den mobila tjänst som publicerats till Azure.</p></li>
</ol>

## Testa appen mot den lokala mobila tjänsten i Azure

Nu när mobiltjänsten har publicerats och klienten är ansluten till fjärrmobiltjänsten i Azure kan vi köra appen med Azure som lagringsplats för objekt.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]


## Nästa steg
Nu när du har slutfört snabbstartskursen kan du gå vidare och lära dig hur du utför fler viktiga uppgifter i Mobile Services:

* [Lägga till Mobile Services i en befintlig app][Komma igång med data]
  <br/>Läs mer om lagring och hämtning av data med Mobile Services.

* [Komma igång med datasynkronisering offline]
  <br/>Läs om hur du kan använda datasynkronisering offline för att ge appen kortare svarstid och göra den mer robust.

* [Lägga till autentisering i Mobile Services-appen ][Komma igång med autentisering]
  <br/>Läs om hur du autentiserar användare i appen med en identitetsleverantör.

* [Lägga till push-meddelanden i appen][Komma igång med push-meddelanden]
  <br/>Läs om hur du skickar väldigt enkla push-meddelanden till appen.

* [Felsöka en Mobile Services .NET-serverdel]
  <br/> Läs om hur du diagnostiserar och åtgärdar problem som kan uppstå med Mobile Services .NET-serverdelen.

Mer information om universella Windows-appar finns i [Stödja flera enhetsplattformar från en enda mobiltjänst](mobile-services-how-to-use-multiple-clients-single-service.md#shared-vs).

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->

<!-- Images. -->



<!-- URLs. -->
[Visual Studio 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Komma igång med data]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md
[Komma igång med datasynkronisering offline]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[Komma igång med autentisering]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[Komma igång med push-meddelanden]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript och HTML]: mobile-services-win8-javascript/
[Klassisk Azure-portal]: https://manage.windowsazure.com/
[Felsöka en Mobile Services .NET-serverdel]: mobile-services-dotnet-backend-how-to-troubleshoot.md



<!--HONumber=sep16_HO1-->


