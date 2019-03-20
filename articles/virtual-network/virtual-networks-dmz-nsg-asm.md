---
title: 'Exempel på Azure DMZ – skapa en enkel DMZ med NSG: er | Microsoft Docs'
description: Skapa ett perimeternätverrk med Nätverkssäkerhetsgrupper (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: f8622b1d-c07d-4ea6-b41c-4ae98d998fff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 115a459c6a9e4ea96931c89272a49396f0656258
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993347"
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-classic-powershell"></a>Exempel 1 – skapa en enkel DMZ med NSG: er med klassisk PowerShell
[Gå tillbaka till gränsen bästa praxis sidan][HOME]

> [!div class="op_single_selector"]
> * [Resource Manager-mall](virtual-networks-dmz-nsg.md)
> * [Klassisk – PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Det här exemplet skapar en primitiv DMZ med fyra Windows-servrar och Nätverkssäkerhetsgrupper. Det här exemplet beskrivs relevanta PowerShell-kommandon för att ge en bättre förståelse för varje steg. Det finns också ett avsnitt för trafik scenariot att ge en djupgående steg för steg hur trafik fortsätter genom försvarslinjer i Perimeternätverket. Slutligen är avsnittet i referenserna den fullständiga koden och anvisningarna för att skapa den här miljön för att testa och experimentera med olika scenarier. 

![Inkommande perimeternätverk med NSG][1]

## <a name="environment-description"></a>Miljö-beskrivning
I det här exemplet innehåller en prenumeration i följande resurser:

* Två molntjänster: ”FrontEnd001” och ”BackEnd001”
* Ett virtuellt nätverk ”CorpNetwork”, med två undernät; ”FrontEnd” och ”serverdel”
* En Nätverkssäkerhetsgrupp som tillämpas på båda undernäten
* En Windows-Server som representerar en program-webbserver (”IIS01”)
* Två windows-servrar som representerar programmet backend-servrar (”AppVM01”, ”AppVM02”)
* En Windows-server som representerar en DNS-server (”DNS01”)

Det finns ett PowerShell-skript som bygger på de flesta av miljön som beskrivs i det här exemplet i referensavsnittet. Att skapa virtuella datorer och virtuella nätverk, även om utförs av exempelskript, som inte beskrivs i detalj i det här dokumentet. 

Att skapa miljön.

1. Spara nätverk XML-konfigurationsfilen i referensavsnittet (uppdateras med namn, plats och IP-adresser för att matcha det aktuella scenariot)
2. Uppdatera Användarvariabler i skriptet så att den matchar den miljö som skriptet ska köras mot (prenumerationer, tjänstnamn osv.)
3. Kör skriptet i PowerShell

>[!Note]
>Den region som visas i PowerShell-skriptet måste matcha den region som visas i nätverket XML-konfigurationsfilen.
>
>

När skriptet har körts har ytterligare valfria steg vidtas finns i referensavsnittet två skript för att konfigurera webbservern och applikationsserver med en enkel webbapp för att testa med den här DMZ-konfigurationen.

Följande avsnitt innehåller en detaljerad beskrivning av Nätverkssäkerhetsgrupper och hur de fungerar i det här exemplet genom att gå igenom viktiga rader med PowerShell-skriptet.

## <a name="network-security-groups-nsg"></a>Nätverkssäkerhetsgrupper (NSG)
I det här exemplet bygger en NSG-grupp och sedan läsa in med sex regler. 

> [!TIP]
> Generellt sett bör du skapa dina specifika regler för ”Tillåt” först och sedan senaste mer allmänt ”Deny” reglerna. De tilldelade prioritet avgör vilka regler är utvärderas först. När trafik identifieras som gäller för en specifik regel, utvärderas inga fler regler. NSG-regler kan använda antingen i inkommande eller utgående riktning (ur undernätet).
> 
> 

Deklarativt, byggs följande regler för inkommande trafik:

1. Interna DNS-trafik (port 53) tillåts
2. RDP-trafik (port 3389) från Internet för alla virtuella datorer är tillåtet
3. HTTP-trafik (port 80) från Internet till webbservern (IIS01) tillåts
4. All trafik (alla portar) från IIS01 till AppVM1 tillåts
5. All trafik (alla portar) från Internet till hela VNet (både undernät) nekas
6. All trafik (alla portar) från undernätet på klientsidan till Backend-undernät nekas

Med de här reglerna bunden till varje undernät, om en HTTP-begäran var inkommande från Internet till webbservern, både regler 3 (Tillåt) och 5 (neka) är skulle tillämpas, men eftersom regel 3 har högre prioritet bara den skulle tillämpas och regel 5 skulle inte har betydelse. HTTP-begäran skulle därför tillåts till webbservern. Om samma trafiken försökte nå DNS01 servern, regel 5 (neka) skulle vara först med att tillämpa och trafiken kan inte skickas till servern. Regel 6 (neka) blockerar undernätet på klientsidan för att tala med Backend-undernät (förutom tillåten trafik i reglerna 1 och 4), den här regeluppsättningen skyddar Backend-nätverket om en angripare kompromisser webbprogrammet på klientdelen angriparen skulle har begränsad åtkomst till serverdelen ”skyddad” nätverk (endast för resurser som visas på AppVM01 servern).

Det finns en utgående standardregel som tillåter trafik ut till internet. I det här exemplet vi tillåter utgående trafik och ändrar inte någon utgående regler. Om du vill låsa trafik i båda riktningarna, användaren definierats routning krävs och är utforskat ”exempel 3” på den [gräns bästa praxis säkerhetssidan][HOME].

Varje regel beskrivs i detalj enligt följande (**Obs**: ett objekt i den följande listan som början med ett dollartecken (till exempel: $NSGName) är en användardefinierad variabel från skriptet i referensavsnittet i det här dokumentet):

1. Först måste en Nätverkssäkerhetsgrupp skapas för att lagra reglerna:

    ```PowerShell
    New-AzureNetworkSecurityGroup -Name $NSGName `
        -Location $DeploymentLocation `
        -Label "Security group for $VNetName subnets in $DeploymentLocation"
    ```

2. Den första regeln i det här exemplet kan DNS-trafik mellan alla interna nätverk till DNS-servern på backend-undernät. Regeln har vissa viktiga parametrar:
   
   * ”Type” innebär det att gälla i den här regeln i vilken riktning av flödet i nätverkstrafiken. Riktningen är ur undernät eller virtuella datorn (beroende på var den här NSG binds). Därför om typen är ”Inbound” och trafiken som kommer in i undernätet, regeln skulle tillämpas och trafik som lämnar undernätet inte påverkas av den här regeln.
   * ”Prioritet” Anger att där ett trafikflöde utvärderas. Ju lägre det nummer desto högre prioritet. När en regel som gäller för en specifik trafikflödet, bearbetas inga ytterligare regler. Därför om en regel med prioritet 1 tillåter trafik, och en regel med prioritet 2 nekar trafik, och båda regler gäller vid trafik så trafiken kan flöda (eftersom regel 1 har högre prioritet det tog att gälla och inga ytterligare regler tillämpades).
   * ”Action” avser om trafik som påverkas av den här regeln är blockerad eller tillåten.

     ```PowerShell    
     Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
        -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[4] `
        -DestinationPortRange '53' `
        -Protocol *
     ```

3. Den här regeln tillåter RDP-trafik kan flöda från internet till RDP-porten på alla servrar i det bundna undernätet. Den här regeln använder två särskilda typer av adressprefix; ”VIRTUAL_NETWORK” och ”INTERNET”. Dessa taggar är ett enkelt sätt att åtgärda en större kategori av adressprefix.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
         -Type Inbound -Priority 110 -Action Allow `
         -SourceAddressPrefix INTERNET -SourcePortRange '*' `
         -DestinationAddressPrefix VIRTUAL_NETWORK `
         -DestinationPortRange '3389' `
         -Protocol *
    ```

4. Den här regeln tillåter inkommande Internettrafik till trycker på webbservern. Den här regeln ändrar inte beteendet routning. Regeln tillåter endast trafik till IIS01 att skicka. Därför om trafik från Internet hade webbservern som mål den här regeln skulle tillåta att den och stoppa bearbetningen ytterligare regler. (I regeln med prioritet 140 alla andra inkommande internet-trafiken blockeras). Om du endast bearbetar HTTP-trafik, kan den här regeln begränsas ytterligare så att bara mål Port 80.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
         -Type Inbound -Priority 120 -Action Allow `
         -SourceAddressPrefix Internet -SourcePortRange '*' `
         -DestinationAddressPrefix $VMIP[0] `
         -DestinationPortRange '*' `
         -Protocol *
    ```

5. Den här regeln tillåter trafik skickas från IIS01 servern till AppVM01-servern, en senare regeln blockerar alla andra klientdel för Backend-trafik. Att förbättra den här regeln om porten är känt som ska läggas till. Till exempel IIS-servern når endast SQL Server på AppVM01, kan portintervallet för målet ska ändras från ”*” (valfritt) till 1433 (SQL-port), vilket medför att ett mindre inkommande risken för angrepp på AppVM01 webbprogrammet någonsin behövs.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
        -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
    ```

6. Den här regeln nekar trafik från internet till alla servrar i nätverket. Med reglerna med prioritet 110 och 120 är effekten att endast inkommande Internettrafik till brandväggen och RDP-portar på servrar och blockerar allt annat. Den här regeln är en ”felsäker” regel för att blockera alla oväntat flöden.
    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $VNetName VNet from the Internet" `
        -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *
    ```
7. Sista regeln nekar trafik från undernätet på klientsidan till Backend-undernät. Eftersom den här regeln är en inkommande regel som endast är tillåts omvänd trafik (från serverdelen för klientdelen).

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
        -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix `
        -DestinationPortRange '*' `
        -Protocol * 
    ```

## <a name="traffic-scenarios"></a>Trafik-scenarier
#### <a name="allowed-internet-to-web-server"></a>(*Tillåtna*) Internet till webbservern
1. En internet-användare begär en HTTP-sida från FrontEnd001.CloudApp.Net (Internet som riktas mot Cloud Services)
2. Cloud service skickar trafik via öppna slutpunkter på port 80 för IIS01 (webbserver)
3. Frontend-undernätet börjar bearbetning av inkommande regel:
   1. 1 för NSG-regel (DNS) inte tillämpa, gå till nästa regel
   2. NSG regel 2 (RDP) inte tillämpa, gå till nästa regel
   3. NSG-regel 3 (Internet till IIS01) gäller, trafik är tillåtna, stoppa regelbehandling
4. Trafiken kommer till den interna IP-adressen för webbservern IIS01 (10.0.1.5)
5. IIS01 lyssnar för webbtrafik, får den här förfrågan och startar bearbetning av begäran
6. IIS01 begär SQL Server på AppVM01 information
7. Eftersom det finns inga regler för utgående trafik på Frontend-undernätet, tillåts trafik
8. Backend-undernät börjar bearbetning av inkommande regel:
   1. 1 för NSG-regel (DNS) inte tillämpa, gå till nästa regel
   2. NSG regel 2 (RDP) inte tillämpa, gå till nästa regel
   3. NSG-regel 3 (Internet-brandväggen) inte tillämpa, gå till nästa regel
   4. NSG-regel 4 (IIS01 till AppVM01) gäller, trafik tillåts, stoppa regelbehandling
9. AppVM01 tar emot en SQL-fråga och svarar
10. Eftersom det finns inga regler för utgående trafik på Backend-undernät, tillåts svaret
11. Frontend-undernätet börjar bearbetning av inkommande regel:
    1. Det finns ingen NSG-regel som gäller för inkommande trafik från Backend-undernät för Frontend-undernätet, så att ingen av NSG-regler tillämpas
    2. System Standardregeln som tillåter trafik mellan undernät för att den här trafiken så att trafiken tillåts.
12. IIS-servern tar emot SQL-svar och slutför HTTP-svar och skickar till begäranden
13. Eftersom det finns inga utgående regler på undernätet på klientsidan svaret tillåts och internet-användare får sidan begärs.

#### <a name="allowed-rdp-to-backend"></a>(*Tillåtna*) RDP till serverdelen
1. Serveradministratören på internet begär RDP-session till AppVM01 på BackEnd001.CloudApp.Net:xxxxx där xxxxx är antalet slumpmässigt tilldelad port för RDP till AppVM01 (tilldelad port finns på Azure portal eller via PowerShell)
2. Backend-undernät börjar bearbetning av inkommande regel:
   1. 1 för NSG-regel (DNS) inte tillämpa, gå till nästa regel
   2. NSG regel 2 (RDP) gäller, trafik är tillåtna, stoppa regelbehandling
3. Med inga utgående regler gäller för standard och återvändande trafik tillåts
4. RDP-session är aktiverat
5. AppVM01 måste ange användarnamn och lösenord

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Tillåtna*) Web server DNS-sökningen på DNS-server
1. Web Server, IIS01, måste en data-feed på www.data.gov, men måste matcha adressen.
2. Nätverkskonfigurationen för VNet-listor DNS01 (10.0.2.4 på Backend-undernät) som den primära DNS-servern, IIS01 skickar en DNS-begäran till DNS01
3. Inga utgående regler på Frontend-undernätet trafik tillåts
4. Backend-undernät börjar bearbetning av inkommande regel:
   * 1 för NSG-regel (DNS) gäller, trafik är tillåtna, stoppa regelbehandling
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

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Tillåtna*) Web server access-fil på AppVM01
1. IIS01 begär en fil på AppVM01
2. Inga utgående regler på Frontend-undernätet trafik tillåts
3. Backend-undernät börjar bearbetning av inkommande regel:
   1. 1 för NSG-regel (DNS) inte tillämpa, gå till nästa regel
   2. NSG regel 2 (RDP) inte tillämpa, gå till nästa regel
   3. NSG-regel 3 (Internet till IIS01) inte tillämpa, gå till nästa regel
   4. NSG-regel 4 (IIS01 till AppVM01) gäller, trafik tillåts, stoppa regelbehandling
