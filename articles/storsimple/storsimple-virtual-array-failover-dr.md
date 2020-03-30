---
title: Redundans- och haveriberedskap för StorSimple Virtual Array
description: Läs mer om hur du redundans din StorSimple Virtual Array.
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
ms.openlocfilehash: 464fa05f658dd6e6e25d79f8840ceeb939383149
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467223"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Haveriberedskap och redundansväxling av enhet för StorSimple Virtual Array via Azure-portalen

## <a name="overview"></a>Översikt
I den här artikeln beskrivs haveriberedskapen för microsoft Azure StorSimple Virtual Array, inklusive detaljerade steg för att växla över till en annan virtuell matris. Med en redundans kan du flytta data från en *källenhet* i datacentret till en *målenhet.* Målenheten kan finnas på samma eller en annan geografisk plats. Enheten redundans är för hela enheten. Under redundans ändrar molndata för källenheten ägarskapet till målenhetens.

Den här artikeln gäller endast StorSimple Virtual Arrays. Om du vill växla över en enhet i 8 000-serien går du till [Enhetsväxling och haveriberedskap av din StorSimple-enhet](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Vad är katastrofåterställning och enhetsväxling?

I ett DR-scenario (Disaster Recovery) slutar den primära enheten att fungera. I det här fallet kan du flytta molndata som är associerade med den misslyckade enheten till en annan enhet. Du kan använda den primära enheten som *källa* och ange en annan enhet som *mål*. Den här processen kallas *redundans*. Under redundans ändras alla volymer eller resurser från källenheten ägarskap och överförs till målenheten. Ingen filtrering av data är tillåten.

DR modelleras som en fullständig enhetsåterställning med hjälp av värmekarta-baserad nivåindelning och spårning. En värmekarta definieras genom att data tilldelas ett värmevärde baserat på läs- och skrivmönster. Den här värmekartan nivåer sedan de lägsta värmedatasegmenten till molnet först samtidigt som de hög värmedatasegment (mest använda) finns i den lokala nivån. Under en DR använder StorSimple värmekartan för att återställa och rehydrera data från molnet. Enheten hämtar alla volymer/resurser i den senaste säkerhetskopian (enligt internt) och utför en återställning från säkerhetskopian. Den virtuella matrisen dirigerar hela DR-processen.

> [!IMPORTANT]
> Källenheten tas bort i slutet av enhetens redundans och därför stöds inte en återställning efter fel.
> 
> 

Haveriberedskapet är orkestrerat via enhetens redundansfunktion och initieras från bladet **Enheter.** Det här bladet tar bort alla StorSimple-enheter som är anslutna till Tjänsten StorSimple Device Manager. För varje enhet kan du se eget namn, status, etablerad och maximal kapacitet, typ och modell.

## <a name="prerequisites-for-device-failover"></a>Förhandskrav för enhetsredundans

### <a name="prerequisites"></a>Krav

För en enhetsväxling kontrollerar du att följande förutsättningar är uppfyllda:

* Källenheten måste vara i ett **inaktiverat** tillstånd.
* Målenheten måste visas som **klar för att konfigureras** i Azure-portalen. Etablera en virtuell målmatris med samma eller högre kapacitet. Använd det lokala webbgränssnittet för att konfigurera och registrera den virtuella målmatrisen.
  
  > [!IMPORTANT]
  > Försök inte konfigurera den registrerade virtuella enheten via tjänsten. Ingen enhetskonfiguration bör utföras via tjänsten.
  > 
  > 
* Målenheten kan inte ha samma namn som källenheten.
* Käll- och målenheten måste vara av samma typ. Du kan bara växla över en virtuell matris som konfigurerats som en filserver till en annan filserver. Detsamma gäller för en iSCSI-server.
* För en filserver DR rekommenderar vi att du ansluter målenheten till samma domän som källan. Den här konfigurationen säkerställer att resursbehörigheterna matchas automatiskt. Endast redundans till en målenhet i samma domän stöds.
* De tillgängliga målenheterna för DR är enheter som har samma eller större kapacitet jämfört med källenheten. De enheter som är anslutna till din tjänst men inte uppfyller kriterierna för tillräckligt med utrymme är inte tillgängliga som målenheter.

### <a name="other-considerations"></a>Andra överväganden

* För en planerad redundans:
  
  * Vi rekommenderar att du tar alla volymer eller resurser på källenheten offline.
  * Vi rekommenderar att du tar en säkerhetskopia av enheten och sedan fortsätter med redundansen för att minimera dataförlust.
* För en oplanerad redundans använder enheten den senaste säkerhetskopian för att återställa data.

### <a name="device-failover-prechecks"></a>Förkontroll av enhetsväxling

Innan DR börjar utför enheten förkontroller. Dessa kontroller hjälper till att säkerställa att inga fel uppstår när DR påbörjas. Förkontrollerna inkluderar:

* Validerar lagringskontot.
* Kontrollera molnanslutningen till Azure.
* Kontrollera tillgängligt utrymme på målenheten.
* Kontrollera om en iSCSI-serverkällenhetsvolym har
  
  * giltiga ACR-namn.
  * giltig IQN (högst 220 tecken).
  * giltiga CHAP-lösenord (12-16 tecken långa).

Om någon av de föregående förhandskontrollerna misslyckas kan du inte fortsätta med DR.If any of the preceding prechecks fail, you cannot proceed with the DR. Lös dessa problem och försök sedan dr.

När DR har slutförts överförs ägarskapet av molndata på källenheten till målenheten. Källenheten är då inte längre tillgänglig i portalen. Åtkomsten till alla volymer/resurser på källenheten blockeras och målenheten blir aktiv.

> [!IMPORTANT]
> Även om enheten inte längre är tillgänglig förbrukar den virtuella datorn som du etablerade på värdsystemet fortfarande resurser. När DR har slutförts kan du ta bort den här virtuella datorn från värdsystemet.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Växla över till en virtuell matris

Vi rekommenderar att du etablerar, konfigurerar och registrerar en annan StorSimple-virtuell matris med tjänsten StorSimple Device Manager innan du kör den här proceduren.

> [!IMPORTANT]
> 
> * Det går inte att växla från en StorSimple 8000-serieenhet till en virtuell 1 200-enhet.
> * Du kan växla över från en FIPS-aktiverad virtuell enhet (Federal Information Processing Standard) till en annan FIPS-aktiverad enhet eller till en icke-FIPS-enhet som distribueras i regeringsportalen.


Utför följande steg för att återställa enheten till en virtuell enhet för målet StorSimple.

1. Etablera och konfigurera en målenhet som uppfyller [förutsättningarna för enhetsväxling](#prerequisites). Slutför enhetskonfigurationen via det lokala webbgränssnittet och registrera den i Tjänsten StorSimple Device Manager. Om du skapar en filserver går du till steg 1 [för att konfigurera som filserver](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Om du skapar en iSCSI-server går du till steg 1 [för att konfigurera som iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Ta volymer/resurser offline på värden. Om du vill koppla från volymerna/resurserna läser du värdens operativsystemets specifika instruktioner. Om du inte redan är offline måste du ta alla volymer/resurser offline på enheten genom att göra följande.
   
    1. Gå till **Bladet Enheter** och välj enhet.
   
    2. Gå till **Inställningar > Hantera >-resurser** (eller **Inställningar > Hantera > volymer).** 
   
    3. Välj en resurs/volym, högerklicka och välj **Ta offline**. 
   
    4. När du uppmanas att bekräfta, kontrollera **jag förstår effekten av att ta denna aktie offline.** 
   
    5. Klicka på **Koppla från**.

3. Gå till **Hantering > enheter**i tjänsten StorSimple Device Manager . Markera och klicka på källenheten i bladet **Enheter.**

4. Klicka på **Inaktivera**i **instrumentpanelsbladet** för enheten.

5. I **bladet Inaktivera** uppmanas du att bekräfta det. Enhetsinaktivering är en *permanent* process som inte kan ångras. Du påminns också om att ta dina resurser/volymer offline på värden. Skriv enhetsnamnet för att bekräfta och klicka på **Inaktivera**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Inaktiveringen startar. Du får ett meddelande när avaktiveringen har slutförts.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. På sidan Enheter ändras enhetstillståndet nu till **Inaktiverat**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. I bladet **Enheter** väljer och klickar du på den inaktiverade källenheten för redundans. 
9. Klicka på **Växla över**i **instrumentpanelsbladet.** 
10. Gör följande i **bladet Överst över-enhet:**
    
    1. Källenhetsfältet fylls i automatiskt. Observera den totala datastorleken för källenheten. Datastorleken bör vara mindre än den tillgängliga kapaciteten på målenheten. Granska information som är associerad med källenheten, till exempel enhetsnamn, total kapacitet och namnen på de resurser som har misslyckats över.

    2. Välj en **Target-enhet**i listrutan över tillgängliga enheter . Endast de enheter som har tillräcklig kapacitet visas i listrutan.

    3. Kontrollera att **jag förstår att den här åtgärden kommer att växla över data till målenheten**. 

    4. Klicka på **Växla över**.
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Ett redundansjobb initieras och du får ett meddelande. Gå till **Enheter > jobb** för att övervaka redundansen.
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. I bladet **Jobb** visas ett redundansjobb som skapats för källenheten. Det här jobbet utför DR-förkontrollerna.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     När DR-förkontrollerna har framgångsrikts kommer redundansjobbet att skapa återställningsjobb för varje resurs/volym som finns på källenheten.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. När redundansen är klar går du till bladet **Enheter.**
    
    1. Markera och klicka på den StorSimple-enhet som användes som målenhet för redundansprocessen.
    2. Gå till **Inställningar > Hantering >-resurser** (eller **Volymer** om iSCSI-server). I bladet **Resurser** kan du visa alla resurser (volymer) från den gamla enheten.
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Du måste [skapa ett DNS-alias](https://support.microsoft.com/kb/168322) så att alla program som försöker ansluta kan omdirigeras till den nya enheten.

## <a name="errors-during-dr"></a>Fel under DR

**Avbrott i molnanslutningen under DR**

Om molnanslutningen avbryts efter att DR har startats och innan enhetsåterställningen är klar misslyckas DR.If the cloud connectivity is disrupted after DR has started and before the device restore is complete, the DR will fail. Du får ett felmeddelande. Målenheten för DR är markerad som *oanvändbar.* Du kan inte använda samma målenhet för framtida datadr.

**Inga kompatibla målenheter**

Om de tillgängliga målenheterna inte har tillräckligt med utrymme visas ett fel om att det inte finns några kompatibla målenheter.

**Förkontrollfel**

Om en av förkontrollerna inte är nöjd visas fel före kontroll.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Återställning av haveuliker för affärskontinuitet (BCDR)

Ett SCENARIO för återställning av affärskontinuitetskatastrof (BCDR) inträffar när hela Azure-datacentret slutar fungera. Detta kan påverka tjänsten StorSimple Device Manager och tillhörande StorSimple-enheter.

Om det finns StorSimple-enheter som registrerades strax innan en katastrof inträffade kan dessa StorSimple-enheter behöva tas bort. Efter katastrofen kan du återskapa och konfigurera dessa enheter.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [administrerar den virtuella storsimple-matrisen med hjälp av det lokala webbgränssnittet](storsimple-ova-web-ui-admin.md).

