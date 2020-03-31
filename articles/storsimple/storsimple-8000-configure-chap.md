---
title: Konfigurera CHAP för StorSimple 8000-seriens enhet | Microsoft-dokument
description: Beskriver hur du konfigurerar CHAP (Challenge Handshake Authentication Protocol) på en StorSimple-enhet.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267968"
---
# <a name="configure-chap-for-your-storsimple-device"></a>Konfigurera CHAP för StorSimple-enheten

I den här självstudien beskrivs hur du konfigurerar CHAP för Din StorSimple-enhet. Proceduren som beskrivs i den här artikeln gäller för Enheter i StorSimple 8000-serien.

CHAP står för Challenge Handshake Authentication Protocol. Det är ett autentiseringsschema som används av servrar för att verifiera fjärrklienters identitet. Verifieringen baseras på ett delat lösenord eller en hemlighet. CHAP kan vara enkelriktad (enkelriktad) eller ömsesidig (dubbelriktad). Ett sätt CHAP är när målet autentiserar en initierare. I ömsesidig eller omvänd CHAP autentiserar målet initieraren och initieraren autentiserar sedan målet. Autentisering av initierare kan implementeras utan målautentisering. Målautentisering kan dock endast implementeras om initierarautentisering också implementeras.

Vi rekommenderar att du använder CHAP för att förbättra iSCSI-säkerheten.

> [!NOTE]
> Tänk på att IPSEC för närvarande inte stöds på StorSimple-enheter.

CHAP-inställningarna på StorSimple-enheten kan konfigureras på följande sätt:

* Enkelriktad eller enkelriktad autentisering
* Dubbelriktad eller ömsesidig eller omvänd autentisering

I vart och ett av dessa fall måste portalen för enheten och programvaran för server iSCSI-initierarprogramvaran konfigureras. De detaljerade stegen för den här konfigurationen beskrivs i följande självstudiekurs.

## <a name="unidirectional-or-one-way-authentication"></a>Enkelriktad eller enkelriktad autentisering

Vid enkelriktad autentisering autentiserar målet initieraren. Den här autentiseringen kräver att du konfigurerar CHAP-initierarinställningarna på StorSimple-enheten och iSCSI-initieraren på värden. De detaljerade procedurerna för StorSimple-enheten och Windows-värden beskrivs nedan.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Så här konfigurerar du enheten för enkelriktad autentisering

