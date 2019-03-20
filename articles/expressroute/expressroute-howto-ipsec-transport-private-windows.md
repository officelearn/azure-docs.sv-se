---
title: 'Konfigurera IPSec-transportläge för Windows-värdar privat peering: ExpressRoute: Azure | Microsoft Docs'
description: Så här att aktivera IPSec-transportläge mellan virtuella Azure Windows-datorer och lokala Windows-värdar via ExpressRoute privat peering med hjälp av grupprincipobjekt och organisationsenheter.
services: expressroute
author: fabferri
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: fabferri
ms.custom: seodec18
ms.openlocfilehash: d728980517988e2dc39be4e4b64d20157a1aef54
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58121187"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Konfigurera IPSec-transportläge för ExpressRoute privat peering

Den här artikeln hjälper dig att skapa IPsec-tunnlar i transportläge via ExpressRoute privat peering mellan virtuella Azure-datorer som kör Windows och Windows-värdar på plats. Stegen i den här artikeln skapar den här konfigurationen med hjälp av grupprincipobjekt. Det är möjligt att skapa den här konfigurationen utan att använda organisationsenheter (OU) och grupprincipobjekt (GPO), kombinationen av organisationsenheter och GPO: er förenklar kontroll över dina säkerhetsprinciper och gör att du kan snabbt skala upp. I den här artikeln förutsätter vi att du redan har en Active Directory-konfigurationen och att du är van vid att använda organisationsenheter och GPO: er.

## <a name="about-this-configuration"></a>Om den här konfigurationen

Konfigurationen i följande steg kan du använda en enda Azure-nätverk (VNet) med ExpressRoute privat peering. Men den här konfigurationen kan sträcka sig över flera virtuella Azure-nätverk och lokala nätverk. Den här artikeln hjälper dig att definiera en princip för IPsec-kryptering och tillämpa den på en grupp med virtuella Azure-datorer och värdar på plats som ingår i samma OU. Du kan konfigurera kryptering mellan virtuella Azure-datorer (vm1 och vm2) och lokala host1 endast för HTTP-trafik med målport 8080. Olika typer av IPsec-princip kan skapas baserat på dina krav.

### <a name="working-with-ous"></a>Arbeta med organisationsenheter 

Den säkerhetsprincip som är associerade med en Organisationsenhet skickas till datorerna via GPO. Det finns några fördelar med att använda organisationsenheter i stället för att tillämpa principer på en enda värd:

* Associera en princip med en Organisationsenhet garanterar att datorer som tillhör samma OU får samma principer.
* Ändra säkerhetsprincipen som är associerade med OU gäller ändringarna för alla värdar i Organisationsenheten.

### <a name="diagrams"></a>Diagram

Följande diagram visar gränssnittet och tilldelade IP-adressutrymme. Virtuella Azure-datorer och den lokala värden kör Windows 2016. Virtuella Azure-datorer och lokala host1 är en del av samma domän. Virtuella Azure-datorer och lokala värdar kan matcha namn med DNS.

[![1]][1]

Det här diagrammet visar IPsec-tunnlar under överföring i ExpressRoute privat peering.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Arbeta med IPsec-princip

I Windows, kryptering som är associerade med IPsec-principen. IPsec-princip bestämmer vilken IP-trafik är skyddad och säkerhetsmekanism som tillämpas på IP-paket.
**IPSec-principer** består av följande objekt: **Filtrera listor**, **filteråtgärder**, och **säkerhetsregler**.

När du konfigurerar IPsec-principen, är det viktigt att du förstår termer som följande IPSec-princip:

* **IPsec-princip:** En samling regler. Endast en princip kan vara aktiva (”tilldelats”) vid given tidpunkt. Varje princip kan ha en eller flera regler som kan vara aktiva samtidigt. En dator kan tilldelas endast en aktiv IPsec-princip fått tid. I IPsec-princip kan du också definiera flera åtgärder som vidtas i olika situationer. Varje uppsättning med IPsec-regler är associerad med en filterlista som påverkar vilken typ av nätverkstrafik som regeln gäller.

