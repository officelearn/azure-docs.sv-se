---
title: 'Azure DMZ exempel – skapa en enkel DMZ med NSG: er | Microsoft Docs'
description: Skapa en DMZ med Nätverkssäkerhetsgrupper (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: ec29e6b250f927a3a4a94ffdf83d6c7c0e325722
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>Exempel 1 – skapa en enkel DMZ med NSG: er med en Azure Resource Manager-mall
[Gå tillbaka till gränsen bästa praxis säkerhetssidan][HOME]

> [!div class="op_single_selector"]
> * [Resource Manager-mall](virtual-networks-dmz-nsg.md)
> * [Klassisk - PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Det här exemplet skapar en primitiv DMZ med fyra Nätverkssäkerhetsgrupper och Windows-servrar. Detta exempel beskrivs varje relevant mall avsnitt att ge en bättre förståelse för varje steg. Det finns också ett trafik scenariot avsnitt ge stegvisa inblick i hur trafik fortsätter via lager i skyddsstrategierna i Perimeternätverket. Slutligen är avsnitt i hänvisning klar mallkoden och instruktioner för att skapa den här miljön för att testa och experimentera med olika scenarier. 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![Inkommande DMZ med NSG][1]

## <a name="environment-description"></a>Beskrivning av miljö
I det här exemplet innehåller en prenumeration i följande resurser:

* En enskild resursgrupp
* Ett virtuellt nätverk med två undernät; ”FrontEnd” och ”BackEnd”
* En Nätverkssäkerhetsgrupp som tillämpas på båda undernäten
* En Windows-Server som representerar en program-webbserver (”IIS01”)
* Två windows-servrar som representerar backend-programservrar (”AppVM01”, ”AppVM02”)
* En Windows-server som representerar en DNS-server (”DNS01”)
* En offentlig IP-adress som är kopplade till webbservern program

Det finns en länk till en Azure Resource Manager-mall som bygger på miljön som beskrivs i det här exemplet i referensavsnittet. Skapande av virtuella datorer och virtuella nätverk, även om utfärdad av exempel-mallen som inte beskrivs i detalj i detta dokument. 

**Att skapa den här miljön** (detaljerade anvisningar finns i referensavsnittet i det här dokumentet);

1. Distribuera Azure Resource Manager-mallen på: [Azure Quickstart mallar][Template]
2. Installera exempelprogrammet på: [exempelskript för programmet][SampleApp]

>[!NOTE]
>Till RDP för backend-servrar i den här instansen används IIS-servern som en ”hopp”. Första RDP till IIS-servern och sedan från RDP för IIS-servern till backend-servern. Alternativt kan en offentlig IP-adress associeras med varje servernätverkskort för enklare RDP.
> 
>

Följande avsnitt innehåller en detaljerad beskrivning Nätverkssäkerhetsgruppen och hur den fungerar i det här exemplet genom att gå igenom viktiga raderna i Azure Resource Manager-mall.

## <a name="network-security-groups-nsg"></a>Nätverkssäkerhetsgrupper (NSG)
I det här exemplet bygger en NSG-grupp och sedan in med sex regler. 

>[!TIP]
>Generellt sett bör du skapa specifika ”Tillåt” reglerna först och sedan de mer allmänna reglerna som ”Deny” sist. De tilldelade prioritet bestämmer vilka regler är utvärderas först. När du har hittat trafik ska gälla för en specifik regel utvärderas inga ytterligare regler. NSG-regler kan använda antingen i inkommande eller utgående riktning (ur undernätet).
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

Det finns en utgående Standardregeln som tillåter trafik ut till internet. I det här exemplet vi att tillåta utgående trafik och inte ändra de utgående reglerna. Om du vill tillämpa säkerhetsprincipen så att trafik i båda riktningarna användaren definierat routning krävs och är utforskade ”exempel 3” på den [gräns bästa praxis sidan][HOME].

Varje regel diskuteras i detalj på följande sätt:

1. En Nätverkssäkerhetsgrupp resurs måste initieras för reglerna:

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

2. Den första regeln i det här exemplet tillåter DNS-trafik mellan alla interna nätverk till DNS-servern på backend-undernät. Regeln har vissa viktiga parametrar:
  * ”destinationAddressPrefix” - regler kan använda en särskild typ av adressprefix kallas en ”standard tagg”, dessa taggar är systemdefinierade identifierare som tillåter ett enkelt sätt att adressera en större kategori av adressprefix. Den här regeln använder standard-taggen ”Internet” för en obestämd alla adresser utanför det virtuella nätverket. Andra prefix etiketter är VirtualNetwork och AzureLoadBalancer.
  * ”Riktning” betyder i vilken riktning för trafikflöde regeln träder i kraft. Riktningen är ur ett undernät eller virtuella datorn (beroende på om den här NSG binds). Därför om riktningen är ”inkommande” trafik kommer in undernätet, skulle regeln och trafik som lämnar undernätet inte påverkas av den här regeln.
  * ”Prioritet” Anger att ett trafikflöde utvärderas. Ju lägre det nummer desto högre prioritet. När en regel som gäller för en specifik trafikflödet, bearbetas inga ytterligare regler. Därför om en regel med prioritet 1 tillåter trafik, och en regel med prioritet 2 nekar trafik, och båda reglerna som gäller för trafik som sedan trafiken skulle kunna flöda (eftersom regel 1 har en högre prioritet det tog att gälla och inga ytterligare regler har tillämpats).
  * ”Åtkomst” anger om trafik som påverkas av regeln är blockerade (”Deny”) eller tillåtna (”Tillåt”).

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

3. Den här regeln kan RDP-trafik ska flödas från internet till RDP-porten på alla servrar i det bundna undernätet. 

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

4. Den här regeln tillåter inkommande trafik för internet att träffa på webbservern. Den här regeln ändras inte dirigeringsbeteendet. Regeln kan bara trafik till IIS01 att skicka. Därför om trafik från Internet hade webbservern som leder den här regeln skulle göra det möjligt och stoppa bearbetningen ytterligare regler. (I regeln med prioritet 140 alla andra inkommande internet-trafiken blockeras). Om du bara bearbetning av HTTP-trafik, kan den här regeln begränsas ytterligare så att bara mål Port 80.

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

5. Den här regeln kan trafik skickas från servern IIS01 till AppVM01-server, en senare regeln block andra klientdel till Backend-trafik. Att förbättra den här regeln om porten är känd som ska läggas till. Till exempel om IIS-servern är träffa endast SQL Server på AppVM01, Målportintervallet ändras från ”*” (alla) till 1433 (SQL-port), vilket ger en mindre inkommande risken för angrepp på AppVM01 bör webbprogrammet någonsin äventyras.

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

6. Den här regeln nekar trafik från internet till alla servrar i nätverket. Med regler med prioritet 110 och 120 är effekten att bara inkommande Internettrafik till brandvägg och RDP-portar på servrar och block allt annat. Den här regeln är en ”felsäkra” regel att blockera alla oväntat flöden.

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

7. Den slutliga regeln nekar trafik från undernätet Frontend till Backend-undernät. Eftersom den här regeln är en regel för inkommande endast, tillåts omvänd trafik (från serverdelen för klientdelen).

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

## <a name="traffic-scenarios"></a>Trafik scenarier
#### <a name="allowed-internet-to-web-server"></a>(*Tillåtna*) Internet till webbservern
1. En internet-användare begär en HTTP-sida från offentliga IP-adressen för nätverkskortet som är associerade med IIS01 nätverkskort
2. Offentlig IP-adress skickar trafik till VNet mot IIS01 (webbserver)
3. Undernätet frontend börjar bearbetning av inkommande regel:
  1. NSG regel 1 (DNS) inte tillämpas, gå till nästa regel
  2. NSG regel 2 (RDP) inte tillämpas, gå till nästa regel
  3. NSG regel 3 (Internet till IIS01) gäller, trafik är tillåtna, stoppa regel bearbetning
4. Trafik träffar interna IP-adressen för webbservern IIS01 (10.0.1.5)
5. IIS01 lyssnar för webbtrafik, tar emot denna begäran och startar bearbetning av begäran
6. IIS01 begär SQL Server på AppVM01 information
7. Inga regler för utgående trafik på undernätet Frontend, tillåts trafik
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
12. IIS-servern tar emot svaret SQL och slutför HTTP-svar och skickar till beställaren
13. Eftersom det finns inga regler för utgående trafik på undernätet Frontend, svaret tillåts och Internet-användare får den begärda webbsidan.

#### <a name="allowed-rdp-to-iis-server"></a>(*Tillåtna*) RDP till IIS-server
1. Serveradministratör på internet begär en RDP-session till IIS01 på offentliga IP-adressen för nätverkskortet som är associerade med IIS01 NIC (den här offentliga IP-adressen finns via portalen eller PowerShell)
2. Offentlig IP-adress skickar trafik till VNet mot IIS01 (webbserver)
3. Undernätet frontend börjar bearbetning av inkommande regel:
  1. NSG regel 1 (DNS) inte tillämpas, gå till nästa regel
  2. NSG regel 2 (RDP) gäller, trafik är tillåtna, stoppa regel bearbetning
4. Med några regler för utgående trafik standardregler gäller och returnera trafik tillåts
5. RDP-session är aktiverad
6. IIS01 måste ange användarnamn och lösenord

>[!NOTE]
>Till RDP för backend-servrar i den här instansen används IIS-servern som en ”hopp”. Första RDP till IIS-servern och sedan från RDP för IIS-servern till backend-servern.
>
>

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

#### <a name="denied-rdp-to-backend"></a>(*Nekas*) RDP till serverdelen
1. En internet-användare försöker att RDP server AppVM01
2. Eftersom det inte finns några offentliga IP-adresser som är associerade med den här NIC-servrar, den här trafiken anger aldrig VNet och skulle nå servern
3. Men om en offentlig IP-adress har aktiverats av någon anledning NSG regel 2 (RDP) skulle göra att den här trafiken

>[!NOTE]
>Till RDP för backend-servrar i den här instansen används IIS-servern som en ”hopp”. Första RDP till IIS-servern och sedan från RDP för IIS-servern till backend-servern.
>
>

#### <a name="denied-web-to-backend-server"></a>(*Nekas*) Web till backend-servern
1. En internet-användare försöker få åtkomst till en fil på AppVM01
2. Eftersom det inte finns några offentliga IP-adresser som är associerade med den här NIC-servrar, den här trafiken anger aldrig VNet och skulle nå servern
3. Om en offentlig IP-adress har aktiverats av någon anledning skulle NSG regel 5 (Internet till VNet) blockera den här trafiken

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Nekas*) Web DNS-sökningen på DNS-server
1. En internet-användare försöker att leta upp en intern DNS-post på DNS01
2. Eftersom det inte finns några offentliga IP-adresser som är associerade med den här NIC-servrar, den här trafiken anger aldrig VNet och skulle nå servern
3. Om en offentlig IP-adress har aktiverats av någon anledning NSG regel 5 (Internet till VNet) skulle blockera den här trafiken (Obs: att regel 1 (DNS) inte tillämpas eftersom begäranden källadress är på internet och regel 1 gäller endast för lokala VNet som källa)

#### <a name="denied-sql-access-on-the-web-server"></a>(*Nekas*) SQL-åtkomst på webbservern
1. En internet-användare begär SQL-data från IIS01
2. Eftersom det inte finns några offentliga IP-adresser som är associerade med den här NIC-servrar, den här trafiken anger aldrig VNet och skulle nå servern
3. Om en offentlig IP-adress har aktiverats av någon anledning, börjar undernätet Frontend bearbetning av inkommande regel:
  1. NSG regel 1 (DNS) inte tillämpas, gå till nästa regel
  2. NSG regel 2 (RDP) inte tillämpas, gå till nästa regel
  3. NSG regel 3 (Internet till IIS01) gäller, trafik är tillåtna, stoppa regel bearbetning
4. Trafik träffar interna IP-adressen för IIS01 (10.0.1.5)
5. IIS01 lyssnar inte på port 1433, så inga svar på begäran

## <a name="conclusion"></a>Sammanfattning
Det här exemplet är ett relativt enkla och rakt framåt sätt att isolera backend-undernät från inkommande trafik.

Fler exempel och en översikt över nätverket säkerhetsgränser finns [här][HOME].

## <a name="references"></a>Referenser
### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Det här exemplet använder en fördefinierad Azure Resource Manager-mall i en GitHub-databas som hanteras av Microsoft och öppen för allmänheten. Den här mallen kan distribueras direkt från GitHub, eller hämtas och ändras så att de passar dina behov. 

Den huvudsakliga mallen finns i filen med namnet ”azuredeploy.json”. Den här mallen kan skickas via PowerShell eller CLI (med associerade ”azuredeploy.parameters.json”-fil) för att distribuera den här mallen. Jag hitta det enklaste sättet är att använda knappen ”distribuera till Azure” på sidan README.md på GitHub.

Följ dessa steg för att distribuera den mall som skapar det här exemplet från GitHub och Azure-portalen:

1. Från en webbläsare, navigerar du till den [mall][Template]
2. Klicka på ”Distribuera till Azure” (eller ”visualisera”-knappen för att se en grafisk representation av den här mallen)
3. Anger Storage-konto, användarnamn och lösenord i bladet parametrar och klicka sedan på **OK**
5. Skapa en resursgrupp för distributionen (du kan använda en befintlig, men jag rekommenderar en ny för bästa resultat)
6. Om det behövs ändrar du inställningarna för prenumerationen och platsen för ditt VNet.
7. Klicka på **Granska juridiska villkor**, Läs villkoren och klicka på **inköp** accepterar.
8. Klicka på **skapa** starta distributionen av denna mall.
9. När distributionen har slutförts, gå till resursgruppen som skapats för den här distributionen till finns resurser som konfigurerats i.

>[!NOTE]
>Den här mallen kan RDP IIS01 bara till servern (hitta den offentliga IP-Adressen för IIS01 på portalen). Till RDP för backend-servrar i den här instansen används IIS-servern som en ”hopp”. Första RDP till IIS-servern och sedan från RDP för IIS-servern till backend-servern.
>
>

Om du vill ta bort den här distributionen, ta bort resursgruppen och alla underordnade resurser tas också bort.

#### <a name="sample-application-scripts"></a>Exempelskript för programmet
När mallen har körts kan ställa du in webbservern och app-servern med en enkel webbapp för att testa med den här DMZ-konfigurationen. Om du vill installera ett exempelprogram för det här och andra DMZ exempel något finns på följande länk: [exempelskript för programmet][SampleApp]

## <a name="next-steps"></a>Nästa steg

* Distribuera det här exemplet
* Skapa exempelprogrammet
* Testa olika trafikflöden via den här DMZ

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "Inkommande DMZ med NSG"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md