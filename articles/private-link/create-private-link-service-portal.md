---
title: Snabb start – skapa en privat länk-tjänst med hjälp av Azure Portal
titlesuffix: Azure Private Link
description: Lär dig hur du skapar en privat länk-tjänst med hjälp av Azure Portal i den här snabb starten
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: 4dd27d3fb4321c2eecf222136376bfd4b49790bb
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027778"
---
# <a name="quickstart-create-a-private-link-service-using-the-azure-portal"></a>Snabb start: skapa en privat länk-tjänst med hjälp av Azure Portal

Tjänsten Azure Private Link är referensen till din egen tjänst som drivs av en privat Azure-länk. Tjänsten eller resursen som körs bakom Azure Load Balancer kan aktive ras för åtkomst till privat länk. Konsumenter av tjänsten kan komma åt tjänsten privat från sina egna virtuella nätverk. I den här snabb starten får du lära dig hur du skapar en privat länk-tjänst med hjälp av Azure Portal.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Skapa en intern belastningsutjämnare

Skapa först ett virtuellt nätverk och sedan en intern belastningsutjämnare som ska användas med tjänsten Azure Private Link.

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet ska du skapa en Virtual Network och under nätet som är värd för belastningsutjämnaren som används för att få åtkomst till din privata länk tjänst.


1. Uppe till vänster i portalen väljer du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.
   
1. I fönsterrutan **Skapa virtuellt nätverk** skriver eller väljer du dessa värden:
   
   - **Namn**: Skriv **MyVNet**.
   - **ResourceGroup**: Välj **Skapa ny**, ange **MyResourceGroupLB** och välj **OK**. 
   - **Undernät** > **Namn**: Skriv **MyBackendSubnet**.
   
