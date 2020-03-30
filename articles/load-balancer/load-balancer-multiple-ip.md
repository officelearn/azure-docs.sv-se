---
title: Belastningsutjämning på flera IP-konfigurationer - Azure-portal
titleSuffix: Azure Load Balancer
description: I den här artikeln kan du läsa mer om belastningsutjämning över primära och sekundära IP-konfigurationer med Azure-portalen.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 4bf74986462ecb2659505f8a1261b9b24aba3fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74077009"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Belastningsutjämning på flera IP-konfigurationer med hjälp av Azure-portalen

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [Powershell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)


I den här artikeln ska vi visa dig hur du använder Azure Load Balancer med flera IP-adresser på en sekundär nätverksenhetskontroll (NIC). Följande diagram illustrerar vårt scenario:

![Lastbalanseringsscenario](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

I vårt scenario använder vi följande konfiguration:

- Två virtuella datorer (VMs) som kör Windows.
- Varje virtuell dator har ett primärt och ett sekundärt nätverkskort.
- Varje sekundärt nätverkskort har två IP-konfigurationer.
- Varje virtuell dator är värd för två webbplatser: contoso.com och fabrikam.com.
- Varje webbplats är bunden till en IP-konfiguration på det sekundära nätverkskortet.
- Azure Load Balancer används för att exponera två frontend-IP-adresser, en för varje webbplats. Front-end-adresserna används för att distribuera trafik till respektive IP-konfiguration för varje webbplats.
- Samma portnummer används för både ip-adresser och IP-adresser för frontend-pool.

## <a name="prerequisites"></a>Krav

Vårt scenarioexempel förutsätter att du har en resursgrupp med namnet **contosofabrikam** som är konfigurerad enligt följande:

- Resursgruppen innehåller ett virtuellt nätverk med namnet **myVNet**.
- **MyVNet-nätverket** innehåller två virtuella datorer med namnet **VM1** och **VM2**.
- VM1 och VM2 är i samma tillgänglighetsuppsättning som heter **myAvailset**. 
- VM1 och VM2 har var och en ett primärt nätverkskort med namnet **VM1NIC1** respektive **VM2NIC1.** 
- VM1 och VM2 har var och en ett sekundärt nätverkskort med namnet **VM1NIC2** respektive **VM2NIC2.**

Mer information om hur du skapar virtuella datorer med flera nätverkskort finns i [Skapa en virtuell dator med flera nätverkskort med powershell](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Utföra belastningsutjämning på flera IP-konfigurationer

Slutför följande steg för att uppnå det scenario som beskrivs i den här artikeln.

### <a name="step-1-configure-the-secondary-nics"></a>Steg 1: Konfigurera de sekundära nätverkskorten

För varje virtuell dator i det virtuella nätverket lägger du till IP-konfigurationen för det sekundära nätverkskortet:  

1. Bläddra till Azure-portalen: https://portal.azure.com. Logga in med ditt Azure-konto.

2. Markera ikonen **Resursgrupp** längst upp till vänster på skärmen. Välj sedan den resursgrupp där dina virtuella datorer finns (till exempel **contosofabrikam**). I fönstret **Resursgrupper** visas alla resurser och nätverkskort för de virtuella datorerna.

3. För det sekundära nätverkskortet för varje virtuell dator lägger du till IP-konfigurationen:

    1. Välj det sekundära nätverkskort som du vill konfigurera.
    
    2. Välj **IP-konfigurationer**. Välj **Lägg till**i nästa fönsterruta längst upp.

    3. Lägg till en andra IP-konfiguration i nätverkskortet under **Lägg till IP-konfiguration:** 

        1. Ange ett namn för den sekundära IP-konfigurationen. (För VM1 och VM2 namnger du till exempel IP-konfigurationen **VM1NIC2-ipconfig2** respektive **VM2NIC2-ipconfig2.)**

        2. För inställningen **Privat IP-adress**, **Allokering** väljer du **Statisk**.

        3. Välj **OK**.

När den andra IP-konfigurationen för det sekundära nätverkskortet är klar visas den under **IP-konfigurationsinställningarna** för det angivna nätverkskortet.

### <a name="step-2-create-the-load-balancer"></a>Steg 2: Skapa lastbalanseraren

Skapa din belastningsutjämnare för konfigurationen:

1. Bläddra till Azure-portalen: https://portal.azure.com. Logga in med ditt Azure-konto.

2. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverksbelastningsutjämningsutjämnare****Networking** > . Välj sedan **Skapa**.

3. Under **Skapa belastningsutjämnare**skriver du ett namn på lastbalanseraren. I det här fallet använder vi namnet **mylb**.

4. Skapa en ny offentlig IP-adress som kallas **PublicIP1**under **Offentlig IP-adress.**

5. Under **Resursgrupp**väljer du den befintliga resursgruppen för dina virtuella datorer (till exempel **contosofabrikam**). Välj den plats som lastjämningsutjämnaren ska distribueras till och välj sedan **OK**.

Belastningsutjämnaren börjar distribuera. Distributionen kan ta några minuter att slutföra. När distributionen är klar visas belastningsutjämnaren som en resurs i resursgruppen.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Steg 3: Konfigurera IP-poolen i fronten

Konfigurera IP-poolen för varje webbplats (contoso.com och fabrikam.com) på lastbalansen:

1. Välj **Fler tjänster**i portalen . Skriv **offentlig IP-adress** i filterrutan och välj sedan **Offentliga IP-adresser**. Välj **Lägg till**i nästa fönsterruta längst upp.

2. Konfigurera två offentliga IP-adresser **(PublicIP1** och **PublicIP2)** för båda webbplatserna (contoso.com och fabrikam.com):

   1. Skriv ett namn på frontend-IP-adressen.

   2. För **Resursgrupp**väljer du den befintliga resursgruppen för dina virtuella datorer (till exempel **contosofabrikam**).

   3. För **Plats**väljer du samma plats som de virtuella datorerna.

   4. Välj **OK**.

      När de offentliga IP-adresserna har skapats visas de under de **offentliga IP-adresserna.**

3. <a name="step3-3"></a>Välj **Fler tjänster**i portalen . Skriv **belastningsutjämnaren i** filterrutan och välj sedan **Belastningsutjämnare**. 

4. Välj den belastningsutjämnare **(mylb)** som du vill lägga till ip-poolen i frontend-poolen.

5. Under **Inställningar**väljer du **Frontend IP-konfiguration**. Välj **Lägg till**i nästa fönsterruta längst upp.

6. Skriv ett namn på frontend-IP-adressen (till exempel **contosofe** eller **fabrikamfe**).

7. <a name="step3-7"></a>Välj **IP-adress**. Under **Välj offentlig IP-adress**väljer du IP-adress för frontend **(PublicIP1** eller **PublicIP2**).

8. Skapa den andra IP-adressen för frontend-talet genom att upprepa <a href="#step3-3">steg 3</a> till och med <a href="#step3-7">steg 7</a> i det här avsnittet.

När frontend-poolen har konfigurerats visas IP-adresserna under **ip-konfigurationsinställningarna för belastningsutjämnare frontend.** 
    
### <a name="step-4-configure-the-back-end-pool"></a>Steg 4: Konfigurera backend-poolen

För varje webbplats (contoso.com och fabrikam.com) konfigurerar du backend-adresspoolen på lastbalansen:
        
1. Välj **Fler tjänster**i portalen . Skriv **belastningsutjämnaren i** filterrutan och välj sedan **Belastningsutjämnare**.

2. Välj den belastningsutjämnare **(mylb)** som du vill lägga till backend-poolen i.

3. Under **Inställningar**väljer du **Backend-pooler**. Skriv ett namn på backend-poolen (till exempel **contosopool** eller **fabrikampool**). Välj **Lägg till**i nästa fönsterruta längst upp. 

4. Välj **Tillgänglighetsuppsättning**för **kopplad till**.

5. För **tillgänglighetsuppsättning**väljer du **myAvailset**.

6. Lägg till IP-konfigurationerna för målnätverket för båda virtuella datorerna: 

    ![Konfigurera backend-pooler för belastningsutjämnare](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. För **virtuell måldator**väljer du den virtuella datorn som du vill lägga till i backend-poolen (till exempel **VM1** eller **VM2**).

    2. För **Nätverks-IP-konfiguration**väljer du IP-konfigurationen för det sekundära nätverkskortet för den virtuella datorn som du valde i föregående steg (till exempel **VM1NIC2-ipconfig2** eller **VM2NIC2-ipconfig2**).

7. Välj **OK**.

När backend-poolen har konfigurerats visas adresserna under **serverdelspoolens serverdelspool** för belastningsutjämnare.

### <a name="step-5-configure-the-health-probe"></a>Steg 5: Konfigurera hälsoavsökningen

Konfigurera en hälsoavsökning för din belastningsutjämnare:

1. Välj **Fler tjänster**i portalen . Skriv **belastningsutjämnaren i** filterrutan och välj sedan **Belastningsutjämnare**.

2. Välj den belastningsutjämnare **(mylb)** som du vill lägga till hälsoavsökningen i.

3. Under **Inställningar**väljer du **Hälsoavsökning**. Välj **Lägg till**i nästa fönsterruta längst upp. 

4. Skriv ett namn för hälsoavsökningen (till exempel **HTTP**). Välj **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Steg 6: Konfigurera belastningsutjämningsregler

Konfigurera reglerna för belastningsutjämning för varje webbplats (contoso.com och fabrikam.com):
    
1. <a name="step6-1"></a>Under **Inställningar**väljer du **Belastningsutjämningsregler**. Välj **Lägg till**i nästa fönsterruta längst upp. 

2. För **Namn**skriver du ett namn för belastningsutjämningsregeln (till exempel **HTTPc** för contoso.com eller **HTTPf** för fabrikam.com).

3. För **IP-adress längst**fram väljer du den ip-adress som du tidigare skapade (till exempel **contosofe** eller **fabrikamfe**).

4. För **Port-** och **Backend-port**behåller du standardvärdet **80**.

5. För **Flytande IP (direkt serverretur)** väljer du **Inaktiverad**.

6. <a name="step6-6"></a>Välj **OK**.

7. Skapa den andra belastningsutjämnarens regel genom att upprepa <a href="#step6-1">steg 1</a> till och med <a href="#step6-6">steg 6</a> i det här avsnittet.

När reglerna har konfigurerats visas de under inställningarna för **belastningsutjämningsreglerna.**

### <a name="step-7-configure-dns-records"></a>Steg 7: Konfigurera DNS-poster

Som det sista steget konfigurerar du DNS-resursposterna så att de pekar på respektive front-end IP-adresser för din belastningsutjämnare. Du kan vara värd för dina domäner i Azure DNS. Mer information om hur du använder Azure DNS med belastningsutjämnare finns i [Använda Azure DNS med andra Azure-tjänster](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Nästa steg
- Läs mer om hur du kombinerar belastningsutjämningstjänster i Azure [med hjälp av belastningsutjämningstjänster i Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Lär dig hur du kan använda olika typer av loggar för att hantera och felsöka belastningsutjämnare i [Azure Monitor-loggar för Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
