1. Klicka på knappen **Nytt** i det övre vänstra hörnet i Azure Portal.

1. Klicka på **Beräkna** > **Funktionsapp** och välj din **prenumeration**. Använd sedan funktionsappinställningarna enligt tabellen.

    ![Skapa en funktionsapp i Azure Portal](./media/functions-create-function-app-portal/function-app-create-flow.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. | 
    | **[Resursgrupp](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Namnet på den nya resursgrupp där du vill skapa funktionsappen. | 
    | **[Värdplan](../articles/azure-functions/functions-scale.md)** |   Förbrukningsplan | Värdplan som definierar hur resurser allokeras till din funktionsapp. I **standardförbrukningsplanen** läggs resurser till dynamiskt när de behövs i funktionerna. Du betalar endast för den tid som dina funktioner körs.   |
    | **Plats** | Västra Europa | Välj en plats nära dig eller nära andra tjänster som kommer att användas i dina funktioner. |
    | **[Lagringskonto](../articles/storage/storage-create-storage-account.md#create-a-storage-account)** |  Globalt unikt namn |  Namnet på det nya lagringskonto som ska användas av funktionsappen. Du kan också använda ett befintligt konto. |

1. Klicka på **Skapa** för att etablera och distribuera den nya funktionsappen.