---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 3cd5f59a3dcf3afcd26d16874e7dc77ca0a7e844
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841678"
---
### <a name="install-via-composer"></a>Installera via Composer
1. Skapa en fil med namnet **Compose. JSON** i roten i projektet och Lägg till följande kod i den:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. Hämta **[Composer. Phar][composer-phar]** i projekt roten.
3. Öppna en kommando tolk och kör följande kommando i projekt roten
   
    ```
    php composer.phar install
    ```

Du kan också gå till [Azure Storage php-klientprogrammet][php-sdk-github] på GitHub för att klona käll koden.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar
