---
title: Använda portalen för att aktivera Fjärrskrivbord för en roll
titleSuffix: Azure Cloud Services
description: Konfigurera ditt azure cloud service-program så att fjärrskrivbordsanslutningar tillåts
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: d65f4b55be317234c10a0e90cfe413d9e38a6a90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247467"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services

> [!div class="op_single_selector"]
> * [Azure-portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Powershell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Med Fjärrskrivbord kan du komma åt skrivbordet för en roll som körs i Azure. Du kan använda en anslutning till fjärrskrivbord för att felsöka och diagnostisera problem med programmet medan det körs.

Du kan aktivera en anslutning till fjärrskrivbord i rollen under utvecklingen genom att inkludera modulerna för fjärrskrivbord i tjänstdefinitionen eller välja att aktivera Fjärrskrivbord via tillägget För fjärrskrivbord. Den metod som föredras är att använda tillägget Fjärrskrivbord eftersom du kan aktivera Fjärrskrivbord även efter att programmet har distribuerats utan att behöva distribuera om programmet.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Konfigurera Fjärrskrivbord från Azure-portalen

Azure-portalen använder metoden För tillägg för fjärrskrivbord så att du kan aktivera Fjärrskrivbord även efter att programmet har distribuerats. Med inställningarna för **fjärrskrivbord** för molntjänsten kan du aktivera Fjärrskrivbord, ändra det lokala administratörskontot som används för att ansluta till de virtuella datorerna, certifikatet som används vid autentisering och ange utgångsdatum.

1. Klicka på **Molntjänster,** välj namnet på molntjänsten och välj sedan **Fjärrskrivbord**.

    ![Fjärrskrivbord för molntjänster](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Välj om du vill aktivera Fjärrskrivbord för en enskild roll eller för alla roller och ändra sedan värdet för växlaren till **Aktiverad**.

3. Fyll i de obligatoriska fälten för användarnamn, lösenord, utgång och certifikat.

    ![Fjärrskrivbord för molntjänster](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Alla rollinstanser startas om när du först aktiverar Fjärrskrivbord och väljer **OK** (bock). För att förhindra en omstart måste certifikatet som används för att kryptera lösenordet installeras på rollen. För att förhindra en omstart [laddar du upp ett certifikat för molntjänsten](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) och går sedan tillbaka till den här dialogrutan.

4. Välj den roll som du vill uppdatera eller välj **Alla** för alla roller i **Roller.**

5. När du är klar med konfigurationsuppdateringarna väljer du **Spara**. Det tar en stund innan dina rollinstanser är redo att ta emot anslutningar.

## <a name="remote-into-role-instances"></a>Fjärransluten till rollinstanser

När Fjärrskrivbord har aktiverats på rollerna kan du initiera en anslutning direkt från Azure-portalen:

1. Klicka på **Instanser** för att öppna **inställningarna för instanser.**
2. Välj en rollinstans som har Fjärrskrivbord konfigurerat.
3. Klicka på **Anslut** om du vill hämta en RDP-fil för rollinstansen.

    ![Fjärrskrivbord för molntjänster](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Klicka på **Öppna** och sedan **anslut** för att starta anslutningen till fjärrskrivbord.

>[!NOTE]
> Om din molntjänst sitter bakom en NSG kan du behöva skapa regler som tillåter trafik på portarna **3389** och **20000**.  Fjärrskrivbord använder port **3389**.  Cloud Service-instanser är belastningsbalanserade, så du kan inte direkt styra vilken instans som ska anslutas till.  *RemoteForwarder-* och *RemoteAccess-agenter* hanterar RDP-trafik och tillåter klienten att skicka en RDP-cookie och ange en enskild instans att ansluta till.  *RemoteForwarder-* och *RemoteAccess-agenterna* kräver att port **20000*** är öppen, vilket kan blockeras om du har en NSG.

## <a name="additional-resources"></a>Ytterligare resurser

[Konfigurera molntjänster](cloud-services-how-to-configure-portal.md)
