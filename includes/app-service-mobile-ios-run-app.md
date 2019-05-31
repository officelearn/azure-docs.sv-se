---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240277"
---
1. Öppna det hämtade klientprojektet med Xcode.

2. Gå till den [Azure-portalen](https://portal.azure.com/) och navigera till den mobila appen som du skapade. På den `Overview` bladet letar du reda på URL: en som är den offentliga slutpunkten för din mobilapp. Exempel – sitename för min app name ”test123” kommer att https://test123.azurewebsites.net.

3. Öppna filen för Swift projekt `ToDoTableViewController.swift` i den här mappen - ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift. Programnamnet är `ZUMOAPPNAME`.

4. I `viewDidLoad()` metod, Ersätt `ZUMOAPPURL` parameter med offentlig slutpunkt ovan.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    blir
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Öppna filen för Objective-C-projekt, `QSTodoService.m` i den här mappen - ZUMOAPPNAME/ZUMOAPPNAME. Programnamnet är `ZUMOAPPNAME`.

6. I `init` metod, Ersätt `ZUMOAPPURL` parameter med offentlig slutpunkt ovan.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    blir
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Klicka på **Kör**-knappen för att skapa projektet och starta appen i iOS-simulatorn.

8. I appen klickar du på plusikonen ( **+** ), anger en beskrivande text, till exempel *Slutför självstudien*, och klickar sedan på knappen Spara. Detta skickar en POST-begäran till den Azure-serverdel som du distribuerade tidigare. Serverdelen infogar data från begäran i TodoItem-SQL-tabellen och returnerar information om det nyligen lagrade objektet tillbaka till mobilappen. Mobilappen visar dessa data i listan.

   ![Snabbstart för appar som körs i iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
