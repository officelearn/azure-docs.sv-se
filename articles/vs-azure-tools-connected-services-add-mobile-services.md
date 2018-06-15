---
title: Lägga till Mobile Services med anslutna tjänster i Visual Studio | Microsoft Docs
description: Lägga till Mobile Services med hjälp av dialogrutan Visual Studio Lägg till anslutna tjänster
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
ms.date: 12/16/2015
ms.author: mlearned
ms.openlocfilehash: d185fdafebad56f8970e390b2a0672c3fb84df8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23884218"
---
# <a name="adding-mobile-services-by-using-visual-studio-connected-services"></a>Lägga till Mobile Services med hjälp av Visual Studio anslutna Services
Med Visual Studio 2015, som du kan ansluta till Azure Mobile Services med hjälp av den **Lägg till ansluten tjänst** dialogrutan. Du kan ansluta från alla klientappar för C#, JavaScript-app eller plattformsoberoende Cordova-app. När du ansluter kan du skapa och komma åt data, skapa anpassade API: er och schemalagda jobb eller lägga till stöd för push-meddelanden.  Anslutna tjänster åtgärden lägger till alla lämpliga referenser och anslutningen kod. Du kan också dra nytta av inbyggt stöd för autentisering med en mängd olika populära identitet system, t.ex Azure AD, Facebook, Twitter och Microsoft Accounts.

## <a name="supported-project-types"></a>Stöds projekttyper
> [!NOTE]
> I Visual Studio 2015 stöds att lägga till Azure Mobile Services för universella Windows-(Windows 10)-projekt med hjälp av dialogrutan Lägg till anslutna tjänster inte. Du kan lägga till Azure Mobile Services genom att installera de paket med hjälp av NuGet Package Manager för projektet.
> 
> 

Du kan använda dialogrutan Connected Services för att ansluta till Azure Mobile Services i följande projekttyper.

* .NET Windows 8.1 Store och Phone universell App-projekt
* JavaScript Windows 8.1 Store och Phone universell App-projekt
* Projekt som skapats med Visual Studio Tools för Apache Cordova

## <a name="connect-to-azure-mobile-services-using-the-add-connected-services-dialog"></a>Ansluta till Azure Mobile Services genom att använda dialogrutan Lägg till anslutna tjänster
1. Kontrollera att du har ett Azure-konto. Om du inte har ett Azure-konto, kan du registrera dig för en [kostnadsfri utvärdering](http://go.microsoft.com/fwlink/?LinkId=518146).
2. Öppna den **Lägg till anslutna tjänster** dialogrutan.
   
   * Öppna projektet i Visual Studio .NET appar, öppna snabbmenyn för den **referenser** nod i Solution Explorer och välj sedan **Lägg till ansluten tjänst**
     
        ![Ansluta till Azure-mobiltjänsten](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)
   * Öppna projektet i Visual Studio för Apache Cordova-app-projekt, öppna snabbmenyn för distributionsprojektets nod i Solution Explorer och välj sedan **Lägg till ansluten tjänst**.
3. I den **Lägg till ansluten tjänst** dialogrutan Välj **Azure Mobile Services**, och välj sedan den **konfigurera** knappen. Du kan uppmanas att logga in på Azure om du inte redan gjort det.
   
    ![Lägga till en Azure-mobiltjänsten](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)
4. I den **Azure Mobile Services** dialogrutan Välj en befintlig mobila tjänst om du har en. Följ anvisningarna nedan för att göra det om du behöver skapa en ny Azure-mobiltjänsten. Annars fortsätter du till nästa steg.
   
    Skapa en ny Mobiltjänst konto:
   
   1. Välj den ** skapa tjänst ** länken längst ned i dialogrutan.
       ![Lägg till ny anslutna Mobiltjänst](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)
   2. På den **skapa Mobiltjänst** dialogrutan, du kan välja en JavaScript-serverdel Mobiltjänst eller en .NET-serverdel Mobiltjänst från den **Runtime** listrutan. 
      
       ![Skapa en Mobiltjänst](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)
      
       En JavaScript-serverdelstjänst är enkelt och kraftfullt. Om du skapar en Mobiltjänst för JavaScript-serverdel serversidan JavaScript-koden lagras i molnet, men du kan redigera server-skript med hjälp av Server Explorer eller Azure-hanteringsportalen. 
      
       En .NET-serverdel Mobiltjänst ger full effekt och flexibilitet för webb-API och Entity Framework. Om du skapar en Mobiltjänst för .NET-serverdel skapas automatiskt ett projekt och läggas till i din lösning. 
   3. Välj den **Region** där du vill använda den mobila tjänsten och sedan ange ett användarnamn och lösenord för servern.
   4. När du har angett informationen som krävs, Välj den **skapa** för att skapa mobiltjänsten.
   5. Den nya mobiltjänsten ska visas i listan på den **Azure Mobile Services** dialogrutan. Välj den nya mobiltjänsten i listan och välj sedan den **Lägg till** för att lägga till tjänsten i projektet.
5. Granska komma igång sidan som visas och ta reda på hur projektet har ändrats. En komma igång-sida visas i webbläsaren när du lägger till en ansluten tjänst. Du kan granska den föreslagna nästa steg och kodexempel eller växla till sidan vad hände om du vill se vilka referenser har lagts till i ditt projekt och hur koden och configuration-filer som har ändrats.
6. Med hjälp av kodexempel som vägledning börja skriva kod för att få åtkomst till din Mobiltjänst!

## <a name="how-your-project-is-modified"></a>Hur projektet har ändrats
Hur Visual Studio ändrar projektet beror på projekttypen. C# klientprogram, se [vad hände – C#-projekt](http://go.microsoft.com/fwlink/p/?LinkId=513119). JavaScript-klientprogram, se [vad hände – JavaScript-projekt](http://go.microsoft.com/fwlink/p/?LinkId=513120). Cordova-appar finns [vad hände – Cordova-projekt](http://go.microsoft.com/fwlink/p/?LinkId=513116).

## <a name="next-steps"></a>Nästa steg
Ställ frågor och få hjälp: 

* [MSDN-Forum: Azure Mobile Services](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)
* [Azure Mobile Services i Microsoft Azure-teamets blogg](https://azure.microsoft.com/blog/topics/mobile/)
* [Azure Mobile Services på azure.microsoft.com](https://azure.microsoft.com/services/mobile-services/)
* [Azure Mobile Services-dokumentationen på azure.microsoft.com](https://azure.microsoft.com/documentation/services/mobile-services/)

