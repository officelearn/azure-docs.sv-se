---
title: Komma igång med Azure Mobile Services för PhoneGap/cordova-appar| Microsoft Docs
description: Den här kursen hjälper dig att komma igång med Azure Mobile Services för PhoneGap-utveckling för iOS, Android och Windows Phone.
services: mobile-services
documentationcenter: ''
author: ggailey777
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-phonegap
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 07/21/2016
ms.author: ggailey777

---
# Komma igång med Mobile Services
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

Den här kursen visar hur du lägger till en molnbaserad serverdelstjänst för en app med Azure Mobile Services. I kursen får du skapa både en ny mobiltjänst och en enkel *To do list*-app (att göra-lista) som lagrar appdata i den nya mobiltjänsten.

En skärmbild från den färdiga appen:

![][3]

### Ytterligare krav
För den här kursen krävs följande:

* PhoneGap-verktyg (v3.2+ krävs för Windows Phone 8-projekt).
* Ett aktivt Microsoft Azure-konto.
* PhoneGap stöder utveckling för flera plattformar. Förutom PhoneGap-verktygen måste du installera verktyg för varje plattform som du ska utveckla för:
  
  * Windows Phone: Installera [Visual Studio 2012 Express för Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374)
  * iOS: Installera [Xcode](v4.4+ krävs)
  * Android: Installera [Android Developer Tools][Android SDK]
      <br/>(Mobile Services SDK för Android stöder appar för Android 2.2 eller senare. Android 4.2 eller senare krävs för att köra snabbstartsappen.)

## Skapa en ny mobiltjänst
[!INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Skapa en ny PhoneGap-app
I det här avsnittet skapar du en ny PhoneGap-app som är ansluten till din mobiltjänst.

1. Gå till den [Klassisk Azure-portal], klicka på **Mobile Services** och sedan på den mobiltjänst som du nyss skapade.
2. Välj snabbstartsfliken, klicka på **PhoneGap** under **Choose platform** (Välj plattform) och expandera **Create a new PhoneGap app** (Skapa en ny PhoneGap-app).
   
    ![][0]
   
    Därmed visas tre enkla steg för att skapa en PhoneGap-app som är ansluten till din mobiltjänst.
   
    ![][1]
3. Ladda ned och installera PhoneGap och utvecklingsverktygen för minst en plattform (Windows Phone, iOS eller Android), om du inte redan gjort det.
4. Klicka på **Create TodoItem table** (Skapa ToDoItem-tabell) för att skapa en tabell för lagring av appdata.
5. Under **Download and run your app** (Ladda ned och kör appen) klickar du på **Download** (Ladda ned).
   
    Projektet för *To do list*-appen som är ansluten till mobiltjänsten laddas ned tillsammans med Mobile Services JavaScript SDK. Spara den komprimerade projektfilen lokalt på datorn och notera var du sparar den.

## Köra den nya PhoneGap-appen
Det sista steget i den här kursen är att skapa och köra den nya appen.

1. Bläddra till den plats där du sparade de komprimerade projektfilerna och expandera filerna på datorn.
2. Öppna och kör projektet enligt instruktionerna nedan för varje plattform.
   
   * **Windows Phone 8**
     
     1. Windows Phone 8: Öppna SLN-filen i mappen **platforms\wp8** i Visual Studio 2012 Express för Windows Phone.
     2. Tryck på **F5** för att återskapa projektet och starta appen.
        
        ![][2]
   * **iOS**
     
     1. Öppna projektet i mappen **plattformar/ios** i Xcode.
     2. Tryck på **Kör** för att skapa klientprojektet och starta appen i iPhone-emulatorn (vilket är standard för det här projektet).
        
        ![][3]
   * **Android**
     
     1. I Eclipse: Klicka på **Fil**, klicka på **Importera**, expandera **Android**, klicka på **Befintlig Android-kod i arbetsyta** och klicka sedan på **Nästa**.
     2. Klicka på **Browse**, bläddra till platsen där de expanderade projektfilerna finns och klicka på **OK**. Kontrollera att projektet TodoActivity är markerat och klicka sedan på **Finish**. <p>Därmed importeras projektfilerna till den aktuella arbetsytan.</p>
     3. Starta projektet i Android-emulatorn genom att klicka på **Kör** på **Kör**-menyn.
        
         ![][4]
        
        > [!NOTE]
        > För att kunna köra projektet i Android-emulatorn måste du definiera minst en Android Virtual Device (AVD). Du kan skapa och hantera dessa enheter med AVD Manager.
        > 
        > 
3. När du har startat appen i någon av emulatorerna ovan skriver du lite text i textrutan och klickar sedan på **Lägg till**.
   
    Därmed skickas en POST-begäran till den nya mobiltjänsten som finns på Azure. Data från begäran infogas i tabellen **TodoItem**. Objekt som lagras i tabellen returneras av mobiltjänsten och data visas i listan.
   
   > [!IMPORTANT]
   > Observera att ändringarna i det här plattformsprojektet skrivs över om huvudprojektet återskapas med PhoneGap-verktygen. Gör i stället ändringar i projektets www-rotkatalog (se nedan).
   > 
   > 
4. Gå till den [Klassisk Azure-portal], klicka på fliken **Data** och klicka sedan på tabellen **TodoItem**.
   
    ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png)
   
    Nu kan du bläddra bland de data som infogats i tabellen via appen.
   
    ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png)

## Göra appuppdateringar och återskapa projekt för varje plattform
1. Ändra kodfilerna i www-katalogen, som i det här fallet är todolist/www.
2. Kontrollera att alla verktyg för målplattformen är tillgängliga i systemsökvägen.
3. Öppna en kommandotolk i projektets rotkatalog och kör något av följande plattformsspecifika kommandon:
   
   * **Windows Phone**
     
       Kör följande kommando i Kommandotolken Visual Studio-utvecklare:
     
           phonegap local build wp8
   * **iOS**
     
       Öppna terminalen och kör följande kommando:
     
           phonegap local build ios
   * **Android**
     
       Öppna en kommandotolk eller ett terminalfönster och kör följande kommando.
     
           phonegap local build android
4. Öppna varje projekt i motsvarande utvecklingsmiljö enligt beskrivningen i föregående avsnitt.

> [!NOTE]
> Du kan granska koden som ansluter till mobiltjänsten för att fråga efter och infoga data i filen js/index.js.
> 
> 

## Nästa steg
Nu när du har slutfört guiden kan du gå vidare och lära dig fler viktiga uppgifter i Mobile Services:

* **[Lägg till autentisering i appen]**  
  Läs om hur du autentiserar användare i appen med en identitetsleverantör.  
* **[Lägg till push-meddelanden i appen](https://msdn.microsoft.com/magazine/dn879353.aspx)**  
  Läs om hur du registrerar dig för och skickar push-meddelanden till din app.
* **[HTML- och JavaScript-kodexempel för Mobile Services](mobile-services-html-how-to-use-client-library.md)**  
  Läs mer om hur du använder JavaScript-klientbiblioteket för att komma åt data, anropa anpassade API: er och utföra autentisering.

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Images. -->
[0]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
[1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
[2]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
[3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
[4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png

<!-- URLs. -->
[Lägg till autentisering i appen]: mobile-services-html-get-started-users.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Klassisk Azure-portal]: https://manage.windowsazure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio 2012 Express för Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374



<!--HONumber=sep16_HO1-->


