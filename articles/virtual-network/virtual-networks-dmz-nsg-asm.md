---
title: 'Azure DMZ exempel – skapa en enkel DMZ med NSG: er | Microsoft Docs'
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
ms.openlocfilehash: ed172d552e1e4c9ee27c58abcd7ad2d98df21579
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
ms.locfileid: "23928890"
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-classic-powershell"></a>Exempel 1 – skapa en enkel DMZ NSG: er med klassiska PowerShell
[Gå tillbaka till gränsen bästa praxis säkerhetssidan][HOME]

> [!div class="op_single_selector"]
> * [Resource Manager-mall](virtual-networks-dmz-nsg.md)
> * [Klassisk - PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Det här exemplet skapar en primitiv DMZ med fyra Nätverkssäkerhetsgrupper och Windows-servrar. Detta exempel beskrivs var och en av de relevanta PowerShell-kommandona för att ge en bättre förståelse för varje steg. Det finns också ett trafik scenariot avsnitt för att ge en detaljerad steg för steg hur trafik fortsätter via lager i skyddsstrategierna i Perimeternätverket. Slutligen är avsnitt i hänvisning den fullständiga koden och anvisningarna för att skapa den här miljön för att testa och experimentera med olika scenarier. 

![Inkommande perimeternätverk med NSG][1]

## <a name="environment-description"></a>Beskrivning av miljö
I det här exemplet innehåller en prenumeration i följande resurser:

* Två molntjänster: ”FrontEnd001” och ”BackEnd001”
* Ett virtuellt nätverk ”CorpNetwork” med två undernät; ”FrontEnd” och ”BackEnd”
* En Nätverkssäkerhetsgrupp som tillämpas på båda undernäten
* En Windows-Server som representerar en program-webbserver (”IIS01”)
* Två windows-servrar som representerar backend-programservrar (”AppVM01”, ”AppVM02”)
* En Windows-server som representerar en DNS-server (”DNS01”)

Det finns ett PowerShell-skript som bygger mest i miljön som beskrivs i det här exemplet i referensavsnittet. Skapande av virtuella datorer och virtuella nätverk, även om den är klar med exempelskriptet som inte beskrivs i detalj i detta dokument. 

Att skapa miljön.

1. Spara nätverket XML-konfigurationsfilen finns i referensavsnittet (uppdaterade med namn, plats och IP-adresser för att matcha det aktuella scenariot)
2. Uppdatera Användarvariabler i skript för att matcha den miljö som skriptet ska köras mot (prenumerationer, tjänstnamn osv.)
3. Kör skriptet i PowerShell

>[!Note]
>Den region som visas i PowerShell-skriptet måste matcha den region som visas i nätverket XML-konfigurationsfilen.
>
>

När skriptet körs har ytterligare valfria steg vidtas finns i referensavsnittet två skript för att konfigurera webbserver- och app-servern med en enkel webbapp för att testa med den här DMZ-konfigurationen.

Följande avsnitt innehåller en detaljerad beskrivning av Nätverkssäkerhetsgrupper och hur de fungerar i det här exemplet genom att gå igenom viktiga rader med PowerShell-skriptet.

## <a name="network-security-groups-nsg"></a>Nätverkssäkerhetsgrupper (NSG)
I det här exemplet bygger en NSG-grupp och sedan in med sex regler. 

> [!TIP]
> Generellt sett bör du skapa specifika ”Tillåt” reglerna först och sedan de mer allmänna reglerna som ”Deny” sist. De tilldelade prioritet bestämmer vilka regler är utvärderas först. När du har hittat trafik ska gälla för en specifik regel utvärderas inga ytterligare regler. NSG-regler kan använda antingen i inkommande eller utgående riktning (ur undernätet).
> 
> 

Deklarativt, byggs följande regler för inkommande trafik:

1. Intern DNS-trafik (port 53) tillåts
2. RDP-trafik (port 3389) från Internet till någon virtuell dator är tillåtet
3. HTTP-trafik (port 80) från Internet till webbservern (IIS01) tillåts
4. All trafik (alla portar) från IIS01 till AppVM1 tillåts
5. All trafik (alla portar) från Internet till hela virtuella nätverk (båda undernäten) nekas
6. All trafik (alla portar) från undernätet som klientdel till Backend-undernät nekas

Med de här reglerna bunden till varje undernät, om en HTTP-begäran var inkommande trafik från Internet till webbservern, både regler 3 (Tillåt) och 5 (neka) är skulle ha använts, men eftersom regel 3 har högre prioritet bara den skulle tillämpas och regel 5 inte skulle komma till användning. HTTP-begäran skulle därför tillåtas till webbservern. Om samma trafiken försökte nå servern DNS01 regel 5 (neka) skulle vara först att tillämpa och trafiken kan inte skickas till servern. Regel 6 (neka) blockerar undernätet Frontend från kommunicerar med Backend-undernät (förutom tillåten trafik i regler 1 och 4), den här regeluppsättningen skyddar Backend-nätverket om en angripare kompromisser webbprogrammet på Frontend angriparen skulle har begränsad åtkomst till Backend ”skyddad” nätverket (endast för resurser som visas på AppVM01 servern).

Det finns en utgående Standardregeln som tillåter trafik ut till internet. I det här exemplet vi att tillåta utgående trafik och inte ändra de utgående reglerna. Om du vill låsa i båda riktningarna användaren definierat routning krävs och är utforskade ”exempel 3” på den [gräns bästa praxis sidan][HOME].

Varje regel är beskrivs i detalj (**Observera**: ett objekt i den följande listan som början med ett dollartecken (till exempel: $NSGName) är en användardefinierad variabel från skriptet i referensavsnittet i det här dokumentet):

1. En Nätverkssäkerhetsgrupp måste först skapas för att lagra reglerna:

    ```PowerShell
    New-AzureNetworkSecurityGroup -Name $NSGName `
        -Location $DeploymentLocation `
        -Label "Security group for $VNetName subnets in $DeploymentLocation"
    ```

2. Den första regeln i det här exemplet tillåter DNS-trafik mellan alla interna nätverk till DNS-servern på backend-undernät. Regeln har vissa viktiga parametrar:
   
   * ”Typ” betyder i vilken riktning för trafikflöde regeln träder i kraft. Riktningen är ur ett undernät eller virtuella datorn (beroende på om den här NSG binds). Därför om typen är ”inkommande” trafik kommer in undernätet, skulle regeln och trafik som lämnar undernätet inte påverkas av den här regeln.
   * ”Prioritet” Anger att ett trafikflöde utvärderas. Ju lägre det nummer desto högre prioritet. När en regel som gäller för en specifik trafikflödet, bearbetas inga ytterligare regler. Därför om en regel med prioritet 1 tillåter trafik, och en regel med prioritet 2 nekar trafik, och båda reglerna som gäller för trafik som sedan trafiken skulle kunna flöda (eftersom regel 1 har en högre prioritet det tog att gälla och inga ytterligare regler har tillämpats).
   * ”Åtgärden” innebär det att om trafik som påverkas av regeln blockeras eller tillåts.

    ```PowerShell    
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
        -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[4] `
        -DestinationPortRange '53' `
        -Protocol *
    ```

3. Den här regeln kan RDP-trafik ska flödas från internet till RDP-porten på alla servrar i det bundna undernätet. Den här regeln använder två särskilda typer av adressprefix; ”VIRTUAL_NETWORK” och ”INTERNET”. Dessa taggar är ett enkelt sätt att adressera en större kategori av adressprefix.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
         -Type Inbound -Priority 110 -Action Allow `
         -SourceAddressPrefix INTERNET -SourcePortRange '*' `
         -DestinationAddressPrefix VIRTUAL_NETWORK `
         -DestinationPortRange '3389' `
         -Protocol *
    ```

4. Den här regeln tillåter inkommande trafik för internet att träffa på webbservern. Den här regeln ändras inte dirigeringsbeteendet. Regeln kan bara trafik till IIS01 att skicka. Därför om trafik från Internet hade webbservern som leder den här regeln skulle göra det möjligt och stoppa bearbetningen ytterligare regler. (I regeln med prioritet 140 alla andra inkommande internet-trafiken blockeras). Om du bara bearbetning av HTTP-trafik, kan den här regeln begränsas ytterligare så att bara mål Port 80.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
         -Type Inbound -Priority 120 -Action Allow `
         -SourceAddressPrefix Internet -SourcePortRange '*' `
         -DestinationAddressPrefix $VMIP[0] `
         -DestinationPortRange '*' `
         -Protocol *
    ```

5. Den här regeln kan trafik skickas från servern IIS01 till AppVM01-server, en senare regeln block andra klientdel till Backend-trafik. Att förbättra den här regeln om porten är känd som ska läggas till. Till exempel om IIS-servern är träffa endast SQL Server på AppVM01, Målportintervallet ändras från ”*” (alla) till 1433 (SQL-port), vilket ger en mindre inkommande risken för angrepp på AppVM01 bör webbprogrammet någonsin äventyras.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
        -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
    ```

6. Den här regeln nekar trafik från internet till alla servrar i nätverket. Med regler med prioritet 110 och 120 är effekten att bara inkommande Internettrafik till brandvägg och RDP-portar på servrar och block allt annat. Den här regeln är en ”felsäkra” regel att blockera alla oväntat flöden.
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
7. Den slutliga regeln nekar trafik från undernätet Frontend till Backend-undernät. Eftersom den här regeln är en regel för inkommande endast, tillåts omvänd trafik (från serverdelen för klientdelen).

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

## <a name="traffic-scenarios"></a>Trafik scenarier
#### <a name="allowed-internet-to-web-server"></a>(*Tillåtna*) Internet till webbservern
1. En internet-användare begär en HTTP-sida från FrontEnd001.CloudApp.Net (Internet Facing Cloud Service)
2. Cloud service överför trafik via öppna slutpunkter på port 80 mot IIS01 (webbserver)
3. Undernätet frontend börjar bearbetning av inkommande regel:
   1. NSG regel 1 (DNS) inte tillämpas, gå till nästa regel
   2. NSG regel 2 (RDP) inte tillämpas, gå till nästa regel
   3. NSG regel 3 (Internet till IIS01) gäller, trafik är tillåtna, stoppa regel bearbetning
4. Trafik träffar interna IP-adressen för webbservern IIS01 (10.0.1.5)
5. IIS01 lyssnar för webbtrafik, tar emot denna begäran och startar bearbetning av begäran
6. IIS01 begär SQL Server på AppVM01 information
7. Eftersom det finns inga regler för utgående trafik på undernätet Frontend, tillåts trafik
8. Backend-undernät börjar bearbetning av inkommande regel:
   1. NSG regel 1 (DNS) inte tillämpas, gå till nästa regel
   2. NSG regel 2 (RDP) inte tillämpas, gå till nästa regel
   3. NSG regel 3 (Internet-brandväggen) inte tillämpas, gå till nästa regel
   4. NSG regel 4 (IIS01 till AppVM01) gäller, tillåts trafik, stoppa regelbearbetningen
9. AppVM01 tar emot en SQL-fråga och svarar
10. Eftersom det finns inga regler för utgående trafik på Backend-undernät, tillåts svaret
11. Undernätet frontend börjar bearbetning av inkommande regel:
    1. Det finns ingen NSG-regel som gäller för inkommande trafik från Backend-undernät till undernätet Frontend, så att ingen av NSG: N regler tillämpas
    2. System Standardregeln som tillåter trafik mellan undernät att den här trafiken så att trafik tillåts.
12. IIS-servern tar emot svaret SQL och slutför HTTP-svar och skickar till begäranden
13. Eftersom det inte finns några regler för utgående trafik på undernätet Frontend svaret tillåts och internet-användare får den begärda webbsidan.

#### <a name="allowed-rdp-to-backend"></a>(*Tillåtna*) RDP till serverdelen
1. Serveradministratören på internet begär RDP-session till AppVM01 på BackEnd001.CloudApp.Net:xxxxx där xxxxx är slumpmässigt tilldelad portnumret för RDP till AppVM01 (tilldelad port finns på Azure-portalen eller via PowerShell)
2. Backend-undernät börjar bearbetning av inkommande regel:
   1. NSG regel 1 (DNS) inte tillämpas, gå till nästa regel
   2. NSG regel 2 (RDP) gäller, trafik är tillåtna, stoppa regel bearbetning
3. Med några regler för utgående trafik standardregler gäller och returnera trafik tillåts
4. RDP-session är aktiverad
5. AppVM01 måste ange användarnamn och lösenord

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Tillåtna*) Web server DNS-sökningen på DNS-server
1. Web Server, IIS01, måste en datafeed på www.data.gov, men måste matcha adressen.
2. Nätverkskonfigurationen för listorna VNet DNS01 (10.0.2.4 på Backend-undernät) som den primära DNS-servern, IIS01 skickar en DNS-begäran till DNS01
3. Inga regler för utgående trafik på undernätet Frontend, tillåts trafik
4. Backend-undernät börjar bearbetning av inkommande regel:
   * NSG regel 1 (DNS) gäller, trafik är tillåtna, stoppa regel bearbetning
5. DNS-servern tar emot begäran
6. DNS-servern har inte cachelagrade-adress och begär en rot-DNS-server på internet
7. Inga regler för utgående trafik på Backend-undernät, tillåts trafik
8. Internet-DNS-servern svarar, eftersom denna session initierades internt, tillåts svaret
9. DNS-servern cachelagrar svaret och svarar på den ursprungliga begäranden tillbaka till IIS01
10. Inga regler för utgående trafik på Backend-undernät, tillåts trafik
11. Undernätet frontend börjar bearbetning av inkommande regel:
    1. Det finns ingen NSG-regel som gäller för inkommande trafik från Backend-undernät till undernätet Frontend, så att ingen av NSG: N regler tillämpas
    2. System Standardregeln som tillåter trafik mellan undernät skulle göra att den här trafiken så att trafik tillåts
12. IIS01 tar emot svaret från DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Tillåtna*) Web server access-fil på AppVM01
1. IIS01 begär en fil på AppVM01
2. Inga regler för utgående trafik på undernätet Frontend, tillåts trafik
3. Backend-undernät börjar bearbetning av inkommande regel:
   1. NSG regel 1 (DNS) inte tillämpas, gå till nästa regel
   2. NSG regel 2 (RDP) inte tillämpas, gå till nästa regel
   3. NSG regel 3 (Internet till IIS01) inte tillämpas, gå till nästa regel
   4. NSG regel 4 (IIS01 till AppVM01) gäller, tillåts trafik, stoppa regelbearbetningen
4. AppVM01 tar emot begäran och svarar med (förutsatt att du har behörighet)
5. Eftersom det finns inga regler för utgående trafik på Backend-undernät, tillåts svaret
6. Undernätet frontend börjar bearbetning av inkommande regel:
   1. Det finns ingen NSG-regel som gäller för inkommande trafik från Backend-undernät till undernätet Frontend, så att ingen av NSG: N regler tillämpas
   2. System Standardregeln som tillåter trafik mellan undernät att den här trafiken så att trafik tillåts.
7. IIS-servern tar emot filen

#### <a name="denied-web-to-backend-server"></a>(*Nekas*) Web till backend-servern
1. En internet-användare försöker få åtkomst till en fil på AppVM01 via tjänsten BackEnd001.CloudApp.Net
2. Eftersom inga slutpunkter är öppen för filresursen är den här trafiken skulle inte klarar Molntjänsten och skulle nå servern
3. Om slutpunkterna öppna av någon anledning skulle NSG regel 5 (Internet till VNet) blockera den här trafiken

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Nekas*) Web DNS-sökningen på DNS-server
1. En internet-användare försöker att leta upp en intern DNS-post på DNS01 via tjänsten BackEnd001.CloudApp.Net
2. Eftersom inga slutpunkter är öppen för DNS är den här trafiken skulle inte klarar Molntjänsten och skulle nå servern
3. Om slutpunkterna öppna av någon anledning NSG regel 5 (Internet till VNet) skulle blockera den här trafiken (Obs: regel 1 (DNS) inte tillämpas av två skäl, först källadressen är på internet, den här regeln gäller för det lokala VNet som källa den här regeln är också en Tillåt-regel, så det skulle aldrig neka trafik)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Nekas*) Web SQL-åtkomst genom brandväggen
1. En internet-användare begär SQL-data från FrontEnd001.CloudApp.Net (Internet Facing Cloud Service)
2. Eftersom inga slutpunkter är öppen för SQL är den här trafiken skulle inte klarar Molntjänsten och skulle nå brandväggen
3. Om slutpunkter öppna av någon anledning börjar undernätet Frontend bearbetning av inkommande regel:
   1. NSG regel 1 (DNS) inte tillämpas, gå till nästa regel
   2. NSG regel 2 (RDP) inte tillämpas, gå till nästa regel
   3. NSG regel 3 (Internet till IIS01) gäller, trafik är tillåtna, stoppa regel bearbetning
4. Trafik träffar interna IP-adressen för IIS01 (10.0.1.5)
5. IIS01 lyssnar inte på port 1433, så inga svar på begäran

## <a name="conclusion"></a>Sammanfattning
Det här exemplet är ett relativt enkla och rakt framåt sätt att isolera backend-undernät från inkommande trafik.

Fler exempel och en översikt över nätverket säkerhetsgränser finns [här][HOME].

## <a name="references"></a>Referenser
### <a name="main-script-and-network-config"></a>Huvudsakliga skript- och konfiguration
Spara fullständig skript i ett PowerShell-skriptfil. Spara konfigurationen nätverk i en fil med namnet ”NetworkConf1.xml”.
Ändra de användardefinierade variablerna vid behov och kör skriptet.

#### <a name="full-script"></a>Fullständigt skript
Det här skriptet kommer att baseras på de användardefinierade variablerna.

1. Ansluta till en Azure-prenumeration
2. skapar ett lagringskonto
3. Skapa ett VNet och två undernät som har definierats i konfigurationsfilen för nätverk
4. Skapa fyra windows server-datorer
5. Konfigurera NSG inklusive:
   * Skapa en NSG
   * Fylla det med regler
   * Bindning NSG: N till lämpliga undernät

Detta PowerShell-skript ska köras lokalt på en internet-ansluten dator eller server.

> [!IMPORTANT]
> När du kör det här skriptet kanske varningar eller andra informationsmeddelanden som visas i PowerShell. Endast felmeddelanden i rött är orsaken till problem.
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
Spara XML-filen med uppdaterad plats och lägga till länken till den här filen till variabeln $NetworkConfigFile i det här skriptet.

```XML
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

#### <a name="sample-application-scripts"></a>Exempelskript för programmet
Om du vill installera ett exempelprogram för det här och andra DMZ exempel något finns på följande länk: [exempelskript för programmet][SampleApp]

## <a name="next-steps"></a>Nästa steg
* Uppdatera och spara XML-fil
* Kör PowerShell-skript för att skapa miljön
* Installera exempelprogrammet
* Testa olika trafikflöden via den här DMZ

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "Inkommande perimeternätverk med NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

