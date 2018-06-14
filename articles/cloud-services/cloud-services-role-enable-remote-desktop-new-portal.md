---
title: Aktivera anslutning till fjärrskrivbord för en roll i Azure-molntjänster | Microsoft Docs
description: Hur du konfigurerar ditt tjänstprogram för azure-molnet för att tillåta anslutningar till fjärrskrivbord
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: 73ea1d64-1529-4d72-b58e-f6c10499e6bb
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: mmccrory
ms.openlocfilehash: 2169fd95f51b468770a2e1e4c185d493babf220f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
ms.locfileid: "29877372"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Aktivera anslutning till fjärrskrivbord för en roll i Azure-molntjänster

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Fjärrskrivbord kan du få åtkomst till skrivbordet på en roll som körs i Azure. Du kan använda anslutning till fjärrskrivbord för att felsöka och diagnostisera problem med programmet när den körs.

Du kan aktivera anslutning till fjärrskrivbord i din roll under utvecklingen genom att inkludera Remote Desktop-moduler i tjänstedefinitionsfilen eller kan du aktivera fjärrskrivbord via Remote Desktop-tillägget. Det bästa sättet är att använda Remote Desktop-tillägget som du kan aktivera fjärrskrivbord även när programmet distribueras utan att behöva distribuera programmet.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Konfigurera fjärrskrivbord från Azure-portalen

Azure-portalen använder metoden Remote Desktop-tillägget så att du kan aktivera fjärrskrivbord även när programmet distribueras. Den **fjärrskrivbord** inställningar för din molntjänst kan du aktivera Fjärrskrivbord, ändra det lokala administratörskontot som används för att ansluta till de virtuella datorerna, certifikatet används för autentisering och ange upphör att gälla datum.

1. Klicka på **molntjänster**, Välj namnet på Molntjänsten och välj sedan **fjärrskrivbord**.

    ![Cloud services fjärrskrivbord](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Välj om du vill aktivera Fjärrskrivbord för en enskild roll eller för alla roller och sedan ändra värdet för switcher till **aktiverad**.

3. Fyll i obligatoriska fält för användarnamn, lösenord, upphör att gälla och certifikat.

    ![Cloud services fjärrskrivbord](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Alla rollinstanser kommer att startas om när du först aktivera Fjärrskrivbord och väljer **OK** (markering). Certifikatet som används för att kryptera lösenordet måste installeras på vilken roll för att förhindra att en omstart. För att förhindra en omstart [överföra ett certifikat för Molntjänsten](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) och återgå sedan till den här dialogrutan.

4. I **roller**, väljer du den roll som du vill uppdatera eller välj **alla** för alla roller.

5. När du är klar configuration-uppdateringar, Välj **spara**. Det tar en stund innan dina rollinstanser är redo att ta emot anslutningar.

## <a name="remote-into-role-instances"></a>Remote i rollinstanser

När fjärrskrivbord har aktiverats på rollerna, kan du upprätta en anslutning direkt från Azure portal:

1. Klicka på **instanser** att öppna den **instanser** inställningar.
2. Välj en rollinstans som har konfigurerats för fjärrskrivbord.
3. Klicka på **Anslut** om en RDP-fil för instansen.

    ![Cloud services fjärrskrivbord](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Klicka på **öppna** och sedan **Anslut** fjärrskrivbord-anslutningen ska upprättas.

>[!NOTE]
> Om din molntjänst placerad bakom en NSG, du kan behöva skapa regler som tillåter trafik på portarna **3389** och **20000**.  Fjärrskrivbord använder port **3389**.  Molnet tjänstinstanser belastningsutjämnas, så du inte kan styra vilken instans att ansluta till direkt.  Den *RemoteForwarder* och *RemoteAccess* agenter hantera RDP-trafik och tillåter klienten att skicka en RDP-cookie och ange en enskild instans att ansluta till.  Den *RemoteForwarder* och *RemoteAccess* agenter kräver att port **20000*** är öppna, som kan blockeras om du har en NSG.

## <a name="additional-resources"></a>Ytterligare resurser

[Så här konfigurerar du molntjänster](cloud-services-how-to-configure-portal.md)
