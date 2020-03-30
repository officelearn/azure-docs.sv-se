---
title: Skapa ett Azure Data Explorer-kluster & DB i det virtuella nätverket
description: I den här artikeln får du lära dig hur du skapar ett Azure Data Explorer-kluster i det virtuella nätverket.
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 4dff471fa0f2194756409e01512ed223a1d46024
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241444"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>Skapa ett Azure Data Explorer-kluster i det virtuella nätverket

Azure Data Explorer stöder distribution av ett kluster till ett undernät i ditt virtuella nätverk (VNet). Med den här funktionen kan du komma åt klustret privat från ditt virtuella Azure-nätverk eller lokala åtkomstresurser som Event Hub och Storage i det virtuella nätverket och begränsa inkommande och utgående trafik.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-network-security-group-nsg"></a>Skapa nätverkssäkerhetsgrupp (NSG)

[NSG (Network Security Groups)](/azure/virtual-network/security-overview) ger möjlighet att styra nätverksåtkomsten inom ett virtuella nätverk. Azure Data Explorer kan nås med hjälp av två slutpunkter HTTPs (443) och TDS (1433). Följande NSG-regler måste konfigureras för att tillåta åtkomst till dessa slutpunkter för hantering, övervakning och korrekt drift av klustret.

Så här skapar du nätverkssäkerhetsgruppen:

1. Välj knappen **+ Skapa en resurs** i det övre vänstra hörnet av portalen.
1. Sök efter *nätverkssäkerhetsgrupp*.
1. Välj **Skapa**längst ned på skärmen under **Nätverkssäkerhetsgrupp**.
1. Fyll i följande information i fönstret **Skapa nätverkssäkerhetsgrupp.**

   ![Skapa NSG-formulär](media/vnet-create-cluster-portal/network-security-group.png)

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Prenumeration | Din prenumeration | Välj den Azure-prenumeration som ska användas för klustret.|
    | Resursgrupp | Din resursgrupp | Använd en befintlig resursgrupp eller skapa en ny resursgrupp. |
    | Namn | AzureDataExplorerNsg | Välj ett namn som identifierar din NSG (Network Security Group) i resursgruppen.  |
    | Region | *Västra USA* | Välj den region som bäst uppfyller dina behov.
    | | | |

1. Välj **Granska + skapa** för att granska din klusterinformation och **Skapa** för att etablera klustret.

1. När distributionen är klar väljer du **Gå till resurs**.

    ![Gå till resurs](media/vnet-create-cluster-portal/notification-nsg.png)

