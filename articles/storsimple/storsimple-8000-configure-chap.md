---
title: Konfigurera CHAP för StorSimple 8000-serieenhet | Microsoft Docs
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
ms.openlocfilehash: c1a558769ffaa52ed2e996a2b537a5ea409101bd
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="configure-chap-for-your-storsimple-device"></a>Konfigurera CHAP för din StorSimple-enhet

Den här självstudiekursen beskrivs hur du konfigurerar CHAP för din StorSimple-enhet. Det förfarande som beskrivs i den här artikeln gäller StorSimple 8000-serien enheter.

CHAP står för Challenge Handshake Authentication Protocol. Det är ett autentiseringsschema som används av servrar för att verifiera identiteten för fjärrklienter. Verifieringen baseras på en delad lösenord eller hemlighet. CHAP kan vara ett sätt (enkelriktad) eller ömsesidig (dubbelriktad). Ett sätt CHAP är när målet autentiserar en initierare. I ömsesidig eller omvänd CHAP målet autentiserar initieraren och sedan initieraren målet. Initieraren autentisering kan genomföras utan target autentisering. Mål-autentisering kan dock implementeras endast om initieraren authentication implementeras också.

Som bästa praxis rekommenderar vi att du använder CHAP för att förbättra säkerheten för iSCSI.

> [!NOTE]
> Tänk på att IPSEC inte stöds för närvarande på StorSimple-enheter.

CHAP-inställningar på StorSimple-enheten kan konfigureras på följande sätt:

* Enkelriktade eller envägs-autentisering
* Dubbelriktad eller ömsesidig eller omvänd autentisering

Portal för enheten och serverprogramvara iSCSI-initierare måste konfigureras i dessa fall. Detaljerade anvisningar för den här konfigurationen beskrivs i följande kursen.

## <a name="unidirectional-or-one-way-authentication"></a>Enkelriktade eller envägs-autentisering

Målet autentiserar initieraren i enkelriktad autentisering. Den här autentiseringen måste du konfigurera inställningarna för CHAP-initieraren på StorSimple-enheten och iSCSI-initieraren programvaran på värden. Detaljerade anvisningar för din StorSimple-enhet och Windows-värd beskrivs nedan.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Du konfigurerar din enhet för enkelriktad autentisering

