---
title: Ändra enhetskonfigurationen för StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du använder StorSimple Device Manager-tjänsten för att konfigurera om en StorSimple-enhet som redan har distribuerats.
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
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58008378"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Använda StorSimple Device Manager-tjänsten för att ändra din konfiguration för StorSimple-enheten

## <a name="overview"></a>Översikt

Azure-portalen **Enhetsinställningar** i avsnittet den **inställningar** bladet innehåller alla enheter-parametrar som du kan konfigurera om på en StorSimple-enhet som hanteras av en StorSimple Device Manager-tjänst . Den här självstudien beskrivs hur du kan använda den **inställningar** bladet för att göra följande på enhetsnivå:

* Ändra eget namn på enhet
* Ändra tid Enhetsinställningar
* Tilldela en sekundär DNS
* Ändra nätverksgränssnitt
* Växla eller tilldela IP-adresser

## <a name="modify-device-friendly-name"></a>Ändra eget namn på enhet

Du kan använda Azure-portalen för att ändra enhetens namn och tilldela den ett unikt namn för ditt val. Använd den **allmänna inställningar** bladet på enheten för att ändra det egna namnet för enheten. Det egna namnet kan innehålla vilka tecken och kan vara högst 64 tecken.

> [!NOTE] 
> Du kan bara ändra enhetsnamnet i Azure-portalen innan installationen av enheten är klar. När den minimala Enhetsinstallationen är klar kan ändra du inte namnet på enheten.

