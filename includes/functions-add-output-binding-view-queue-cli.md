---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 44823ce888e97b308f29403612f598c0eb585ae5
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673377"
---
Du kan visa kön i [Azure-portalen](../articles/storage/queues/storage-quickstart-queues-portal.md) eller i [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Du kan också visa kön i Azure CLI, enligt beskrivningen i följande steg:

1. Öppna funktionsprojektets *local.setting.json-fil* och kopiera anslutningssträngvärdet. I ett terminal- eller kommandofönster kör du följande `AZURE_STORAGE_CONNECTION_STRING`kommando för att skapa en `<MY_CONNECTION_STRING>`miljövariabel med namnet , klistra in din specifika anslutningssträng i stället för . (Den här miljövariabeln innebär att du inte behöver ange `--connection-string` anslutningssträngen till varje efterföljande kommando med argumentet.)

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Valfritt) Använd [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) kommandot för att visa lagringsköerna i ditt konto. Utdata från det här kommandot `outqueue`ska innehålla en kö med namnet , som skapades när funktionen skrev sitt första meddelande till den kön.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Använd [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) kommandot för att läsa meddelandet från den här kön, vilket bör vara det förnamn som du använde när du testade funktionen tidigare. Kommandot läser och tar bort det första meddelandet från kön. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Det här skriptet använder certutil för att avkoda den base64-kodade meddelandesamlingen från en lokal temp-fil. Om det inte finns några `> NUL` utdata kan du prova att ta bort från skriptet för att sluta undertrycka certutil-utdata om det finns ett fel. 
    
    ---
    
    Eftersom meddelandetexten lagras [base64-kodad](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)måste meddelandet avkodas innan det visas. När du `az storage message get`har kör tas meddelandet bort från kön. Om det bara fanns `outqueue`ett meddelande i hämtas inte ett meddelande när du kör det här kommandot en andra gång och får i stället ett felmeddelande.