---
title: Microsoft Azure FXT Edge Filer nätverksanslutningar och -strömförsörjning
description: Hur du kabelanslut nätverksportar och kopplar ström för Azure FXT Edge Filer maskinvara
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: ae179e8ce2a2ba772a7fb14825660e0fff9e7410
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542916"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Självstudier: Kontrollera nätverksanslutningar och ange kraften att noden Azure FXT Edge Filer

Den här självstudien lär du dig hur du kabelanslut nätverksanslutningar för en Azure FXT Edge Filer maskinvara nod.

I den här kursen lär du dig: 

> [!div class="checklist"]
> * Så här väljer du typ av nätverkskabeln för din miljö
> * Hur du ansluter en Azure FXT Edge Filer-nod till datacenternätverket
> * Hur du dirigerar kablar via kabel hanteringsarmen (CMA)
> * Hur du ansluter power till rackbaserade enheten och slår på den

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien ska Azure FXT Edge Filer installeras i en standard utrustning rack. CMA bör vara installerad på noden för filer. 

## <a name="identify-ports"></a>Identifiera portar

Identifiera de olika portarna på baksidan av din Azure FXT Edge-Filer. 
 
![Baksidan av en kablade enhet](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Kabelansluta enheten

* Ansluta RJ-45-portar till ditt datacenter nätverkskällan enligt beskrivningen i [nätverksportar](#network-ports).  
* På ett säkert sätt ansluta den [iDRAC port](#idrac-port) till ett separat nätverk med en säker DHCP-server. 
* Använda USB-portar och VGA-port för att ansluta ett tangentbord och bildskärm till noden för installationen. Du måste starta om noden och [ställa in ett initialt lösenord](fxt-node-password.md) att aktivera noden är andra portar. Läs [Ange inledande lösenord](fxt-node-password.md) mer information. 

Den här artikeln beskriver också hur du [ansluta nätström](#connect-power-cables) för noden. 

Den här artikeln beskriver också hur du ansluter till noden [serieport](#serial-port-only-when-necessary)om det behövs för specialiserad felsökning. 

### <a name="network-ports"></a>Nätverksportar 

Varje nod i Azure FXT Edge Filer innehåller följande nätverksportar: 

* Sex höghastighetsnätverk 25GbE/10GbE dubbel hastighet data-portar: 

  * Fyra portar som tillhandahålls av två med dubbla portar plugin-programmet nätverkskort
  * Två portar som tillhandahålls av moderkortet mezzanine-nätverkskort 

* Två 1GbE-portar som tillhandahålls av moderkortet mezzanine-nätverkskort 

För snabba 25GbE/10 GbE-dataportar har standard SFP28-kompatibla burar. Om du vill använda optiska kablar, måste du installera SFP28 optiska sändtagare moduler (inte anges).

1GbE-portar har Standardanslutningar RJ-45.

En fullständig lista över stöds kablar, växlar och mottagarna, finns det [Cavium FastlinQ 41000 serien samverkan matrisen](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

Vilken typ av anslutningar till att använda systemets beror på din datacentermiljö.

* Om du ansluter till ett nätverk för 25GbE, kabel – var och en av portarna som snabba data med någon av följande kabeltyper av:

  * Optiska kabel och SFP28 optiska sändtagare med 25GbE eller dubbel hastighet 25GbE/10 GbE-funktion
  * SFP28 typ 25GbE-kompatibla direktansluten twinaxial kabel

* Om du ansluter till ett 10 GbE-nätverk, kabel – var och en av portarna som snabba data med något av följande: 

  * Optiska kabel och SFP28 optiska sändtagare med 10 GbE- eller dubbel hastighet 25GbE/10 GbE-funktionen.
  * SFP28 typ 25GbE-kompatibla direktansluten twinaxial kabel
  * SFP28 typ 10 GbE-kompatibla direktansluten twinaxial kabel

* Nätverksportar 1GbE som används för hantering av klustertrafik. Kontrollera den **använda 1Gb mgmt nätverk** när du skapar klustret om du vill skapa ett fysiskt separata nätverk för klusterkonfiguration (som beskrivs i [konfigurera hanteringsnätverket](fxt-cluster-create.md#configure-the-management-network)). Kabelanslut portar med standard dra Cat5 eller bättre kabel enligt beskrivningen i listan stöds kablar.

  Du kan lämna 1GbE portarna uncabled om du planerar att använda hög hastighet portar för all trafik. Som standard används inte nätverksportar 1GbE om en högre hastighet dataport är tillgänglig.  

### <a name="idrac-port"></a>iDRAC port  

Den port som är märkt iDRAC är en 1Gb-anslutning som tillåter kommunikation med en RAS-styrenhet används för maskinvaruhantering av och övervakning. FXT programvaran använder Intelligent Platform Management Interface (IPMI) med den här styrenheten för felsökning och återställning. Du kan använda inbyggt [iDRAC gränssnittet](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) att övervaka maskinvara under den här porten. iDRAC och IPMI-åtkomst är aktiverat som standard. 

> [!Note]
> IDRAC porten kan kringgå operativsystemet och interagera direkt med maskinvaran på noden. 

Använd dessa säkerhetsstrategier när du ansluter och konfigurerar iDRAC-port:

* Bara ansluta iDRAC portar till ett nätverk som är fysiskt avgränsade från nätverket används för åtkomst till klustret.
* Ange ett administratörslösenord för säkert iDRAC på varje nod. Du måste ange det här lösenordet för att aktivera maskinvaran - följer du anvisningarna i [ange maskinvara lösenord](fxt-node-password.md).
* Portkonfiguration för standard iDRAC använder DHCP- och IPv4 för IP-adresstilldelning. Kontrollera att din DHCP-miljö är väl skyddad och att anslutningarna är begränsad mellan DHCP-klienter och DHCP-servern. (Kontrollpanelen i klustret ingår inställningar om du vill ändra nodernas adress metod när du har skapat klustret.)
* Lämna porten iDRAC inställd på ”dedikerad läget” (standard), vilket begränsar iDRAC/IPMI nätverkstrafik till den dedikerade RJ-45-porten.

Porten som iDRAC kräver inte en snabb nätverksanslutning.
  
### <a name="serial-port-only-when-necessary"></a>Seriell port (vid behov)

I vissa situationer kan kan Microsoft Service och Support visa att ansluta en terminal till en nod serieport att diagnostisera problem.  

Att koppla konsolen:

1. Leta upp den seriella porten (COM1) på baksidan av noden FXT Edge-Filer.
1. Använd en null-modemkabel för att ansluta den seriella porten till en terminal som konfigurerats för ANSI-115200-8N1.
1. Logga in på konsolen och vidta andra åtgärder enligt anvisningarna från supportpersonalen.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Väg kablar i hanteringsarmen kabel (CMA)

Varje nod i Azure FXT Edge Filer levereras med en valfri kabel hanteringsarmen. CMA förenklar kabel Routning och ger enklare åtkomst till baksidan av chassit utan att behöva koppla från kablar. 

Följ dessa instruktioner för att dirigera kablar via CMA: 

1. Med de koppla radbryter som tillhandahålls kan paketera kablarna tillsammans som de anger och avsluta korgar så att de inte stör ligger intill varandra system (1).
1. Dirigera kabel paket via de inre och yttre korgar (2) med CMA i tjänsten position.
1. Använda förinstallerade remmar för hook och slinga början eller slutet av korgar för att skydda kablarna (3).
1. Hamnar CMA tillbaka på rätt plats på fack (4).
1. Installera status indikator kabel på baksidan av systemet och skydda kabeln genom Routning via CMA. Anslut den andra änden av kabeln till hörnet av yttre CMA korgen (5). 

   > [!CAUTION]
   > Skydda alla slack status indikator kabeln för att undvika eventuella skador från skjuter ut kablar efter routning denna kabel via CMA. 

![Bild av CMA med kablar installerad](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
>  Om du inte har installerat CMA använder två hook och loopa remmar i rail kit att dirigera kablar på baksidan av systemet.
> 
>  1. Leta upp de yttre CMA hakparenteserna båda racklister inre sidor.
>  2. Paketera kablarna försiktigt, att de Rensa av system-anslutningarna till vänster och höger.
>  3. Tråd hook och slinga remmar via tooled fack på yttre CMA hakparenteserna på båda sidor av systemet för att skydda en kabel-paket.
> 
>     ![Kablar dirigeras utan en CMA](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Om IP-adresskraven

IP-adresser som hanteras av klusterprogramvaran för maskinvarunoder i Azure FXT Edge Filer hybrid storage cacheminne.

Varje nod kräver minst en IP-adress, men noden adresser tilldelas när noder läggs till eller tas bort från klustret. 

Det totala antalet IP-adresser som krävs beror på antalet noder i som utgör ditt cacheminne. 

Konfigurera IP-adressintervall genom att använda programvaran på Kontrollpanelen när noderna har installerats. Mer information, [samla in information för klustret](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Ansluta strömkablar

Varje Azure FXT Edge Filer nod använder två power anger enheter (PSUs). 

> [!TIP] 
> Om du vill dra nytta av de två redundanta PSUs, koppla varje AC-strömkabeln till en power distributionsenhet (PDU) på en oberoende gren-krets.  
> 
> Du kan använda en UPS för att driva PDU extra skydd. 

1. Anslut de inkluderade strömkablar till PSUs i chassit. Se till att sladdar och PSUs helt isatt. 
1. Koppla strömkablar till kraftfördelningsenheter på utrustning rack. Använd om möjligt två separata strömkällor för alla två kablar. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Starta en Azure FXT Edge Filer-nod

Om du vill starta noden, trycker du på strömknappen på framsidan av systemet. Det är knappen på höger sida på Kontrollpanelen. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Stäng av en Azure FXT Edge Filer-nod

Strömknappen kan användas för att stänga av systemet under testningen och innan du lägger till det till ett kluster. När en nod i Azure FXT Edge Filer är på som en del av ett kluster, bör du dock använda Kontrollpanelen klusterprogramvaran för att stänga av maskinvaran. Läs [så säkert stänga av Azure FXT Edge Filer maskinvara](fxt-power-off.md) mer information. 

## <a name="next-steps"></a>Nästa steg

När du slutför kablar ström på varje nod för maskinvara och initiera dem genom att ange sina lösenord rot. 
> [!div class="nextstepaction"]
> [Ange inledande lösenord](fxt-node-password.md)