4. AppVM01 tar emot begäran och svarar med (förutsatt att du har behörighet)
5. Eftersom det finns inga regler för utgående trafik på Backend-undernät, tillåts svaret
6. Frontend-undernätet börjar bearbetning av inkommande regel:
   1. Det finns ingen NSG-regel som gäller för inkommande trafik från Backend-undernät för Frontend-undernätet, så att ingen av NSG-regler tillämpas
   2. System Standardregeln som tillåter trafik mellan undernät för att den här trafiken så att trafiken tillåts.
7. IIS-servern tar emot filen

#### <a name="denied-web-to-backend-server"></a>(*Nekad*) webben till backend-servern
1. En internet-användare försöker få åtkomst till en fil på AppVM01 via tjänsten BackEnd001.CloudApp.Net
2. Eftersom det finns inga slutpunkter som är öppna för filresursen är den här trafiken skickar inte Molntjänsten och skulle nå servern
3. Om du slutpunkterna öppna av någon anledning skulle NSG-regel 5 (Internet till VNet) blockera den här trafiken

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Nekad*) Web DNS-sökningen på DNS-server
1. En internet-användare försöker att leta upp en intern DNS-post på DNS01 via tjänsten BackEnd001.CloudApp.Net
2. Eftersom det finns inga slutpunkter som är öppen för DNS är den här trafiken skickar inte Molntjänsten och skulle nå servern
3. Om du slutpunkterna öppna av någon anledning NSG-regel 5 (Internet till VNet) skulle blockera den här trafiken (Obs: den regel 1 (DNS) inte tillämpas av två skäl, först källadressen är internet, den här regeln gäller endast för det lokala virtuella nätverket som källa den här regeln är också en Tillåt-regel så att det skulle aldrig nekar trafik)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Nekad*) webben till SQL-åtkomst via brandväggen
1. En internet-användare begär SQL-data från FrontEnd001.CloudApp.Net (Internet som riktas mot Cloud Services)
2. Eftersom det finns inga slutpunkter som är öppen för SQL är den här trafiken skickar inte Molntjänsten och skulle nå brandväggen
3. Om du slutpunkter öppna av någon anledning börjar med Frontend-undernätet bearbetning av inkommande regel:
   1. 1 för NSG-regel (DNS) inte tillämpa, gå till nästa regel
   2. NSG regel 2 (RDP) inte tillämpa, gå till nästa regel
   3. NSG-regel 3 (Internet till IIS01) gäller, trafik är tillåtna, stoppa regelbehandling
