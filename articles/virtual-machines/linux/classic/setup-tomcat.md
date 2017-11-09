---
title: "Ställa in Apache Tomcat på en Linux-dator | Microsoft Docs"
description: "Lär dig hur du ställer in Apache Tomcat7 med hjälp av Azure virtuella datorer som kör Linux."
services: virtual-machines-linux
documentationcenter: 
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 45ecc89c-1cb0-4e80-8944-bd0d0bbedfdc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: ningk
ms.openlocfilehash: b76f6f7f53bc649fbc740a79e182f81f3fd983c7
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2017
---
# <a name="set-up-tomcat7-on-a-linux-virtual-machine-with-azure"></a>Ställa in Tomcat7 på en Linux-dator med Azure
Apache Tomcat (eller bara Tomcat kan också kallades Djakarta Tomcat) är en öppen källkod webbservern och servlet-behållare som utvecklats av Apache Software Foundation (ASF). Tomcat implementerar Java-Servlet och specifikationer för JavaServer sidor (JSP) från Sun Microsystems. Tomcat ger en ren Java HTTP web server-miljö där du kan köra Java-kod. I den enklaste konfigurationen körs Tomcat i en enda process. Den här processen körs en Java virtual machine (JVM). Alla HTTP-begäran från en webbläsare till Tomcat behandlas som en separat tråd i Tomcat-processen.  

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln beskriver hur du använder den klassiska distributionsmodellen. Vi rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Om du vill använda en Resource Manager-mall för att distribuera en VM Ubuntu med öppna JDK och Tomcat, se [i den här artikeln](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

I den här artikeln får du installerar Tomcat7 på en Linux-avbildning och distribuera den i Azure.  

Du kommer att lära dig:  

* Så här skapar du en virtuell dator i Azure.
* Hur du förbereder den virtuella datorn för Tomcat7.
* Så här installerar du Tomcat7.

Det förutsätts att du redan har en Azure-prenumeration.  Om inte, du kan registrera dig för en kostnadsfri utvärderingsversion på [Azure-webbplatsen](https://azure.microsoft.com/). Om du har en MSDN-prenumeration, se [Microsoft särskilda priser för Azure: MSDN, MPN och BizSpark fördelar](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Läs mer om Azure i [vad är Azure?](https://azure.microsoft.com/overview/what-is-azure/).

Den här artikeln förutsätter att du har grundläggande kunskaper om Tomcat- och Linux.  

## <a name="phase-1-create-an-image"></a>Fas 1: Skapa en avbildning
I det här steget ska du skapa en virtuell dator med hjälp av en Linux-avbildning i Azure.  

### <a name="step-1-generate-an-ssh-authentication-key"></a>Steg 1: Skapa en SSH-nyckel för autentisering
SSH är ett viktigt verktyg för administratörer. Dock rekommenderas konfigurera åtkomstsäkerhet baserat på ett fastställt mänskliga lösenord inte. Angripare kan dela upp i ditt system baserat på ett användarnamn och ett svagt lösenord.

Goda nyheter är att det är ett sätt att lämna fjärråtkomst öppen och oroa dig inte om lösenord. Den här metoden består av autentisering med asymmetrisk kryptering. Den privata nyckeln är den som ger autentisering. Du kan även låsa användarens konto för att inte tillåta lösenordsautentisering.

En annan fördel med den här metoden är att du inte behöver olika lösenord för att logga in på olika servrar. Du kan autentisera med hjälp av den personliga privata nyckeln på alla servrar som förhindrar att behöva komma ihåg flera lösenord.



Följ dessa steg för att generera nyckeln SSH-autentisering.

1. Hämta och installera PuTTYgen från följande plats: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Kör Puttygen.exe.
3. Klicka på **generera** att generera nycklar. Du kan öka slumpmässighet genom att flytta musen över det tomma utrymmet i fönstret i processen.  
   ![PuTTY nyckel Generator skärmbild som visar knappen Skapa ny nyckel][1]
4. När processen generera visas Puttygen.exe den offentliga nyckeln.  
   ![PuTTY nyckel Generator skärmbild som visar den offentliga nyckeln och Spara privat nyckel knappen][2]
5. Markera och kopiera den offentliga nyckeln och spara det i en fil med namnet publicKey.pem. Klicka inte på **spara offentlig nyckel**, eftersom den sparade offentliga nyckeln filformatet skiljer sig från den offentliga nyckeln som vi vill.
6. Klicka på **Spara privat nyckel**, och spara den i en fil med namnet privateKey.ppk.

### <a name="step-2-create-the-image-in-the-azure-portal"></a>Steg 2: Skapa avbildningen i Azure-portalen
1. I den [portal](https://portal.azure.com/), klickar du på **ny** i Aktivitetsfältet för att skapa en avbildning. Välj den Linux-avbildning som är baserad på dina behov. I följande exempel används Ubuntu 14.04 bilden.
![Skärmbild av portalen som visar knappen Nytt][3]

2. För **värdnamn**, ange namnet på den URL som du och Internet-klienter använder för att komma åt den virtuella datorn. Definiera den sista delen av DNS-namn, till exempel tomcatdemo. Azure skapar sedan URL: en som tomcatdemo.cloudapp.net.  

3. För **SSH autentiseringsnyckel**, Kopiera värdet för nyckeln från filen publicKey.pem, som innehåller den offentliga nyckeln som genererats av PuTTYgen.  
![SSH-autentiseringsnyckel rutan i portalen][4]

4. Konfigurera övriga inställningar efter behov och klicka sedan på **skapa**.  

## <a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Fas 2: Förbereda den virtuella datorn för Tomcat7
I det här steget kommer du konfigurerar en slutpunkt för Tomcat trafik och ansluter sedan till den nya virtuella datorn.

### <a name="step-1-open-the-http-port-to-allow-web-access"></a>Steg 1: Öppna HTTP-porten för att tillåta webbåtkomst
Slutpunkter i Azure består av ett TCP- eller UDP-protokoll, tillsammans med en offentlig och privat port. Den privata porten är den port som tjänsten lyssnar på den virtuella datorn. Den offentliga porten är den port som Azure-Molntjänsten lyssnar på externt för inkommande, Internet-baserade trafik.  

TCP-port 8080 är standardportnumret som Tomcat använder för att lyssna. Om den här porten är öppen med en Azure slutpunkt du och andra Internet-klienter kan komma åt Tomcat sidor.  

1. I portalen klickar du på **Bläddra** > **virtuella datorer**, och klicka sedan på den virtuella datorn som du skapade.  
   ![Skärmbild av katalogen virtuella datorer][5]
2. Om du vill lägga till en slutpunkt till den virtuella datorn klickar du på den **slutpunkter** rutan.
   ![Skärmbild som visar rutan slutpunkter][6]
3. Klicka på **Lägg till**.  

   1. Ange ett namn för slutpunkten i för slutpunkten, **Endpoint**, och ange sedan 80 i **offentlig Port**.  

      Om du ställer in den 80, behöver du inte inkludera portnumret i den URL som används för att komma åt Tomcat. Till exempel http://tomcatdemo.cloudapp.net.    

      Om du anger ett annat värde, till exempel 81, behöver du lägga till portnumret i URL: en för Tomcat. Till exempel http://tomcatdemo.cloudapp.net:81 /.
   2. Ange 8080 i **privat Port**. Som standard lyssnar Tomcat på TCP-port 8080. Om du har ändrat standardinställningen lyssna port Tomcat, bör du uppdatera **privat Port** ska vara samma som Tomcat lyssningsport.  
      ![Skärmbild av Användargränssnittet som visar Lägg till kommandot offentlig Port och privat Port][7]
4. Klicka på **OK** att lägga till slutpunkten till den virtuella datorn.

### <a name="step-2-connect-to-the-image-you-created"></a>Steg 2: Anslut till den bild som du skapat
Du kan välja ett SSH-verktyg för att ansluta till den virtuella datorn. I det här exemplet använder vi PuTTY.  

1. Hämta DNS-namnet på den virtuella datorn från portalen.
    1. Klicka på **Bläddra** > **virtuella datorer**.
    2. Välj namnet på den virtuella datorn och klicka sedan på **egenskaper**.
    3. I den **egenskaper** panelen, titta i den **domännamn** om du vill hämta DNS-namn.  

2. Hämta porten för SSH-anslutningar från den **SSH** rutan.  
![Skärmbild som visar portnumret för SSH-anslutning][8]

3. Hämta [PuTTY](http://www.putty.org/).  

4. Klicka på den körbara filen Putty.exe efter hämtningen. Konfigurera alternativen för grundläggande med värdnamn i PuTTY-konfiguration och portnumret som hämtas från egenskaperna för den virtuella datorn.   
![Skärmbild som visar alternativ för namn på och portnummer för värden PuTTY-konfiguration][9]

5. I den vänstra rutan klickar du på **anslutning** > **SSH** > **Auth**, och klicka sedan på **Bläddra** att ange den platsen för filen privateKey.ppk. PrivateKey.ppk-filen innehåller den privata nyckeln som genereras av PuTTYgen tidigare i det ”fas 1: skapa en avbildning” i den här artikeln.  
![Skärmbild som visar anslutning directory-hierarkin och knappen Bläddra.][10]

6. Klicka på **öppna**. Du kan bli aviserad genom en meddelanderuta. Om du har konfigurerat DNS-namn och portnummer korrekt, klickar du på **Ja**.
![Skärmbild som visar meddelandet][11]

7. Du uppmanas att ange ditt användarnamn.  
![Skärmbild som visar var du vill ange ett användarnamn][12]

8. Ange det användarnamn som du använde för att skapa den virtuella datorn i den ”fas 1: skapa en avbildning” tidigare i den här artikeln. Du ser något som liknar följande:  
![Skärmbild som visar bekräftelse för autentisering][13]

## <a name="phase-3-install-software"></a>Fas 3: Installera programvara
I det här steget installera Java runtime environment, Tomcat7 och andra Tomcat7-komponenter.  

### <a name="java-runtime-environment"></a>Java runtime environment
Tomcat är skriven i Java. Det finns två typer av Java Development Kit (JDKs), OpenJDK och Oracle JDK. Du kan välja det du vill använda.  

> [!NOTE]
> Båda JDKs har nästan samma kod för klasser i Java-API, men koden för den virtuella datorn är olika. OpenJDK tenderar att använda öppna bibliotek, medan Oracle JDK tenderar att använda stängd de. Oracle JDK har flera klasser och vissa fast buggar och Oracle JDK är mer stabilt än OpenJDK.

#### <a name="install-openjdk"></a>Installera OpenJDK  

Använd följande kommando för att hämta OpenJDK.   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  


* Att skapa en katalog innehåller JDK-filer:  

        sudo mkdir /usr/lib/jvm  
* Extrahera JDK-filerna i katalogen/usr/lib/jvm:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/

#### <a name="install-oracle-jdk"></a>Installera Oracle JDK


Använda följande kommando för att hämta Oracle JDK från Oracle-webbplats.  

     wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  
* Att skapa en katalog innehåller JDK-filer:  

        sudo mkdir /usr/lib/jvm  
* Extrahera JDK-filerna i katalogen/usr/lib/jvm:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  
* Att ställa in Oracle JDK som standard Java virtual machine:  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  

        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

#### <a name="confirm-that-java-installation-is-successful"></a>Bekräfta att Java-installationen har lyckats
Du kan använda ett kommando som följande för att testa om Java runtime environment har installerats på rätt sätt:  

    java -version  

Om du har installerat OpenJDK, bör du se följande meddelande: ![lyckade OpenJDK installationen visas][14]

Om du har installerat Oracle JDK, bör du se följande meddelande: ![lyckade Oracle JDK installationen visas][15]

### <a name="install-tomcat7"></a>Installera Tomcat7
Använd följande kommando för att installera Tomcat7.  

    sudo apt-get install tomcat7  

Om du inte använder Tomcat7, använder du lämplig variation av det här kommandot.  

#### <a name="confirm-that-tomcat7-installation-is-successful"></a>Bekräfta att Tomcat7 installationen har lyckats
Bläddra till Tomcat-serverns DNS-namn om du vill kontrollera om Tomcat7 har installerats. Exempel-URL är http://tomcatexample.cloudapp.net/ i den här artikeln. Om du ser ett meddelande som följande Tomcat7 är korrekt installerad.
![Lyckad Tomcat7 installationen visas][16]

### <a name="install-other-tomcat7-components"></a>Installera komponenter för andra Tomcat7
Det finns andra valfria Tomcat-komponenter som du kan installera.  

Använd den **sudo lgh cache-sökning tomcat7** kommandot för att se alla tillgängliga komponenter. Använd följande kommandon för att installera vissa användbara komponenter.  

    sudo apt-get install tomcat7-admin      #admin web applications

    sudo apt-get install tomcat7-user         #tools to create user instances  

## <a name="phase-4-configure-tomcat7"></a>Steg 4: Konfigurera Tomcat7
I det här steget kan du administrera Tomcat.

### <a name="start-and-stop-tomcat7"></a>Starta och stoppa Tomcat7
Tomcat7 servern startar automatiskt när du installerar den. Du kan också starta den med följande kommando:   

    sudo /etc/init.d/tomcat7 start

Så här stoppar Tomcat7:

    sudo /etc/init.d/tomcat7 stop

Visa status för Tomcat7:

    sudo /etc/init.d/tomcat7 status

Starta om Tomcat-tjänster: 

    sudo /etc/init.d/tomcat7 restart

### <a name="tomcat7-administration"></a>Tomcat7 administration
Du kan redigera konfigurationsfilen Tomcat användare att konfigurera dina administratörsautentiseringsuppgifter. Ange följande kommando:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Här är ett exempel:  
![Skärmbild som visar sudo vi kommandoutdata][17]  

> [!NOTE]
> Skapa ett starkt lösenord för administratörsanvändarnamnet.  

När du redigerar filen, bör du starta om Tomcat7 tjänster med följande kommando för att säkerställa att ändringarna börjar gälla:  

    sudo /etc/init.d/tomcat7 restart  

Öppna din webbläsare och ange **http://<your tomcat server DNS name>/manager/html** som URL. Till exempel i den här artikeln är URL: en http://tomcatexample.cloudapp.net/manager/html.  

Efter anslutning, bör du se något som liknar följande:  
![Skärmbild av Tomcat Web Application Manager][18]

## <a name="common-issues"></a>Vanliga problem
### <a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Det går inte att komma åt den virtuella datorn med Tomcat och Moodle från Internet
#### <a name="symptom"></a>Symtom  
  Tomcat körs men du kan inte se sidan Tomcat standard med din webbläsare.
#### <a name="possible-root-cause"></a>Möjliga underliggande orsaker   

  * Tomcat lyssningsporten är inte samma som den privata porten för den virtuella datorns slutpunkten för Tomcat-trafik.  

     Kontrollera offentliga porten och privat port endpoint-inställningar och kontrollera att den privata porten är samma som Tomcat lyssningsport. Se ”fas 1: skapa en avbildning” i den här artikeln för instruktioner om hur du konfigurerar slutpunkter för den virtuella datorn.  

     Öppna /etc/httpd/conf/httpd.conf (Red Hat-version) eller /etc/tomcat7/server.xml (Debian-version) för att fastställa lyssningsporten för Tomcat. Som standard är Tomcat lyssna port 8080. Här är ett exempel:  

        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"   URIEncoding="UTF-8"            redirectPort="8443" />  

     Om du använder en virtuell dator som Debian och Ubuntu och du vill ändra den standard port för Tomcat lyssna (till exempel 8081) kan öppna du också porten för operativsystemet. Först öppna profilen:  

        sudo vi /etc/default/tomcat7  

     Sedan Avkommentera den sista raden och ändra ”Nej” till ”Ja”.  

        AUTHBIND=yes
  2. Brandväggen har inaktiverat lyssna port för Tomcat.

     Du kan bara se sidan Tomcat från den lokala värden. Problemet är sannolikt att den port som är öppna på Tomcat, blockeras av brandväggen. Du kan använda verktyget w3m för att söka på webbsidan. Följande kommandon installera w3m och gå till sidan med Tomcat:  


        sudo yum installera w3m w3m-img


        w3m http://localhost: 8080  
#### <a name="solution"></a>Lösning

  * Om Tomcat lyssna porten är inte samma som den privata porten för slutpunkten för trafik till den virtuella datorn, behöver du ändra den privata porten för att vara samma som Tomcat lyssningsport.   
  2. Om problemet orsakas av brandvägg/iptables, lägger du till följande rader /etc/sysconfig/iptables. Den andra raden krävs endast för https-trafik:  

      -A -p tcp -m tcp--datorer 80 -j ACCEPTERA indata

      -A -p tcp -m tcp--datorer 443 -j ACCEPTERA indata  

     > [!IMPORTANT]
     > Kontrollera att tidigare raderna placeras ovanför de rader som skulle globalt att begränsa åtkomsten till exempel följande: - A indata -j AVVISA--avvisa-med icmp värden förbjuden



Kör följande kommando för att läsa in iptables:

    service iptables restart

Det här har testats på CentOS 6.3.

### <a name="permission-denied-when-you-upload-project-files-to-varlibtomcat7webapps"></a>Åtkomst nekad när du överför projektfilerna till /var/lib/tomcat7/webapps /
#### <a name="symptom"></a>Symtom
  När du använder en SFTP-klient (till exempel FileZilla) att ansluta till den virtuella datorn och navigera till /var/lib/tomcat7/webapps/publicera webbplatsen kan få du ett felmeddelande liknar följande:  

     status:    Listing directory /var/lib/tomcat7/webapps
     Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
     Error:    /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
     Error:    File transfer failed
#### <a name="possible-root-cause"></a>Möjliga underliggande orsaker
  Du har inte åtkomstbehörighet till mappen /var/lib/tomcat7/webapps.  
#### <a name="solution"></a>Lösning  
  Du behöver få behörighet från rotkontot. Du kan ändra ägarskap för mappen från roten för användarnamnet som du använde när du har etablerat datorn. Här är ett exempel med azureuser kontonamn:  

     sudo chown azureuser -R /var/lib/tomcat7/webapps

  Använd alternativet -R för att tillämpa behörigheter för alla filer i en katalog för.  

  Det här kommandot fungerar även för kataloger. Alternativet -R ändrar behörigheter för alla filer och kataloger i katalogen. Här är ett exempel:  

     sudo chown -R username:group directory  

  Det här kommandot ändrar ägare (både användare och grupper) för alla filer och mappar som finns i katalogen.  

  Följande kommando ändrar bara behörigheten för katalogen som mappen. Filer och mappar i katalogen ändras inte.  

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
