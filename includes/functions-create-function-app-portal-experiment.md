1. Välj knappen **Nytt** längst upp till vänster i Azure Portal, välj sedan **Compute** > **Funktionsapp**. 

    ![Skapa en funktionsapp i Azure Portal](./media/functions-create-function-app-portal/function-app-create-flow.png)

2. Använd funktionsappinställningarna enligt tabellen under bilden.

    ![Definiera nya funktionsappinställningar](./media/functions-create-function-app-portal/function-app-create-flow2.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z`, `0-9` och `-`.  | 
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. | 
    | **[Resursgrupp](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Namnet på den nya resursgrupp där du vill skapa funktionsappen. | 
    | **OS** | Windows | Serverlösa värdtjänster är för närvarande endast tillgängliga om du kör Windows. För värdtjänster i Linux, se [Skapa din första funktion som körs på Linux med hjälp av Azure CLI](../articles/azure-functions/functions-create-first-azure-function-azure-cli-linux.md). |
    | **[Värdplan](../articles/azure-functions/functions-scale.md)** |   Förbrukningsplan | Värdplan som definierar hur resurser allokeras till din funktionsapp. I **standardförbrukningsplanen** läggs resurser till dynamiskt när de behövs i funktionerna. För den här typen av [serverlösa](https://azure.microsoft.com/overview/serverless-computing/) värdtjänster betalar du bara för den tid som dina funktioner körs.   |
    | **Plats** | Västra Europa | Välj en [plats](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som kommer att användas i dina funktioner. |
    | **[Lagringskonto](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)** |  Globalt unikt namn |  Namnet på det nya lagringskonto som ska användas av funktionsappen. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också använda ett befintligt konto. |

3. Välj **Skapa** för att etablera och distribuera funktionsappen. 

4. Välj meddelandeikonen i det övre högra hörnet av portalen och titta efter meddelandet **Distribueringen lyckades**. 

    ![Definiera nya funktionsappinställningar](./media/functions-create-function-app-portal/function-app-create-notification.png)

4. Välj **Gå till resurs** att visa den nya funktionsappen.
