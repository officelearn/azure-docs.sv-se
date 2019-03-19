---
title: Belastningsutjämning på flera IP-konfigurationer - Azure-portalen
titlesuffix: Azure Load Balancer
description: Belastningsutjämning mellan primära och sekundära IP-konfigurationer.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0cf5aa45e1e8a28dfcdadac0ea32658e5993d06c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57856143"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Belastningsutjämning på flera IP-konfigurationer med hjälp av Azure-portalen

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)


I den här artikeln ska vi visar dig hur du använder Azure Load Balancer med flera IP-adresser på en sekundär nätverksgränssnitt (NIC). Följande diagram illustrerar vårt scenario:

![Lastbalanseringsscenario](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

I vårt scenario använder vi följande konfiguration:

- Två virtuella datorer (VM) som kör Windows.
- Varje virtuell dator har en primär och en sekundärt nätverkskort.
- Varje sekundär nätverkskort har två IP-konfigurationer.
- Varje virtuell dator är värd för två webbplatser: contoso.com och fabrikam.com.
- Varje webbplats är bunden till en IP-konfiguration på sekundärt nätverkskort.
- Azure belastningsutjämnare används för att exponera två frontend IP-adresser, en för varje webbplats. Frontend-adresser används för att distribuera trafik till respektive IP-konfigurationen för varje webbplats.
- Samma portnummer används för både frontend IP-adresser och IP-adresser för backend-poolen.

## <a name="prerequisites"></a>Förutsättningar

Vårt Scenarioexempel förutsätter att du har en resursgrupp med namnet **contosofabrikam** som har konfigurerats på följande sätt:

- Resursgruppen innehåller ett virtuellt nätverk med namnet **myVNet**.
- Den **myVNet** nätverk innehåller två virtuella datorer med namnet **VM1** och **VM2**.
- VM1 och VM2 finns i samma tillgänglighetsuppsättning namngivna **myAvailset**. 
- VM1 och VM2 har ett primärt nätverkskort med namnet **VM1NIC1** och **VM2NIC1**respektive. 
- VM1 och VM2 har ett sekundärt nätverkskort med namnet **VM1NIC2** och **VM2NIC2**respektive.

Mer information om hur du skapar virtuella datorer med flera nätverkskort finns i [skapa en virtuell dator med flera nätverkskort med hjälp av PowerShell](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Utföra belastningsutjämning på flera IP-konfigurationer

Utför följande steg för att uppnå det scenario som beskrivs i den här artikeln.

### <a name="step-1-configure-the-secondary-nics"></a>Steg 1: Konfigurera sekundära nätverkskort

Lägg till IP-konfiguration för det sekundära nätverkskortet för varje virtuell dator i det virtuella nätverket:  

1. Bläddra till Azure portal: https://portal.azure.com. Logga in med ditt Azure-konto.

2. I det övre vänstra hörnet på skärmen väljer du den **resursgrupp** ikon. Välj sedan den resursgrupp där dina virtuella datorer finns (till exempel **contosofabrikam**). Den **resursgrupper** rutan visar alla resurser och nätverkskort för virtuella datorer.

3. Lägg till IP-konfigurationen för det sekundära nätverkskortet för varje virtuell dator:

    1. Välj det sekundära nätverkskortet som du vill konfigurera.
    
    2. Välj **IP-konfigurationer**. I nästa ruta i den övre delen väljer **Lägg till**.

    3. Under **Lägg till IP-konfigurationer**, lägga till en sekundär IP-konfiguration till nätverkskortet: 

        1. Ange ett namn för den sekundära IP-konfigurationen. (Till exempel kalla IP-konfiguration för VM1 och VM2 **VM1NIC2 ipconfig2** och **VM2NIC2 ipconfig2**respektive.)

        2. För den **privat IP-adress**, **allokering** inställningen, Välj **statiska**.

        3. Välj **OK**.

Efter den andra IP-adresskonfigurationen för det sekundära nätverkskortet är klar, visas det under den **IP-konfigurationer** inställningar för det angivna nätverkskortet.

### <a name="step-2-create-the-load-balancer"></a>Steg 2: Skapa lastbalanseraren

Skapa en belastningsutjämnare för konfigurationen:

1. Bläddra till Azure portal: https://portal.azure.com. Logga in med ditt Azure-konto.

2. I det övre vänstra hörnet på skärmen väljer **skapa en resurs** > **nätverk** > **belastningsutjämnaren**. Välj sedan **skapa**.

3. Under **skapa belastningsutjämnare**, Skriv ett namn för belastningsutjämnaren. I det här scenariot använder vi namnet **mylb**.

4. Under **offentliga IP-adressen**, skapa en ny offentlig IP som kallas **PublicIP1**.

5. Under **resursgrupp**, Välj den befintliga resursgruppen för dina virtuella datorer (till exempel **contosofabrikam**). Välj plats för att distribuera belastningsutjämnaren till och välj sedan **OK**.

Belastningsutjämnaren börjar distribuera. Distributionen kan ta några minuter att slutföra. När distributionen är klar, visas belastningsutjämnaren som en resurs i resursgruppen.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Steg 3: Konfigurera IP-adresspoolen på klientsidan

Konfigurera IP-adresspoolen på klientsidan belastningsutjämnaren för varje webbplats (contoso.com och fabrikam.com):

1. I portalen, väljer **fler tjänster**. Skriv i rutan filtrera **offentliga IP-adressen** och välj sedan **offentliga IP-adresser**. I nästa ruta i den övre delen väljer **Lägg till**.

2. Konfigurera två offentliga IP-adresser (**PublicIP1** och **PublicIP2**) för båda webbplatserna (contoso.com och fabrikam.com):

   1. Skriv ett namn för din frontend IP-adress.

   2. För **resursgrupp**, Välj den befintliga resursgruppen för dina virtuella datorer (till exempel **contosofabrikam**).

   3. För **plats**, välja samma plats som de virtuella datorerna.

   4. Välj **OK**.

      När de offentliga IP-adresserna har skapats visas de under den **offentlig IP-adress** adresser.

3. <a name="step3-3"></a>I portalen, väljer **fler tjänster**. Skriv i rutan filtrera **belastningsutjämnare** och välj sedan **Load Balancer**. 

4. Välj belastningsutjämnaren (**mylb**) som du vill lägga till IP-adresspoolen på klientsidan till.

5. Under **inställningar**väljer **Frontend-IP-konfiguration**. I nästa ruta i den övre delen väljer **Lägg till**.

6. Skriv ett namn för din frontend IP-adress (till exempel **contosofe** eller **fabrikamfe**).

7. <a name="step3-7"></a>Välj **IP-adress**. Under **Välj offentlig IP-adress**, Välj IP-adresser för din klient (**PublicIP1** eller **PublicIP2**).

8. Skapa andra frontend IP-adressen genom att upprepa <a href="#step3-3">steg 3</a> via <a href="#step3-7">steg 7</a> i det här avsnittet.

När-adresspoolen på klientsidan har konfigurerats kan IP-adresser visas under belastningsutjämnaren **Frontend-IP-konfiguration** inställningar. 
    
### <a name="step-4-configure-the-back-end-pool"></a>Steg 4: Konfigurera backend-pool

Konfigurera backend-adresspoolen belastningsutjämnaren för varje webbplats (contoso.com och fabrikam.com):
        
1. I portalen, väljer **fler tjänster**. Skriv i rutan filtrera **belastningsutjämnare** och välj sedan **Load Balancer**.

2. Välj belastningsutjämnaren (**mylb**) som du vill lägga till backend-poolen till.

3. Under **inställningar**väljer **Serverdelspooler**. Skriv ett namn för backend-poolen (till exempel **contosopool** eller **fabrikampool**). I nästa ruta i den övre delen väljer **Lägg till**. 

4. För **som är kopplad till**väljer **tillgänglighetsuppsättning**.

5. För **tillgänglighetsuppsättning**väljer **myAvailset**.

6. Lägg till målnätverkets IP-konfigurationer för både virtuella datorer: 

    ![Konfigurera backend-adresspooler för belastningsutjämnare](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. För **virtuella måldatorn**, Välj den virtuella datorn som du vill lägga till i backend-poolen (till exempel **VM1** eller **VM2**).

    2. För **nätverks-IP-konfiguration**, Välj IP-adresskonfigurationen för det sekundära nätverkskortet för den virtuella datorn som du valde i föregående steg (till exempel **VM1NIC2 ipconfig2** eller **VM2NIC2 ipconfig2** ).

7. Välj **OK**.

När backend poolen har konfigurerats kan adresserna som visas under belastningsutjämnaren **serverdelspool** inställningar.

### <a name="step-5-configure-the-health-probe"></a>Steg 5: Konfigurera hälsoavsökningen

Konfigurera en hälsoavsökning för belastningsutjämnaren:

1. I portalen, väljer **fler tjänster**. Skriv i rutan filtrera **belastningsutjämnare** och välj sedan **Load Balancer**.

2. Välj belastningsutjämnaren (**mylb**) som du vill lägga till hälsoavsökningen till.

3. Under **inställningar**väljer **hälsoavsökning**. I nästa ruta i den övre delen väljer **Lägg till**. 

4. Skriv ett namn för hälsoavsökningen (till exempel **HTTP**). Välj **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Steg 6: Konfigurera belastningsutjämningsregler

Konfigurera regler för belastningsutjämning för varje webbplats (contoso.com och fabrikam.com):
    
1. <a name="step6-1"></a>Under **inställningar**väljer **belastningsutjämningsregler**. I nästa ruta i den övre delen väljer **Lägg till**. 

2. För **namn**, Skriv ett namn för regeln för belastningsutjämning (till exempel **HTTPc** för contoso.com, eller **HTTPf** för fabrikam.com).

3. För **Frontend-IP-adress**, välja frontend IP-adressen som du skapade tidigare (till exempel **contosofe** eller **fabrikamfe**).

4. För **Port** och **serverdelsport**, Behåll standardvärdet **80**.

5. För **flytande IP (direkt serverreturnering)** väljer **inaktiverad**.

6. <a name="step6-6"></a>Välj **OK**.

7. Skapa andra belastningsutjämningsregeln genom att upprepa <a href="#step6-1">steg 1</a> via <a href="#step6-6">steg 6</a> i det här avsnittet.

När reglerna har konfigurerats, visas de under belastningsutjämnaren **belastningsutjämningsregler** inställningar.

### <a name="step-7-configure-dns-records"></a>Steg 7: Konfigurera DNS-poster

Konfigurera DNS-resursposter så att den pekar till respektive frontend IP-adresserna för belastningsutjämnaren som det sista steget. Du kan vara värd för dina domäner i Azure DNS. Läs mer om hur du använder Azure DNS med belastningsutjämnare, [med hjälp av Azure DNS med andra Azure-tjänster](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du kombinerar belastningsutjämning i Azure i [med tjänster för belastningsutjämning i Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Lär dig hur du kan använda olika typer av loggar för att hantera och felsöka belastningsutjämnare i [Azure Monitor-loggarna för Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
