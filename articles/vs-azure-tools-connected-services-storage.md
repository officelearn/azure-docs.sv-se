---
title: Lägg till Azure Storage med hjälp av Connected Services i Visual Studio | Microsoft Docs
description: Lägga till Azure Storage i din app med hjälp av dialogrutan Visual Studio lägga till Connected Services
services: visual-studio-online
author: ghogen
manager: douge
assetId: 521ec044-ad4b-4828-8864-01decde2e758
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/26/2017
ms.author: ghogen
ms.openlocfilehash: 031555659cc501365e762ba973c70b68129dbb4e
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969316"
---
# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>Att lägga till Azure storage med hjälp av Visual Studio Connected Services
Med Visual Studio, du kan ansluta något av följande till Azure Storage med hjälp av den **Lägg till Connected Services** dialogrutan:

- C#-molntjänst
- Mobil tjänst för .NET-serverdelen
- ASP.NET-webbplats eller tjänst
- ASP.NET Core-tjänst
- Azure WebJob-tjänst 

Funktionen anslutna tjänsten lägger till alla nödvändiga referenser och kod för anslutning till ditt projekt och ändrar konfigurationsfilerna på rätt sätt. 

När du har slutfört den **Lägg till Connected Services** dialogrutan visar dokumentationen med information om de steg som krävs för att börja arbeta med blob-lagring, köer och tabeller automatiskt.

## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>Anslut till Azure Storage genom att använda dialogen Connected Services
1. Öppna projektet i Visual Studio

1. I **Solution Explorer**, högerklicka på den **Connected Services** nod, och från snabbmenyn och välj **Lägg till Connected Service**.
   
    ![Lägga till Azure ansluten tjänst](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. I den **Connected Services** väljer **Molnlagring med Azure Storage**.
   
    ![Lägg till Azure Storage](./media/vs-azure-tools-connected-services-storage/add-azure-storage.png)

1. I den **Azure Storage** dialogrutan, Välj ett befintligt lagringskonto och väljer **Lägg till**.
   
    Om du vill skapa ett lagringskonto kan gå till nästa steg. Annars går du till steg 6.
    
    ![Lägg till befintligt lagringskonto i projekt](./media/vs-azure-tools-connected-services-storage/select-azure-storage-account.png)

1. Skapa ett lagringskonto: 
   
   1. Välj **skapa ett nytt Lagringskonto** längst ned i dialogrutan.

   1. Fyll i **skapa Lagringskonto** dialogrutan och välj **skapa**.
      
       ![Nya Azure storage-konto](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)
      
   1. När den **Azure Storage** dialogrutan visas, visas det nya lagringskontot i listan. Välj det nya lagringskontot i listan och välj **Lägg till**.

1. Lagringen anslutna tjänsten visas under den **referenser för** nod i ditt projekt.
   
## <a name="how-your-project-is-modified"></a>Hur ditt projekt ändras
När du är klar dialogrutan Visual Studio lägger du till referenser och ändrar vissa konfigurationsfiler. Specifika ändringarna beror på vilken projekt: 

- ASP.NET-projekt – [vad hände – ASP.NET-projekt](http://go.microsoft.com/fwlink/p/?LinkId=513126)
- ASP.NET Core-projekt – [vad hände – ASP.NET 5-projekt](http://go.microsoft.com/fwlink/p/?LinkId=513124) 
- Molntjänstprojekt (web-roller och worker-roller) - [vad hände – Cloud Service-projekt](http://go.microsoft.com/fwlink/p/?LinkId=516965)
- WebJob-projekt – [vad hände - WebJob-projekt](visual-studio/vs-storage-webjobs-what-happened.md)

## <a name="next-steps"></a>Nästa steg
- [MSDN-Forum: Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
- [Microsoft Azure Storage-teamets blogg](http://blogs.msdn.com/b/windowsazurestorage/)
- [Azure Storage-dokumentation](https://docs.microsoft.com/azure/storage/)