![Enhetens namn i allmänhet inställningar](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

En StorSimple-enhet som är ansluten till StorSimple Device Manager-tjänsten tilldelas ett standardnamn. Standardnamnet visar normalt serienumret för enheten. Ett standardnamn för enheten som är högst 15 tecken långt, till exempel 8600-SHX0991003G44HT anger till exempel följande:

* **8600** – anger den nuvarande enhetsmodellen.
* **SHX** – anger platsen manufacturing.
* **0991003** -indikerar en specifik produkt.
* **G44HT**– de 5 senaste siffrorna ökas för att skapa unika serienummer. Det kan inte vara en sekventiell uppsättning.

## <a name="modify-device-description"></a>Ändra enhetsbeskrivning

Använd den **allmänna inställningar** bladet på enheten för att ändra enhetsbeskrivningen.

![Enhetsbeskrivning i allmänna inställningar](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

En beskrivning kan vanligtvis identifiera ägare och den fysiska platsen för enheten. Beskrivningsfältet måste innehålla färre än 256 tecken.

## <a name="modify-time-settings"></a>Ändra tidsinställningar

Din enhet måste synkronisera tiden för att kunna autentisera med din molntjänstleverantör för lagring. Använd den **allmänna inställningar** bladet på enheten för att ändra tiden Enhetsinställningar.

![Enhetsbeskrivning i allmänna inställningar](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Välj din tidszon från den nedrullningsbara listan. Du kan ange upp till två Network Time Protocol (NTP) servrar:

 - **Primär NTP-server** -konfigurationen är obligatoriskt och anges när du använder Windows PowerShell för StorSimple för att konfigurera din enhet. Du kan ange standard Windows Server **time.windows.com** som NTP-servern. Du kan visa den primära NTP-serverkonfigurationen via Azure-portalen, men du måste använda Windows PowerShell-gränssnittet för att ändra den. Använd den `Set-HcsNTPClientServerAddress` cmdlet för att ändra primär NTP-server för din enhet. Mer information finns i syntaxen för [Set-HcsNTPClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet.

- **Sekundär NTP-server** -konfigurationen är valfritt. Du kan använda portalen för att konfigurera en sekundär NTP-server.

När du konfigurerar NTP-servern kan du se till att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till Internet. När du anger en offentlig NTP-server, måste du se till att din nätverksbrandväggar och andra säkerhetsenheter är konfigurerad för att tillåta NTP-trafik skickas till och från det externa nätverket. Om dubbelriktad NTP-trafik inte tillåts, måste du använda en intern NTP-server (en Windows-domänkontrollant ger den här funktionen). Om enheten inte kan synkronisera tiden, kanske den inte kan kommunicera med dina molnlagringsleverantören.

Om du vill se en lista över allmänna NTP-servrar går du till den [NTP-servrar Web](https://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Vad händer om enheten är distribuerat i en annan tidszon?

Om enheten är distribuerat i en annan tidszon, ändrar tidszonen för enheten. Med hänsyn till att alla principer för säkerhetskopiering använder enhetens tidszon, justeras automatiskt principer för säkerhetskopiering i enlighet med den nya tidszonen. Ingen åtgärd från användaren krävs.

## <a name="modify-dns-settings"></a>Ändra DNS-inställningar

En DNS-server används när enheten försöker kommunicera med din molntjänstleverantör för lagring. Använd den **nätverksinställningar** bladet på enheten för att visa och ändra de konfigurerade DNS-inställningarna. 

![DNS-inställningarna i nätverksinställningar](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

För hög tillgänglighet behöver du konfigurera både primärt och sekundära DNS-servrarna under distributionen av första enheten.

**Primär DNS-server** -du använda Windows PowerShell för StorSimple för att först ange den primära DNS-servern under den inledande konfigurationen. Du kan konfigurera om den primära DNS-servern endast via Windows PowerShell-gränssnittet. Använd den `Set-HcsDNSClientServerAddress` cmdlet för att ändra den primära DNS-servern för enheten. Mer information finns i syntaxen för [Set-HcsDNSClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet.

**Sekundär DNS-server** – om du vill ändra den sekundära DNS-servern använder den `Set-HcsDNSClientServerAddress` cmdlet i Windows PowerShell-gränssnittet på enheten eller **nätverksinställningar** bladet för StorSimple-enheten i Azure-portalen.

Utför följande steg om du vill ändra den sekundära DNS-servern i Azure-portalen.

1. Gå till StorSimple Device Manager-tjänsten. Välj i listan med enheter, och klicka på din enhet.

2. I den **inställningar** gå till bladet **Enhetsinställningar > nätverk**. Gör det öppnas den **nätverksinställningar** bladet. Klicka på **DNS-inställningarna** panelen. Ändra sekundär DNS-serverns IP-adress.

    ![Ändra sekundär DNS-serverns IP-adress](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. I kommandofältet klickar du på **spara** och när du uppmanas att bekräfta klickar du på **OK**.

    ![Spara och bekräfta ändringar](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Ändra nätverksgränssnitt

Enheten har sex enheten nätverksgränssnitt, varav fyra är 1 GbE och varav två är 10 GbE. Dessa gränssnitt är märkta som DATA 0 – 5 för DATA. DATA 0, 1 av DATA, DATA 4 och 5 för DATA är 1 GbE DATA 2 och DATA 3 är 10 GbE-nätverksgränssnitt.

Använd den **nätverksinställningar** bladet för att konfigurera var och en av de gränssnitt som ska användas.

![Konfigurera nätverksgränssnitt via nätverksinställningar](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

För att säkerställa hög tillgänglighet, rekommenderar vi att du har minst två iSCSI och två gränssnitt för moln-aktiverat på enheten. Vi rekommenderar men som kräver inte att oanvända gränssnitt kan inaktiveras.

För varje nätverksgränssnitt visas följande parametrar:

* **Hastighet** – inte en användarangiven parameter. DATA 0, 1 av DATA, DATA 4 och 5 för DATA är alltid 1 GbE DATA 2 och DATA 3 är 10 GbE-gränssnitt.
  
  > [!NOTE]
  > Hastighet och duplex förhandlas alltid automatiskt. Jumboramar stöds inte.
  
* **Gränssnitt tillstånd** – ett gränssnitt kan aktiveras eller inaktiveras. Om aktiverad, försöker enheten att använda gränssnittet. Vi rekommenderar att endast de gränssnitt som är ansluten till nätverket och används aktiveras. Inaktivera alla gränssnitt som du inte använder.
* **Gränssnittstyp** – den här parametern kan du isolera iSCSI-trafik från molnet lagringsrelaterad trafik. Den här parametern kan vara något av följande:
  
  * **Moln-aktiverat** – när aktiverad enheten ska använda det här gränssnittet för att kommunicera med molnet.
  * **iSCSI-aktiverat** – när aktiverad enheten ska använda det här gränssnittet för att kommunicera med iSCSI-värden.
    
    Vi rekommenderar att du isolerar iSCSI-trafik från molnet lagringsrelaterad trafik. Observera också att om värden ligger inom samma undernät som din enhet, inte behöver du tilldela en gateway. Om värden är i ett annat undernät än din enhet, behöver du dock att tilldela en gateway.
* **IP-adress** – när du konfigurerar någon av nätverksgränssnitt, måste du konfigurera en virtuell IP (VIP). Detta kan vara IPv4 eller IPv6, eller bådadera. Både IPv4 och IPv6-adress-familjer har stöd för nätverksgränssnitt för enheten. När du använder IPv4 kan ange en 32-bitars IP-adress (*xxx.xxx.xxx.xxx*) i punkt decimalform. När du använder IPv6 kan bara ange ett 4-siffrig prefix och 128-bitars-adressen kommer att genereras automatiskt för enheten nätverksgränssnittets baserat på det prefixet.
* **Undernät** – detta avser nätmask och konfigureras via Windows PowerShell-gränssnittet.
* **Gateway** – det här är den standardgateway som ska användas av det här gränssnittet när den försöker kommunicera med noder som inte är inom samma IP-adressutrymme (undernät). Standard-gateway måste vara i samma adressutrymmet (undernät) som gränssnitt IP-adress, som bestäms av nätmasken.
* **Fasta IP-adressen** – det här fältet är bara tillgänglig när du konfigurerar DATA 0 gränssnitt. Du kan behöva ansluta direkt till enhetens styrenhet för åtgärder som uppdateringar eller felsöka enheten. Den fasta IP-adressen kan användas för åtkomst till både aktivt och passiva styrenheten på din enhet.

> [!NOTE]
> * För att fungera korrekt, kontrollera gränssnittets hastighet och duplex på växeln som varje enhetsgränssnitt är ansluten till. Växeln gränssnitt länkegenskaper antingen med eller konfigureras för Gigabit Ethernet (1000 Mbit/s) och vara full duplex. Gränssnitt som fungerar med lägre hastigheter eller i läget halv duplex ska resultera i prestandaproblem.
> * För att minimera störningar och driftstopp, rekommenderar vi att du aktiverar stöd för portfast på var och en av de växelportar som iSCSI-nätverksgränssnittet på din enhet ska kunna ansluta till. Se till att nätverksanslutningen kan upprättas snabbt i händelse av redundans.

### <a name="configure-data-0"></a>Konfigurera DATA 0

DATA 0 är moln-aktiverat som standard. När du konfigurerar DATA 0, måste du också konfigurera två fasta IP-adresser, en för varje styrenhet. Dessa fasta IP-adresser kan användas för att komma åt styrenheterna direkt och är användbara när du installerar uppdateringar på en enhet för skräpinsamling ska fungera korrekt, eller när du använder domänkontrollanter i syfte att felsöka.

Du kan konfigurera om fasta IP-domänkontrollanter via bladet DATA 0-inställningar.

![Konfigurera nätverksgränssnitt – DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> De fasta IP-adresserna för styrenheten används för att hantera uppdateringarna till enheten och algoritmer för frigöring av utrymme (skräpinsamling) ska fungera korrekt. Därför måste de fasta IP-adresserna vara dirigerbara och kunna ansluta till Internet.

### <a name="configure-data-1---data-5"></a>Konfigurera DATA 1 – DATA 5

För 1 – 5 nätverksgränssnitt för DATA, kan du konfigurera nätverksinställningarna som du ser i följande skärmbild:

![Konfigurera nätverksgränssnitt DATA 1 – DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Växla eller tilldela IP-adresser

För närvarande, om ett nätverksgränssnitt på styrenheten har tilldelats en VIP som används (av samma enhet eller en annan enhet i nätverket), växlar sedan kontrollanten över. Om du växlar eller tilldela virtuella IP-adresser för ett nätverksgränssnitt för enheten, måste du följa en rätt procedur som du kan skapa en duplicerad IP-situation.

Utför följande steg för att växla eller omtilldela VIP för någon av nätverksgränssnitt:

#### <a name="to-reassign-ips"></a>Att tilldela IP-adresser

1. Ta bort IP-adressen för båda gränssnitten.
2. När IP-adresser har rensats kan du tilldela de nya IP-adresserna för respektive gränssnitten.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [konfigurera MPIO för din StorSimple-enhet](storsimple-8000-configure-mpio-windows-server.md).
* Lär dig hur du [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