* **Filtrera listor:** Filtrera listor är paket med ett eller flera filter. En lista kan innehålla flera filter. Filter definierar om kommunikation tillåts, skyddad eller blockerad, enligt den IP-adressintervall, protokoll eller portar med specifika protocol. Varje filter som matchar en viss uppsättning villkor. till exempel paket som skickas från ett visst undernät till en viss dator på en specifik målport. När nätverksförhållanden matchar en eller flera av dessa filter, är filterlistan aktiverad. Varje filter har definierats i en specifik filterlista. Filter kan inte delas mellan adressfilterlistor. Dock kan en viss filterlista införlivas i flera IPsec-principer. 

* **Filteråtgärder:** En säkerhetsmetod definierar en uppsättning säkerhetsalgoritmer, protokoll, och viktiga erbjuder under IKE-förhandlingar på en dator. Filteråtgärder är en lista över säkerhetsmetoder, rangordnas i prioritetsordning.  När en dator förhandlar en IPsec-session, godkänner eller skickar förslag baserat på säkerhetsinställningen som lagras i listan över åtgärder.

* **Säkerhetsregler:** Reglerna styr hur och när en IPSec-princip skyddar kommunikationen. Den använder **filterlista** och **filteråtgärder** att skapa en IPsec-regel för att skapa IPsec-anslutning. Varje princip kan ha en eller flera regler som kan vara aktiva samtidigt. Varje regel innehåller en lista över IP-filter och en samling säkerhetsåtgärder som äger rum vid matchning med filterlistan:
  * IP-filteråtgärder
  * Autentiseringsmetoder
  * Inställningar för IP-tunnel
  * Anslutningstyper

[![5]][5]

## <a name="before-you-begin"></a>Innan du börjar

Se till att du uppfyller följande krav:

* Du måste ha en fungerande Active Directory-konfiguration som du kan använda för att implementera grupprincipinställningar. Läs mer om grupprincipobjekt, [grupprincipobjekt](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx).

* Du måste ha en aktiv ExpressRoute-krets.
  * Information om hur du skapar en ExpressRoute-krets finns i [skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md). 
  * Kontrollera att kretsen är aktiverad av anslutningsleverantören. 
  * Kontrollera att du har Azure privat peering har konfigurerats för din krets. Se den [konfigurera routning](expressroute-howto-routing-arm.md) artikeln routning anvisningar. 
  * Kontrollera att du har ett virtuellt nätverk och en virtuell nätverksgateway skapas och helt etablerad. Följ anvisningarna för att [skapar en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). En virtuell nätverksgateway för ExpressRoute använder GatewayType ExpressRoute, inte VPN.

* Den virtuella nätverksgatewayen för ExpressRoute måste vara ansluten till ExpressRoute-kretsen. Mer information finns i [ansluta ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md).

* Kontrollera att de virtuella datorerna Windows Azure har distribuerats till det virtuella nätverket.

* Kontrollera att anslutningen mellan lokala värdar och virtuella Azure-datorer.

* Kontrollera att de virtuella datorerna Windows Azure och lokala värdar ska kunna använda DNS för korrekt namnmatchning.

### <a name="workflow"></a>Arbetsflöde

1. Skapa ett grupprincipobjekt och koppla den till Organisationsenheten.
2. Definiera en IPsec **filteråtgärd**.
3. Definiera en IPsec **Filtreringslista**.
4. Skapa en IPSec-princip med **säkerhetsregler**.
5. Tilldela IPsec Grupprincipobjektet till Organisationsenheten.

### <a name="example-values"></a>Exempelvärden

* **Domännamn:** ipsectest.com

* **OU:** IPSecOU

* **Lokala Windows-dator:** host1

* **Windows Azure virtuella datorer:** vm1, vm2

## <a name="creategpo"></a>1. Skapa ett grupprincipobjekt

1. Öppna snapin-modulen Grupprinciphantering för att skapa ett nytt grupprincipobjekt som är länkade till en Organisationsenhet, och leta upp den Organisationsenhet som Grupprincipobjektet länkas. I det här exemplet heter Organisationsenheten **IPSecOU**. 

   [![9]][9]
