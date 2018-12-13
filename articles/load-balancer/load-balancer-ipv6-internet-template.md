---
title: Distribuera en internetuppkopplad belastningsutjämnare med IPv6 - Azure-mall
titlesuffix: Azure Load Balancer
description: Så här distribuerar IPv6-stöd för Azure Load Balancer och belastningsutjämnade virtuella datorer.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: IPv6, azure-belastningsutjämnare, dual stack, offentlig IP-adress, inbyggd ipv6, mobil, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 2374df3a457e0f4730dcaa6d69ea3f2e0fa6e9cc
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163216"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Distribuera en internetuppkopplad belastningsutjämnare-lösning med IPv6 med en mall

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Mall](load-balancer-ipv6-internet-template.md)



En Azure Load Balancer är en Layer 4-lastbalanserare (TCP, UDP). Lastbalanseraren ger hög tillgänglighet genom att distribuera inkommande trafik mellan felfria tjänstinstanser i molntjänster eller virtuella datorer i en lastbalanseringsuppsättning. Azure Load Balancer kan även presentera dessa tjänster på flera portar, flera IP-adresser eller både och.

## <a name="example-deployment-scenario"></a>Exempelscenario för distribution

Följande diagram illustrerar de belastningsutjämningslösning som distribueras med hjälp av mallen för exemplet som beskrivs i den här artikeln.

