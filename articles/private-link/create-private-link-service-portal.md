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
ms.openlocfilehash: 5b7bc8be89068f0d3cf6722c36ae7fd5cc560736
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96012126"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Snabb start: skapa en privat länk-tjänst med hjälp av Azure Portal

En Azure Private Link-tjänst refererar till din egen tjänst som hanteras av en privat länk. Du kan ge privat länk åtkomst till den tjänst eller resurs som fungerar bakom Azure Standard Load Balancer. Konsumenter av tjänsten kan komma åt den privat från sina egna virtuella nätverk. I den här snabb starten får du lära dig hur du skapar en privat länk-tjänst med hjälp av Azure Portal.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Skapa en intern lastbalanserare

Skapa först ett virtuellt nätverk. Skapa sedan en intern belastningsutjämnare som ska användas med den privata länk tjänsten.

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

I det här avsnittet skapar du ett virtuellt nätverk. Du skapar också ett undernät som är värd för belastningsutjämnaren som har åtkomst till din privata länk tjänst.

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | USA, östra 2      |
| **\<IPv4-address-space>**   | 10.3.0.0/16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>Skapa en standardlastbalanserare

Använd portalen för att skapa en standard intern belastningsutjämnare. Namnet och IP-adressen som du anger konfigureras automatiskt som belastningsutjämnarens klient del.

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **nätverk**  >  **Load Balancer**.

1. Ange eller Välj följande information på fliken **grundläggande** på sidan **skapa belastnings utjämning** :

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | **Prenumeration**               | Välj din prenumeration.    |
    | **Resursgrupp**         | Välj **myResourceGroupLB** från rutan.|
    | **Namn**                   | Ange **myLoadBalancer**.                                   |
    | **Region**         | Välj **USA, östra 2**.                                        |
    | **Typ**          | Välj **Intern**.                                        |
    | **SKU**           | Välj **standard**.                          |
    | **Virtuellt nätverk**           | Välj **myVNet**.                          |
    | **Tilldelning av IP-adress**              | Välj **Statisk**.   |
    | **Privat IP-adress**|Ange en adress i adress utrymmet för det virtuella nätverket och under nätet. Ett exempel är 10.3.0.7.  |

1. Acceptera standardinställningarna för återstående inställningar och välj sedan **Granska + skapa**

1. På fliken **Granska och skapa** väljer du **skapa**.

### <a name="create-standard-load-balancer-resources"></a>Skapa standard resurser för belastnings utjämning

I det här avsnittet konfigurerar du inställningar för lastbalanseraren för en serverdelsadresspool och en hälsoavsökning. Du anger också regler för belastnings utjämning.

#### <a name="create-a-back-end-pool"></a>Skapa en backend-pool

En backend-adresspool innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till belastningsutjämnaren. Med den här poolen kan du distribuera trafik till dina resurser. Skapa backend-adresspoolen med namnet **myBackendPool** för att inkludera resurser som belastnings Utjämnings trafik.

1. Välj **alla tjänster** på menyn längst till vänster.
1. Välj **alla resurser** och välj sedan **myLoadBalancer** i listan resurser.
1. Under **Inställningar** väljer du **Serverdelspooler** och sedan **Lägg till**.
1. På sidan **Lägg till en server dels grupp** anger du **myBackendPool** som namn på backend-poolen och väljer sedan **Lägg till**.

#### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Använd en hälso avsökning för att låta belastnings utjämning övervaka resurs status. Baserat på resurs svar på hälso kontroller lägger hälso avsökningarna dynamiskt till eller tar bort resurser från belastnings Utjämnings rotationen.

Så här skapar du en hälso avsökning för att övervaka hälso tillståndet för resurserna:

1. Välj **alla resurser** på menyn längst till vänster och välj sedan **myLoadBalancer** i resurs listan.

1. Under **Inställningar** väljer du **Hälsoavsökningar** och sedan **Lägg till**.

1. På sidan **Lägg till hälso avsökning** anger eller väljer du följande värden:

   - **Namn**: ange **myHealthProbe**.
   - **Protokoll**: Välj **TCP**.
   - **Port**: ange **80**.
   - **Intervall**: ange **15**. Det här värdet är antalet sekunder mellan avsöknings försök.
   - **Tröskelvärde** för ej felfri: ange **2**. Det här värdet är antalet avsöknings fel i följd som inträffar innan en virtuell dator betraktas som ohälsosam.

