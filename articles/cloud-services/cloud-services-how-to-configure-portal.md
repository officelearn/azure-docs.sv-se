---
title: Så här konfigurerar du en tjänst i molnet (portal) | Microsoft Docs
description: Lär dig hur du konfigurerar molntjänster i Azure. Lär dig att uppdatera tjänstkonfigurationen för molnet och konfigurera fjärråtkomst till rollinstanser. De här exemplen använder Azure-portalen.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 7308f3c0-825e-499d-bfa5-c60f86371921
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: adegeo
ms.openlocfilehash: 255e496881f6269d37d3b2d982ba31861458631c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
ms.locfileid: "29874329"
---
# <a name="how-to-configure-cloud-services"></a>Så här konfigurerar du molntjänster

Du kan konfigurera de vanligaste inställningarna för en molnbaserad tjänst i Azure-portalen. Du kan också uppdatera konfigurationsfilerna direkt, hämta en tjänstkonfigurationsfil för uppdatering och sedan överföra den uppdaterade filen och uppdatera molntjänsten med konfigurationsändringarna. Vilket sätt du än väljer kommer konfigurationsuppdateringarna att skickas ut till alla rollinstanser.

Du kan också hantera instanser av dina molntjänstroller eller fjärrskrivbord i dem.

Azure kan bara garantera 99,95% tjänsten under configuration uppdateringar om du har minst två rollinstanser för varje roll. Som gör det möjligt för en virtuell dator att bearbeta klientbegäranden medan den andra uppdateras. Mer information finns i [serviceavtal](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Ändra en tjänst i molnet

Efter att den [Azure-portalen](https://portal.azure.com/), navigera till Molntjänsten. Härifrån kan hantera du många aspekter av den.

![Inställningssidan](./media/cloud-services-how-to-configure-portal/cloud-service.png)

Den **inställningar** eller **alla inställningar** länkar öppnas **inställningar** där du kan ändra den **egenskaper**, ändra den  **Konfigurationen**, hantera den **certifikat**, Ställ in **Varna regler**, och hantera den **användare** som har åtkomst till den här Molntjänsten.

![Azure cloud service-inställningar](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Hantera gäst-OS-version

Som standard uppdaterar Azure regelbundet dina gästoperativsystemet till den senaste stödda bilden i OS-familjen som du har angett i tjänstkonfigurationen (.cscfg), till exempel Windows Server 2016.

Om du behöver använder en viss OS-version du kan ange i **Configuration**.

![Ange OS-version](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Om du väljer en viss OS-version inaktiverar automatisk OS uppdateringar och gör korrigering ditt ansvar. Du måste se till att dina rollinstanser tar emot uppdateringar eller du kan visa ditt program till säkerhetsproblem.

## <a name="monitoring"></a>Övervakning

Du kan lägga till aviseringar Molntjänsten. Klicka på **inställningar** > **avisering regler** > **Lägg till avisering**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Härifrån kan ställa du in en avisering. Med den **mått** nedrullningsbara listrutan som du kan ställa in en avisering för följande typer av data.

* Disk-lästa
* Skrivning till disk
* Nätverk i
* Nätverk ut
* CPU-procent

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Konfigurera övervakning från en mått panel

Istället för att använda **inställningar** > **Varningsregler**, kan du klicka på någon av panelerna mått i den **övervakning** avsnitt av Molntjänsten.

![Cloud Service Monitoring](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Härifrån kan du anpassa diagrammet används med panelen eller lägga till en varningsregel.

## <a name="reboot-reimage-or-remote-desktop"></a>Omstart eller avbildningsåterställning fjärrskrivbord

Du kan ställa in fjärrskrivbord via den [Azure-portalen (ställa in fjärrskrivbord)](cloud-services-role-enable-remote-desktop-new-portal.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md), eller via [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Välj cloud service-instans om du vill starta om, avbildningsåterställning eller fjärråtkomst till en molntjänst.

![Cloud Service-instans](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Initiera en fjärrskrivbordsanslutning, starta från en fjärrdator om instansen eller via fjärranslutning återavbilda (startar med en ny avbildning) instansen.

![Cloud Service-instans knappar](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Konfigurera om din .cscfg

Du kan behöva konfigurera om Molntjänsten via den [service config (cscfg)](cloud-services-model-and-package.md#cscfg) fil. Du måste först hämta .cscfg-filen och ändra den sedan ladda upp den.

1. Klicka på den **inställningar** ikon eller **alla inställningar** länk för att öppna **inställningar**.

    ![Inställningssidan](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Klicka på den **Configuration** objekt.

    ![Konfiguration av bladet](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Klicka på knappen **Hämta**.

    ![Ladda ned](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. När du har uppdaterat tjänstkonfigurationsfilen överföra och tillämpa konfigurationsuppdateringarna:

    ![Ladda upp](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Välj .cscfg-filen och klicka på **OK**.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [distribuera en tjänst i molnet](cloud-services-how-to-create-deploy-portal.md).
* Konfigurera en [domännamn](cloud-services-custom-domain-name-portal.md).
* [Hantera din molntjänst](cloud-services-how-to-manage-portal.md).
* Konfigurera [ssl-certifikat](cloud-services-configure-ssl-certificate-portal.md).
