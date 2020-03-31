---
title: 'Azure ExpressRoute privat peering: Konfigurera IPsec-transportsätt – Windows-värdar'
description: Aktivera IPsec-transportsätt mellan virtuella Azure Windows-datorer och lokala Windows-värdar via ExpressRoute-privat peering med gpu:er och gpu:er.
services: expressroute
author: fabferri
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: fabferri
ms.custom: seodec18
ms.openlocfilehash: 1bc33047d31262af443cddc418853fbacd88aec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022015"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Konfigurera IPsec-transportsätt för ExpressRoute-privat peering

Den här artikeln hjälper dig att skapa IPsec-tunnlar i transportsätt via ExpressRoute privat peering mellan Virtuella Azure-datorer som kör Windows och lokala Windows-värdar. Stegen i den här artikeln skapar den här konfigurationen med hjälp av grupprincipobjekt. Även om det är möjligt att skapa den här konfigurationen utan att använda organisationsenheter (ORGANISATIONSENHETER) och grupprincipobjekt (GPU: er), kommer kombinationen av organisationsenheter och gpu:er att förenkla kontrollen av dina säkerhetsprinciper och gör att du snabbt kan skala upp. Stegen i den här artikeln förutsätter att du redan har en Active Directory-konfiguration och att du är bekant med att använda OUs och GPU: er.

## <a name="about-this-configuration"></a>Om den här konfigurationen

Konfigurationen i följande steg använder ett enda virtuellt Azure-nätverk (VNet) med ExpressRoute-privat peering. Den här konfigurationen kan dock sträcka sig över fler Virtuella Azure-nätverk och lokala nätverk. Den här artikeln hjälper dig att definiera en IPsec-krypteringsprincip och tillämpa den på en grupp virtuella Azure-datorer och värdar lokalt som ingår i samma organisationsenhet. Du konfigurerar kryptering mellan virtuella Azure-datorer (vm1 och vm2) och den lokala värden1 endast för HTTP-trafik med målport 8080. Olika typer av IPsec-princip kan skapas baserat på dina krav.

### <a name="working-with-ous"></a>Arbeta med OUs 

Säkerhetsprincipen som är associerad med en organisationsenhet överförs till datorerna via GPO. Några fördelar med att använda ru: er, snarare än att tillämpa principer på en enda värd, är:

* Att associera en princip med en organisationsenhet garanterar att datorer som tillhör samma organisationsenhet får samma principer.
* Om du ändrar säkerhetsprincipen som är associerad med organisationsenhet tillämpas ändringarna på alla värdar i organisationsenheten.

### <a name="diagrams"></a>Diagram

Följande diagram visar sammankopplingen och tilldelat IP-adressutrymme. De virtuella Azure-datorerna och den lokala värden kör Windows 2016. Virtuella Azure-datorer och den lokala värd1 är en del av samma domän. Virtuella Azure-datorer och lokala värdar kan matcha namn korrekt med DNS.

[![1]][1.]

Det här diagrammet visar IPsec-tunnlarna under transport i ExpressRoute privat peering.

[![4]][4.]

### <a name="working-with-ipsec-policy"></a>Arbeta med IPsec-principen

I Windows associeras kryptering med IPsec-principen. IPsec-principen avgör vilken IP-trafik som är skyddad och säkerhetsmekanismen som tillämpas på IP-paketen.
**IPSec-principer** består av följande objekt: **Filterlistor,** **Filteråtgärder**och **Säkerhetsregler**.

När du konfigurerar IPsec-principen är det viktigt att förstå följande IPsec-principterminologi:

* **IPsec-princip:** En samling regler. Endast en princip kan vara aktiv ("tilldelad") vid en viss tidpunkt. Varje princip kan ha en eller flera regler, som alla kan vara aktiva samtidigt. En dator kan bara tilldelas en aktiv IPsec-princip vid en given tidpunkt. Men inom IPsec-principen kan du definiera flera åtgärder som kan vidtas i olika situationer. Varje uppsättning IPsec-regler är associerade med en filterlista som påverkar den typ av nätverkstrafik som regeln gäller för.

