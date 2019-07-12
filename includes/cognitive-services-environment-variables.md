---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 6d6451d50a00569eb1da8f5b0a0dc10d3c6b1115
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841426"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Konfigurera en miljövariabel för autentisering

Program måste autentisera åtkomsten till de kognitiva tjänsterna som de använder. För att autentisera, rekommenderar vi att skapa en miljövariabel för att lagra nycklarna för dina Azure-resurser. 

När du har skapat din nyckel kan skriva till en ny miljövariabel på den lokala datorn där programmet körs. Konfigurera miljövariabeln genom att öppna ett konsolfönster och följa anvisningarna för ditt operativsystem. Ersätt `your-key` med någon av nycklarna för din resurs.

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