1. Gå till Enhetshanteraren för StorSimple-tjänsten i Azure-portalen. Klicka på **enheter** och markera och klicka på en enhet som du vill konfigurera CHAP för. Gå till **Enhetsinställningar > säkerhet**. I den **säkerhetsinställningar** bladet, klickar du på **CHAP**.
   
    ![CHAP-initieraren](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. I den **CHAP** bladet och i den **CHAP-initieraren** avsnitt:
   
   1. Ange ett användarnamn för CHAP-initieraren.
   2. Ange ett lösenord för CHAP-initieraren.
      
    > [!IMPORTANT]
    > CHAP-användarnamn måste innehålla färre än 233 tecken. CHAP-lösenord måste vara mellan 12 och 16 tecken. Ett längre användarnamn eller lösenord resulterar i ett autentiseringsfel på Windows-värd.
   
   3. Bekräfta lösenordet.

       ![CHAP-initieraren](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Klicka på **Spara**. Ett bekräftelsemeddelande visas. Spara ändringarna genom att klicka på **OK**.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Så här konfigurerar du enkelriktad autentisering på värdservern för Windows
1. Starta iSCSI-initieraren på värdservern för Windows.
2. I den **iSCSI-Initieraregenskaper** och utför följande steg:
   
   1. Klicka på den **identifiering** fliken.
      
       ![iSCSI-initieraregenskaper](./media/storsimple-configure-chap/IC740944.png)
   2. Klicka på **identifiera Portal**.
3. I den **identifiera målportal** dialogrutan:
   
   1. Ange IP-adressen för din enhet.
   2. Klicka på **avancerade**.
      
       ![Identifiera målportal](./media/storsimple-configure-chap/IC740945.png)
4. I den **avancerade inställningar** dialogrutan:
   
   1. Välj den **aktivera CHAP inloggning** kryssrutan.
   2. I den **namn** fältet, ange det användarnamn som du angav för CHAP-initieraren på Azure-portalen.
   3. I den **mål hemlighet** fältet, ange lösenordet du angav för CHAP-initieraren på Azure-portalen.
   4. Klicka på **OK**.
      
       ![Avancerade inställningar som är allmänt](./media/storsimple-configure-chap/IC740946.png)
5. På den **mål** för den **iSCSI-Initieraregenskaper** fönster, enhetens status visas som **ansluten**. Om du använder en enhet med StorSimple 1200 monterade volymerna som ett iSCSI-mål. Steg 3 – 4 måste därför upprepas för varje volym.
   
    ![Volymerna monterade som separata mål](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Om du ändrar iSCSI-namnet, används det nya namnet för den nya iSCSI-sessioner. Nya inställningar används inte för befintliga sessioner förrän du logga ut och logga in igen.

Mer information om hur du konfigurerar CHAP på värdservern Windows går du till [ytterligare överväganden](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Dubbelriktad eller ömsesidig autentisering

I dubbelriktad autentisering målet autentiserar initieraren och sedan initieraren målet. Den här proceduren kräver att användaren konfigurerar inställningar för CHAP-initieraren på omvänd CHAP-inställningar på enheten och iSCSI-initieraren programvaran på värden. I följande procedurer beskrivs hur du konfigurerar ömsesidig autentisering på enheten och på Windows-värd.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Du konfigurerar din enhet för ömsesidig autentisering

1. Gå till Enhetshanteraren för StorSimple-tjänsten i Azure-portalen. Klicka på **enheter** och markera och klicka på en enhet som du vill konfigurera CHAP för. Gå till **Enhetsinställningar > säkerhet**. I den **säkerhetsinställningar** bladet, klickar du på **CHAP**.
   
    ![CHAP-målet](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Rulla ned på den här sidan och i den **CHAP Target** avsnitt:
   
   1. Ange en **omvänd CHAP-användarnamn** för din enhet.
   2. Ange en **omvänd CHAP-lösenord** för din enhet.
   3. Bekräfta lösenordet.
3. I den **CHAP-initieraren** avsnitt:
   
   1. Ange en **användarnamn** för din enhet.
   2. Ange en **lösenord** för din enhet.
   3. Bekräfta lösenordet.

       ![CHAP-initieraren](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Klicka på **Spara**. Ett bekräftelsemeddelande visas. Spara ändringarna genom att klicka på **OK**.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Konfigurera dubbelriktad autentisering på värdservern för Windows

1. Starta iSCSI-initieraren på värdservern för Windows.
2. I den **iSCSI-Initieraregenskaper** -fönstret klickar du på den **Configuration** fliken.
3. Klicka på **CHAP**.
4. I den **iSCSI Initiator ömsesidig CHAP Secret** dialogrutan:
   
   1. Typ av **omvänd CHAP-lösenord** som du konfigurerade i Azure-portalen.
   2. Klicka på **OK**.
      
       ![iSCSI-initieraren ömsesidig CHAP-hemlighet](./media/storsimple-configure-chap/IC740949.png)
5. Klicka på den **mål** fliken.
6. Klicka på den **Anslut** knappen. 
7. I den **ansluta till målet** dialogrutan klickar du på **Avancerat**.
8. I den **avancerade egenskaper** dialogrutan:
   
   1. Välj den **aktivera CHAP inloggning** kryssrutan.
   2. I den **namn** fältet, ange det användarnamn som du angav för CHAP-initieraren på Azure-portalen.
   3. I den **mål hemlighet** fältet, ange lösenordet du angav för CHAP-initieraren på Azure-portalen.
   4. Välj den **utför ömsesidig autentisering** kryssrutan.
      
       ![Avancerade inställningar ömsesidig autentisering](./media/storsimple-configure-chap/IC740950.png)
   5. Klicka på **OK** att slutföra CHAP-konfiguration

Mer information om hur du konfigurerar CHAP på värdservern Windows går du till [ytterligare överväganden](#additional-considerations).

## <a name="additional-considerations"></a>Annat som är bra att tänka på

Den **Snabbanslut** funktionen har inte stöd för anslutningar som har aktiverat CHAP. När CHAP är aktiverat, kontrollera att du använder den **Anslut** knapp som finns på den **mål** att ansluta till ett mål.

![Ansluta till målet](./media/storsimple-configure-chap/IC740947.png)

I den **Anslut till målet** dialogrutan som visas, väljer du den **lägger till den här anslutningen i listan med favoritmål** kryssrutan. Det här valet garanterar att ett försök görs att återställa anslutningen till favorit iSCSI-mål varje gång datorn startas om.

## <a name="errors-during-configuration"></a>Fel under konfigurationen

Om CHAP-konfigurationen är felaktig och det är sannolikt att se en **autentiseringsfel** felmeddelande.

## <a name="verification-of-chap-configuration"></a>Verifiering av CHAP-konfiguration

Du kan verifiera att CHAP används genom att utföra följande steg.

#### <a name="to-verify-your-chap-configuration"></a>Kontrollera din CHAP-konfiguration
1. Klicka på **favorit mål**.
2. Välj det mål som du har aktiverat autentisering.
3. Klicka på **information**.
   
    ![iSCSI-initieraren egenskaper favorit mål](./media/storsimple-configure-chap/IC740951.png)
4. I den **information om favorit mål** dialogrutan anteckna posten i den **autentisering** fältet. Om konfigurationen lyckades, ska det stå **CHAP**.
   
    ![Information om favorit mål](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [StorSimple-säkerhet](storsimple-8000-security.md).
* Lär dig mer om [använder Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