* **Filterlistor:** Filterlistor är bunt med ett eller flera filter. En lista kan innehålla flera filter. Filter definierar om kommunikationen tillåts, skyddas eller blockeras, enligt IP-adressintervall, protokoll eller till och med specifika protokollportar. Varje filter matchar en viss uppsättning villkor. Paket som skickas från ett visst undernät till en viss dator på en viss målport. När nätverksförhållandena matchar ett eller flera av dessa filter aktiveras filterlistan. Varje filter definieras i en specifik filterlista. Filter kan inte delas mellan filterlistor. En viss filterlista kan dock införlivas i flera IPsec-principer. 

* **Filteråtgärder:** En säkerhetsmetod definierar en uppsättning säkerhetsalgoritmer, protokoll och nyckel som en dator erbjuder under IKE-förhandlingar. Filteråtgärder är listor över säkerhetsmetoder, rangordnade i prioritetsordning.  När en dator förhandlar om en IPsec-session accepterar eller skickar den förslag baserat på säkerhetsinställningen som lagras i filteråtgärdslistan.

* **Säkerhetsregler:** Regler styr hur och när en IPsec-princip skyddar kommunikationen. Den använder **filterlista** och **filteråtgärder** för att skapa en IPsec-regel för att skapa IPsec-anslutningen. Varje princip kan ha en eller flera regler, som alla kan vara aktiva samtidigt. Varje regel innehåller en lista över IP-filter och en samling säkerhetsåtgärder som vidtas vid en matchning med den filterlistan:
  * ÅTGÄRDER för IP-filter
  * Autentiseringsmetoder
  * Inställningar för IP-tunnel
  * Anslutningstyper

[![5]][5]

## <a name="before-you-begin"></a>Innan du börjar

Se till att du uppfyller följande förutsättningar:

* Du måste ha en fungerande Active Directory-konfiguration som du kan använda för att implementera grupprincipinställningar. Mer information om grupprincipobjekt finns i [Grupprincipobjekt](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx).

* Du måste ha en aktiv ExpressRoute-krets.
  * Information om hur du skapar en ExpressRoute-krets finns i [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md). 
  * Kontrollera att kretsen är aktiverad av din anslutningsleverantör. 
  * Kontrollera att du har Azure privat peering konfigurerad för din krets. Se [konfigurationsroutningsartikeln](expressroute-howto-routing-arm.md) för routningsinstruktioner. 
  * Kontrollera att du har skapat och etablerat ett virtuellt nätverk gateway. Följ instruktionerna för att [skapa en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). En virtuell nätverksgateway för ExpressRoute använder GatewayType 'ExpressRoute', inte VPN.

* ExpressRoute-gatewayen för virtuellt nätverk måste vara ansluten till ExpressRoute-kretsen. Mer information finns i [Ansluta ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md).

* Kontrollera att virtuella Azure Windows-datorer har distribuerats till det virtuella nätverket.

* Kontrollera att det finns anslutning mellan de lokala värdarna och virtuella Azure-datorerna.

* Kontrollera att virtuella Azure Windows-datorer och lokala värdar kan använda DNS för att korrekt matcha namn.

### <a name="workflow"></a>Arbetsflöde

1. Skapa ett GPO och associera det med organisationsenheten.
2. Definiera en **IPsec-filteråtgärd**.
3. Definiera en **IPsec-filterlista**.
4. Skapa en IPsec-princip med **säkerhetsregler**.
5. Tilldela IPsec-GPO till organisationsenheten.

### <a name="example-values"></a>Exempelvärden

* **Domännamn:** ipsectest.com

* **Ou:** IPSecOU

* **Lokal Windows-dator:** värd1

* **Virtuella Azure Windows-datorer:** vm1, vm2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1. Skapa ett GPO

1. Om du vill skapa ett nytt grupprincipobjekt som är länkat till en organisationsenhet öppnar du snapin-modulen Grupprinciphantering och letar reda på den organisationsenhet som grupprincipobjektet ska länkas till. I exemplet heter organisationsenheten **IPSecOU**. 

   [![9]][9]