4. Trafik når interna IP-adressen för IIS01 (10.0.1.5)
5. IIS01 lyssnar inte på port 1433, så inga svar på begäran

## <a name="conclusion"></a>Sammanfattning
Det här exemplet är ett relativt enkla och okomplicerat sätt att isolera serverdelsundernätet från inkommande trafik.

Fler exempel och en översikt över nätverket säkerhetsgränser finns [här][HOME].

## <a name="references"></a>Referenser
### <a name="main-script-and-network-config"></a>Viktigaste skript och nätverk-config
Spara fullständigt skript i en PowerShell-skriptfilen. Spara konfiguration för nätverk i en fil med namnet ”NetworkConf1.xml”.
Ändra de användardefinierade variablerna vid behov och kör skriptet.

#### <a name="full-script"></a>Fullständigt skript
Det här skriptet kommer, baserat på användardefinierade variabler.

1. Ansluta till en Azure-prenumeration
2. skapar ett lagringskonto
3. Skapa ett VNet och två undernät som definierats i konfigurationsfilen för nätverk
4. Skapa fyra windows server-datorer
5. Konfigurera NSG, inklusive:
   * Skapa en Nätverkssäkerhetsgrupp
   * Fylla det med regler
   * Bindning NSG: N till lämpliga undernät

