---
title: 'Perimeternätverket nätverket exempel – skapa ett perimeternätverk, skydda program med en brandvägg och NSG: er | Microsoft Docs'
description: Skapa ett perimeternätverk med en brandvägg och Nätverkssäkerhetsgrupper (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: c1c64945aaa0bc4cd83cc769dab1c2a755896c01
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442486"
---
# <a name="example-2-build-a-perimeter-network-to-protect-applications-with-a-firewall-and-nsgs"></a>Exempel 2: Skapa ett perimeternätverk för att skydda program med en brandvägg och NSG: er
[Gå tillbaka till Microsofts molntjänster och security-sidan för nätverk][HOME]

Det här exemplet visar hur du skapar ett perimeternätverk (även kallat *DMZ*, *demilitariserad zon*, och *bevakat undernät*) med en brandvägg, fyra Windows Server-datorer och Nätverkssäkerhetsgrupper (NSG). Den innehåller information om var och en av relevanta kommandon för att ge en bättre förståelse för varje steg. ”Trafik scenarier”-avsnittet innehåller en stegvis förklaring av hur trafik fortsätter genom försvarslinjer i perimeternätverket. Slutligen innehåller i avsnittet ”referenser” fullständiga koden och instruktioner för hur du skapar den här miljön för att testa och experimentera med olika scenarier.

![Inkommande perimeternätverk med NVA och NSG: er][1]

## <a name="environment"></a>Miljö 
Det här exemplet är baserat på ett scenario med en Azure-prenumeration som innehåller följande objekt:

* Två molntjänster: FrontEnd001 och BackEnd001.
* Ett virtuellt nätverk med namnet CorpNetwork som har två undernät: Klientdelen och serverdelen.
* En enda NSG som tillämpas på båda undernäten.
* Ett nätverks virtuella installation: En Barracuda NextGen Firewall anslutet till undernätet på klientsidan.
* En Windows Server-dator som representerar en webbserver för programmet: IIS01.
* Två Windows Server-datorer som representerar programmet backend-servrar: AppVM01 och AppVM02.
* En Windows Server-dator som representerar en DNS-server: DNS01.

> [!NOTE]
> Även om det här exemplet använder en Barracuda NextGen Firewall kan skulle många virtuella nätverksinstallationer kunna användas.
> 
> 

PowerShell-skriptet i avsnittet ”referenser” i den här artikeln skapar de flesta av miljön som beskrivs här. Virtuella datorer och virtuella nätverk skapas av skriptet, men de processerna som inte beskrivs i detalj i det här dokumentet.

Att skapa miljön:

1. Spara nätverk XML-konfigurationsfilen i avsnittet ”referenser” (uppdateras med namn, platser och IP-adresser så att den matchar ditt scenario).
2. Uppdatera de användardefinierade variablerna i PowerShell-skript för att matcha den miljö som skriptet ska köras mot (prenumerationer, tjänstnamn och så vidare).
3. Kör skript i PowerShell.

> [!NOTE]
> Den region som anges i PowerShell-skriptet måste matcha den region som angetts i nätverket för XML-konfigurationsfilen.
>
>

När skriptet har körts, kan du utföra dessa steg:

1. Konfigurera brandväggsregler. Se avsnittet ”brandväggsregler” i den här artikeln.
2. Om du vill kan du konfigurera webbservern och applikationsserver med en enkel webbapp för att testa med datorns nätverkskonfiguration i perimeternätverket. Du kan använda två programskript som anges i avsnittet ”referenser”.

Nästa avsnitt beskrivs de flesta av skriptets instruktioner som är relaterade till NSG: er.

## <a name="nsgs"></a>NSG:er
I det här exemplet bygger en NSG-grupp och sedan läsa in med sex regler.

> [!TIP]
> I allmänhet bör du skapa dina specifika regler för ”Tillåt” först och mer allmänt ”Deny” reglerna senast. Tilldelad prioritet-kontroller som regler utvärderas först. När trafik har hittats som gäller för en specifik regel utvärderas inga fler regler. NSG-regler kan använda i det inkommande eller utgående riktning (ur undernätet).
> 
> 

Deklarativt, skapas dessa regler för inkommande trafik:

1. Interna DNS-trafik (port 53) tillåts.
2. RDP-trafik (port 3389) från internet för alla virtuella datorer är tillåtet.
3. HTTP-trafik (port 80) från internet till NVA (brandvägg) tillåts.
4. All trafik (alla portar) från IIS01 till AppVM01 tillåts.
5. All trafik (alla portar) från internet till hela virtuellt nätverk (både undernät) nekas.
6. All trafik (alla portar) från undernätet på klientsidan till BackEnd-undernät nekas.

Med dessa regler som är kopplade till varje undernät, om en HTTP-begäran har inkommande från internet till webbservern, både regel 3 (Tillåt) och regel 5 (neka) verkar gäller, men eftersom regel 3 har högre prioritet kan bara gäller den. Regel 5 har inte betydelse. Så får HTTP-begäran i brandväggen.

Om samma trafiken försökte nå DNS01 servern, regel 5 (neka) skulle vara först med att använda, så att trafiken inte skulle kunna skicka till servern. Regel 6 (neka) blockerar med FrontEnd-undernätet från att kommunicera med BackEnd-undernät (förutom för trafik som tillåts i reglerna 1 och 4). Detta skyddar BackEnd-nätverket om en angripare som komprometterar webbprogrammet på klientdelen. Angriparen skulle i så fall har begränsad åtkomst till nätverkets serverdel ”skyddade”. (Angriparen skulle kunna få åtkomst till resurserna som visas på servern AppVM01.)

Det finns en utgående standardregel som tillåter trafik ut till internet. I det här exemplet vi tillåter utgående trafik och ändrar inte någon utgående regler. Du behöver för att låsa trafik i båda riktningarna, användardefinierade routning. Du kan lära dig om den här tekniken i ett annat exempel i den [huvudsakliga security gräns dokumentet][HOME].

NSG-reglerna som beskrivs här liknar NSG-reglerna i [exempel 1 – skapa en enkel DMZ med NSG: er][Example1]. Granska beskrivningen NSG i den här artikeln för detaljerad information om varje NSG-regel och dess attribut.

## <a name="firewall-rules"></a>Brandväggsregler
Du måste installera en management-klienten på en dator för att hantera brandväggen och skapa de konfigurationer som krävs. Se dokumentationen från din brandvägg (eller andra NVA)-leverantören om hur du hanterar enheten. Resten av det här avsnittet beskriver konfigurationen av brandväggen, via leverantörens management-klienten (inte Azure-portalen eller PowerShell).

Se [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin) anvisningar som klienten ska ladda ned och ansluta till Barracuda-brandväggen som används i det här exemplet.

Du måste du skapa regler i brandväggen. Eftersom scenario i det här exemplet skickar endast internet-trafik inkommande i brandväggen och webbserver, behöver du bara en vidarebefordran NAT-regel. Barracuda-brandväggen som används i det här exemplet, är regeln en mål-NAT-regel (Dst NAT) för att skicka den här trafiken.

Skapa följande regel (eller verifiera befintliga standardregler), gör följande:
1. Barracuda NG Admin klienten instrumentpanelen på konfigurationsfliken i den **användningsinställningar** väljer **Ruleset**. 

   Ett rutnät kallas **Main regler** visar den befintliga aktiv och inaktiverad regler i brandväggen.

2. Om du vill skapa en ny regel väljer du den små gröna **+** knappen i det övre högra hörnet av det här rutnätet. (Brandväggen kan vara låst. Om du ser en knapp som markerats **Lås** och kan inte skapa eller redigera reglerna, Välj knappen för att låsa upp regeluppsättning och tillåta redigering.)
  
3. Om du vill redigera en befintlig regel, Välj den regeln, högerklicka och välj sedan **Redigera regel**.

Skapa en ny regel med namnet något som liknar **WebTraffic.** 

Ikonen för mål NAT-regel som ser ut så här: ![Mål NAT-ikon][2]

Regeln själva ser ut ungefär så här:

![Brandväggsregel][3]

Alla inkommande adresser som kommer till brandväggen försöker nå HTTP (port 80 eller 443 för HTTPS) skickas utanför i brandväggen DHCP1 lokala IP-gränssnittet och omdirigeras till webbservern med IP-adressen 10.0.1.5. Eftersom trafiken komma in på port 80 och gå till webbservern på port 80, krävs ingen portändring av. Men mållistan kan ha varit 10.0.1.5:8080 om webbservern lyssnade på port 8080, som kan översätta den inkommande porten 80 i brandväggen till inkommande port 8080 på webbservern.

Du bör också ange en anslutningsmetoden. Dynamisk SNAT är mest lämplig metod för Målregel från internet.

Även om du bara skapat en regel, är det viktigt att ange sin prioritet korrekt. I rutnätet med alla regler i brandväggen, om den nya regeln är längst ned (under BLOCKALL regeln), kommer den aldrig betydelse. Kontrollera att den nya regeln för webbtrafik är över BLOCKALL regeln.

När regeln har skapats kan behöva du push-överföra den till brandväggen och aktivera den. Om du inte vidtar de här stegen gälla inte regeln ändringen. I nästa avsnitt beskrivs hur du push-meddelanden och aktivering.

## <a name="rule-activation"></a>Regel för aktivering
Nu när regeln har lagts till i RuleSet-metod kan behöva du ladda upp den RuleSet-metod i brandväggen och aktivera den.

![Brandväggen regeln aktivering][4]

I det övre högra hörnet av management-klienten visas en grupp med knappar. Välj **skicka ändringar** att skicka ändrade RuleSet-metod i brandväggen och välj sedan **aktivera**.

Nu när du har aktiverat brandväggen RuleSet-metod har i miljön slutförts. Om du vill kan köra du programmet exempelskript i avsnittet ”referenser” för att lägga till ett program i miljön. Om du lägger till ett program kan testa du scenarier för trafik som beskrivs i nästa avsnitt.

> [!IMPORTANT]
> Du bör tänka på att du inte når webbservern direkt. När en webbläsare begär ett HTTP-sidan från FrontEnd001.CloudApp.Net, skickar HTTP-slutpunkt (port 80) trafiken till brandväggen, inte till webbservern. Brandväggen sedan, använder på grund av regeln som du skapade tidigare, NAT för att mappa begäran till webbservern.
> 
> 

## <a name="traffic-scenarios"></a>Trafik-scenarier
#### <a name="allowed-web-to-web-server-through-the-firewall"></a>(Tillåts) Web att webbserver genom brandväggen
1. En internet-användare begär en HTTP-sida från FrontEnd001.CloudApp.Net (en internet-ansluten molntjänst).
2. Molntjänsten skickar trafik via en öppen slutpunkt på port 80 till lokal i brandväggen-gränssnittet på 10.0.1.4:80.
3. Undernätet på klientsidan startar bearbetning av inkommande regel:
   1. NSG-regel 1 (DNS) gäller inte. Flytta till nästa regel.
   2. NSG-regel 2 (RDP) gäller inte. Flytta till nästa regel.
   3. NSG-regel 3 (internet-brandväggen) gäller. Tillåta trafik. Stoppa regelbearbetningen.
4. Trafiken kommer till den interna IP-adressen för brandväggen (is 10.0.1.4).
5. En brandvägg som vidarebefordrar regel anger att detta är port 80 trafik och omdirigerar till webbservern IIS01.
6. IIS01 lyssnar för webbtrafik, tar emot begäran och startar bearbetning av begäran.
7. IIS01 begär information från SQL Server-instansen på AppVM01.
8. Det finns inga regler för utgående trafik på undernätet på klientsidan så att trafik tillåts.
9. BackEnd-undernät startar bearbetning av inkommande regel:
   1. NSG-regel 1 (DNS) gäller inte. Flytta till nästa regel.
   2. NSG-regel 2 (RDP) gäller inte. Flytta till nästa regel.
   3. NSG-regel 3 (internet-brandväggen) gäller inte. Flytta till nästa regel.
   4. NSG-regel 4 (IIS01 till AppVM01) gäller. Tillåta trafik. Stoppa regelbearbetningen.
10. SQL Server-instansen på AppVM01 tar emot begäran och svarar.
11. Eftersom det finns inga regler för utgående trafik på BackEnd-undernät, tillåts svaret.
12. Undernätet på klientsidan startar bearbetning av inkommande regel:
    1. Det finns ingen NSG-regel som gäller för inkommande trafik från BackEnd-undernät för FrontEnd-undernätet så att ingen av NSG-reglerna gäller.
    2. System Standardregeln som tillåter trafik mellan undernät tillåter den här trafiken så att trafik tillåts.
13. IIS01 tar emot svaret från AppVM01, har slutförts HTTP-svar och skickar det till begäranden.
14. Eftersom detta är en NAT-session från brandväggen kan är målet svar inledningsvis för brandväggen.
15. Brandväggen tar emot svaret från webbservern och vidarebefordrar den tillbaka till internet-användare.
16. Eftersom det finns inga regler för utgående trafik på undernätet på klientsidan, svaret tillåts och internet-användare får webbsidan.

#### <a name="allowed-rdp-to-backend"></a>(Tillåts) RDP till serverdelen
1. En serveradministratör på internet begär en RDP-session till AppVM01 på BackEnd001.CloudApp.Net:*xxxxx*, där *xxxxx* är antalet slumpmässigt tilldelad port för RDP till AppVM01. (Du kan hitta den tilldelade porten på Azure portal eller med hjälp av PowerShell.)
2. Eftersom brandväggen lyssnar endast på FrontEnd001.CloudApp.Net-adress, ingår den inte med det här flödet i nätverkstrafiken.
3. BackEnd-undernät startar bearbetning av inkommande regel:
   1. NSG-regel 1 (DNS) gäller inte. Flytta till nästa regel.
   2. NSG-regel 2 (RDP) gäller. Tillåta trafik. Stoppa regelbearbetningen.
4. Eftersom det finns inga regler för utgående trafik, gäller standardregler och returnera trafik tillåts.
5. RDP-session är aktiverat.
6. AppVM01 ber om ett användarnamn och lösenord.

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Tillåts) Web server DNS-sökning på DNS-server
1. Web server, IIS01, måste en data-feed på www.data.gov men måste matcha adressen.
2. Nätverkskonfigurationen för virtuellt nätverk visar DNS01 (10.0.2.4 på BackEnd-undernät) som den primära DNS-servern. IIS01 skickar en DNS-begäran till DNS01.
3. Eftersom det finns inga regler för utgående trafik på undernätet på klientsidan, tillåts trafik.
4. BackEnd-undernät startar bearbetning av inkommande regel:
   1. NSG-regel 1 (DNS) gäller. Tillåta trafik. Stoppa regelbearbetningen.