2. Välj Organisationsenheten i snapin-modulen för hantering av Grupprincip och högerklicka. I listrutan klickar du på ”**skapa ett grupprincipobjekt i den här domänen och länka det här...** ".

   [![10]][10]
3. Namnge Grupprincipobjektet ett intuitivt namn så att du lätt kan hitta den. Klicka på **OK** skapa och länka Grupprincipobjektet.

   [![11]][11]

## <a name="enablelink"></a>2. Aktivera GPO-länk

Om du vill tillämpa Grupprincipobjektet till Organisationsenheten Grupprincipobjektet inte bara vara kopplat till Organisationsenheten, men länken måste vara aktiverat.

1. Leta upp det Grupprincipobjektet som du skapade, högerklicka och välj **redigera** i listrutan.
2. Om du vill tillämpa Grupprincipobjektet till Organisationsenheten, Välj **länken är aktiverad**.

   [![12]][12]

## <a name="filteraction"></a>3. Definiera IP-filteråtgärd

1. I listrutan, högerklickar du på **IP-säkerhetsprincip på Active Directory**, och klicka sedan på **hantera IP-filtrera listor och filteråtgärder...** .

   [![15]][15]
2. På den ”**hantera filter åtgärder**” klickar du på **Lägg till**.

   [![16]][16]

3. På den **IP-säkerhet filteråtgärd guiden**, klickar du på **nästa**.

   [![17]][17]
4. Namnge Filtreringsåtgärden ett intuitivt namn så att du kan hitta den senare. I det här exemplet heter Filtreringsåtgärden **myEncryption**. Du kan också lägga till en beskrivning. Klicka sedan på **Nästa**.

   [![18]][18]
5. **Förhandla security** kan du definiera beteendet om IPsec kan upprättas med en annan dator. Välj **förhandla security**, klicka sedan på **nästa**.

   [![19]][19]
6. På den **Communicating med datorer som inte stöder IPsec** väljer **Tillåt inte oskyddade kommunikation**, klicka sedan på **nästa**.

   [![20]][20]
7. På den **IP-trafik och säkerhet** väljer **anpassade**, klicka sedan på **inställningar...** .

   [![21]][21]
8. På den **anpassade säkerhetsinställningar för metoden** väljer **dataintegritet och kryptering (ESP): SHA1, 3DES**. Klicka sedan på **OK**.

   [![22]][22]
9. På den **Hantera filteråtgärder** sidan visas som den **myEncryption** filter har lagts till. Klicka på **Stäng**.

   [![23]][23]

## <a name="filterlist1"></a>4. Definiera en IP-filterlista

Skapa en filterlista som anger krypterade HTTP-trafik med målport 8080.

1. För vilka typer av trafik måste krypteras, använda en **IP-filterlistan**. I den **hantera listor över IP-Filter** fliken **Lägg till** att lägga till en ny IP-filterlista.

   [![24]][24]
2. I den **namn:** skriver ett namn för din IP-filterlistan. Till exempel **azure – den lokala-HTTP8080**. Klicka sedan på **Lägg till**.

   [![25]][25]
3. På den **IP filterbeskrivning och spegling egenskapen** väljer **speglad**. Speglad inställning matchar paket i båda riktningarna, vilket gör att för dubbelriktad kommunikation. Klicka sedan på **Nästa**.

   [![26]][26]
4. På den **IP trafikkälla** sidan från den **källadress:** listrutan Välj **en specifik IP-adress eller undernät**. 

   [![27]][27]
5. Ange källadressen **IP-adressen eller undernätet:** IP-trafik, klicka sedan på **nästa**.

   [![28]][28]
6. Ange den **måladress:** IP-adress eller undernät. Klicka sedan på **Nästa**.

   [![29]][29]
7. På den **IP-protokolltyp** väljer **TCP**. Klicka sedan på **Nästa**.

   [![30]][30]
8. På den **IP-protokollet, porten** väljer **från alla portar** och **till den här porten:**. Typ **8080** i textrutan. Dessa inställningar anger HTTP-trafik på målport 8080 krypteras. Klicka sedan på **Nästa**.

   [![31]][31]
