---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240277"
---
1. Öppna det nedladdade klientprojektet med Xcode.

2. Gå till [Azure-portalen](https://portal.azure.com/) och navigera till mobilappen som du skapade. Leta `Overview` efter webbadressen som är den offentliga slutpunkten för mobilappen på bladet. Exempel - webbplatsnamnet för mitt appnamn "test123" kommer att vara https://test123.azurewebsites.net.

3. För Swift-projektet öppnar `ToDoTableViewController.swift` du filen i den här mappen - ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift. Programnamnet är `ZUMOAPPNAME`.

4. Ersätt `viewDidLoad()` `ZUMOAPPURL` parametern med den offentliga slutpunkten ovan.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    Blir
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. För Objective-C-projekt öppnar `QSTodoService.m` du filen i den här mappen - ZUMOAPPNAME/ZUMOAPPNAME. Programnamnet är `ZUMOAPPNAME`.

6. Ersätt `init` `ZUMOAPPURL` parametern med den offentliga slutpunkten ovan.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    Blir
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. Klicka på **Kör**-knappen för att skapa projektet och starta appen i iOS-simulatorn.

8. Klicka på plus ()**+** i appen, skriv meningsfull text, till exempel *Slutför självstudien*och klicka sedan på spara-knappen. Detta skickar en POST-begäran till den Azure-serverdel som du distribuerade tidigare. Serverdelen infogar data från begäran i TodoItem-SQL-tabellen och returnerar information om det nyligen lagrade objektet tillbaka till mobilappen. Mobilappen visar dessa data i listan.

   ![Snabbstart för appar som körs i iOS](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
