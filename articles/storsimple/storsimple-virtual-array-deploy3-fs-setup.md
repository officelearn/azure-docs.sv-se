---
title: Konfigurera virtuella StorSimple-matris som filserver | Microsoft Docs
description: Självstudierna tredje i virtuella StorSimple-matris distribution instruerar du ställa in en virtuell enhet som filserver.
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
ms.openlocfilehash: bf507fb21b314a6811db1c1e45a4356381caada1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927826"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Distribuera StorSimple virtuell matris - uppsättningen upp som filserver via Azure-portalen
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introduktion
Den här artikeln beskriver hur du utför installationen, registrera din StorSimple-filserver, Slutför Enhetsinställningar, och skapa och ansluta till SMB-resurser. Det här är den sista artikeln i serien i kurserna om distribution krävs för att distribuera helt virtuella matrisen som en filserver eller en iSCSI-server.

Installation och konfiguration processen kan ta cirka 10 minuter för att slutföra. Informationen i den här artikeln gäller bara för distributionen av den virtuella StorSimple-matrisen. Distribution av StorSimple 8000-serien enheter, gå till: [distribuera StorSimple 8000-serien enheten kör uppdatering 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Nödvändiga inställningar
Kontrollera följande innan du konfigurerar och konfigurera din virtuella StorSimple-matris:

* Du har etablerats virtuella matris och anslutna till den enligt anvisningarna i den [etablera en virtuell StorSimple-matris på Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) eller [etablera en virtuell StorSimple-matris på VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Du har nyckeln för tjänstregistrering från StorSimple Device Manager-tjänsten som du skapade för att hantera virtuella StorSimple-matriser. Mer information finns i [steg 2: Hämta nyckel för tjänstregistrering](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) för virtuell StorSimple-matrisen.
* Om detta är den andra eller senare virtuella matris som du registrerar med en befintlig StorSimple Enhetshanteraren tjänst bör du ha krypteringsnyckeln för tjänstdata. Den här nyckeln har genererats när den första enheten har registrerats med den här tjänsten. Om du har förlorat den här nyckeln kan se [hämta krypteringsnyckel för tjänstdata](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) för din virtuella StorSimple-matrisen.

## <a name="step-by-step-setup"></a>Steg för steg-installationen
Använd följande steg för steg-instruktioner för att installera och konfigurera din virtuella StorSimple-matris.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Steg 1: Slutför installationen av UI lokala webbprogram och registrera din enhet
#### <a name="to-complete-the-setup-and-register-the-device"></a>Slutför installationen och registrera enheten
1. Öppna ett webbläsarfönster och Anslut till lokala webbgränssnittet. Ange:
   
   `https://<ip-address of network interface>`
   
   Använd anslutnings-URL som anges i föregående steg. Du ser ett felmeddelande om att det finns ett problem med webbplatsens säkerhetscertifikat. Klicka på **Fortsätt till den här webbsidan**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Logga in på webbgränssnittet för ditt virtuella matrisen som **StorSimpleAdmin**. Ange administratörslösenordet för enheten som du har ändrat i steg3: starta den virtuella matrisen i [etablera en virtuell StorSimple-matris på Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) eller i [etablera en virtuell StorSimple-matris på VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Du kommer till den **Start** sidan. Den här sidan beskrivs olika inställningar som krävs för att konfigurera och registrera den virtuella matrisen med Enhetshanteraren för StorSimple-tjänsten. Den **nätverksinställningar**, **Web proxy-inställningar**, och **tidsinställningar** är valfria. Endast nödvändiga inställningar är **Enhetsinställningar** och **Molninställningar**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. I den **nätverksinställningar** sidan **nätverksgränssnitt**, DATA 0 konfigureras automatiskt för dig. Varje nätverksgränssnitt är som standard för att hämta IP-adress automatiskt (DHCP). Därför måste tilldelas en IP-adress, nätmask och gateway automatiskt (för både IPv4 och IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Om du har lagt till fler än ett nätverksgränssnitt under etableringen av enheten kan konfigurera du dem här. Observera att du kan konfigurera din nätverksgränssnittet som IPv4 endast eller som både IPv4 och IPv6. Endast IPv6-konfigurationer stöds inte.
5. DNS-servrar krävs eftersom de används när enheten försöker kommunicera med dina molntjänstleverantörer för lagring eller för att lösa enheten efter namn när konfigurerad som en filserver. I den **nätverksinställningar** sidan den **DNS-servrar**:
   
   1. En primär och sekundär DNS-server konfigureras automatiskt. Om du vill konfigurera statiska IP-adresser kan ange du DNS-servrar. För hög tillgänglighet rekommenderar vi att du konfigurerar en primär och en sekundär DNS-server.
   2. Klicka på **tillämpa** att tillämpa och verifiera nätverksinställningarna.
6. I den **Enhetsinställningar** sidan:
   
   1. Tilldela en unik **namn** till din enhet. Det här namnet kan vara 1 och 15 tecken och får innehålla bokstäver, siffror och bindestreck.
   2. Klicka på den **filserver** ikonen ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) för den **typen** av enhet som du skapar. En filserver kan du skapa delade mappar.
   3. När enheten är en filserver, behöver du ansluta enheten till en domän. Ange en **domännamn**.
   4. Klicka på **Använd**.
7. En dialogruta visas. Ange autentiseringsuppgifter för domänen i angivet format. Klicka på kryssikonen. Autentiseringsuppgifter för domänen har verifierats. Ett felmeddelande visas om autentiseringsuppgifterna är felaktiga.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Klicka på **Använd**. Detta gäller och verifiera enhetsinställningarna för.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Kontrollera att din virtuella matris är i sin egen organisationsenhet (OU) för Active Directory och inga grupprincipobjekt (GPO) tillämpas eller ärvt. En Grupprincip kan installera program, till exempel antivirusprogram på den virtuella StorSimple-matrisen. Installera ytterligare programvara stöds inte och kan leda till att data skadas. 
   > 
   > 
9. (Valfritt) Konfigurera din webbproxyserver. Även om webbproxykonfigurationen är valfri, Tänk på att om du använder en webbproxy, du kan bara konfigurera den här.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   I den **Web proxy** sidan:
   
   1. Ange den **Web proxy URL** i det här formatet: *http://&lt;värd-IP-adress eller FDQN&gt;: portnummer*. Observera att HTTPS-URL: er inte stöds.
   2. Ange **autentisering** som **grundläggande** eller **ingen**.
   3. Om autentisering med måste du också ange en **användarnamn** och **lösenord**.
   4. Klicka på **Använd**. Detta validera och tillämpa de konfigurerade webbproxyinställningarna.
10. (Valfritt) Konfigurera inställningarna för tid för enheten, till exempel tidszon och de primära och sekundära NTP-servrarna. NTP-servrar krävs eftersom din enhet måste synkronisera tiden så att den kan autentisera med molntjänstleverantören.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    I den **tidsinställningar** sidan:
    
    1. I listrutan väljer du den **tidszon** baserat på den geografiska plats där enheten som ska distribueras. Standardtidszon för din enhet är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.
    2. Ange en **primär NTP-server** för din enhet eller acceptera standardvärdet för time.windows.com. Kontrollera att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till Internet.
    3. Om du vill ange en **sekundära NTP-server** för din enhet.
    4. Klicka på **Använd**. Detta validera och tillämpa inställningarna konfigurerade tiden.
11. Konfigurera molninställningar för din enhet. I det här steget ska du slutföra konfigurationen lokala enhet och registrera enheten med Enhetshanteraren för StorSimple-tjänsten.
    
    1. Ange den **nyckel för tjänstregistrering** som du har fått [steg 2: Hämta nyckel för tjänstregistrering](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) för virtuell StorSimple-matrisen.
    2. Om det här är din första enhet som registreras med den här tjänsten visas med den **krypteringsnyckel för tjänstdata**. Kopiera den här nyckeln och spara den på säker plats. Den här nyckeln är obligatorisk med nyckeln för tjänstregistrering att registrera ytterligare enheter med StorSimple enheten Manager-tjänsten. 
       
       Om detta inte är den första enheten som du registrerar med den här tjänsten måste ange krypteringsnyckeln för tjänstdata. Mer information finns i för att få den [krypteringsnyckel för tjänstdata](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) på lokalt webbgränssnitt.
    3. Klicka på **registrera**. Enheten startas om. Du kan behöva vänta 2-3 minuter innan enheten har registrerats. När enheten har startats om kommer du tas till inloggningssidan.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Gå tillbaka till Azure-portalen. Gå till **alla resurser**, Sök efter StorSimple Device Manager-tjänsten.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. I den filtrerade listan Välj StorSimple Device Manager-tjänsten och gå sedan till **Management > enheter**. I den **enheter** bladet, kontrollera att enheten har lyckats ansluta till tjänsten och har status **redo att konfigurera**.
    
    ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Steg 2: Konfigurera enheten som filserver
Utför följande steg i den [Azure-portalen](https://portal.azure.com/) att slutföra installationen för enheten.

#### <a name="to-configure-the-device-as-file-server"></a>Att konfigurera enheten som filserver
1. Gå till Enhetshanteraren för StorSimple-tjänsten och gå sedan till **Management > enheter**. I den **enheter** bladet Välj den enhet som du just skapade. Den här enheten skulle visas som **redo att konfigurera**.
   
   ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Klickar du på enheten och du ser en banderoll som visar att enheten är redo att installationsprogrammet.
   
    ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Klicka på **konfigurera** i kommandofältet. Detta öppnar den **konfigurera** bladet. I den **konfigurera** bladet gör du följande:
   
    1. Namnet på filservern fylls i automatiskt.
    
    2. Kontrollera att molnet lagringskryptering anges till **aktiverad**. Detta kommer att kryptera alla data som skickas till molnet. 
    
    3. En 256-bitars AES-nyckel används med användardefinierade nyckel för kryptering. Ange en nyckel med 32 tecken och sedan registrera nyckeln för att bekräfta det. Registrera nyckeln i en app för hantering av nycklar för framtida bruk.
    
    4. Klicka på **konfigurera nödvändiga inställningar** att ange autentiseringsuppgifterna för lagringskonto som ska användas med din enhet. Klicka på **Lägg till ny** om det finns ingen lagringskontouppgifter som har konfigurerats. **Se till att lagringskontot som du använder stöder blockblobbar. Sidblobbar stöds inte.** Mer information om [blockerar blobbar och sidblobbar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
    ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. I den **lägga till en lagringskontouppgifter** bladet gör du följande: 

    1. Välj aktuell prenumeration om lagringskontot tillhör samma prenumeration som tjänsten. Ange andra är lagringen konto ligger utanför prenumerationen på tjänsten. 
    
    2. Välj ett befintligt lagringskonto i listrutan. 
    
    3. Platsen fylls automatiskt i baserat på det angivna lagringskontot. 
    
    4. Aktivera SSL för att säkerställa en säkert nätverk kommunikationskanalen mellan enheten och i molnet.
    
    5. Klicka på **Lägg till** att lägga till dessa autentiseringsuppgifter för storage-konto. 
   
        ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. När inloggningsuppgifterna till storage-kontot har skapats i **konfigurera** bladet kommer att uppdateras för att visa de angivna lagringskontouppgifter. Klicka på **Konfigurera**.
   
   ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Du ser en som en fil servern håller på att skapas. När servern har skapats, du får ett meddelande.
   
   ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Enhetens status ändras samtidigt till **Online**.
   
   ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Du kan fortsätta att lägga till en resurs.

## <a name="step-3-add-a-share"></a>Steg 3: Lägg till en resurs
Utför följande steg i den [Azure-portalen](https://portal.azure.com/) att skapa en resurs.

#### <a name="to-create-a-share"></a>Skapa en resurs
1. Välj filen server-enheten som du konfigurerade i föregående steg och klicka på **...**  (eller högerklicka). Välj i snabbmenyn **Lägg till resursen**. Du kan också klicka **+ Lägg till resurs** i kommandofältet enhet.
   
   ![Lägg till en resurs](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Ange följande inställningar för resursen:

    1. Ett unikt namn för din resurs. Namnet måste vara en sträng som innehåller 3 till 127 tecken.
    
    2. En valfri **beskrivning** för resursen. Beskrivningen hjälper dig att identifiera resurs-ägare.
    
    3. En **typen** för resursen. Typen kan vara **skiktindelade** eller **lokalt Fäst**, med nivåer som standard. Arbetsbelastningar som kräver lokala garantier, låg latens och hög prestanda, Välj en **lokalt Fäst** delar. Alla andra data, Välj en **skiktindelade** delar.
    En lokalt Fäst resurs etableras tjockt, vilket och säkerställer att primärdata på resursen är lokalt till enheten och inte läcker över till molnet. En skiktad resurs är å andra sidan tunt etablerad. När du skapar en nivåindelad resurs 10% av området är etablerad på den lokala nivån och 90% av utrymmet som har etablerats i molnet. Till exempel om du har etablerat en volym 1 TB 100 GB skulle finns i lokalt utrymme och 900 GB ska användas i molnet när datanivåer. I sin tur innebär detta att du inte kan etablera en skiktad resurs om du kör utanför det lokala utrymmet på enheten.
   
    4. I den **Ange standard fullständig behörighet** fältet, tilldela behörigheter till användaren eller gruppen som har åtkomst till resursen. Ange namnet på användaren eller användargruppen i  *john@contoso.com*  format. Vi rekommenderar att du använder en användargrupp (i stället för en enskild användare) så att administratören behörighet att komma åt resurserna. När du har tilldelat behörigheter här kan använda du sedan Utforskaren för att ändra dessa behörigheter.
   
    5. Klicka på **Lägg till** att skapa resursen. 
    
        ![Lägg till en resurs](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
        Du meddelas att skapa resursen pågår.
   
        ![Lägg till en resurs](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
    När resursen har skapats med de angivna inställningarna i **resurser** bladet uppdateras för att återspegla den nya resursen. Som standard aktiveras övervakning och säkerhetskopiering för resursen.
   
    ![Lägg till en resurs](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Steg 4: Anslut till resursen
Nu behöver du ansluta till en eller flera resurser som du skapade i föregående steg. Utför de här stegen på din Windows Server-värd som är ansluten till din virtuella StorSimple-matris.

#### <a name="to-connect-to-the-share"></a>Att ansluta till resursen
1. Tryck på ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R. I fönstret kör anger den *&#92; &#92;&lt; filservernamnet&gt;*  som sökväg för ersätter *filservernamnet* med enhetsnamn som du tilldelat till filservern. Klicka på **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Detta öppnar Utforskaren. Du bör nu kunna se resurserna som du skapade som mappar. Välj och dubbelklicka på en resurs (mapp) för att visa innehållet.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Du kan nu lägga till filer till dessa resurser och gör en säkerhetskopia.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du använder lokalt webbgränssnitt till [administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

