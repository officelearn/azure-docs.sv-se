
Det sista steget i den här kursen är att skapa och köra den nya appen.

1. Bläddra till den plats där du sparade de komprimerade projektfilerna, expandera filerna på datorn och öppna lösningsfilen i Visual Studio.

2. Tryck på **F5** för att återskapa projektet och starta appen.

3. Ange en beskrivande text i **Infoga ett TodoItem**, till exempel *Slutföra kursen*, och klicka sedan på **Spara**.

    Därmed skickas en POST-begäran till den nya mobiltjänsten som finns på Azure. Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobiltjänsten. Data visas i den andra kolumnen i appen.

4. (Valfritt) Ändra standard-startprojektet i en universell Windows-lösning till den andra appen och kör appen igen.

    Observera att data som sparats i föregående steg läses in från mobiltjänsten när appen startar.
 
4. Tillbaka i den [klassiska Azure-portalen](https://manage.windowsazure.com/) klickar du på **Data**-fliken och klickar sedan på **TodoItems**-tabellen.

    Nu kan du bläddra bland de data som infogats i tabellen via appen.

    ![](./media/mobile-services-javascript-backend-run-app/mobile-data-browse.png)

<!--HONumber=Sep16_HO3-->


