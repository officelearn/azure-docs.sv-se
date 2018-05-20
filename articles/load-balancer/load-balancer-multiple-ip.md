---
title: Belastningsutjämning på flera IP-konfigurationer i Azure | Microsoft Docs
description: Belastningsutjämning mellan primära och sekundära IP-konfigurationer.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 2fbf7bd736a59cbcd6231fa8325a084f54927467
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Belastningsutjämning på flera IP-konfigurationer med hjälp av Azure portal

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)


I den här artikeln är det dags att visa dig hur du använder Azure-belastningsutjämnaren med flera IP-adresser på en sekundär nätverksgränssnitt (NIC). Följande diagram illustrerar vårt scenario:

![Belastningsutjämningsscenario](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

I vårt scenario, ska du använda följande konfiguration:

- Två virtuella datorer (VM) som kör Windows.
- Varje virtuell dator har en primär och sekundär NIC.
- Varje sekundär nätverkskort har två IP-konfigurationer.
- Varje virtuell värd två webbplatser: contoso.com och fabrikam.com.
- Varje webbplats är bunden till en IP-konfiguration på sekundära nätverkskortet.
- Azure belastningsutjämnare används för att exponera två frontend IP-adresser, en för varje webbplats. Frontend-adresser används för att distribuera trafiken till respektive IP-konfigurationen för varje webbplats.
- Samma portnummer används för både frontend och backend-adresspool IP-adresser.

## <a name="prerequisites"></a>Förutsättningar

Vårt Scenarioexempel förutsätter att du har en resursgrupp med namnet **contosofabrikam** som konfigureras på följande sätt:

- Resursgruppens namn innehåller ett virtuellt nätverk med namnet **myVNet**.
- Den **myVNet** nätverk innehåller två virtuella datorer med namnet **VM1** och **VM2**.
- VM1 och VM2 finns i samma tillgänglighetsuppsättning namngivna **myAvailset**. 
- VM1 och VM2 ha en primär nätverkskort med namnet **VM1NIC1** och **VM2NIC1**respektive. 
- VM1 och VM2 har ett sekundärt nätverkskort med namnet **VM1NIC2** och **VM2NIC2**respektive.

Mer information om hur du skapar virtuella datorer med flera nätverkskort finns [skapa en virtuell dator med flera nätverkskort med hjälp av PowerShell](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Utföra belastningsutjämning på flera IP-konfigurationer

Utför följande steg för att uppnå det scenario som beskrivs i den här artikeln.

### <a name="step-1-configure-the-secondary-nics"></a>Steg 1: Konfigurera sekundärt nätverkskort

Lägg till IP-konfiguration för det sekundära nätverkskortet för varje virtuell dator i ditt virtuella nätverk:  

1. Bläddra till den Azure-portalen: http://portal.azure.com. Logga in med ditt Azure-konto.

2. I det övre vänstra hörnet på skärmen, väljer du den **resursgruppen** ikon. Välj resursgruppen där din virtuella dator finns (till exempel **contosofabrikam**). Den **resursgrupper** fönstret visar alla resurser och nätverkskort för virtuella datorer.

3. Lägg till IP-konfigurationen för det sekundära nätverkskortet på varje virtuell dator:

    1. Välj det sekundära nätverkskortet som du vill konfigurera.
    
    2. Välj **IP-konfigurationer**. Välj i rutan nästa längst upp, **Lägg till**.

    3. Under **lägga till IP-konfigurationer**, lägga till en andra IP-konfiguration till NIC: 

        1. Ange ett namn för den sekundära IP-konfigurationen. (Till exempel kalla IP-konfigurationen för VM1 och VM2 **VM1NIC2 ipconfig2** och **VM2NIC2 ipconfig2**respektive.)

        2. För den **privata IP-adressen**, **allokering** inställningen genom att markera **statiska**.

        3. Välj **OK**.

Efter den andra IP-konfigurationen för det sekundära nätverkskortet är klar visas den **IP-konfigurationer** inställningar för det angivna nätverkskortet.

### <a name="step-2-create-the-load-balancer"></a>Steg 2: Skapa belastningsutjämnaren

Skapa din belastningsutjämnare för konfigurationen:

1. Bläddra till den Azure-portalen: http://portal.azure.com. Logga in med ditt Azure-konto.

2. I det övre vänstra hörnet på skärmen, Välj **skapar du en resurs** > **nätverk** > **belastningsutjämnaren**. Välj därefter **skapa**.

3. Under **skapa belastningsutjämnaren**, Skriv ett namn för din belastningsutjämnare. I det här scenariot använder vi namnet **mylb**.

4. Under **offentliga IP-adressen**, skapa en ny offentlig IP-adress kallas **PublicIP1**.

5. Under **resursgruppen**, Välj den befintliga resursgruppen för dina virtuella datorer (till exempel **contosofabrikam**). Välj plats för att distribuera en belastningsutjämnare och välj sedan **OK**.

Belastningsutjämnaren börjar distribuera. Distributionen kan ta några minuter att slutföra. När distributionen är klar visas belastningsutjämnaren som en resurs i resursgruppen.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Steg 3: Konfigurera frontend IP-adresspool

Konfigurera din belastningsutjämnare frontend IP-adresspool för varje webbplats (contoso.com och fabrikam.com):

1. I portalen, Välj **fler tjänster**. Skriv i filterrutan **offentliga IP-adressen** och välj sedan **offentliga IP-adresser**. Välj i rutan nästa längst upp, **Lägg till**.

2. Konfigurera två offentliga IP-adresser (**PublicIP1** och **PublicIP2**) för båda webbplatserna (contoso.com och fabrikam.com):

    1. Ange ett namn för din frontend IP-adress.

    2. För **resursgruppen**, Välj den befintliga resursgruppen för dina virtuella datorer (till exempel **contosofabrikam**).

    3. För **plats**, välj platsen som de virtuella datorerna.

    4. Välj **OK**.

    När de offentliga IP-adresserna har skapats visas de det **offentliga IP-Adressen** adresser.

3. <a name="step3-3"></a>I portalen, Välj **fler tjänster**. Skriv i filterrutan **belastningsutjämnare** och välj sedan **belastningsutjämnare**. 

4. Välj belastningsutjämnaren (**mylb**) som du vill lägga till frontend IP-pool.

5. Under **inställningar**väljer **Frontend-IP-konfiguration**. Välj i rutan nästa längst upp, **Lägg till**.

6. Skriv ett namn för din frontend IP-adress (till exempel **contosofe** eller **fabrikamfe**).

7. <a name="step3-7"></a>Välj **IP-adress**. Under **Välj offentlig IP-adress**, Välj IP-adresserna för dina frontend (**PublicIP1** eller **PublicIP2**).

8. Skapa andra frontend IP-adressen genom att upprepa <a href="#step3-3">steg 3</a> via <a href="#step3-7">steg 7</a> i det här avsnittet.

När den frontend har konfigurerats, IP-adresser visas under din belastningsutjämnare **Frontend-IP-konfiguration** inställningar. 
    
### <a name="step-4-configure-the-back-end-pool"></a>Steg 4: Konfigurera backend-adresspool

Konfigurera backend-adresspool för varje webbplats (contoso.com och fabrikam.com) på din belastningsutjämnare:
        
1. I portalen, Välj **fler tjänster**. Skriv i filterrutan **belastningsutjämnare** och välj sedan **belastningsutjämnare**.

2. Välj belastningsutjämnaren (**mylb**) som du vill lägga till backend-pool.

3. Under **inställningar**väljer **Serverdelspooler**. Skriv ett namn för din serverdel pool (till exempel **contosopool** eller **fabrikampool**). Välj i rutan nästa längst upp, **Lägg till**. 

4. För **som är associerade med**väljer **tillgänglighetsuppsättning**.

5. För **tillgänglighetsuppsättning**väljer **myAvailset**.

6. Lägg till mål nätverket IP-konfigurationer för både virtuella datorer: 

    ![Konfigurera backend-adresspooler för belastningsutjämnare](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. För **virtuella måldatorn**, Välj den virtuella datorn som du vill lägga till backend-poolen (till exempel **VM1** eller **VM2**).

    2. För **nätverks-IP-konfiguration**, Välj IP-adresskonfigurationen för det sekundära nätverkskortet för den virtuella datorn som du valde i föregående steg (till exempel **VM1NIC2 ipconfig2** eller **VM2NIC2 ipconfig2** ).

7. Välj **OK**.

När du har konfigurerat backend-poolen, adresserna visas under din belastningsutjämnare **serverdelspool** inställningar.

### <a name="step-5-configure-the-health-probe"></a>Steg 5: Konfigurera hälsa avsökningen

Konfigurera en hälsoavsökningen för din belastningsutjämnare:

1. I portalen, Välj **fler tjänster**. Skriv i filterrutan **belastningsutjämnare** och välj sedan **belastningsutjämnare**.

2. Välj belastningsutjämnaren (**mylb**) som du vill lägga till hälsoavsökningen till.

3. Under **inställningar**väljer **hälsoavsökningen**. Välj i rutan nästa längst upp, **Lägg till**. 

4. Skriv ett namn för hälsoavsökningen (till exempel **HTTP**). Välj **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Steg 6: Konfigurera regler för belastningsutjämning

Konfigurera regler för belastningsutjämning för varje webbplats (contoso.com och fabrikam.com):
    
1. <a name="step6-1"></a>Under **inställningar**väljer ** belastningsutjämning regler **. Välj i rutan nästa längst upp, **Lägg till**. 

2. För **namn**, Skriv ett namn för regeln för belastningsutjämning (till exempel **HTTPc** för contoso.com, eller **HTTPf** för fabrikam.com).

3. För **Frontend-IP-adress**, Välj frontend IP-adressen som du skapade tidigare (till exempel **contosofe** eller **fabrikamfe**).

4. För **Port** och **serverdelsport**, Behåll standardvärdet **80**.

5. För **flytande IP (direkt serverreturnering)** väljer **aktiverad**.

6. <a name="step6-6"></a>Välj **OK**.

7. Skapa andra belastningsutjämningsregeln genom att upprepa <a href="#step6-1">steg 1</a> via <a href="#step6-6">steg 6</a> i det här avsnittet.

När reglerna har konfigurerats och de visas under din belastningsutjämnare **belastningsutjämningsregler** inställningar.

### <a name="step-7-configure-dns-records"></a>Steg 7: Konfigurera DNS-poster

Konfigurera din DNS-resursposter för att peka till respektive frontend IP-adresserna för dina belastningsutjämnaren som det sista steget. Du kan vara värd för dina domäner i Azure DNS. Mer information om hur du använder Azure DNS med belastningsutjämnaren finns [med hjälp av Azure DNS med andra Azure-tjänster](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du kombinerar belastningsutjämning för tjänster i Azure i [med belastningsutjämning tjänster i Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Lär dig hur du kan använda olika typer av loggar för att hantera och felsöka belastningsutjämnare i [logga analytics för Azure belastningsutjämnare](../load-balancer/load-balancer-monitor-log.md).
