1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**. Välj **skapa nya** och sedan **publicera**. 

    ![Skapa och publicera ny funktionsapp](./media/functions-vstools-publish/functions-vstools-publish-new-function-app.png)

2. Om du inte har redan anslutit Visual Studio till ditt Azure-konto, Välj **Lägg till ett konto...** .  

3. I dialogrutan **Create App Service** (Skapa apptjänst) använder du **värdinställningarna** på det sätt som beskrivs i tabellen nedan. 

    ![Lokal Azure-körningsmiljö](./media/functions-vstools-publish/functions-vstools-publish.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appnamn** | Globalt unikt namn | Namn som unikt identifierar din nya funktionsapp. |
    | **Prenumeration** | Välj din prenumeration | Den Azure-prenumeration som ska användas. |
    | **[Resursgrupp](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Namnet på resursgruppen som funktionsappen ska skapas i. Välj **ny** att skapa en ny resursgrupp.|
    | **[App Service-plan](../articles/azure-functions/functions-scale.md)** | Förbrukningsplan | Se till att välja den **förbrukning** under **storlek** när du klickar på **ny** att skapa en ny plan. Också välja en **plats** i en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster dina funktioner har åtkomst till.  |

    >[!NOTE]
    >Ett Azure storage-konto krävs av Functions-runtime. Därmed skapas ett nytt Azure Storage-konto för dig när du skapar en funktionsapp.

4. Klicka på **skapa** att skapa en funktionsapp och relaterade resurser i Azure med dessa inställningar och distribuera ditt projekt funktionskoden. 

5. När installationen är klar, notera den **Webbadress** -värde som är adressen till appen funktionen i Azure.

    ![Lokal Azure-körningsmiljö](./media/functions-vstools-publish/functions-vstools-publish-profile.png)
