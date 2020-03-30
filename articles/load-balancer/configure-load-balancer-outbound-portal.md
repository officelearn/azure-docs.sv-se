---
title: Konfigurera belastningsutjämning och utgående regler med hjälp av Azure-portalen
titleSuffix: Azure Load Balancer
description: Den här artikeln visar hur du konfigurerar belastningsutjämning och utgående regler i Standard belastningsutjämnare med hjälp av Azure-portalen.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b75f49155991bfc71f788ad88f166c0bec281841
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590018"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Konfigurera belastningsutjämning och utgående regler i Standard load Balancer med hjälp av Azure-portalen

Den här artikeln visar hur du konfigurerar utgående regler i Standard Load Balancer med hjälp av Azure-portalen.  

Belastningsutjämnarresursen innehåller två främre ändar och tillhörande regler. Du har en frontend för inkommande trafik och en annan frontend för utgående trafik.  

Varje klientreferens refererar till en offentlig IP-adress. I det här fallet skiljer sig den offentliga IP-adressen för inkommande trafik från adressen för utgående trafik.   Belastningsutjämningsregeln ger endast ingående belastningsutjämning. Den utgående regeln styr den utgående nätverksadressöversättningen (NAT) för den virtuella datorn.  

Scenariot använder två backend-pooler: en för inkommande trafik och en för utgående trafik. Dessa pooler illustrerar kapacitet och ger flexibilitet för scenariot.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Skapa en lastbalanserare

I det här avsnittet skapar du en belastningsutjämnare som kommer att belastningsutjämna virtuella datorer. Du kan skapa en offentlig belastningsutjämnare eller en intern belastningsutjämnare. När du skapar en offentlig belastningsutjämnare skapar du en ny offentlig IP-adress som har konfigurerats som klientdel för belastningsutjämnaren. Frontend kommer att heta **LoadBalancerFrontEnd** som standard.

1. Välj **Skapa en resurs** > **Nätverksbelastningsutjämnare****Networking** > längst upp till vänster på skärmen .
2. På fliken Grunderna på sidan **Skapa belastningsutjämnare** anger eller väljer du följande information: **Basics**

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa nytt** och skriv **myResourceGroupSLB** i textrutan.|
    | Namn                   | **myLoadBalancer**                                   |
    | Region         | Välj **Europa, västra**.                                        |
    | Typ          | Välj **Offentlig**.                                        |
    | SKU           | Välj **Standard** eller **Basic**. Microsoft rekommenderar Standard för produktionsarbetsbelastningar. |
    | Offentlig IP-adress | Välj **Skapa ny**. Om du har en befintlig offentlig IP som du vill använda väljer du **Använd befintlig**.  Befintlig offentlig IP måste vara **Standard** SKU.  Grundläggande offentliga IPs är inte kompatibla med **Standard** SKU belastningsutjämnare.  |
    | Namn på offentlig IP-adress              | Skriv **myPublicIP** i textrutan.|
    | Tillgänglighetszon | Välj **Zonundant** om du vill skapa en flexibel belastningsutjämningsfaktor. Om du vill skapa en zonindelningsbelastningsutdelningsapparat väljer du en viss zon från 1, 2 eller 3 |

3. Acceptera standardinställningarna för resten av konfigurationen.
4. Välj **Granska + skapa**

    > [!IMPORTANT]
    > Resten av den här snabbstarten förutsätter att **Standard** SKU väljs under SKU-urvalsprocessen ovan.