1. Gå till tjänsten StorSimple Device Manager i Azure-portalen. Klicka på **Enheter** och välj och klicka på en enhet som du vill konfigurera CHAP för. Gå till **Enhetsinställningar > Säkerhet**. Klicka på **CHAP**i bladet **Säkerhetsinställningar** .
   
    ![CHAP-initiativtagare](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. I **CHAP-bladet** och i **CHAP-initiatorsektionen:**
   
   1. Ange ett användarnamn för CHAP-initieraren.
   2. Ange ett lösenord för CHAP-initieraren.
      
      > [!IMPORTANT]
      > CHAP-användarnamnet måste innehålla färre än 233 tecken. CHAP-lösenordet måste vara mellan 12 och 16 tecken. Ett längre användarnamn eller lösenord resulterar i ett autentiseringsfel på Windows-värden.
   
   3. Bekräfta lösenordet.

       ![CHAP-initiativtagare](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Klicka på **Spara**. Ett bekräftelsemeddelande visas. Spara ändringarna genom att klicka på **OK**.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Så här konfigurerar du enkelriktad autentisering på Windows-värdservern
1. Starta iSCSI-initieraren på Windows-värdservern.
2. Gör följande i fönstret **egenskaper för iSCSI-initierare:**
   
   1. Klicka på fliken **Identifiering.**
      
       ![iSCSI-initieraregenskaper](./media/storsimple-configure-chap/IC740944.png)
   2. Klicka på **Upptäck portal**.
3. I dialogrutan **Upptäck målportal:**
   
   1. Ange enhetens IP-adress.
   2. Klicka på **Avancerat**.
      
       ![Upptäck målportalen](./media/storsimple-configure-chap/IC740945.png)
4. I dialogrutan **Avancerade inställningar:**
   
   1. Markera kryssrutan **Aktivera CHAP-inloggning.**
   2. Ange det användarnamn som du angav för CHAP-initieraren i Azure-portalen i fältet **Namn.**
   3. Ange det lösenord som du angav för CHAP-initieraren i Azure-portalen i fältet **Målhemlighet.**
   4. Klicka på **OK**.
      
       ![Avancerade inställningar allmänt](./media/storsimple-configure-chap/IC740946.png)
5. På fliken **Mål** i fönstret **egenskaper för iSCSI-initierare** ska enhetsstatusen visas som **Ansluten**. Om du använder en StorSimple 1200-enhet monteras varje volym som ett iSCSI-mål. Därför måste steg 3-4 upprepas för varje volym.
   
    ![Volymer monterade som separata mål](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Om du ändrar iSCSI-namnet används det nya namnet för nya iSCSI-sessioner. Nya inställningar används inte för befintliga sessioner förrän du loggar ut och loggar in igen.

Mer information om hur du konfigurerar CHAP på Värdservern i Windows finns i [Ytterligare överväganden](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Dubbelriktad eller ömsesidig autentisering

Vid dubbelriktad autentisering autentiserar målet initieraren och initieraren autentiserar målet. Den här proceduren kräver att användaren konfigurerar CHAP-inställningarna, återför CHAP-inställningarna på enheten och iSCSI Initiator-programvaran på värden. Följande procedurer beskriver stegen för att konfigurera ömsesidig autentisering på enheten och på Windows-värden.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Så här konfigurerar du enheten för ömsesidig autentisering

1. Gå till tjänsten StorSimple Device Manager i Azure-portalen. Klicka på **Enheter** och välj och klicka på en enhet som du vill konfigurera CHAP för. Gå till **Enhetsinställningar > Säkerhet**. Klicka på **CHAP**i bladet **Säkerhetsinställningar** .
   
    ![CHAP-mål](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Bläddra nedåt på den här sidan och i avsnittet **CHAP-mål:**
   
   1. Ange ett **Reverse CHAP-användarnamn** för enheten.
   2. Ange ett **Reverse CHAP-lösenord** för enheten.
   3. Bekräfta lösenordet.
3. I avsnittet **CHAP Initiator:**
   
   1. Ange ett **användarnamn** för enheten.
   2. Ange ett **lösenord** för enheten.
   3. Bekräfta lösenordet.

       ![CHAP-initiativtagare](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Klicka på **Spara**. Ett bekräftelsemeddelande visas. Spara ändringarna genom att klicka på **OK**.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Så här konfigurerar du dubbelriktad autentisering på Windows-värdservern

1. Starta iSCSI-initieraren på Windows-värdservern.
2. Klicka på fliken Konfiguration i fönstret egenskaper **för** **iSCSI-initierare.**
3. Klicka på **CHAP**.
4. I dialogrutan **iSCSI Initiator Mutual CHAP Secret:**
   
   1. Skriv det **omvända CHAP-lösenordet** som du har konfigurerat i Azure-portalen.
   2. Klicka på **OK**.
      
       ![iSCSI-initiativtagare ömsesidig CHAP hemlighet](./media/storsimple-configure-chap/IC740949.png)
5. Klicka på fliken **Mål.**
6. Klicka på **knappen Anslut.** 
7. Klicka på **Avancerat**i dialogrutan **Anslut till mål.**
8. I dialogrutan **Avancerade egenskaper:**
   
   1. Markera kryssrutan **Aktivera CHAP-inloggning.**
   2. Ange det användarnamn som du angav för CHAP-initieraren i Azure-portalen i fältet **Namn.**
   3. Ange det lösenord som du angav för CHAP-initieraren i Azure-portalen i fältet **Målhemlighet.**
   4. Markera kryssrutan **Utför ömsesidig autentisering.**
      
       ![Avancerad ömsesidig autentisering av inställningar](./media/storsimple-configure-chap/IC740950.png)
   5. Klicka på **OK** för att slutföra CHAP-konfigurationen

Mer information om hur du konfigurerar CHAP på Värdservern i Windows finns i [Ytterligare överväganden](#additional-considerations).

## <a name="additional-considerations"></a>Annat som är bra att tänka på

**Funktionen Snabbanslutning** stöder inte anslutningar som har CHAP aktiverat. När CHAP är aktiverat kontrollerar du att du använder knappen **Anslut** som är tillgänglig på fliken **Mål** för att ansluta till ett mål.

![Anslut till målet](./media/storsimple-configure-chap/IC740947.png)

Markera kryssrutan **Lägg till den här anslutningen i listan Över Favoritmål** i dialogrutan Anslut till **mål** som visas. Den här markeringen säkerställer att varje gång datorn startas om görs ett försök att återställa anslutningen till iSCSI-favoritmålen.

## <a name="errors-during-configuration"></a>Fel under konfigurationen

Om CHAP-konfigurationen är felaktig kommer det troligen att visas ett felmeddelande **om autentiseringsfel.**

## <a name="verification-of-chap-configuration"></a>Verifiering av CHAP-konfiguration

Du kan kontrollera att CHAP används genom att utföra följande steg.

#### <a name="to-verify-your-chap-configuration"></a>Så här verifierar du CHAP-konfigurationen
1. Klicka på **Favoritmål**.
2. Välj det mål som du har aktiverat autentisering för.
3. Klicka på **Information**.
   
    ![iSCSI-initieraregenskaper favoritmål](./media/storsimple-configure-chap/IC740951.png)
4. I dialogrutan **Favoritmålinformation** noterar du posten i fältet **Autentisering.** Om konfigurationen lyckades, bör det säga **CHAP**.
   
    ![Information om favoritmål](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [StorSimple-säkerhet](storsimple-8000-security.md).
* Läs mer om [hur du använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