9. Visa IP-filterlistan.  Konfigurationen av IP-filterlistan **azure – den lokala-HTTP8080** utlöser kryptering för all trafik som matchar följande kriterier:

   * Alla Källadress i 10.0.1.0/24 (Azure Subnet2)
   * Alla måladresser i 10.2.27.0/25 (lokala undernät)
   * TCP-protokoll
   * Målport 8080

   [![32]][32]

## <a name="filterlist2"></a>5. Redigera IP-filterlistan

Om du vill kryptera samma typ av trafik i motsatt riktning (från den lokala värden för Azure-VM) måste ett andra IP-filter. Processen för hur du konfigurerar för det nya filtret är samma process som du använde för att ställa in den första IP-filtret. De enda skillnaderna är källans undernät och målundernätet.

1. Om du vill lägga till ett nytt IP-filter i listan, Välj **redigera**.

   [![33]][33]
2. På den **IP-filterlistan** klickar du på **Lägg till**.

   [![34]][34]
3. Skapa ett andra IP-filter med inställningarna i följande exempel:

   [![35]][35]
4. När du har skapat den andra IP-adressfilter ser IP-filterlistan ut så här:

   [![36]][36]

Om kryptering krävs mellan en lokal plats och en Azure-undernätet att skydda ett program, i stället för att ändra befintliga IP-filterlistan, kan du lägga till en ny IP-filterlista i stället. Associera 2 IP adressfilterlistor IPSec-principen i samma ger bättre flexibilitet eftersom en specifik IP-filterlistan kan ändras eller tas bort när som helst utan att påverka de andra IP-filter-listorna.

## <a name="ipsecpolicy"></a>6. Skapa en IPsec-säkerhetsprincip 

Skapa en IPSec-princip med säkerhetsregler.

1. Välj den **IPSecurity principer på Active directory** som är associerad med Organisationsenheten. Högerklicka och välj **skapa IP-säkerhetsprincip**.

   [![37]][37]
2. Namn på säkerhetsprincip. Till exempel **princip – azure – den lokala**. Klicka sedan på **Nästa**.

   [![38]][38]
3. Klicka på **nästa** utan att markera kryssrutan.

   [![39]][39]
4. Kontrollera att den **redigera egenskaper för** kryssrutan är markerad och klicka sedan på **Slutför**.

   [![40]][40]

## <a name="editipsec"></a>7. Redigera IPsec-säkerhetsprincip

Lägg till IPSec-principen i den **IP-filterlistan** och **filteråtgärd** som du tidigare har konfigurerat.

1. HTTP-principens egenskaper **regler** fliken **Lägg till**.

   [![41]][41]
2. På sidan Välkommen **nästa**.

   [![42]][42]
3. En regel ger dig möjlighet att definiera IPsec-läge: tunnelläge eller transportläge.

   * Det ursprungliga paketet kapslas tunnelläge, av en uppsättning IP-rubriker. Tunnelläge skyddar interna routningsinformationen genom att kryptera det ursprungliga paketet IP-huvud. Tunnelläge implementeras mellan gateway-servrarna i scenarier med plats-till-plats-VPN. Tunnelläge är i de flesta fall används för slutpunkt till slutpunkt-kryptering mellan värdar.

   * Transportläge krypterar endast nyttolasten och ESP-trailer; IP-huvudet av det ursprungliga paketet inte är krypterad. I transportläge har IP-källan och IP-målet för paket inte ändrats.

   Välj **regeln specificerar inte en tunnel**, och klicka sedan på **nästa**.

   [![43]][43]
4. **Nätverkstyp** definierar vilka nätverks-anslutning har associerats säkerhetsprincipen. Välj **alla nätverksanslutningar**, och klicka sedan på **nästa**.

   [![44]][44]
5. Välj IP-filterlistan som du skapade tidigare, **azure – den lokala-HTTP8080**, och klicka sedan på **nästa**.

   [![45]][45]
6. Markerar du den befintliga **myEncryption** som du skapade tidigare.

   [![46]][46]
