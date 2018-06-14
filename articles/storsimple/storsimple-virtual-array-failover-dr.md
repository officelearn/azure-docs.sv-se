---
title: StorSimple virtuell matris disaster recovery och enheten redundans | Microsoft Docs
description: Mer information om hur du redundans din virtuella StorSimple-matris.
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
ms.openlocfilehash: 12079f8dbc409afe5acc274fa08bda878c90b76e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927770"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Disaster recovery och enheten redundans för din virtuella StorSimple-matrisen via Azure-portalen

## <a name="overview"></a>Översikt
Den här artikeln beskriver katastrofåterställning för din Microsoft Azure StorSimple virtuell matrisen inklusive detaljerade instruktioner att växla över till en annan virtuell matris. En redundansväxling kan du flytta data från en *källa* enheten i datacentret till en *mål* enhet. Målenheten kan finnas i samma eller en annan geografisk plats. Enhet för växling vid fel är för hela enheten. Under växling vid fel ändras molndata för källan ägarskap till som målenheten.

Den här artikeln gäller bara för virtuella StorSimple-matriser. Om du vill växla över en 8000-serieenhet, gå till [enheten redundans och disaster recovery av StorSimple-enheten](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Vad är disaster recovery och enheten redundans?

I en (DR) katastrofåterställning, den primära enheten slutar fungera. I det här scenariot kan du flytta molndata som är associerade med den misslyckade enheten till en annan enhet. Du kan använda den primära enheten som den *källa* och ange en annan enhet som den *mål*. Den här processen kallas den *redundans*. Ändra ägarskap och överförs till målenheten under växling vid fel, alla volymer eller resurser från källan. Ingen filtrering av data är tillåten.

DR modelleras som en fullständig enhet återställning med hjälp av den termiska karta-baserade skiktning och spårning. En termisk karta definieras genom att tilldela värdet termiska data baserat på läsa och skriva mönster. Den här termiska mappa sedan nivåer lägsta värme-datasegment till molnet först samtidigt som datasegment hög värme (används mest) på den lokala nivån. Under en Katastrofåterställning använder StorSimple termisk karta för att återställa och rehydrate data från molnet. Enheten hämtar alla volymer/resurser i den senaste senaste säkerhetskopieringen (vilket anges internt) och utför en återställning från säkerhetskopian. Virtuella matrisen samordnar hela DR-processen.

> [!IMPORTANT]
> På källenheten tas bort i slutet av enheten redundans och därför en återställning stöds inte.
> 
> 

Katastrofåterställning är samordnade via funktionen med redundans i enheten och initieras från den **enheter** bladet. Det här bladet tabulates alla StorSimple-enheter som är anslutna till din StorSimple Device Manager-tjänst. Du kan se eget namn, status, etablerad och maximal kapacitet, typ och modell för varje enhet.

## <a name="prerequisites-for-device-failover"></a>Krav för växling vid fel för enheten

### <a name="prerequisites"></a>Krav

För en enhet växling, kontrollerar du att följande krav är uppfyllda:

* Källan måste finnas i en **inaktiverad** tillstånd.
* Målenheten måste visas som **redo att konfigurera** i Azure-portalen. Etablera en virtuell Målmatrisen av samma eller högre kapacitet. Använda lokala webbgränssnittet för att konfigurera och registrera har virtuella Målmatrisen.
  
  > [!IMPORTANT]
  > Försök inte konfigurera den registrerade virtuella enheten via tjänsten. Ingen enhetskonfiguration ska utföras via tjänsten.
  > 
  > 
* Målenheten kan inte ha samma namn som källan.
* Käll- och enheten måste vara av samma typ. Du kan bara redundansväxla virtuella matriskonfiguration som en filserver till en annan filserver. Detsamma gäller för en iSCSI-server.
* För en filserver DR rekommenderar vi att du ansluter målenheten till samma domän som källa. Den här konfigurationen garanterar att behörigheter till resursen löses automatiskt. Endast redundans till en målenhet i samma domän.
* De tillgängliga målenheterna för Katastrofåterställning är enheter som har samma eller större kapacitet jämfört med källan. De enheter som är anslutna till din tjänst men inte uppfyller villkoren för tillräckligt med utrymme är inte tillgängliga som målenheter.

### <a name="other-considerations"></a>Andra överväganden

* För en planerad redundans 
  
  * Vi rekommenderar att du vidtar alla volymer eller resurser på källenheten offline.
  * Vi rekommenderar att du gör en säkerhetskopia av enheten och fortsätt sedan med växling vid fel för att minimera dataförlust. 
* En oplanerad redundans med enheten använder den senaste säkerhetskopian för att återställa data.

### <a name="device-failover-prechecks"></a>Enheten redundans prechecks

Innan DR börjar utför prechecks på enheten. Dessa kontroller att säkerställa att inga fel inträffar när DR påbörjas. Prechecks omfattar:

* Verifiera lagringskontot.
* Kontrollerar anslutningen molnet till Azure.
* Kontrollerar diskutrymme på målenheten.
* Kontrollerar om en iSCSI-server enheten källvolymen har
  
  * giltiga ACR-namn.
  * giltig IQN (högst 220 tecken).
  * giltig CHAP-lösenord (12-16 tecken lång).

Du kan inte fortsätta med DR om något av föregående prechecks misslyckas. Lösa dessa problem och försök sedan DR.

När ar har slutförts överförs ägarskapet till molndata på källan till målenheten. Källenhet är sedan inte längre i portalen. Blockeras åtkomst till alla volymer/resurser på källan och målenheten blir aktiv.

> [!IMPORTANT]
> Om enheten är inte längre tillgänglig, förbrukar den virtuella datorn som du har etablerats på värdsystemet fortfarande resurser. När ar har slutförts kan du ta bort den här virtuella datorn från värdsystemet.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Växla över till en virtuell matris

Vi rekommenderar att etablera, konfigurera och registrera en annan virtuell StorSimple-matris med din StorSimple Device Manager-tjänsten innan du kör den här proceduren.

> [!IMPORTANT]
> 
> * Du kan inte växla över från en StorSimple 8000-serieenhet till en virtuell enhet 1200.
> * Du kan växla över från en virtuell enhet FIPS Federal Information Processing Standard () aktiverade till en annan FIPS-aktiverad enhet eller till en icke-FIPS-enhet som har distribuerats i Government-portalen.


Utför följande steg för att återställa enheten till en mål virtuella StorSimple-enheten.

1. Etablera och konfigurera en målenhet som uppfyller den [krav för växling vid fel för enheten](#prerequisites). Slutför enhetskonfiguration via lokala webbgränssnittet och registrera till din StorSimple Device Manager-tjänsten. Om du skapar en filserver, går du till steg 1 av [som filserver](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Om du skapar en iSCSI-server går du till steg 1 av [konfigurerats som server för iSCSI-](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Ta volymer/resurser offline på värden. Om du vill koppla volymer/resurser finns i operativsystemet – specifika instruktionerna för värden. Om det inte redan offline, måste du ta alla volymer/resurser offline på enheten genom att göra följande.
   
    1. Gå till **enheter** bladet och välj din enhet.
   
    2. Gå till **Inställningar > Hantera > resurser** (eller **Inställningar > Hantera > volymer**). 
   
    3. Välj en resurs/volym, högerklicka och välj **ta offline**. 
   
    4. När du uppmanas att bekräfta att kontrollera **jag förstår konsekvenserna av att ta resursen offline.** 
   
    5. Klicka på **ta offline**.

3. I Enhetshanteraren för StorSimple-tjänsten går du till **Management > enheter**. I den **enheter** bladet Välj och klicka på din källenhet.

4. I din **enheten instrumentpanelen** bladet, klickar du på **inaktivera**.

5. I den **inaktivera** bladet du uppmanas att bekräfta. Inaktivering av enheten är en *permanent* process som inte kan ångras. Du har också en påminnelse om att ta dina resurser/volymer offline på värden. Skriv enhetsnamnet för att bekräfta och klicka på **inaktivera**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Inaktiveringen startar. Du får ett meddelande när inaktiveringen har slutförts.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Enhetens tillstånd ändras på sidan enheter nu till **inaktiverad**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. I den **enheter** bladet Välj och klicka på inaktiverad källenheten för redundans. 
9. I den **enheten instrumentpanelen** bladet, klickar du på **växla över**. 
10. I den **växla över enhet** bladet gör du följande:
    
    1. Källan enhetens fält fylls i automatiskt. Observera den totala datastorleken för källan. Storleken på data ska vara mindre än den tillgängliga kapaciteten på målenheten. Granska informationen som är associerade med källenheten som enhetsnamn, total kapacitet och namnen på de resurser som har redundansväxlats.

    2. Välj den nedrullningsbara listan över tillgängliga enheter en **målenhet**. Endast de enheter som har tillräcklig kapacitet för visas i listrutan.

    3. Kontrollera att **jag förstår att den här åtgärden växlar över data till målenheten**. 

    4. Klicka på **växla över**.
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Initierar ett jobb för växling vid fel och du får ett meddelande. Gå till **enheter > jobb** att övervaka växling vid fel.
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. I den **jobb** bladet du ser ett failover-jobb skapas för källan. Det här jobbet utför DR prechecks.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     När DR prechecks är lyckas, kommer beställningsjobbet starta återställningsjobb för varje resurs/volym som finns på källenheten.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. När redundansväxlingen är klar, går du till den **enheter** bladet.
    
    1. Välj och klicka på den virtuella StorSimple-enheten som användes som målenhet för failover-processen.
    2. Gå till **Inställningar > Management > resurser** (eller **volymer** om iSCSI-server). I den **resurser** bladet kan du visa alla resurser (volymer) från den gamla enheten.
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Du behöver [skapa en DNS-alias](https://support.microsoft.com/kb/168322) så att alla program som försöker ansluta till kan omdirigeras till den nya enheten.

## <a name="errors-during-dr"></a>Fel uppstod vid Katastrofåterställning

**Molnet anslutningen avbrott under Katastrofåterställning**

Om molnet anslutningen avbryts när DR har startats och innan enheten återställningen är slutförd, misslyckas DR. Du får ett meddelande om failore. Målenhet för Katastrofåterställning är markerad som *inte kan användas.* Du kan inte använda samma målenhet för framtida DRs.

**Ingen kompatibel målenheter**

Om tillgängliga målservrar enheter inte har tillräckligt med utrymme, ser du ett fel i syfte att det finns ingen kompatibel målenheter.

**Precheck fel**

Om en av prechecks inte är uppfyllt, se precheck fel.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Katastrofåterställning för verksamhetskontinuitet (BCDR)

En business continuity (BCDR) katastrofåterställning inträffar när hela Azure-datacentret slutar att fungera. Detta kan påverka din StorSimple Device Manager-tjänst och de associera StorSimple-enheterna.

Om StorSimple-enheter som registrerats precis innan en katastrof inträffade behöva dessa StorSimple-enheter som ska tas bort. Efter en katastrof kan du återskapa och konfigurerar enheterna.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du [administrera din virtuella StorSimple-matris med lokala webbgränssnittet](storsimple-ova-web-ui-admin.md).

