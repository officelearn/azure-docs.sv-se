---
title: Konfigurera en ADCD (Application Developers Controlled Distribution) i IBM zD&T v1 | Microsoft-dokument
description: Kör en IBM Z-utvecklings- och testmiljö (zD&T) på virtuella Azure-datorer (VIRTUELLA datorer).
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 66f80c79219090c27da37dfc1d9149df5604961f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68841384"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Konfigurera en ADCD (Application Developers Controlled Distribution) i IBM zD&T v1

Du kan köra en IBM Z Development and Test Environment (zD&T) på Virtuella Azure-datorer (VIRTUELLA datorer). Den här miljön emulerar IBM Z-seriens arkitektur. Den kan vara värd för en mängd olika Z-serien operativsystem eller installationer (även kallad Z instanser eller paket), som görs tillgängliga via anpassade paket som kallas IBM Application Developers Controlled Distributions (ADCDs).

Den här artikeln visar hur du konfigurerar en ADCD-instans i en zD&T-miljö på Azure. ADCDs skapar kompletta implementeringar av Z-serien för utvecklings- och testmiljöer som körs i zD&T.

Liksom zD&T är ADCDs endast tillgängliga för IBM-kunder och partners och är uteslutande avsedda för utvecklings- och testningsändamål. De får inte användas för produktionsmiljöer. Många IBM-installationspaket finns tillgängliga för nedladdning via [Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) eller [IBM PartnerWorld.](https://www.ibm.com/partnerworld/public)

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har ett, skapa ett [gratis konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- [ZD-&T-miljön][ibm-install-z] har tidigare konfigurerats på Azure. Den här artikeln förutsätter att du använder samma Ubuntu 16.04 VM-avbildning som skapats tidigare.

- Tillgång till ADCD-media via IBM PartnerWorld eller Passport Advantage.

- En [licensieringsserver](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Detta krävs för att köra IBM zD&T. Hur du skapar den beror på hur du licensierar programvaran från IBM:

  - **Maskinvarubaserad licensieringsserver** kräver en USB-maskinvaruenhet som innehåller de rationella token som krävs för att komma åt alla delar av programvaran. Du måste få detta från IBM.

  - **Programvarubaserad licensieringsserver** kräver att du konfigurerar en centraliserad server för hantering av licensnycklarna. Den här metoden är att föredra och kräver att du ställer in nycklarna du får från IBM i hanteringsservern.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Ladda ner installationspaketen från Passport Advantage

Åtkomst till ADCD-mediet krävs. Stegen nedan förutsätter att du är en IBM-kunder och kan använda Passport Advantage. IBM-partner kan använda [IBM PartnerWorld.](https://www.ibm.com/partnerworld/public)

> [!NOTE]
> Den här artikeln förutsätter att en Windows-dator används för att komma åt Azure-portalen och hämta IBM-media. Om du använder ett Mac- eller Ubuntu-skrivbord kan kommandona och processen för att hämta IBM-mediet skilja sig något åt.

1. Logga in på [Passport Advantage](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Välj **Nedladdning av programvara** och Media **Access**.

3. Välj **Programerbjudande och avtalsnummer**och klicka på **Fortsätt**.

4. Ange artikelbeskrivningen eller artikelnumret och klicka på **Finder**.

5. Du kan också klicka på den alfabetiska ordningslistan för att visa och visa produkten efter namn.

6. Välj **Alla operativsystem** i fältet **Operativsystem**och Alla **språk** i fältet **Språk**. Klicka sedan på **Gå**.

7. Klicka på **Välj enskilda filer** om du vill expandera listan och visa det enskilda mediet som ska hämtas.

8. Kontrollera vilka paket som du vill hämta, välj **Hämta**och hämta sedan filerna till den katalog du vill använda.

## <a name="upload-the-adcd-packages"></a>Ladda upp ADCD-paketen(er)

Nu när du har paketen måste du ladda upp dem till din virtuella dator på Azure.

1. Starta en **ssh-session** med den virtuella Ubuntu-datorn som du skapade i Azure-portalen. Gå till den virtuella datorn, välj **bladet Översikt** och välj sedan **Anslut**.

2. Markera fliken **SSH** och kopiera sedan kommandot ssh till Urklipp.

3. Logga in på den virtuella datorn med dina autentiseringsuppgifter och den [SSH-klient](/azure/virtual-machines/linux/use-remote-desktop) som du väljer. Denna demo använder Linux-tillägg för Windows 10, som lägger till en bash skal till Windows kommandotolken. PuTTY fungerar lika bra.

4. När du är inloggad skapar du en katalog för att ladda upp IBM-paketen. Tänk på linux är skiftlägeskänslig. Den här demon förutsätter till exempel att paketen överförs till:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Ladda upp filerna med en SSH-klient som[WinSCP](https://winscp.net/eng/index.php). Eftersom SCP är en del av SSH använder den port 22, vilket är vad SSH använder. Om den lokala datorn inte är Windows kan du skriva [scp-kommandot](http://man7.org/linux/man-pages/man1/scp.1.html) i SSH-sessionen.

6. Initiera överföringen till Azure VM-katalogen som du skapade, som blir avbildningslagring för zD&T.

    > [!NOTE]
    > Se till att **ADCDTOOLS. XML** ingår i katalogen **hem/MyUserID/ZDT/adcd/nov2017.** Du behöver den senare.

7. Vänta tills filerna har överförts, vilket kan ta lite tid beroende på din anslutning till Azure.

8. När uppladdningarna är klara navigerar du till volymkatalogen och expanderar alla **gz-volymer:**

    ```
        gunzip \*.gz
    ```
    
![Utforskaren som visar expanderade gz-volymer](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Konfigurera avbildningslagringen

Nästa steg är att konfigurera zD&T för att använda de uppladdade paketen. Avbildningen lagringsprocessen inom zD&T kan du montera och använda bilderna. Den kan använda SSH eller FTP.

1. Starta **zDTServer**. För att göra detta måste du vara på rotnivå. Ange följande två kommandon i ordning:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Observera URL-utdata med kommandot och använd den här URL:en för att komma åt webbservern. Det liknar:
     > https://(ditt VM-namn eller IP-adress):9443/ZDTMC/index.html
     >
     > Kom ihåg att webbåtkomsten använder port 9443. Använd detta för att logga in på webbservern. Användar-ID för ZD&T är **zdtadmin** och lösenordet är **lösenord**.

    ![Välkomstskärmen för IBM zD&T Enterprise Edition](media/02-welcome.png)

3. Välj **Bildlagring**under **Konfigurera**på sidan **Snabbstart** .

     ![Snabbstartskärmen för IBM zD&T Enterprise Edition](media/03-quickstart.png)

4. Välj **SSH File Transfer Protocol**på sidan **Konfigurera bildlagring** .

5. För **Värdnamn**skriver du **Localhost** och anger katalogsökvägen för var du laddade upp bilderna. Till exempel /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Ange **användar-ID** och **lösenord** för den virtuella datorn. Använd inte ZD-&T-användar-ID och lösenord.

7. Testa anslutningen för att kontrollera att du har åtkomst och välj sedan **Spara** för att spara konfigurationen.

## <a name="configure-the-target-environments"></a>Konfigurera målmiljöer

Nästa steg är att konfigurera zD&T-målmiljön. Den här emulerade värdmiljön är där dina avbildningar körs.

1. Välj **Målmiljöer**under **Konfigurera**på sidan **Snabbstart** .

2. På sidan **Konfigurera målmiljöer** väljer du **Lägg till mål**.

3. Välj **Linux**. IBM stöder två typer av miljöer, Linux och Cloud(OpenStack), men den här demon körs på Linux.

4. Ange **localhost**för **värdnamn**på sidan **Lägg till målmiljö** . Håll **SSH-porten** inställd på **22**.

5. Ange en etikett som **MyCICS** i **etikettrutan Målmiljö.**

     ![Skärmen Lägg till målmiljö](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Konfigurera ADCD och distribuera

När du har slutfört de föregående konfigurationsstegen måste du konfigurera zD&T för att använda paketen och målmiljön. Återigen använder du bildlagringsprocessen i zD&T, vilket gör att du kan montera och använda bilderna. Den kan använda SSH eller FTP.

1. Välj **ADCD**under **Konfigurera**på sidan **Snabbstart** . En uppsättning instruktioner visas som talar om de steg som måste slutföras innan ett ADCD-paket kan monteras. Detta förklarar varför vi namngav målkatalogen som vi gjorde tidigare.

2. Förutsatt att alla bilder laddades upp till rätt kataloger, klicka på **BILDEN från ADCD** länken visas i nedre högra (visas i steg 7 i följande skärmdump).

     ![IBM zD&T Enterprise Edition – Konfigurera ADCD-skärm](media/05-adcd.png)

## <a name="create-the-image"></a>Skapa avbildningen

När föregående konfigurationssteg är klart visas sidan **Skapa en avbildning med ADCD-komponenter.**

1. Välj volymen (nov 2017 i det här fallet) om du vill visa de olika paket som finns i den volymen.

2. För den här demon väljer du **CICS (Customer Information Control System) - 5.3**.

3. Skriv ett namn på bilden som **MyCICS Image**i rutan **Bildnamn** .

4. Välj knappen **Skapa bild** längst ned till höger.

     ![IBM zD&T Enterprise Edition – Skapa en bild med adcd-komponenter](media/06-adcd.png)

5. I fönstret som visas och berätta att avbildningen har distribuerats väljer du **Distribuera avbildningar**.

6. På sidan **Distribuera en bild till en målmiljö** markerar du den bild som du skapade på föregående sida (**MyCICS Image**) och målmiljön som skapades tidigare (**MyCICS**).

7. På nästa skärm anger du dina autentiseringsuppgifter för den virtuella datorn (det vill än ztadmin-referensen).

8. I fönstret Egenskaper anger du antalet **CP-processorer (Central processorer),** mängden **systemminne (GB)** och **distributionskatalogen** för den pågående avbildningen. Eftersom detta är en demo, hålla den liten.

9. Kontrollera att rutan är markerad för **IPL-kommando automatiskt till z/OS efter distribution**.

     ![Skärmen Egenskaper](media/07-properties.png)

10. Välj **Slutför**.

11. Välj **Distribuera avbildning** från **sidan Distribuera en avbildning till en målmiljösida.**

Din avbildning kan nu distribuera och är redo att monteras av en 3270 terminal emulator.

> [!NOTE]
> Om du får ett felmeddelande om att du inte har tillräckligt med diskutrymme bör du tänka på att regionen kräver 151 Gb.

Grattis! Du kör nu en IBM stordatormiljö på Azure.

## <a name="learn-more"></a>Läs mer

- [Stordatormigration: myter och fakta](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale på Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [Troubleshooting](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/) (Felsökning)
- [Avmystifiera stordator till Azure-migrering](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
