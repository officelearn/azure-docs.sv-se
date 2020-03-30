---
title: Snabbstart - Skapa en privat länktjänst med hjälp av Azure-portalen
titlesuffix: Azure Private Link
description: Lär dig hur du skapar en privat länktjänst med hjälp av Azure-portalen i den här snabbstarten
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: f21b440ee0e2c53d9824300e85b683629c1575da
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78252553"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Snabbstart: Skapa en privat länktjänst med hjälp av Azure-portalen

En Azure Private Link-tjänst refererar till din egen tjänst som hanteras av Private Link. Du kan ge privat länk åtkomst till tjänsten eller resursen som fungerar bakom Azure Standard Load Balancer. Konsumenter av din tjänst kan komma åt den privat från sina egna virtuella nätverk. I den här snabbstarten får du lära dig hur du skapar en private link-tjänst med hjälp av Azure-portalen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Skapa en intern lastbalanserare

Skapa först ett virtuellt nätverk. Skapa sedan en intern belastningsutjämnare som ska användas med tjänsten Privat länk.

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

I det här avsnittet skapar du ett virtuellt nätverk. Du kan också skapa undernätet som värd för belastningsutjämnaren som använder tjänsten Private Link.

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resursgruppnamn>**  | myResourceGroupLB |
| **\<>virtuellt nätverksnamn** | myVNet          |
| **\<regionnamn>**          | USA, östra 2      |
| **\<IPv4-adress-utrymme>**   | 10.3.0.0\16          |
| **\<>i>**          | myBackendSubnet        |
| **\<>för>** | 10.3.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>Skapa en standardlastbalanserare

Använd portalen för att skapa en intern standardbelastningsutjämnare. Namnet och IP-adressen som du anger konfigureras automatiskt som belastningsutjämnarens klientdel.

1. Välj **Skapa en resurs** > **Nätverksbelastningsutjämnare****Networking** > längst upp till vänster i portalen .

1. Ange eller välj följande information på fliken Grunderna på sidan **Skapa belastningsutjämnare:** **Basics**

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | **Prenumeration**               | Välj din prenumeration.    |
    | **Resursgrupp**         | Välj **myResourceGroupLB** i rutan .|
    | **Namn**                   | Ange **myLoadBalancer**.                                   |
    | **Regionen**         | Välj **USA, östra 2**.                                        |
    | **Typ**          | Välj **Intern**.                                        |
    | **Sku**           | Välj **Standard**.                          |
    | **Virtuellt nätverk**           | Välj **myVNet**.                          |
    | **TILLDELNING AV IP-adress**              | Välj **Statisk**.   |
    | **Privat IP-adress**|Ange en adress som finns i adressutrymmet för ditt virtuella nätverk och i undernät. Ett exempel är 10.3.0.7.  |

1. Acceptera standardinställningarna för de återstående inställningarna och välj sedan **Granska + skapa**

1. Välj **Skapa**på fliken **Granska + skapa** .

### <a name="create-standard-load-balancer-resources"></a>Skapa standardbelastningsutjämningsresurser

I det här avsnittet konfigurerar du inställningar för lastbalanseraren för en serverdelsadresspool och en hälsoavsökning. Du kan också ange belastningsutjämnareregler.

#### <a name="create-a-back-end-pool"></a>Skapa en backend-pool

En backend-adresspool innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till belastningsutjämnaren. Med den här poolen kan du distribuera trafik till dina resurser. Skapa backend-adresspoolen **myBackendPoolen** för att inkludera resurser som belastningsbalanstrafik.

1. Välj **Alla tjänster** på menyn längst till vänster.
1. Välj **Alla resurser**och välj sedan **myLoadBalancer** i resurslistan.
1. Under **Inställningar** väljer du **Serverdelspooler** och sedan **Lägg till**.
1. På sidan **Lägg till en backend-pool** anger du **myBackendPool** som namn på backend-poolen och väljer sedan **Lägg till**.

#### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Använd en hälsoavsökning för att låta belastningsutjämnaren övervaka resursstatus. Baserat på resurssvar på hälsokontroller lägger hälsoavsökningen dynamiskt till eller tar bort resurser från belastningsutjämnarrotationen.

Så här skapar du en hälsoavsökning för att övervaka resursernas hälsa:

1. Välj **Alla resurser** på menyn längst till vänster och välj sedan **myLoadBalancer** i resurslistan.

1. Under **Inställningar** väljer du **Hälsoavsökningar** och sedan **Lägg till**.

