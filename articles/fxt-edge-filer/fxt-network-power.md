---
title: 'Självstudiekurs: Anslut kablar till en Azure FXT Edge Filer'
description: Så här kabelr du nätverksportarna och ansluter ström för Azure FXT Edge Filer-maskinvara
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 1e3c60fd955bd899955c46b7670acd3f088d0f86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239786"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Självstudiekurs: Skapa nätverksanslutningar och strömförsörjning till Azure FXT Edge Filer-noden

Den här självstudien lär dig hur du kabel nätverksanslutningar för en Azure FXT Edge Filer hårdvara nod.

I den här kursen lär du dig: 

> [!div class="checklist"]
> * Så här väljer du vilken typ av nätverkskabel för din miljö
> * Så här ansluter du en Azure FXT Edge Filer-nod till ditt datacenternätverk
> * Hur man kan dra kablar genom kabelhanteringsarmen (CMA)
> * Så här ansluter du ström till den rackade enheten och slår på den

## <a name="prerequisites"></a>Krav

Innan du startar den här självstudien bör Azure FXT Edge Filer installeras i ett standardprogram för utrustning. CMA ska installeras på filer-noden. 

## <a name="identify-ports"></a>Identifiera portar

Identifiera de olika portarna på baksidan av dina Azure FXT Edge Filer. 
 
