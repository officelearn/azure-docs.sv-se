---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 5e3c4622131528fc2c40a1510aeea3092018d182
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77474163"
---
### <a name="install-via-composer"></a>Installera via Composer
1. Skapa en fil med namnet **composer.json** i projektets rot och lägg till följande kod i den:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Ladda ner **[composer.phar][composer-phar]** i projektroten.
3. Öppna en kommandotolk och kör följande kommando i projektroten
   
    ```
    php composer.phar install
    ```

Du kan också gå till [Azure Storage PHP-klientbiblioteket][php-sdk-github] på GitHub för att klona källkoden.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar
