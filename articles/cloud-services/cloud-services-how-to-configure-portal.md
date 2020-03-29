---
title: Konfigurera en molntjänst (portal) | Microsoft-dokument
description: Lär dig hur du konfigurerar molntjänster i Azure. Lär dig att uppdatera konfigurationen av molntjänsten och konfigurera fjärråtkomst till rollinstanser. Dessa exempel använder Azure-portalen.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 12/07/2016
ms.author: tagore
ms.openlocfilehash: 554d3e465b42ca889ba03565e87193f80e89ed1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75361015"
---
# <a name="how-to-configure-cloud-services"></a>Konfigurera molntjänster

Du kan konfigurera de vanligaste inställningarna för en molntjänst i Azure-portalen. Eller, om du vill uppdatera dina konfigurationsfiler direkt, hämta en tjänstkonfigurationsfil för att uppdatera och sedan ladda upp den uppdaterade filen och uppdatera molntjänsten med konfigurationsändringarna. Oavsett vilket, konfigurationsuppdateringarna skjuts ut till alla rollinstanser.

Du kan också hantera instanser av dina molntjänstroller eller fjärrskrivbord till dem.

Azure kan bara säkerställa 99,95 procent tjänsttillgänglighet under konfigurationsuppdateringarna om du har minst två rollinstanser för varje roll. Det gör det möjligt för en virtuell dator att bearbeta klientbegäranden medan den andra uppdateras. Mer information finns i [Servicenivåavtal](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Ändra en molntjänst

När du har öppnat [Azure-portalen](https://portal.azure.com/)navigerar du till din molntjänst. Härifrån hanterar du många aspekter av det.

![Sidan Inställningar](./media/cloud-services-how-to-configure-portal/cloud-service.png)

Länkarna **Inställningar** eller **Alla inställningar** öppnar **Inställningar** där du kan ändra **egenskaper,** ändra **konfigurationen,** hantera certifikat, ställa in **varningsregler**och hantera de **användare** som har åtkomst till den här molntjänsten. **Alert rules**

![Azure-molntjänstinställningar](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Hantera gästoperativsystem-version

Som standard uppdaterar Azure regelbundet gästoperativsystemet till den senaste avbildningen som stöds i os-familjen som du har angett i tjänstkonfigurationen (.cscfg), till exempel Windows Server 2016.

Om du behöver rikta in dig på en viss OS-version kan du ange den i **Konfiguration**.

![Ange OS-version](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Om du väljer en specifik OS-version inaktiveras automatiska OS-uppdateringar och ditt ansvar åtgärdas. Du måste se till att dina rollinstanser tar emot uppdateringar eller så kan du utsätta ditt program för säkerhetsproblem.

## <a name="monitoring"></a>Övervakning

Du kan lägga till aviseringar till din molntjänst. Klicka på **Lägg** > **Alert Rules** > **till avisering av inställningar.**

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Härifrån kan du ställa in en avisering. Med listrutan **Mått** kan du ställa in en avisering för följande typer av data.

* Disk läsa
* Skriva disk
* Nätverk i
* Nätverk ut
* CPU-procent

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Konfigurera övervakning från en måttpanel

I stället för att använda > **inställningarvarningsregler**kan du klicka på en av måttpanelerna i avsnittet **Övervakning** i molntjänsten. **Settings**

![Övervakning av molntjänster](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Härifrån kan du anpassa diagrammet som används med panelen eller lägga till en varningsregel.

## <a name="reboot-reimage-or-remote-desktop"></a>Starta om, ta igen eller fjärrskrivbord

Du kan konfigurera fjärrskrivbord via [Azure-portalen (konfigurera fjärrskrivbord),](cloud-services-role-enable-remote-desktop-new-portal.md) [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)eller Visual [Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Om du vill starta om, använda om eller fjärransluta till en molntjänst väljer du molntjänstinstansen.

![Molntjänstinstans](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Du kan sedan initiera en fjärrskrivbordsanslutning, fjärrstarta instansen eller fjärrinbilda (börja med en ny bild) instansen.

![Knappar för molntjänstinstans](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Konfigurera om CSCFG

Du kan behöva konfigurera om molntjänsten via [filen Service config (cscfg).](cloud-services-model-and-package.md#cscfg) Först måste du ladda ner cscfg-filen, ändra den och sedan ladda upp den.

1. Klicka på ikonen **Inställningar** eller länken **Alla inställningar** för att öppna **inställningar**.

    ![Sidan Inställningar](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Klicka på **konfigurationsartikeln.**

    ![Konfigurationsblad](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Klicka på knappen **Hämta**.

    ![Ladda ned](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. När du har uppdaterat tjänstkonfigurationsfilen laddar du upp och installerar konfigurationsuppdateringarna:

    ![Ladda upp](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Markera CSCFG-filen och klicka på **OK**.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [distribuerar en molntjänst](cloud-services-how-to-create-deploy-portal.md).
* Konfigurera ett [eget domännamn](cloud-services-custom-domain-name-portal.md).
* [Hantera din molntjänst](cloud-services-how-to-manage-portal.md).
* Konfigurera [SSL-certifikat](cloud-services-configure-ssl-certificate-portal.md).