1. På fliken **Inkommande säkerhetsregler** väljer du **+Lägg till**.
1. Fyll i följande information i fönstret **Lägg till inkommande säkerhetsregel.**

    ![Skapa NSG-formulär för inkommande regel](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **Inställning** | **Föreslaget värde** 
    |---|---|
    | Källa | ServiceTag (tjänst)
    | Servicetag för källa | AzureDataExplorerManagement
    | Källportintervall | *
    | Mål | VirtualNetwork
    | Målportintervall | *
    | Protokoll | TCP
    | Åtgärd | Tillåt
    | Prioritet | 100
    | Namn | TillåtAzureDataExplorerManagement
    | | |
    
1. Upprepa de två föregående stegen för alla inkommande och utgående beroenden enligt [beroenden för VNet-distribution](/azure/data-explorer/vnet-deloyment#dependencies-for-vnet-deployment). Alternativt kan utgående regler ersättas med en enda regel för att tillåta *Internet* för portarna 443 och 80.
    
    NSG-reglerna för inkommande och utgående beroenden ska se ut så här:

    ![NSG-regler](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>Skapa offentliga IP-adresser

Så här skapar du offentliga IP-adresser för frågan (Motor):

1. Välj knappen **+ Skapa en resurs** i det övre vänstra hörnet av portalen.
1. Sök efter *nätverkssäkerhetsgrupp*.
1. Under **Offentlig IP-adress**väljer du **Skapa**längst ned på skärmen .
1. Fyll i följande information i fönstret **Skapa offentlig IP-adress.**
   
   ![Skapa offentligt IP-formulär](media/vnet-create-cluster-portal/public-ip-blade.png)

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | IP-version | IPv4 | Välj IP-versionen. Vi stöder endast IPv4.|
    | Sku | Standard | Vi kräver **standard** för fråge-URI-slutpunkt (Motor). |
    | Namn | motor-pip | Välj ett namn som identifierar din offentliga IP-adress i resursgruppen.
    | Prenumeration | Din prenumeration | Välj den Azure-prenumeration som du vill använda för din offentliga IP.|
    | Resursgrupp | Din resursgrupp | Använd en befintlig resursgrupp eller skapa en ny resursgrupp. |
    | Location | *Västra USA* | Välj den region som bäst uppfyller dina behov.
    | | | |

1. Välj **Skapa** om du vill skapa den offentliga IP-adressen.

1. Om du vill skapa den offentliga IP-adressen (inmatning) följer du samma instruktioner och väljer 
    * **Sku**: Grundläggande
    * **IP-adresstilldelning**: Statisk

## <a name="create-virtual-network-and-subnet"></a>Skapa virtuellt nätverk och undernät

Så här skapar du det virtuella nätverket och undernätet:

1. Välj knappen **+ Skapa en resurs** i det övre vänstra hörnet av portalen.
1. Sök efter *virtuellt nätverk*.
1. Under **Virtuellt nätverk**väljer du **Skapa**längst ned på skärmen .
1. Fyll i följande information i fönstret **Skapa virtuellt nätverk.**

   ![Skapa virtuellt nätverksformulär](media/vnet-create-cluster-portal/vnet-blade.png)

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Prenumeration | Din prenumeration | Välj den Azure-prenumeration som ska användas för klustret.|
    | Resursgrupp | Din resursgrupp | Använd en befintlig resursgrupp eller skapa en ny resursgrupp. |
    | Namn | AzureDataExplorerVnet | Välj ett namn som identifierar ditt virtuella nätverk i resursgruppen.
    | Region | *Västra USA* | Välj den region som bäst uppfyller dina behov.
    | | | |

    > [!NOTE]
    > För produktionsarbetsbelastningar, planera din undernätsstorlek enligt [plan undernätsstorlek i ditt virtuella nätverk](/azure/data-explorer/vnet-deloyment#plan-subnet-size-in-your-vnet)

1. Välj **Granska + skapa** för att granska din klusterinformation och **Skapa** för att etablera klustret.

1. När distributionen är klar väljer du **Gå till resurs**.
1. Gå till **Bladet Undernät** och välj **standardundernätet.**
    
    ![Blad för undernät](media/vnet-create-cluster-portal/subnets.png)

1. I **default** standardundernätsfönstret:
    1. Välj din **nätverkssäkerhetsgrupp** på den nedrullningsbara menyn. 
    1. Välj namn på nätverkssäkerhetsgruppen i det här fallet **AzureDataExplorerNsg**. 
    1. **Spara**

    ![Konfigurera undernät](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>Skapa ett kluster

Skapa ett Azure Data Explorer-kluster med en definierad uppsättning beräknings- och lagringsresurser i en Azure-resursgrupp enligt beskrivningen i [skapa ett kluster](create-cluster-database-portal.md#create-a-cluster).

1. Innan du slutför klusterskapande väljer du fliken **Nätverk** i **klusterfönstret Skapa en Azure Data Explorer** för att tillhandahålla information om virtuella nätverk med hjälp av de resurser som skapats på de tidigare flikarna:

   ![Skapa klustervnet-formulär](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    |---|---|---|
    | Prenumeration | Din prenumeration | Välj den Azure-prenumeration som du vill använda för nätverksresurser.|
    | Virtual Network | AzureDataExplorerVnet | Välj det virtuella nätverk som skapats i föregående steg.
    | Undernät | standard | Välj det undernät som har skapats i föregående steg.
    | Fråga offentlig IP | motor-pip | Välj den offentliga IP-adressen för frågan som skapats i föregående steg.
    | Offentlig IP-adress för datainmatning | dm-pip (dm-pip) | Välj den offentliga IP-adressen för inmatning som skapats i föregående steg.
    | | | |

1. Välj **Granska + skapa** för att skapa ditt kluster.
1. När distributionen är klar väljer du **Gå till resurs**.

Om du vill distribuera ditt Azure Data Explorer-kluster till ditt virtuella nätverk använder du [distributionen av Azure Data Explorer-klustret i din VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure Resource Manager-mall.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera Azure Data Explorer till ditt virtuella nätverk](vnet-create-cluster-portal.md)