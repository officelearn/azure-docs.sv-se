1. Klicka på den **ny** knapp hittades i det övre vänstra hörnet i Azure-portalen sedan väljer **Compute** > **Funktionsapp**. 

    ![Skapa en funktionsapp i Azure-portalen](./media/functions-create-function-app-portal/function-app-create-flow.png)

2. Använd funktionen app-inställningar som anges i tabellen nedan avbildningen.

    ![Definiera nya funktionen app-inställningar](./media/functions-create-function-app-portal/function-app-create-flow2.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z`, `0-9`, och `-`.  | 
    | **Prenumeration** | Din prenumeration | Den prenumeration som den här nya funktionen appen kommer att skapas. | 
    | **[Resursgrupp](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Namnet på den nya resursgrupp där du vill skapa funktionsappen. | 
    | **OS** | Windows | Serverlösa värd är för närvarande endast tillgängligt när du kör i Windows. Linux-värd, finns i [skapa din första funktion som körs på Linux med hjälp av Azure CLI](../articles/azure-functions/functions-create-first-azure-function-azure-cli-linux.md). |
    | **[Värdplan](../articles/azure-functions/functions-scale.md)** |   Förbrukningsplan | Värdplan som definierar hur resurser allokeras till din funktionsapp. I **standardförbrukningsplanen** läggs resurser till dynamiskt när de behövs i funktionerna. I det här [serverlösa](https://azure.microsoft.com/overview/serverless-computing/) värd, du betalar bara för den tid dina funktioner köras.   |
    | **Plats** | Västra Europa | Välj en plats nära dig eller nära andra tjänster som kommer att användas i dina funktioner. |
    | **[Lagringskonto](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)** |  Globalt unikt namn |  Namnet på det nya lagringskonto som ska användas av funktionsappen. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också använda ett befintligt konto. |

1. Klicka på **Skapa** för att etablera och distribuera den nya funktionsappen.
