---
title: Perimeternätverksexempel – skapa ett program för perimeternätverksskydd med en brandvägg och NSG:er | Microsoft Docs
description: Bygg en DMZ med brandvägg och Nätverkssäkerhetsgrupper (NSG)
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
ms.openlocfilehash: 31d945f64cccd0c811d4dc45163583224102fb8a
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55965247"
---
# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>Exempel 2 – Skapa ett perimeternätverk för att skydda program med en brandvägg och NSG:er
[Gå tillbaka till gränsen bästa praxis sidan][HOME]

Det här exemplet skapar en DMZ med brandvägg, fyra windows-servrar och Nätverkssäkerhetsgrupper. Den visar även hur var och en av relevanta kommandon för att ge en bättre förståelse för varje steg. Det finns också ett avsnitt för trafik scenariot att ge en djupgående steg för steg hur trafik fortsätter genom försvarslinjer i Perimeternätverket. Slutligen är avsnittet i referenserna den fullständiga koden och anvisningarna för att skapa den här miljön för att testa och experimentera med olika scenarier. 

![Inkommande DMZ med NVA och NSG][1]

## <a name="environment-description"></a>Miljö-beskrivning
I det här exemplet finns det en prenumeration som innehåller följande:

* Två molntjänster: ”FrontEnd001” och ”BackEnd001”
* Ett virtuellt nätverk ”CorpNetwork”, med två undernät: ”FrontEnd” och ”serverdel”
* En enkel Nätverkssäkerhetsgrupp som tillämpas på båda undernäten
* En virtuell nätverksinstallation, i det här exemplet en Barracuda NextGen-brandväggen, som är anslutna till undernätet på klientsidan
* En Windows-Server som representerar en program-webbserver (”IIS01”)
* Två windows-servrar som representerar programmet tillbaka sluta servrar (”AppVM01”, ”AppVM02”)
* En Windows-server som representerar en DNS-server (”DNS01”)

> [!NOTE]
> Även om det här exemplet används en Barracuda NextGen Firewall, kan många olika virtuella nätverksenheter användas i det här exemplet.
> 
> 

I referensavsnittet nedan finns ett PowerShell-skript som skapar de flesta av miljön som beskrivs ovan. Att skapa virtuella datorer och virtuella nätverk, även om utförs av exempelskript, som inte beskrivs i detalj i det här dokumentet.

Att skapa miljön:

1. Spara nätverk XML-konfigurationsfilen i referensavsnittet (uppdateras med namn, plats och IP-adresser för att matcha det aktuella scenariot)
2. Uppdatera Användarvariabler i skriptet så att den matchar den miljö som skriptet ska köras mot (prenumerationer, tjänstnamn osv.)
3. Kör skriptet i PowerShell

**Obs!** Den region som visas i PowerShell-skriptet måste matcha den region som visas i nätverket XML-konfigurationsfilen.

När skriptet har körts kan följande efterskript steg vidtas:

1. Konfigurera brandväggsreglerna beskrivs detta i avsnittet nedan: Brandväggsregler.
2. Du kan också är i referensavsnittet två skript för att konfigurera webbserver- och app-servern med en enkel webbapp för att testa med den här perimeternätverkskonfigurationen.

Nästa avsnitt beskrivs de flesta av skript-instruktioner i förhållande till Nätverkssäkerhetsgrupper.

## <a name="network-security-groups-nsg"></a>Nätverkssäkerhetsgrupper (NSG)
I det här exemplet bygger en NSG-grupp och sedan läsa in med sex regler. 

> [!TIP]
> Generellt sett bör du skapa dina specifika regler för ”Tillåt” först och sedan senaste mer allmänt ”Deny” reglerna. De tilldelade prioritet avgör vilka regler är utvärderas först. När trafik identifieras som gäller för en specifik regel, utvärderas inga fler regler. NSG-regler kan använda antingen i inkommande eller utgående riktning (ur undernätet).
> 
> 

Deklarativt, byggs följande regler för inkommande trafik:

1. Interna DNS-trafik (port 53) tillåts
2. RDP-trafik (port 3389) från Internet för alla virtuella datorer är tillåtet
3. HTTP-trafik (port 80) från Internet till NVA (brandvägg) tillåts
4. All trafik (alla portar) från IIS01 till AppVM1 tillåts
5. All trafik (alla portar) från Internet till hela VNet (både undernät) nekas
6. All trafik (alla portar) från undernätet på klientsidan till Backend-undernät nekas

