---
title: Att lägga till Mobile Services med hjälp av Connected Services i Visual Studio | Microsoft Docs
description: Lägga till Mobile Services med hjälp av dialogrutan Visual Studio lägga till Connected Services
services: visual-studio-online
documentationcenter: na
author: mlhoop
manager: douge
editor: ''
ms.assetid: 75c3cb93-88e1-476d-a416-f34caa3608e3
ms.service: visual-studio-online
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: mobile
ms.custom: vs-azure
ms.date: 12/16/2015
ms.author: mlearned
ms.openlocfilehash: a1b2524b059601e1f6f999af7a9d9e063f7c0420
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233454"
---
# <a name="adding-mobile-services-by-using-visual-studio-connected-services"></a>Att lägga till Mobile Services med hjälp av Visual Studio Connected Services
Med Visual Studio 2015 kan du ansluta till Azure Mobile Services med den **Lägg till Connected Service** dialogrutan. Du kan ansluta från alla klientappar för C#, JavaScript-app eller plattformsoberoende Cordova-app. När du ansluter kan du skapa och komma åt data, skapa anpassade API: er och schemalagda jobb eller lägga till stöd för push-meddelanden.  Anslutna tjänster åtgärden lägger till alla lämpliga referenser och anslutningen kod. Du kan också dra nytta av inbyggt stöd för autentisering med en mängd olika populära identitet system, t.ex Azure AD, Facebook, Twitter och Microsoft-Accounts.

## <a name="supported-project-types"></a>Stöds projekttyper
> [!NOTE]
> I Visual Studio 2015 stöds att lägga till Azure Mobile Services till en Windows Universal (Windows 10) projekt med hjälp av dialogrutan Lägg till Connected Services inte. Du kan lägga till Azure Mobile Services genom att installera de paket med NuGet Package Manager för ditt projekt.
> 
> 

Du kan använda dialogrutan Connected Services för att ansluta till Azure Mobile Services i följande projekttyper.

* .NET Windows 8.1 Store och Phone universell App-projekt
* JavaScript Windows 8.1 Store och Phone universell App-projekt
* Projekt som skapats med hjälp av Visual Studio Tools för Apache Cordova

## <a name="connect-to-azure-mobile-services-using-the-add-connected-services-dialog"></a>Ansluta till Azure Mobile Services genom att använda dialogen Lägg till Connected Services
1. Kontrollera att du har ett Azure-konto. Om du inte har ett Azure-konto, kan du registrera dig för en [kostnadsfri utvärdering](http://go.microsoft.com/fwlink/?LinkId=518146).
2. Öppna den **Lägg till Connected Services** dialogrutan.
   
   * För .NET-appar, öppna ditt projekt i Visual Studio, öppna snabbmenyn för den **referenser** noden i Solution Explorer och välj sedan **Lägg till Connected Service**
     
        ![Ansluta till Azure-Mobiltjänst](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)
   * Öppna projektet i Visual Studio för Apache Cordova-app-projekt, öppna snabbmenyn för distributionsprojektets nod i Solution Explorer och välj sedan **Lägg till Connected Service**.
3. I den **Lägg till Connected Service** dialogrutan väljer du **Azure Mobile Services**, och välj sedan den **konfigurera** knappen. Du kan uppmanas att logga in på Azure om du inte redan gjort det.
   
    ![Att lägga till en Azure-Mobiltjänst](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)
4. I den **Azure Mobile Services** dialogrutan väljer du en befintlig mobil tjänst om du har en. Följ anvisningarna nedan för att göra det om du vill skapa en ny Azure-mobiltjänst. Annars går du till nästa steg.
   
    Skapa en ny Mobiltjänst konto:
   
   1. Välj den ** skapa tjänst ** länken längst ned i dialogrutan.
       ![Lägg till ny anslutna Mobiltjänst](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)
   2. På den **skapa Mobiltjänst** dialogrutan kan du välja en mobil tjänst för JavaScript-serverdelen eller en .NET-serverdel mobil tjänst från den **Runtime** listrutan. 
      
       ![Skapa en Mobiltjänst](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)
      
       En JavaScript-servertjänst är enkel och kraftfull. Om du skapar en mobil tjänst för JavaScript-serverdelen, JavaScript-kod från serversidan lagras i molnet, men du kan redigera server-skript med hjälp av Server Explorer eller Azure-hanteringsportalen. 
      
       En mobil tjänst för .NET-serverdelen ger dig all kraft och flexibilitet i Web API och Entity Framework. Om du skapar en mobil tjänst för .NET-serverdelen, ett projekt skapas åt dig och läggas till i din lösning. 
   3. Välj den **Region** där du vill ha den mobila tjänsten och sedan ange ett användarnamn och lösenord för servern.
   4. När du har angett all nödvändig information, väljer den **skapa** för att skapa mobiltjänsten.
   5. Den nya mobiltjänsten ska visas i listan på den **Azure Mobile Services** dialogrutan. Välj den nya mobiltjänsten i listan och välj sedan den **Lägg till** för att lägga till tjänsten i projektet.
5. Granska den komma igång sida som visas och ta reda på hur ditt projekt har ändrats. En komma igång-sida visas i webbläsaren när du lägger till en ansluten tjänst. Du kan granska nästa föreslagna steg och kodexempel eller växla till sidan vad hände för att se vilka referenser har lagts till i ditt projekt och hur din kod och konfigurationsfiler som har ändrats.
6. Med kodexempel som vägledning, börja skriva kod för att få åtkomst till din Mobiltjänst!

## <a name="how-your-project-is-modified"></a>Hur ditt projekt ändras
Hur Visual Studio ändrar projektet beror på vilken projekt. För C# klient apps finns i [vad har hänt – C# projekt](http://go.microsoft.com/fwlink/p/?LinkId=513119). JavaScript-klientprogram, se [vad hände – JavaScript-projekt](http://go.microsoft.com/fwlink/p/?LinkId=513120). Cordova-appar Se [vad hände – Cordova-projekt](http://go.microsoft.com/fwlink/p/?LinkId=513116).

## <a name="next-steps"></a>Nästa steg
Ställ frågor och få hjälp: 

* [MSDN-Forum: Azure mobila tjänster](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)
* [Azure mobila tjänster på Microsoft Azure-teamets blogg](https://azure.microsoft.com/blog/topics/mobile/)
* [Azure mobila tjänster på azure.microsoft.com](https://azure.microsoft.com/services/mobile-services/)
* [Dokumentation om Azure mobila tjänster på azure.microsoft.com](https://azure.microsoft.com/documentation/services/mobile-services/)