5. Välj **Skapa**på fliken **Granska + skapa** .   

    ![Skapa en Standard Load Balancer](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Skapa resurser för lastbalansering

I det här avsnittet konfigurerar du inställningarna för lastbalanseraren för en serverdelsadresspool och en hälsoavsökning, och anger en regel för lastbalanseraren.

### <a name="create-a-backend-pool"></a>Skapa en serverdelspool

En servergrupp innehåller IP-adresserna för de virtuella nätverkskorten i serverdapoolen. Skapa serverdelsadresspoolen **myBackendPool** för att inkludera virtuella datorer för belastningsutjämning av internettrafik.

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och välj sedan **myLoadBalancer** i resurslistan.
2. Under **Inställningar**väljer du **Backend-pooler**och väljer sedan **Lägg till**.
3. Skriv **myBackendPool**för namn på sidan **Lägg till en backend-pool** och välj sedan **Lägg till**.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

En hälsoavsökning används för att övervaka appens status. Hälsoavsökningen lägger till eller tar bort virtuella datorer från belastningsutjämnaren baserat på deras svar på hälsokontroller. Skapa en hälsoavsökning **myHealthProbe** så att du kan övervaka de virtuella datorernas hälsotillstånd.

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och välj sedan **myLoadBalancer** i resurslistan.
2. Under **Inställningar**väljer du **Hälsoavsökningar**och väljer sedan **Lägg till**.
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myHealthProbe**. |
    | Protokoll | Välj **HTTP**. |
    | Port | Skriv in **80**.|
    | Intervall | Ange **15** för antal **intervall** i sekunder mellan avsökningsförsök. |
    | Felfritt tröskelvärde | Välj **2** för antal **felaktiga tröskelvärden** eller på varandra följande avsökningsfel som måste inträffa innan en virtuell dator anses vara fel.|
    | | |
4. Välj **OK**.

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel
En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. 

Du definierar:
 - Frontend IP-konfiguration för inkommande trafik.
 - Den bakre IP-poolen för att ta emot trafiken.
 - Den nödvändiga käll- och målporten. 

I följande avsnitt skapar du en:
 - Belastningsutjämnare regel **minHTTPRule** för att lyssna på port 80.
 - Frontend **LoadBalancerFrontEnd**.
 - Backend adress pool **myBackEndPool** också använda port 80. 

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och välj sedan **myLoadBalancer** i resurslistan.
2. Under **Inställningar**väljer du **Belastningsutjämningsregler**och väljer sedan **Lägg till**.
3. Använd dessa värden för att konfigurera belastningsutjämningsregeln:
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **minHTTPRule**. |
    | Protokoll | Välj **TCP**. |
    | Port | Skriv in **80**.|
    | Backend-port | Skriv in **80**. |
    | Serverdelspool | Välj **myBackendPool**.|
    | Hälsoavsökning | Välj **myHealthProbe**. |
    | Skapa implicita utgående regler | Välj **Nej**. Vi skapar utgående regler i ett senare avsnitt med hjälp av en dedikerad offentlig IP. |
4. Lämna resten av standardinställningarna och välj sedan **OK**.

## <a name="create-outbound-rule-configuration"></a>Skapa utgående regelkonfiguration
Utgående regler för belastningsutjämnare konfigurerar utgående SNAT för virtuella datorer i serverdapoolen. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Skapa en utgående offentlig IP-adress och klientdel

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och välj sedan **myLoadBalancer** i resurslistan.

2. Under **Inställningar**väljer du **Frontend IP-konfiguration**och väljer sedan **Lägg till**.

3. Använd dessa värden för att konfigurera ip-konfigurationen för klientdel för utgående:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **LoadBalancerFrontEndOutbound**. |
    | IP-version | Välj **IPv4**. |
    | IP-typ | Välj **IP-adress**.|
    | Offentlig IP-adress | Välj **Skapa ny**. Ange **myPublicIPOutbound**i **Lägg till en offentlig IP-adress**.  Välj **OK**. |

4. Välj **Lägg till**.

### <a name="create-an-outbound-backend-pool"></a>Skapa en utgående backend-pool

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och välj sedan **myLoadBalancer** i resurslistan.

2. Under **Inställningar**väljer du **Backend-pooler**och väljer sedan **Lägg till**.

3. Skriv **myBackendPoolOutbound**som namn på sidan **Lägg till en backend-pool** och välj sedan **Lägg till**.

### <a name="create-outbound-rule"></a>Skapa utgående regel

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och välj sedan **myLoadBalancer** i resurslistan.

2. Under **Inställningar**väljer du **Utgående regler**och väljer sedan Lägg **till**.

3. Använd dessa värden för att konfigurera utgående regler:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myOutboundRule**. |
    | IP-adress för klientdel | Välj **LoadBalancerFrontEndOutbound**. |
    | Tidsgränsen för inaktiv tid (minuter) | Flytta skjutreglaget till **15 minuter.|
    | TCP-återställning | Välj **Aktiverad**.|
    | Serverdelspool | Välj **myBackendPoolOutbound** |
    | Portallokering -> portallokering | Välj **Manuellt välja antal utgående portar** |
    | Utgående portar -> Välj av | Välj **portar per instans** |
    | Utgående portar -> portar per instans | Ange **10 000**. |

4. Välj **Lägg till**.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, lastbalanseraren och alla relaterade resurser när de inte längre behövs. Markera resursgruppen **myResourceGroupSLB** som innehåller belastningsutjämnaren och välj sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln:
 - Du har skapat en standardbelastningsutjämnare.
 - Konfigurerade trafikregler för både inkommande och utgående belastningsutjämnare.
 - Konfigurerade en hälsoavsökning för de virtuella datorerna i backend-poolen. 

Om du vill veta mer fortsätter du till [självstudierna för Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
