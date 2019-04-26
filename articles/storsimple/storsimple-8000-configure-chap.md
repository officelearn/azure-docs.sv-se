---
title: Konfigurera CHAP för enhet i StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du konfigurerar CHAP Challenge Handshake Authentication Protocol () på en StorSimple-enhet.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: efc116c278bfe72419800603a3b365f461fe0a28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362856"
---
# <a name="configure-chap-for-your-storsimple-device"></a>Konfigurera CHAP för din StorSimple-enhet

Den här självstudien beskrivs hur du konfigurerar CHAP för din StorSimple-enhet. Proceduren som beskrivs i den här artikeln gäller för enheter i StorSimple 8000-serien.

CHAP står för Challenge Handshake Authentication Protocol. Det är ett schema för autentiseringsmetoder som används av servrar för att verifiera identiteten för fjärrklienter. Verifieringen baseras på en delad lösenord eller en hemlighet. CHAP kan vara ett sätt (enkelriktade) eller ömsesidig (dubbelriktad). Ett sätt CHAP är när målet autentiserar en initierare. Målet autentiserar initieraren i ömsesidig eller omvänd CHAP och sedan initieraren autentiserar målet. Initierare autentisering kan genomföras utan mål-autentisering. Mål-autentisering kan dock implementeras endast om initierare autentisering genomförs även.

Som bästa praxis rekommenderar vi att du använder CHAP för att förbättra säkerheten för iSCSI.

> [!NOTE]
> Tänk på att IPSEC inte för närvarande stöds på StorSimple-enheter.

CHAP-inställningar på StorSimple-enheten kan konfigureras på följande sätt:

* Enkelriktade eller enkelriktad autentisering
* Dubbelriktad eller ömsesidig eller omvänd autentisering

Portal för enheten och serverprogramvara för iSCSI-initierare måste konfigureras i var och en av dessa fall. Detaljerade anvisningar för den här konfigurationen beskrivs i följande självstudie.

## <a name="unidirectional-or-one-way-authentication"></a>Enkelriktade eller enkelriktad autentisering

Enkelriktade Authentication autentiserar målet initieraren. Den här autentiseringen måste du konfigurera inställningarna för CHAP-initierare på StorSimple-enheten och iSCSI-initierare programvara på värden. Detaljerade anvisningar för din StorSimple-enheten och Windows-värden beskrivs nedan.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Du konfigurerar din enhet för enkelriktad autentisering