![Lastbalanseringsscenario](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

I det här scenariot skapar du följande Azure-resurser:

* Ett virtuellt nätverksgränssnitt för varje virtuell dator med både IPv4 och IPv6-adresserna som tilldelats
* en internetuppkopplad belastningsutjämnare med en IPv4 och en offentlig IPv6-IP-adress
* Två belastningsutjämningsregler för att mappa de offentliga virtuella IP-adresserna till de privata slutpunkterna
* en Tillgänglighetsuppsättning som innehåller två virtuella datorer
* Två virtuella datorer (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>Distribuera mallen med hjälp av Azure portal

Den här artikeln refererar till en mall som har publicerats i den [Azure-Snabbstartsmallar](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) galleriet. Du kan ladda ned mallen från galleriet eller starta distribution i Azure direkt från galleriet. Den här artikeln förutsätter att du har hämtat mallen till den lokala datorn.

1. Öppna Azure-portalen och logga in med ett konto som har behörighet att skapa virtuella datorer och nätverksresurser i en Azure-prenumeration. Även om du inte använder befintliga resurser, kontot måste ha behörighet att skapa en resursgrupp och ett lagringskonto.
2. Klicka på ”+ ny” från menyn och anger ”mall” i sökrutan. Välj ”malldistributionen” från sökresultaten.

    ![lb-ipv6-portal-step2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Allt bladet klickar du på ”malldistribution”.

    ![lb-ipv6-portal – steg 3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Klicka på ”Skapa”.

    ![lb-ipv6-portal-step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Klicka på ”Redigera mall”. Ta bort det befintliga innehållet och kopiera och klistra in i hela innehållet i mallfilen (för att innehålla början och sluta {}) och sedan klicka på ”Spara”.

    > [!NOTE]
    > Om du använder Microsoft Internet Explorer när du klistrar in du ser en dialogruta som ber dig att tillåta åtkomst till Urklipp i Windows. Klicka på ”Tillåt åtkomst”.

    ![lb-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Klicka på ”Redigera parametrar”. I bladet parametrar anger du värden enligt instruktionerna i avsnittet Parametrar för mallen och klicka på ”Spara” för att Stäng bladet parametrar. Välj din prenumeration, en befintlig resursgrupp eller skapa en på bladet anpassad distribution. Om du skapar en resursgrupp, välj sedan en plats för resursgruppen. Klicka sedan på **juridiska villkor**, klicka sedan på **köp** för de juridiska villkoren. Azure börjar distribuera resurserna. Det tar flera minuter att distribuera alla resurser.

    ![lb-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Mer information om dessa parametrar finns i den [-mallens parametrar och variabler](#template-parameters-and-variables) senare i den här artikeln.

7. Om du vill se de resurser som skapas av mallen, klicka på Bläddra, rulla nedåt i listan tills du ser ”resursgrupper” och sedan klicka på den.

    ![lb-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Klicka på namnet på resursgruppen som du angav i steg 6 på resursbladet för grupper. Du kan se en lista över alla resurser som har distribuerats. Om allt gått bra ska det stå ”Succeeded” under ”senaste distribution”. Om inte, kan du kontrollera att det konto du använder har behörighet att skapa resurserna som krävs.

    ![lb-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Om du bläddrar resursgrupper omedelbart när du har slutfört steg 6 visas status för ”distribuera” för ”senaste distribution” medan resurserna som distribueras.

9. Klicka på ”myIPv6PublicIP” i listan över resurser. Du ser att det finns en IPv6-adress under IP-adress och att dess DNS-namn är det värde du angav för parametern dnsNameforIPv6LbIP i steg 6. Den här resursen är det offentliga IPv6-adress och värddatornamn namn som är tillgänglig för Internet-klienter.

    ![lb-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Verifiera anslutningen

När mallen har distribuerats, kan du verifiera anslutningen genom att slutföra följande uppgifter:

1. Logga in på Azure Portal och ansluta till var och en av de virtuella datorerna som skapats av malldistributionen. Om du har distribuerat en virtuell Windows Server-dator, köra ipconfig/allt från en kommandotolk. Du ser att de virtuella datorerna har både IPv4 och IPv6-adresser. Om du har distribuerat virtuella Linux-datorer måste du konfigurera Linux-operativsystem för att ta emot dynamiska IPv6-adresser med hjälp av anvisningarna för din Linux-distribution.
2. Initiera en anslutning till den offentliga IPv6-adressen för belastningsutjämnaren från en klient för IPv6-Internet-anslutna. För att bekräfta att belastningsutjämnaren balansering mellan två virtuella datorer, kan du installera en webbserver som Microsoft Internet Information Services (IIS) på varje virtuell dator. Standardwebbsidan på varje server kan innehålla texten ”Server0” eller ”Server1” för att identifiera den. Öppna en webbläsare på en IPv6-Internet-ansluten klient och bläddra till värdnamnet som du angav för parametern dnsNameforIPv6LbIP till belastningsutjämnaren för att bekräfta slutpunkt till slutpunkt IPv6-anslutning till varje virtuell dator. Om du bara ser sidan från endast en server kan du behöva rensa webbläsarens cache. Öppna flera privata webbläsarsessioner. Du bör se ett svar från varje server.
3. Initiera en anslutning till den offentliga IPv4-adressen för belastningsutjämnaren i en IPv4-Internet-anslutna-klient. För att bekräfta att belastningsutjämnaren är de två virtuella datorerna för belastningsutjämning, kan du testa med hjälp av IIS, enligt beskrivningen i steg 2.
4. Initiera en utgående anslutning till en IPv6- eller IPv4-anslutna Internet-enhet från varje virtuell dator. I båda fallen är käll-IP som setts av målenheten den offentliga IPv4- eller IPv6-adressen för belastningsutjämnaren.

> [!NOTE]
> ICMP för både IPv4 och IPv6 är blockerad i Azure-nätverket. Därför misslyckas ICMP-verktyg som pinga alltid. Använd ett TCP-alternativ, till exempel använda TCPing eller PowerShell Test-NetConnection cmdlet för att testa anslutningen. Observera att IP-adresser som visas i diagrammet är exempel på värden som kan visas. Eftersom IPv6-adresser tilldelas dynamiskt, varierar de adresser som du får och kan variera beroende på region. Dessutom är det vanligt för den offentliga IPv6-adressen på belastningsutjämnaren ska börja med ett annat prefix än privata IPv6-adresserna i backend-poolen.

## <a name="template-parameters-and-variables"></a>Mallens parametrar och variabler

En Azure Resource Manager-mall innehåller flera variabler och parametrar som du kan anpassa efter dina behov. Variabler används för fasta värden som du inte vill att en användare att ändra. Parametrar används för värden som du vill att en användare att ange när mallen distribueras. Exempel-mallen har konfigurerats för det scenario som beskrivs i den här artikeln. Du kan anpassa det efter behoven i din miljö.

Exempel-mallen som används i den här artikeln innehåller följande variabler och parametrar:

| Parametern / Variable | Anteckningar |
| --- | --- |
| adminUsername |Ange namnet på det administratörskonto som används för att logga in på de virtuella datorerna med. |
| adminPassword |Ange lösenord för administratörskonto som används för att logga in på de virtuella datorerna med. |
| dnsNameforIPv4LbIP |Ange DNS-värdnamn som du vill tilldela som det offentliga namnet på belastningsutjämnaren. Det här namnet matchar belastningsutjämnarens offentliga IPv4-adress. Namnet måste vara i gemener och matcha regexet: ^ [a-z] [ett-z0 - 9 –]{1,61}[a-z0-9] $. |
| dnsNameforIPv6LbIP |Ange DNS-värdnamn som du vill tilldela som det offentliga namnet på belastningsutjämnaren. Det här namnet matchar belastningsutjämnarens offentliga IPv6-adress. Namnet måste vara i gemener och matcha regexet: ^ [a-z] [ett-z0 - 9 –]{1,61}[a-z0-9] $. Detta kan vara samma namn som IPv4-adress. När en klient skickar en DNS-fråga för det här namnet som Azure kommer att returnera registrerar både A- och AAAA när namnet delas. |
| vmNamePrefix |Ange prefix för VM-namn. Mallen till en siffra (0, 1, etc.) till namnet när de virtuella datorerna skapas. |
| nicNamePrefix |Ange nätverksprefixet gränssnittet namn. Mallen till en siffra (0, 1, etc.) till namnet när nätverksgränssnitt som skapas. |
| storageAccountName |Ange namnet på ett befintligt lagringskonto eller ange namnet på en ny som ska skapas av mallen. |
| availabilitySetName |Ange sedan namnet på tillgänglighetsuppsättningen som ska användas med de virtuella datorerna |
| addressPrefix |Adressprefixet används för att definiera adressintervallet för det virtuella nätverket |
| subnetName |Namnet på undernätet i skapats för det virtuella nätverket |
| subnetPrefix |Adressprefixet används för att definiera adressintervallet för undernätet |
| vnetName |Ange ett namn för det virtuella nätverket som används av de virtuella datorerna. |
| ipv4PrivateIPAddressType |Allokeringsmetoden används för den privata IP-adressen (statisk eller dynamisk) |
| ipv6PrivateIPAddressType |Allokeringsmetoden som används för privat IP-adress (dynamisk). IPv6 har endast stöd för dynamisk allokering. |
| numberOfInstances |Antalet belastningsutjämnade instanser som driftsattes av mallen |
| ipv4PublicIPAddressName |Ange DNS-namn som du vill använda för att kommunicera med den offentliga IPv4-adressen för belastningsutjämnaren. |
| ipv4PublicIPAddressType |Allokeringsmetoden används för den offentliga IP-adressen (statisk eller dynamisk) |
| Ipv6PublicIPAddressName |Ange DNS-namn som du vill använda för att kommunicera med den offentliga IPv6-adressen för belastningsutjämnaren. |
| ipv6PublicIPAddressType |Allokeringsmetoden som används för offentliga IP-adress (dynamisk). IPv6 har endast stöd för dynamisk allokering. |
| lbName |Ange namnet på belastningsutjämnaren. Det här namnet visas i portalen eller används för att referera till den med ett CLI eller PowerShell-kommando. |

De återstående variablerna i mallen innehåller härledda värdena som tilldelas när Azure skapar resurserna. Ändra inte de här variablerna.
