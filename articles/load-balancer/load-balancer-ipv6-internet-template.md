---
title: Distribuera en Internet-vänd belastningsutjämnare med IPv6 - Azure-mall
titleSuffix: Azure Load Balancer
description: Lär dig hur du distribuerar IPv6-stöd för Azure Load Balancer och belastningsbalanserade virtuella datorer med hjälp av en Azure-mall.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure load balancer, dual stack, public ip, native ipv6, mobile, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: eb9703a1944a650f41d76c05d79764f8bdf8cd52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045445"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Distribuera en Internet-vänd belastningsutjämnarelösning med IPv6 med hjälp av en mall

> [!div class="op_single_selector"]
> * [Powershell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Mall](load-balancer-ipv6-internet-template.md)


>[!NOTE] 
>I den här artikeln beskrivs en inledande IPv6-funktion så att basic Load Balancers kan tillhandahålla både IPv4- och IPv6-anslutning. Omfattande IPv6-anslutning är nu tillgänglig med [IPv6 för Azure VNETs](../virtual-network/ipv6-overview.md) som integrerar IPv6-anslutning med dina virtuella nätverk och innehåller viktiga funktioner som IPv6 Network Security Group-regler, IPv6 Användardefinierad routning, IPv6 Basic och Standard belastningsutjämning med mera.  IPv6 för Azure VNETs är den rekommenderade standarden för IPv6-program i Azure. Se [IPv6 för Azure VNET Powershell-distribution](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)  

En Azure Load Balancer är en Layer 4-lastbalanserare (TCP, UDP). Lastbalanseraren ger hög tillgänglighet genom att distribuera inkommande trafik mellan felfria tjänstinstanser i molntjänster eller virtuella datorer i en lastbalanseringsuppsättning. Azure Load Balancer kan även presentera dessa tjänster på flera portar, flera IP-adresser eller både och.

## <a name="example-deployment-scenario"></a>Exempel på distributionsscenario

Följande diagram illustrerar den belastningsutjämningslösning som distribueras med hjälp av exempelmallen som beskrivs i den här artikeln.

