---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671501"
---
Även om funktionen är enkel att konfigurera, innebär det inte att din upplevelse kommer att vara problem fri. Om du stöter på problem med att komma åt den önskade slut punkten finns det några verktyg som du kan använda för att testa anslutningen från App-konsolen. Det finns två konsoler som du kan använda. Det ena är kudu-konsolen och den andra konsolen i Azure Portal. Om du vill komma åt kudu-konsolen från din app går du till Verktyg-> kudu. Du kan också komma åt Kudo-konsolen på [webbplats namn]. scm. azurewebsites. net. När webbplatsen har lästs in går du till fliken fel söknings konsol. För att komma till den Azure Portal värdbaserade konsolen går du till Verktyg->-konsolen från din app. 

#### <a name="tools"></a>Verktyg
Verktygen **ping**, **nslookup**och **tracert** fungerar inte via konsolen på grund av säkerhets begränsningar. Två separata verktyg har lagts till för att fylla i Void. För att testa DNS-funktionen har vi lagt till ett verktyg med namnet nameresolver. exe. Syntax:

    nameresolver.exe hostname [optional: DNS Server]

Du kan använda **nameresolver** för att kontrol lera de värdnamn som appen är beroende av. På så sätt kan du testa om du har något som är mis-konfigurerat med din DNS eller kanske inte har åtkomst till din DNS-server. Du kan se den DNS-server som appen kommer att använda i-konsolen genom att titta på miljövariabler WEBSITE_DNS_SERVER och WEBSITE_DNS_ALT_SERVER.

Med nästa verktyg kan du testa TCP-anslutningar till en kombination av värd och port. Det här verktyget kallas **tcpping** och syntaxen är:

    tcpping.exe hostname [optional: port]

**Tcpping** -verktyget visar om du kan komma åt en speciell värd och port. Den kan bara visas om: det finns ett program som lyssnar på värd-och port kombinationen och det finns en nätverks åtkomst från din app till den angivna värden och porten.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Fel sökning av åtkomst till VNet-baserade resurser
Det finns ett antal saker som kan förhindra att appen når en speciell värd och port. Merparten av tiden är ett av tre saker:

* **En brand vägg är på väg.** Om du har en brand vägg i vägen kommer du att trycka på TCP-timeout. TCP-tidsgräns är 21 sekunder i det här fallet. Använd verktyget **tcpping** för att testa anslutningen. TCP-tidsgräns kan bero på många saker bortom brand väggarna, men starta där. 
* **DNS är inte tillgängligt.** DNS-timeoutvärdet är tre sekunder per DNS-server. Om du har två DNS-servrar är tids gränsen 6 sekunder. Använd nameresolver för att se om DNS fungerar. Kom ihåg att du inte kan använda nslookup eftersom det inte använder DNS ditt VNet har kon figurer ATS med. Om det inte går att komma åt kan du ha en brand vägg eller NSG som blockerar åtkomsten till DNS, eller så kan den vara avstängd.

Om dessa objekt inte besvarar dina problem, se först till exempel: 

**regional VNet-integrering**
* är målet en icke-RFC1918 adress och du har inte WEBSITE_VNET_ROUTE_ALL angetts till 1?
* finns det en NSG som blockerar utgående från ditt integrations-undernät?
* Om du går över ExpressRoute eller en VPN-anslutning, är din lokala gateway konfigurerad för att dirigera trafik tillbaka till Azure? Om du kan komma åt slut punkter i ditt VNet men inte lokalt, kontrollerar du dina vägar.
* har du tillräckligt med behörighet för att ställa in delegering i integrations under nätet? Under konfigurationen av regional VNet-integrering delegeras ditt integrations undernät till Microsoft. Web. GRÄNSSNITTET för VNet-integrering kommer att delegera under nätet till Microsoft. Web automatiskt. Om ditt konto inte har tillräckliga nätverks behörigheter för att konfigurera delegering, behöver du någon som kan ange attribut i ditt integrations-undernät för att delegera under nätet. Om du vill delegera integrations under nätet manuellt går du till Azure Virtual Network Subnet UI och anger delegering för Microsoft. Web. 

**Gateway krävs VNet-integrering**
* är adress intervallet för punkt-till-plats i RFC 1918-intervallen (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Visas gatewayen som i portalen? Om din Gateway inte är igång kan du ta den tillbaka.
* Visas certifikaten som synkroniserade eller så misstänker du att nätverks konfigurationen har ändrats?  Om dina certifikat inte är synkroniserade eller om du misstänker att det har skett en ändring av din VNet-konfiguration som inte har synkroniserats med ASP, klickar du på synkronisera nätverk.
* Om du går över en VPN-anslutning är den lokala gatewayen konfigurerad för att dirigera trafik tillbaka till Azure? Om du kan komma åt slut punkter i ditt VNet men inte lokalt, kontrollerar du dina vägar.
* försöker du använda en gateway för samexistens som stöder både punkt-till-plats-och ExpressRoute? Samexistens-gatewayer stöds inte med VNet-integrering.

Fel sökning av nätverks problem är en utmaning eftersom det inte går att se vad som blockerar åtkomst till en speciell värd: port kombination. Några av orsakerna är:

* Du har en brand vägg på din värd som förhindrar åtkomst till program porten från platsens IP-intervall. Korsande undernät kräver ofta offentlig åtkomst.
* Mål värden är avstängd.
* Ditt program är nere.
* Du har fel IP-adress eller värddator namn.
* Ditt program lyssnar på en annan port än det du förväntade dig. Du kan matcha ditt process-ID med lyssnings porten med hjälp av "netstat-Aon" på slut punkts värden. 
* Dina nätverks säkerhets grupper konfigureras på ett sådant sätt att de förhindrar åtkomst till din program värd och port från ditt plats-till-plats-IP-intervall.

Kom ihåg att du inte vet vilken adress appen faktiskt kommer att använda. Det kan vara vilken adress som helst i integrations under nätet eller från punkt-till-plats-adressintervallet, så du måste tillåta åtkomst från hela adress intervallet. 

Ytterligare fel söknings steg är:

* Anslut till en virtuell dator i ditt VNet och försök att ansluta till resurs värden: port därifrån. Om du vill testa TCP-åtkomst använder du PowerShell **-kommandot test-NetConnection**. Syntax:

      test-netconnection hostname [optional: -Port]

* Hämta ett program på en virtuell dator och testa åtkomsten till denna värd och port från-konsolen från din app med hjälp av **tcpping**

#### <a name="on-premises-resources"></a>Lokala resurser ####

Om din app inte kan komma åt en resurs lokalt, kontrollerar du om du kan komma åt resursen från ditt VNet. Använd PowerShell **-kommandot test-NetConnection** för att kontrol lera TCP-åtkomst. Om din virtuella dator inte kan komma åt din lokala resurs, kanske inte VPN-eller ExpressRoute-anslutningen är korrekt konfigurerad.

Om det virtuella nätverket som finns på den virtuella datorn kan komma åt ditt lokala system, men din app inte kan, beror orsaken förmodligen på något av följande orsaker:

* Dina vägar har inte kon figurer ATS med ditt undernät eller pekar på plats adress intervall i din lokala gateway.
* Dina nätverks säkerhets grupper blockerar åtkomsten för ditt punkt-till-plats-IP-intervall.
* Dina lokala brand väggar blockerar trafik från ditt punkt-till-plats-IP-intervall.
* Du försöker komma åt en icke-RFC 1918-adress med hjälp av funktionen regional VNet-integrering.