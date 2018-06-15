---
title: Ändra StorSimple 8000-serien enhetskonfigurationen | Microsoft Docs
description: Beskriver hur du kan använda Enhetshanteraren för StorSimple-tjänsten för att konfigurera om en StorSimple-enhet som redan har distribuerats.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 13ff24c24a881297775fa5f65821e53ceb83c351
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875160"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Använda Enhetshanteraren för StorSimple-tjänsten för att ändra konfigurationen för StorSimple-enhet

## <a name="overview"></a>Översikt

Azure-portalen **Enhetsinställningar** under den **inställningar** bladet innehåller alla enheter-parametrar som du kan konfigurera om på en StorSimple-enhet som hanteras av en tjänst för StorSimple Enhetshanteraren . Den här självstudiekursen beskrivs hur du kan använda den **inställningar** bladet för att utföra följande uppgifter för enhetsnivå:

* Ändra eget namn för enhet
* Ändra tid Enhetsinställningar
* Tilldela en sekundär DNS
* Ändra nätverksgränssnitt
* Tilldela IP-adresser eller växla

## <a name="modify-device-friendly-name"></a>Ändra eget namn för enhet

Du kan använda Azure-portalen för att ändra namnet på en enhet och tilldela den ett unikt namn för ditt val. Använd den **allmänna inställningar** bladet på enheten för att ändra det egna namnet för enheten. Det egna namnet får innehålla tecken och kan innehålla högst 64 tecken.

> [!NOTE] 
> Du kan bara ändra enhetsnamnet i Azure-portalen innan enheten installationen är klar. När den minimala Enhetsinstallationen är klar kan ändra du inte namnet på en enhet.

