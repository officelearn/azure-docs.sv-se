1. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.

1. Klicka på **Compute** > **Funktionsapp**. Använd sedan funktionsappinställningarna enligt tabellen.

    ![Skapa en funktionsapp i Azure Portal](./media/functions-create-function-app-portal/function-app-create-flow.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z`, `0-9`, och `-`.  | 
    | **Prenumeration** | Din prenumeration | Den prenumeration som den här nya funktionen appen kommer att skapas. | 
    | **[Resursgrupp](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Namnet på den nya resursgrupp där du vill skapa funktionsappen. | 
    | **[Värdplan](../articles/azure-functions/functions-scale.md)** |   Förbrukningsplan | Värdplan som definierar hur resurser allokeras till din funktionsapp. I **standardförbrukningsplanen** läggs resurser till dynamiskt när de behövs i funktionerna. Du betalar endast för den tid som dina funktioner körs.   |
    | **Plats** | Västra Europa | Välj en plats nära dig eller nära andra tjänster som kommer att användas i dina funktioner. |
    | **[Lagringskonto](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)** |  Globalt unikt namn |  Namnet på det nya lagringskonto som ska användas av funktionsappen. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också använda ett befintligt konto. |

1. Klicka på **Skapa** för att etablera och distribuera den nya funktionsappen.
