---
title: Ändra enhetskonfigurationen i StorSimple 8000-serien | Microsoft-dokument
description: Beskriver hur du använder Tjänsten StorSimple Device Manager för att konfigurera om en StorSimple-enhet som redan har distribuerats.
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
ms.openlocfilehash: 774f5a73a5fc30352698c0af0c279fbbe488c480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267695"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Använda Tjänsten StorSimple Device Manager för att ändra konfigurationen av StorSimple-enheten

## <a name="overview"></a>Översikt

Avsnittet Azure portal **Device-inställningar** i bladet **Inställningar** innehåller alla enhetsparametrar som du kan konfigurera om på en StorSimple-enhet som hanteras av en StorSimple Enhetshanteraren-tjänst. I den här självstudien beskrivs hur du kan använda **bladet Inställningar** för att utföra följande uppgifter på enhetsnivå:

* Ändra enhetsvänligt namn
* Ändra inställningar för enhetens tid
* Tilldela en sekundär DNS
* Ändra nätverksgränssnitt
* Byta eller tilldela om IPs

## <a name="modify-device-friendly-name"></a>Ändra enhetsvänligt namn

Du kan använda Azure-portalen för att ändra enhetens namn och tilldela den ett unikt eget namn som du väljer. Använd bladet **Allmänna inställningar** på enheten för att ändra enhetens eget namn. Det egna namnet kan innehålla alla tecken och kan vara högst 64 tecken långt.

> [!NOTE] 
> Du kan bara ändra enhetsnamnet i Azure-portalen innan enhetskonfigurationen är klar. När den minsta enhetskonfigurationen är klar kan du inte ändra enhetsnamnet.