![Baksidan av en kabelanordning](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Kabelansluta enheten

* Anslut RJ-45-portarna till datacentrets nätverkskälla enligt beskrivningen i [Nätverksportar](#network-ports).  
* Anslut [iDRAC-porten](#idrac-port) på ett säkert sätt till ett separat nätverk med en säker DHCP-server. 
* Använd USB-portarna och VGA-porten för att ansluta ett tangentbord och bildskärm till noden för första installationen. Du måste starta noden och [ange ett första lösenord](fxt-node-password.md) för att aktivera nodens andra portar. Läs [Ange inledande lösenord](fxt-node-password.md) för mer information. 

I den här artikeln beskrivs också hur du [ansluter växelström](#connect-power-cables) för noden. 

I den här artikeln beskrivs också hur du ansluter till nodens [seriella port](#serial-port-only-when-necessary)om det behövs för specialiserad felsökning. 

### <a name="network-ports"></a>Nätverksportar 

Varje Azure FXT Edge Filer-nod innehåller följande nätverksportar: 

* Sex höghastighetsdataportar med 25 GbE/10 GbE med dubbla hastigheter: 

  * Fyra portar med två plug-in-nätverkskort med dubbla portar
  * Två portar från moderkortet mezzanine nätverkskort 

* Två 1GbE-portar från moderkortet mezzanine nätverkskort 

De snabba 25 GbE/10GbE-dataportarna har standard-SFP28-kompatibla burar. Om du vill använda optiska kablar måste du installera optiska SFP28-sändtagaremoduler (medföljer ej).

1 GbE-portarna har standard-RJ-45-kontakter.

En fullständig lista över kablar, brytare och sändtagare som stöds finns i [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

Vilken typ av anslutningar som ska användas för systemet beror på din datacentermiljö.

* Om du ansluter till ett 25 GbE-nätverk kabel varje höghastighetsdataportar med någon av följande kabeltyper:

  * Optisk kabel och SFP28 optisk sändtagare med 25 GbE eller dubbel hastighet 25GbE/10GbE kapacitet
  * SFP28 typ 25GbE-kompatibel direkt anslutning twinaxial kabel

* Om du ansluter till ett 10 GbE-nätverk kabel var och en av höghastighetsdataportarna med något av följande: 

  * Optisk kabel och SFP28 optisk sändtagare med 10GbE eller dubbla hastighet 25GbE/10GbE kapacitet.
  * SFP28 typ 25GbE-kompatibel direkt anslutning twinaxial kabel
  * SFP28 typ 10GbE-kompatibel direkt anslutning twinaxial kabel

* 1 GbE-nätverksportarna används för klusterhanteringstrafik. Kontrollera alternativet **Använd 1 Gb mgmt-nätverk** när du skapar klustret om du vill skapa ett fysiskt separat nätverk för klusterkonfiguration (beskrivs i [Konfigurera hanteringsnätverket](fxt-cluster-create.md#configure-the-management-network)). Kabel portarna med standard Cat5 eller bättre kabel som beskrivs i listan över kablar som stöds.

  Du kan lämna 1 GbE-portarna oerbara om du planerar att använda höghastighetsportar för all trafik. Som standard används inte 1 GbE-nätverksportarna om det finns en dataport med högre hastighet.  

### <a name="idrac-port"></a>iDRAC-port  

Porten märkt iDRAC är en 1Gb-anslutning som möjliggör kommunikation med en fjärråtkomststyrenhet som används för maskinvaruhantering och övervakning. FXT-programvaran använder IPMI (Intelligent Platform Management Interface) med den här styrenheten för felsökning och återställning. Du kan använda det inbyggda [iDRAC-gränssnittet](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) för att övervaka maskinvaran via den här porten. iDRAC- och IPMI-åtkomst är aktiverade som standard. 

> [!Note]
> IDRAC-porten kan kringgå operativsystemet och interagera direkt med maskinvaran på noden. 

Använd dessa säkerhetsstrategier när du ansluter och konfigurerar iDRAC-porten:

* Anslut endast iDRAC-portar till ett nätverk som är fysiskt separerat från det datanätverk som används för att komma åt klustret.
* Ange ett säkert iDRAC-administratörslösenord på varje nod. Du måste ange det här lösenordet för att aktivera maskinvaran – följ instruktionerna i [Ange maskinvarulösenord](fxt-node-password.md).
* Standardkonfigurationen för iDRAC-port använder DHCP och IPv4 för IP-adresstilldelning. Kontrollera att DHCP-miljön är väl skyddad och att anslutningarna är begränsade mellan DHCP-klienter och DHCP-servern. (Klusterkontrollpanelen innehåller inställningar för att ändra nodernas adresskonfigurationsmetod när du har skapat klustret.)
* Lämna iDRAC-porten inställd på "dedikerat läge" (standard), vilket begränsar iDRAC/IPMI-nätverkstrafiken till den dedikerade RJ-45-porten.

IDRAC-porten kräver ingen höghastighetsanslutning.
  
### <a name="serial-port-only-when-necessary"></a>Seriell port (endast vid behov)

I vissa situationer kan Microsoft Service och support tala om för dig att ansluta en terminal till en nods seriella port för att diagnostisera ett problem.  

Så här ansluter du konsolen:

1. Leta reda på den seriella (COM1) porten på baksidan av FXT Edge Filer-noden.
1. Använd en null-modemkabel för att ansluta den seriella porten till en terminal som konfigurerats för ANSI-115200-8N1.
1. Logga in på konsolen och vidta andra åtgärder enligt anvisningar från supportpersonalen.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Dra kablar i kabelhanteringsarmen (CMA)

Varje Azure FXT Edge Filer-nod levereras med en kabelhanteringsarm som tillval. CMA förenklar kabeldragningen och ger enklare åtkomst till baksidan av chassit utan att behöva koppla bort kablar. 

Följ dessa instruktioner för att dra kablarna genom CMA: 

1. Med hjälp av de medföljande slipswraparna buntar du ihop kablarna när de kommer in i och ut ur korgarna så att de inte stör intilliggande system (1).
1. Med CMA i serviceläge, dra kabelbunten genom de inre och yttre korgarna (2).
1. Använd de förinstallerade krok- och ögelbanden i vardera änden av korgarna för att säkra kablarna (3).
1. Svinga CMA på plats på facket (4).
1. Installera statusindikatorkabeln på baksidan av systemet och säkra kabeln genom att dra den genom CMA. Fäst den andra änden av kabeln i hörnet av den yttre CMA-korgen (5). 

   > [!CAUTION]
   > För att undvika eventuella skador från utskjutande kablar, säkra eventuellt slack i statusindikatorkabeln efter att ha dirigerat kabeln genom CMA. 

![Illustration av CMA med installerade kablar](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
>  Om du inte har installerat CMA, använd de två krok- och ögelbanden som finns i järnvägssatsen för att dra kablarna på baksidan av systemet.
> 
>  1. Placera de yttre CMA-fästena på insidan av båda rackflänsarna.
>  2. Bunta kablarna försiktigt och dra dem bort från systemkontakterna till vänster och höger sida.
>  3. Trä haken och öglan remmar genom de verktygsstysnlor på de yttre CMA fästen på varje sida av systemet för att säkra kabelbuntar.
> 
>     ![Kablar som dras utan CMA](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Om IP-adresskrav

För maskinvarunoder i en Azure FXT Edge Filer hybridlagringscache hanteras IP-adresser av klusterprogramvaran.

Varje nod kräver minst en IP-adress, men nodadresser tilldelas när noder läggs till eller tas bort från klustret. 

Det totala antalet IP-adresser som krävs beror på antalet noder i cacheminnet. 

Konfigurera IP-adressintervallet med hjälp av Kontrollpanelens programvara när noderna har installerats. Mer information finns [i Samla in information för klustret](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Anslut strömkablar

Varje Azure FXT Edge Filer-nod använder två nätaggregat. 

> [!TIP] 
> Om du vill dra nytta av de två redundanta nätaggregaten ansluter du varje nätkabel till en kraftdistributionsenhet (PDU) på en oberoende grenkrets.  
> 
> Du kan använda en UPS för att driva pdus för extra skydd. 

1. Anslut de medföljande nätsladdarna till nätaggregaten i chassit. Se till att sladdar och nätaggregat sitter helt. 
1. Fäst nätsladdarna på kraftfördelningsenheterna på utrustningsstället. Använd om möjligt två separata strömkällor för de två sladdarna. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Ström på en Azure FXT Edge Filer-nod

För att driva upp noden trycker du på strömbrytaren på systemets framsida. Knappen är på kontrollpanelen på höger sida. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Stänga av en Azure FXT Edge Filer-nod

Strömbrytaren kan användas för att stänga av systemet under testningen och innan du lägger till det i ett kluster. Men när en Azure FXT Edge Filer-nod används som en del av ett kluster bör du använda klusterkontrollpanelens programvara för att stänga av maskinvaran. Läs [så här stänger du av Azure FXT Edge Filer-maskinvara](fxt-power-off.md) på ett säkert sätt för mer information. 

## <a name="next-steps"></a>Nästa steg

När du har kabeldragning maskinvaran slår du på var och en av noderna och initierar dem genom att ange deras rotlösenord. 
> [!div class="nextstepaction"]
> [Ange inledande lösenord](fxt-node-password.md)