5. DNS-servern tar emot begäran.
6. DNS-servern har inte den adress som cachelagras och frågar en rot-DNS-server på internet.
7. Eftersom det finns inga regler för utgående trafik på BackEnd-undernät, tillåts trafik.
8. Internet-DNS-servern svarar. Eftersom sessionen initierades internt tillåts svaret.
9. DNS-servern cachelagrar svaret och svarar på begäran från IIS01.
10. Eftersom det finns inga regler för utgående trafik på BackEnd-undernät, tillåts trafik.
11. Undernätet på klientsidan startar bearbetning av inkommande regel:
    1. Det finns ingen NSG-regel som gäller för inkommande trafik från BackEnd-undernät för FrontEnd-undernätet så att ingen av NSG-reglerna gäller.
    2. System Standardregeln som tillåter trafik mellan undernät tillåter den här trafiken så att trafiken tillåts.
12. IIS01 tar emot svaret från DNS01.

#### <a name="allowed-web-server-file-access-on-appvm01"></a>(Tillåts) Web server filåtkomst på AppVM01
1. IIS01 begär en fil på AppVM01.
2. Eftersom det finns inga regler för utgående trafik på undernätet på klientsidan, tillåts trafik.
3. BackEnd-undernät startar bearbetning av inkommande regel:
   1. NSG-regel 1 (DNS) gäller inte. Flytta till nästa regel.
   2. NSG-regel 2 (RDP) gäller inte. Flytta till nästa regel.
   3. NSG-regel 3 (internet-brandväggen) gäller inte. Flytta till nästa regel.
   4. NSG-regel 4 (IIS01 till AppVM01) gäller. Tillåta trafik. Stoppa regelbearbetningen.
