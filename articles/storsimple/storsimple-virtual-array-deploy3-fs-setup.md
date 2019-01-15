---
title: Konfigurera StorSimple Virtual Array som filserver | Microsoft Docs
description: Den här tredje självstudien i StorSimple Virtual Array distribution instruerar du ställer in en virtuell enhet som filserver.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f699e40a4a31b6d57b12a43ae307806d3f010015
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267189"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Distribuera StorSimple Virtual Array - Set konfigurera som filserver via Azure-portalen
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introduktion
Den här artikeln beskriver hur du utför installationen, registrera din StorSimple-filserver, Slutför installationen av enheten och skapa och ansluta till SMB-resurser. Det här är den senaste artikeln i serien av självstudiekurserna om distribution krävs för att distribuera din virtuella matris helt som en filserver eller en iSCSI-server.

Installationen och konfigurationen kan ta upp till 10 minuter för att slutföra. Informationen i den här artikeln gäller bara för distribution av StorSimple Virtual Array. Distribution av enheter i StorSimple 8000-serien, går du till: [Distribuera din StorSimple 8000 series-enhet som kör uppdatering 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Nödvändiga inställningar
Innan du konfigurerar och konfigurera din StorSimple Virtual Array, kontrollerar du att:

* Du har etablerat en virtuell matris och är anslutna till den som beskrivs i den [etablera en StorSimple virtuell matris i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) eller [etablera en StorSimple Virtual Array i VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Du har tjänstregistreringsnyckeln från StorSimple Device Manager-tjänsten som du skapade för att hantera StorSimple Virtual Array. Mer information finns i [steg 2: Hämta tjänstregistreringsnyckeln](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) för StorSimple Virtual Array.
* Om det här är den andra eller senare virtuella matrisen som du registrerar med en befintlig StorSimple Device Manager-tjänst, bör du ha krypteringsnyckeln för tjänstdata. Den här nyckeln skapades när den första enheten har registrerats med den här tjänsten. Om du har tappat bort den här nyckeln finns i [hämta krypteringsnyckeln för tjänstdata](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) för StorSimple Virtual Array.

## <a name="step-by-step-setup"></a>Stegvis installation
Använd följande steg för steg-instruktioner för att installera och konfigurera din StorSimple Virtual Array.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Steg 1: Slutför lokala web UI-installationen och registrera din enhet
#### <a name="to-complete-the-setup-and-register-the-device"></a>Att slutföra installationen och registrera enheten
1. Öppna ett webbläsarfönster och Anslut till det lokala webbgränssnittet. Ange:
   
   `https://<ip-address of network interface>`
   
   Använd anslutnings-URL som anges i föregående steg. Du ser ett felmeddelande om att det finns ett problem med webbplatsens säkerhetscertifikat. Klicka på **Fortsätt till den här webbsidan**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Logga in på webbgränssnittet på den virtuella matrisen som **StorSimpleAdmin**. Ange enhetens administratörslösenord som du har ändrat i steg3: Starta den virtuella matrisen i [etablera en StorSimple virtuell matris i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) eller i [etablera en StorSimple Virtual Array i VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Du kommer till den **Start** sidan. Den här sidan beskriver de olika inställningar som krävs för att konfigurera och registrera den virtuella matrisen med StorSimple Device Manager-tjänsten. Den **nätverksinställningar**, **Web proxyinställningar**, och **tidsinställningar** är valfria. De enda obligatoriska inställningarna är **Enhetsinställningar** och **Molninställningar**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. I den **nätverksinställningar** sidan **nätverksgränssnitt**, DATA 0 konfigureras automatiskt åt dig. Varje nätverksgränssnitt är som standard för att hämta IP-adress automatiskt (DHCP). Därför kan tilldelas en IP-adress, undernät och gateway automatiskt (för både IPv4 och IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Om du har lagt till mer än ett nätverksgränssnitt under etableringen av enheten, kan du konfigurera dem här. Observera att du kan konfigurera nätverksgränssnittets som IPv4 endast eller som både IPv4 och IPv6. Endast IPv6-konfigurationer stöds inte.
5. DNS-servrar krävs eftersom de används när enheten försöker kommunicera med dina molntjänstleverantörer för lagring eller för att lösa enheten efter namn när konfigurerad som en filserver. I den **nätverksinställningar** sidan den **DNS-servrar**:
   
   1. En primär och sekundär DNS-server konfigureras automatiskt. Om du vill konfigurera statiska IP-adresser kan du ange DNS-servrar. För hög tillgänglighet rekommenderar vi att du konfigurerar en primär och en sekundär DNS-server.
   2. Klicka på **tillämpa** att tillämpa och verifiera nätverksinställningarna.
6. I den **Enhetsinställningar** sidan:
   
   1. Tilldela ett unikt **namn** till din enhet. Det här namnet kan vara 1 och 15 tecken och får innehålla bokstäver, siffror och bindestreck.
   2. Klicka på den **filserver** ikonen ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) för den **typ** för enhet som du skapar. En filserver kan du skapa delade mappar.
   3. När enheten är en filserver, behöver du ansluta enheten till en domän. Ange en **domännamn**.
   4. Klicka på **Verkställ**.
7. En dialogruta visas. Ange dina autentiseringsuppgifter för domänen i formatet som anges. Klicka på kryssikonen. Autentiseringsuppgifter för domänen har verifierats. Du ser ett felmeddelande om autentiseringsuppgifterna är felaktiga.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Klicka på **Verkställ**. Detta gäller och validera enhetsinställningarna.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Kontrollera att din virtuella matris är i sin egen organisationsenhet (OU) för Active Directory och inga grupprincipobjekt (GPO) tillämpas eller ärvs. En Grupprincip kan installera program, till exempel ett antivirusprogram på StorSimple Virtual Array. Installera ytterligare programvara stöds inte och kan leda till skadade data. 
   > 
   > 
9. (Valfritt) Konfigurera din webbproxyserver. Men webbproxykonfigurationen är valfritt, Tänk på att om du använder en webbproxy, du kan bara konfigurera den här.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   I den **webbproxy** sidan:
   
   1. Ange den **webbadress proxy** i följande format: *http://&lt;värd-IP-adress eller FQDN&gt;: portnummer*. Observera att HTTPS-URL: er inte stöds.
   2. Ange **autentisering** som **grundläggande** eller **ingen**.
   3. Om du använder autentisering, du måste också tillhandahålla en **användarnamn** och **lösenord**.
   4. Klicka på **Verkställ**. På så sätt validera och tillämpa konfigurerade Webbproxyinställningar.
10. (Valfritt) Konfigurera inställningarna för tid för din enhet, till exempel tidszon och de primära och sekundära NTP-servrarna. NTP-servrar krävs eftersom din enhet måste synkronisera tiden så att den kan autentisera med molntjänstleverantören.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    I den **tidsinställningar** sidan:
    
    1. I listrutan, Välj den **tidszon** baserat på den geografiska plats där enheten ska distribueras. Standardtidszon för din enhet är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.
    2. Ange en **primär NTP-server** för din enhet eller acceptera standardvärdet för time.windows.com. Kontrollera att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till Internet.
    3. Om du vill ange en **sekundär NTP-server** för din enhet.
    4. Klicka på **Verkställ**. På så sätt validera och tillämpa inställningarna för konfigurerade tid.
11. Konfigurera molninställningar för din enhet. I det här steget ska du slutför konfigurationen av lokala enheten och sedan registrera enheten med StorSimple Device Manager-tjänsten.
    
    1. Ange den **tjänstregistreringsnyckeln** som du fick i [steg 2: Hämta tjänstregistreringsnyckeln](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) för StorSimple Virtual Array.
    2. Om det här är din första enhet som registreras med den här tjänsten visas med den **krypteringsnyckeln för tjänstdata**. Kopiera den här nyckeln och spara den på säker plats. Den här nyckeln är krävas med Registreringsnyckeln för tjänsten för att registrera ytterligare enheter med StorSimple Device Manager-tjänsten. 
       
       Om detta inte är den första enheten som du registrerar med den här tjänsten kommer du behöva ange krypteringsnyckeln för tjänstdata. Mer information finns i för att få den [krypteringsnyckeln för tjänstdata](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) på din lokala webbgränssnitt.
    3. Klicka på **registrera**. Enheten startas om. Du kan behöva vänta 2 – 3 minuter innan enheten har registrerats. När enheten har startats om, tas du till inloggningssidan.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Gå tillbaka till Azure-portalen. Gå till **alla resurser**, Sök efter din StorSimple Device Manager-tjänsten.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. I den filtrerade listan, Välj din StorSimple Device Manager-tjänst och gå sedan till **Management > enheter**. I den **enheter** bladet, kontrollera att enheten har lyckats ansluta till tjänsten och har statusen **redo att konfigurera**.
    
    ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Steg 2: Konfigurera enheten som filserver
Utför följande steg i den [Azure-portalen](https://portal.azure.com/) att slutföra den obligatoriska Enhetsinstallationen.

#### <a name="to-configure-the-device-as-file-server"></a>Att konfigurera enheten som filserver
1. Gå till StorSimple Device Manager-tjänsten och gå sedan till **Management > enheter**. I den **enheter** bladet väljer du den enhet som du precis skapade. Den här enheten skulle visas som **redo att konfigurera**.
   
   ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Klicka på enheten och du ser en banderoll som anger att enheten är redo att installationsprogrammet.
   
    ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Klicka på **konfigurera** i kommandofältet. Gör det öppnas den **konfigurera** bladet. I den **konfigurera** bladet gör du följande:
   
    1. Namnet på filservern fylls i automatiskt.
    
    2. Kontrollera att molnlagringskryptering anges till **aktiverad**. Detta kommer att kryptera alla data som skickas till molnet. 
    
    3. En 256-bitars AES-nyckel används med den anpassade nyckeln för kryptering. Ange en nyckel med 32 tecken och sedan registrera nyckeln för att bekräfta det. Anteckna nyckeln i en nyckelhanteringsapp för framtida bruk.
    
    4. Klicka på **konfigurera nödvändiga inställningar** att ange autentiseringsuppgifter för lagringskonto som ska användas med enheten. Klicka på **Lägg till ny** om det finns inga autentiseringsuppgifter för lagringskonto konfigurerats. **Se till att storage-konto som du använder stöder blockblobar. Sidblobar stöds inte.** Mer information om [blockerar blobar och sidblobar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
    ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. I den **lägga till en lagringskontouppgifter** bladet gör du följande: 

    1. Välj aktuell prenumeration om lagringskontot tillhör samma prenumeration som tjänsten. Ange andra är lagringen konto ligger utanför tjänstprenumeration. 
    
    2. Välj ett befintligt lagringskonto i listrutan. 
    
    3. Platsen automatiskt fylls i baserat på det angivna lagringskontot. 
    
    4. Aktivera SSL att säkerställa en säker nätverkskommunikationskanal mellan enheten och molnet.
    
    5. Klicka på **Lägg till** att lägga till den här autentiseringsuppgiften för lagringskontot. 
   
        ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. När autentiseringsuppgifterna för lagringskontot har skapats, den **konfigurera** bladet kommer att uppdateras för att visa de angivna lagringskontouppgifter. Klicka på **Konfigurera**.
   
   ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Du ser en som en fil servern skapas. När servern har skapats, kommer du att meddelas.
   
   ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Enhetens status ändras också till **Online**.
   
   ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Du kan fortsätta att lägga till en resurs.

## <a name="step-3-add-a-share"></a>Steg 3: Lägga till en resurs
Skapa en resurs genom att utföra stegen nedan på [Azure-portalen](https://portal.azure.com/).

#### <a name="to-create-a-share"></a>Skapa en resurs
1. Välj den fil server-enhet som du konfigurerade i föregående steg och klicka på **...**  (eller högerklicka). I snabbmenyn väljer **Lägg till resurs**. Du kan också klicka på **+ Lägg till resurs** i kommandofältet för enheten.
   
   ![Lägga till en resurs](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Ange följande inställningar för resursen:

    1. Ett unikt namn för din resurs. Namnet måste vara en sträng som innehåller 3 till 127 tecken.
    
    2. En valfri **beskrivning** för resursen. Beskrivningen kan du identifiera resurs-ägare.
    
    3. En **typ** för resursen. Typen kan vara **Nivåindelad** eller **lokalt fixerade**, nivåer med standardvärdet. För arbetsbelastningar som kräver lokala garantier, Låg fördröjning och högre prestanda, väljer en **lokalt fixerade** dela. All övrig data, Välj en **Nivåindelad** dela.
    En lokalt Fäst resurs etableras tjockt och garanterar att primärdata på resursen är lokalt på enheten och inte läcker över till molnet. En nivåindelad resurs är å andra sidan tunt etablerad. När du skapar en nivåindelad resurs är 10% av utrymmet är etablerad på den lokala nivån och 90% av utrymmet som har etablerats i molnet. Till exempel om du har etablerat en volym på 1 TB, 100 GB skulle finnas i det lokala utrymmet och 900 GB skulle användas i molnet när de data-nivåerna. Detta innebär i sin tur att du inte kan etablera en nivåindelad resurs om du kör utanför det lokala utrymmet på enheten.
   
    4. I den **Ange standard fullständig behörighet** fältet, tilldela behörigheter till användaren eller gruppen som har åtkomst till den här resursen. Ange namnet på användaren eller användargruppen i *john@contoso.com* format. Vi rekommenderar att du använder en användargrupp (i stället för en enskild användare) för att tillåta admin-behörighet att komma åt dessa resurser. När du har tilldelat behörigheterna här kan du sedan använda Utforskaren till att ändra dessa behörigheter.
   
    5. Klicka på **Lägg till** resursen ska skapas. 
    
        ![Lägga till en resurs](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
        Du får ett meddelande om att resursen skapas.
   
        ![Lägga till en resurs](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
    När resursen har skapats med de angivna inställningarna i **resurser** bladet uppdateras för att återspegla den nya resursen. Som standard aktiveras övervakning och säkerhetskopiering för resursen.
   
    ![Lägga till en resurs](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Steg 4: Ansluta till resursen
Du kommer nu att behöva ansluta till en eller flera resurser som du skapade i föregående steg. Utföra dessa steg på Windows Server-värd är ansluten till din StorSimple Virtual Array.

#### <a name="to-connect-to-the-share"></a>Att ansluta till resursen
1. Tryck på ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R. I fönstret kör anger den *&#92; &#92; &lt;filservernamn&gt;* som sökväg för ersätta *filservernamn* med namnet på enheten som du tilldelat till din fil Server. Klicka på **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Detta öppnar Utforskaren. Du bör nu kunna se de resurser du har skapat som mappar. Välj och dubbelklicka på en resurs (mapp) för att visa innehållet.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Du kan nu lägga till filer till dessa resurser och gör en säkerhetskopia.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du använder lokalt webbgränssnitt till [administrera StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

