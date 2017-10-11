---
title: "Installationsprogrammet för Microsoft Azure StorSimple virtuell matris iSCSI-server | Microsoft Docs"
description: "Beskriver hur du utför installationen, registrera StorSimple iSCSI-servern och slutför Enhetsinställningar."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 076df176d7cd40c009aea27004fe0f4415999c80
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Distribuera StorSimple virtuell Array – Set in som en iSCSI-server via Azure-portalen

![processflöde för iSCSI-installationen](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Översikt

Den här självstudiekursen för distribution gäller för Microsoft Azure StorSimple virtuell matrisen. Den här självstudiekursen beskrivs hur du utför installationen, registrera StorSimple iSCSI-servern, Slutför Enhetsinställningar, och sedan skapa, montera, initiera och formatera volymer på din virtuella StorSimple-matrisen som konfigurerats som en iSCSI-server. 

De förfaranden som beskrivs ta här cirka 30 minuter till 1 timme att slutföra. Den information som publiceras i den här artikeln gäller bara för virtuella StorSimple-matriser.

## <a name="setup-prerequisites"></a>Nödvändiga inställningar

Kontrollera följande innan du konfigurerar och konfigurera din virtuella StorSimple-matris:

* Du har etablerats virtuella matris och anslutna till den, enligt beskrivningen i [distribuera StorSimple virtuell matris - etablera en virtuell Hyper-V-matris](storsimple-ova-deploy2-provision-hyperv.md) eller [distribuera StorSimple virtuell matris - etablera en virtuell VMware-matris ](storsimple-virtual-array-deploy2-provision-vmware.md).
* Du har nyckeln för tjänstregistrering från StorSimple Device Manager-tjänsten som du skapade för att hantera din virtuella StorSimple-matriser. Mer information finns i **steg 2: Hämta nyckel för tjänstregistrering** i [distribuera StorSimple virtuell matris - förbereda portalen](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Om detta är den andra eller senare virtuella matris som du registrerar med en befintlig StorSimple Enhetshanteraren tjänst bör du ha krypteringsnyckeln för tjänstdata. Den här nyckeln har genererats när den första enheten har registrerats med den här tjänsten. Om du har förlorat den här nyckeln kan se **hämta krypteringsnyckel för tjänstdata** i [använda Webbgränssnittet för att administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Steg för steg-installationen

Använd följande steg för steg-instruktioner för att installera och konfigurera din virtuella StorSimple-matris:

* [Steg 1: Slutför installationen av UI lokala webbprogram och registrera din enhet](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* [Steg 2: Slutför installationen av nödvändiga enhet](#step-2-complete-the-required-device-setup)
* [Steg 3: Lägg till en volym](#step-3-add-a-volume)
* [Steg 4: Montera, initiera och formatera en volym](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Steg 1: Slutför installationen av UI lokala webbprogram och registrera din enhet

#### <a name="to-complete-the-setup-and-register-the-device"></a>Slutför installationen och registrera enheten

1. Öppna ett webbläsarfönster. Att ansluta till webbservern UI-Typ:
   
    `https://<ip-address of network interface>`
   
    Använd anslutnings-URL som anges i föregående steg. Du ser ett fel som får ett meddelande om att det finns ett problem med webbplatsens säkerhetscertifikat. Klicka på **Fortsätt till den här webbsidan**.
   
    ![säkerhetscertifikat fel](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Logga in på webbgränssnittet för din virtuella enhet som **StorSimpleAdmin**. Ange administratörslösenordet för enheten som du har ändrat i steg3: starta den virtuella enheten i [distribuera StorSimple virtuell matris - etablera en virtuell enhet i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) eller [distribuera StorSimple virtuell matris - etablera en virtuell enhet i VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Inloggningssidan](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Du kommer till den **Start** sidan. Den här sidan beskrivs olika inställningar som krävs för att konfigurera och registrera den virtuella enheten StorSimple Device Manager-tjänsten. Observera att den **nätverksinställningar**, **Web proxy-inställningar**, och **tidsinställningar** är valfria. Endast nödvändiga inställningar är **Enhetsinställningar** och **Molninställningar**.
   
    ![Startsida](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. På den **nätverksinställningar** sidan **nätverksgränssnitt**, DATA 0 konfigureras automatiskt för dig. Varje nätverksgränssnitt är som standard för att hämta en IP-adress automatiskt (DHCP). Därför kan kommer en IP-adress, nätmask och gateway att tilldelas automatiskt (för både IPv4 och IPv6).
   
    När du planerar att distribuera din enhet som en iSCSI-server (för att etablera blocklagring) rekommenderar vi att du inaktiverar den **hämta IP-adress automatiskt** och konfigurera statiska IP-adresser.
   
    ![Inställningssidan för nätverk](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Om du har lagt till fler än ett nätverksgränssnitt under etableringen av enheten kan konfigurera du dem här. Observera att du kan konfigurera din nätverksgränssnittet som IPv4 endast eller som både IPv4 och IPv6. Endast IPv6-konfigurationer stöds inte.
5. DNS-servrar krävs eftersom de används när enheten försöker kommunicera med dina molntjänstleverantörer för lagring eller för att lösa enheten efter namn om den är konfigurerad som en filserver. På den **nätverksinställningar** sidan den **DNS-servrar**:
   
   1. En primär och sekundär DNS-server konfigureras automatiskt. Om du vill konfigurera statiska IP-adresser kan ange du DNS-servrar. För hög tillgänglighet rekommenderar vi att du konfigurerar en primär och en sekundär DNS-server.
   2. Klicka på **Använd**. Detta gäller och verifiera nätverksinställningarna.
6. På den **Enhetsinställningar** sidan:
   
   1. Tilldela en unik **namn** till din enhet. Det här namnet kan vara 1 och 15 tecken och får innehålla bokstäver, siffror och bindestreck.
   2. Klicka på den **iSCSI-server** ikonen ![iSCSI Serverikon](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) för den **typ** av enhet som du skapar. En iSCSI-server gör att du kan tillhandahålla blocklagring.
   3. Ange om du vill att den här enheten måste vara ansluten till domänen. Om enheten är en iSCSI-server kan är ansluta till domänen valfritt. Om du väljer att inte ansluta till iSCSI-servern till en domän, klickar du på **tillämpa**, vänta tills inställningarna som ska användas och gå sedan vidare till nästa steg.
      
       Om du vill ansluta enheten till en domän. Ange en **domännamn**, och klicka sedan på **tillämpa**.
      
      > [!NOTE]
      > Om du ansluter till iSCSI-servern till en domän måste kontrollera att virtuella matrisen i sin egen organisationsenhet (OU) för Microsoft Azure Active Directory och inga grupprincipobjekt (GPO) som tillämpas på den.
      > 
      > 
   4. En dialogruta visas. Ange autentiseringsuppgifter för domänen i angivet format. Klicka på kryssikonen ![kryssikon](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). Autentiseringsuppgifter för domänen kommer att verifieras. Ett felmeddelande visas om autentiseringsuppgifterna är felaktiga.
      
       ![autentiseringsuppgifter](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Klicka på **Använd**. Detta gäller och verifiera enhetsinställningarna för.
7. (Valfritt) Konfigurera din webbproxyserver. Även om webbproxykonfigurationen är valfri, Tänk på att om du använder en webbproxy, du kan bara konfigurera den här.
   
    ![Konfigurera webbproxy](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    På den **Web proxy** sidan:
   
   1. Ange den **Web proxy URL** i det här formatet: *http://host-IP adress* eller *FDQN:Port nummer*. Observera att HTTPS-URL: er inte stöds.
   2. Ange **autentisering** som **grundläggande** eller **ingen**.
   3. Om du använder autentisering måste du också att tillhandahålla en **användarnamn** och **lösenord**.
   4. Klicka på **Använd**. Detta validera och tillämpa de konfigurerade webbproxyinställningarna.
8. (Valfritt) Konfigurera inställningarna för tid för enheten, till exempel tidszon och de primära och sekundära NTP-servrarna. NTP-servrar krävs eftersom din enhet måste synkronisera tiden så att den kan autentisera med molntjänstleverantören.
   
    ![Tidsinställningar](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    På den **tidsinställningar** sidan:
   
   1. I listrutan, väljer du den **tidszon** baserat på den geografiska plats där enheten som ska distribueras. Standardtidszon för din enhet är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.
   2. Ange en **primär NTP-server** för din enhet eller acceptera standardvärdet för time.windows.com. Kontrollera att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till Internet.
   3. Om du vill ange en **sekundära NTP-server** för din enhet.
   4. Klicka på **Använd**. Detta validera och tillämpa inställningarna konfigurerade tiden.
9. Konfigurera molninställningar för din enhet. I det här steget ska du slutföra konfigurationen lokala enhet och registrera enheten med Enhetshanteraren för StorSimple-tjänsten.
   
   1. Ange den **nyckel för tjänstregistrering** som du har fått **steg 2: Hämta nyckel för tjänstregistrering** i [distribuera StorSimple virtuell matris - förbereda portalen](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Om detta inte är den första enheten som du registrerar med den här tjänsten behöver du ange den **krypteringsnyckel för tjänstdata**. Den här nyckeln är obligatorisk med nyckeln för tjänstregistrering att registrera ytterligare enheter med StorSimple enheten Manager-tjänsten. Mer information finns i [hämta krypteringsnyckel för tjänstdata](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) på lokalt webbgränssnitt.
   3. Klicka på **registrera**. Enheten startas om. Du kan behöva vänta 2-3 minuter innan enheten har registrerats. När enheten har startats om kommer du tas till inloggningssidan.
      
      ![Registrera enhet](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Gå tillbaka till Azure-portalen.
11. Navigera till den **enheter** bladet för din tjänst. Om du har många resurser, klickar du på **alla resurser**på ditt tjänstnamn (Sök efter det om det behövs) och klicka sedan på **enheter**.
12. På den **enheter** bladet, kontrollera att enheten har anslutits till tjänsten genom att söka efter status. Enhetens status bör vara **Redo för installation**.
    
    ![Registrera enhet](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Steg 2: Konfigurera enheten som iSCSI-server

Utför följande steg i Azure portal för att slutföra installationen för enheten.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Att konfigurera enheten som iSCSI-server

1. Gå till Enhetshanteraren för StorSimple-tjänsten och gå sedan till **Management > enheter**. I den **enheter** bladet Välj den enhet som du just skapade. Den här enheten skulle visas som **redo att konfigurera**.
   
    ![Konfigurera enhet som iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Klickar du på enheten och du ser en banderoll som visar att enheten är redo att installationsprogrammet.
   
    ![Konfigurera enhet som iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Klicka på **konfigurera** i kommandofältet enhet. Detta öppnar den **konfigurera** bladet. I den **konfigurera** bladet gör du följande:
   
   * ISCSI-servernamnet fylls i automatiskt.
   * Kontrollera att molnet lagringskryptering anges till **aktiverad**. Detta garanterar att data som skickas från enheten till molnet är krypterad.
   * Ange en krypteringsnyckel med 32 tecken och registrera den på en app för hantering av nycklar för framtida bruk.
   * Välj ett lagringskonto som ska användas med din enhet. I den här prenumerationen kan du välja ett befintligt lagringskonto eller klicka **Lägg till** att välja ett konto från en annan prenumeration.
     
     ![Konfigurera enhet som iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Klicka på **konfigurera** att slutföra konfigurationen av iSCSI-server.
   
    ![Konfigurera enhet som iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Du meddelas att iSCSI-servern skapa pågår. När iSCSI-server har skapats i **enheter** bladet uppdateras och motsvarande enhetens status är **Online**.
   
    ![Konfigurera enhet som iSCSI-server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Steg 3: Lägg till en volym

1. I den **enheter** bladet väljer enheten som du precis har konfigurerats som en iSCSI-server. Klicka på **...**  (du kan också högerklicka på den här raden) och på snabbmenyn Välj **Lägg till volymen**. Du kan också klicka på **+ Lägg till volymen** från kommandofältet. Detta öppnar den **Lägg till volymen** bladet.
   
    ![Lägg till en volym](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. I den **Lägg till volymen** bladet gör du följande:
   
   * I den **volymnamn** , ange ett unikt namn för volymen. Namnet måste vara en sträng som innehåller 3 till 127 tecken.
   * I den **typen** nedrullningsbara listan, ange om du vill skapa en **skiktindelade** eller **lokalt Fäst** volym. Arbetsbelastningar som kräver lokala garantier, låg latens och hög prestanda, Välj **lokalt Fäst** **volym**. Alla andra data, Välj **skiktindelade** **volym**.
   * I den **kapacitet** anger storleken på volymen. En nivåindelad volym måste vara mellan 500 GB och 5 TB och en lokalt Fäst volym måste vara mellan 50 och 500 GB.
     
     En lokalt Fäst volym etableras tjockt, vilket och säkerställer att primärdata på volymen förblir på enheten och inte läcker över till molnet.
     
     En nivåindelad volym är å andra sidan tunt etablerad. När du skapar en nivåindelad volym etableras cirka 10% av utrymmet på den lokala nivån och 90% av utrymmet som har etablerats i molnet. Till exempel om du har etablerat en volym 1 TB 100 GB skulle finns i det lokala utrymmet och 900 GB ska användas i molnet när datanivåer. Detta innebär i sin tur är att om du kör utanför det lokala utrymmet på enheten, du kan inte etablera en skiktad resurs (eftersom de 10% är inte tillgänglig).
     
     ![Lägg till en volym](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Klicka på **anslutna värdar**, Välj en åtkomstkontrollpost (ACR) som motsvarar iSCSI-initierare som du vill ansluta till den här volymen och klickar sedan på **Välj**. <br><br> 
3. Om du vill lägga till en ny anslutna värddator, klickar du på **Lägg till ny**, ange ett namn för värden och dess iSCSI-kvalificerade namn (IQN) och klicka sedan på **Lägg till**. Om du inte har en IQN, går du till [bilaga A: hämta IQN för en Windows Server-värd](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Lägg till en volym](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. När du är klar med att konfigurera volymen, klickar du på **OK**. En volym skapas med de angivna inställningarna och ett meddelande visas. Som standard aktiveras övervakning och säkerhetskopiering för volymen.
   
     ![Lägg till en volym](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Kontrollera att volymen har skapats genom att gå till den **volymer** bladet. Du bör se den volym som visas.
   
   ![Lägg till en volym](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Steg 4: Montera, initiera och formatera en volym

Utför följande steg för att montera, initiera och formatera dina StorSimple-volymer på en Windows Server-värd.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Montera, initiera och formatera en volym

1. Öppna den **iSCSI-initieraren** app på en lämplig server.
2. I fönstret **Egenskaper för iSCSI-initieraren**, på fliken **Identifiering**, klickar du på **Identifiera portal**.
   
    ![identifiera portal](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. I dialogrutan **Identifiera målportal**, anger du IP-adressen för ditt iSCSI-aktiverade nätverksgränssnitt och klickar på **OK**.
   
    ![IP-adress](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. I fönstret **Egenskaper för iSCSI-initieraren**, på fliken **Mål**, letar du upp **Upptäckta mål**. (Varje volym vara ett upptäckta mål.) Enhetens status borde visas som **Inaktiv**.
   
    ![upptäckta mål](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Välj en målenhet och klicka sedan på **Anslut**. När enheten är ansluten, bör statusen ändras till **Ansluten**. (Mer information om hur du använder Microsoft iSCSI-initieraren finns [installera och konfigurera Microsoft iSCSI Initiator][1].
   
    ![Välj målenheten](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. På din Windows-värd trycker du på Windows-tangenten + X och klickar sedan på **Kör**.
7. I dialogrutan **Kör**, skriver du in **diskmgmt.msc**. Klicka på **OK** och dialogrutan **Diskhantering** visas. Den högra panelen visar volymerna som finns på din värd.
8. I fönstret **Diskhantering** visas de monterade volymerna enligt följande bild. Högerklicka på den identifierade volymen (klicka på diskens namn) och klicka sedan på **Online**.
   
    ![Diskhantering](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Högerklicka och välj **initiera Disk**.
   
    ![Initiera disk 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. I dialogrutan Välj diskarna som du vill initiera och klicka sedan på **OK**.
    
    ![initiera disken 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Guiden Ny enkel volym startas. Välj storleken för en disk och klicka sedan på **nästa**.
    
    ![guiden Skapa ny volym 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Tilldela en enhetsbeteckning till volymen och klicka sedan på **nästa**.
    
    ![guiden Skapa ny volym 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Ange parametrar för att formatera volymen. **Endast NTFS stöds på Windows Server.** Ange storlek på allokeringsenheten till 64 kB. Ange en etikett för volymen. Det är rekommenderad praxis för det här namnet ska vara identiskt med namn på volymer som du angav på din virtuella StorSimple-matrisen. Klicka på **Nästa**.
    
    ![guiden Skapa ny volym 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Kontrollera värdena för volymen och klickar sedan på **Slutför**.
    
    ![guiden Skapa ny volym 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Volymerna visas som **Online** på den **Diskhantering** sidan.
    
    ![volymer online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder lokalt webbgränssnitt till [administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Bilaga A: hämta IQN för en Windows Server-värd

Utför följande steg för att få det kvalificerade iSCSI-namnet (IQN) för en Windows-värd som kör Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Hämta IQN för en Windows-värd

1. Starta Microsoft iSCSI-initieraren på din Windows-värd.
2. I fönstret för **iSCSI-initieraregenskaper**, fliken **Konfiguration**, markerar och kopierar du strängen från fältet **Namn på initieraren**.
   
    ![iSCSI-initieraregenskaper](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Spara den här strängen.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