![Enhetsnamn i allmänna inställningar](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

En StorSimple-enhet som är ansluten till Tjänsten StorSimple Device Manager tilldelas ett standardnamn. Standardnamnet återspeglar vanligtvis enhetens serienummer. Ett standardenhetsnamn som är 15 tecken långt, till exempel 8600-SHX0991003G44HT, anger till exempel följande:

* **8600** – Anger enhetsmodellen.
* **SHX** – Anger tillverkningsanläggningen.
* **0991003** - Anger en specifik produkt.
* **G44HT**- De sista 5 siffrorna ökas för att skapa unika serienummer. Detta kanske inte är en sekventiell uppsättning.

## <a name="modify-device-description"></a>Ändra enhetsbeskrivning

Använd bladet **Allmänna inställningar** på enheten för att ändra enhetsbeskrivningen.

![Enhetsbeskrivning i allmänna inställningar](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

En enhetsbeskrivning hjälper vanligtvis till att identifiera enhetens ägare och fysiska plats. Beskrivningsfältet måste innehålla färre än 256 tecken.

## <a name="modify-time-settings"></a>Ändra tidsinställningar

Enheten måste synkronisera tiden för att autentisera med din molnlagringstjänstleverantör. Använd bladet **Allmänna inställningar** på enheten för att ändra enhetstidsinställningarna.

![Enhetsbeskrivning i allmänna inställningar](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Välj tidszon i listrutan. Du kan ange upp till två NTP-servrar (Network Time Protocol):

 - **Primär NTP-server** - Konfigurationen krävs och anges när du använder Windows PowerShell för StorSimple för att konfigurera enheten. Du kan ange standard- **Time.windows.com** För Windows Server som NTP-server. Du kan visa den primära NTP-serverkonfigurationen via Azure-portalen, men du måste använda Windows PowerShell-gränssnittet för att ändra den. Använd `Set-HcsNTPClientServerAddress` cmdleten för att ändra enhetens primära NTP-server. Mer information finns i syntaxen för [Cmdlet Set-HcsNTPClientServerAddress.](https://technet.microsoft.com/library/dn688138.aspx)

- **Sekundär NTP-server** - Konfigurationen är valfri. Du kan använda portalen för att konfigurera en sekundär NTP-server.

När du konfigurerar NTP-servern ska du se till att NTP-trafiken kan passera från ditt datacenter till Internet. När du anger en offentlig NTP-server måste du se till att nätverksbrandväggerna och andra säkerhetsenheter är konfigurerade så att NTP-trafik kan färdas till och från det externa nätverket. Om dubbelriktad NTP-trafik inte är tillåten måste du använda en intern NTP-server (en Windows-domänkontrollant tillhandahåller den här funktionen). Om enheten inte kan synkronisera tiden kanske den inte kan kommunicera med din molnlagringsleverantör.

Om du vill visa en lista över offentliga NTP-servrar går du till [NTP-servrarnas webb](https://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Vad händer om enheten distribueras i en annan tidszon?

Om enheten distribueras i en annan tidszon ändras enhetens tidszon. Med tanke på att alla principer för säkerhetskopiering använder enhetens tidszon justeras säkerhetskopieringsprinciperna automatiskt i enlighet med den nya tidszonen. Ingen åtgärd från användaren krävs.

## <a name="modify-dns-settings"></a>Ändra DNS-inställningar

En DNS-server används när enheten försöker kommunicera med din molnlagringstjänstleverantör. Använd **bladet Nätverksinställningar** på enheten för att visa och ändra de konfigurerade DNS-inställningarna. 

![DNS-inställningar i Nätverksinställningar](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

För hög tillgänglighet måste du konfigurera både de primära och de sekundära DNS-servrarna under den första enhetsdistributionen.

**Primär DNS-server** - Du använder Windows PowerShell för StorSimple för att först ange den primära DNS-servern under den första installationen. Du kan bara konfigurera om den primära DNS-servern via Windows PowerShell-gränssnittet. Använd `Set-HcsDNSClientServerAddress` cmdleten för att ändra enhetens primära DNS-server. Mer information finns i syntaxen för [Cmdlet Set-HcsDNSClientServerAddress.](https://technet.microsoft.com/library/dn688138.aspx)

**Sekundär DNS-server** – Om du vill `Set-HcsDNSClientServerAddress` ändra den sekundära DNS-servern använder du cmdleten i Windows PowerShell-gränssnittet för enheten eller **nätverksinställningsbladet** på din StorSimple-enhet i Azure-portalen.

Om du vill ändra den sekundära DNS-servern i Azure-portalen utför du följande steg.

1. Gå till StorSimple Device Manager-tjänsten. Välj och klicka på enheten i listan över enheter.

2. Gå till **Enhetsinställningar > Nätverk**i bladet **Inställningar** . Då öppnas bladet **Nätverksinställningar.** Klicka på panelen **DNS-inställningar.** Ändra den sekundära IP-adressen för DNS-servern.

    ![Ändra sekundär IP-server-IP-adress för DNS-servern](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. Klicka på **Spara** i kommandofältet och klicka på **OK**när du uppmanas att bekräfta.

    ![Spara och bekräfta ändringar](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Ändra nätverksgränssnitt

Enheten har sex nätverksgränssnitt för enheter, varav fyra är 1 GbE och varav två är 10 GbE. Dessa gränssnitt är märkta som DATA 0 – DATA 5. DATA 0, DATA 1, DATA 4 och DATA 5 är 1 GbE, medan DATA 2 och DATA 3 är 10 GbE-nätverksgränssnitt.

Använd **bladet Nätverksinställningar** för att konfigurera vart och ett av de gränssnitt som ska användas.

![Konfigurera nätverksgränssnitt via nätverksinställningar](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

För att säkerställa hög tillgänglighet rekommenderar vi att du har minst två iSCSI-gränssnitt och två molnaktiverade gränssnitt på din enhet. Vi rekommenderar men kräver inte att oanvända gränssnitt inaktiveras.

För varje nätverksgränssnitt visas följande parametrar:

* **Hastighet** – Inte en användarkonfigurerbar parameter. DATA 0, DATA 1, DATA 4 och DATA 5 är alltid 1 GbE, medan DATA 2 och DATA 3 är 10 GbE-gränssnitt.
  
  > [!NOTE]
  > Hastighet och duplex förhandlas alltid automatiskt. Jumboramar stöds inte.
  
* **Gränssnittstillstånd** – Ett gränssnitt kan aktiveras eller inaktiveras. Om den är aktiverad försöker enheten använda gränssnittet. Vi rekommenderar att endast de gränssnitt som är anslutna till nätverket och används aktiveras. Inaktivera alla gränssnitt som du inte använder.
* **Gränssnittstyp** – Med den här parametern kan du isolera iSCSI-trafik från molnlagringstrafik. Den här parametern kan vara något av följande:
  
  * **Moln aktiverat** – när det är aktiverat använder enheten det här gränssnittet för att kommunicera med molnet.
  * **iSCSI aktiverat** – när det är aktiverat kommer enheten att använda det här gränssnittet för att kommunicera med iSCSI-värden.
    
    Vi rekommenderar att du isolerar iSCSI-trafik från molnlagringstrafik. Observera också att om värden befinner sig i samma undernät som enheten behöver du inte tilldela en gateway. Men om värden finns i ett annat undernät än enheten måste du tilldela en gateway.
* **IP-adress** – När du konfigurerar något av nätverksgränssnitten måste du konfigurera en virtuell IP (VIP). Detta kan vara IPv4 eller IPv6 eller båda. Både IPv4- och IPv6-adressfamiljerna stöds för enhetsnätverksgränssnitten. När du använder IPv4 anger du en 32-bitars IP-adress *(xxx.xxx.xxx.xxx*) i punkt decimalnotation. När du använder IPv6 anger du bara ett fyrsiffrigt prefix, och en 128-bitarsadress genereras automatiskt för enhetens nätverksgränssnitt baserat på prefixet.
* **Undernät** – Detta refererar till nätmasken och konfigureras via Windows PowerShell-gränssnittet.
* **Gateway** – Detta är standardgatewayen som ska användas av det här gränssnittet när den försöker kommunicera med noder som inte finns inom samma IP-adressutrymme (undernät). Standardgatewayen måste finnas i samma adressutrymme (undernät) som gränssnittets IP-adress, enligt vad som bestäms av nätmasken.
* **Fast IP-adress** – Det här fältet är bara tillgängligt när du konfigurerar DATA 0-gränssnittet. För åtgärder som uppdateringar eller felsökning av enheten kan du behöva ansluta direkt till enhetsstyrenheten. Den fasta IP-adressen kan användas för att komma åt både den aktiva och den passiva styrenheten på enheten.

> [!NOTE]
> * Kontrollera gränssnittets hastighet och dubbelsidighet på växeln som varje enhetsgränssnitt är anslutet till för att säkerställa att gränssnittet fungerar korrekt. Switch-gränssnitt bör antingen förhandla med eller konfigureras för Gigabit Ethernet (1000 Mbps) och vara full-duplex. Gränssnitt som arbetar med långsammare hastigheter eller i halv duplex kommer att resultera i prestandaproblem.
> * För att minimera störningar och driftstopp rekommenderar vi att du aktiverar portfast på var och en av switchportarna som iSCSI-nätverksgränssnittet på enheten ansluter till. Detta säkerställer att nätverksanslutningen kan upprättas snabbt i händelse av en redundansväxling.

### <a name="configure-data-0"></a>Konfigurera DATA 0

DATA 0 är molnaktiverat som standard. När du konfigurerar DATA 0 måste du också konfigurera två fasta IP-adresser, en för varje styrenhet. Dessa fasta IP-adresser kan användas för att komma åt enhetskontrollanterna direkt och är användbara när du installerar uppdateringar på enheten, för att skräpinsamling ska fungera korrekt eller när du kommer åt styrenheterna i felsökningssyfte.

Du kan konfigurera om de fasta IP-styrenheterna via bladet DATA 0-inställningar.

![Konfigurera nätverksgränssnitt - DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> De fasta IP-adresserna för styrenheten används för att underhålla uppdateringarna till enheten och för att utrymmesåterkallningsalgoritmer (skräpinsamling) ska fungera korrekt. Därför måste de fasta IP-adresserna vara dirigerbara och kunna ansluta till Internet.

### <a name="configure-data-1---data-5"></a>Konfigurera DATA 1 - DATA 5

För DATA 1 - DATA 5-nätverksgränssnitt kan du konfigurera alla nätverksinställningar som visas i följande skärmbild:

![Konfigurera nätverksgränssnitt DATA 1 - DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Byta eller tilldela om IPs

För närvarande, om något nätverksgränssnitt på styrenheten tilldelas en VIP som används (av samma enhet eller en annan enhet i nätverket), då styrenheten kommer att växla över. Om du byter eller återanvisar VIP-adresser för ett enhetsnätverksgränssnitt måste du följa en korrekt procedur eftersom du kan skapa en dubblett-IP-situation.

Utför följande steg för att byta eller tilldela om VIP-adresserna för något av nätverksgränssnitten:

#### <a name="to-reassign-ips"></a>Så här återanvisar du IPs

1. Rensa IP-adressen för båda gränssnitten.
2. När IP-adresserna har rensats tilldelar du de nya IP-adresserna till respektive gränssnitt.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [konfigurerar MPIO för din StorSimple-enhet](storsimple-8000-configure-mpio-windows-server.md).
* Lär dig hur du [använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

