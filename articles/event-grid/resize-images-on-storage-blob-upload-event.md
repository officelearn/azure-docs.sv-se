---
title: "Använd Azure händelse rutnätet för att automatisera storleksändring upp bilder | Microsoft Docs"
description: "Azure händelse rutnätet kan utlösa på blob-överföringar i Azure Storage. Du kan använda den för att skicka filer har överförts till Azure Storage till andra tjänster, till exempel Azure Functions för storleksändring och andra förbättringar."
services: event-grid
author: ggailey777
manager: cfowler
editor: 
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 709d23ab590c06d5da9b03e2767bc0be5905355b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="automate-resizing-uploaded-images-using-event-grid"></a>Automatisera storleksändring överförda bilder med hjälp av händelse rutnätet

[Azure händelse rutnätet](overview.md) är en eventing tjänst för molnet. Händelsen rutnätet kan du skapa prenumerationer på händelser som skapats av Azure-tjänster eller resurser från tredje part.  

Den här kursen ingår två i en serie kurser för lagring. Utökar den [tidigare lagring kursen] [ previous-tutorial] att lägga till serverlösa miniatyr generera automatiskt med hjälp av Azure händelse rutnätet och Azure Functions. Gör att händelsen rutnätet [Azure Functions](..\azure-functions\functions-overview.md) att svara på [Azure Blob storage](..\storage\blobs\storage-blobs-introduction.md) händelser och generera miniatyrbilder av överförda bilder. En händelseprenumeration skapas mot Blob storage skapa en händelse. När en blob läggs till en viss behållare för Blob storage kallas en slutpunkt för funktionen. Data som skickas till funktionen bindningen från händelsen rutnät används för att få åtkomst till blob och generera på miniatyrbilden. 

Du kan använda Azure CLI och Azure portal för att lägga till funktioner för storleksändring i en befintlig avbildning överför app.

