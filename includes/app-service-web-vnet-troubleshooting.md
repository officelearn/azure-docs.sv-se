---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 652d42d6e2d9e909c3a03bd82a3a36f91bc73807
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419556"
---
Funktionen är lätt att ställa in, men det betyder inte att din upplevelse kommer att vara problemfri. Om du stöter på problem med att komma åt önskad slutpunkt finns det några verktyg som du kan använda för att testa anslutningen från appkonsolen. Det finns två konsoler som du kan använda. Den ena är Kudu-konsolen och den andra är konsolen i Azure-portalen. Om du vill nå Kudu-konsolen från din app går du till **Tools** > **Kudu**. Du kan också nå Kudo-konsolen på [sitename].scm.azurewebsites.net. När webbplatsen har läses in går du till fliken **Felsökningskonsol.** Om du vill komma till den Azure portal-värd konsol från din app, gå till **Verktyg** > **Console**.

#### <a name="tools"></a>Verktyg
Verktygen **ping**, **nslookup**, och **tracert** kommer inte att fungera genom konsolen på grund av säkerhetsbegränsningar. För att fylla tomrummet läggs två separata verktyg till. Om du vill testa DNS-funktioner har vi lagt till ett verktyg med namnet **nameresolver.exe**. Syntax:

    nameresolver.exe hostname [optional: DNS Server]

Du kan använda nameresolver för att kontrollera de värdnamn som appen är beroende av. På så sätt kan du testa om du har något felkonfigurerat med din DNS eller kanske inte har tillgång till DNS-servern. Du kan se den DNS-server som appen använder i konsolen genom att titta på de miljövariabler WEBSITE_DNS_SERVER och WEBSITE_DNS_ALT_SERVER.

Du kan använda nästa verktyg för att testa för TCP-anslutning till en värd- och portkombination. Det här verktyget kallas **tcpping** och syntaxen är:

    tcpping.exe hostname [optional: port]

Den **tcpping** verktyget talar om för dig om du kan nå en viss värd och port. Det kan bara visa framgång om det finns ett program som lyssnar på värd- och portkombinationen, och det finns nätverksåtkomst från din app till den angivna värden och porten.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>Felsöka åtkomst till virtuella nätverksbaserade resurser
Ett antal saker kan hindra din app från att nå en viss värd och port. För det mesta är det en av dessa saker:

* **En brandvägg är i vägen.** Om du har en brandvägg i vägen, träffar du TCP timeout. Tidsgränsen för TCP är 21 sekunder i det här fallet. Använd **tcpping-verktyget** för att testa anslutningen. TCP-timeout kan orsakas av många saker bortom brandväggar, men börja där.
* **DNS är inte tillgängligt.** DNS-timeouten är 3 sekunder per DNS-server. Om du har två DNS-servrar är tidsgränsen 6 sekunder. Använd nameresolver för att se om DNS fungerar. Du kan inte använda nslookup, eftersom den inte använder den DNS som det virtuella nätverket är konfigurerat med. Om det inte går att komma åt kan du ha en brandvägg eller NSG-blockering av åtkomst till DNS eller så kan den vara nere.

Om dessa objekt inte svarar på dina problem, leta först efter saker som:

**Regional VNet-integrering**
* Är din destination en icke-RFC1918-adress och du har inte WEBSITE_VNET_ROUTE_ALL inställd på 1?
* Finns det en NSG som blockerar utgående från ditt integrationsundernät?
* Om du går över Azure ExpressRoute eller en VPN, är din lokala gateway konfigurerad för att dirigera trafik tillbaka upp till Azure? Om du kan nå slutpunkter i det virtuella nätverket men inte lokalt kontrollerar du dina vägar.
* Har du tillräckligt med behörighet för att ange delegering i integrationsundernätet? Under den regionala konfigurationen för VNet-integrering delegeras integrationsundernätet till Microsoft.Web. Användargränssnittet för VNet-integrering delegerar undernätet automatiskt till Microsoft.Web. Om ditt konto inte har tillräcklig nätverksbehörighet för att ange delegering behöver du någon som kan ange attribut i undernätet för integrering för att delegera undernätet. Om du vill delegera integrationsundernätet manuellt går du till undernätverksgränssnittet i Azure Virtual Network och anger delegeringen för Microsoft.Web.

