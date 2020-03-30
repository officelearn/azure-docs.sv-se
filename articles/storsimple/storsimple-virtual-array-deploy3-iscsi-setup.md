---
title: Microsoft Azure StorSimple Virtual Array iSCSI-serverinstallation | Microsoft-dokument
description: Beskriver hur du utför den första installationen, registrerar StorSimple iSCSI-servern och slutför enhetskonfigurationen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 4560ca2b07826e2a071f515f147dfab8cbec3624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254500"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Distribuera StorSimple Virtual Array – Konfigurera som en iSCSI-server via Azure Portal

![iscsi-inställningsprocessflöde](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Den här självstudien gäller för Microsoft Azure StorSimple Virtual Array. I den här självstudien beskrivs hur du utför den första installationen, registrerar storSimple iSCSI-servern, slutför enhetskonfigurationen och skapar, monterar, initierar och formaterar volymer på den virtuella storsimple-matrisen som konfigurerats som en iSCSI-server. 

De förfaranden som beskrivs här tar cirka 30 minuter till 1 timme att slutföra. Informationen som publiceras i den här artikeln gäller endast StorSimple Virtual Arrays.

## <a name="setup-prerequisites"></a>Nödvändiga nödvändiga komponenter

Innan du konfigurerar och konfigurerar den virtuella storsimple-matrisen ska du se till att:

* Du har etablerat en virtuell matris och anslutit till den enligt beskrivningen i [Deploy StorSimple Virtual Array - Etablera en virtuell matris i Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) eller [Distribuera StorSimple Virtual Array - Etablera en virtuell matris i VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Du har tjänstregistreringsnyckeln från Tjänsten StorSimple Device Manager som du har skapat för att hantera dina virtuella storsimple-matriser. Mer information finns i **steg 2: Hämta tjänstregistreringsnyckeln** i [Deploy StorSimple Virtual Array - Förbered portalen](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Om det här är den andra eller efterföljande virtuella matrisen som du registrerar med en befintlig StorSimple Enhetshanteraren-tjänst bör du ha krypteringsnyckeln för tjänstdata. Den här nyckeln genererades när den första enheten registrerades med den här tjänsten. Om du har tappat bort den här nyckeln läser **du Hämta krypteringsnyckeln** för tjänstdata i [Använd webbgränssnittet för att administrera den virtuella storsimple-matrisen](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Steg-för-steg-inställningar

Använd följande steg-för-steg-instruktioner för att konfigurera den virtuella storsimple-matrisen:

* [Steg 1: Slutför den lokala webbgränssnittskonfigurationen och registrera enheten](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* Steg 2: Slutför den nödvändiga enhetskonfigurationen
* [Steg 3: Lägga till en volym](#step-3-add-a-volume)
* [Steg 4: Montera, initiera och formatera en volym](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Steg 1: Slutför den lokala webbgränssnittskonfigurationen och registrera enheten

#### <a name="to-complete-the-setup-and-register-the-device"></a>Så här slutför du installationen och registrerar enheten

1. Öppna ett webbläsarfönster. Så här ansluter du till webbgränssnittstypen:
   
    `https://<ip-address of network interface>`
   
    Använd anslutnings-URL:en som noterades i föregående steg. Ett felmeddelande visas om att det har uppstått ett problem med webbplatsens säkerhetscertifikat. Klicka på **Fortsätt till den här webbsidan**.
   
    ![fel i säkerhetscertifikatet](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Logga in på webbgränssnittet för din virtuella enhet som **StorSimpleAdmin**. Ange enhetsadministratörslösenordet som du ändrade i steg 3: Starta den virtuella enheten i [Deploy StorSimple Virtual Array - Etablera en virtuell enhet i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) eller [Distribuera StorSimple Virtual Array - Etablera en virtuell enhet i VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Inloggningssida](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Du kommer att tas till **hemsidan.** På den här sidan beskrivs de olika inställningar som krävs för att konfigurera och registrera den virtuella enheten med Tjänsten StorSimple Device Manager. Observera att **nätverksinställningarna,** **webbproxyinställningarna**och **tidsinställningarna** är valfria. De enda nödvändiga inställningarna är **Enhetsinställningar** och **Molninställningar**.
   
    ![Startsida](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. På sidan **Nätverksinställningar** under **Nätverksgränssnitt konfigureras**DATA 0 automatiskt åt dig. Varje nätverksgränssnitt är som standard inställt för att hämta en IP-adress automatiskt (DHCP). Därför tilldelas en IP-adress, ett undernät och en gateway automatiskt (för både IPv4 och IPv6).
   
    När du planerar att distribuera enheten som en iSCSI-server (för att etablera blocklagring) rekommenderar vi att du inaktiverar alternativet **Hämta IP-adress automatiskt** och konfigurerar statiska IP-adresser.
   
    ![Sidan Nätverksinställningar](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Om du har lagt till mer än ett nätverksgränssnitt under etableringen av enheten kan du konfigurera dem här. Du kan bara konfigurera nätverksgränssnittet som IPv4 eller som både IPv4 och IPv6. Endast IPv6-konfigurationer stöds inte.
5. DNS-servrar krävs eftersom de används när enheten försöker kommunicera med dina molnlagringstjänstleverantörer eller för att matcha enheten efter namn om den är konfigurerad som en filserver. På sidan **Nätverksinställningar** under **DNS-servrarna:**
   
   1. En primär och sekundär DNS-server konfigureras automatiskt. Om du väljer att konfigurera statiska IP-adresser kan du ange DNS-servrar. För hög tillgänglighet rekommenderar vi att du konfigurerar en primär och en sekundär DNS-server.
   2. Klicka på **Använd**. Detta kommer att gälla och validera nätverksinställningarna.
6. På sidan **Enhetsinställningar:**
   
   1. Tilldela enheten ett unikt **namn.** Det här namnet kan vara 1-15 tecken och kan innehålla bokstav, siffror och bindestreck.
   2. Klicka på **iSCSI-serverikonen** ![iSCSI-serverikonen](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) för den **typ** av enhet som du skapar. En iSCSI-server gör att du kan etablera blocklagring.
   3. Ange om du vill att den här enheten ska domänanslutas. Om enheten är en iSCSI-server är det valfritt att ansluta till domänen. Om du bestämmer dig för att inte ansluta din iSCSI-server till en domän klickar du på **Använd**, väntar tills inställningarna ska tillämpas och hoppar sedan till nästa steg.
      
       Om du vill ansluta enheten till en domän. Ange ett **domännamn**och klicka sedan på **Använd**.
      
      > [!NOTE]
      > Om du ansluter din iSCSI-server till en domän kontrollerar du att den virtuella matrisen finns i den egna organisationsenhet (OU) för Microsoft Azure Active Directory och att inga grupprincipobjekt (GPO) tillämpas på den.
      > 
      > 
   4. En dialogruta visas. Ange dina domänuppgifter i angivet format. Klicka på kryssikonen ![kryssikon](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). Domänautentiseringsuppgifterna verifieras. Ett felmeddelande visas om autentiseringsuppgifterna är felaktiga.
      
       ![autentiseringsuppgifter](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Klicka på **Använd**. Detta kommer att gälla och validera enhetsinställningarna.
7. (Du kan också konfigurera webbproxyservern. Även om webbproxykonfigurationen är valfri, var medveten om att om du använder en webbproxy så kan du bara konfigurera den här.
   
    ![konfigurera webbproxy](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    På **sidan Webbproxy:**
   
   1. Ange **webbproxy-URL:en** i det här formatet: *http:\//host-IP-adress* eller *FQDN:Port-nummer*. Observera att HTTPS-url:er inte stöds.
   2. Ange **autentisering** som **grundläggande** eller **ingen**.
   3. Om du använder autentisering måste du också ange ett **användarnamn** och **lösenord**.
   4. Klicka på **Använd**. Detta validerar och tillämpar de konfigurerade webbproxyinställningarna.
8. (Du kan också) konfigurera tidsinställningarna för enheten, till exempel tidszon och de primära och sekundära NTP-servrarna. NTP-servrar krävs eftersom enheten måste synkronisera tiden så att den kan autentisera med dina molntjänstleverantörer.
   
    ![Tidsinställningar](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    På sidan **Tidsinställningar:**
   
   1. Välj **tidszonen i** listrutan baserat på den geografiska plats där enheten distribueras. Standardtidszonen för enheten är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.
   2. Ange en **primär NTP-server** för enheten eller acceptera standardvärdet för time.windows.com. Kontrollera att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till Internet.
   3. Du kan också ange en **sekundär NTP-server** för enheten.
   4. Klicka på **Använd**. Detta validerar och tillämpar de konfigurerade tidsinställningarna.
9. Konfigurera molninställningarna för enheten. I det här steget slutför du den lokala enhetskonfigurationen och registrerar sedan enheten med tjänsten StorSimple Device Manager.
   
   1. Ange den **tjänstregistreringsnyckel** som du fick i **steg 2: Hämta tjänstregistreringsnyckeln** i [Deploy StorSimple Virtual Array - Förbered portalen](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Om detta inte är den första enheten som du registrerar dig för den här tjänsten måste du ange **krypteringsnyckeln för tjänsten**. Den här nyckeln krävs med tjänstens registreringsnyckel för att registrera ytterligare enheter med Tjänsten StorSimple Device Manager. Mer information finns [i Hämta krypteringsnyckeln](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) för tjänstdata i ditt lokala webbgränssnitt.
   3. Klicka på **Registrera**. Detta startar om enheten. Du kan behöva vänta i 2-3 minuter innan enheten har registrerats. När enheten har startats om kommer du till inloggningssidan.
      
      ![Registrera enhet](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Gå tillbaka till Azure-portalen.
11. Navigera till **tjänstens enhetsblad.** Om du har många resurser klickar du på **Alla resurser,** klickar på tjänstnamnet (sök efter det om det behövs) och sedan på **Enheter**.
12. Kontrollera att enheten har anslutits till tjänsten på bladet **Enheter** genom att slå upp statusen. Enhetens status bör vara **Redo för installation**.
    
    ![Registrera enhet](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Steg 2: Konfigurera enheten som iSCSI-server

Utför följande steg i Azure-portalen för att slutföra den nödvändiga enhetskonfigurationen.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Så här konfigurerar du enheten som iSCSI-server

1. Gå till tjänsten StorSimple Device Manager och gå sedan till **Hantering >-enheter**. I bladet **Enheter** väljer du den enhet som du just skapade. Den här enheten visas som **klar att konfigureras**.
   
    ![Konfigurera enhet som iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Klicka på enheten så visas ett banderollmeddelande som anger att enheten är redo att konfigureras.
   
    ![Konfigurera enhet som iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Klicka på **Konfigurera** i enhetskommandofältet. Då öppnas **bladet Konfigurera.** Gör följande i **bladet Konfigurera:**
   
   * ISCSI-servernamnet fylls i automatiskt.
   * Kontrollera att molnlagringskrypteringen är **aktiverad**. Detta säkerställer att data som skickas från enheten till molnet krypteras.
   * Ange en krypteringsnyckel på 32 tecken och spela in den i en nyckelhanteringsapp för framtida referens.
   * Välj ett lagringskonto som ska användas med enheten. I den här prenumerationen kan du välja ett befintligt lagringskonto eller klicka på **Lägg** till för att välja ett konto från en annan prenumeration.
     
     ![Konfigurera enhet som iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Klicka på **Konfigurera** om du vill slutföra konfigurationen av iSCSI-servern.
   
    ![Konfigurera enhet som iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Du får ett meddelande om att iSCSI-servern har skapats. När iSCSI-servern har skapats uppdateras bladet **Enheter** och motsvarande enhetsstatus är **Online**.
   
    ![Konfigurera enhet som iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Steg 3: Lägga till en volym

1. I bladet **Enheter** väljer du den enhet som du just konfigurerade som en iSCSI-server. Klicka på **...** (högerklicka på den här raden) och välj **Lägg till volym**på snabbmenyn . Du kan också klicka på **+ Lägg till volym** från kommandofältet. Detta öppnar **bladet Lägg till volym.**
   
    ![Lägga till en volym](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. Gör följande i bladet **Lägg till volym:**
   
   * Ange ett unikt namn för volymen i fältet **Volymnamn.** Namnet måste vara en sträng som innehåller 3 till 127 tecken.
   * I listrutan **Typ** anger du om du vill skapa en **nivåindelad** eller **Lokalt fäst** volym. För arbetsbelastningar som kräver lokala garantier, låga svarstider och högre prestanda väljer du **Lokalt fäst** **volym**. För alla andra data väljer du **Nivåinerad** **volym**.
   * Ange volymens storlek i fältet **Kapacitet.** En nivåindelad volym måste vara mellan 500 GB och 5 TB och en lokalt fäst volym måste vara mellan 50 GB och 500 GB.
     
     En lokalt fäst volym etableras tjockt och säkerställer att de primära data i volymen stannar på enheten och inte spiller till molnet.
     
     En nivåindelad volym å andra sidan är tunt etablerad. När du skapar en nivåindelad volym etableras cirka 10 % av utrymmet på den lokala nivån och 90 % av utrymmet etableras i molnet. Om du till exempel har etablerat en volym på 1 TB skulle 100 GB finnas i det lokala utrymmet och 900 GB skulle användas i molnet när datanivåerna. Detta innebär i sin tur att om du får på allt lokalt utrymme på enheten, kan du inte etablera en nivåindelad resurs (eftersom 10% inte kommer att vara tillgänglig).
     
     ![Lägga till en volym](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Klicka på **Anslutna värdar,** välj en åtkomstkontrollpost (ACR) som motsvarar den iSCSI-initierare som du vill ansluta till den här volymen och klicka sedan på **Välj**. <br><br> 
3. Om du vill lägga till en ny ansluten värd, klickar du på **Lägg till ny,** anger ett namn för värden och dess iSCSI-kvalificerat namn (IQN) och klickar sedan på **Lägg till**. Om du inte har IQN går du till [bilaga A: Hämta IQN för en Windows Server-värd](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Lägga till en volym](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. När du är klar med konfigurationen av volymen klickar du på **OK**. En volym skapas med de angivna inställningarna och du kommer att se ett meddelande. Som standard aktiveras övervakning och säkerhetskopiering för volymen.
   
     ![Lägga till en volym](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Om du vill bekräfta att volymen har skapats går du till bladet **Volymer.** Du bör se volymen i listan.
   
   ![Lägga till en volym](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Steg 4: Montera, initiera och formatera en volym

Utför följande steg för att montera, initiera och formatera Dina StorSimple-volymer på en Windows Server-värd.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Montera, initiera och formatera en volym

1. Öppna **iSCSI-initierarappen** på rätt server.
2. I fönstret **Egenskaper för iSCSI-initieraren**, på fliken **Identifiering**, klickar du på **Identifiera portal**.
   
    ![upptäck portal](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. I dialogrutan **Identifiera målportal**, anger du IP-adressen för ditt iSCSI-aktiverade nätverksgränssnitt och klickar på **OK**.
   
    ![IP-adress](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. I fönstret **Egenskaper för iSCSI-initieraren**, på fliken **Mål**, letar du upp **Upptäckta mål**. (Varje volym kommer att vara ett upptäckt mål.) Enhetsstatusen ska visas som **Inaktiv**.
   
    ![upptäckta mål](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Markera en målenhet och klicka sedan på **Anslut**. När enheten är ansluten, bör statusen ändras till **Ansluten**. (Mer information om hur du använder Microsoft iSCSI-initieraren finns i [Installera och konfigurera Microsoft iSCSI Initiator][1].
   
    ![välj målenhet](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. På din Windows-värd trycker du på Windows-tangenten + X och klickar sedan på **Kör**.
7. I dialogrutan **Kör**, skriver du in **diskmgmt.msc**. Klicka på **OK** och dialogrutan **Diskhantering** visas. Den högra panelen visar volymerna som finns på din värd.
8. I fönstret **Diskhantering** visas de monterade volymerna enligt följande bild. Högerklicka på den identifierade volymen (klicka på diskens namn) och klicka sedan på **Online**.
   
    ![diskhantering](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Högerklicka och välj **Initiera disk**.
   
    ![initiera disk 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. Markera de diskar som ska initieras i dialogrutan och klicka sedan på **OK**.
    
    ![initiera disk 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Guiden Ny enkel volym startar. Markera en diskstorlek och klicka sedan på **Nästa**.
    
    ![guiden ny volym 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Tilldela en enhetsbeteckning till volymen och klicka sedan på **Nästa**.
    
    ![guiden ny volym 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Ange de parametrar som ska formateras. **På Windows Server stöds endast NTFS.** Ställ in allokeringsenhetsstorleken på 64K. Ange en etikett för volymen. Det är en rekommenderad metod för att det här namnet ska vara identiskt med det volymnamn som du angav på den virtuella storsimple-matrisen. Klicka på **Nästa**.
    
    ![guiden ny volym 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Kontrollera värdena för volymen och klicka sedan på **Slutför**.
    
    ![guiden ny volym 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Volymerna visas som **Online** på sidan **Diskhantering.**
    
    ![volymer online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder det lokala webbgränssnittet för att [administrera din StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Tillägg A: Hämta IQN för en Windows Server-värd

Utför följande steg för att få det kvalificerade iSCSI-namnet (IQN) för en Windows-värd som kör Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Hämta IQN för en Windows-värd

1. Starta Microsoft iSCSI-initieraren på din Windows-värd.
2. I fönstret för **iSCSI-initieraregenskaper**, fliken **Konfiguration**, markerar och kopierar du strängen från fältet **Namn på initieraren**.
   
    ![iSCSI-initieraregenskaper](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Spara den här strängen.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



