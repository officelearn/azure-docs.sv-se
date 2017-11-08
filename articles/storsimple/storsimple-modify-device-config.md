---
title: "Ändra konfigurationen för StorSimple-enhet | Microsoft Docs"
description: "Beskriver hur du använder StorSimple Manager-tjänsten för att konfigurera om en StorSimple-enhet som redan har distribuerats."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: bb679264-8d46-429c-9ef7-630dc3b4a423
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 5025dc2643f9e57c0d8fa919b7d601f184d74431
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-modify-your-storsimple-device-configuration"></a>Använda StorSimple Manager-tjänsten för att ändra konfigurationen för StorSimple-enhet
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Om du vill visa versionen av den här artikeln för den nya Azure portalen, gå till [använda StorSimple Manager-tjänsten för att ändra konfigurationen för StorSimple-enheten](storsimple-8000-modify-device-config.md). Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Översikt
Den klassiska Azure-portalen **konfigurera** innehåller alla enheter-parametrar som du kan konfigurera om på en StorSimple-enhet som hanteras av en StorSimple Manager-tjänsten. Den här självstudiekursen beskrivs hur du kan använda den **konfigurera** sidan om du vill utföra följande uppgifter för enhetsnivå:

* Ändra inställningar för enheter 
* Ändra inställningarna för tid 
* Ändra DNS-inställningar 
* Ändra nätverksgränssnitt
* Tilldela IP-adresser eller växla

## <a name="modify-device-settings"></a>Ändra inställningar för enheter
Inställningar för enheter är det egna namnet för enheten och enhetsbeskrivning.

> [!NOTE] 
> Du kan inte ändra enhetsnamn i den klassiska Azure-portalen. Byta namn på enheten stöds inte.

En StorSimple-enhet som är ansluten till StorSimple Manager-tjänsten har tilldelats ett standardnamn. Standardnamnet visar normalt serienumret på enheten. En enhet för standardnamnet är 15 tecken långt, till exempel 8600-SHX0991003G44HT anger till exempel följande:

* **8600** – anger enhetsmodellen.
* **SHX** – anger anläggning.
* **0991003** -indikerar en viss produkt.
* **G44HT**-de 5 senaste siffrorna ökas för att skapa unika serienummer. Det kan inte vara en sekventiell uppsättning.

Du kan ange en enhetsbeskrivning. En enhetsbeskrivning identifierar vanligtvis ägaren och den fysiska platsen för enheten. Beskrivningsfältet måste innehålla färre än 256 tecken.

## <a name="modify-time-settings"></a>Ändra inställningarna för tid
Din enhet måste synkronisera tiden för att kunna autentisera med din molntjänstleverantör för lagring. Välj tidszon från den nedrullningsbara listrutan och ange upp till två Network Time Protocol (NTP) servrar. Primär NTP-server krävs och anges när du använder Windows PowerShell för StorSimple för att konfigurera din enhet. Du kan ange standard Windows Server **time.windows.com** som NTP-server. Du kan visa primär NTP-serverkonfigurationen via den klassiska Azure-portalen, men du måste använda Windows PowerShell-gränssnittet för att ändra den.

Sekundär NTP-serverkonfigurationen är valfritt. Du kan använda den klassiska portalen för att konfigurera en sekundär NTP-server. 

När du konfigurerar NTP-server måste du kontrollera att ditt nätverk tillåter NTP-trafik skickas från ditt datacenter till Internet. När du anger en offentlig NTP-server, måste du se till att nätverkets brandväggar och andra säkerhetsenheter är konfigurerad för att tillåta NTP-trafik skickas till och från det externa nätverket. Om dubbelriktad NTP-trafik inte tillåts, måste du använda en intern NTP-server (en Windows-domänkontrollant ger den här funktionen). Om enheten inte kan synkronisera tiden, kanske den inte kommunicera med lagringsprovidern i molnet.