**Gateway-obligatorisk VNet-integrering**
* Är adressintervallet punkt till plats i RFC 1918-intervallen (10.0.0.0-10.255.255.255 / 172.16.0 0,0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* Visas gatewayen som att den finns uppe i portalen? Om din gateway är nere, sedan ta upp den igen.
* Visas certifikat som synkroniserade eller misstänker du att nätverkskonfigurationen har ändrats?  Om certifikaten inte är synkroniserade eller om du misstänker att en ändring har gjorts i den virtuella nätverkskonfigurationen som inte synkroniserades med ASPs väljer du **Synkronisera nätverk**.
* Om du går över ett VPN, är den lokala gatewayen konfigurerad för att dirigera trafik tillbaka upp till Azure? Om du kan nå slutpunkter i det virtuella nätverket men inte lokalt kontrollerar du dina vägar.
* Försöker du använda en samexistens gateway som stöder både peka på plats och ExpressRoute? Samexistens gateways stöds inte med VNet Integration.

Felsökning av nätverksproblem är en utmaning eftersom du inte kan se vad som blockerar åtkomsten till en viss kombination av värd:port. Några orsaker är:

* Du har en brandvägg på värden som förhindrar åtkomst till programporten från ip-intervallet för punkt till plats. Att korsa undernät kräver ofta allmänhetens tillgång.
* Din målvärd är nere.
* Din ansökan är nere.
* Du hade fel IP eller värdnamn.
* Ditt program lyssnar på en annan port än vad du förväntade dig. Du kan matcha ditt process-ID med lyssningsporten med hjälp av "netstat -aon" på slutpunktsvärden.
* Nätverkssäkerhetsgrupperna är konfigurerade på ett sådant sätt att de förhindrar åtkomst till programvärden och porten från ip-intervallet för punkt till plats.

Du vet inte vilken adress din app faktiskt använder. Det kan vara vilken adress som helst i undernätet för integrering eller punkt-till-plats-adressintervallet, så du måste tillåta åtkomst från hela adressintervallet.

Ytterligare felsökningssteg är:

* Anslut till en virtuell dator i det virtuella nätverket och försök nå din resursvärd:port därifrån. Om du vill testa för TCP-åtkomst använder du **powershell-kommandot test-netconnection**. Syntax:

      test-netconnection hostname [optional: -Port]

* Ta fram ett program på en virtuell dator och testa åtkomsten till värden och porten från konsolen från appen med hjälp av **tcpping**.

#### <a name="on-premises-resources"></a>Lokala resurser ####

Om appen inte kan nå en lokal resurs kontrollerar du om du kan nå resursen från det virtuella nätverket. Använd **kommandot test-netconnection** PowerShell för att söka efter TCP-åtkomst. Om den virtuella datorn inte kan nå din lokala resurs kanske din VPN- eller ExpressRoute-anslutning inte är korrekt konfigurerad.

Om den virtuella nätverksbaserade virtuella datorn kan nå ditt lokala system men inte appen kan, är orsaken sannolikt en av följande orsaker:

* Dina rutter är inte konfigurerade med undernätet eller punkt-till-plats-adressintervall i din lokala gateway.
* Nätverkssäkerhetsgrupper blockerar åtkomsten för ip-intervallet från punkt till plats.
* Dina lokala brandväggar blockerar trafiken från ip-intervallet från punkt till plats.
* Du försöker nå en adress som inte är RFC 1918 med hjälp av den regionala funktionen för VNet-integrering.