Med de här reglerna bunden till varje undernät, om en HTTP-begäran var inkommande från Internet till webbservern, både regler 3 (Tillåt) och 5 (neka) är skulle tillämpas, men eftersom regel 3 har högre prioritet bara den skulle tillämpas och regel 5 skulle inte har betydelse. HTTP-begäran skulle därför tillåts i brandväggen. Om samma trafiken försökte nå DNS01 servern, regel 5 (neka) skulle vara först med att tillämpa och trafiken kan inte skickas till servern. Regel 6 (neka) blockerar undernätet på klientsidan för att tala med Backend-undernät (förutom tillåten trafik i reglerna 1 och 4), detta skyddar Backend-nätverket om en angripare kompromisser webbprogrammet på klientdelen angriparen skulle ha begränsad åtkomst till Serverdelen ”skyddad” nätverk (endast för resurser som visas på AppVM01 servern).

Det finns en utgående standardregel som tillåter trafik ut till internet. I det här exemplet vi tillåter utgående trafik och ändrar inte någon utgående regler. Att låsa trafik i båda riktningarna, användaren definierats routning krävs, detta är utforskat i ett annat exempel som finns i den [huvudsakliga security gräns dokumentet][HOME].

Ovanstående vi nämnt NSG-reglerna är mycket lika NSG-reglerna i [exempel 1 – skapa en enkel DMZ med NSG: er][Example1]. Läs igenom NSG beskrivningen i detta dokument för detaljerad information om varje NSG-regel och dess attribut.

## <a name="firewall-rules"></a>Brandväggsregler
En management-klienten måste installeras på en dator som kan hantera brandväggen och skapa de konfigurationer som krävs. Se dokumentationen från din brandvägg (eller andra NVA)-leverantören om hur du hanterar enheten. Resten av det här avsnittet beskriver konfigurationen av brandväggen, via leverantörer management-klienten (dvs. inte Azure-portalen eller PowerShell).

