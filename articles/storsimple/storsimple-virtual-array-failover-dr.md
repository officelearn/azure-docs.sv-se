---
title: StorSimple Virtual Array disaster recovery och enheten redundans | Microsoft Docs
description: Läs mer om att redundansväxla StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: be3d98dc0b3a8119fb853493440c6fc78d65c5a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61409628"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Disaster recovery och enheten redundans för StorSimple Virtual Array via Azure-portalen

## <a name="overview"></a>Översikt
Den här artikeln beskrivs haveriberedskap för din Microsoft Azure StorSimple Virtual Array inklusive detaljerade steg att växla över till en annan virtuell matris. En redundansväxling kan du flytta dina data från en *källa* enheten i datacentret till en *target* enhet. Målenheten kan finnas i samma eller en annan geografisk plats. Redundansväxling av enhet är för hela enheten. Under redundansväxlingen ändras molndata för källenheten ägarskap till som målenheten.

Den här artikeln gäller bara för StorSimple Virtual Array. Om du vill redundansväxla en 8000-serien-enhet går du till [enheten redundans och återställning vid återställning av din StorSimple-enhet](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Vad är disaster recovery och enheten redundans?

I ett scenario med disaster recovery (DR), den primära enheten slutar fungera. I det här scenariot kan du flytta molndata som hör till misslyckade enheten till en annan enhet. Du kan använda den primära enheten som den *källa* och ange en annan enhet som den *target*. Den här processen kallas den *redundans*. Under redundansväxlingen, alla volymer eller resurser från källenheten ändra ägarskap och överförs till målenheten. Ingen filtrering av data är tillåten.

DR modelleras som en fullständig enhet återställning med hjälp av den termiska kartan-baserade lagringsnivåer och spårning. En termisk karta definieras genom att tilldela ett värde för den termiska data baserat på läsa och skriva mönster. Den här termisk mappar sedan nivåerna lägsta termisk datasegmenten till molnet först samtidigt som datasegment hög termisk (mest används) på den lokala nivån. Under en Katastrofåterställning använder StorSimple den termiska kartan för att återställa och rehydrate data från molnet. Enheten hämtar alla volymer/resurser i den senaste senaste säkerhetskopieringen (enligt bedömning internt) och utför en återställning från säkerhetskopian. Den virtuella matrisen samordnar hela DR-processen.

> [!IMPORTANT]
> Källenheten tas bort i slutet av redundansväxling av enhet och därför en återställning efter fel stöds inte.
> 
> 

Katastrofåterställning är orkestreras via funktionen enheten växling vid fel och initieras från den **enheter** bladet. Det här bladet tabulates alla StorSimple-enheter som är anslutna till din StorSimple Device Manager-tjänsten. Du kan se namn, status, etablerade och högsta kapacitet, typ och modell för varje enhet.

## <a name="prerequisites-for-device-failover"></a>Krav för redundansväxling av enhet

### <a name="prerequisites"></a>Nödvändiga komponenter

Se till att följande krav är uppfyllda för redundansväxling av enhet:

* Källenheten måste finnas i en **inaktiverad** tillstånd.
* Målenheten måste visas som **redo att konfigurera** i Azure-portalen. Etablera en virtuell matris mål av samma eller högre kapacitet. Använd det lokala webbgränssnittet för att konfigurera och registrera den virtuella Målmatrisen har.
  
  > [!IMPORTANT]
  > Försök inte att konfigurera den registrerade virtuella enheten via tjänsten. Någon enhetskonfiguration ska utföras via tjänsten.
  > 
  > 
* Målenheten kan inte ha samma namn som källenheten.
* Käll- och enheten måste vara av samma typ. Du kan bara redundansväxla en virtuell matris som konfigurerats som en server till en annan filserver. Detsamma gäller för en iSCSI-server.
* För en filserver-DR rekommenderar vi att du ansluter målenheten till samma domän som källan. Den här konfigurationen garanterar att behörigheter till resursen löses automatiskt. Endast redundans till en målenhet i samma domän.
* Tillgängliga målenheter för Katastrofåterställning är enheter som har samma eller större kapacitet som jämfört med källan. Enheter som är anslutna till din tjänst men inte uppfyller villkoren för tillräckligt med utrymme är inte tillgängliga som kan användas.

### <a name="other-considerations"></a>Annat att tänka på

* För en planerad redundans 
  
  * Vi rekommenderar att du vidtar alla volymer eller resurser på källenheten offline.
  * Vi rekommenderar att du gör en säkerhetskopia av enheten och fortsätter sedan med växling vid fel att minimera dataförlust. 
* En oplanerad redundans med enheten använder den senaste säkerhetskopian för att återställa data.

### <a name="device-failover-prechecks"></a>Enheten redundans föruppdateringskontrollen

Innan DR påbörjas, utför enheten föruppdateringskontrollen. Dessa kontroller kan du se till att inga fel inträffar när DR inleds. Föruppdateringskontrollen är:

* Validerar det storage-kontot.
* Kontrollera cloud-anslutning till Azure.
* Kontrollera tillgängligt utrymme på målenheten.
* Kontrollerar om en iSCSI-servern enheten källvolymen har
  
  * giltiga ACR-namn.
  * giltigt IQN (högst 220 tecken).
  * giltigt CHAP-lösenord (12 – 16 tecken lång).

Om något av föregående prechecks misslyckas kan du gå vidare med DR. Lös problemen och försök sedan DR.

När ar har slutförts, överförs ägarskapet för molndata på källenheten till målenheten. Källenheten är sedan inte längre tillgänglig i portalen. Blockeras åtkomsten till alla volymer/resurser på källenheten och målenheten blir aktiv.

> [!IMPORTANT]
> Om enheten är inte längre tillgänglig, är den virtuella datorn som du har etablerats på värdsystemet fortfarande förbruka resurser. När DR är slutförd kan du ta bort den här virtuella datorn från värdsystemet.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Växla över till en virtuell matris

Vi rekommenderar att etablera, konfigurera och registrera en annan StorSimple Virtual Array med din StorSimple Device Manager-tjänsten innan du kör den här proceduren.

> [!IMPORTANT]
> 
> * Du kan inte växla över från en enhet i StorSimple 8000-serien till en virtuell enhet 1200.
> * Du kan växla över från en virtuell enhet FIPS Federal Information Processing Standard () aktiverat till en annan FIPS-aktiverad enhet eller till en icke-FIPS-enhet som har distribuerats i Government portal.


Utför följande steg för att återställa enheten till målets virtuella StorSimple-enheter.

1. Etablera och konfigurera en målenhet som uppfyller den [krav för redundansväxling av enhet](#prerequisites). Slutför enhetskonfigurationen via det lokala webbgränssnittet och registrera den till StorSimple Device Manager-tjänsten. Om du skapar en filserver, går du till steg 1 av [konfigurera som filserver](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Om du skapar en iSCSI-server går du till steg 1 av [konfigurera som iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Ta volymer/resurser offline på värden. Om du vill koppla volymer/resurser finns i operativsystemet-specifika instruktionerna för värden. Om det inte redan offline, måste du ta alla volymer/resurser offline på enheten genom att göra följande.
   
    1. Gå till **enheter** bladet och välj din enhet.
   
    2. Gå till **Inställningar > Hantera > resurser** (eller **Inställningar > Hantera > volymer**). 
   
    3. Välj en resurs/volym, högerklicka och välj **ta offline**. 
   
    4. När du uppmanas att bekräfta **jag är införstådd med konsekvenserna av att koppla den här resursen.** 
   
    5. Klicka på **ta offline**.

3. I din StorSimple Device Manager-tjänst går du till **Management > enheter**. I den **enheter** bladet Välj och klicka på källenheten.

4. I din **instrumentpanelen** bladet klickar du på **inaktivera**.

5. I den **inaktivera** bladet du uppmanas att bekräfta. Inaktivering av enheten är en *permanent* process som inte kan ångras. Du är också en påminnelse om att ta dina resurser/volymer offline på värden. Ange enhetsnamnet för att bekräfta och klicka på **inaktivera**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Inaktiveringen startar. Du får ett meddelande när inaktiveringen har slutförts.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. På sidan enheter ändrar enhetens tillstånd nu till **inaktiverad**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. I den **enheter** bladet Välj och klicka på inaktiverad källenheten för redundans. 
9. I den **instrumentpanelen** bladet klickar du på **Redundansväxla**. 
10. I den **Redundansväxla enhet** bladet gör du följande:
    
    1. Enheten källfält fylls i automatiskt. Observera den totala datastorleken för källenheten. Storleken på data ska vara mindre än den tillgängliga kapaciteten på målenheten. Granska informationen som är associerade med källenheten som enhetens namn, total kapacitet och namnen på de resurser som har redundansväxlats.

    2. Välj den nedrullningsbara listan över tillgängliga enheter en **målenheten**. Endast de enheter som har tillräcklig kapacitet visas i listrutan.

    3. Kontrollera att **jag förstår att den här åtgärden misslyckas över data till målenheten**. 

    4. Klicka på **Redundansväxla**.
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Ett redundansjobb initierar och du får ett meddelande. Gå till **enheter > jobb** att övervaka växling vid fel.
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. I den **jobb** bladet visas ett redundansjobb som skapats för källenheten. Det här jobbet utför DR-föruppdateringskontrollen.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     När DR föruppdateringskontrollen har lyckats kommer redundansväxlingen starta återställningsjobb för varje resurs/volym som finns på källenheten.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. När redundansväxlingen är klar går du till den **enheter** bladet.
    
    1. Välj och klicka på StorSimple-enheten som användes som målenheten för failover-processen.
    2. Gå till **Inställningar > Management > resurser** (eller **volymer** om iSCSI-servern). I den **resurser** bladet kan du visa alla resurser (volymer) från den gamla enheten.
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Behöver du [skapa en DNS-alias](https://support.microsoft.com/kb/168322) så att alla program som försöker ansluta kan kommer du automatiskt till den nya enheten.

## <a name="errors-during-dr"></a>Fel under DR

**Molnet anslutning avbrott under en Katastrofåterställning**

Om molnanslutning avbryts DR har börjat och innan enheten återställningen är slutförd, misslyckas DR. Du får ett meddelande med fel. Målenheten för Haveriberedskap har markerats som *oanvändbar.* Du kan inte använda samma målenheten för framtida DRs.

**Ingen kompatibel målenheter**

Om de tillgängliga målservrar enheterna inte har tillräckligt med utrymme, ser du ett fel för den effekt som att det finns ingen kompatibel målenheter.

**Precheck fel**

Om någon av föruppdateringskontrollen inte är uppfyllt, ser du precheck fel.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Kontinuitet för företag-haveriberedskap (BCDR)

Ett affärskontinuitet disaster recovery (BCDR) affärsscenario inträffar när hela Azure-datacentret slutar att fungera. Detta kan påverka din StorSimple Device Manager-tjänsten och de associera StorSimple-enheterna.

Om det finns StorSimple-enheter som registrerats precis innan en katastrof har inträffat sedan dessa StorSimple-enheter kan behöva tas bort. Efter en katastrof, kan du återskapa och konfigurera dessa enheter.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du [administrera StorSimple Virtual Array med hjälp av det lokala webbgränssnittet](storsimple-ova-web-ui-admin.md).

