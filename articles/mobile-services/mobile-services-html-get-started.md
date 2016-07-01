<properties
    pageTitle="Komma igång med Azure Mobile Services för HTML/JavaScript-appar | Microsoft Azure"
    description="Den här kursen hjälper dig att komma igång med Azure Mobile Services för HTML-utveckling"
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html5"
    ms.devlang="javascript"
    ms.topic="get-started-article" 
    ms.date="11/30/2015"
    ms.author="glenga"/>


# <a name="getting-started"> </a>Komma igång med Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

##Översikt 

Den här kursen visar hur du lägger till en molnbaserad serverdelstjänst för en HTML-app med Azure Mobile Services. I kursen får du skapa både en ny mobiltjänst och en enkel *To do list*-app (att göra-lista) som lagrar appdata i den nya mobiltjänsten. Du kan se videoversionen av kursen nedan. 

> [AZURE.VIDEO mobile-get-started-html]
 
En skärmbild från den färdiga appen:

![][0]

Att slutföra den här kursen är en förutsättning för alla andra kurser i Mobile Services för HTML-appar. Det finns också en [PhoneGap/Cordova-version](mobile-services-javascript-backend-phonegap-get-started.md) av kursen.

##Krav

Följande krävs för att kunna genomföra kursen:

+ Du måste ha någon av följande webbservrar på din lokala dator:

    +  **I Windows**: IIS Express. IIS Express installeras med [Microsoft Web Platform Installer].
    +  **I MacOS X**: Python, har normalt redan installerats.
    +  **I Linux**: Python. Du måste installera den [senaste versionen av Python].

    Du kan ha din app på valfri webbserver, men det är dessa webbservrar som stöds av de skript som laddas ned.  

+ En webbläsare som stöder HTML5.
+ Ett Azure-konto. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F"%20target="_blank). 


## <a name="create-new-service"> </a>Skapa en ny mobiltjänst

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Skapa en ny HTML-app

När du har skapat din mobiltjänst finns en snabbstartsguide i den klassiska Azure Portal om hur du skapar en ny app eller ändrar en befintlig app som är ansluten till mobiltjänsten.

I det här avsnittet skapar du en ny HTML-app som är ansluten till din mobiltjänst.

1.  Gå till den [Klassisk Azure-portal], klicka på **Mobile Services** och sedan på den mobiltjänst som du nyss skapade.


2. Välj snabbstartsfliken, klicka på **Windows** under **Choose platform** (Välj plattform) och expandera **Create a new HTML app** (Skapa en ny HTML-app).

    ![][6]

    Därmed visas tre enkla steg för att skapa och vara värd för en HTML-app som är ansluten till din mobiltjänst.

    ![][7]

3. Klicka på **Create TodoItems table** för att skapa en tabell för lagring av appdata.

4. Under **Download and run your app** (Ladda ned och kör appen) klickar du på **Download** (Ladda ned).

    _To do list_-appen som är ansluten till din mobiltjänst laddas ned. Spara den komprimerade filen lokalt på datorn och notera var du sparar den.

5. Välj fliken **Configure** och kontrollera att `localhost` redan finns i listan **Allow requests from host names** (Tillåt begäranden från värdnamn) under **Cross-Origin Resource Sharing (CORS)**. Om den inte gör det skriver du `localhost` i fältet **Host name** och klickar på **Save**.

    ![][9]

    > [AZURE.IMPORTANT] Om du ska distribuera appen i den här guiden till en annan webbserver än localhost måste du lägga till värdnamnet för webbservern i listan **Allow requests from host names** (Tillåt begäranden från värdnamn). Mer information finns i [Cross-origin resource sharing](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx).

## Konfigurera värd för och köra HTML-appen

Det sista steget i den här kursen är att konfigurera datorn som värd och köra den nya appen lokalt på datorn.

1. Bläddra till den plats där du sparade de komprimerade projektfilerna, expandera filerna på datorn och starta en av följande kommandofiler från undermappen **server**.

    + **launch-windows** (Windows-datorer)
    + **launch-mac.command** (Mac OS X-datorer)
    + **launch-linux.sh** (Linux-datorer)

    > [AZURE.NOTE] På en Windows-dator: Skriv `R` när PowerShell frågar om du vill köra skriptet. Webbläsaren kan varna dig att inte köra skriptet eftersom det laddades ned från Internet. I så fall måste du ange att webbläsaren ska fortsätta att läsa in skriptet.

    Därmed startas en webbserver på din lokala dator som värd för den nya appen.

2. Starta appen genom att öppna URL:en <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> i en webbläsare.

3. Ange en beskrivande text i **Enter new task** (Ange en ny uppgift) i appen, till exempel _Slutföra kursen_, och klicka sedan på **Add** (Lägg till).

    ![][10]

    Därmed skickas en POST-begäran till den nya mobiltjänsten som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobiltjänsten. Data visas i den andra kolumnen i appen.

    > [AZURE.NOTE] Du kan granska koden som ansluter till mobiltjänsten för att fråga efter och infoga data i filen page.js.

4. Gå till den [Klassisk Azure-portal], välj fliken **Data** och klicka sedan på tabellen **TodoItems**.

    ![][11]

    Nu kan du bläddra bland de data som infogats i tabellen via appen.

    ![][12]

## <a name="next-steps"> </a>Nästa steg
Nu när du har slutfört guiden kan du lära dig fler viktiga uppgifter i Mobile Services:

* **[Lägga till autentisering i appen]**  
  Läs om hur du autentiserar användare i appen med en identitetsprovider.

* **[HTML- och JavaScript-kodexempel för Mobile Services]**  
  Läs mer om hur du använder Mobile Services med HTML eller JavaScript


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Komma igång med Mobile Services]:#getting-started
[Skapa en ny mobiltjänst]:#create-new-service
[Definiera mobiltjänstinstansen]:#define-mobile-service-instance
[Nästa steg]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png

[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[Lägga till autentisering i appen]: mobile-services-html-get-started-users.md

[Klassisk Azure-portal]: https://manage.windowsazure.com/
[Microsoft Web Platform Installer]:  http://go.microsoft.com/fwlink/p/?LinkId=286333
[senaste versionen av Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[HTML- och JavaScript-kodexempel för Mobile Services]: mobile-services-html-how-to-use-client-library.md
[Cross-origin resource sharing (CORS)]: http://msdn.microsoft.com/library/azure/dn155871.aspx
 



<!--HONumber=Jun16_HO2-->