4. AppVM01 tar emot begäran och svarar med (förutsatt att du har behörighet).
5. Eftersom det finns inga regler för utgående trafik på BackEnd-undernät, tillåts svaret.
6. Undernätet på klientsidan startar bearbetning av inkommande regel:
   1. Det finns ingen NSG-regel som gäller för inkommande trafik från BackEnd-undernät för FrontEnd-undernätet så att ingen av NSG-reglerna gäller.
   2. System Standardregeln som tillåter trafik mellan undernät tillåter den här trafiken så att trafiken tillåts.
7. IIS01 tar emot filen.

#### <a name="denied-web-direct-to-web-server"></a>(Nekad) Web-direct till webbservern
Eftersom webbservern IIS01 och brandväggen finns i samma molntjänst, delar de samma offentliga IP-adress. Så omdirigeras all HTTP-trafik i brandväggen. När en begäran skulle vara har behandlats, kan inte det gå direkt till webbservern. Den skickar, utformning genom brandväggen först. Se det första scenariot i det här avsnittet för trafikflödet.

#### <a name="denied-web-to-backend-server"></a>(Nekad) Webben till BackEnd-servern
1. En internet-användare försöker få åtkomst till en fil på AppVM01 via tjänsten BackEnd001.CloudApp.Net.
2. Eftersom det finns inga slutpunkter som är öppen för fildelning, detta vidarebefordrar inte Molntjänsten och kommer inte att nå servern.
3. Om slutpunkterna är öppna av någon anledning, blockerar NSG-regel 5 (internet till virtuellt nätverk) trafiken.