2. I snapin-modulen Grupprinciphantering väljer du organisationsenheten och högerklickar. I listrutan klickar du på "**Skapa en GPO i den här domänen och länka den här...**".

   [![10]][10 år]
3. Ge den ett intuitivt namn till den för att ange ett intuitivt namn så att du enkelt kan hitta den senare. Klicka på **OK** om du vill skapa och länka den globala handlingsplanen.

   [![11]][11]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2. Aktivera länken GPO

Om du vill tillämpa den allmänna kopplingen på organisationsenheten måste den globala projekterad vara inte bara kopplad till organisationsenheten, utan länken måste också aktiveras.

1. Leta reda på den nationella handlingsplan som du skapade, högerklicka och välj **Redigera** i listrutan.
2. Om du vill använda den nationella organisationsombudet på organisationsenheten väljer du **Länka aktiverad**.

   [![12]][12 år]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3. Definiera IP-filteråtgärden

1. Högerklicka på **IP-säkerhetsprincipen i Active Directory**i listrutan och klicka sedan på **Hantera IP-filterlistor och filteråtgärder...**.

   [![15]][15 år]
2. Klicka på **Lägg till**på fliken**Hantera filteråtgärder**.

   [![16]][16 år]

3. Klicka på **Nästa**i guiden Åtgärd för **IP-säkerhetsfilter**.

   [![17]][17 år]
4. Ge filteråtgärden ett intuitivt namn ett intuitivt namn så att du kan hitta den senare. I det här exemplet heter filteråtgärden **myEncryption**. Du kan också lägga till en beskrivning. Klicka sedan på **Nästa**.

   [![18]][18 år]
5. **Förhandla säkerhet** kan du definiera beteendet om IPsec inte kan upprättas med en annan dator. Välj **Förhandla om säkerhet**och klicka sedan på **Nästa**.

   [![19]][19 år]
6. På sidan **Kommunicera med datorer som inte stöder IPsec** väljer du Tillåt inte osäker **kommunikation**och klickar sedan på **Nästa**.

   [![20]][20 år]
7. På sidan **IP-trafik och säkerhet** väljer du **Anpassad**och klickar sedan på **Inställningar...**.

   [![Den 21]][21]
8. På sidan **Anpassade inställningar för säkerhetsmetod** väljer du **Dataintegritet och kryptering (ESP): SHA1, 3DES**. Klicka sedan på **OK**.

   [![22]][22]
9. På sidan **Hantera filteråtgärder** kan du se att **filtret myEncryption** har lagts till. Klicka på **Stäng**.

   [![23]][23 år]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4. Definiera en IP-filterlista

Skapa en filterlista som anger krypterad HTTP-trafik med målport 8080.

1. Om du vill kvalificera vilka typer av trafik som måste krypteras använder du en **IP-filterlista**. Klicka på **Lägg till** på fliken **Hantera IP-filterlistor** om du vill lägga till en ny IP-filterlista.

   [![24]][24 år]
2. Skriv ett namn på ip-filterlistan i fältet **Namn:** . Till exempel **azure-onpremises-HTTP8080**. Klicka sedan på **Lägg till**.

   [![25]][25 år]
3. På egenskapssidan **för IP-filterbeskrivning och speglad** väljer du **Speglad**. Den speglade inställningen matchar paket som går i båda riktningarna, vilket möjliggör tvåvägskommunikation. Klicka sedan på **Nästa**.

   [![26]][26 år]
4. På sidan **IP-trafikkälla** väljer du **En specifik IP-adress eller undernät**i listrutan **Källa:** 

   [![27]][27 år]
5. Ange **källadressens IP-adress eller undernät:** för IP-trafiken och klicka sedan på **Nästa**.

   [![28]][28 år]
6. Ange **måladress:** IP-adress eller undernät. Klicka sedan på **Nästa**.

   [![29]][29]
7. Välj **TCP**på sidan **IP Protocol Type** . Klicka sedan på **Nästa**.

   [![30]][30 år]
