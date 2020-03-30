---
title: Konfigurera proxyinställningar för mobilitetstjänsten för Azure till Azure Disaster Recovery | Microsoft-dokument
description: Innehåller information om hur du konfigurerar mobilitetstjänster när kunder använder en proxy i sin källmiljö.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 3d33b5a89a718a41e5c547551f6e7eb4f7033a63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503132"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Konfigurera proxyinställningar för mobilitetstjänsten för Azure till Azure Disaster Recovery

Den här artikeln innehåller vägledning om hur du anpassar nätverkskonfigurationer på den virtuella virtuella datorn för mål Azure (VM) när du replikerar och återställer virtuella Azure-datorer från en region till en annan med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Syftet med det här dokumentet är att tillhandahålla steg för att konfigurera proxyinställningar för Azure Site Recovery Mobility Service i Azure to Azure Disaster Recovery-scenariot. 

Proxyservrar är nätverksgateways som tillåter/inte tillåter nätverksanslutningar till slutpunkter. Vanligtvis är en proxy en dator utanför klientdatorn som försöker komma åt nätverksslutpunkter. En bypass-lista gör det möjligt för klienten att göra anslutningar direkt till slutpunkterna utan att gå igenom proxyn. Ett användarnamn och lösenord kan eventuellt ställas in för en proxy av nätverksadministratörer så att endast autentiserade klienter kan använda proxy. 

## <a name="before-you-start"></a>Innan du börjar

Lär dig hur Site Recovery ger haveriberedskap för [det här scenariot](azure-to-azure-architecture.md).
Förstå [nätverksvägledningen](azure-to-azure-about-networking.md) när du replikerar och återställer virtuella Azure-datorer från en region till en annan med hjälp av [Azure Site Recovery](site-recovery-overview.md).
Kontrollera att proxyn är konfigurerad på rätt sätt baserat på organisationens behov.

## <a name="configure-the-mobility-service"></a>Konfigurera mobilitetstjänsten

Mobilitetstjänsten stöder endast oautentiserade proxyservrar. Det ger två sätt att ange proxyinformation för kommunikation med slutpunkter för webbplatsåterställning. 

### <a name="method-1-auto-detection"></a>Metod 1: Automatisk identifiering

Mobilitetstjänsten identifierar automatiskt proxyinställningarna från miljöinställningar eller IE-inställningar (endast Windows) under aktivera replikering. 

- Windows OS: Under Enable Replication identifierar Mobilitetstjänsten proxyinställningarna som konfigurerade i Internet Explorer för användare av lokalt system. Om du vill konfigurera proxy för local system-konto kan en administratör använda psexec för att starta en kommandotolk och sedan Internet Explorer. 
- Windows OS: Proxyinställningar konfigureras som miljövariabler http_proxy och no_proxy. 
- Linux OS: Proxyinställningar konfigureras i /etc/profile eller /etc/environment som miljövariabler http_proxy, no_proxy. 
- Proxyinställningarna för automatisk upptäckt sparas i Mobility Service-proxyproxyen ProxyInfo.conf 
- Standardplats för ProxyInfo.conf 
    - Windows: C:\ProgramData\Microsoft Azure Site Recovery\Config\ProxyInfo.conf 
    - Linux: /usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>Metod 2: Ange anpassade programproxyinställningar

I det här fallet tillhandahåller kunden anpassade programproxyinställningar i Mobilitetstjänstens config-fil ProxyInfo.conf. Med den här metoden kan kunder endast tillhandahålla proxy för Mobilitetstjänsten eller en annan proxy för Azure Site Recovery Mobility Service än en proxy (eller ingen proxy) för resten av programmen på datorn.

## <a name="proxy-template"></a>Proxymall
ProxyInfo.conf innehåller följande mall [proxy]http://1.2.3.4 Address= Port=5678 BypassList=hypervrecoverymanager.windowsazure.com,login.microsoftonline.com,blob.core.windows.net. Den BypassList stöder inte jokertecken som "*.windows.net" men att ge windows.net är tillräckligt bra för att kringgå. 

## <a name="next-steps"></a>Nästa steg:
- Läs [nätverksvägledning](site-recovery-azure-to-azure-networking-guidance.md) för replikering av virtuella Azure-datorer.
- Distribuera haveriberedskap genom [att replikera virtuella Azure-datorer](site-recovery-azure-to-azure.md).