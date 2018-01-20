Med Azure Functions-projektmallen i Visual Studio skapas ett projekt som kan publiceras till en funktionsapp i Azure. En funktion kan du gruppera funktioner som en logisk enhet för hantering, distribution och delning av resurser.   

1. I Visual Studio väljer **ny** > **projekt** från den **filen** menyn. 

2. I den **nytt projekt** markerar **installerad**, expandera **Visual C#** > **moln**väljer **Azure Funktioner**, ange ett **namn** för ditt projekt och klickar på **OK**. Funktionsappens namn måste vara ett giltigt C#-namnområde. Du kan inte använda understreck, bindestreck eller andra icke-alfanumeriska tecken. 

    ![Dialogrutan Nytt projekt för att skapa en funktion i Visual Studio](./media/functions-vstools-create/functions-vstools-add-new-project.png) 

2. Använd inställningarna i tabellen nedan avbildningen.
 
    ![Dialogrutan ny funktion i Visual Studio](./media/functions-vstools-create/functions-vstools-add-new-function.png) 

    | Inställning      | Föreslaget värde  | Beskrivning                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Version** | Azure Functions v1 <br />(.NET Framework) | Detta skapar ett projekt för funktionen som använder Azure Functions version 1 körningsfel. Version 2-körningsmiljön som stöder .NET Core är för närvarande under förhandsgranskning. Mer information finns i [så målversionen Azure Functions-runtime](../articles/azure-functions/functions-versions.md).   | 
    | **Mall** | HTTP-utlösare | Detta skapar en funktion som utlöses av en HTTP-begäran. |
    | **Lagringskonto**  | Storage-emulatorn | En HTTP-utlösare använder inte kontot anslutning till lagringsplatsen. Alla andra typer av utlösare kräver en giltig anslutningssträng för Storage-konto. |
    | **Behörighet som krävs** | Anonym | Funktionen skapade kan utlösas av alla klienter utan att ange en nyckel. Den här inställningen för auktorisering gör det enkelt att testa den nya funktionen. Mer information om nycklar och auktorisering finns [auktorisering nycklar](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) i den [HTTP och webhook bindningar](../articles/azure-functions/functions-bindings-http-webhook.md). |         
3. Klicka på **OK** skapa funktionen projekt och HTTP aktiveras funktionen. 

