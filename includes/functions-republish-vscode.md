---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/20/2020
ms.author: glenga
ms.openlocfilehash: 574756aa9d9bac4aa42febf6a4fca4c62014db3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84732487"
---
1. I Visual Studio Code väljer du F1 för att öppna kommando paletten. I paletten kommando söker du efter och väljer **Azure Functions: distribuera till Function-appen**.

1. Om du inte är inloggad uppmanas du att **Logga in på Azure**. När du har loggat in från webbläsaren går du tillbaka till Visual Studio Code. Om du har flera prenumerationer **väljer du en prenumeration** som innehåller din Function-app.

1. Välj din befintliga Function-app i Azure. När du varnas om att skriva över alla filer i Function-appen väljer du **distribuera** för att bekräfta varningen och fortsätta.

Projektet har återskapats, paketerats om och laddats upp till Azure. Det befintliga projektet ersätts av det nya paketet och Function-appen startas om.