1. Välj **OK**.

#### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En belastnings Utjämnings regel definierar hur trafiken distribueras till resurser. Regeln definierar:

- Klient delens IP-konfiguration för inkommande trafik.
- Server delens IP-pool för att ta emot trafiken.
- Käll-och mål portarna som krävs.

Belastnings Utjämnings regeln med namnet **myLoadBalancerRule** lyssnar på port 80 i **LoadBalancerFrontEnd** -klient delen. Regeln skickar nätverks trafik till **myBackendPool** backend-adresspoolen på samma port 80.

Så här skapar du en belastnings Utjämnings regel:

1. Välj **alla resurser** på menyn längst till vänster och välj sedan **myLoadBalancer** i resurs listan.

1. Under **Inställningar** väljer du **regler för belastnings utjämning** och väljer sedan **Lägg till**.

1. På sidan **Lägg till belastnings Utjämnings regel** anger eller väljer du följande värden om de inte redan finns:

   - **Namn**: ange **myLoadBalancerRule**.
   - **IP-adress för klient del:** Ange **LoadBalancerFrontEnd**.
   - **Protokoll**: Välj **TCP**.
   - **Port**: ange **80**.
   - **Backend-port**: ange **80**.
   - **Backend-pool**: Välj **myBackendPool**.
   - **Hälso avsökning**: Välj **myHealthProbe**. 

1. Välj **OK**.

## <a name="create-a-private-link-service"></a>Skapa en Private Link-tjänst

I det här avsnittet ska du skapa en privat länk-tjänst bakom en standard belastningsutjämnare.

1. I den övre vänstra delen av sidan i Azure Portal väljer du **skapa en resurs**  >  **nätverk**  >  **Private Link Center (för hands version)**. Du kan också använda portalens sökruta för att söka efter en privat länk.

1. I **privat länk Center – översikt**  >  **exponerar din egen tjänst så att andra kan ansluta**, Välj **Start**.

1. Under **skapa en privat länk tjänst – grunderna** anger eller väljer du den här informationen:

    | Inställning           | Värde                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | Projekt information:  |                                                                              |
    | **Prenumeration**      | Välj din prenumeration.                                                     |
    | **Resursgrupp**    | Välj **myResourceGroupLB**.                                                    |
    | Instans information: |                                                                              |
    | **Namn**              | Ange **myPrivateLinkService**. |
    | **Region**            | Välj **USA, östra 2**.                                                        |

1. Välj **Nästa: utgående inställningar**.

1. Under **skapa en privat länk tjänst-utgående inställningar** anger eller väljer du den här informationen:

    | Inställning                           | Värde                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Load Balancer**                     | Välj **myLoadBalancer**.                                                           |
    | **IP-adress för Load Balancer-klient** | Välj IP-adressen för klient delen av **myLoadBalancer**.                                |
    | **Käll-NAT virtuellt nätverk**        | Välj **myVNet**.                                                                   |
    | **Käll-NAT-undernät**                 | Välj **myBackendSubnet**.                                                          |
    | **Aktivera TCP-proxy v2**               | Välj **Ja** eller **Nej** beroende på om ditt program förväntar sig ett TCP-proxy v2-huvud. |
    | **Inställningar för privata IP-adresser**       | Konfigurera tilldelnings metoden och IP-adressen för varje NAT-IP.                  |

1. Välj **Nästa: åtkomst säkerhet**.

1. Under **skapa en privat länk tjänst-åtkomst säkerhet**, väljer du **synlighet** och väljer sedan **rollbaserad åtkomst kontroll**.
  
1. Välj antingen **Nästa: Taggar**  >  **Granska + skapa** eller Välj fliken **Granska + skapa** högst upp på sidan.

1. Granska informationen och välj **skapa**.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den privata länk tjänsten tar du bort resurs gruppen för att rensa resurserna som används i den här snabb starten.

1. Skriv **myResourceGroupLB** i sökrutan längst upp i portalen och välj **myResourceGroupLB** från Sök resultaten.
1. Välj **Ta bort resursgrupp**.
1. I **Skriv resurs gruppens namn**, anger du **myResourceGroup**.
1. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en intern Azure Load Balancer och en privat länk tjänst. Du kan också lära dig hur du [skapar en privat slut punkt med hjälp av Azure Portal](./create-private-endpoint-portal.md).