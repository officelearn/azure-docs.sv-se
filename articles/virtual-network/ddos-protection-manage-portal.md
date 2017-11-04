---
title: "Hantera Azure DDoS-skydd Standard med hjälp av Azure portal | Microsoft Docs"
description: "Lär dig hur du använder Azure DDoS-skydd Standard telemetri i Azure-Monitor minimera angreppet."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.openlocfilehash: 5c599b4cc867dbc9a081af3a081195b998f63954
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Hantera Azure DDoS-skydd Standard med hjälp av Azure portal

>[!IMPORTANT]
>Azure DDoS-skydd Standard (DDoS-skydd) är för närvarande under förhandsgranskning. Ett begränsat antal Azure-resurser stöd DDoS-skydd, och i ett begränsat antal regioner. Du behöver [registrera dig för tjänsten](http://aka.ms/ddosprotection) under begränsad förhandsgranskningen att hämta DDoS-skydd är aktiverat för din prenumeration. Du kontaktas av Azure DDoS-teamet vid registreringen och vägleder dig genom processen för aktivering. DDoS-skydd finns tillgänglig i oss Öst oss Väst och West centrala oss regioner. Under förhandsgranskningen gör debiteras inte du för att använda tjänsten.

Den här artikeln visar hur du använder Azure-portalen för att aktivera DDoS-skydd, inaktivera DDoS-skydd och använda telemetri för att minimera angreppet. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på http://portal.azure.com.

## <a name="enable-ddos-protection"></a>Aktivera DDoS-skydd

Aktivera DDoS-skydd på en ny eller befintlig virtuellt nätverk.

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>Skapa ett nytt virtuellt nätverk och aktivera DDoS-skydd

1. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.
2. Välj **nätverk**, och välj sedan **virtuellt nätverk**.
3. Ange information för virtuellt nätverk. Under *DDoS-skydd*, klickar du på **aktiverad**, och klicka sedan på **skapa**.

    ![Skapa det virtuella nätverket](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

En varning om att aktivera DDoS-skydd debiteras. Inga avgifter för DDoS-skydd uppkommer under förhandsgranskningen. Avgifter tas ut vid allmän tillgänglighet (GA) och kunder får 30 dagars varsel före början av kostnader och GA.

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Aktivera DDoS-skydd på ett befintligt virtuellt nätverk 

1. Klicka på **virtuella nätverken** på Azure portal-menyn och välj sedan det virtuella nätverket.
2. Klicka på **DDoS-skydd**, klickar du på **aktiverad** på den *DDoS-skydd* skärmen och klicka sedan på **spara**. 

En varning om att aktivera DDoS-skydd debiteras. Inga avgifter för DDoS-skydd uppkommer under förhandsgranskningen. Avgifter tas ut vid allmän tillgänglighet (GA) och kunder får 30 dagars varsel före början av kostnader och GA.

## <a name="disable-ddos-protection"></a>Inaktivera DDoS-skydd

1. Klicka på **virtuella nätverken** på Azure portal-menyn och välj sedan det virtuella nätverket.
2. Klicka på **DDoS-skydd**, klickar du på **inaktiverad** på den *DDoS-skydd* skärmen och klicka sedan på **spara**.

## <a name="configure-alerts-on-ddos-protection-metrics"></a>Konfigurera aviseringar för mått för DDoS-skydd

Utnyttja Azure-Monitor varningskonfigurationen, kan du välja någon av de tillgängliga måtten DDoS-skydd att varna när det finns en aktiv migrering under ett angrepp. När villkoren är uppfyllda visas en varning e-post på den angivna adressen.

1. Klicka på **övervakaren**, och klicka sedan på **mått**.
2. På den *mått* väljer du den resursgrupp, resurstypen för **offentliga IP-adressen**, och din Azure offentlig IP.
3. Om du vill konfigurera en e-postavisering för ett mått, klickar du på **Klicka om du vill lägga till en avisering**. En e-postavisering kan skapas på alla mått, men det med uppenbara måttet **Under DDoS-attacker eller inte**. Detta är ett booleskt värde 1 eller 0. En **1** innebär du angripet. En **0** innebär att du inte är angripet.
4. Ange nivån för att skickas under ett angrepp **Under DDoS-attacker eller inte** och **villkoret som är större än noll (0) under de senaste 5 minuterna**. Liknande aviseringar kan ställas in för andra mått.

    ![Konfigurera mått](./media/ddos-protection-manage-portal/ddos-metrics.png)

    Inom några minuter för angreppsidentifiering visas ett meddelande med hjälp av Azure-Monitor mätvärden.

    ![Attack avisering](./media/ddos-protection-manage-portal/ddos-alert.png) 

Du kan också lära dig mer om [hur du konfigurerar webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md) och [logikappar](../logic-apps/logic-apps-what-are-logic-apps.md) för att skapa aviseringar.

## <a name="configure-logging-on-ddos-protection-metrics"></a>Konfigurera loggning på mått för DDoS-skydd

1. Klicka på **övervakaren**, och klicka sedan på **diagnostikinställningar**.
2. På den *mått* väljer du den resursgrupp, resurstypen för **offentliga IP-adressen**, och din Azure offentlig IP.
3. Klicka på **aktiverar diagnostik för att samla in följande data**.

Det finns tre alternativ för loggning:

- **Arkivet till ett lagringskonto** – skriver loggar till ett lagringskonto.
- **Dataströmmen till en händelsehubb** – tillåter en logg mottagare att hämta loggar med en händelsehubb. Detta möjliggör integrering med Splunk eller andra SIEM-system.
- **Skicka till logganalys** – skriver loggar till Azure OMS Log Analytics-tjänsten.

## <a name="use-ddos-protection-telemetry"></a>Använd telemetri för DDoS-skydd

Telemetri för en attack tillhandahålls via Azure-Monitor i realtid. Telemetrin är tillgänglig enbart för den varaktighet som en offentlig IP-adress är under lösning. Telemetri visas inte före eller efter en attack minskas.

1. Klicka på **övervakaren**, och klicka sedan på **mått**. 
2. På den *mått* väljer du den resursgrupp, resurstypen för **offentliga IP-adressen**, och din Azure offentlig IP. Ett antal tillgängliga mått visas till vänster på skärmen. De här måtten när du väljer är visas i diagram registreringen i Azure övervakaren mått diagrammet på översiktsskärmen. 

Mått namnen finns olika typer av paket och byte kontra paket, med en grundläggande konstruktion av taggnamn på varje mått på följande sätt:

- **Ignorerade taggnamnet (t.ex. inkommande paket bort DDoS):** antalet paket som tagits bort/befordras av systemet för DDoS-skydd.
- **Vidarebefordrade taggnamnet (t.ex.: inkommande paket som vidarebefordras DDoS):** antalet paket som vidarebefordras av DDoS-systemet till målet VIP-trafik som inte var filtrerade.
- **Inga taggnamnet (t.ex.: inkommande paket DDoS):** det totala antalet paket som hör till bakgrundsrensning systemet – som representerar summan av paket som ignoreras och vidarebefordras.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [Analysera loggar från Azure storage med logganalys](../log-analytics/log-analytics-azure-storage.md)
- [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)