Om du vill se en lista över allmänna NTP-servrar, gå till den [NTP-servrar Web](http://support.ntp.org/bin/view/Servers/WebHome). 

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Vad händer om enheten är distribuerat i en annan tidszon?
Om enheten har distribuerats i en annan tidszon, ändras tidszon enhet. Med hänsyn till att alla principer för säkerhetskopiering använder enheten tidszon, justeras automatiskt principer för säkerhetskopiering i enlighet med den nya tidszonen. Inga användaråtgärder krävs.

## <a name="modify-dns-settings"></a>Ändra DNS-inställningar
En DNS-server används när enheten försöker kommunicera med din molntjänstleverantör för lagring. För hög tillgänglighet krävs att konfigurera både primärt och sekundära DNS-servrar under den inledande distributionen. Om du vill konfigurera om den primära DNS-servern, behöver du använda Windows PowerShell-gränssnittet på din StorSimple-enhet.

Du kan använda den klassiska Azure-portalen om du vill ändra den sekundära DNS-servern.

## <a name="modify-network-interfaces"></a>Ändra nätverksgränssnitt
Enheten har sex enheten nätverksgränssnitt, varav fyra är 1 GbE och varav två 10 GbE. Gränssnitten är märkta som DATA 0 – DATA 5. DATA 0 DATA 1, DATA 4 och 5 DATA är 1 GbE DATA 2 och DATA 3 är 10 GbE-nätverkskort.

Konfigurera **gränssnitt för nätverksinställningar** för varje gränssnitt som ska användas. För att säkerställa hög tillgänglighet, rekommenderar vi att du har minst två iSCSI och två gränssnitt för moln-aktiverat på enheten. Vi rekommenderar men som kräver inte att oanvända gränssnitt inaktiveras.

När du konfigurerar någon av nätverksgränssnitt, måste du konfigurera en virtuell IP-adress (VIP).

DATA 0 är moln-aktiverat som standard. Du måste också konfigurera två fasta IP-adresser, en för varje domänkontrollant när du konfigurerar DATA 0. Fasta IP-adresserna kan användas för att komma åt styrenheterna direkt och är användbara när du installerar uppdateringar på enheten eller när du har åtkomst till domänkontrollanterna för felsökning.

I StorSimple 8000 Series uppdatering 1 routning mått för DATA 0 anges med den lägsta; Om enheten kör StorSimple 8000 Series uppdatering 1, därför dirigeras moln-trafik via DATA 0. Anteckna detta om du har mer än en moln-aktiverat nätverksgränssnittet på din StorSimple-enhet.

> [!NOTE]
> De fasta IP-adresserna för styrenheten används för att hantera uppdateringarna till enheten. Därför måste de fasta IP-adresserna vara dirigerbara och kunna ansluta till Internet.
> 
> 

För varje nätverksgränssnitt visas följande parametrar:

* **Hastighet** – inte en parameter som kan konfigureras av användaren. DATA 0 DATA 1, DATA 4 och 5 DATA är alltid 1 GbE DATA 2 och DATA 3 är 10 GbE-gränssnitt.
  
  > [!NOTE]
  > Hastighet och duplex är alltid automatiskt förhandlade. Jumboramar stöds inte.
  > 
  > 
* **Gränssnitt tillstånd** – ett gränssnitt kan aktiveras eller inaktiveras. Om aktiverad, försöker enheten använda gränssnittet. Vi rekommenderar att gränssnitt som är ansluten till nätverket och används aktiveras. Inaktivera alla gränssnitt som du inte använder.
* **Gränssnittstyp** – den här parametern kan du isolera iSCSI-trafik från molnet lagringsrelaterad trafik. Den här parametern kan vara något av följande:
  
  * **Molnet aktiverat** – när aktiverad enheten använder det här gränssnittet för att kommunicera med molnet.
  * **iSCSI-aktiverade** – när aktiverad enheten använder det här gränssnittet för att kommunicera med iSCSI-värden.
    
    Vi rekommenderar att du isolerar iSCSI-trafik från molnet lagringsrelaterad trafik. Observera också att om värden ligger inom samma undernät som enheten, inte behöver du tilldela en gateway. men om värden finns i ett annat undernät än enheten, måste du tilldela en gateway.
* **IP-adress** – det kan vara IPv4 eller IPv6- eller båda. Både IPv4 och IPv6-adress familjer stöds för enheten nätverksgränssnitt. När du använder IPv4 kan ange en 32-bitars IP-adress (*xxx.xxx.xxx.xxx*) i punkt decimalform. När du använder IPv6 kan bara ange ett 4-siffrig prefix och en 128-bitars adress genereras automatiskt för din enhet nätverksgränssnittet baserat på det prefixet.
* **Undernät** – detta refererar till nätmasken och konfigureras via Windows PowerShell-gränssnittet.
* **Gateway** – detta är den standard-gateway som ska användas av det här gränssnittet när den försöker kommunicera med noder som inte finns inom samma IP-adressutrymme (undernät). Standard-gateway måste vara i samma adressutrymmet (undernät) som gränssnittet IP-adress, utifrån nätmasken.
* **Fasta IP-adressen** – det här fältet är bara tillgänglig när du konfigurerar DATA 0 gränssnitt. Du kan behöva ansluta direkt till enheten styrenheten för åtgärder som till exempel uppdateringar eller felsöka enheten. Den fasta IP-adressen kan användas för att få åtkomst till både aktiva och passiva domänkontrollant på enheten.

Du kan konfigurera styrenhet 0 och 1 via den klassiska Azure-portalen.

> [!NOTE]
> * För att fungera korrekt, kontrollera gränssnittets hastighet och duplex på växeln som varje enhetsgränssnittet är ansluten till. Växeln gränssnitt länkegenskaper antingen med eller konfigureras för Gigabit Ethernet (1000 Mbps) och vara full duplex. Gränssnitt med långsammare hastigheter eller läget halv duplex leder prestandaproblem.
> * För att minimera avbrott och driftstopp, rekommenderar vi att du aktiverar stöd för portfast på var och en av de växelportar som iSCSI-nätverksgränssnittet för enheten ska kunna ansluta till. Se till att nätverksanslutningen kan upprättas snabbt vid en växling vid fel.
> 
> 

## <a name="swap-or-reassign-ips"></a>Tilldela IP-adresser eller växla
För närvarande, om något nätverksgränssnitt på domänkontrollanten har tilldelats en VIP som används (av samma enhet eller en annan enhet i nätverket), misslyckas styrenheten över. Du måste därför följa på rätt sätt om du växla VIP för nätverksgränssnittet enhet eftersom du vill skapa en duplicerad IP-situation.

Utför följande steg för att växla eller omtilldela VIP för alla nätverkskort:

#### <a name="to-reassign-ips"></a>Tilldela IP-adresser
1. Ta bort IP-adressen för båda gränssnitten.
2. Efter IP-adresser har rensats kan du tilldela nya IP-adresser till respektive gränssnitt.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [konfigurera MPIO för din StorSimple-enhet](storsimple-configure-mpio-windows-server.md).
* Lär dig hur du [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

