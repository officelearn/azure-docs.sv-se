---
title: "Hantera Azure DDoS-skydd Standard med hjälp av Azure portal | Microsoft Docs"
description: "Lär dig hur du använder Azure DDoS-skydd Standard telemetri i Azure-Monitor minimera angreppet."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 958533079c0e0864ff0e561ad865114ef2a415a8
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2017
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Hantera Azure DDoS-skydd Standard med hjälp av Azure portal

Lär dig mer om att aktivera och inaktivera för tjänsten (DDoS)-skydd och använda telemetri för att minska en DDoS-attack med Azure DDoS-skydd Standard. DDoS-skydd Standard skyddar Azure-resurser som virtuella datorer, belastningsutjämnare och programgatewayer som har en Azure [offentliga IP-adressen](virtual-network-public-ip-address.md) kopplade till den. Läs mer om DDoS-skydd Standard och dess funktioner i [DDoS-skydd Standard översikt](ddos-protection-overview.md). 

>[!IMPORTANT]
>Azure DDoS-skydd Standard (DDoS-skydd) är för närvarande under förhandsgranskning. Ett begränsat antal Azure-resurser stöd för DDoS-skydd och det är endast tillgänglig i ett begränsat antal regioner. En lista över tillgängliga regioner, se [DDoS-skydd Standard översikt](ddos-protection-overview.md). Du behöver [registrera dig för tjänsten](http://aka.ms/ddosprotection) under begränsad förhandsgranskningen att hämta DDoS-skydd är aktiverat för din prenumeration. Efter registrering kontaktas du av Azure DDoS-team som vägleder dig genom processen för aktivering. 

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>Aktivera DDoS-skydd Standard - nytt virtuellt nätverk

1. Logga in på Azure Portal på http://portal.azure.com. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
2. Klicka på **skapar du en resurs** i det övre vänstra hörnet i Azure-portalen.
3. Välj **nätverk**, och välj sedan **virtuellt nätverk**.
4. Skapa ett virtuellt nätverk med dina valda inställningar. Mer information om hur du skapar virtuella nätverk finns [skapa ett virtuellt nätverk](virtual-networks-create-vnet-arm-pportal.md). Under **DDoS-skydd**, klickar du på **aktiverad**, och klicka sedan på **skapa**. Om du inte ser **DDoS-skydd**, en trolig orsak är att din prenumeration inte har registrerats för funktionen. Du måste slutföra [registrering](http://aka.ms/ddosprotection), och meddelande om att din prenumeration har aktiverats för funktionen, innan **DDoS-skydd** visas.

    ![Skapa det virtuella nätverket](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

    > [!WARNING]
    > När du väljer en region väljer du en region som stöds i listan i [Azure DDoS-skydd Standard översikt](ddos-protection-overview.md). Om du inte väljer en region som stöds misslyckas för det virtuella nätverket.

    En varning om att aktivera DDoS-skydd debiteras. Inga avgifter för DDoS-skydd uppkommer under förhandsgranskningen. Avgifter påförs vid allmän tillgänglighet. Du får 30 dagars varsel före början av kostnader och allmän tillgänglighet.

## <a name="enable-ddos-protection-standard---existing-virtual-network"></a>Aktivera DDoS-skydd Standard - befintligt virtuellt nätverk 

1. Klicka på **virtuella nätverken** på Azure portal-menyn och välj sedan det virtuella nätverket.
2. Klicka på **DDoS-skydd**, klickar du på **aktiverad** på den *DDoS-skydd* skärmen och klicka sedan på **spara**. Om du inte ser **DDoS-skydd**, en trolig orsak är att din prenumeration inte har registrerats för funktionen. Du måste slutföra [registrering](http://aka.ms/ddosprotection), och meddelande om att din prenumeration har aktiverats för funktionen, innan **DDoS-skydd** visas. 

    > [!WARNING]
    > Det virtuella nätverket måste finnas i en region som stöds. En lista över regioner som stöds, se [Azure DDoS-skydd Standard översikt](ddos-protection-overview.md).

    En varning om att aktivera DDoS-skydd debiteras. Inga avgifter för DDoS-skydd uppkommer under förhandsgranskningen. Avgifter påförs vid allmän tillgänglighet. Du får 30 dagars varsel före början av kostnader och allmän tillgänglighet.

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Inaktivera DDoS-skydd på ett virtuellt nätverk

1. Klicka på **virtuella nätverken** på Azure portal-menyn och välj sedan det virtuella nätverket.
2. Klicka på **DDoS-skydd**, klickar du på **inaktiverad** på den *DDoS-skydd* skärmen och klicka sedan på **spara**.

## <a name="configure-alerts-on-ddos-protection-metrics"></a>Konfigurera aviseringar för mått för DDoS-skydd

Du kan välja någon av tillgängliga mått för DDoS-skydd att varna när det finns en aktiv migrering under en attack med hjälp av Azure-Monitor varningskonfigurationen. När villkoren är uppfyllda får den angivna adressen en avisering e-post.

1. Klicka på **övervakaren**, och klicka sedan på **mått**.
2. På den *mått* väljer du den resursgrupp, resurstypen för **offentliga IP-adressen**, och din Azure offentliga IP-adress.
3. Om du vill konfigurera en e-postavisering för ett mått, klickar du på **Lägg till mått avisering**. En e-postavisering kan skapas på alla mått, men det med uppenbara måttet **Under DDoS-attacker eller inte**. Detta är ett booleskt värde 1 eller 0. En **1** innebär du angripet. En **0** innebär att du inte är angripet.
4. Ange nivån för att skickas under ett angrepp **Under DDoS-attacker eller inte** och **villkoret som är större än noll (0) under de senaste 5 minuterna**. Liknande aviseringar kan ställas in för andra mått.

    ![Konfigurera mått](./media/ddos-protection-manage-portal/ddos-metrics.png)

    Inom några minuter för angreppsidentifiering visas ett meddelande med hjälp av Azure-Monitor mätvärden.

    ![Attack avisering](./media/ddos-protection-manage-portal/ddos-alert.png) 

Du kan också lära dig mer om [hur du konfigurerar webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md) och [logikappar](../logic-apps/logic-apps-what-are-logic-apps.md) för att skapa aviseringar.

## <a name="configure-logging-on-ddos-protection-standard-metrics"></a>Konfigurera loggning på DDoS-skydd Standard mått

1. Klicka på **övervakaren**, och klicka sedan på **diagnostikinställningar**.
2. På den *mått* väljer du den resursgrupp, resurstypen för **offentliga IP-adressen**, och din Azure offentliga IP-adress.
3. Klicka på **aktiverar diagnostik för att samla in följande data**.

Det finns tre alternativ för loggning:

- **Arkivet till ett lagringskonto**: skriver loggar till ett lagringskonto.
- **Dataströmmen till en händelsehubb**: gör en logg mottagare att hämta loggar med en händelsehubb. Detta möjliggör integrering med Splunk eller andra SIEM-system.
- **Skicka till logganalys**: skriver loggar till Azure OMS Log Analytics-tjänsten.

## <a name="use-ddos-protection-telemetry"></a>Använd telemetri för DDoS-skydd

Telemetri för en attack tillhandahålls via Azure-Monitor i realtid. Telemetrin är tillgänglig enbart för den varaktighet som en offentlig IP-adress är under lösning. Telemetri visas inte före eller efter en attack minskas.

1. Klicka på **övervakaren**, och klicka sedan på **mått**. 
2. På den *mått* väljer du den resursgrupp, resurstypen för **offentliga IP-adressen**, och din Azure offentliga IP-adress. En serie **tillgängliga mått** visas till vänster på skärmen. De här måtten, när du väljer är visas i diagram registreringen i den **Azure övervakaren mått diagram** på översiktsskärmen. 

Mått namnen finns olika typer av paket och byte kontra paket, med en grundläggande konstruktion av taggnamn på varje mått på följande sätt:

- **Ignorerade taggnamnet (t.ex. inkommande paket bort DDoS)**: antalet paket som tagits bort/befordras av systemet för DDoS-skydd.
- **Vidarebefordrade taggnamnet (t.ex.: inkommande paket som vidarebefordras DDoS)**: antal paket som vidarebefordras av DDoS-systemet till målet VIP-trafik som inte var filtrerade.
- **Inga taggnamnet (t.ex.: inkommande paket DDoS):** det totala antalet paket som hör till bakgrundsrensning systemet – som representerar summan av paket som ignoreras och vidarebefordras.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Analysera loggar från Azure storage med logganalys](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Kom igång med Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)