![Lastbalanseringsscenario](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

I det här scenariot skapar du följande Azure-resurser:

* ett virtuellt nätverksgränssnitt för varje virtuell dator med både IPv4- och IPv6-adresser tilldelade
* en Internet-vänd belastningsutjämnare med en IPv4 och en IPv6 public IP-adress
* två belastningsutjämningsregler för att mappa de offentliga VIP-paketen till de privata slutpunkterna
* en tillgänglighetsuppsättning som innehåller de två virtuella datorerna
* två virtuella datorer (VIRTUELLA datorer)

## <a name="deploying-the-template-using-the-azure-portal"></a>Distribuera mallen med Hjälp av Azure-portalen

Den här artikeln refererar till en mall som publiceras i azure [quickstart-mallgalleriet.](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) Du kan hämta mallen från galleriet eller starta distributionen i Azure direkt från galleriet. Den här artikeln förutsätter att du har hämtat mallen till den lokala datorn.

1. Öppna Azure-portalen och logga in med ett konto som har behörighet att skapa virtuella datorer och nätverksresurser i en Azure-prenumeration. Om du inte använder befintliga resurser behöver kontot också behörighet för att skapa en resursgrupp och ett lagringskonto.
2. Klicka på "+Ny" på menyn och skriv sedan "mall" i sökrutan. Välj "Malldistribution" i sökresultaten.

    ![lb-ipv6-portal-steg2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Klicka på "Malldistribution" i bladet Allt.

    ![lb-ipv6-portal-steg3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Klicka på "Skapa".

    ![lb-ipv6-portal-steg4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Klicka på "Redigera mall". Ta bort det befintliga innehållet och kopiera/klistra in hela innehållet i mallfilen (om du vill inkludera start och { }) och klicka sedan på Spara.

    > [!NOTE]
    > Om du använder Microsoft Internet Explorer får du en dialogruta där du uppmanas att tillåta åtkomst till Urklipp i Windows när du klistrar in. Klicka på "Tillåt åtkomst".

    ![lb-ipv6-portal-steg5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Klicka på "Redigera parametrar". I bladet Parametrar anger du värdena per vägledningen i avsnittet Mallparametrar och klickar sedan på "Spara" för att stänga bladet Parametrar. I bladet Anpassad distribution väljer du din prenumeration, en befintlig resursgrupp eller skapar en. Om du skapar en resursgrupp väljer du en plats för resursgruppen. Klicka sedan på **Juridiska termer**och sedan på **Köp** för de juridiska villkoren. Azure börjar distribuera resurserna. Det tar flera minuter att distribuera alla resurser.

    ![lb-ipv6-portal-steg6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Mer information om dessa parametrar finns i avsnittet [Mallparametrar och variabler](#template-parameters-and-variables) senare i den här artikeln.

7. Om du vill visa de resurser som skapas av mallen klickar du på Bläddra, rullar nedåt i listan tills du ser "Resursgrupper" och klickar sedan på den.

    ![lb-ipv6-portal-steg7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Klicka på namnet på resursgruppen som du angav i steg 6 i bladet Resursgrupper. Du ser en lista över alla resurser som har distribuerats. Om allt gick bra, bör det stå "lyckades" under "Senaste distribution." Om inte, se till att kontot du använder har behörighet att skapa nödvändiga resurser.

    ![lb-ipv6-portal-steg8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Om du bläddrar bland resursgrupperna direkt efter att du har slutfört steg 6 visas statusen "Distribution" medan resurserna distribueras.

9. Klicka på "myIPv6PublicIP" i listan över resurser. Du ser att den har en IPv6-adress under IP-adress och att dess DNS-namn är det värde som du angav för parametern dnsNameforIPv6LbIP i steg 6. Den här resursen är den offentliga IPv6-adressen och värdnamnet som är tillgängligt för Internet-klienter.

    ![lb-ipv6-portal-steg9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Verifiera anslutning

När mallen har distribuerats kan du validera anslutningen genom att utföra följande uppgifter:

1. Logga in på Azure-portalen och anslut till var och en av de virtuella datorer som skapats av malldistributionen. Om du har distribuerat en virtuell Windows Server kör du ipconfig /all från en kommandotolk. Du ser att de virtuella datorerna har både IPv4- och IPv6-adresser. Om du distribuerade virtuella Linux-datorer måste du konfigurera Linux OS för att ta emot dynamiska IPv6-adresser med hjälp av instruktionerna för din Linux-distribution.
2. Starta en anslutning till den offentliga IPv6-adressen för belastningsutjämnningsutjämningen från en IPv6-klient som är ansluten till Internet. Om du vill bekräfta att belastningsutjämkaren balanserar mellan de två virtuella datorerna kan du installera en webbserver som Microsoft Internet Information Services (IIS) på var och en av de virtuella datorerna. Standardwebbsidan på varje server kan innehålla texten "Server0" eller "Server1" för att identifiera den på ett unikt sätt. Öppna sedan en webbläsare på en IPv6-internetansluten klient och bläddra till det värdnamn som du angav för parametern dnsNameforIPv6LbIP för belastningsutjämnaren för att bekräfta end-to-end IPv6-anslutning till varje virtuell dator. Om du bara ser webbsidan från bara en server kan du behöva rensa webbläsarens cacheminne. Öppna flera privata webbläsarsessioner. Du bör se ett svar från varje server.
3. Starta en anslutning till den offentliga IPv4-adressen för belastningsutjämnningsutjämningen från en IPv4-ansluten klient. Om du vill bekräfta att belastningsutjämnaren är belastningsutjämning av de två virtuella datorerna kan du testa med IIS, enligt beskrivningen i steg 2.
4. Starta en utgående anslutning till en IPv6- eller IPv4-ansluten Internet-enhet från varje virtuell dator. I båda fallen är käll-IP som visas av målenheten den offentliga IPv4- eller IPv6-adressen för belastningsutjämnaren.

> [!NOTE]
> ICMP för både IPv4 och IPv6 är blockerad i Azure-nätverket. Som ett resultat, ICMP verktyg som ping misslyckas alltid. Om du vill testa anslutningen använder du ett TCP-alternativ som TCPing eller PowerShell Test-NetConnection cmdlet. Observera att IP-adresserna som visas i diagrammet är exempel på värden som kan visas. Eftersom IPv6-adresserna tilldelas dynamiskt kommer de adresser du får att skilja sig åt och kan variera mellan olika regioner. Det är också vanligt att den offentliga IPv6-adressen på belastningsutjämnaren börjar med ett annat prefix än de privata IPv6-adresserna i backend-poolen.

## <a name="template-parameters-and-variables"></a>Mallparametrar och variabler

En Azure Resource Manager-mall innehåller flera variabler och parametrar som du kan anpassa efter dina behov. Variabler används för fasta värden som du inte vill att en användare ska ändra. Parametrar används för värden som du vill att en användare ska tillhandahålla när du distribuerar mallen. Exempelmallen är konfigurerad för det scenario som beskrivs i den här artikeln. Du kan anpassa detta till behoven i din miljö.

Exempelmallen som används i den här artikeln innehåller följande variabler och parametrar:

| Parameter/variabel | Anteckningar |
| --- | --- |
| adminUsername |Ange namnet på det administratörskonto som används för att logga in på de virtuella datorerna med. |
| adminPassword |Ange lösenordet för det administratörskonto som används för att logga in på de virtuella datorerna med. |
| dnsNameforIPv4LbIP |Ange det DNS-värdnamn som du vill tilldela som det offentliga namnet på belastningsutjämnaren. Det här namnet matchas till belastningsutjämnarens offentliga IPv4-adress. Namnet måste vara gemen och matcha regex: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP |Ange det DNS-värdnamn som du vill tilldela som det offentliga namnet på belastningsutjämnaren. Det här namnet matchas till belastningsutjämnarens offentliga IPv6-adress. Namnet måste vara gemen och matcha regex: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$. Det kan vara samma namn som IPv4-adressen. När en klient skickar en DNS-fråga för det här namnet returneras både A- och AAAA-posterna när namnet delas. |
| vmNamePrefix |Ange vm-namnprefixet. Mallen lägger till ett tal (0, 1 osv.) till namnet när de virtuella datorerna skapas. |
| nicNamePrefix |Ange namnprefixet för nätverksgränssnittet. Mallen lägger till ett nummer (0, 1, etc.) till namnet när nätverksgränssnitten skapas. |
| storageAccountName |Ange namnet på ett befintligt lagringskonto eller ange namnet på ett nytt som ska skapas av mallen. |
| tillgänglighetSetName |Ange sedan namnet på den tillgänglighet som ska användas med de virtuella datorerna |
| addressPrefix |Adressprefixet som används för att definiera adressintervallet för det virtuella nätverket |
| undernätNamn |Namnet på undernätet som skapats för det virtuella nätverket |
| subnetPrefix |Adressprefixet som används för att definiera adressintervallet för undernätet |
| vnetName |Ange namnet på det virtuella nätverk som används av de virtuella datorerna. |
| ipv4PrivateIPAddressTyp |Allokeringsmetoden som används för den privata IP-adressen (statisk eller dynamisk) |
| ipv6PrivateIPAddressTyp |Allokeringsmetoden som används för den privata IP-adressen (Dynamisk). IPv6 stöder endast dynamisk allokering. |
| numberOfInstances |Antalet belastningsbalanserade instanser som distribueras av mallen |
| ipv4PublicIPAddressName |Ange det DNS-namn som du vill använda för att kommunicera med lastbalanserarens offentliga IPv4-adress. |
| ipv4PublicIPAddressTyp |Allokeringsmetoden som används för den offentliga IP-adressen (statisk eller dynamisk) |
| Ipv6PublicIPAddressName |Ange det DNS-namn som du vill använda för att kommunicera med lastbalanserarens offentliga IPv6-adress. |
| ipv6PublicIPAddressTyp |Allokeringsmetoden som används för den offentliga IP-adressen (Dynamisk). IPv6 stöder endast dynamisk allokering. |
| lbName (lbName) |Ange namnet på belastningsutjämnaren. Det här namnet visas i portalen eller används när det refererar till det med ett CLI- eller PowerShell-kommando. |

De återstående variablerna i mallen innehåller härledda värden som tilldelas när Azure skapar resurserna. Ändra inte dessa variabler.

## <a name="next-steps"></a>Nästa steg

JSON-syntaxen och egenskaperna för en belastningsutjämnare i en mall finns i [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