Instruktioner för att klienten ska ladda ned och ansluter till Barracuda som används i det här exemplet finns här: [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

I brandväggen måste vidarebefordra regler skapas. Eftersom det här exemplet skickar endast internet-trafik inkommande i brandväggen och sedan till webbservern, krävs bara en vidarebefordran NAT-regel. Barracuda NextGen-brandväggen som används i det här exemplet är regeln en mål-NAT-regel (”Dst NAT”) för att skicka den här trafiken.

Från och med Barracuda NG Admin-klienten instrumentpanelen, gå till konfigurationsfliken för att skapa följande regel (eller verifiera befintliga standardregler), i de användningsinställningar avsnittet klickar du på RuleSet-metod. Ett rutnät som kallas ”Main regler” visar befintliga aktiva och inaktiva regler i brandväggen. I det övre högra hörnet av det här rutnätet är en liten, grön ”+” knappen, klicka här för att skapa en ny regel (Obs: brandväggen kan vara ”låst” för ändringar, om du ser en knapp markeras som ”låsa” och du kan inte skapa eller redigera regler, klicka på knappen för att ”låsa upp” regeluppsättning och Tillåt redigering). Om du vill redigera en befintlig regel, Välj regeln, högerklickar och väljer Redigera regel.

Skapa en ny regel och ange ett namn, till exempel ”WebTraffic”. 

Ikonen för mål NAT-regel som ser ut så här: ![Mål NAT-ikon][2]

Själva regeln skulle se ut ungefär så här:

![Brandväggsregel][3]

Här några inkommande adress som kommer till brandväggen försöker nå HTTP (port 80 eller 443 för HTTPS) skickas ut i brandväggen ”DHCP1 lokala IP”-gränssnittet och omdirigeras till webbservern med IP-adressen för 10.0.1.5. Eftersom trafiken komma in på port 80 och gå till webbservern på port 80 behövdes ingen portändring. Dock kunde mållistan har 10.0.1.5:8080 om våra webbservern lyssnade på port 8080 därför översättning av den inkommande porten 80 i brandväggen till inkommande port 8080 på webbservern.

Anslutningsmetod bör också vara att visas, för Målregel från Internet, ”dynamisk SNAT” är mest lämplig. 

Även om endast en regeln har skapats är det viktigt att dess prioritet är korrekt. Om i rutnätet med alla regler i brandväggen är den nya regeln längst ned (under regeln ”BLOCKALL”) kommer den aldrig betydelse. Se till att den nyligen skapade regeln för webbtrafik är över BLOCKALL regeln.

När regeln har skapats, om det måste flyttas till brandväggen och därefter aktiverar du, om detta inte görs regeln ändringen börjar gälla. Push-meddelanden och aktivering processen beskrivs i nästa avsnitt.

## <a name="rule-activation"></a>Regel för aktivering
Med RuleSet-metod som ändras för att lägga till den här regeln, måste ruleset överförs till brandväggen och aktiverat.

![Brandväggen regeln aktivering][4]

I det övre högra hörnet av management-klienten finns i ett kluster med knappar. Klicka på ”Skicka ändringar” för att skicka ändrade regler i brandväggen och sedan på knappen ”Aktivera”.

Det här exemplet miljö-versionen har slutförts med aktivering brandväggen RuleSet-metod. Du kan också publicera build skript i avsnittet referenser kan köras för att lägga till ett program till den här miljön för att testa den nedan trafik scenarier.

> [!IMPORTANT]
> Det är viktigt att tänka på att du inte överskrids webbservern direkt. När en webbläsare begär ett HTTP-sidan från FrontEnd001.CloudApp.Net, skickar HTTP-slutpunkt (port 80) trafiken till brandväggen inte webbservern. Brandväggen sedan enligt regeln skapades ovan, NAT-enheter som begär till webbservern.
> 
> 

## <a name="traffic-scenarios"></a>Trafik-scenarier
#### <a name="allowed-web-to-web-server-through-firewall"></a>(Tillåts) Web till en webbserver som du via brandvägg
1. Internet-begäranden HTTP användarsidan från FrontEnd001.CloudApp.Net (Internet som riktas mot Cloud Services)
2. Cloud service skickar trafik via öppna slutpunkter på port 80 till brandväggen lokala gränssnittet på 10.0.1.4:80
3. Frontend-undernätet börjar bearbetning av inkommande regel:
   1. 1 för NSG-regel (DNS) inte tillämpa, gå till nästa regel
   2. NSG regel 2 (RDP) inte tillämpa, gå till nästa regel
   3. NSG-regel 3 (Internet-brandväggen) gäller, trafik är tillåtna, stoppa regelbehandling
4. Trafiken kommer till den interna IP-adress för brandvägg (is 10.0.1.4)
5. Brandväggsregel för IP-vidarebefordran finns i det här är trafik på port 80, omdirigerar till webbservern IIS01
6. IIS01 lyssnar för webbtrafik, får den här förfrågan och startar bearbetning av begäran
7. IIS01 begär SQL Server på AppVM01 information
8. Inga utgående regler på Frontend-undernätet trafik tillåts
9. Backend-undernät börjar bearbetning av inkommande regel:
   1. 1 för NSG-regel (DNS) inte tillämpa, gå till nästa regel
   2. NSG regel 2 (RDP) inte tillämpa, gå till nästa regel
   3. NSG-regel 3 (Internet-brandväggen) inte tillämpa, gå till nästa regel
   4. NSG-regel 4 (IIS01 till AppVM01) gäller, trafik tillåts, stoppa regelbehandling
10. AppVM01 tar emot en SQL-fråga och svarar
11. Eftersom det finns inga utgående regler på Backend-undernät tillåts svaret
12. Frontend-undernätet börjar bearbetning av inkommande regel:
    1. Det finns ingen NSG-regel som gäller för inkommande trafik från Backend-undernät för Frontend-undernätet, så att ingen av NSG-regler tillämpas
    2. System Standardregeln som tillåter trafik mellan undernät för att den här trafiken så att trafiken tillåts.
13. IIS-servern tar emot SQL-svar och slutför HTTP-svar och skickar till begäranden
14. Eftersom detta är en NAT-session från brandväggen är svar målet (inledningsvis) för brandväggen
15. Brandväggen tar emot svaret från servern och vidarebefordrar tillbaka till Internet-användare
16. Eftersom det finns inga regler för utgående trafik på undernätet på klientsidan svaret tillåts och Internet-användare får den webbsida som begärdes.

#### <a name="allowed-rdp-to-backend"></a>(Tillåts) RDP till serverdelen
1. Serveradministratören på internet begär RDP-session till AppVM01 på BackEnd001.CloudApp.Net:xxxxx där xxxxx är antalet slumpmässigt tilldelad port för RDP till AppVM01 (tilldelad port finns på Azure Portal eller via PowerShell)
2. Eftersom brandväggen lyssnar endast på FrontEnd001.CloudApp.Net-adress måste ingår den inte med det här flödet i nätverkstrafiken
3. Backend-undernät börjar bearbetning av inkommande regel:
   1. 1 för NSG-regel (DNS) inte tillämpa, gå till nästa regel
   2. NSG regel 2 (RDP) gäller, trafik är tillåtna, stoppa regelbehandling
4. Med inga utgående regler gäller för standard och återvändande trafik tillåts
5. RDP-session är aktiverat
6. AppVM01 frågar efter användarnamn lösenord

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Tillåts) Web Server DNS-sökning på DNS-server
1. Web Server, IIS01, måste en data-feed på www.data.gov, men måste matcha adressen.
2. Nätverkskonfigurationen för VNet-listor DNS01 (10.0.2.4 på Backend-undernät) som den primära DNS-servern, IIS01 skickar en DNS-begäran till DNS01
3. Inga utgående regler på Frontend-undernätet trafik tillåts
4. Backend-undernät börjar bearbetning av inkommande regel:
   1. 1 för NSG-regel (DNS) gäller, trafik är tillåtna, stoppa regelbehandling