7. Windows stöder fyra olika typer av autentiseringar: Kerberos, certifikat, NTLMv2 och i förväg delad nyckel. Eftersom vi arbetar med domänanslutna värdar, väljer **Active Directory-standard (Kerberos V5-protokollet)**, och klicka sedan på **nästa**.

   [![47]][47]
8. Den nya principen skapar säkerhetsregeln: **azure – den lokala-HTTP8080**. Klicka på **OK**.

   [![48]][48]

IPsec-principen kräver att alla HTTP-anslutningar på målport 8080 att använda IPSec-transportläge. Eftersom HTTP är ett protokoll i klartext, säkerhetsprincip aktiverad försäkrar du dig data krypteras när de överförs via ExpressRoute privat peering. IP-säkerhetsprincip för Active Directory är mer komplicerad att konfigurera än Windows-brandväggen med avancerad säkerhet, men den tillåter flera anpassning av IPsec-anslutning.

## <a name="assigngpo"></a>8. Tilldela IPsec Grupprincipobjektet till Organisationsenheten

1. Visa principen. Säkerhetsprincip för gruppen är definierade, men har ännu inte tilldelats.

   [![49]][49]
2. Att tilldela grupprincipen säkerhet till Organisationsenheten **IPSecOU**, högerklicka på säkerhetsprincipen och valde **tilldela**.
   Varje dator här hör till Organisationsenheten har security grupprincipen.

   [![50]][50]

## <a name="checktraffic"></a>Kontrollera trafikkryptering

För att Kolla in kryptering grupprincipobjekt tillämpas på Organisationsenheten kan du installera IIS på alla virtuella Azure-datorer och i host1. Varje IIS är anpassad för att svara på HTTP-förfrågningar på port 8080.
För att verifiera kryptering, kan du installera en nätverksavsökare (till exempel Wireshark) i alla datorer i Organisationsenheten.
Ett powershell-skript fungerar som en HTTP-klient för att generera HTTP-begäranden på port 8080:

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
Följande nätverksbild visar resultaten för den lokala host1 med Visa filter ESP så att den matchar endast krypterad trafik:

[![51]][51]

Om du kör i powershell-skript på-premisies (HTTP-klient), visas en liknande spårning nätverksbild i Azure-VM.

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "Diagram IPsec-transportläge för nätverk via ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "IPsec intressanta trafik"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Windows IPsec-princip"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "organisationsenhet i grupprincipen"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "skapa ett grupprincipobjekt som är associerade med Organisationsenheten"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "tilldela ett namn till Grupprincipobjektet som är associerade med Organisationsenheten"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "redigera Grupprincipobjektet"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "hantera listor för IP-Filter och filteråtgärder"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "lägga till filteråtgärd"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "guiden"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "filteråtgärd namn"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "filteråtgärd"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "ange beteendet är en osäker anslutning har upprättats"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "säkerhetsmekanism"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "anpassad metod för säkerhet"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "listan över filter"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "lägga till en ny IP-filterlista"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "lägga till HTTP-trafik till IP-filter"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "matchning paketet i båda riktningarna"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "valet av källans undernät"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "käll-nätverk"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "Målnätverk"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "protokoll"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "port och målport"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "filterlista"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "IP-filterlistan med HTTP-trafik"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "lägger till ett andra IP-Filter"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP-adressfilter lista sekund post"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "IP-adressfilter lista sekund post"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "skapa IP-säkerhetsprincip"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "namn på IPsec-princip"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "guiden för IPsec-princip"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "redigera av IPsec-princip"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "lägga till nya säkerhetsregel i IPsec-principen"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "skapa en ny säkerhetsregel"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "transportläge"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "nätverkstyp"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "valet av befintlig IP-filterlista"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "valet av befintlig filteråtgärd"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "valet av autentiseringsmetod"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "genomgången av skapandet av säkerhetsprincipen"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "IPsec-princip länkad till Grupprincipobjektet men inte tilldelad"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "IPsec-princip som tilldelats till Grupprincipobjektet"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "avbilda av IPsec-krypterad trafik"
