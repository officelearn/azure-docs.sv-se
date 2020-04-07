---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673319"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Hämta anslutningssträngen för Azure Storage

Tidigare har du skapat ett Azure Storage-konto som ska användas av funktionsappen. Anslutningssträngen för det här kontot lagras säkert i appinställningar i Azure. Genom att hämta inställningen till filen *local.settings.json* kan du använda den anslutningsskrivningen till en lagringskö i samma konto när du kör funktionen lokalt. 

1. Från projektets rot kör du följande `<app_name>` kommando och ersätter med namnet på funktionsappen från föregående snabbstart. Det här kommandot skriver över alla befintliga värden i filen.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Öppna *local.settings.json* och leta `AzureWebJobsStorage`reda på värdet med namnet , som är anslutningssträngen för lagringskonto. Du använder `AzureWebJobsStorage` namnet och anslutningssträngen i andra avsnitt i den här artikeln.

> [!IMPORTANT]
> Eftersom *local.settings.json* innehåller hemligheter som hämtats från Azure, utesluter du alltid den här filen från källkontrollen. *Gitignore-filen* som skapats med ett lokalt funktionsprojekt utesluter filen som standard.