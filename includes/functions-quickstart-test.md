
Eftersom Azure Functions-snabbstart innehåller funktionell kod kan du testa den nya funktionen omedelbart.

1. På fliken **Utveckla** kan du granska fönstret **Kod** och se att den angivna koden förväntar sig en HTTP-begäran med ett *namn*-värde skickat antingen i meddelandetexten eller i en frågesträng. När funktionen körs returneras värdet i svarsmeddelandet. Exemplet som visas nedan är en JavaScript-funktion.
   
2. Klicka på **Testa** för att visa det inbyggda fönstret för HTTP-testbegäran för funktionen.
 
    ![](./media/functions-quickstart-test/function-app-develop-tab-testing.png)

3. I textrutan **Begärandetext** ändrar du värdet för egenskapen *Namn* till ditt namn och klickar på **Kör**. Du ser att utförandet utlöses av en HTTP-testbegäran, informationen skrivs till loggarna och svaret "hello..." visas i **Utdata**. 

4. Om du vill utlösa körningen av samma funktion från ett HTTP-testverktyg eller i ett annat fönster i webbläsaren kopierar du värdet för **Funktionswebbadress** från fliken **Utveckla** och klistrar in det i verktyget eller webbläsarens adressfält. Lägg till frågesträngsvärdet `&name=yourname` till URL:en och utför begäran. Observera att samma information skrivs till loggarna och att samma sträng finns i brödtexten i svarsmeddelandet.

    ![](./media/functions-quickstart-test/function-app-browser-testing.png)