![Publicerade webbprogram i Edge-webbläsare](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en allmän Azure Storage-konto
> * Distribuera serverlösa kod med hjälp av Azure-funktioner
> * Skapa ett Blob storage händelseprenumerationen i händelsen rutnätet

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

+ Du måste ha slutfört föregående Blob storage kursen: [överför avbildningen data i molnet med Azure Storage][previous-tutorial]. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt i det här avsnittet kräver att du använder Azure CLI version 2.0.14 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Om du inte använder Cloud Shell måste du först logga in med `az login`.

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

Azure Functions kräver ett allmänt lagringskonto. Skapa ett separat Allmänt lagringskonto i resursgruppen med hjälp av den [az storage-konto skapar](/cli/azure/storage/account#create) kommando.

Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. 

Ersätta en egen globalt unikt namn för den allmänna storage-konto där du ser i följande kommando i `<general_storage_account>` platshållare. 

```azurecli-interactive
az storage account create --name <general_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind storage
```

## <a name="create-a-function-app"></a>Skapa en funktionsapp  

Du måste ha en funktionsapp som värd för körningen av din funktion. Funktionsappen är en miljö för serverfri körning av funktionskoden. Skapa en funktionsapp med kommandot [az functionapp create](/cli/azure/functionapp#create). 

I följande kommando i stället använda dina egna unika funktionen appnamn där du ser den `<function_app>` platshållare. `<function_app>` används som DNS-standarddomän för funktionsappen. Därför måste namnet vara unikt bland alla appar i Azure. I det här fallet `<general_storage_account>` är namnet på den allmänna storage-konto som du skapade.  

```azurecli-interactive
az functionapp create --name <function_app> --storage-account  <general_storage_account>  \
--resource-group myResourceGroup --consumption-plan-location westcentralus
```

Nu måste du konfigurera funktionen för att ansluta till blob-lagring. 

## <a name="configure-the-function-app"></a>Konfigurera funktionen appen

Funktionen måste anslutningssträngen att ansluta till blob storage-konto. I det här fallet `<blob_storage_account>` är namnet på Blob storage-konto som du skapade i föregående kursen. Hämta anslutningssträngen med den [az lagring konto visa anslutningssträng](/cli/azure/storage/account#show-connection-string) kommando. Miniatyrbilden behållarens namn måste också anges till `thumbs`. Lägg till dessa programinställningar i funktionsapp med den [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) kommando.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <blob_storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings myblobstorage_STORAGE=$storageConnectionString \
myContainerName=thumbs
```

Du kan nu distribuera ett projekt för funktionen kod till den här funktionen appen.

## <a name="deploy-the-function-code"></a>Distribuera Funktionskoden 

C#-funktion som utför storleksändring är tillgänglig i det här [exempel GitHub-lagringsplatsen](https://github.com/Azure-Samples/function-image-upload-resize). Distribuera projektet funktioner koden i appen funktionen med hjälp av den [az functionapp källa distributionskonfiguration](/cli/azure/functionapp/deployment/source#config) kommando. 

I följande kommando, `<function_app>` är samma funktionsapp som du skapade i föregående skript.

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

Funktionen Ändra storlek på bilden utlöses av en händelseprenumeration till en Blob som skapats händelse. Data som skickas till utlösaren innehåller URL-Adressen till blob, som i sin tur har överförts till indatabindning att hämta överförda avbildningen från Blob storage. Funktionen genererar en miniatyrbild och skriver den resulterande dataströmmen till en separat behållare i Blob storage. Mer information om den här funktionen finns i [Readme-filen i exemplet databasen](https://github.com/Azure-Samples/function-image-upload-resize/blob/master/README.md).
 
Funktionen Projektkod distribueras direkt från databasen gemensamma exemplet. Mer information om distributionsalternativ för Azure Functions finns [kontinuerlig distribution för Azure Functions](../azure-functions/functions-continuous-deployment.md).

## <a name="create-your-event-subscription"></a>Skapa händelseprenumerationen

En händelseprenumerationen anger vilken provider-genererade händelser som du vill skickas till en viss slutpunkt. I det här fallet exponeras slutpunkten av din funktion. Använd följande steg för att skapa en händelseprenumeration från din funktion i Azure-portalen: 

1. I den [Azure-portalen](https://portal.azure.com), klicka på pilen längst ned till vänster om du vill expandera alla tjänster skriver `functions` i den **Filter** och välj sedan **funktionen appar**. 

    ![Bläddra till funktionen appar i Azure-portalen](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. Expandera funktionen appen, Välj den **imageresizerfunc** fungera, och välj sedan **Lägg till händelse rutnätet prenumeration**.

    ![Bläddra till funktionen appar i Azure-portalen](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. Använda prenumerationsinställningar händelsen som anges i tabellen.

    ![Skapa händelseprenumerationen från funktionen i Azure-portalen](./media/resize-images-on-storage-blob-upload-event/event-subscription-create-flow.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Namn** | imageresizersub | Namn som identifierar händelseprenumerationen. | 
    | **Ämnestyp** |  Lagringskonton | Välj Händelseprovidern för Storage-konto. | 
    | **Prenumeration** | Din prenumeration | Som standard måste din aktuella prenumeration väljas.   |
    | **Resursgrupp** | myResourceGroup | Välj **använda befintliga** och välj den resursgrupp som du har använt i det här avsnittet.  |
    | **Instans** |  `<blob_storage_account>` |  Välj Blob storage-konto som du skapade. |
    | **Händelsetyper** | BLOB som skapats | Avmarkera alla typer utom **Blob som skapats**. Händelsetyper av `Microsoft.Storage.BlobCreated` skickas till funktionen.| 
    | **Prenumeranten slutpunkt** | namn automatiskt | Använd slutpunkts-URL som genereras. | 
    | **Prefixet filter** | / blobServices/standard/behållare/bilder/blobbar / | Filtrerar storage-händelser till endast de på det **bilder** behållare.| 

4. Klicka på **skapa** att lägga till händelseprenumerationen. Detta skapar en händelseprenumeration som utlöser **imageresizerfunc** när en blob läggs till i **bilder** behållare. Bilderna läggs till i **tummen** behållare.

Nu när backend-tjänster har konfigurerats, testa funktionen avbildningen storlek i exemplet webbapp. 

## <a name="test-the-sample-app"></a>Testa exempelappen

Om du vill testa storleksändring i webbapp, bläddra till URL-Adressen till ditt publicerade program. Standardwebbadressen för webbappen är `https://<web_app>.azurewebsites.net`.

Klicka på den **överföra foton** region att välja och överföra en fil. Du kan också dra ett foto till den här regionen. 

Observera att när överförda bilden försvinner, visas en kopia av överförda bilden i den **skapas miniatyrer** karusell. Den här avbildningen har storleken av funktionen, läggas till i behållaren tummen och hämtas med webbklienten. 

![Publicerade webbprogram i Edge-webbläsare](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en allmän Azure Storage-konto
> * Distribuera serverlösa kod med hjälp av Azure-funktioner
> * Skapa ett Blob storage händelseprenumerationen i händelsen rutnätet

Gå vidare till avsnitt tre lagring självstudiekursen seriens att lära dig säker åtkomst till lagringskontot.

> [!div class="nextstepaction"]
> [Säker åtkomst till ett program data i molnet](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)


+ Mer information om händelsen rutnätet finns [en introduktion till Azure händelse rutnätet](overview.md). 
+ Om du vill prova en annan självstudier som innehåller Azure Functions finns [skapa en funktion som kan integreras med Azure Logikappar](..\azure-functions\functions-twitter-email.md). 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md