1. Välj **Skapa**.

   ![Skapa ett virtuellt nätverk](../load-balancer/media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-a-standard-load-balancer"></a>Skapa en standardlastbalanserare

Skapa en intern standard belastnings utjämning med hjälp av portalen. Det namn och den IP-adress som du skapar konfigureras automatiskt som lastbalanserarens klientdel.

1. Längst upp till vänster i portalen väljer du **Skapa en resurs** > **Netverk** > **Lastbalanserare**.
   
2. På fliken **Grundläggande inställningar** på sidan **Skapa lastbalanserare** anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan **Granska + skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj *MyResourceGroupLB* i list rutan.|
    | Namn                   | *myLoadBalancer*                                   |
    | Region         | Välj **USA, östra 2**.                                        |
    | Typ          | Välj **intern**.                                        |
    | SKU           | Välj **standard**.                          |
    | Virtuellt nätverk           | Välj *MyVNet*.                          |    
    | Tilldelning av IP-adress              | Välj **Statisk**.   |
    | Privat IP-adress|Ange en adress i adress utrymmet för ditt virtuella nätverk och undernät, till exempel *10.3.0.7*.  |

3. På fliken **Granska + skapa** klickar du på **Skapa**. 
   

### <a name="create-standard-load-balancer-resources"></a>Skapa standard resurser för belastnings utjämning

I det här avsnittet konfigurerar du inställningar för lastbalanseraren för en serverdelsadresspool och en hälsoavsökning samt anger regler för lastbalanseraren.

#### <a name="create-a-backend-pool"></a>Skapa en backend-pool

För att distribuera trafik till dina resurser innehåller en backend-adresspool IP-adresserna för de virtuella nätverkskorten som är anslutna till Load Balancer. Skapa *myBackendPool* för backend-adresspoolen för att inkludera resurser för belastnings Utjämnings trafik.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och välj sedan **myLoadBalancer** i listan resurser.
2. Under **Inställningar**väljer du **backend-pooler**och väljer sedan **Lägg till**.
3. På sidan **Lägg till en server dels grupp** anger du *myBackendPool*som namn på din backend-pool. Välj sedan **Lägg till**.



#### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Om du vill tillåta att belastningsutjämnaren övervakar resurs status använder du en hälso avsökning. Hälso avsökningen lägger till eller tar bort resurser dynamiskt från belastnings Utjämnings rotationen baserat på deras svar på hälso kontroller. 

**Så här skapar du en hälso avsökning för att övervaka hälso tillståndet för resurserna:**

1. Välj **Alla resurser** på den vänstra menyn och välj sedan **MyLoadBalancer** i resurslistan.
   
1. Under **Inställningar** väljer du **Hälsoavsökningar** och sedan **Lägg till**.
   
1. På sidan **Lägg till en hälsoavsökning**  skriver eller väljer du följande värden:
   
   - **Namn**: Skriv **MyHealthProbe**.
   - **Protokoll**: list rutan och välj **TCP**. 
   - **Port**: Skriv **80**. 
   - **Intervall**: Skriv **15**. Intervallet är antalet sekunder mellan avsökningsförsök.
   - **Tröskelvärde för Ej felfri**: Skriv **2**. Det här värdet är det antal avsökningsfel i följd som ska inträffa innan en virtuell dator anses vara felaktig.
   
1. Välj **OK**.

#### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En belastnings Utjämnings regel definierar hur trafiken distribueras till resurser. Regeln definierar IP-konfigurationen på klientdelen för inkommande trafik, serverdels-IP-poolen för att ta emot trafik samt nödvändiga käll- och målportar. 

Lastbalanserarregeln med namnet **MyLoadBalancerRule** avlyssnar port 80 i klientdelen **LoadBalancerFrontEnd**. Regeln skickar nätverkstrafik till serverdelsadresspoolen **MyBackendPool**, även det med port 80. 

**Så här skapar du lastbalanserarregeln:**

1. Välj **Alla resurser** på den vänstra menyn och välj sedan **MyLoadBalancer** i resurslistan.
   
1. Under **Inställningar**väljer du **regler för belastnings utjämning**och väljer sedan **Lägg till**.
   
1. På sidan **Lägg till belastnings Utjämnings regel** skriver eller väljer du följande värden, om de inte redan finns:
   
   - **Namn**: Skriv **MyLoadBalancerRule**.
   - **Klientdels-IP-adress:** Skriv **LoadBalancerFrontend** om det inte finns.
   - **Protokoll**: Välj **TCP**.
   - **Port**: Skriv **80**.
   - **Serverdelsport**: Skriv **80**.
   - **Serverdelspool**: Välj **MyBackendPool**.
   - **Hälsoavsökning**: Välj **MyHealthProbe**. 
   
1. Välj **OK**.
   
## <a name="create-a-private-link-service"></a>Skapa en privat länk-tjänst

I det här avsnittet ska du skapa en privat länk-tjänst bakom en standard belastningsutjämnare.

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs** > **nätverk** > **Private Link Center (för hands version)** . Du kan också söka i privat länk via portal sökning.

2. I **privat länk Center – översikt**, på alternativet att **"exponera din egen tjänst så att andra kan ansluta" väljer du** start.

3. I **skapa en privat länk tjänst – grunderna**anger eller väljer du den här informationen:

    | Inställning           | Värde                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | **Projekt information:**  |                                                                              |
    | Prenumeration      | Välj din prenumeration                                                     |
    | Resursgrupp    | Välj *MyResourceGroupLB*                                                     |
    | **INSTANS INFORMATION:** |                                                                              |
    | Namn              | Ange *myPrivateLinkService* |
    | Region            | Välj *USA, östra 2*                                                         |

4. Välj **Nästa: utgående inställningar**.

5. I **skapa en privat länk tjänst-utgående inställningar**anger eller väljer du den här informationen:


    | Inställning                           | Värde                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | Load Balancer                     | Välj *MyLoadBalancer*                                                           |
    | IP-adress för Load Balancer-klient | Välj IP-adressen för klient delen MyLoadBalancer                                |
    | Käll-NAT virtuellt nätverk        | Välj *myVNET*                                                                   |
    | Käll-NAT-undernät                 | Välj *myBackendSubnet*                                                          |
    | Aktivera TCP-proxy v2               | Välj Ja/Nej beroende på om ditt program förväntar sig TCP proxy v2-huvud |
    | Inställningar för privata IP-adresser       | Konfigurera tilldelnings metoden och IP-adressen för varje NAT-IP                  |

6. Välj **Nästa: åtkomst säkerhet**.

7. I **skapa en privat länk tjänst-åtkomst säkerhet**anger eller väljer du den här informationen:

    | Inställning                                     | Värde                                                             |
    |---------------------------------------------|-------------------------------------------------------------------|
    | Synlighet                                  | Välj *rollbaserad åtkomst kontroll*         |
  
8. Välj **Nästa: Taggar**och **granska sedan + skapa** eller Välj **fliken Granska + skapa** högst upp på sidan.

9. Granska informationen och välj **skapa**.

## <a name="clean-up-resources"></a>Rensa resurser 
När du är klar med den privata länk tjänsten tar du bort resurs gruppen för att rensa resurserna som används i den här snabb starten:

1. Skriv *myResourceGroupLB* i **sökrutan längst** upp i portalen och välj *myResourceGroupLB* från Sök resultaten. 
2. Välj **Ta bort resursgrupp**. 
3. Ange myResourceGroup för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en intern Azure Load Balancer och en privat länk tjänst. Mer information om hur du skapar privata slut punkter finns i [skapa privata slut punkter med hjälp av Azure Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).