5. DNS-servern tar emot begäran
6. DNS-servern har inte den adress som cachelagras och frågar en rot-DNS-server på internet
7. Inga utgående regler på Backend-undernät tillåts trafik
8. Internet-DNS-servern svarar, eftersom den här sessionen initierades internt, tillåts svaret
9. DNS-servern cachelagrar svaret och svarar på den första begäran tillbaka till IIS01
10. Inga utgående regler på Backend-undernät tillåts trafik
11. Frontend-undernätet börjar bearbetning av inkommande regel:
    1. Det finns ingen NSG-regel som gäller för inkommande trafik från Backend-undernät för Frontend-undernätet, så att ingen av NSG-regler tillämpas
    2. System Standardregeln som tillåter trafik mellan undernät skulle tillåta den här trafiken så att trafiken tillåts
12. IIS01 tar emot svaret från DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Tillåts) Web Server access-fil på AppVM01
1. IIS01 begär en fil på AppVM01
2. Inga utgående regler på Frontend-undernätet trafik tillåts
3. Backend-undernät börjar bearbetning av inkommande regel:
   1. 1 för NSG-regel (DNS) inte tillämpa, gå till nästa regel
   2. NSG regel 2 (RDP) inte tillämpa, gå till nästa regel
   3. NSG-regel 3 (Internet-brandväggen) inte tillämpa, gå till nästa regel
   4. NSG-regel 4 (IIS01 till AppVM01) gäller, trafik tillåts, stoppa regelbehandling
4. AppVM01 tar emot begäran och svarar med (förutsatt att du har behörighet)
5. Eftersom det finns inga utgående regler på Backend-undernät tillåts svaret
6. Frontend-undernätet börjar bearbetning av inkommande regel:
   1. Det finns ingen NSG-regel som gäller för inkommande trafik från Backend-undernät för Frontend-undernätet, så att ingen av NSG-regler tillämpas
   2. System Standardregeln som tillåter trafik mellan undernät för att den här trafiken så att trafiken tillåts.
7. IIS-servern tar emot filen

#### <a name="denied-web-direct-to-web-server"></a>(Nekad) Web-direct till webbservern
Eftersom webbservern, IIS01 och brandväggen finns i samma molntjänst delar den samma offentliga IP-adressen. Därför skulle alla HTTP-trafik dirigeras till brandväggen. Begäran skulle vara har behandlats, det kan inte gå direkt till webbservern, men det skickades utformning genom brandväggen först. Se det första scenariot i det här avsnittet för trafikflödet.

