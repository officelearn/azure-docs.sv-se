---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 734fce2c01614d5dca73f171fb59f25f39d13705
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461504"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Konfigurera en miljövariabel för autentisering

Program måste autentisera åtkomsten till de kognitiva tjänsterna som de använder. För att autentisera, rekommenderar vi att skapa en miljövariabel för att lagra en nyckel i Azure-resurser. 

När du har skapat din nyckel kan skriva till en ny miljövariabel på den lokala datorn där programmet körs. Konfigurera miljövariabeln genom att öppna ett konsolfönster och följa anvisningarna för ditt operativsystem. Ersätt `your-key` med din åtkomstnyckel för Avvikelseidentifiering detektor:

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    När du har lagt till miljövariabeln kan du behöva starta om alla program som körs och som behöver läsa in miljövariabeln, däribland konsolfönstret. Till exempel om du använder Visual Studio som redigeringsprogram, starta om Visual Studio innan du kör exemplet.

* Linux
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.
    
* macOS
    
    Redigera din .bash_profile och lägg till miljövariabeln:
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    När du har lagt till miljövariabeln så kör `source .bash_profile` från konsolfönstret så att ändringarna träder i kraft.
