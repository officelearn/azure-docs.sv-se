---
title: Lägg till Azure Storage med hjälp av anslutna Services i Visual Studio | Microsoft Docs
description: Lägg till Azure Storage i appen med hjälp av dialogrutan Visual Studio Lägg till anslutna tjänster
services: visual-studio-online
author: ghogen
manager: douge
assetId: 521ec044-ad4b-4828-8864-01decde2e758
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/26/2017
ms.author: ghogen
ms.openlocfilehash: 3c5d3dc1d91a6f8bb1816b2985f86ec5c4a12e63
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>Lägga till Azure storage med hjälp av Visual Studio anslutna Services
Med Visual Studio, du kan ansluta något av följande till Azure Storage med hjälp av den **Lägg till anslutna tjänster** dialogrutan:

- C#-Molntjänsten
- .NET-serverdel Mobiltjänst
- ASP.NET-webbplats eller tjänst
- ASP.NET Core service
- Azure Webjobs-tjänst 

Funktionen anslutna tjänsten lägger till alla nödvändiga referenser och anslutningen kod i ditt projekt och ändrar konfigurationsfilerna på lämpligt sätt. 

När du har slutfört den **Lägg till anslutna tjänster** dialogrutan visar dokumentation med de steg som krävs för att börja arbeta med blob storage, köer och tabeller automatiskt.

## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>Ansluta till Azure Storage genom att använda dialogrutan anslutna tjänster
1. Öppna projektet i Visual Studio

1. I **Solution Explorer**, högerklicka på den **anslutna tjänster** nod, och från snabbmenyn och välj **Lägg till ansluten tjänst**.
   
    ![Lägg till Azure ansluten tjänst](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. I den **anslutna tjänster** väljer **lagringsutrymmet i molnet med Azure Storage**.
   
    ![Lägg till Azure Storage](./media/vs-azure-tools-connected-services-storage/add-azure-storage.png)

1. I den **Azure Storage** dialogrutan, Välj ett befintligt lagringskonto och välj **Lägg till**.
   
    Om du behöver skapa ett lagringskonto går du till nästa steg. Annars fortsätter du till steg 6.
    
    ![Lägg till befintligt lagringskonto i projekt](./media/vs-azure-tools-connected-services-storage/select-azure-storage-account.png)

1. Skapa ett lagringskonto: 
   
   1. Välj **skapa ett nytt Lagringskonto** längst ned i dialogrutan.

   1. Fyll i den **skapa Lagringskonto** dialogrutan och välj **skapa**.
      
       ![Nya Azure storage-konto](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)
      
   1. När den **Azure Storage** dialogrutan visas, visas det nya lagringskontot i listan. Markera det nya lagringskontot i listan och välj **Lägg till**.

1. Lagring ansluten visas under den **referenser** nod i projektet.
   
## <a name="how-your-project-is-modified"></a>Hur projektet har ändrats
När du avslutar dialogrutan lägger till referenser i Visual Studio och ändrar vissa konfigurationsfiler. Vissa ändringar beror på projekttypen: 

- ASP.NET-projekt - [vad hände – ASP.NET-projekt](http://go.microsoft.com/fwlink/p/?LinkId=513126)
- ASP.NET Core projekt - [vad hände – ASP.NET 5 projekt](http://go.microsoft.com/fwlink/p/?LinkId=513124) 
- Molntjänstprojekt (webb- och arbetsroller) - [vad hände – Cloud Service-projekt](http://go.microsoft.com/fwlink/p/?LinkId=516965)
- Webbjobbet projekt - [vad hände - Webbjobb projekt](visual-studio/vs-storage-webjobs-what-happened.md)

## <a name="next-steps"></a>Nästa steg
- [MSDN-Forum: Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
- [Microsoft Azure Storage-teamets blogg](http://blogs.msdn.com/b/windowsazurestorage/)
- [Azure Storage-dokumentation](https://docs.microsoft.com/azure/storage/)
