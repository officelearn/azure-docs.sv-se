---
title: Konfigurera belastnings utjämning och utgående regler med hjälp av Azure Portal
titleSuffix: Azure Load Balancer
description: Den här artikeln visar hur du konfigurerar belastnings utjämning och utgående regler i Standard Load Balancer med hjälp av Azure Portal.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b75f49155991bfc71f788ad88f166c0bec281841
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77590018"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Konfigurera belastnings utjämning och utgående regler i Standard Load Balancer med hjälp av Azure Portal

Den här artikeln visar hur du konfigurerar utgående regler i Standard Load Balancer med hjälp av Azure Portal.  

Belastnings Utjämnings resursen innehåller två klient delar och deras associerade regler. Du har en klient del för inkommande trafik och en annan klient del för utgående trafik.  

Varje klient del refererar till en offentlig IP-adress. I det här scenariot skiljer sig den offentliga IP-adressen för inkommande trafik från adressen för utgående trafik.   Belastnings Utjämnings regeln tillhandahåller endast inkommande belastnings utjämning. Regeln för utgående trafik styr utgående Network Address Translation (NAT) för den virtuella datorn.  

Scenariot använder två server dels pooler: en för inkommande trafik och en för utgående trafik. Dessa pooler illustrerar kapaciteten och ger flexibilitet för scenariot.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Skapa en lastbalanserare

I det här avsnittet skapar du en belastningsutjämnare som ska belastningsutjämna virtuella datorer. Du kan skapa en offentlig belastningsutjämnare eller en intern belastningsutjämnare. När du skapar en offentlig belastningsutjämnare skapar du en ny offentlig IP-adress som är konfigurerad som klient del för belastningsutjämnaren. Klient delen får namnet **LoadBalancerFrontEnd** som standard.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs** > **nätverk** > **Load Balancer**.
2. På fliken **grundläggande** på sidan **skapa belastnings utjämning** anger eller väljer du följande information:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa ny** och skriv **myResourceGroupSLB** i text rutan.|
    | Name                   | **myLoadBalancer**                                   |
    | Region         | Välj **Europa, västra**.                                        |
    | Typ          | Välj **Offentlig**.                                        |
    | SKU           | Välj **standard** eller **Basic**. Microsoft rekommenderar standard för produktions arbets belastningar. |
    | Offentlig IP-adress | Välj **Skapa ny**. Om du har en befintlig offentlig IP-adress som du vill använda väljer du **Använd befintlig**.  Den befintliga offentliga IP-adressen måste vara **standard** -SKU.  Grundläggande offentliga IP-adresser är inte kompatibla med **standard** -SKU: n.  |
    | Namn på offentlig IP-adress              | Skriv **myPublicIP** i textrutan.|
    | Tillgänglighetszon | Välj **zon-redundant** för att skapa en elastisk Load Balancer. Om du vill skapa en zonindelade Load Balancer väljer du en speciell zon från 1, 2 eller 3 |

3. Acceptera standardinställningarna för resten av konfigurationen.
4. Välj **Granska + skapa**

    > [!IMPORTANT]
    > Resten av den här snabb starten förutsätter att **standard** -SKU väljs under urvals processen för SKU ovan.