![Enhetens namn i allmänhet inställningar](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

En StorSimple-enhet som är ansluten till tjänsten StorSimple Enhetshanteraren tilldelas ett standardnamn. Standardnamnet visar normalt serienumret på enheten. En enhet för standardnamnet är 15 tecken långt, till exempel 8600-SHX0991003G44HT anger till exempel följande:

* **8600** – anger enhetsmodellen.
* **SHX** – anger anläggning.
* **0991003** -indikerar en viss produkt.
* **G44HT**-de 5 senaste siffrorna ökas för att skapa unika serienummer. Det kan inte vara en sekventiell uppsättning.

## <a name="modify-device-description"></a>Ändra enhetsbeskrivning

Använd den **allmänna inställningar** bladet på enheten för att ändra enhetsbeskrivningen.

![Enhetsbeskrivning i allmänna inställningar](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

En enhetsbeskrivning identifierar vanligtvis ägaren och den fysiska platsen för enheten. Beskrivningsfältet måste innehålla färre än 256 tecken.

## <a name="modify-time-settings"></a>Ändra inställningarna för tid

Din enhet måste synkronisera tiden för att kunna autentisera med din molntjänstleverantör för lagring. Använd den **allmänna inställningar** bladet på enheten för att ändra inställningarna för tid för enheten.

![Enhetsbeskrivning i allmänna inställningar](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Välj tidszonen från den nedrullningsbara listan. Du kan ange upp till två Network Time Protocol (NTP) servrar:

 - **Primär NTP-server** -konfigurationen som krävs och anges när du använder Windows PowerShell för StorSimple för att konfigurera din enhet. Du kan ange standard Windows Server **time.windows.com** som NTP-server. Du kan visa primär NTP-serverkonfigurationen via Azure-portalen, men du måste använda Windows PowerShell-gränssnittet för att ändra den. Använd den `Set-HcsNTPClientServerAddress` cmdlet och ändra primär NTP-server i din enhet. Mer information går du till synxtax för [Set-HcsNTPClientServerAddress] (https://technet.microsoft.com/library/dn688138.aspx) cmdlet.

- **Sekundär NTP-server** -konfigurationen är valfritt. Du kan använda portalen för att konfigurera en sekundär NTP-server.

När du konfigurerar NTP-server måste du kontrollera att ditt nätverk tillåter NTP-trafik skickas från ditt datacenter till Internet. När du anger en offentlig NTP-server, måste du se till att nätverkets brandväggar och andra säkerhetsenheter är konfigurerad för att tillåta NTP-trafik skickas till och från det externa nätverket. Om dubbelriktad NTP-trafik inte tillåts, måste du använda en intern NTP-server (en Windows-domänkontrollant ger den här funktionen). Om enheten inte kan synkronisera tiden, kanske den inte kommunicera med lagringsprovidern i molnet.

Om du vill se en lista över allmänna NTP-servrar, gå till den [NTP-servrar Web](http://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Vad händer om enheten är distribuerat i en annan tidszon?

Om enheten har distribuerats i en annan tidszon, ändras tidszon enhet. Med hänsyn till att alla principer för säkerhetskopiering använder enheten tidszon, justeras automatiskt principer för säkerhetskopiering i enlighet med den nya tidszonen. Inga användaråtgärder krävs.

## <a name="modify-dns-settings"></a>Ändra DNS-inställningar

En DNS-server används när enheten försöker kommunicera med din molntjänstleverantör för lagring. Använd den **nätverksinställningar** bladet på enheten för att visa och ändra de konfigurerade DNS-inställningarna. 

![DNS-inställningar i nätverksinställningar](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

För hög tillgänglighet krävs att konfigurera både primärt och sekundära DNS-servrar under den inledande distributionen.

**Primär DNS-server** -du använder Windows PowerShell för StorSimple för att först ange primär DNS-servern under den första installationen. Du kan konfigurera om den primära DNS-servern endast via Windows PowerShell-gränssnittet. Använd den `Set-HcsDNSClientServerAddress` cmdlet och ändra den primära DNS-servern för enheten. Mer information finns i synxtax för [Set HcsDNSClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet.

**Sekundär DNS-server** – om du vill ändra den sekundära DNS-servern använder den `Set-HcsDNSClientServerAddress` cmdlet i Windows PowerShell-gränssnittet för enheten eller **nätverksinställningar** bladet för din StorSimple-enhet i Azure-portalen.

Utför följande steg om du vill ändra den sekundära DNS-servern i Azure-portalen.

1. Gå till StorSimple Device Manager-tjänsten. Välj i listan med enheter och klicka på enheten.

2. I den **inställningar** gå till bladet **Enhetsinställningar > nätverk**. Detta öppnar den **nätverksinställningar** bladet. Klicka på **DNS-inställningarna** panelen. Ändra sekundär DNS-serverns IP-adress.

    ![Ändra sekundär DNS server IP adderss](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. I kommandofältet klickar du på **spara** och när du uppmanas att bekräfta på **OK**.

    ![Spara och bekräfta ändringar](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Ändra nätverksgränssnitt

Enheten har sex enheten nätverksgränssnitt, varav fyra är 1 GbE och varav två 10 GbE. Gränssnitten är märkta som DATA 0 – DATA 5. DATA 0 DATA 1, DATA 4 och 5 DATA är 1 GbE DATA 2 och DATA 3 är 10 GbE-nätverkskort.

Använd den **nätverksinställningar** bladet konfigurera varje gränssnitt som ska användas.

![Konfigurera nätverksgränssnitt via nätverksinställningar](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

För att säkerställa hög tillgänglighet, rekommenderar vi att du har minst två iSCSI och två gränssnitt för moln-aktiverat på enheten. Vi rekommenderar men som kräver inte att oanvända gränssnitt inaktiveras.

För varje nätverksgränssnitt visas följande parametrar:

* **Hastighet** – inte en parameter som kan konfigureras av användaren. DATA 0 DATA 1, DATA 4 och 5 DATA är alltid 1 GbE DATA 2 och DATA 3 är 10 GbE-gränssnitt.
  
  > [!NOTE]
  > Hastighet och duplex är alltid automatiskt förhandlade. Jumboramar stöds inte.
  
* **Gränssnitt tillstånd** – ett gränssnitt kan aktiveras eller inaktiveras. Om aktiverad, försöker enheten använda gränssnittet. Vi rekommenderar att gränssnitt som är ansluten till nätverket och används aktiveras. Inaktivera alla gränssnitt som du inte använder.
* **Gränssnittstyp** – den här parametern kan du isolera iSCSI-trafik från molnet lagringsrelaterad trafik. Den här parametern kan vara något av följande:
  
  * **Molnet aktiverat** – när aktiverad enheten använder det här gränssnittet för att kommunicera med molnet.
  * **iSCSI-aktiverade** – när aktiverad enheten använder det här gränssnittet för att kommunicera med iSCSI-värden.
    
    Vi rekommenderar att du isolerar iSCSI-trafik från molnet lagringsrelaterad trafik. Observera också att om värden ligger inom samma undernät som enheten, inte behöver du tilldela en gateway. men om värden finns i ett annat undernät än enheten, måste du tilldela en gateway.
* **IP-adress** – när du konfigurerar någon av nätverksgränssnitt, måste du konfigurera en virtuell IP-adress (VIP). Detta kan vara IPv4 eller IPv6- eller båda. Både IPv4 och IPv6-adress familjer stöds för enheten nätverksgränssnitt. När du använder IPv4 kan ange en 32-bitars IP-adress (*xxx.xxx.xxx.xxx*) i punkt decimalform. När du använder IPv6 kan bara ange ett 4-siffrig prefix och en 128-bitars adress genereras automatiskt för din enhet nätverksgränssnittet baserat på det prefixet.
* **Undernät** – detta refererar till nätmasken och konfigureras via Windows PowerShell-gränssnittet.
* **Gateway** – detta är den standard-gateway som ska användas av det här gränssnittet när den försöker kommunicera med noder som inte finns inom samma IP-adressutrymme (undernät). Standard-gateway måste vara i samma adressutrymmet (undernät) som gränssnittet IP-adress, utifrån nätmasken.
* **Fasta IP-adressen** – det här fältet är bara tillgänglig när du konfigurerar DATA 0 gränssnitt. Du kan behöva ansluta direkt till enheten styrenheten för åtgärder som till exempel uppdateringar eller felsöka enheten. Den fasta IP-adressen kan användas för att få åtkomst till både aktiva och passiva domänkontrollant på enheten.

> [!NOTE]
> * För att fungera korrekt, kontrollera gränssnittets hastighet och duplex på växeln som varje enhetsgränssnittet är ansluten till. Växeln gränssnitt länkegenskaper antingen med eller konfigureras för Gigabit Ethernet (1000 Mbps) och vara full duplex. Gränssnitt med långsammare hastigheter eller läget halv duplex leder prestandaproblem.
> * För att minimera avbrott och driftstopp, rekommenderar vi att du aktiverar stöd för portfast på var och en av de växelportar som iSCSI-nätverksgränssnittet för enheten ska kunna ansluta till. Se till att nätverksanslutningen kan upprättas snabbt vid en växling vid fel.

### <a name="configure-data-0"></a>Konfigurera DATA 0

DATA 0 är moln-aktiverat som standard. Du måste också konfigurera två fasta IP-adresser, en för varje domänkontrollant när du konfigurerar DATA 0. Fasta IP-adresserna kan användas för att komma åt styrenheterna direkt och är användbara när du installerar uppdateringar på enheten för skräpinsamling ska fungera korrekt, eller när du har åtkomst till domänkontrollanterna för felsökning.

Du kan konfigurera om de fasta IP-domänkontrollanterna via inställningsbladet DATA 0.

![Konfigurera nätverksgränssnittet - DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> De fasta IP-adresserna för styrenheten används för att underhålla uppdateringarna till enheten och utrymme frigöring algoritmer (skräpinsamling) ska fungera korrekt. Därför måste de fasta IP-adresserna vara dirigerbara och kunna ansluta till Internet.

### <a name="configure-data-1---data-5"></a>Konfigurera DATA 1 - DATA 5

För DATA 1 - DATA 5 nätverksgränssnitt, kan du konfigurera alla nätverksinställningar som visas i följande skärmbild:

![Konfigurera nätverksgränssnitt DATA 1 - 5 DATA](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Tilldela IP-adresser eller växla

För närvarande, om något nätverksgränssnitt på domänkontrollanten har tilldelats en VIP som används (av samma enhet eller en annan enhet i nätverket), misslyckas styrenheten över. Om du byter eller omtilldela VIP för ett nätverksgränssnitt på enheten måste du följa en rätt procedur som du kan skapa en duplicerad IP-situation.

Utför följande steg för att växla eller omtilldela VIP för alla nätverkskort:

#### <a name="to-reassign-ips"></a>Tilldela IP-adresser

1. Ta bort IP-adressen för båda gränssnitten.
2. Efter IP-adresser har rensats kan du tilldela nya IP-adresser till respektive gränssnitt.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [konfigurera MPIO för din StorSimple-enhet](storsimple-8000-configure-mpio-windows-server.md).
* Lär dig hur du [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

