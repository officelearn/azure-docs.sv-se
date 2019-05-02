---
title: Ange in ett program utvecklare kontrollerad Distribution (ADCD) i IBM zD & T v1 | Microsoft Docs
description: Kör en IBM Z Development och Test-miljö (zD & T)-miljö på Azure Virtual Machines (VM).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: c6fcb345b49ce6354a24408ebe163fb928990252
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925681"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Ange in ett program utvecklare kontrollerad Distribution (ADCD) i IBM zD & T v1

Du kan köra en IBM Z Development och Test-miljö (zD & T)-miljö på Azure Virtual Machines (VM). Den här miljön emulerar IBM Z Series-arkitekturen. Det kan vara värd för en mängd olika operativsystem för Z-serien eller installationer (kallas även Z instanser eller paket), som är tillgängliga via anpassade paket kallas IBM-program utvecklare kontrolleras distributioner (ADCDs).

Den här artikeln visar hur du ställer in en ADCD-instans i en zD & T miljön i Azure. ADCDs skapa kompletta Z serien operativsystemet implementeringar för utveckling och testmiljöer som körs i zD & T.

Precis som zD & T, ADCDs är endast tillgängliga för IBM-kunder och partner och är exklusivt för utveckling och testning. De är inte ska användas för produktionsmiljöer. Ett stort antal IBM-paket är tillgängliga för hämtning via [Passport nytta](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) eller [IBM PartnerWorld](https://www-356.ibm.com/partnerworld/wps/servlet/ContentHandler/isv_com_sys_zos_adcd).

## <a name="prerequisites"></a>Nödvändiga komponenter

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Den [zD & T miljö] [ ibm-install-z] tidigare har konfigurerat på Azure. Den här artikeln förutsätter att du använder samma Ubuntu 16.04 VM-avbildning som skapades tidigare.

- Åtkomst till ADCD media via IBM PartnerWorld eller Passport nytta.

- En [licensieringsservern](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Detta krävs för att köra IBM zD & T. Hur du skapar den beror på hur du licensierar programvara från IBM:

  - **Maskinvarubaserad licensieringsservern** kräver en USB-maskinvaruenhet som innehåller de rationella token som behövs för att få åtkomst till alla delar av programvaran. Du måste skaffa från IBM.

  - **Programvarubaserade licensieringsservern** kräver att du installerar en central server för hantering av licensiering nycklarna. Den här metoden är att föredra och kräver att du ställer in de nycklar som du får från IBM i management-servern.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Ladda ned installationspaket från Passport nytta

Åtkomst till media ADCD krävs. Stegen nedan förutsätter att du har ett IBM-kunder och kan använda Passport nytta. IBM-partner kan använda [IBM PartnerWorld](https://www-356.ibm.com/partnerworld/wps/servlet/ContentHandler/isv_com_sys_zos_adcd).

> [!NOTE]
> Den här artikeln förutsätter att en Windows-dator används för att få åtkomst till Azure-portalen och hur du hämtar IBM-media. Om du använder en Mac- eller Ubuntu desktop kommandon och processen för att hämta IBM mediet kan skilja sig åt.

1. Logga in på [Passport nytta](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Välj **nedladdning av programvara** och **Media Access**.

3. Välj **programmet erbjudande och avtal**, och klicka på **Fortsätt**.

4. Ange en del beskrivning eller artikelnummer och klicka på **Finder**.

5. Du kan också klicka på alfabetisk ordning listan för att visa och visa theproduct efter namn.

6. Välj **alla operativsystem** i den **fältet operativsystem**, och **alla språk** i den **språk fältet**. Klicka sedan på **Gå**.

7. Klicka på **Välj enskilda filer** att expandera listan och visa enskilda mediet ska ladda ned.

8. Kontrollera de paket som du vill ladda ned, Välj **hämta**, och ladda ned filer till den katalog du vill.

## <a name="upload-the-adcd-packages"></a>Ladda upp ADCD-paket

Nu när du har paket överföra du dem till den virtuella datorn på Azure.

1. I Azure-portalen kan initiera en **ssh** session med Ubuntu-VM som du skapade. Gå till din virtuella dator väljer du den **översikt** bladet och välj sedan **Connect**.

2. Välj den **SSH** fliken och sedan kopiera den ssh-kommando till Urklipp.

3. Logga in på den virtuella datorn med dina autentiseringsuppgifter och [SSH-klient](/azure/virtual-machines/linux/use-remote-desktop) val. Den här demonstrationen använder Linux-tillägg för Windows 10 som lägger tillför ett bash-gränssnitt i Kommandotolken för Windows. PuTTY fungerar precis lika bra.

4. När inloggad kan du skapa en katalog för att ladda upp IBM-paket. Tänk på Linux är skiftlägeskänsligt. Den här demon förutsätter till exempel paket som överförs till:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Ladda upp filer med en SSH-klient som[WinSCP](https://winscp.net/eng/index.php). Eftersom SCP är en del av SSH, använder port 22, vilket är vad SSH använder. Om den lokala datorn inte är Windows, kan du skriva det [scp-kommandot](http://man7.org/linux/man-pages/man1/scp.1.html) i SSH-session.

6. Initiera överföringen till den Virtuella Azure-katalog som du skapade, som blir bildlagring för zD & T.

    > [!NOTE]
    > Se till att **ADCDTOOLS. XML** ingår i guiden Överför till den **home/MyUserID/ZDT/adcd/nov2017** directory. Du behöver den senare.

7. Vänta tills de filer som ska överföras, vilket kan ta lite tid beroende på din anslutning till Azure.

8. När överföringar har slutförts, gå till katalogen för volymer och expandera alla de **gz** volymer:

    ```
        gunzip \*.gz
    ```
    
![Utforskaren som visar expanderas gz volymer](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Konfigurera lagring för avbildning

Nästa steg är att konfigurera zD & T att använda den överförda paket. Avbildningen för lagring i zD & T kan du montera och använda avbildningar. Det kan använda SSH- eller FTP.

1. Starta den **zDTServer**. Om du vill göra detta måste finnas på rotnivå. Ange följande två kommandon i ordning:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Notera URL-utdata med kommandot och använda den här URL: en för åtkomst till webbservern. Den liknar:
     > https://(Your VM name or IP Address):9443/ZDTMC/index.HTML
     >
     > Kom ihåg att din webbåtkomst använder port 9443. Används för att logga in på webbservern. Användar-ID för ZD & T är **zdtadmin** och lösenordet är **lösenord**.

    ![IBM zD & T Enterprise Edition välkomstskärmen](media/02-welcome.png)

3. På den **Snabbstart** sidan under **konfigurera**väljer **bildlagring**.

     ![IBM zD & T Enterprise Edition Quick Start-skärmen](media/03-quickstart.png)

4. På den **konfigurera bildlagring** väljer **SSH File Transfer Protocol**.

5. För **värdnamn**, typ **Localhost** och ange sökvägen till katalogen där du laddade upp avbildningarna. Till exempel /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Ange den **användar-ID** och **lösenord** för den virtuella datorn. Använd inte ZD & T användar-ID och lösenord.

7. Testa anslutningen till att kontrollera att du har åtkomst och välj sedan **spara** att spara konfigurationen.

## <a name="configure-the-target-environments"></a>Konfigurera målmiljöer

Nästa steg är att konfigurera zD & T målmiljön. Den här emulerade värdmiljö är där dina avbildningar kör.

1. På den **Snabbstart** sidan under **konfigurera**väljer **rikta miljöer**.

2. På den **konfigurera målmiljöer** väljer **Lägg till mål**.

3. Välj **Linux**. IBM stöder två typer av miljöer, Linux och Cloud(OpenStack), men den här demon körs på Linux.

4. På den **Lägg till målmiljön** sidan för **värdnamn**, ange **localhost**. Behåll **SSH-porten** inställd **22**.

5. I den **målmiljö etikett** anger en etikett som **MyCICS.**

     ![Lägg till målskärmen för miljö](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Konfigurera ADCD och distribuera

När du har slutfört föregående konfigurationssteg, måste du konfigurera zD & T att använda paket och mål-miljön. Igen, kan du använda avbildningen för lagring i zD & T, vilket gör att du kan montera och använda avbildningar. Det kan använda SSH- eller FTP.

1. På den **Snabbstart** sidan under **konfigurera**väljer **ADCD**. En uppsättning instruktioner visas om de steg som måste slutföras innan ett ADCD paket kan monteras. Det förklarar varför vi valde namnet målkatalogen på sätt som vi utförde tidigare.

2. Förutsatt att alla bilder som har överförts till rätt kataloger, klickar du på den **avbildning från ADCD** länken som visas i det nedre högra hörnet (visas i steg 7 i följande skärmbild).

     ![IBM zD & T Enterprise Edition - konfigurera ADCD skärmen](media/05-adcd.png)

## <a name="create-the-image"></a>Skapa avbildningen

När det tidigare konfigurationssteget är slutförd, den **skapar du en avbildning med hjälp av ADCD komponenter** visas.

1. Välj volymen (Nov 2017 i det här fallet) för att visa olika paket som tillhör den volymen.

2. Den här demon Välj **kundens Information kontroll System (CICS) - 5.3**.

3. I den **avbildningsnamn** skriver ett namn för avbildningen som **MyCICS bild**.

4. Välj den **Skapa avbildning** knappen längst ned till höger.

     ![IBM zD & T Enterprise Edition - skapa en avbildning med hjälp av skärmen ADCD komponenter](media/06-adcd.png)

5. I fönstret som visas om avbildningen har distribuerats, Välj **distribuera avbildningar**.

6. På den **distribuera en avbildning till en målmiljö** väljer du den avbildning som du skapade på föregående sida (**MyCICS bild**) och målmiljön för skapade tidigare (**MyCICS**).

7. På nästa skärm anger du dina autentiseringsuppgifter för den virtuella datorn (det vill säga inte ztadmin autentiseringsuppgifter).

8. I fönstret Egenskaper anger du antalet **centrala processorer (CPs)**, mängden **systemminnet (GB)**, och **distributionskatalogen** för avbildningen som körs. Eftersom detta är en demo du hålla allt små.

9. Kontrollera att den är markerad för **problemet IPL kommando för att z/OS när du distribuerar automatiskt**.

     ![Egenskapsskärmen](media/07-properties.png)

10. Välj **fullständig**.

11. Välj **distribuera avbildningen** från den **distribuera en avbildning till en målmiljö** sidan.

Avbildningen kan nu distribuera och är redo att monteras av en terminalemulator 3270.

> [!NOTE]
> Om du får ett felmeddelande om du inte har tillräckligt med ledigt diskutrymme, Observera att regionen kräver 151 Gb.

Grattis! En miljö för IBM-stordatorer körs nu på Azure.

## <a name="learn-more"></a>Läs mer

- [Stordatormigrering: myths och fakta](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale på Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [Felsökning](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Avmystifiera stordatorprogram till Azure-migrering](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