#### <a name="denied-web-to-backend-server"></a>(Nekad) Webben till Backend-servern
1. Internet-användare försöker få åtkomst till en fil på AppVM01 via tjänsten BackEnd001.CloudApp.Net
2. Eftersom det finns inga slutpunkter som är öppna för filresursen är detta skickar inte Molntjänsten och skulle nå servern
3. Om du slutpunkterna öppna av någon anledning skulle NSG-regel 5 (Internet till VNet) blockera den här trafiken

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Nekad) Web DNS-sökning på DNS-server
1. Internet-användare försöker att söka efter en intern DNS-post på DNS01 via tjänsten BackEnd001.CloudApp.Net
2. Eftersom det finns inga slutpunkter som är öppen för DNS är detta skickar inte Molntjänsten och skulle nå servern
3. Om du slutpunkterna öppna av någon anledning NSG-regel 5 (Internet till VNet) skulle blockera den här trafiken (Obs: den regel 1 (DNS) inte tillämpas av två skäl, först källadressen är internet, den här regeln gäller endast för det lokala virtuella nätverket som källa även detta är en Tillåt-regel så att det skulle aldrig nekar trafik)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Nekad) Webben till SQL-åtkomst via brandväggen
1. Internet-användare begär SQL-data från FrontEnd001.CloudApp.Net (Internet som riktas mot Cloud Services)
2. Eftersom det finns inga slutpunkter som är öppen för SQL är detta skickar inte Molntjänsten och skulle nå brandväggen
3. Om du slutpunkter öppna av någon anledning börjar med Frontend-undernätet bearbetning av inkommande regel:
   1. 1 för NSG-regel (DNS) inte tillämpa, gå till nästa regel
   2. NSG regel 2 (RDP) inte tillämpa, gå till nästa regel
   3. NSG-regel 2 (Internet-brandväggen) gäller, trafik är tillåtna, stoppa regelbehandling
4. Trafiken kommer till den interna IP-adress för brandvägg (is 10.0.1.4)
5. Brandväggen har inga vidarebefordringsregler för SQL och släpper trafiken

## <a name="conclusion"></a>Sammanfattning
Det här är ett relativt okomplicerat sätt skyddar ditt program med en brandvägg och isolera backend-undernät från inkommande trafik.

Fler exempel och en översikt över nätverket säkerhetsgränser finns [här][HOME].

## <a name="references"></a>Referenser
### <a name="main-script-and-network-config"></a>Huvudskriptet och konfigurationer för nätverk
Spara fullständigt skript i en PowerShell-skriptfilen. Spara konfiguration för nätverk i en fil med namnet ”NetworkConf2.xml”.
Ändra variablerna användardefinierade efter behov. Kör skriptet och följ brandväggen regeln installationsprogrammet anvisningarna ovan.

#### <a name="full-script"></a>Fullständigt skript
Det här skriptet kommer baserat på användardefinierade variabler:

1. Ansluta till en Azure-prenumeration
2. Skapa ett nytt lagringskonto
3. Skapa ett nytt VNet och två undernät som definierats i konfigurationsfilen för nätverk
4. Skapa 4 windows server-datorer
5. Konfigurera NSG, inklusive:
   * Skapa en Nätverkssäkerhetsgrupp
   * Fylla det med regler
   * Bindning NSG: N till lämpliga undernät

Det här PowerShell-skriptet ska köras lokalt på en internet-ansluten dator eller server.

> [!IMPORTANT]
> När skriptet har körts kanske varningar eller andra informationsmeddelanden som pop i PowerShell. Endast felmeddelanden i rött är orsaka problem.
> 
> 

```powershell
    <# 
     .SYNOPSIS
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
       - Three Servers on the BackEnd Subnet
       - Network Security Groups to allow/deny traffic patterns as declared

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in a
      myriad of ways. Please be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that can be used, but should not be used for all scenarios. You
      are responsible to assess your security needs and the appropriate protections
      needed, and then effectively implement those protections.

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

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

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

    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
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
    # No User Defined Variables or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
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

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
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
                # Set up all the EndPoints we'll need once we're up and running
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
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

      # Add NSG Rules
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

        # Assign the NSG to the Subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
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
Spara den här xml-filen med uppdaterade plats och lägga till länken till den här filen till variabeln $NetworkConfigFile i skriptet ovan.

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
Om du vill installera ett exempelprogram för detta och andra DMZ-exempel finns en på följande länk: [Exempelskript för program][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Inkommande perimeternätverk med NSG"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Mål NAT-ikon"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Brandväggsregel"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Brandväggen regeln aktivering"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