#### <a name="denied-web-dns-lookup-on-the-dns-server"></a>(Nekad) Web DNS-sökning på DNS-servern
1. En internet-användare försöker att leta upp en intern DNS-post på DNS01 via tjänsten BackEnd001.CloudApp.Net.
2. Eftersom det finns inga slutpunkter som är öppen för DNS kan detta vidarebefordrar inte Molntjänsten och kommer inte att nå servern.
3. Om slutpunkterna är öppna av någon anledning, blockerar NSG-regel 5 (internet till virtuellt nätverk) trafiken. (Regel 1 [DNS] gäller inte för två skäl. Först källadressen är internet och den här regeln gäller bara när det lokala virtuella nätverket är källan. Andra: den här regeln är en Tillåt-regel så att det aldrig nekar trafik.)

#### <a name="denied-web-to-sql-access-through-the-firewall"></a>(Nekad) Webben till SQL-åtkomst genom brandväggen
1. En internet-användare begär SQL-data från FrontEnd001.CloudApp.Net (en internet-ansluten cloud Services).
2. Eftersom det finns inga slutpunkter som är öppen för SQL, detta vidarebefordrar inte Molntjänsten och kommer inte att nå brandväggen.
3. Om slutpunkterna är öppna av någon anledning, startar bearbetning av inkommande regel undernätet på klientsidan:
   1. NSG-regel 1 (DNS) gäller inte. Flytta till nästa regel.
   2. NSG-regel 2 (RDP) gäller inte. Flytta till nästa regel.
   3. NSG-regel 3 (internet-brandväggen) gäller. Tillåta trafik. Stoppa regelbearbetningen.