Det här PowerShell-skriptet ska köras lokalt på en internet-ansluten dator eller server.

> [!IMPORTANT]
> När skriptet har körts kanske varningar eller andra informationsmeddelanden som pop i PowerShell. Endast felmeddelanden i rött är orsaka problem.
> 
>

```PowerShell
<# 
 .SYNOPSIS
  Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)

 .DESCRIPTION
  This script will build out a sample DMZ setup containing:
   - A default storage account for VM disks
   - Two new cloud services
   - Two Subnets (FrontEnd and BackEnd subnets)
   - One server on the FrontEnd Subnet
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
    $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"

  # VM Base Disk Image Details
    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

  # NSG Details
    $NSGName = "MyVNetSG"

# User-Defined VM Specific Configuration
    # Note: To ensure proper NSG Rule creation later in this script:
    #       - The Web Server must be VM 0
    #       - The AppVM1 Server must be VM 1
    #       - The DNS server must be VM 3
    #
    #       Otherwise the NSG rules in the last section of this
    #       script will need to be changed to match the modified
    #       VM array numbers ($i) so the NSG Rule IP addresses
    #       are aligned to the associated VM IP addresses.

    # VM 0 - The Web Server
      $VMName += "IIS01"
      $ServiceName += $FrontEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $FESubnet
      $VMIP += "10.0.1.5"

    # VM 1 - The First Application Server
      $VMName += "AppVM01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.5"

    # VM 2 - The Second Application Server
      $VMName += "AppVM02"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.6"

    # VM 3 - The DNS Server
      $VMName += "DNS01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.4"

# ----------------------------- #
# No User-Defined Variables or  #
# Configuration past this point #
# ----------------------------- #    

  # Get your Azure accounts
    Add-AzureAccount
    Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
    Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

  # Create Storage Account
    If (Test-AzureName -Storage -Name $StorageAccountName) { 
        Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
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
Else { Write-Host "The network configuration file was found" -ForegroundColor Green
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
        New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
            Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
            Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
            Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
            Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
            Remove-AzureEndpoint -Name "PowerShell" | `
            New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
            # Note: A Remote Desktop endpoint is automatically created when each VM is created.
        $i++
    }
    # Add HTTP Endpoint for IIS01
    Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM

# Configure NSG
    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

  # Build the NSG
    Write-Host "Building the NSG" -ForegroundColor Cyan
    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

  # Add NSG Rules
    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
        -SourceAddressPrefix Internet -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
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
  # Install Test Web App (Run Post-Build Script on the IIS Server)
  # Install Backend resource (Run Post-Build Script on the AppVM01)
  Write-Host
  Write-Host "Build Complete!" -ForegroundColor Green
  Write-Host
  Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
  Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
  Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
  Write-Host
```

#### <a name="network-config-file"></a>Konfigurationsfilen för nätverk
Spara den här xml-filen med uppdaterade plats och lägga till länken till den här filen till variabeln $NetworkConfigFile i föregående skript.

```XML
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

## <a name="next-steps"></a>Nästa steg
* Uppdatera och spara XML-fil
* Kör PowerShell-skript för att skapa miljön
* Installera exempelprogrammet
* Testa olika trafikflöden via det här perimeternätverket

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "Inkommande perimeternätverk med NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

