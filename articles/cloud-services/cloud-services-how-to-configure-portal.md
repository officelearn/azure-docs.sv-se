---
title: Så här konfigurerar du en molntjänst (portal) | Microsoft Docs
description: Lär dig hur du konfigurerar molntjänster i Azure. Lär dig att uppdatera konfigurationen för Molntjänsten och konfigurera fjärråtkomst till rollinstanser. De här exemplen använder Azure portal.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7308f3c0-825e-499d-bfa5-c60f86371921
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: jeconnoc
ms.openlocfilehash: 904056363c685ef0a16b229ce72383eb80701a39
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006421"
---
# <a name="how-to-configure-cloud-services"></a>Så här konfigurerar du Cloud Services

Du kan konfigurera de vanligaste inställningarna för en molntjänst i Azure-portalen. Du kan också uppdatera konfigurationsfilerna direkt, hämta en tjänstkonfigurationsfil för uppdatering och sedan överföra den uppdaterade filen och uppdatera molntjänsten med konfigurationsändringarna. Vilket sätt du än väljer kommer konfigurationsuppdateringarna att skickas ut till alla rollinstanser.

Du kan också hantera instanser av din molntjänstroller eller fjärrskrivbord i dessa.

Azure kan bara kontrollera tillgänglighets på 99,95 procent under konfigurationsuppdateringarna om du har minst två rollinstanser för varje roll. Som gör det möjligt för en virtuell dator att bearbeta klientbegäranden medan den andra uppdateras. Mer information finns i [serviceavtal](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Ändra en molntjänst

Efter att den [Azure-portalen](https://portal.azure.com/), navigera till din molntjänst. Härifrån kan hantera du många aspekter av den.

![Inställningssidan](./media/cloud-services-how-to-configure-portal/cloud-service.png)

Den **inställningar** eller **alla inställningar** länkar öppnas **inställningar** där du kan ändra den **egenskaper**, ändra den  **Konfigurationen**, hantera den **certifikat**, Ställ in **Aviseringsregler**, och hantera den **användare** som har åtkomst till den här Molntjänsten.

![Azure-molnet tjänstinställningar](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Hantera gäst-OS-version

Som standard uppdateras Azure regelbundet dina gästoperativsystemet på den senaste stödda avbildningen i OS-familj som du har angett i din tjänstkonfiguration (.cscfg), till exempel Windows Server 2016.

Om du vill rikta en specifik version av Operativsystemet kan du ange den i **Configuration**.

![Ange OS-version](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Välja en specifik operativsystemversion inaktiverar automatisk OS uppdaterar och gör korrigeringar ditt ansvar. Du måste se till att dina rollinstanser tar emot uppdateringar eller du kan lägga upp ditt program till säkerhetsproblem.

## <a name="monitoring"></a>Övervakning

Du kan lägga till aviseringar i din molntjänst. Klicka på **inställningar** > **Aviseringsregler** > **Lägg till avisering**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Härifrån kan ställa du in en avisering. Med den **mått** nedrullningsbara listrutan som du kan ställa in en avisering för följande typer av data.

* Disk-lästa
* Diskskrivning
* Nätverk in
* Nätverk ut
* CPU-procent

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Konfigurera övervakning från en metrisk panel

Istället för att använda **inställningar** > **Aviseringsregler**, du kan klicka på någon av mått paneler på den **övervakning** avsnittet av Molntjänsten.

![Molntjänst övervakning](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Härifrån kan du anpassa diagram som används med panelen eller Lägg till en varningsregel.

## <a name="reboot-reimage-or-remote-desktop"></a>Omstart eller återställa avbildningen av fjärrskrivbord

Du kan konfigurera fjärrskrivbord via den [Azure-portalen (Konfigurera fjärrskrivbord)](cloud-services-role-enable-remote-desktop-new-portal.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md), eller via [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Om du vill starta om, väljer reimage eller Fjärranslut till en molntjänst, du molntjänstinstans.

![Molntjänstinstans](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Du kan initiera en fjärrskrivbordsanslutning, via en fjärranslutning starta om instansen eller återavbilda via en fjärranslutning (börja med en ny avbildning) instansen.

![Cloud Service-instans knappar](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Konfigurera om din .cscfg

Du kan behöva konfigurera om din molntjänst via den [service config (cscfg)](cloud-services-model-and-package.md#cscfg) fil. Först måste du ladda ned din .cscfg-filen, ändrar du den och sedan ladda upp den.

1. Klicka på den **inställningar** ikonen eller **alla inställningar** länken så att du öppnar **inställningar**.

    ![Inställningssidan](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Klicka på den **Configuration** objekt.

    ![Konfigurationsbladet](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Klicka på knappen **Hämta**.

    ![Ladda ned](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. När du uppdaterar tjänstkonfigurationsfilen, ladda upp och tillämpa uppdateringarna för:

    ![Ladda upp](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Välj .cscfg-filen och klicka på **OK**.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [distribuera en molntjänst](cloud-services-how-to-create-deploy-portal.md).
* Konfigurera en [domännamn](cloud-services-custom-domain-name-portal.md).
* [Hantera din molntjänst](cloud-services-how-to-manage-portal.md).
* Konfigurera [ssl-certifikat](cloud-services-configure-ssl-certificate-portal.md).
