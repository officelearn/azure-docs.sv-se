
Mobiltjänstens projekt som du hämtar låter dig köra din nya mobiltjänst direkt på din lokala dator eller virtuella dator. Detta gör det enkelt att felsöka koden för tjänsten innan du publicerar den till Azure.

I det här avsnittet testar du din nya app mot den mobiltjänst som körs lokalt.

1. Bläddra till den plats där du sparade de komprimerade projektfilerna, expandera filerna på datorn och öppna lösningsfilen i Visual Studio.

2. I Solution Explorer i Visual Studio högerklickar du på tjänstens projekt, klickar på **Ställ in som startprojekt** och trycker sedan på **F5** för att skapa projektet och starta mobiltjänsten lokalt.

    ![](./media/mobile-services-dotnet-backend-test-local-service-dotnet/mobile-service-startup.png)

    En webbsida visas när mobiltjänsten startats.

3. För att testa butiksappen högerklickar du på projektet för klientappen, klickar på **Ställ in som startprojekt** och trycker sedan på **F5** för att omkompilera projektet och starta appen.

    Detta startar appen, som ansluter till den lokala mobiltjänstinstansen.   

4. I appen anger du beskrivande text, som till exempel _Slutför guiden_, i **Infoga en TodoItem** och klickar sedan på **Spara**.

    Detta skickar en POST-begäran till den lokala mobiltjänsten. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobiltjänsten. Data visas i den andra kolumnen i appen.

<!--HONumber=Sep16_HO3-->


