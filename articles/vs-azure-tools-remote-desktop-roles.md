---
title: "Med hjälp av fjärrskrivbord med Azure roller | Microsoft Docs"
description: "Med hjälp av fjärrskrivbord med Azure roller"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: eab135d10c0d6df8ca72ac47d6804017a998a3d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="using-remote-desktop-with-azure-roles"></a>Med hjälp av fjärrskrivbord med Azure roller
Med hjälp av Azure SDK och Remote Desktop Services, kan du komma åt Azure roller och virtuella datorer som hanteras av Azure. Du kan konfigurera Fjärrskrivbordstjänster från en Azure cloud service-projekt i Visual Studio. Om du vill aktivera Remote Desktop Services måste du skapa ett fungerande projekt som innehåller en eller flera roller och publicera den på Azure.

> [!IMPORTANT]
> Du ska använda en Azure roll för felsökning eller endast utveckling. Syftet med varje virtuell dator är att köra en viss roll i ditt Azure-program inte att köra andra program. Om du vill använda Azure som värd för en virtuell dator som du kan använda för något ändamål, se åtkomst till Azure virtuella datorer från Server Explorer.
> 
> 

## <a name="to-enable-and-use-remote-desktop-for-an-azure-role"></a>Aktivera och använda Fjärrskrivbord för en Azure-roll
1. Öppna snabbmenyn för cloud service-projekt i Solution Explorer och välj sedan **publicera**.
   
    Den **publicera Azure-programmet** guiden visas.
   
    ![Publicera kommandot för en Cloud Service-projekt](./media/vs-azure-tools-remote-desktop-roles/IC799161.png)
2. Längst ned i **Publiceringsinställningar i Microsoft Azure** sidan i guiden väljer den **aktivera Fjärrskrivbord** för alla roller. 
   
    Den **konfiguration av fjärrskrivbord** dialogrutan visas.
3. Längst ned i den **konfiguration av fjärrskrivbord** dialogrutan väljer du den **fler alternativ** knappen. 
   
    Detta visar en nedrullningsbar listruta som låter dig skapa eller välj ett certifikat så att du kan kryptera autentiseringsuppgifter information när du ansluter via fjärrskrivbord.
4. Välj i den nedrullningsbara listan  **&lt;skapa >**, eller välj en befintlig från listan. 
   
    Hoppa över följande steg om du väljer ett befintligt certifikat.
   
   > [!NOTE]
   > De certifikat som du behöver för en anslutning till fjärrskrivbord skiljer sig från de certifikat som du använder för andra åtgärder i Azure. Fjärråtkomst-certifikatet måste ha en privat nyckel.
   > 
   > 
   
    Den **skapa certifikat** dialogrutan visas.
   
   1. Ange ett eget namn för det nya certifikatet och välj sedan den **OK** knappen. Det nya certifikatet visas i listrutan.
   2. I den **konfiguration av fjärrskrivbord** dialogrutan Ange ett användarnamn och ett lösenord.
      
       Du kan inte använda ett befintligt konto. Ange inte administratören som användarnamn för det nya kontot.
      
      > [!NOTE]
      > Om lösenordet inte uppfyller kraven på komplexitet, visas en röd ikon bredvid textrutan lösenord. Lösenordet måste innehålla versaler, gemena bokstäver och siffror eller symboler.
      > 
      > 
   3. Välj ett datum som kontot upphör att gälla och efter vilken anslutning till fjärrskrivbord kommer att blockeras.
   4. När du har angett informationen som krävs, välja den **OK** knappen.
      
       Flera inställningar som gör att fjärråtkomsttjänsten läggs till i .cscfg- och .csdef-filer.
5. I den **Publiceringsinställningar i Microsoft Azure** guiden, Välj den **OK** knappen när du är redo att publicera din tjänst i molnet.
   
    Om du inte är redo att publicera, väljer du den **Avbryt** knappen. Konfigurationsinställningarna sparas och du kan publicera din molntjänst senare.

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Ansluta till en Azure-roll med hjälp av fjärrskrivbord
Du kan använda Server Explorer för att logga in på virtuella datorer som är värd för Azure när du har publicerat din tjänst i molnet på Azure. 

1. I Server Explorer expanderar den **Azure** nod, och sedan expanderar du noden för en tjänst i molnet och en av dess roller för att visa en lista över instanser.
2. Öppna snabbmenyn för en instans-nod och välj sedan **ansluta med hjälp av fjärrskrivbord**.
   
    ![Ansluter via fjärrskrivbord](./media/vs-azure-tools-remote-desktop-roles/IC799162.png)
3. Ange användarnamn och lösenord som du skapade tidigare. Du är nu inloggad i fjärrsessionen.

