---
title: Distribuera en Internetriktade belastningsutjämnare med IPv6 - Azure-mall | Microsoft Docs
description: Så här distribuerar du IPv6-stöd för Azure belastningsutjämnare och belastningsutjämnade virtuella datorer.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: IPv6, azure belastningsutjämnare, dual stack, offentlig IP-adress, inbyggd ipv6, mobil, iot
ms.assetid: 2998e943-13fc-4ea9-a68c-875e53a08db3
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 431b43979ac364d943c58c40b4199b7f30f9acf6
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
ms.locfileid: "30263086"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Distribuera en Internetriktade belastningsutjämnare-lösning med IPv6 med en mall

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Mall](load-balancer-ipv6-internet-template.md)



En Azure belastningsutjämnare är en Layer 4-belastningsutjämnare (TCP, UDP). Belastningsutjämnaren ger hög tillgänglighet genom att distribuera inkommande trafik mellan felfria tjänstinstanser i molntjänster eller virtuella datorer i en belastningsutjämningsuppsättning. Azure belastningsutjämnare kan även presentera dessa tjänster på flera portar, flera IP-adresser eller både och.

## <a name="example-deployment-scenario"></a>Exempelscenario för distribution

Följande diagram illustrerar nätverkslösning för belastningsutjämning som distribueras med hjälp av mallen exempel som beskrivs i den här artikeln.