1. Gå till din StorSimple Device Manager-tjänsten i Azure-portalen. Klicka på **enheter** och markera och klicka på en enhet som du vill konfigurera CHAP för. Gå till **Enhetsinställningar > säkerhet**. I den **säkerhetsinställningar** bladet klickar du på **CHAP**.
   
    ![CHAP-initierare](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. I den **CHAP** bladet och i den **CHAP-initierare** avsnittet:
   
   1. Ange ett användarnamn för CHAP-initierare.
   2. Ange ett lösenord för CHAP-initierare.
      
      > [!IMPORTANT]
      > CHAP-användarnamnet måste innehålla färre än 233 tecken. CHAP-lösenordet måste vara mellan 12 och 16 tecken. En längre användarnamnet eller lösenordet resulterar i ett autentiseringsfel på Windows-värd.
   
   3. Bekräfta lösenordet.

       ![CHAP-initierare](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Klicka på **Spara**. Ett bekräftelsemeddelande visas. Spara ändringarna genom att klicka på **OK**.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Konfigurera enkelriktad autentisering på Windows-värdservern
1. Starta iSCSI-initieraren på värdservern för Windows.
2. I den **iSCSI-Initieraregenskaper** och utför följande steg:
   
   1. Klicka på den **identifiering** fliken.
      
       ![iSCSI-initieraregenskaper](./media/storsimple-configure-chap/IC740944.png)
   2. Klicka på **identifiera Portal**.
3. I den **identifiera målportal** dialogrutan:
   
   1. Ange IP-adressen för din enhet.
   2. Klicka på **Avancerat**.
      
       ![Identifiera målportal](./media/storsimple-configure-chap/IC740945.png)
4. I den **avancerade inställningar** dialogrutan:
   
   1. Välj den **aktivera CHAP inloggning** markerar du kryssrutan.
   2. I den **namn** fältet, ange det användarnamn som du angav för CHAP-initierare i Azure-portalen.
   3. I den **Target hemlighet** fältet, anger du det lösenord du angav för CHAP-initierare i Azure-portalen.
   4. Klicka på **OK**.
      
       ![Avancerade inställningar som är allmänt](./media/storsimple-configure-chap/IC740946.png)
5. På den **mål** fliken den **iSCSI-Initieraregenskaper** fönstret enhetens status borde visas som **ansluten**. Om du använder en enhet med StorSimple 1200 monterade volymerna som en iSCSI-mål. Steg 3 – 4 måste därför upprepas för varje volym.
   
    ![Volymer som monterats som separata mål](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Om du ändrar iSCSI-namnet, används det nya namnet för den nya iSCSI-sessioner. Nya inställningarna används inte för befintliga sessioner förrän du loggar ut och logga in igen.

Mer information om hur du konfigurerar CHAP på värdservern Windows går du till [ytterligare överväganden](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Dubbelriktad eller ömsesidig autentisering

Målet autentiserar initieraren och sedan initieraren autentiserar målet i dubbelriktad autentisering. Den här proceduren kräver att användaren att konfigurera inställningarna för CHAP-initierare, inställningar för omvänt CHAP på enheten och iSCSI-initierare programvara på värden. I följande procedurer beskrivs stegen för att konfigurera ömsesidig autentisering på enheten och på Windows-värd.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Du konfigurerar din enhet för ömsesidig autentisering

1. Gå till din StorSimple Device Manager-tjänsten i Azure-portalen. Klicka på **enheter** och markera och klicka på en enhet som du vill konfigurera CHAP för. Gå till **Enhetsinställningar > säkerhet**. I den **säkerhetsinställningar** bladet klickar du på **CHAP**.
   
    ![CHAP-mål](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Rulla nedåt i den här sidan i den **CHAP-mål** avsnittet:
   
   1. Ange en **omvänd CHAP-användarnamn** för din enhet.
   2. Ange en **lösenord** för din enhet.
   3. Bekräfta lösenordet.
3. I den **CHAP-initierare** avsnittet:
   
   1. Ange en **användarnamn** för din enhet.
   2. Ange en **lösenord** för din enhet.
   3. Bekräfta lösenordet.

       ![CHAP-initierare](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Klicka på **Spara**. Ett bekräftelsemeddelande visas. Spara ändringarna genom att klicka på **OK**.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Konfigurera dubbelriktad autentisering på Windows-värdservern

1. Starta iSCSI-initieraren på värdservern för Windows.
2. I den **iSCSI-Initieraregenskaper** fönstret klickar du på den **Configuration** fliken.
3. Klicka på **CHAP**.
4. I den **iSCSI-initieraren ömsesidig CHAP-hemlighet** dialogrutan:
   
   1. Skriv den **omvänd CHAP-lösenord** som du konfigurerade i Azure-portalen.
   2. Klicka på **OK**.
      
       ![iSCSI-initierare ömsesidig CHAP-hemlighet](./media/storsimple-configure-chap/IC740949.png)
5. Klicka på den **mål** fliken.
6. Klicka på den **Connect** knappen. 
7. I den **ansluta till målet** dialogrutan klickar du på **Avancerat**.
8. I den **avancerade egenskaper** dialogrutan:
   
   1. Välj den **aktivera CHAP inloggning** markerar du kryssrutan.
   2. I den **namn** fältet, ange det användarnamn som du angav för CHAP-initierare i Azure-portalen.
   3. I den **Target hemlighet** fältet, anger du det lösenord du angav för CHAP-initierare i Azure-portalen.
   4. Välj den **utför ömsesidig autentisering** markerar du kryssrutan.
      
       ![Avancerade inställningar ömsesidig autentisering](./media/storsimple-configure-chap/IC740950.png)
   5. Klicka på **OK** att slutföra konfigurationen av CHAP

Mer information om hur du konfigurerar CHAP på värdservern Windows går du till [ytterligare överväganden](#additional-considerations).

## <a name="additional-considerations"></a>Annat som är bra att tänka på

Den **Snabbanslut** funktionen stöder inte anslutningar som har aktiverat CHAP. När CHAP har aktiverats kan du se till att du använder den **Connect** knappen som är tillgänglig på den **mål** flik för att ansluta till ett mål.

![Ansluta till mål](./media/storsimple-configure-chap/IC740947.png)

I den **Anslut till målet** dialogrutan som visas, väljer du den **lägga till den här anslutningen i listan över favoritmål** markerar du kryssrutan. Det här valet garanterar att varje gång datorn startas om, görs ett försök att återställa anslutningen till favorit iSCSI-mål.

## <a name="errors-during-configuration"></a>Fel vid konfiguration av

Om din CHAP-konfigurationen är felaktig kommer du förmodligen kommer att se en **autentiseringsfel** felmeddelande.

## <a name="verification-of-chap-configuration"></a>Verifiering av CHAP-konfiguration

Du kan kontrollera att CHAP används genom att utföra följande steg.

#### <a name="to-verify-your-chap-configuration"></a>Kontrollera din CHAP-konfiguration
1. Klicka på **favoritmål**.
2. Välj målet som du har aktiverat autentisering.
3. Klicka på **information**.
   
    ![iSCSI-initierare egenskaper favorit mål](./media/storsimple-configure-chap/IC740951.png)
4. I den **favorit målinformation** dialogrutan anteckna posten i den **autentisering** fält. Om konfigurationen lyckades, ska det stå **CHAP**.
   
    ![Favorit målinformation](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [StorSimple-säkerhet](storsimple-8000-security.md).
* Läs mer om [med StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