5. På fliken **Granska och skapa** väljer du **skapa**.   

    ![Skapa en Standard Load Balancer](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Skapa resurser för lastbalansering

I det här avsnittet konfigurerar du inställningarna för lastbalanseraren för en serverdelsadresspool och en hälsoavsökning, och anger en regel för lastbalanseraren.

### <a name="create-a-backend-pool"></a>Skapa en serverdelspool

En backend-adresspool innehåller IP-adresserna för de virtuella nätverkskorten i backend-poolen. Skapa **myBackendPool** för backend-adresspoolen för att inkludera virtuella datorer för belastnings utjämning av Internet trafik.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och välj sedan **myLoadBalancer** i listan resurser.
2. Under **Inställningar**väljer du **backend-pooler**och väljer sedan **Lägg till**.
3. På sidan **Lägg till en server dels grupp** anger du **myBackendPool**som namn på din backend-pool. Välj sedan **Lägg till**.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

En hälso avsökning används för att övervaka appens status. Hälso avsökningen lägger till eller tar bort virtuella datorer från belastningsutjämnaren baserat på deras svar på hälso kontroller. Skapa en hälsoavsökning **myHealthProbe** så att du kan övervaka de virtuella datorernas hälsotillstånd.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och välj sedan **myLoadBalancer** i listan resurser.
2. Välj **hälso avsökningar**under **Inställningar**och välj sedan **Lägg till**.
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myHealthProbe**. |
    | Protokoll | Välj **http**. |
    | Port | Ange **80**.|
    | Intervall | Ange **15** som **intervall** i sekunder mellan avsöknings försök. |
    | Tröskelvärde för ej felfri | Välj **2** för antalet fel i **tröskeln** eller på varandra följande avsöknings fel som måste inträffa innan en virtuell dator betraktas som ohälsosam.|
    | | |
4. Välj **OK**.

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel
En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. 

Du definierar:
 - IP-konfiguration för klient delen för inkommande trafik.
 - Server delens IP-pool för att ta emot trafiken.
 - Käll-och mål port som krävs. 

I följande avsnitt skapar du en:
 - Belastnings Utjämnings regeln **myHTTPRule** för att lyssna på port 80.
 - Frontend- **LoadBalancerFrontEnd**.
 - **MyBackEndPool** för backend-adresspoolen använder också port 80. 

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och välj sedan **myLoadBalancer** i listan resurser.
2. Välj **belastnings Utjämnings regler**under **Inställningar**och välj sedan **Lägg till**.
3. Använd de här värdena för att konfigurera belastnings Utjämnings regeln:
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myHTTPRule**. |
    | Protokoll | Välj **TCP**. |
    | Port | Ange **80**.|
    | Backend-port | Ange **80**. |
    | Serverdelspool | Välj **myBackendPool**.|
    | Hälsoavsökning | Välj **myHealthProbe**. |
    | Skapa implicit utgående regler | Välj **Nej**. Vi skapar utgående regler i ett senare avsnitt med hjälp av en dedikerad offentlig IP-adress. |
4. Lämna resten av standardinställningarna och välj sedan **OK**.

## <a name="create-outbound-rule-configuration"></a>Skapa utgående regel konfiguration
Utgående regler för belastningsutjämnare konfigurerar utgående SNAT för virtuella datorer i backend-poolen. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Skapa en utgående offentlig IP-adress och klient del

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och välj sedan **myLoadBalancer** i listan resurser.

2. Under **Inställningar**väljer du **klient delens IP-konfiguration**och väljer sedan **Lägg till**.

3. Använd de här värdena för att konfigurera klient delens IP-konfiguration för utgående:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **LoadBalancerFrontEndOutbound**. |
    | IP-version | Välj **IPv4**. |
    | IP-typ | Välj **IP-adress**.|
    | Offentlig IP-adress | Välj **Skapa ny**. I **Lägg till en offentlig IP-adress**, anger du **myPublicIPOutbound**.  Välj **OK**. |

4. Välj **Lägg till**.

### <a name="create-an-outbound-backend-pool"></a>Skapa en utgående backend-pool

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och välj sedan **myLoadBalancer** i listan resurser.

2. Under **Inställningar**väljer du **backend-pooler**och väljer sedan **Lägg till**.

3. På sidan **Lägg till en server dels grupp** anger du **myBackendPoolOutbound**som namn på din backend-pool. Välj sedan **Lägg till**.

### <a name="create-outbound-rule"></a>Skapa utgående regel

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och välj sedan **myLoadBalancer** i listan resurser.

2. Välj **utgående regler**under **Inställningar**och välj sedan **Lägg till**.

3. Använd de här värdena för att konfigurera de utgående reglerna:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myOutboundRule**. |
    | IP-adress för klient del | Välj **LoadBalancerFrontEndOutbound**. |
    | Tids gräns för inaktivitet (minuter) | Flytta skjutreglaget till * * 15 minuter.|
    | TCP-återställning | Välj **Aktiverad**.|
    | Serverdelspool | Välj **myBackendPoolOutbound** |
    | Port tilldelning – > port tilldelning | Välj **manuellt Välj antalet utgående portar** |
    | Utgående portar-> Välj efter | Välj **portar per instans** |
    | Utgående portar-> portar per instans | Ange **10 000**. |

4. Välj **Lägg till**.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, lastbalanseraren och alla relaterade resurser när de inte längre behövs. Välj den resurs grupps **myResourceGroupSLB** som innehåller belastningsutjämnaren och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln:
 - Du har skapat en standard belastningsutjämnare.
 - Konfigurerade både inkommande och utgående trafik regler för belastningsutjämnare.
 - Konfigurerat en hälso avsökning för de virtuella datorerna i backend-poolen. 

Om du vill veta mer kan du fortsätta till [självstudierna för Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