1. På sidan **Lägg till en hälsoavsökning** anger eller markerar du följande värden:

   - **Namn**: Ange **myHealthProbe**.
   - **Protokoll**: Välj **TCP**.
   - **Port:** Ange **80**.
   - **Intervall**: Ange **15**. Det här värdet är antalet sekunder mellan avsökningsförsök.
   - **Fel tröskel:** Ange **2**. Det här värdet är antalet på varandra följande avsökningsfel som inträffar innan en virtuell dator anses vara fel.

1. Välj **OK**.

#### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En belastningsutjämnad regel definierar hur trafiken fördelas till resurser. Regeln definierar:

- Front-end IP-konfiguration för inkommande trafik.
- Den bakre IP-poolen för att ta emot trafiken.
- Nödvändiga käll- och målportar.

Belastningsutjämnarregeln **myLoadBalancerRule** lyssnar på port 80 i **LoadBalancerFrontEnd-fronten.** Regeln skickar nätverkstrafik till backend-adresspoolen **myBackendPoolen** på samma port 80.

Så här skapar du en belastningsutjämnad regel:

1. Välj **Alla resurser** på menyn längst till vänster och välj sedan **myLoadBalancer** i resurslistan.

1. Under **Inställningar**väljer du **Belastningsutjämningsregler**och väljer sedan **Lägg till**.

1. På sidan **Lägg till belastningsutjämningsregel** anger eller markerar du följande värden om de inte redan finns:

   - **Namn**: Ange **myLoadBalancerRule**.
   - **IP-adress för frontend:** Ange **LoadBalancerFrontEnd**.
   - **Protokoll**: Välj **TCP**.
   - **Port:** Ange **80**.
   - **Backend port:** Ange **80**.
   - **Backend pool:** Välj **myBackendPool**.
   - **Hälsoprober:** Välj **myHealthProbe**. 

1. Välj **OK**.

## <a name="create-a-private-link-service"></a>Skapa en Private Link-tjänst

I det här avsnittet skapar du en private link-tjänst bakom en standardbelastningsutjämnare.

1. På den övre vänstra delen av sidan i Azure-portalen väljer du **Skapa ett privat** > **resursnätverkscenter** > **(förhandsversion).** Du kan också använda portalens sökruta för att söka efter Privat länk.

1. I **Private Link Center - Översikt** > **Exponera din egen tjänst så att andra kan ansluta**väljer du **Start**.

1. Under **Skapa en privat länktjänst - Grunderna**anger eller väljer du den här informationen:

    | Inställning           | Värde                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | Projektinformation:  |                                                                              |
    | **Prenumeration**      | Välj din prenumeration.                                                     |
    | **Resursgrupp**    | Välj **myResourceGroupLB**.                                                    |
    | Information om instans: |                                                                              |
    | **Namn**              | Ange **myPrivateLinkService**. |
    | **Regionen**            | Välj **USA, östra 2**.                                                        |

1. Välj **Nästa: Utgående inställningar**.

1. Under **Skapa en privat länktjänst - Utgående inställningar**anger eller väljer du den här informationen:

    | Inställning                           | Värde                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Belastningsutjämnare**                     | Välj **myLoadBalancer**.                                                           |
    | **IP-adress för belastningsutjämnare** | Välj front-end IP-adress **för myLoadBalancer**.                                |
    | **Virtuellt nätverk för KÄLLA NAT**        | Välj **myVNet**.                                                                   |
    | **NAT-undernät för källa**                 | Välj **myBackendSubnet**.                                                          |
    | **Aktivera TCP-proxy v2**               | Välj **JA** eller **NEJ** beroende på om ditt program förväntar sig ett TCP-proxy v2-huvud. |
    | **Inställningar för privat IP-adress**       | Konfigurera allokeringsmetod och IP-adress för varje NAT-IP.                  |

1. Välj **Nästa: Åtkomstsäkerhet**.

1. Under **Skapa en privat länktjänst - Åtkomstsäkerhet**väljer du **Synlighet**och väljer sedan **Endast rollbaserad åtkomstkontroll**.
  
1. Välj antingen **Nästa: Taggar** > **granska + skapa** eller välj fliken Granska + **skapa** högst upp på sidan.

1. Granska din information och välj **Skapa**.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med tjänsten Privat länk tar du bort resursgruppen för att rensa de resurser som används i den här snabbstarten.

1. Ange **myResourceGroupLB** i sökrutan högst upp på portalen och välj **myResourceGroupLB** i sökresultaten.
1. Välj **Ta bort resursgrupp**.
1. Ange **myResourceGroup** **i TYP RESURSGRUPPSNAMN**.
1. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en intern Azure-belastningsutjämning och en Private Link-tjänst. Du kan också lära dig hur du [skapar en privat slutpunkt med hjälp av Azure-portalen](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).
