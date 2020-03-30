---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671501"
---
Även om funktionen är lätt att ställa in, betyder det inte att din upplevelse kommer att vara problemfri. Om du stöter på problem med att komma åt önskad slutpunkt finns det några verktyg som du kan använda för att testa anslutningen från appkonsolen. Det finns två konsoler som du kan använda. Den ena är Kudu-konsolen och den andra är konsolen i Azure-portalen. Om du vill nå Kudu-konsolen från din app går du till Verktyg -> Kudu. Du kan också nå Kudo-konsolen på [sitename].scm.azurewebsites.net. När webbplatsen har läses in går du till fliken Felsökningskonsol. För att komma till Azure-portalen värd konsol sedan från din app gå till Verktyg -> Console. 

#### <a name="tools"></a>Verktyg
Verktygen **ping**, **nslookup**, och **tracert** kommer inte att fungera genom konsolen på grund av säkerhetsbegränsningar. För att fylla tomrummet, två separata verktyg till. För att testa DNS-funktioner har vi lagt till ett verktyg med namnet nameresolver.exe. Syntax:

    nameresolver.exe hostname [optional: DNS Server]

Du kan använda **nameresolver** för att kontrollera de värdnamn som appen är beroende av. På så sätt kan du testa om du har något felkonfigurerat med din DNS eller kanske inte har tillgång till DNS-servern. Du kan se den DNS-server som din app kommer att använda i konsolen genom att titta på de miljövariabler WEBSITE_DNS_SERVER och WEBSITE_DNS_ALT_SERVER.

Med nästa verktyg kan du testa för TCP-anslutning till en värd- och portkombination. Det här verktyget kallas **tcpping** och syntaxen är:

    tcpping.exe hostname [optional: port]

Den **tcpping** verktyget talar om för dig om du kan nå en viss värd och port. Det kan bara visa framgång om: det finns ett program som lyssnar på värd- och portkombinationen, och det finns nätverksåtkomst från din app till den angivna värden och porten.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Felsöka åtkomst till virtuella resurser
Det finns ett antal saker som kan hindra din app från att nå en viss värd och port. För det mesta är det en av tre saker:

* **En brandvägg är i vägen.** Om du har en brandvägg i vägen, kommer du att träffa TCP timeout. Tidsgränsen för TCP är 21 sekunder i det här fallet. Använd **tcpping-verktyget** för att testa anslutningen. TCP timeout kan bero på många saker bortom brandväggar men börja där. 
* **DNS är inte tillgängligt.** DNS-timeouten är tre sekunder per DNS-server. Om du har två DNS-servrar är tidsgränsen 6 sekunder. Använd nameresolver för att se om DNS fungerar. Kom ihåg att du inte kan använda nslookup eftersom den inte använder DEN DNS som ditt virtuella nätverk är konfigurerat med. Om det inte går att komma åt kan du ha en brandvägg eller NSG-blockering av åtkomst till DNS eller så kan den vara nere.

Om dessa objekt inte svarar på dina problem, leta först efter saker som: 

**regional VNet-integrering**
* är din destination en icke-RFC1918 adress och du inte har WEBSITE_VNET_ROUTE_ALL satt till 1?
* finns det en NSG som blockerar utgående från ditt integrationsundernät?
* Om du går över ExpressRoute eller en VPN, är din lokala gateway konfigurerad för att dirigera trafik tillbaka upp till Azure? Om du kan nå slutpunkter i ditt virtuella nätverk men inte lokalt kontrollerar du dina rutter.
* Har du tillräckligt med behörighet för att ange delegering i integrationsundernätet? Under den regionala konfigurationen för VNet-integrering delegeras integrationsundernätet till Microsoft.Web. Användargränssnittet för VNet-integrering delegerar undernätet automatiskt till Microsoft.Web. Om ditt konto inte har tillräcklig nätverksbehörighet för att ange delegering behöver du någon som kan ange attribut i undernätet för integrering för att delegera undernätet. Om du vill delegera integrationsundernätet manuellt går du till undernätverksgränssnittet i Azure Virtual Network och anger delegering för Microsoft.Web. 

**gateway krävs VNet Integration**
* är adressintervallet punkt till plats i RFC 1918-intervallen (10.0.0.0-10.255.255.255 / 172.16. 0,0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* Visas Gatewayen som uppe i portalen? Om din gateway är nere, sedan ta upp den igen.
* Visas certifikat som synkroniserade eller misstänker du att nätverkskonfigurationen har ändrats?  Om dina certifikat är osynkroniserade eller om du misstänker att det har gjorts en ändring i din VNet-konfiguration som inte synkroniserades med asps, tryck på "Synkronisera nätverk".
* Om du går över en VPN, är den lokala gatewayen konfigurerad för att dirigera trafik tillbaka upp till Azure? Om du kan nå slutpunkter i ditt virtuella nätverk men inte lokalt kontrollerar du dina rutter.
* Försöker du använda en samexistens gateway som stöder både peka på plats och ExpressRoute? Samexistens gateways stöds inte med VNet Integration.

Felsökning av nätverksproblem är en utmaning eftersom det inte går att se vad som blockerar åtkomsten till en viss kombination av värd:port. Några av orsakerna är:

* Du har en brandvägg på värden som förhindrar åtkomst till programporten från din punkt till plats IP-intervall. Att korsa undernät kräver ofta allmänhetens tillgång.
* Din målvärd är nere.
* Din ansökan är nere.
* Du hade fel IP eller värdnamn.
* Ditt program lyssnar på en annan port än vad du förväntade dig. Du kan matcha ditt process-ID med lyssningsporten med hjälp av "netstat -aon" på slutpunktsvärden. 
* Nätverkssäkerhetsgrupperna är konfigurerade på ett sådant sätt att de förhindrar åtkomst till programvärden och porten från din punkt till plats-IP-intervall.

Kom ihåg att du inte vet vilken adress din app faktiskt kommer att använda. Det kan vara vilken adress som helst i undernätet för integrering eller punkt-till-plats-adressintervallet, så du måste tillåta åtkomst från hela adressintervallet. 

Ytterligare felsökningssteg är:

* Anslut till en virtuell dator i ditt virtuella nätverk och försök nå din resursvärd:port därifrån. Om du vill testa för TCP-åtkomst använder du **powershell-kommandot test-netconnection**. Syntax:

      test-netconnection hostname [optional: -Port]

* Ta fram ett program på en virtuell dator och testa åtkomsten till värden och porten från konsolen från appen med **tcpping**

#### <a name="on-premises-resources"></a>Lokala resurser ####

Om appen inte kan nå en lokal resurs kontrollerar du om du kan nå resursen från ditt virtuella nätverk. Använd **kommandot test-netconnection** PowerShell för att söka efter TCP-åtkomst. Om den virtuella datorn inte kan nå din lokala resurs kanske din VPN- eller ExpressRoute-anslutning inte är korrekt konfigurerad.

Om din virtuella dator värd kan nå ditt lokala system men din app inte kan, är orsaken sannolikt en av följande orsaker:

* Dina vägar är inte konfigurerade med undernätet eller pekar på platsadressintervall i den lokala gatewayen.
* Nätverkssäkerhetsgrupper blockerar åtkomsten för ip-intervallet point-to-site.
* Dina lokala brandväggar blockerar trafiken från ip-intervallet för punkt till plats.
* Du försöker nå en adress som inte är RFC 1918 med hjälp av den regionala funktionen för VNet-integrering.