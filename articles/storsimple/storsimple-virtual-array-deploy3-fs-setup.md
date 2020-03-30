---
title: Konfigurera StorSimple Virtual Array som filserver | Microsoft-dokument
description: Den tredje självstudien i StorSimple Virtual Array-distributionen instruerar dig att konfigurera en virtuell enhet som filserver.
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
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16a5e0bb3e50e3a90951572e8d2847d379c1b114
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297649"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Distribuera StorSimple Virtual Array – Konfigurera som filserver via Azure Portal
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introduktion

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

I den här artikeln beskrivs hur du utför den första installationen, registrerar storsimple-filservern, slutför enhetskonfigurationen och skapar och ansluter till SMB-resurser. Det här är den sista artikeln i serien av självstudier för distribution som krävs för att fullständigt distribuera den virtuella matrisen som en filserver eller en iSCSI-server.

Installationen och konfigurationsprocessen kan ta cirka 10 minuter att slutföra. Informationen i den här artikeln gäller endast distributionen av StorSimple Virtual Array. För distribution av StorSimple 8000-serieenheter går du till: [Distribuera din StorSimple 8000-serie enhet som kör Uppdatering 2](storsimple-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Nödvändiga nödvändiga komponenter
Innan du konfigurerar och konfigurerar den virtuella storsimple-matrisen ska du se till att:

* Du har etablerat en virtuell matris och anslutit till den enligt beskrivningen i [etablera en StorSimple Virtual Array i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) eller [etablera en StorSimple Virtual Array i VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Du har tjänstregistreringsnyckeln från Tjänsten StorSimple Device Manager som du har skapat för att hantera StorSimple-virtuella matriser. Mer information finns i [steg 2: Hämta tjänstregistreringsnyckeln](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) för StorSimple Virtual Array.
* Om det här är den andra eller efterföljande virtuella matrisen som du registrerar med en befintlig StorSimple Enhetshanteraren-tjänst bör du ha krypteringsnyckeln för tjänstdata. Den här nyckeln genererades när den första enheten registrerades med den här tjänsten. Om du har förlorat den här nyckeln läser [du Hämta krypteringsnyckeln](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) för tjänstdata för den virtuella storsimple-matrisen.

## <a name="step-by-step-setup"></a>Steg-för-steg-inställningar
Använd följande steg-för-steg-instruktioner för att konfigurera den virtuella storsimple-matrisen.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Steg 1: Slutför den lokala webbgränssnittskonfigurationen och registrera enheten
#### <a name="to-complete-the-setup-and-register-the-device"></a>Så här slutför du installationen och registrerar enheten
1. Öppna ett webbläsarfönster och anslut till det lokala webbgränssnittet. Ange:
   
   `https://<ip-address of network interface>`
   
   Använd anslutnings-URL:en som noterades i föregående steg. Ett fel visas som anger att det finns ett problem med webbplatsens säkerhetscertifikat. Klicka på **Fortsätt till den här webbsidan**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Logga in på webbgränssnittet för din virtuella matris som **StorSimpleAdmin**. Ange enhetsadministratörslösenordet som du har ändrat i steg 3: Starta den virtuella matrisen i [Etablera en StorSimple Virtual Array i Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) eller etablera [en StorSimple Virtual Array i VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Du tas till **hemsidan.** På den här sidan beskrivs de olika inställningar som krävs för att konfigurera och registrera den virtuella matrisen med tjänsten StorSimple Device Manager. **Nätverksinställningarna,** **webbproxyinställningarna**och **tidsinställningarna** är valfria. De enda nödvändiga inställningarna är **Enhetsinställningar** och **Molninställningar**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. På sidan **Nätverksinställningar** under **Nätverksgränssnitt konfigureras**DATA 0 automatiskt åt dig. Varje nätverksgränssnitt är som standard inställt för att hämta IP-adress automatiskt (DHCP). Därför tilldelas en IP-adress, ett undernät och en gateway automatiskt (för både IPv4 och IPv6).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Om du har lagt till mer än ett nätverksgränssnitt under etableringen av enheten kan du konfigurera dem här. Du kan bara konfigurera nätverksgränssnittet som IPv4 eller som både IPv4 och IPv6. Endast IPv6-konfigurationer stöds inte.
5. DNS-servrar krävs eftersom de används när enheten försöker kommunicera med dina molnlagringstjänstleverantörer eller för att matcha enheten efter namn när den konfigureras som en filserver. På sidan **Nätverksinställningar** under **DNS-servrarna:**
   
   1. En primär och sekundär DNS-server konfigureras automatiskt. Om du väljer att konfigurera statiska IP-adresser kan du ange DNS-servrar. För hög tillgänglighet rekommenderar vi att du konfigurerar en primär och en sekundär DNS-server.
   2. Klicka på **Använd** om du vill tillämpa och validera nätverksinställningarna.
6. På sidan **Enhetsinställningar:**
   
   1. Tilldela enheten ett unikt **namn.** Det här namnet kan vara 1-15 tecken och kan innehålla bokstav, siffror och bindestreck.
   2. Klicka på ikonen ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png) **Filserver** för den **typ** av enhet som du skapar. En filserver gör att du kan skapa delade mappar.
   3. Eftersom enheten är en filserver måste du ansluta enheten till en domän. Ange ett **domännamn**.
   4. Klicka på **Använd**.
7. En dialogruta visas. Ange dina domänuppgifter i angivet format. Klicka på checkikonen. Domänautentiseringsuppgifterna verifieras. Ett felmeddelande visas om autentiseringsuppgifterna är felaktiga.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Klicka på **Använd**. Detta kommer att gälla och validera enhetsinställningarna.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Kontrollera att den virtuella matrisen finns i en egen organisationsenhet (OU) för Active Directory och att inga grupprincipobjekt (GPO) tillämpas på den eller ärvs. Grupprincipen kan installera program som antivirusprogram på StorSimple Virtual Array. Installera ytterligare programvara stöds inte och kan leda till data korruption. 
   > 
   > 
9. (Du kan också konfigurera webbproxyservern. Även om webbproxykonfigurationen är valfri, var medveten om att om du använder en webbproxy så kan du bara konfigurera den här.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   På **sidan Webbproxy:**
   
   1. Ange **webbproxy-URL:en** i det här formatet: *http://&lt;&gt;värd-IP-adress eller FQDN-:Portnummer*. Observera att HTTPS-url:er inte stöds.
   2. Ange **autentisering** som **grundläggande** eller **ingen**.
   3. Om du använder autentisering måste du också ange ett **användarnamn** och **lösenord**.
   4. Klicka på **Använd**. Detta validerar och tillämpar de konfigurerade webbproxyinställningarna.
10. (Du kan också) konfigurera tidsinställningarna för enheten, till exempel tidszon och de primära och sekundära NTP-servrarna. NTP-servrar krävs eftersom enheten måste synkronisera tiden så att den kan autentisera med dina molntjänstleverantörer.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    På sidan **Tidsinställningar:**
    
    1. Välj **tidszonen i** listrutan baserat på den geografiska plats där enheten distribueras. Standardtidszonen för enheten är PST. Enheten använder den här tidszonen för alla schemalagda åtgärder.
    2. Ange en **primär NTP-server** för enheten eller acceptera standardvärdet för time.windows.com. Kontrollera att ditt nätverk tillåter att NTP-trafik skickas från ditt datacenter till Internet.
    3. Du kan också ange en **sekundär NTP-server** för enheten.
    4. Klicka på **Använd**. Detta validerar och tillämpar de konfigurerade tidsinställningarna.
11. Konfigurera molninställningarna för enheten. I det här steget slutför du den lokala enhetskonfigurationen och registrerar sedan enheten med tjänsten StorSimple Device Manager.
    
    1. Ange den **tjänstregistreringsnyckel** som du fick i [steg 2: Hämta tjänstregistreringsnyckeln](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) för StorSimple Virtual Array.
    2. Om detta är din första enhet som registrerar sig med den här tjänsten kommer du att presenteras med **krypteringsnyckeln för tjänstens data**. Kopiera den här nyckeln och spara den på säker plats. Den här nyckeln krävs med tjänstens registreringsnyckel för att registrera ytterligare enheter med Tjänsten StorSimple Device Manager. 
       
       Om detta inte är den första enheten som du registrerar med den här tjänsten måste du ange krypteringsnyckeln för tjänstdata. Mer information finns i hämta [krypteringsnyckeln](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) för tjänstdata i ditt lokala webbgränssnitt.
    3. Klicka på **Registrera**. Detta startar om enheten. Du kan behöva vänta i 2-3 minuter innan enheten har registrerats. När enheten har startats om kommer du till inloggningssidan.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Gå tillbaka till Azure-portalen. Gå till **Alla resurser**och sök efter tjänsten StorSimple Device Manager.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. I den filtrerade listan väljer du tjänsten StorSimple Device Manager och navigerar sedan till **Hantering > Enheter**. Kontrollera att enheten har anslutits till tjänsten i bladet **Enheter** och har statusen **Redo att konfigurera**.
    
    ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Steg 2: Konfigurera enheten som filserver
Utför följande steg i [Azure-portalen](https://portal.azure.com/) för att slutföra den nödvändiga enhetskonfigurationen.

#### <a name="to-configure-the-device-as-file-server"></a>Så här konfigurerar du enheten som filserver
1. Gå till tjänsten StorSimple Device Manager och gå sedan till **Hantering >-enheter**. I bladet **Enheter** väljer du den enhet som du just skapade. Den här enheten visas som **klar att konfigureras**.
   
   ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Klicka på enheten så visas ett banderollmeddelande som anger att enheten är redo att konfigureras.
   
    ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Klicka på **Konfigurera** i kommandofältet. Då öppnas **bladet Konfigurera.** Gör följande i **bladet Konfigurera:**
   
   1. Filservernamnet fylls i automatiskt.
    
   2. Kontrollera att molnlagringskrypteringen är **aktiverad**. Detta krypterar alla data som skickas till molnet. 
    
   3. En 256-bitars AES-nyckel används med den användardefinierade nyckeln för kryptering. Ange en tangent på 32 tecken och ange nyckeln igen för att bekräfta den. Registrera nyckeln i en nyckelhanteringsapp för framtida referens.
    
   4. Klicka på **Konfigurera obligatoriska inställningar** för att ange autentiseringsuppgifter för lagringskonto som ska användas med enheten. Klicka på **Lägg till ny** om inga lagringskontouppgifter har konfigurerats. **Kontrollera att lagringskontot du använder stöder blockblobar. Sidblobar stöds inte.** Mer information om [blockblobar och sidblobar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
      ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. Gör följande i bladet **Lägg till ett lagringskontouppgifter:** 

    1. Välj aktuell prenumeration om lagringskontot har samma prenumeration som tjänsten. Ange annat är lagringskontot ligger utanför tjänstprenumerationen. 
    
    2. Välj ett befintligt lagringskonto i listrutan. 
    
    3. Platsen fylls i automatiskt baserat på det angivna lagringskontot. 
    
    4. Aktivera TLS för att säkerställa en säker nätverkskommunikationskanal mellan enheten och molnet.
    
    5. Klicka på **Lägg till** om du vill lägga till den här lagringskontoautentiseringen. 
   
        ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. När lagringskontoautentiseringsuppgifterna har skapats uppdateras **bladet Konfigurera** för att visa de angivna autentiseringsuppgifterna för lagringskontot. Klicka på **Konfigurera**.
   
   ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Du kommer att se en att en filserver skapas. När filservern har skapats meddelas du.
   
   ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Enhetsstatusen ändras också till **Online**.
   
   ![Konfigurera en filserver](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Du kan fortsätta att lägga till en resurs.

## <a name="step-3-add-a-share"></a>Steg 3: Lägga till en resurs
Skapa en resurs genom att utföra stegen nedan på [Azure-portalen](https://portal.azure.com/).

#### <a name="to-create-a-share"></a>Så här skapar du en resurs
1. Markera den filserverenhet som du konfigurerade i föregående steg och klicka på **...** (eller högerklicka). Välj Lägg till **resurs**på snabbmenyn . Du kan också klicka på **+ Lägg till resurs** i enhetskommandofältet.
   
   ![Lägga till en resurs](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Ange följande resursinställningar:

   1. Ett unikt namn på din aktie. Namnet måste vara en sträng som innehåller 3 till 127 tecken.
    
   2. En valfri **beskrivning** för resursen. Beskrivningen hjälper till att identifiera aktieägarna.
    
   3. En **typ** för resursen. Typen kan **nivåieras** eller **lokalt fästas,** med nivåindelad som standard. För arbetsbelastningar som kräver lokala garantier, låga svarstider och högre prestanda väljer du en **lokalt fäst** resurs. För alla andra data väljer du en **nivåindelad** resurs.
      En lokalt fäst resurs etableras tjockt och säkerställer att de primära data på resursen förblir lokala till enheten och inte spiller till molnet. En nivåindelad resurs är däremot tunt etablerad. När du skapar en nivåindelad resurs etableras 10 % av utrymmet på den lokala nivån och 90 % av utrymmet etableras i molnet. Om du till exempel har etablerat en volym på 1 TB skulle 100 GB finnas i det lokala utrymmet och 900 GB skulle användas i molnet när datanivåerna. Detta innebär i sin tur att om du får på allt lokalt utrymme på enheten kan du inte etablera en nivåindelad resurs.
   
   4. I fältet **Ange fullständiga standardbehörigheter till,** tilldela behörigheterna till användaren eller den grupp som använder den här resursen. Ange namnet på användaren eller användargruppen i *\@john contoso.com-format.* Vi rekommenderar att du använder en användargrupp (i stället för en enskild användare) för att tillåta administratörsbehörighet att komma åt dessa resurser. När du har tilldelat behörigheterna här kan du sedan använda Utforskaren till att ändra dessa behörigheter.
   
   5. Klicka på **Lägg till** om du vill skapa resursen. 
    
       ![Lägga till en resurs](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       Du får ett meddelande om att resursen skapas.
   
       ![Lägga till en resurs](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      När resursen har skapats med de angivna inställningarna uppdateras bladet **Aktier** för att återspegla den nya resursen. Som standard är övervakning och säkerhetskopiering aktiverad för resursen.
   
      ![Lägga till en resurs](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Steg 4: Anslut till resursen
Du måste nu ansluta till en eller flera resurser som du skapade i föregående steg. Utför de här stegen på din Windows Server-värd som är ansluten till den virtuella storsimple-matrisen.

#### <a name="to-connect-to-the-share"></a>Så här ansluter du till resursen
1. Tryck ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) på + R. I fönstret Kör anger *du &lt;&#92;&#92;filservernamnet&gt; * som sökväg och ersätter *filservernamnet* med det enhetsnamn som du har tilldelat filservern. Klicka på **OK**.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Detta öppnar Utforskaren. Du bör nu kunna se de resurser du har skapat som mappar. Välj och dubbelklicka på en resurs (mapp) för att visa innehållet.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Du kan nu lägga till filer i dessa resurser och göra en säkerhetskopia.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du använder det lokala webbgränssnittet för att [administrera din StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

