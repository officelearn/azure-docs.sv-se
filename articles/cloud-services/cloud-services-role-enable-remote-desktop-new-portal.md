---
title: "Aktivera anslutning till fjärrskrivbord för en roll i Azure-molntjänster | Microsoft Docs"
description: "Hur du konfigurerar ditt tjänstprogram för azure-molnet för att tillåta anslutningar till fjärrskrivbord"
services: cloud-services
documentationcenter: 
author: mmccrory
manager: timlt
editor: 
ms.assetid: 73ea1d64-1529-4d72-b58e-f6c10499e6bb
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: mmccrory
ms.openlocfilehash: b9ae4442f57170746eb0de94849b09625be51264
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Aktivera anslutning till fjärrskrivbord för en roll i Azure-molntjänster
> [!div class="op_single_selector"]
> * [Azure-portalen](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)

Fjärrskrivbord kan du få åtkomst till skrivbordet på en roll som körs i Azure. Du kan använda anslutning till fjärrskrivbord för att felsöka och diagnostisera problem med programmet när den körs.

Du kan aktivera anslutning till fjärrskrivbord i din roll under utvecklingen genom att inkludera Remote Desktop-moduler i tjänstedefinitionsfilen eller kan du aktivera fjärrskrivbord via Remote Desktop-tillägget. Det bästa sättet är att använda Remote Desktop-tillägget som du kan aktivera fjärrskrivbord även när programmet distribueras utan att behöva distribuera programmet.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Konfigurera fjärrskrivbord från Azure-portalen
Azure-portalen använder metoden Remote Desktop-tillägget så att du kan aktivera fjärrskrivbord även när programmet distribueras. Den **fjärrskrivbord** bladet för din molntjänst kan du aktivera Fjärrskrivbord, ändra det lokala administratörskontot som används för att ansluta till de virtuella datorerna, certifikatet används i autentisering och ange förfallodatum.

1. Klicka på **molntjänster**, klicka på namnet på Molntjänsten och klicka sedan på **fjärrskrivbord**.

    ![Cloud services fjärrskrivbord](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Välj om du vill aktivera Fjärrskrivbord för en enskild roll eller för alla roller och sedan ändra värdet för switcher till **aktiverad**.

3. Fyll i obligatoriska fält för användarnamn, lösenord, upphör att gälla och certifikat.

    ![Cloud services fjärrskrivbord](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Alla rollinstanser kommer att startas om när du först aktivera Fjärrskrivbord och klicka på OK (markering). Certifikatet som används för att kryptera lösenordet måste installeras på vilken roll för att förhindra att en omstart. För att förhindra en omstart [överföra ett certifikat för Molntjänsten](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) och återgå sedan till den här dialogrutan.
   >
   >
3. I **roller**, väljer du den roll som du vill uppdatera eller välj **alla** för alla roller.

4. När du är klar configuration-uppdateringar, klickar du på **spara**. Det tar en stund innan dina rollinstanser är redo att ta emot anslutningar.

## <a name="remote-into-role-instances"></a>Remote i rollinstanser
När fjärrskrivbord har aktiverats på rollerna, kan du upprätta en anslutning direkt från Azure Portal:

1. Klicka på **instanser** att öppna den **instanser** bladet.
2. Välj en rollinstans som har konfigurerats för fjärrskrivbord.
3. Klicka på **Anslut** om en RDP-fil för instansen.

    ![Cloud services fjärrskrivbord](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Klicka på **öppna** och sedan **Anslut** fjärrskrivbord-anslutningen ska upprättas.

>[!NOTE]
> Om din molntjänst placerad bakom en NSG, du kan behöva skapa regler som tillåter trafik på portarna **3389** och **20000**.  Fjärrskrivbord använder port **3389**.  Molnet tjänstinstanser belastningsutjämnas, så du inte kan styra vilken instans att ansluta till direkt.  Den *RemoteForwarder* och *RemoteAccess* agenter hantera RDP-trafik och tillåter klienten att skicka en RDP-cookie och ange en enskild instans att ansluta till.  Den *RemoteForwarder* och *RemoteAccess* agenter kräver att port **20000*** öppnas, där blockeras om du har en NSG.

## <a name="additional-resources"></a>Ytterligare resurser

[Hur du konfigurerar molntjänster](cloud-services-how-to-configure-portal.md)
[Cloud services vanliga frågor och svar - fjärrskrivbord](cloud-services-faq.md)