8. På sidan **IP Protocol Port** väljer du Från valfri **port** och till den **här porten:**. Skriv **8080** i textrutan. Dessa inställningar anger endast HTTP-trafiken på målport 8080 krypteras. Klicka sedan på **Nästa**.

   [![31]][31]
9. Visa IP-filterlistan.  Konfigurationen av **IP-filterlistan azure-onpremises-HTTP8080** utlöser kryptering för all trafik som matchar följande villkor:

   * Alla källadresser i 10.0.1.0/24 (Azure Subnet2)
   * Alla måladresser i 10.2.27.0/25 (lokalt undernät)
   * TCP-protokoll
   * Destinationshamn 8080

   [![32]][32 år]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5. Redigera IP-filterlistan

Om du vill kryptera samma typ av trafik i motsatt riktning (från den lokala värden till den virtuella Azure-datorn) behöver du ett andra IP-filter. Processen för att ställa in det nya filtret är samma process som du använde för att ställa in det första IP-filtret. De enda skillnaderna är källundernätet och målundernätet.

1. Om du vill lägga till ett nytt IP-filter i IP-filterlistan väljer du **Redigera**.

   [![33]][33 år]
2. Klicka på **Lägg till**på sidan **IP-filterlista.**

   [![34]][34 år]
3. Skapa ett andra IP-filter med inställningarna i följande exempel:

   [![35]][35 år]
4. När du har skapat det andra IP-filtret kommer IP-filterlistan att se ut så här:

   [![36]][36 år]

Om kryptering krävs mellan en lokal plats och ett Azure-undernät för att skydda ett program, i stället för att ändra den befintliga IP-filterlistan, kan du lägga till en ny IP-filterlista i stället. Att associera 2 IP-filterlistor till samma IPsec-princip ger bättre flexibilitet eftersom en specifik IP-filterlista kan ändras eller tas bort när som helst utan att påverka de andra IP-filterlistorna.

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6. Skapa en IPsec-säkerhetsprincip 

Skapa en IPsec-princip med säkerhetsregler.

1. Välj **DE IPSecurity-principer i Active Directory** som är associerad med organisationsenheten. Högerklicka och välj **Skapa IP-säkerhetsprincip**.

   [![37]][37 år]
2. Namnge säkerhetsprincipen. Till exempel **princip-azure-onpremises**. Klicka sedan på **Nästa**.

   [![38]][38 år]
3. Klicka på **Nästa** utan att markera kryssrutan.

   [![39]][39 år]
4. Kontrollera att kryssrutan **Redigera egenskaper** är markerad och klicka sedan på **Slutför**.

   [![40]][40 år]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7. Redigera säkerhetsprincipen för IPsec

Lägg till **IP-filterlistan** och **filteråtgärden** som du tidigare har konfigurerat i IPsec-principen.

1. Klicka på **Lägg till**på fliken EGENSKAPER för HTTP-principegenskaper. **Rules**

   [![41]][41]
2. På sidan Välkommen klickar du på **Nästa**.

   [![42]][42 år]
3. En regel ger möjlighet att definiera IPsec-läget: tunnelläge eller transportsätt.

   * I tunnelläge kapslas det ursprungliga paketet in av en uppsättning IP-huvuden. Tunnelläge skyddar den interna routningsinformationen genom att kryptera IP-huvudet för det ursprungliga paketet. Tunnelläget implementeras i stor utsträckning mellan gateways i VPN-scenarier från plats till plats. Tunnelläget används i de flesta fall för end-to-end-kryptering mellan värdar.

   * Transportsättet krypterar endast nyttolasten och ESP-trailern. IP-huvudet för det ursprungliga paketet är inte krypterat. I transportläge är IP-källan och IP-målet för paketen oförändrade.

   Markera **Den här regeln anger inte någon tunnel**och klickar sedan på **Nästa**.

   [![43]][43 år]
4. **Nätverkstyp** definierar vilken nätverksanslutning som associeras med säkerhetsprincipen. Markera **Alla nätverksanslutningar**och klicka sedan på **Nästa**.

   [![44]][44 år]
5. Markera den IP-filterlista som du skapade tidigare, **azure-onpremises-HTTP8080**och klicka sedan på **Nästa**.

   [![45]][45 år]