![Belastningsutjämningsscenario](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

I det här scenariot skapar du följande Azure-resurser:

* ett virtuellt nätverksgränssnitt för varje virtuell dator med både IPv4 och IPv6-adresser som tilldelats
* en Internetriktade belastningsutjämnare med en IPv4 och IPv6-offentlig IP-adress
* två läsa in belastningsutjämning regler för att mappa offentliga VIP till privata slutpunkter
* en Tillgänglighetsuppsättning med två virtuella datorer
* två virtuella datorer (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>Distribuera mallen med hjälp av Azure portal

Den här artikeln refererar till en mall som har publicerats i den [Azure Quickstart mallar](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) gallery. Du kan hämta en mall från galleriet eller starta distributionen i Azure direkt från galleriet. Den här artikeln förutsätter att du har hämtat mallen till den lokala datorn.

1. Öppna Azure-portalen och logga in med ett konto som har behörighet att skapa virtuella datorer och nätverksresurser i en Azure-prenumeration. Om du inte använder befintliga resurser, måste kontot också behörighet att skapa en resursgrupp och ett lagringskonto.
2. Klicka på ”+ ny” från menyn och anger ”mall” i sökrutan. Välj ”malldistribution” i sökresultatet.

    ![lb-ipv6-portal-step2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. I den allt bladet, klickar du på ”malldistribution”.

    ![lb-ipv6-portal-step3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Klicka på ”Skapa”.

    ![lb-ipv6-portal-step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Klicka på ”Redigera mallen”. Ta bort det befintliga innehållet och kopiera och klistra in i hela innehållet i mallfilen (som innehåller början och sluta {}) och sedan klicka på ”Spara”.

    > [!NOTE]
    > Om du använder Microsoft Internet Explorer när du klistrar du in visas en dialogruta där du ombeds att tillåta åtkomst till Urklipp. Klicka på ”Tillåt åtkomst”.

    ![lb-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Klicka på ”Redigera parametrar”. Ange värden för varje riktlinjerna i avsnittet mallen parametrar i bladet parametrar och sedan på ”Spara” för att stänga bladet parametrar. Välj din prenumeration, en befintlig resursgrupp eller skapa en anpassad distribution-bladet. Om du skapar en resursgrupp, väljer du en plats för resursgruppen. Klicka sedan på **juridiska villkor**, klicka på **inköp** för de juridiska villkoren. Azure börjar distribuera resurserna. Det tar flera minuter att distribuera alla resurser.

    ![lb-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Mer information om dessa parametrar finns i [mallparametrar och variabler](#template-parameters-and-variables) senare i den här artikeln.

7. Klicka på Bläddra om du vill se de resurser som skapas av mallen, bläddra nedåt i listan tills du se ”resursgrupper” och klickar sedan.

    ![lb-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Klicka på namnet på resursgruppen som du angav i steg 6 på resursbladet grupper. Du kan se en lista över alla resurser som har distribuerats. Om alla gått bra ska det stå ”Succeeded” under ”senaste distribution”. Om inte, kan du kontrollera att det konto du använder har behörighet att skapa de nödvändiga resurserna.

    ![lb-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Om du bläddrar resursgrupperna omedelbart när du har slutfört steg 6 Visa ”senaste distribution” status för ”Deploying” medan resurserna som distribueras.

9. Klicka på ”myIPv6PublicIP” i listan över resurser. Du kan se att det finns en IPv6-adress under IP-adress och att dess DNS-namn är du det angivna värdet för parametern dnsNameforIPv6LbIP i steg 6. Den här resursen är offentliga IPv6-adress och värddatornamn namnet som har åtkomst till Internet-klienter.

    ![lb-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Kontrollera anslutning

När mallen har distribuerats kan du verifiera anslutningen genom att slutföra följande uppgifter:

1. Logga in på Azure-portalen och Anslut till var och en av de virtuella datorerna som skapats med mallen distribueras. Om du har distribuerat en Windows Server-VM köra ipconfig/alla från en kommandotolk. Du ser att de virtuella datorerna har både IPv4 och IPv6-adresser. Om du har distribuerat virtuella Linux-datorer måste du konfigurera Linux-operativsystem för att ta emot dynamiska IPv6-adresser med hjälp av anvisningarna för Linux-distribution.
2. Initiera en anslutning till den offentliga IPv6-adressen för belastningsutjämnaren från en klient för IPv6-Internet-anslutna. Du kan installera en webbserver som Microsoft Internet Information Services (IIS) på varje virtuell dator för att bekräfta att belastningsutjämnaren belastningsutjämning mellan de två virtuella datorerna. Standardwebbsidan på varje server kan innehålla text ”Server0” eller ”Server1” för att identifiera den. Öppna en webbläsare på en IPv6-Internet-ansluten klient och bläddra till värdnamnet för parametern dnsNameforIPv6LbIP av belastningsutjämnare för att bekräfta slutpunkt till slutpunkt IPv6-anslutning till varje virtuell dator. Om du bara ser webbsidan från bara en server kan du behöva rensa webbläsarens cacheminne. Öppna flera privata webbläsarsessioner. Du bör se svar från varje server.
3. Initiera en anslutning till den offentliga IPv4-adressen för belastningsutjämnaren från en klient för IPv4-Internet-anslutna. För att bekräfta att belastningsutjämnaren är två virtuella datorer för belastningsutjämning, kan du testa med hjälp av IIS, enligt anvisningarna i steg 2.
4. Starta en utgående anslutning till en IPv6- eller IPv4-ansluten till Internet-enhet från varje virtuell dator. I båda fallen är käll-IP som setts av målenheten den offentliga IPv4- eller IPv6-adressen för belastningsutjämnaren.

> [!NOTE]
> ICMP för både IPv4 och IPv6 är blockerad i Azure-nätverk. Därför ICMP-verktyg som pinga alltid att misslyckas. Använda ett TCP-alternativ, till exempel TCPing eller PowerShell Test-NetConnection cmdlet för att testa anslutningen. Observera att IP-adresser som visas i diagrammet är exempel på värden som kan visas. Eftersom IPv6-adresser tilldelas dynamiskt adresserna visas varierar och kan variera beroende på region. Dessutom är det vanligt för offentliga IPv6-adressen på belastningsutjämnaren ska börja med ett annat prefix än privata IPv6-adresser i backend-poolen.

## <a name="template-parameters-and-variables"></a>Mallparametrar och variabler

En Azure Resource Manager-mall innehåller flera variabler och parametrar som du kan anpassa efter dina behov. Variabler används för fasta värden som du inte vill att en användare kan ändra. Parametrar som används för värden som du vill att en användare att ange när mallen distribueras. Exempel mallen har konfigurerats för det scenario som beskrivs i den här artikeln. Du kan anpassa efter behoven för din miljö.

Exempel-mallen som används i den här artikeln innehåller följande variabler och parametrar:

| Parametern / Variable | Anteckningar |
| --- | --- |
| adminUsername |Ange namnet på det administratörskonto som används för att logga in på de virtuella datorerna med. |
| adminPassword |Ange lösenordet för det administratörskonto som används för att logga in på de virtuella datorerna med. |
| dnsNameforIPv4LbIP |Ange DNS-värdnamn som du vill tilldela som det offentliga namnet på belastningsutjämnaren. Det här namnet matchar belastningsutjämnarens offentlig IPv4-adress. Namnet måste vara gemener och matchar regex: ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP |Ange DNS-värdnamn som du vill tilldela som det offentliga namnet på belastningsutjämnaren. Det här namnet matchar belastningsutjämnarens offentliga IPv6-adress. Namnet måste vara gemener och matchar regex: ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. Det kan vara samma namn som IPv4-adress. När en klient skickar en DNS-fråga för det här namnet Azure returnerar registrerar A- och AAAA när namnet delas. |
| vmNamePrefix |Ange namnprefixet VM. Mallen till en siffra (0, 1, etc.) med namnet när de virtuella datorerna har skapats. |
| nicNamePrefix |Ange namnprefixet network interface. Mallen till en siffra (0, 1, etc.) med namnet när nätverksgränssnitten skapas. |
| storageAccountName |Ange namnet på ett befintligt lagringskonto eller ange namnet på en ny skapas av mallen. |
| availabilitySetName |Ange sedan namnet på tillgänglighetsuppsättningen som ska användas med de virtuella datorerna |
| addressPrefix |Adressprefixet som används för att definiera adressintervall för det virtuella nätverket |
| subnetName |Namnet på undernät i skapas för VNet |
| subnetPrefix |Adressprefixet som används för att definiera adressintervallet i undernätet |
| vnetName |Ange namn för det VNet som används av de virtuella datorerna. |
| ipv4PrivateIPAddressType |Allokeringsmetoden används för den privata IP-adressen (statisk eller dynamisk) |
| ipv6PrivateIPAddressType |Allokeringsmetoden som används för privat IP-adress (dynamisk). IPv6 har endast stöd för dynamisk allokering. |
| numberOfInstances |Antalet instanser för Utjämning av nätverksbelastning distribueras av mallen |
| ipv4PublicIPAddressName |Ange DNS-namn som du vill använda för att kommunicera med den offentliga IPv4-adressen för belastningsutjämnaren. |
| ipv4PublicIPAddressType |Allokeringsmetoden används för den offentliga IP-adressen (statisk eller dynamisk) |
| Ipv6PublicIPAddressName |Ange DNS-namn som du vill använda för att kommunicera med den offentliga IPv6-adressen för belastningsutjämnaren. |
| ipv6PublicIPAddressType |Allokeringsmetod som används för den offentliga IP-adressen (dynamisk). IPv6 har endast stöd för dynamisk allokering. |
| lbName |Ange namnet på belastningsutjämnaren. Det här namnet visas i portalen eller används för att referera till den med CLI eller PowerShell-kommando. |

De återstående variablerna i mallen innehåller härledda värden som tilldelas när Azure skapar resurser. Ändra inte dessa variabler.