4. Trafiken kommer till den interna IP-adressen för brandväggen (is 10.0.1.4).
5. Brandväggen har inga vidarebefordringsregler för SQL och släpper trafiken.

## <a name="conclusion"></a>Sammanfattning
Det här exemplet visar ett relativt enkelt sätt att skydda ditt program med en brandvägg och isolera serverdelsundernätet från inkommande trafik.

Du kan hitta fler exempel och en översikt över nätverket säkerhetsgränser [här][HOME].

## <a name="references"></a>Referenser
### <a name="full-script-and-network-config"></a>Fullständig skript och nätverk-config
Spara fullständigt skript i en PowerShell-skriptfilen. Spara nätverk config skriptet i en fil med namnet NetworkConf2.xml.
Ändra de definierade-Användarvariabler efter behov. Kör skriptet och följ sedan instruktionerna i avsnittet ”brandväggsregler” i den här artikeln.

#### <a name="full-script"></a>Fullständigt skript
Det här skriptet, baserat på användardefinierade variabler, Slutför följande steg:

1. Anslut till en Azure-prenumeration.
2. Skapa ett lagringskonto.
3. Skapa ett virtuellt nätverk och två undernät, som definieras i konfigurationsfilen för nätverket.
4. Skapa fyra Windows Server-datorer.
5. Konfigurera NSG. Konfigurationen är klar de här stegen:
   * Skapar en NSG.
   * Fyller NSG med regler.
   * Binder NSG: N till lämpliga undernät.