6. Välj den befintliga **filteråtgärds-minKryptering** som du skapade tidigare.

   [![46]][46 år]
7. Windows stöder fyra olika typer av autentiseringar: Kerberos, certifikat, NTLMv2 och fördelad nyckel. Eftersom vi arbetar med domänanslutna värdar väljer du **Active Directory standard (Kerberos V5-protokoll)** och klickar sedan på **Nästa**.

   [![47]][47 år]
8. Den nya principen skapar säkerhetsregeln: **azure-onpremises-HTTP8080**. Klicka på **OK**.

   [![48]][48 år]

IPsec-principen kräver att alla HTTP-anslutningar på målporten 8080 ska använda IPsec-transportsätt. Eftersom HTTP är ett klartextprotokoll säkerställer säkerhetsprincipen att data krypteras när de överförs via ExpressRoute-privata peering. IP-säkerhetsprincipen för Active Directory är mer komplex att konfigurera än Windows-brandväggen med avancerad säkerhet, men det möjliggör mer anpassning av IPsec-anslutningen.

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8. Tilldela IPsec-given till organisationsenhet

1. Visa principen. Säkerhetsgruppprincipen har definierats, men ännu inte tilldelats.

   [![49]][49]
2. Om du vill tilldela säkerhetsgruppsprincipen till **IPSecOU-organisation av OUSecOU**högerklickar du på säkerhetsprincipen och väljer **Tilldela**.
   Varje dator tht tillhör organisationsenhet kommer att ha säkerhetsgruppsprincipen tilldelad.

   [![50]][50 år]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>Kontrollera trafikkryptering

Om du vill checka ut det gpo-krypteringsobjekt som tillämpas på organisationsenhetsenheten installerar du IIS på alla virtuella Azure-datorer och i värd1. Varje IIS är anpassad för att svara på HTTP-begäranden på port 8080.
Om du vill verifiera kryptering kan du installera en nätverkssniffare (som Wireshark) på alla datorer i organisationsenheten.
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
Följande nätverksinsamling visar resultaten för lokal värd1 med displayfilter ESP för att matcha endast den krypterade trafiken:

[![51]][51]

Om du kör POWERSHELL-skriptet on-premisies (HTTP-klient) visar nätverksfångsten i Azure VM en liknande spårning.

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "nätverksdiagram IPsec transportsätt via ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "IPsec intressant trafik"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Windows IPsec-princip"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "Organisationsenhet i koncernpolicyn"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "skapa ett GPO som är associerat med organisationsenheten"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "tilldela ett namn till det gpo som är associerat med organisationsenheten"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "redigera GPO"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "Hantera IP-filterlistor och filteråtgärder"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "lägg till filteråtgärd"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Åtgärdsguiden"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "Namn på filteråtgärd"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "Filter åtgärd"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "ange beteendet är en osäker anslutning upprättas"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "säkerhetsmekanism"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "Anpassad säkerhetsmetod"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "filteråtgärdslista"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "Lägg till en ny IP-filterlista"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "Lägga till HTTP-trafik i IP-filtret"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "match paket i båda riktningarna"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "val av källundernätet"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "Källa Nätverk"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "Målnätverk"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "Protokoll"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "källhamn och destinationshamn"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "filterlista"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "IP-filterlista med HTTP-trafik"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "Lägga till ett andra IP-filter"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP-filter list-andra post"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "IP-filter list-andra post"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "skapa IP-säkerhetsprincipen"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "namn på IPsec-principen"
[Principguiden för 39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "IPsec"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "redigering av IPsec-principen"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "lägg till ny säkerhetsregel i IPsec-principen"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "skapa en ny säkerhetsregel"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "Transportsätt"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "Nätverkstyp"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "val av befintlig IP-filterlista"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "val av befintlig filteråtgärd"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "val av autentiseringsmetod"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "avsluta processen för att skapa säkerhetspolitiken"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "IPsec-princip kopplad till GPO men inte tilldelad"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "IPsec-princip som tilldelats GPO"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "Fånga av IPsec krypterad trafik"
