---
title: 'Exempel på Azure DMZ – skapa en enkel DMZ med NSG: er | Microsoft Docs'
description: Skapa ett perimeternätverrk med Nätverkssäkerhetsgrupper (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 68655ea03f53fe7100f67d111fcd3c8595bdf4c9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109400"
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>Exempel 1 – skapa en enkel DMZ med NSG: er med en Azure Resource Manager-mall
[Gå tillbaka till gränsen bästa praxis sidan][HOME]

> [!div class="op_single_selector"]
> * [Resource Manager-mall](virtual-networks-dmz-nsg.md)
> * [Klassisk – PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Det här exemplet skapar en primitiv DMZ med fyra Windows-servrar och Nätverkssäkerhetsgrupper. Det här exemplet beskrivs relevanta mall-avsnitt för att ge en bättre förståelse för varje steg. Det finns också ett avsnitt för trafik scenariot att ge stegvisa inblick i hur trafik fortsätter genom försvarslinjer i Perimeternätverket. Slutligen är avsnittet i referenserna den fullständiga mallkod och instruktioner för att skapa den här miljön för att testa och experimentera med olika scenarier. 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![Inkommande perimeternätverk med NSG][1]

## <a name="environment-description"></a>Miljö-beskrivning
I det här exemplet innehåller en prenumeration i följande resurser:

* En enskild resursgrupp
* Ett virtuellt nätverk med två undernät; ”FrontEnd” och ”serverdel”
* En Nätverkssäkerhetsgrupp som tillämpas på båda undernäten
* En Windows-Server som representerar en program-webbserver (”IIS01”)
* Två windows-servrar som representerar programmet backend-servrar (”AppVM01”, ”AppVM02”)
* En Windows-server som representerar en DNS-server (”DNS01”)
* Offentlig IP-adress som är associerade med programmet webbservern

Det finns en länk till en Azure Resource Manager-mall som bygger den miljö som beskrivs i det här exemplet i referensavsnittet. Att skapa virtuella datorer och virtuella nätverk, även om gör du genom att exempelmall som inte beskrivs i detalj i det här dokumentet. 

**Att skapa den här miljön** (detaljerade anvisningar finns i referensavsnittet i det här dokumentet);

1. Distribuera Azure Resource Manager-mallen på: [Azure-Snabbstartmallar][Template]
2. Installera exempelprogrammet på: [Exempelskript för program][SampleApp]

>[!NOTE]
>För RDP för backend-servrar i den här instansen används IIS-server som en ”jumpbox”. Första RDP till IIS-servern och sedan från RDP för IIS-Server till backend-servern. Alternativt kan en offentlig IP-adress associeras med varje NIC för enklare RDP-server.
> 
>

Följande avsnitt innehåller en detaljerad beskrivning av den nya Nätverkssäkerhetsgruppen och hur den fungerar i det här exemplet genom att gå igenom viktiga rader med Azure Resource Manager-mall.

## <a name="network-security-groups-nsg"></a>Nätverkssäkerhetsgrupper (NSG)
I det här exemplet bygger en NSG-grupp och sedan läsa in med sex regler. 

>[!TIP]
>Generellt sett bör du skapa dina specifika regler för ”Tillåt” först och sedan senaste mer allmänt ”Deny” reglerna. De tilldelade prioritet avgör vilka regler är utvärderas först. När trafik identifieras som gäller för en specifik regel, utvärderas inga fler regler. NSG-regler kan använda antingen i inkommande eller utgående riktning (ur undernätet).
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

Det finns en utgående standardregel som tillåter trafik ut till internet. I det här exemplet vi tillåter utgående trafik och ändrar inte någon utgående regler. Om du vill tillämpa säkerhetsprincipen för trafik i båda riktningarna användaren definierats routning krävs och är utforskat ”exempel 3” på den [gräns bästa praxis säkerhetssidan][HOME].

Varje regel beskrivs i detalj på följande sätt:

1. En Nätverkssäkerhetsgrupp resurs måste ha instanser skapade för att lagra reglerna:

    ```JSON
    "resources": [
      {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/networkSecurityGroups",
        "name": "[variables('NSGName')]",
        "location": "[resourceGroup().location]",
        "properties": { }
      }
    ]
    ``` 

2. Den första regeln i det här exemplet kan DNS-trafik mellan alla interna nätverk till DNS-servern på backend-undernät. Regeln har vissa viktiga parametrar:
   * ”destinationAddressPrefix” - målets adressprefix anges till ”10.0.2.4” så att DNS-trafik tillåts att nå DNS-servern.
   * ”Riktning” innebär det att gälla i den här regeln i vilken riktning av flödet i nätverkstrafiken. Riktningen är ur undernät eller virtuella datorn (beroende på var den här NSG binds). Därför om riktningen är ”Inbound” och trafiken som kommer in i undernätet, regeln skulle tillämpas och trafik som lämnar undernätet inte påverkas av den här regeln.
   * ”Prioritet” Anger att där ett trafikflöde utvärderas. Ju lägre det nummer desto högre prioritet. När en regel som gäller för en specifik trafikflödet, bearbetas inga ytterligare regler. Därför om en regel med prioritet 1 tillåter trafik, och en regel med prioritet 2 nekar trafik, och båda regler gäller vid trafik så trafiken kan flöda (eftersom regel 1 har högre prioritet det tog att gälla och inga ytterligare regler tillämpades).
   * ”Åtkomst” innebär det att om trafik som påverkas av den här regeln är blockerade (”Deny”) eller tillåtna (”Tillåt”).

     ```JSON
     "properties": {
     "securityRules": [
      {
        "name": "enable_dns_rule",
        "properties": {
          "description": "Enable Internal DNS",
          "protocol": "*",
          "sourcePortRange": "*",
          "destinationPortRange": "53",
          "sourceAddressPrefix": "VirtualNetwork",
          "destinationAddressPrefix": "10.0.2.4",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
     ```

3. Den här regeln tillåter RDP-trafik kan flöda från internet till RDP-porten på alla servrar i det bundna undernätet. 

    ```JSON
    {
      "name": "enable_rdp_rule",
      "properties": {
        "description": "Allow RDP",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "3389",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 110,
        "direction": "Inbound"
      }
    },
    ```

4. Den här regeln tillåter inkommande Internettrafik till trycker på webbservern. Den här regeln ändrar inte beteendet routning. Regeln tillåter endast trafik till IIS01 att skicka. Därför om trafik från Internet hade webbservern som mål den här regeln skulle tillåta att den och stoppa bearbetningen ytterligare regler. (I regeln med prioritet 140 alla andra inkommande internet-trafiken blockeras). Om du endast bearbetar HTTP-trafik, kan den här regeln begränsas ytterligare så att bara mål Port 80.

    ```JSON
    {
      "name": "enable_web_rule",
      "properties": {
        "description": "Enable Internet to [variables('VM01Name')]",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "10.0.1.5",
        "access": "Allow",
        "priority": 120,
        "direction": "Inbound"
        }
      },
    ```

5. Den här regeln tillåter trafik skickas från IIS01 servern till AppVM01-servern, en senare regeln blockerar alla andra klientdel för Backend-trafik. Att förbättra den här regeln om porten är känt som ska läggas till. Till exempel IIS-servern når endast SQL Server på AppVM01, kan portintervallet för målet ska ändras från ”*” (valfritt) till 1433 (SQL-port), vilket medför att ett mindre inkommande risken för angrepp på AppVM01 webbprogrammet någonsin behövs.

    ```JSON
    {
      "name": "enable_app_rule",
      "properties": {
        "description": "Enable [variables('VM01Name')] to [variables('VM02Name')]",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "10.0.1.5",
        "destinationAddressPrefix": "10.0.2.5",
        "access": "Allow",
        "priority": 130,
        "direction": "Inbound"
      }
    },
     ```

6. Regler kan använda en särskild typ av adressprefix som kallas en ”standard tagg”, dessa taggar är systemdefinierade identifierare som gör det enkelt att åtgärda en större kategori av adressprefix. Den här regeln använder standard-taggen ”VirtualNetwork” för måladress-prefix för en obestämd alla adresser i det virtuella nätverket. Andra prefix-etiketter är Internet och AzureLoadBalancer. Den här regeln nekar trafik från internet till alla servrar i nätverket. Med reglerna med prioritet 110 och 120 är effekten att endast inkommande Internettrafik till brandväggen och RDP-portar på servrar och blockerar allt annat. Den här regeln är en ”felsäker” regel för att blockera alla oväntat flöden.

    ```JSON
    {
      "name": "deny_internet_rule",
      "properties": {
        "description": "Isolate the [variables('VNetName')] VNet from the Internet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Deny",
        "priority": 140,
        "direction": "Inbound"
      }
    },
     ```

7. Sista regeln nekar trafik från undernätet på klientsidan till Backend-undernät. Eftersom den här regeln är en inkommande regel som endast är tillåts omvänd trafik (från serverdelen för klientdelen).

    ```JSON
    {
      "name": "deny_frontend_rule",
      "properties": {
        "description": "Isolate the [variables('Subnet1Name')] subnet from the [variables('Subnet2Name')] subnet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "[variables('Subnet1Prefix')]",
        "destinationAddressPrefix": "[variables('Subnet2Prefix')]",
        "access": "Deny",
        "priority": 150,
        "direction": "Inbound"
      }
    }
    ```

## <a name="traffic-scenarios"></a>Trafik-scenarier
#### <a name="allowed-internet-to-web-server"></a>(*Tillåtna*) Internet till webbservern
1. En internet-användare begär en HTTP-sida från offentliga IP-adressen för nätverkskortet som är associerade med IIS01 NIC
2. Offentliga IP-adress skickar trafik till det virtuella nätverket mot IIS01 (webbserver)
3. Frontend-undernätet börjar bearbetning av inkommande regel:
   1. 1 för NSG-regel (DNS) inte tillämpa, gå till nästa regel
   2. NSG regel 2 (RDP) inte tillämpa, gå till nästa regel
   3. NSG-regel 3 (Internet till IIS01) gäller, trafik är tillåtna, stoppa regelbehandling
4. Trafiken kommer till den interna IP-adressen för webbservern IIS01 (10.0.1.5)
5. IIS01 lyssnar för webbtrafik, får den här förfrågan och startar bearbetning av begäran
6. IIS01 begär SQL Server på AppVM01 information
7. Inga utgående regler på Frontend-undernätet trafik tillåts
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
12. IIS-servern tar emot SQL-svar och slutför HTTP-svar och skickar till beställaren
13. Eftersom det finns inga regler för utgående trafik på undernätet på klientsidan är svaret tillåts och Internet-användare får den webbsida som begärdes.

#### <a name="allowed-rdp-to-iis-server"></a>(*Tillåtna*) RDP till IIS-server
1. En serveradministratör på internet begär en RDP-session till IIS01 på den offentliga IP-adressen för nätverkskortet som är associerade med IIS01 nätverkskortet (den här offentliga IP-adressen kan hittas via portalen eller PowerShell)
2. Offentliga IP-adress skickar trafik till det virtuella nätverket mot IIS01 (webbserver)
3. Frontend-undernätet börjar bearbetning av inkommande regel:
   1. 1 för NSG-regel (DNS) inte tillämpa, gå till nästa regel
   2. NSG regel 2 (RDP) gäller, trafik är tillåtna, stoppa regelbehandling
4. Med inga utgående regler gäller för standard och återvändande trafik tillåts
5. RDP-session är aktiverat
6. IIS01 måste ange användarnamn och lösenord

>[!NOTE]
>För RDP för backend-servrar i den här instansen används IIS-server som en ”jumpbox”. Första RDP till IIS-servern och sedan från RDP för IIS-Server till backend-servern.
>
>

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

#### <a name="denied-rdp-to-backend"></a>(*Nekad*) RDP till serverdelen
1. En internet-användare försöker RDP till servern AppVM01
2. Eftersom det finns inga offentliga IP-adresser som är associerade med den här NIC-servrar är den här trafiken anger aldrig det virtuella nätverket och inte skulle nå servern
3. Men en offentlig IP-adress har aktiverats av någon anledning, NSG-regel 2 (RDP) skulle tillåta den här trafiken

>[!NOTE]
>För RDP för backend-servrar i den här instansen används IIS-server som en ”jumpbox”. Första RDP till IIS-servern och sedan från RDP för IIS-Server till backend-servern.
>
>

#### <a name="denied-web-to-backend-server"></a>(*Nekad*) webben till backend-servern
1. En internet-användare försöker få åtkomst till en fil på AppVM01
2. Eftersom det finns inga offentliga IP-adresser som är associerade med den här NIC-servrar är den här trafiken anger aldrig det virtuella nätverket och inte skulle nå servern
3. Om en offentlig IP-adress har aktiverats av någon anledning, skulle NSG-regel 5 (Internet till VNet) blockera den här trafiken

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Nekad*) Web DNS-sökningen på DNS-server
1. En internet-användare försöker att leta upp en intern DNS-post på DNS01
2. Eftersom det finns inga offentliga IP-adresser som är associerade med den här NIC-servrar är den här trafiken anger aldrig det virtuella nätverket och inte skulle nå servern
3. Om en offentlig IP-adress har aktiverats av någon anledning, NSG-regel 5 (Internet till VNet) skulle blockera den här trafiken (Obs: att regel 1 (DNS) inte tillämpas eftersom begäranden källadress är internet och regel 1 gäller bara för det lokala virtuella nätverket som källa)

#### <a name="denied-sql-access-on-the-web-server"></a>(*Nekad*) SQL-åtkomst på webbservern
1. En internet-användare begär SQL-data från IIS01
2. Eftersom det finns inga offentliga IP-adresser som är associerade med den här NIC-servrar är den här trafiken anger aldrig det virtuella nätverket och inte skulle nå servern
3. Om en offentlig IP-adress har aktiverats av någon anledning, börjar med Frontend-undernätet bearbetning av inkommande regel:
   1. 1 för NSG-regel (DNS) inte tillämpa, gå till nästa regel
   2. NSG regel 2 (RDP) inte tillämpa, gå till nästa regel
   3. NSG-regel 3 (Internet till IIS01) gäller, trafik är tillåtna, stoppa regelbehandling
4. Trafik når interna IP-adressen för IIS01 (10.0.1.5)
5. IIS01 lyssnar inte på port 1433, så inga svar på begäran

## <a name="conclusion"></a>Sammanfattning
Det här exemplet är ett relativt enkla och okomplicerat sätt att isolera serverdelsundernätet från inkommande trafik.

Fler exempel och en översikt över nätverket säkerhetsgränser finns [här][HOME].

## <a name="references"></a>Referenser
### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Det här exemplet använder en fördefinierad Azure Resource Manager-mall i en GitHub-lagringsplats som hanteras av Microsoft och öppen för allmänheten. Den här mallen kan distribueras direkt från GitHub, eller hämtas och ändras för att passa dina behov. 

Den huvudsakliga mallen finns i filen med namnet ”azuredeploy.json”. Den här mallen kan skickas via PowerShell eller CLI (med associerade ”azuredeploy.parameters.json”-fil) för att distribuera den här mallen. Jag hittar det enklaste sättet är att använda knappen ”distribuera till Azure” på README.md-sidan på GitHub.

Följ dessa steg om du vill distribuera den mall som skapar det här exemplet från GitHub och Azure-portalen:

1. Från en webbläsare, navigerar du till den [mall][Template]
2. Klicka på knappen ”distribuera till Azure” (eller ”visualisera”-knappen för att se en grafisk representation av den här mallen)
3. Anger det Lagringskonto, användarnamn och lösenord i bladet parametrar och klickar sedan på **OK**
5. Skapa en resursgrupp för distributionen (du kan använda en befintlig, men det rekommenderar jag en ny för bästa resultat)
6. Om det behövs ändrar du inställningarna för prenumerationen och platsen för ditt virtuella nätverk.
7. Klicka på **Granska juridiska villkor**, Läs licensvillkoren och klicka på **köp** accepterar.
8. Klicka på **skapa** starta distributionen av den här mallen.
9. När distributionen har slutförts, går du till resursgruppen som skapades för den här distributionen att se de resurser som konfigurerats i.

>[!NOTE]
>Den här mallen kan RDP till IIS01-servern (hitta den offentliga IP-Adressen för IIS01 på portalen). För RDP för backend-servrar i den här instansen används IIS-server som en ”jumpbox”. Första RDP till IIS-servern och sedan från RDP för IIS-Server till backend-servern.
>
>

Om du vill ta bort den här distributionen, ta bort resursgruppen och alla underordnade resurser tas också bort.

#### <a name="sample-application-scripts"></a>Exempelskript för program
När mallen har körts, kan du konfigurera webbservern och applikationsserver med en enkel webbapp för att testa med den här DMZ-konfigurationen. Om du vill installera ett exempelprogram för denna och andra DMZ-exempel, finns en på följande länk: [Exempelskript för program][SampleApp]

## <a name="next-steps"></a>Nästa steg

* Distribuera det här exemplet
* Skapa exempelprogrammet
* Testa olika trafikflöden via det här perimeternätverket

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "Inkommande perimeternätverk med NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md
