<properties
    pageTitle="Komma igång med Mobile Services för Windows Store JavaScript-appar | Azure Mobile Services"
    description="Den här kursen hjälper dig att komma igång med Azure Mobile Services för Windows Store-utveckling i JavaScript."
    services="mobile-services"
    documentationCenter="windows"
    authors="ggailey777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="javascript"
    ms.topic="get-started-article"
    ms.date="07/21/2016"
    ms.author="glenga"/>


# Komma igång med Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Motsvarande avsnitt för Mobile Apps finns i [Skapa en Windows-app med Mobile Apps](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md).  

Den här kursen visar hur du lägger till en molnbaserad serverdelstjänst för en Windows Store JavaScript-app med Azure Mobile Services. I kursen får du skapa både en ny mobiltjänst och en enkel *To do list*-app (att göra-lista) som lagrar appdata i den nya mobiltjänsten. I mobiltjänsten du skapar används JavaScript för affärslogik på serversidan. 

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Visual Studio 2013 Express för Windows]

## Skapa en ny mobiltjänst

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Skapa en ny Windows Store-app

När du har skapat din mobiltjänst finns en snabbstartsguide i den klassiska Azure-portalen om hur du skapar en ny Windows Store 8.1 JavaScript-app som är ansluten till mobiltjänsten.

1.  Gå till den [Klassisk Azure-portal], klicka på **Mobile Services** och sedan på den mobiltjänst som du nyss skapade.


2. Välj snabbstartsfliken, klicka på **Windows** under **Choose platform** (Välj plattform) och expandera **Create a new Windows Store app** (Skapa en ny app för Windows Store).

3. Ladda ned och installera [Visual Studio 2013][Visual Studio 2013 Express för Windows] på den lokala datorn eller den virtuella datorn, om du inte redan gjort det.

4. Klicka på **Create TodoItem table** (Skapa ToDoItem-tabell) för att skapa en tabell för lagring av appdata.

5. Välj ett språk för appen under **Download and run your app (Ladda ned och kör appen)** och klicka sedan på **Download (Ladda ned)**.

    Projektet för *To do list*-appen som är ansluten till din mobiltjänst laddas ned. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.

## Kör Windows-appen

Det sista steget i den här kursen är att skapa och köra den nya appen.

1. Bläddra till den plats där du sparade de komprimerade projektfilerna, expandera filerna på datorn och öppna lösningsfilen i Visual Studio.

2. Tryck på **F5** för att återskapa projektet och starta appen.

3. Ange en beskrivande text i **Infoga ett TodoItem**, till exempel *Slutföra kursen*, och klicka sedan på **Spara**.

    Därmed skickas en POST-begäran till den nya mobiltjänsten som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobiltjänsten. Data visas i den andra kolumnen i appen.

4. (Valfritt) Kör app igen och observera att de data som sparades i föregående steg läses in från mobiltjänsten när appen startar.
 
4. Gå till den [Klassisk Azure-portal], välj fliken **Data** och klicka sedan på tabellen **TodoItems**.

    Nu kan du bläddra bland de data som infogats i tabellen via appen.

>[AZURE.NOTE] Du kan granska koden som ansluter till mobiltjänsten för att fråga efter och infoga data i filen default.js.

## Nästa steg
Nu när du har slutfört guiden kan du gå vidare med att lära dig att arbeta med [Mobile Services-klienten för HTML/JavaScript](mobile-services-html-how-to-use-client-library.md). 

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Komma igång med Mobile Services]:#getting-started
[Skapa en ny mobiltjänst]:#create-new-service
[Definiera mobiltjänstinstansen]:#define-mobile-service-instance
[Nästa steg]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Visual Studio 2013 Express för Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Klassisk Azure-portal]: https://manage.windowsazure.com/



<!--HONumber=Sep16_HO3-->