Du bör köra detta PowerShell-skript lokalt på en Internetansluten dator eller server.

> [!IMPORTANT]
> När du kör det här skriptet, visas varningar och andra informationsmeddelanden i PowerShell. Du behöver bara bry sig om felmeddelanden som visas i rött.
> 
> 

```powershell
    <# 
     .SYNOPSIS
      Example of a perimeter network and Network Security Groups in an isolated network. (Azure only. No hybrid connections.)

     .DESCRIPTION
      This script will build out a sample perimeter network setup containing:
       - A default storage account for VM disks.
       - Two new cloud services.
       - Two subnets (the FrontEnd and BackEnd subnets).
       - A network virtual appliance (NVA): a Barracuda NextGen Firewall.
       - One server on the FrontEnd subnet (plus the NVA on the FrontEnd subnet).
       - Three servers on the BackEnd subnet.
       - Network Security Groups to allow/deny traffic patterns as declared.

      Before running the script, ensure the network configuration file is created in
      the directory referenced by the $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in many ways. Be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that you can use, but it should not be used for all scenarios. You
      are responsible for assessing your security needs and the appropriate protections
      and then effectively implementing those protections.

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       myFirewall - 10.0.1.4
       IIS01      - 10.0.1.5

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User-Defined Global Variables
      # These should be changed to reflect your subscription and services.
      # Invalid options will fail in the validation section.

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User-Defined VM-Specific Config
        # To ensure proper NSG rule creation later in this script:
        #       - The web server must be VM 1.
        #       - The AppVM1 server must be VM 2.
        #       - The DNS server must be VM 4.
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No user-defined variables or   #
    # configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create storage account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update subscription pointer to new storage account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create virtual network
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the endpoints we'll need once we're up and running.
                # Note: Web traffic goes through the firewall, so we'll need to set up an HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: An SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *

        # Assign the NSG to the subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-Script Manual Configuration
      # Configure firewall
      # Install test web app (run post-build script on the IIS server)
      # Install back-end resources (run post-build script on AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

#### <a name="network-config-file"></a>Konfigurationsfilen för nätverk
Spara den här XML-filen med uppdaterade platser och sedan lägga till en länk till den här filen i variabeln $NetworkConfigFile i föregående skript.

```xml
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
```

#### <a name="sample-application-scripts"></a>Exempelskript för program
Om du vill installera ett exempelprogram för detta och andra exempel på perimeter-nätverk finns i den [exempel programskript][SampleApp].

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Inkommande perimeternätverk med NSG"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Mål NAT-ikon"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Brandväggsregel"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Brandväggen regeln aktivering"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
