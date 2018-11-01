---
title: Ställa in Apache Tomcat på en Linux-dator | Microsoft Docs
description: Lär dig hur du konfigurerar Apache Tomcat7 med hjälp av Azure virtuella datorer som kör Linux.
services: virtual-machines-linux
documentationcenter: ''
author: NingKuang
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 45ecc89c-1cb0-4e80-8944-bd0d0bbedfdc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: ningk
ms.openlocfilehash: 8c04c9fffbb85bb4db7a369b0dbbad6279f5d6f6
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420089"
---
# <a name="set-up-tomcat7-on-a-linux-virtual-machine-with-azure"></a>Konfigurera Tomcat7 på en Linux-dator med Azure
Apache Tomcat (eller bara Tomcat, också kallades Jakarta Tomcat) är en webbserver med öppen källkod och servletbehållare som har utvecklats av Apache Software Foundation (ASF). Tomcat implementerar Java Servlet och JavaServer sidor (JSP)-specifikationer från Sun Microsystems. Tomcat tillhandahåller en ren Java http-webbservermiljö där du kan köra Java-kod. I den enklaste konfigurationen körs Tomcat i en enda process. Den här processen körs en Java virtual machine (JVM). Varje HTTP-begäran från en webbläsare till Tomcat behandlas som en separat tråd i processen för Tomcat.  

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln beskriver hur du använder den klassiska distributionsmodellen. Vi rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Om du vill använda en Resource Manager-mall för att distribuera en Ubuntu VM med Open JDK och Tomcat, se [i den här artikeln](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

I den här artikeln ska du installera Tomcat7 på en Linux-avbildning och distribuera den på Azure.  

Du kommer att lära dig:  

* Så här skapar du en virtuell dator i Azure.
* Hur du förbereder den virtuella datorn för Tomcat7.
* Så här installerar du Tomcat7.

Det förutsätts att du redan har en Azure-prenumeration.  Om inte, du kan registrera dig för en kostnadsfri utvärderingsversion på [Azure-webbplatsen](https://azure.microsoft.com/). Om du har en MSDN-prenumeration, se [Microsoft specialpriser för Azure: MSDN, MPN och BizSpark-förmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Läs mer om Azure i [vad är Azure?](https://azure.microsoft.com/overview/what-is-azure/).

Den här artikeln förutsätter att du har grundläggande kunskaper om Tomcat- och Linux.  

## <a name="phase-1-create-an-image"></a>Fas 1: Skapa en avbildning
I det här steget skapar du en virtuell dator med hjälp av en Linux-avbildning i Azure.  

### <a name="step-1-generate-an-ssh-authentication-key"></a>Steg 1: Skapa en SSH-nyckel för autentisering
SSH är ett viktigt verktyg för systemadministratörer. Dock rekommenderas konfigurera åtkomstsäkerhet baserat på ett fastställt mänskliga lösenord inte. Angripare kan dela i ditt system baserat på ett användarnamn och ett svagt lösenord.

Den goda nyheten är att det finns ett sätt att lämna fjärråtkomst öppen och oroa dig inte om lösenord. Den här metoden består av autentisering med asymmetrisk kryptering. Den privata nyckeln är det som ger autentisering. Du kan även låsa användarens konto för att inte tillåta lösenordsautentisering.

En annan fördel med den här metoden är att du inte behöver olika lösenord för att logga in på olika servrar. Du kan autentisera med hjälp av den personliga privata nyckeln på alla servrar som hindrar dig från att behöva komma ihåg flera lösenord.



Följ stegen nedan för att skapa SSH-nyckeln för autentisering.

1. Hämta och installera PuTTYgen från följande plats: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Kör Puttygen.exe.
3. Klicka på **generera** att generera nycklar. Du kan öka slumpmässighet genom att flytta musen över det tomma utrymmet i fönstret i processen.  
   ![PuTTY-Nyckelgenerator skärmbild som visar knappen Generera ny nyckel][1]
4. När processen generera visar Puttygen.exe den offentliga nyckeln.  
   ![PuTTY-Nyckelgenerator skärmbild som visar den nya publika nyckeln och Spara privat nyckel knappen][2]
5. Markera och kopiera den offentliga nyckeln och spara den i en fil med namnet publicKey.pem. Klicka inte på **spara offentlig nyckel**, eftersom den sparade offentliga nyckeln filformat skiljer sig från den offentliga nyckeln som vi vill.
6. Klicka på **Spara privat nyckel**, och spara den i en fil med namnet privateKey.ppk.

### <a name="step-2-create-the-image-in-the-azure-portal"></a>Steg 2: Skapa avbildningen i Azure portal
1. I den [portal](https://portal.azure.com/), klickar du på **skapa en resurs** i Aktivitetsfältet för att skapa en avbildning. Välj Linux-avbildning som baseras på dina behov. I följande exempel används Ubuntu 14.04 avbildningen.
![Skärmbild av portalen som visar knappen Nytt][3]

2. För **värdnamn**, ange namnet på den URL som du och Internet-klienter använder för att komma åt den här virtuella datorn. Definiera den sista delen av DNS-namn, till exempel tomcatdemo. Azure skapar sedan URL: en som tomcatdemo.cloudapp.net.  

3. För **SSH autentiseringsnyckeln**, kopiera nyckelvärdet från filen publicKey.pem, som innehåller den offentliga nyckeln som genererats av PuTTYgen.  
![SSH autentiseringsnyckeln rutan i portalen][4]

4. Konfigurera övriga inställningar efter behov och klicka sedan på **skapa**.  

## <a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Fas 2: Förbereda din virtuella dator för Tomcat7
I det här steget kommer du konfigurerar en slutpunkt för Tomcat trafik och sedan ansluta till din nya virtuella dator.

### <a name="step-1-open-the-http-port-to-allow-web-access"></a>Steg 1: Öppna HTTP-porten för att tillåta webbåtkomst
Slutpunkter i Azure består av en TCP eller UDP-protokollet, tillsammans med en offentlig och privat port. Den privata porten är den port som tjänsten lyssnar på på den virtuella datorn. Den offentliga porten är den port som Azure-molntjänst lyssnar på externt för inkommande, Internet-baserad trafik.  

TCP-port 8080 är standardportarna som Tomcat använder för att lyssna. Om den här porten är öppen med en Azure slutpunkt du och andra Internet-klienter kan komma åt Tomcat sidor.  

1. I portalen klickar du på **Bläddra** > **virtuella datorer**, och klicka sedan på den virtuella datorn som du skapade.  
   ![Skärmbild av katalogen virtuella datorer][5]
2. Om du vill lägga till en slutpunkt i den virtuella datorn klickar du på den **slutpunkter** box.
   ![Skärmbild som visar rutan slutpunkter][6]
3. Klicka på **Lägg till**.  

   1. För slutpunkten, anger du ett namn för slutpunkten i **Endpoint**, och ange sedan 80 i **offentlig Port**.  

      Om du ställer in den till 80, behöver du inte inkluderar portnumret i den URL som används för att komma åt Tomcat. Till exempel http://tomcatdemo.cloudapp.net.    

      Om du anger ett annat värde, till exempel 81, måste du lägga till portnumret i URL: en för att komma åt Tomcat. Till exempel http://tomcatdemo.cloudapp.net:81/.
   2. Ange 8080 i **privat Port**. Som standard lyssnar Tomcat på TCP-port 8080. Om du har ändrat standard lyssna port på Tomcat, bör du uppdatera **privat Port** ska vara samma som Tomcat lyssningsport.  
      ![Skärmbild av användargränssnitt som visar Lägg till kommandot, offentlig Port och privat Port][7]
4. Klicka på **OK** att lägga till slutpunkten till den virtuella datorn.

### <a name="step-2-connect-to-the-image-you-created"></a>Steg 2: Anslut till den avbildning som du skapade
Du kan välja valfritt SSH-verktyg för att ansluta till den virtuella datorn. I det här exemplet använder vi PuTTY.  

1. Hämta DNS-namnet på den virtuella datorn från portalen.
    1. Klicka på **Bläddra** > **virtuella datorer**.
    2. Välj namnet på den virtuella datorn och klicka sedan på **egenskaper**.
    3. I den **egenskaper** panelen, titta i den **domännamn** rutan för att hämta DNS-namn.  

2. Hämta portnumret för SSH-anslutningar från den **SSH** box.  
![Skärmbild som visar portnumret för SSH-anslutning][8]

3. Ladda ned [PuTTY](http://www.putty.org/).  

4. Klicka på den körbara filen Putty.exe efter nedladdningen. Konfigurera alternativen för grundläggande med värdnamn i PuTTY-konfiguration, och portnumret som hämtas från egenskaperna för den virtuella datorn.   
![Skärmbild som visar alternativ för namn på och portnummer för värden PuTTY-konfiguration][9]

5. I den vänstra rutan klickar du på **anslutning** > **SSH** > **Auth**, och klicka sedan på **Bläddra** att ange den platsen för filen privateKey.ppk. PrivateKey.ppk-filen innehåller den privata nyckeln som genereras av PuTTYgen tidigare i den ”fas 1: skapa en avbildning” i den här artikeln.  
![Skärmbild som visar kataloghierarkin för anslutning och knappen Bläddra][10]

6. Klicka på **Open** (Öppna). Du kan bli aviserad om med en meddelanderuta. Om du har konfigurerat DNS-namn och portnummer korrekt, klickar du på **Ja**.
![Skärmbild som visar meddelandet][11]

7. Du uppmanas att ange ditt användarnamn.  
![Skärmbild som visar var du vill ange ett användarnamn][12]

8. Ange det användarnamn som du använde för att skapa den virtuella datorn i den ”fas 1: skapa en avbildning” tidigare i den här artikeln. Du kommer se något som liknar följande:  
![Skärmbild som visar autentisering-bekräftelse][13]

## <a name="phase-3-install-software"></a>Fas 3: Installera programvara
I det här steget installerar med Java runtime environment, Tomcat7 och andra Tomcat7-komponenter.  

### <a name="java-runtime-environment"></a>Java runtime environment
Tomcat är skriven i Java. Se [Azure stöds JDKs](https://aka.ms/azure-jdks) för information om hur du hämtar fullständigt stöd för Java-körmiljöer. Du kan också ta med din egen, men resten av den här artikeln använder Azure-stöd-versioner.


#### <a name="install-azure-supported-jdk"></a>Installera Azure stöds JDK

Följ den `apt-get` Installationsinstruktioner dokumenterade på den [Azul Zulu in för Azure](https://www.azul.com/downloads/azure-only/zulu/#apt-repo) webbplats.

#### <a name="confirm-that-java-installation-is-successful"></a>Bekräfta att Java-installationen har slutförts
Du kan använda ett kommando som liknar följande för att testa om med Java runtime environment har installerats korrekt:  
    Java-version  

Du bör se ett meddelande som liknar följande: ![lyckade OpenJDK installationen visas][14]


### <a name="install-tomcat7"></a>Installera Tomcat7
Använd följande kommando för att installera Tomcat7.  

    sudo apt-get install tomcat7  

Om du inte använder Tomcat7, använder du lämplig variation av det här kommandot.  

#### <a name="confirm-that-tomcat7-installation-is-successful"></a>Bekräfta att Tomcat7 installationen har slutförts
Du kan kontrollera om Tomcat7 har installerats genom att bläddra till Tomcat serverns DNS-namn. I den här artikeln exempel-URL är http://tomcatexample.cloudapp.net/. Om du ser ett meddelande som liknar följande Tomcat7 är korrekt installerad.
![Meddelande om lyckad Tomcat7 att installationen][16]

### <a name="install-other-tomcat7-components"></a>Installera andra Tomcat7-komponenter
Det finns andra valfria Tomcat-komponenter som du kan installera.  

Använd den **sudo apt-cache-sökning tomcat7** kommando för att se alla tillgängliga komponenter. Använd följande kommandon för att installera vissa användbara komponenter.  

    sudo apt-get install tomcat7-admin      #admin web applications

    sudo apt-get install tomcat7-user         #tools to create user instances  

## <a name="phase-4-configure-tomcat7"></a>Steg 4: Konfigurera Tomcat7
I det här steget kan du administrera Tomcat.

### <a name="start-and-stop-tomcat7"></a>Starta och stoppa Tomcat7
Tomcat7 servern startar automatiskt när du installerar den. Du kan också starta den med följande kommando:   

    sudo /etc/init.d/tomcat7 start

Sluta Tomcat7:

    sudo /etc/init.d/tomcat7 stop

Visa status för Tomcat7:

    sudo /etc/init.d/tomcat7 status

Starta om Tomcat tjänster: 

    sudo /etc/init.d/tomcat7 restart

### <a name="tomcat7-administration"></a>Tomcat7 administration
Du kan redigera konfigurationsfilen för Tomcat användare att ställa in dina autentiseringsuppgifter som administratör. Ange följande kommando:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Här är ett exempel:  
![Skärmbild som visar utdata från sudo vi][17]  

> [!NOTE]
> Skapa ett starkt lösenord för administratörens användarnamn.  

När du redigerat den här filen, bör du starta om Tomcat7 tjänster med följande kommando för att se till att ändringarna träder i kraft:  

    sudo /etc/init.d/tomcat7 restart  

Öppna din webbläsare och ange **http://<your tomcat server DNS name>/manager/html** som URL. För det här exemplet i den här artikeln URL: en är http://tomcatexample.cloudapp.net/manager/html.  

När du har anslutit, bör du se något som liknar följande:  
![Skärmbild av Tomcat Web Application Manager][18]

## <a name="common-issues"></a>Vanliga problem
### <a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Det går inte att komma åt den virtuella datorn med Tomcat och Moodle från Internet
#### <a name="symptom"></a>Symtom  
  Tomcat körs men du kan inte se standardsidan Tomcat med din webbläsare.
#### <a name="possible-root-cause"></a>Möjliga underliggande orsaker   

  * Lyssnarporten för Tomcat är inte samma som den privata porten för den virtuella datorns slutpunkten för Tomcat-trafik.  

     Kontrollera offentlig port och privat port endpoint-inställningar och kontrollera att den privata porten är samma som Tomcat lyssningsport. Se ”fas 1: skapa en avbildning” i den här artikeln för instruktioner om hur du konfigurerar slutpunkter för den virtuella datorn.  

     Öppna /etc/httpd/conf/httpd.conf (Red Hat-utgåvan) eller /etc/tomcat7/server.xml (Debian utgåvan) för att fastställa lyssnarporten för Tomcat. Som standard är Tomcat listen port 8080. Här är ett exempel:  

        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"   URIEncoding="UTF-8"            redirectPort="8443" />  

     Om du använder en virtuell dator som Debian eller Ubuntu och du vill ändra den standard port för Tomcat lyssna (till exempel 8081), bör du också öppna porten för operativsystemet. Först öppna profilen:  

        sudo vi /etc/default/tomcat7  

     Sedan ta bort den sista raden och ändra ”Nej” till ”Ja”.  

        AUTHBIND=yes
  2. Brandväggen har inaktiverats listen port för Tomcat.

     Du kan bara se Tomcat standardsidan från den lokala värden. Problemet är sannolikt att den port som är öppna på Tomcat, är blockerad av brandväggen. Du kan använda verktyget w3m för att söka på webbsidan. Följande kommandon installera w3m och bläddra till standardsidan Tomcat:  


        sudo yum install w3m w3m-img


        w3m http://localhost:8080  
#### <a name="solution"></a>Lösning

  * Om Tomcat lyssna porten är inte samma som den privata porten för slutpunkten för trafik till den virtuella datorn, behöver du ändra den privata porten för att vara samma som Tomcat lyssningsport.   
  2. Om problemet orsakas av brandvägg/iptables, lägger du till följande rader /etc/sysconfig/iptables. Den andra raden krävs endast för https-trafik:  

      – En indata -p tcp -m tcp--datorer 80 -j ACCEPTERA

      – En indata -p tcp -m tcp--datorer 443 -j ACCEPTERA  

     > [!IMPORTANT]
     > Se till att de föregående raderna placeras ovanför de rader som skulle globalt begränsa åtkomst till, till exempel följande: - en indata -j AVVISA--avvisa-med icmp-värd-förbjuden



Om du vill läsa in iptables, kör du följande kommando:

    service iptables restart

Det här har testats på CentOS 6.3.

### <a name="permission-denied-when-you-upload-project-files-to-varlibtomcat7webapps"></a>När du överför projektfilerna till /var/lib/tomcat7/webapps åtkomst nekad /
#### <a name="symptom"></a>Symtom
  När du använder en SFTP-klient (till exempel FileZilla) för att ansluta till den virtuella datorn och gå till /var/lib/tomcat7/webapps/att publicera din webbplats kan få ett felmeddelande visas liknar följande:  

     status:    Listing directory /var/lib/tomcat7/webapps
     Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
     Error:    /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
     Error:    File transfer failed
#### <a name="possible-root-cause"></a>Möjliga underliggande orsaker
  Du har inte behörighet att komma åt mappen /var/lib/tomcat7/webapps.  
#### <a name="solution"></a>Lösning  
  Du måste skaffa behörighet från rotkontot. Du kan ändra ägarskap för mappen från roten för användarnamnet som du använde när du har etablerat datorn. Här är ett exempel med azureuser kontonamn:  

     sudo chown azureuser -R /var/lib/tomcat7/webapps

  Använd alternativet -R för att tillämpa behörigheter för alla filer i en katalog för.  

  Det här kommandot fungerar även för kataloger. Alternativet -R ändrar behörigheterna för alla filer och kataloger i katalogen. Här är ett exempel:  

     sudo chown -R username:group directory  

  Det här kommandot ändrar ägarskap (både användare och grupper) för alla filer och kataloger som finns i katalogen.  

  Följande kommando ändrar endast tillåtelse från katalogen för distributionsmappen. Filer och mappar i katalogen ändras inte.  

     sudo chown username:group directory

[1]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
