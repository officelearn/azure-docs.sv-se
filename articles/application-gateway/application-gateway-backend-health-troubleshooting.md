---
title: Felsöka hälsoproblem för bakåtsträvning i Azure Application Gateway
description: Beskriver felsÃ¶kning av problem med ã¶1sã¶kning av fjärrsÃ¤gende för Azure Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: a16120194b1b8015466005f42336828c2b4ace6c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983848"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Felsöka hälsoproblem för bakåtsträvning i Programgateway
==================================================

<a name="overview"></a>Översikt
--------

Som standard avsöker Azure Application Gateway serverdservrar för att kontrollera deras hälsotillstånd och för att kontrollera om de är redo att betjäna begäranden. Användare kan också skapa anpassade avsökningar för att nämna värdnamnet, sökvägen som ska avsökas och de statuskoder som ska accepteras som felfria. I varje fall, om servergången inte svarar korrekt, markerar Application Gateway servern som fel och slutar vidarebefordra begäranden till servern. När servern har börjat svara återupptas vidarebefordran av begäranden.

### <a name="how-to-check-backend-health"></a>Så här kontrollerar du rygghälsa

Om du vill kontrollera hälsotillståndet för din backend-pool kan du använda sidan **Backend Health** på Azure-portalen. Du kan också använda [Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0) [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)eller [REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth).

Statusen som hämtas av någon av dessa metoder kan vara någon av följande:

- Felfri

- Inte felfri

- Okänt

Om serverendhälsan för en server är felfri betyder det att Application Gateway vidarebefordrar begäranden till den servern. Men om serverda hälsotillståndet för alla servrar i en servergrupp är ohälsosamt eller okänt, kan du stöta på problem när du försöker komma åt program. I den här artikeln beskrivs symptom, orsak och upplösning för vart och ett av de fel som visas.

<a name="backend-health-status-unhealthy"></a>Serverningshälsostatus: Ohälsosamt
-------------------------------

Om serverdhälsan är felaktig liknar portalvyn följande skärmbild:

![Programgateway-serverdhälsa - Felfritt](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Eller om du använder en Azure PowerShell-, CLI- eller Azure REST API-fråga får du ett svar som liknar följande:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
När du har fått en felaktig serverserverstatus för alla servrar i en servergrupp vidarebefordras inte begäranden till servrarna och Application Gateway returnerar felet "502 Bad Gateway" till den begärande klienten. Om du vill felsöka problemet kontrollerar du kolumnen **Information** på fliken **Serverdhälsa.**

Meddelandet som visas i kolumnen **Information** ger mer detaljerade insikter om problemet, och baserat på dessa kan du börja felsöka problemet.

> [!NOTE]
> Standardavsökningsbegäran skickas i \<formatet\>protokoll ://127.0.0.1:\<port\>/. Till exempel http://127.0.0.1:80 för en http-avsökning på port 80. Endast HTTP-statuskoder för 200 till 399 anses vara felfria. Protokoll- och målporten ärvs från HTTP-inställningarna. Om du vill att Application Gateway ska avsöka på ett annat protokoll, värdnamn eller sökväg och känna igen en annan statuskod som Felfri konfigurerar du en anpassad avsökning och associerar den med HTTP-inställningarna.

<a name="error-messages"></a>Felmeddelanden
------------------------
#### <a name="backend-server-timeout"></a>Timeout för serverservern

**Meddelande:** Tid som serveraden tar för\'att svara på programgatewayens hälsoavsökning är mer än tidsgränsen i avsökningsinställningen.

**Orsak:** När Application Gateway skickar en HTTP-avsökningsbegäran till serverdservern väntar den på ett svar från serverdservern under en konfigurerad period. Om servergången inte svarar inom den konfigurerade perioden (timeout-värdet) markeras den som felfritt tills den börjar svara inom den konfigurerade timeout-perioden igen.

**Upplösning:** Kontrollera varför server- eller programservern inte svarar inom den konfigurerade timeout-perioden och även kontrollerar programberoendena. Kontrollera till exempel om databasen har några problem som kan utlösa en fördröjning i svaret. Om du är medveten om programmets beteende och det bör svara först efter timeout-värdet, öka timeout-värdet från de anpassade avsökningsinställningarna. Du måste ha en anpassad avsökning för att kunna ändra timeout-värdet. Information om hur du konfigurerar en anpassad avsökning [finns på dokumentationssidan](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Så här ökar du timeout-värdet:

1.  Öppna serverdservern direkt och kontrollera hur länge servern har tagit tid att svara på den sidan. Du kan använda vilket verktyg som helst för att komma åt serverdingsservern, inklusive en webbläsare med hjälp av utvecklarverktyg.

1.  När du har listat ut hur mycket tid det tar för programmet att svara väljer du fliken **Hälsoavsökningar** och väljer sedan den avsökning som är associerad med HTTP-inställningarna.

1.  Ange alla timeout-värden som är större än programmets svarstid, i sekunder.

1.  Spara de anpassade avsökningsinställningarna och kontrollera om serverdhälsan visas som Felfri nu.

#### <a name="dns-resolution-error"></a>DNS-lösningsfel

**Meddelande:** Application Gateway kunde inte skapa en avsökning för den här serveringsavgången. Detta inträffar vanligtvis när det fullständiga domännamnet (FQDN) för serverdelen inte har angetts på korrekt sätt. 

**Orsak:** Om serverdelspoolen är av typen IP-adress/FQDN eller AppTjänst, matchar Application Gateway till IP-adressen för FQDN som anges via DNS (Domain Name System) (anpassad eller Azure standard) och försöker ansluta till servern på TCP-porten som nämns i HTTP-inställningarna. Men om det här meddelandet visas tyder det på att Application Gateway inte kunde lösa IP-adressen för den angivna FQDN-adressen.

**Upplösning:**

1.  Kontrollera att FQDN som anges i backend-poolen är korrekt och att det är en allmän domän och försök sedan lösa det från den lokala datorn.

1.  Om du kan lösa IP-adressen kan det vara något fel på DNS-konfigurationen i det virtuella nätverket.

1.  Kontrollera om det virtuella nätverket är konfigurerat med en anpassad DNS-server. Om så är fallet kontrollerar du DNS-servern om varför den inte kan matcha till IP-adressen för det angivna FQDN.If it is, check the DNS server about why it can't resolve to the IP address of the specified FQDN.

1.  Om du använder Azure standard DNS, kontrollera med ditt domännamn registrator om huruvida korrekt A-post eller CNAME-postmappning har slutförts.

1.  Om domänen är privat eller intern försöker du lösa den från en virtuell dator i samma virtuella nätverk. Om du kan lösa det startar du om Application Gateway och kontrollerar igen. Om du vill starta om Application Gateway måste du [stoppa](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) och [börja](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) med att använda PowerShell-kommandona som beskrivs i dessa länkade resurser.

#### <a name="tcp-connect-error"></a>TCP-anslutningsfel

**Meddelande:** Programgatewayen kunde inte ansluta till backend.
Kontrollera att serverningsgången svarar på den port som används för avsökningen.
Kontrollera också om någon NSG/UDR/Firewall blockerar åtkomsten till ip- och porten för den här backend

**Orsak:** Efter DNS-matchningsfasen försöker Application Gateway ansluta till serveråtkomstservern på TCP-porten som är konfigurerad i HTTP-inställningarna. Om Application Gateway inte kan upprätta en TCP-session på den angivna porten markeras avsökningen som fel med det här meddelandet.

**Lösning:** Om det här felet visas gör du så här:

1.  Kontrollera om du kan ansluta till serverdaservern på porten som nämns i HTTP-inställningarna med hjälp av en webbläsare eller PowerShell. Kör till exempel följande kommando:`Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Om den nämnda porten inte är den önskade porten anger du rätt portnummer för Application Gateway för att ansluta till serveråtkomstservern

1.  Om du inte kan ansluta på porten från din lokala dator också, då:

    a.  Kontrollera inställningarna för nätverkssäkerhetsgruppen (NSG) för serverdelsserverns nätverkskort och undernät och om inkommande anslutningar till den konfigurerade porten är tillåtna. Om de inte är det skapar du en ny regel som tillåter anslutningarna. Mer information om hur du skapar NSG-regler [finns på dokumentationssidan](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Kontrollera om NSG-inställningarna för undernätet Application Gateway tillåter utgående offentlig och privat trafik, så att en anslutning kan göras. Läs dokumentsidan som finns i steg 3a om du vill veta mer om hur du skapar NSG-regler.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Kontrollera de användardefinierade inställningarna för udr -inställningar (Application Gateway) och serverdelsserverns undernät för eventuella routningsavvikelser. Kontrollera att UDR inte dirigerar trafiken bort från backend-undernätet. Kontrollera till exempel om det finns vägar till virtuella nätverksinstallationer eller standardvägar som annonseras till undernätet Application Gateway via Azure ExpressRoute och/eller VPN.

    d.  Om du vill kontrollera de gällande vägarna och reglerna för ett nätverkskort kan du använda följande PowerShell-kommandon:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Om du inte hittar några problem med NSG eller UDR kontrollerar du serveråtkomstservern för programrelaterade problem som hindrar klienter från att upprätta en TCP-session på de portar som konfigurerats. Några saker att kontrollera:

    a.  Öppna en kommandotolk (Win+R\> `netstat`- cmd), ange och välj Retur.

    b.  Kontrollera om servern lyssnar på porten som är konfigurerad. Ett exempel:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Om den inte lyssnar på den konfigurerade porten kontrollerar du inställningarna för webbservern. Till exempel: platsbindningar i IIS, serverblock i NGINX och virtuell värd i Apache.

    d.  Kontrollera inställningarna för os-brandväggen för att se till att inkommande trafik till porten är tillåten.

#### <a name="http-status-code-mismatch"></a>HTTP-statuskod matchar inte

**Meddelande:** Statuskoden för serverd\'s HTTP-svar matchade inte avsökningsinställningen. Förväntat:{HTTPStatusCode0} mottaget:{HTTPStatusCode1}.

**Orsak:** När TCP-anslutningen har upprättats och en TLS-handskakning har slutförts (om TLS är aktiverat) skickar Application Gateway avsökningen som en HTTP GET-begäran till serverdservern. Som beskrivits tidigare kommer standardavsökningen att \<vara protokoll\>://127.0.0.1:\<port\>/, och den anser att svarsstatuskoder i rage 200 till 399 är felfria. Om servern returnerar någon annan statuskod markeras den som fel med det här meddelandet.

**Lösning:** Beroende på serverdserverns svarskod kan du vidta följande åtgärder. Några av de gemensamma statuskoderna listas här:

| **Fel** | **Åtgärder** |
| --- | --- |
| Avsökningsstatuskoden matchar inte: Mottaget 401 | Kontrollera om serveråtkomstservern kräver autentisering. Programgatewayavsökningar kan inte skicka autentiseringsuppgifter för autentisering just nu. Tillåt \"antingen HTTP 401\" i en avsökningsstatuskodmatchning eller avsökning till en sökväg där servern inte kräver autentisering. | |
| Avsökningsstatuskoden matchar inte: Mottaget 403 | Tillträde förbjudet. Kontrollera om åtkomst till sökvägen är tillåten på serverdservern. | |
| Avsökningsstatuskoden matchar inte: Mottaget 404 | Sidan hittades inte. Kontrollera om värdnamnssökvägen är tillgänglig på serverdservern. Ändra värdnamn eller sökvägsparameter till ett tillgängligt värde. | |
| Avsökningsstatuskoden matchar inte: Mottaget 405 | Avsökningsbegäranden för Application Gateway använder HTTP GET-metoden. Kontrollera om servern tillåter den här metoden. | |
| Avsökningsstatuskoden matchar inte: Mottaget 500 | Internt serverfel. Kontrollera serverdelens hälsotillstånd och om tjänsterna körs. | |
| Avsökningsstatuskoden matchar inte: Mottaget 503 | Tjänsten är inte tillgänglig. Kontrollera serverdelens hälsotillstånd och om tjänsterna körs. | |

Eller, om du tror att svaret är legitimt och du vill att Application Gateway ska acceptera andra statuskoder som Felfria, kan du skapa en anpassad avsökning. Den här metoden är användbar i situationer där serverdwebbplatsen behöver autentisering. Eftersom avsökningsbegäranden inte innehåller några användaruppgifter misslyckas de och en HTTP 401-statuskod returneras av serverdservern.

Om du vill skapa en anpassad avsökning följer du [dessa steg](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

#### <a name="http-response-body-mismatch"></a>HTTP-svarstexten matchar inte

**Meddelande:** Brödtexten för\'serverdelens HTTP-svar matchade inte avsökningsinställningen. Den mottagna svarstexten innehåller inte {string}.

**Orsak:** När du skapar en anpassad avsökning har du ett alternativ för att markera en serverdelsserver som felfri genom att matcha en sträng från svarstexten. Du kan till exempel konfigurera Application Gateway för att acceptera "obehörig" som en sträng som matchar. Om serversvaret för serverservern för avsökningsbegäran innehåller strängen **obehörig**markeras den som Felfri. Annars markeras den som fel med det här meddelandet.

**Lösning:** Så här löser du problemet:

1.  Öppna serverdelsservern lokalt eller från en klientdator på avsökningssökvägen och kontrollera svarstexten.

1.  Kontrollera att svarstexten i den anpassade avsökningskonfigurationen för programgateway matchar det som är konfigurerat.

1.  Om de inte matchar ändrar du avsökningskonfigurationen så att den har rätt strängvärde att acceptera.

Läs mer om [avsökningsmatchning av programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching).

#### <a name="backend-server-certificate-invalid-ca"></a>Serverservercertifikat ogiltig certifikatutfärdare

**Meddelande:** Servercertifikatet som används av serverdaten signeras inte av en välkänd certifikatutfärdarcertifikatutfärdaren. Vitlista serverdelen på Application Gateway genom att ladda upp rotcertifikatet för servercertifikatet som används av serverdelen.

**Orsak:** End-to-end SSL med Application Gateway v2 kräver att serverdelsserverns certifikat verifieras för att servern ska vara felfri.
För att ett TLS/SSL-certifikat ska vara tillförlitligt måste det certifikatet för serverdservern utfärdas av en certifikatutfärdar som ingår i det betrodda arkivet för Application Gateway. Om certifikatet inte har utfärdats av en betrodd certifikatutfärdare (till exempel om ett självsignerat certifikat användes) bör användare överföra utfärdarens certifikat till Application Gateway.

**Lösning:** Följ dessa steg för att exportera och överföra det betrodda rotcertifikatet till Application Gateway. (De här stegen gäller För Windows-klienter.)

1.  Logga in på datorn där ditt program finns.

1.  Välj Win+R eller högerklicka på **Start-knappen** och välj sedan **Kör**.

1.  Ange `certmgr.msc` och välj Retur. Du kan också söka efter Certifikathanteraren på **Start-menyn.**

1.  Leta reda på `\Certificates - Current User\\Personal\\Certificates\`certifikatet, vanligtvis i , och öppna det.

1.  Markera rotcertifikatet och välj visa **certifikat**.

1.  Välj fliken **Information** i egenskaperna Certifikat.

1.  På fliken **Information** väljer du alternativet **Kopiera till fil** och sparar filen i base-64-kodade X.509 (. CER)-format.

1.  Öppna **http-inställningssidan** för programgateway i Azure-portalen.

1. Öppna HTTP-inställningarna, välj **Lägg till certifikat**och leta reda på certifikatfilen som du just sparade.

1. Välj **Spara** om du vill spara HTTP-inställningarna.

Du kan också exportera rotcertifikatet från en klientdator genom att direkt komma åt servern (kringgå Application Gateway) via webbläsaren och exportera rotcertifikatet från webbläsaren.

Mer information om hur du extraherar och överför betrodda rotcertifikat i Application Gateway finns i [Exportera betroddt rotcertifikat (för v2 SKU)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Felaktig matchning av tillförlitligt rotcertifikat

**Meddelande:** Rotcertifikatet för servercertifikatet som används av serverkopplingen matchar inte det betrodda rotcertifikat som lagts till i programgatewayen. Se till att du lägger till rätt rotcertifikat för att vitlista serverdelen

**Orsak:** End-to-end SSL med Application Gateway v2 kräver att serverdelsserverns certifikat verifieras för att servern ska vara felfri.
För att ett TLS/SSL-certifikat ska vara tillförlitligt måste serveråtkomstservercertifikatet utfärdas av en certifikatutfärdaren som ingår i det betrodda arkivet för Application Gateway. Om certifikatet inte utfärdades av en betrodd certifikatutfärdare (till exempel ett självsignerat certifikat användes), bör användare ladda upp utfärdarens certifikat till Application Gateway.

Certifikatet som har överförts till HTTP-inställningarna för Programgateway måste matcha rotcertifikatet för servercertifikatet för serverservern.

**Lösning:** Om det här felmeddelandet visas finns det en obalans mellan certifikatet som har överförts till Application Gateway och det som har överförts till serverdservern.

Följ steg 1-11 i föregående metod för att överföra rätt betrodda rotcertifikat till Application Gateway.

Mer information om hur du extraherar och överför betrodda rotcertifikat i Application Gateway finns i [Exportera betroddt rotcertifikat (för v2 SKU)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Det här felet kan också uppstå om serverdservern inte utbyter hela kedjan av certifikatet, inklusive Root > Intermediate (om tillämpligt) > Leaf under TLS-handskakningen. Du kan verifiera att du kan använda OpenSSL-kommandon från valfri klient och ansluta till serveråtkomstservern med hjälp av de konfigurerade inställningarna i Application Gateway-avsökningen.

Ett exempel:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Om utdata inte visar hela kedjan av certifikatet som returneras exporterar du certifikatet igen med hela kedjan, inklusive rotcertifikatet. Konfigurera certifikatet på serverdservern. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>Ogiltigt vanligt namn (CN)

**Meddelande:** Det gemensamma namnet (CN) för serverdcertifikatet matchar inte värdhuvudet för avsökningen.

**Orsak:** Programgateway kontrollerar om värdnamnet som anges i HTTP-inställningarna för serverda matchar det i DET CN som visas av serverdaserverns TLS/SSL-certifikat. Detta är Standard_v2 och WAF_v2 SKU beteende. Standard- och WAF-SKU:s servernamnsindikering (SNI) anges som FQDN i servergruppsadressen.

Om det finns en standardavsökning (ingen anpassad avsökning har konfigurerats och associerats) ställs SNI in från värdnamnet som nämns i HTTP-inställningarna. Om "Välj värdnamn från backend-adress" nämns i HTTP-inställningarna, där backend-adresspoolen innehåller ett giltigt FQDN, tillämpas den här inställningen.

Om det finns en anpassad avsökning som är associerad med HTTP-inställningarna kommer SNI att ställas in från värdnamnet som nämns i den anpassade avsökningskonfigurationen. Om **Välj värdnamn från HTTP-inställningar för servernamn** är markerat i den anpassade avsökningen kommer SNI att anges från värdnamnet som nämns i HTTP-inställningarna.

Om **Välj värdnamn från backend-adress** anges i HTTP-inställningarna måste backend-adresspoolen innehålla ett giltigt FQDN.

Om det här felmeddelandet visas matchar inte DEL-av-serverdcertifikatet värdnamnet som konfigurerats i den anpassade avsökningen eller HTTP-inställningarna (om **Välj värdnamn från HTTP-inställningar för servering är markerat).** Om du använder en standardavsökning anges värdnamnet som **127.0.0.1**. Om det inte är ett önskat värde bör du skapa en anpassad avsökning och associera den med HTTP-inställningarna.

**Lösning:**

Följ dessa anvisningar för att lösa problemet.

För Windows:

1.  Logga in på datorn där ditt program finns.

1.  Välj Win+R eller högerklicka på **Start-knappen** och välj **Kör**.

1.  Ange **certmgr.msc** och välj Retur. Du kan också söka efter Certifikathanteraren på **Start-menyn.**

1.  Leta reda på `\Certificates - Current User\\Personal\\Certificates`certifikatet (vanligtvis i) och öppna certifikatet.

1.  Kontrollera certifikatämnet **på**fliken **Detaljer** .

1.  Kontrollera CERTIFIKATETS CN från informationen och ange samma i värdnamnsfältet för den anpassade avsökningen eller i HTTP-inställningarna (om **Välj värdnamn från http-inställningar för servernamn** är markerat). Om det inte är det önskade värdnamnet för din webbplats måste du hämta ett certifikat för den domänen eller ange rätt värdnamn i konfigurationen för anpassad avsökning eller HTTP.

För Linux med OpenSSL:

1.  Kör det här kommandot i OpenSSL:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  Från de egenskaper som visas hittar du CERTIFIKATETS NÄTVERK och anger samma i värdnamnsfältet i http-inställningarna. Om det inte är det önskade värdnamnet för din webbplats måste du hämta ett certifikat för den domänen eller ange rätt värdnamn i konfigurationen för anpassad avsökning eller HTTP.

#### <a name="backend-certificate-is-invalid"></a>Serverningscertifikatet är ogiltigt

**Meddelande:** Serveringscertifikatet är ogiltigt. Aktuellt datum ligger \"inte\" \"inom\" intervallet Giltig från och Giltigt till datum på certifikatet.

**Orsak:** Varje certifikat levereras med ett giltighetsområde och HTTPS-anslutningen är inte säker om inte serverns TLS/SSL-certifikat är giltigt. De aktuella uppgifterna måste vara inom giltigt **från** och **giltigt till** intervall. Om det inte är fallet anses certifikatet vara ogiltigt och det skapar ett säkerhetsproblem där Application Gateway markerar serveråtkomstservern som felfritt.

**Lösning:** Om TLS/SSL-certifikatet har upphört att gälla förnyar du certifikatet med leverantören och uppdaterar serverinställningarna med det nya certifikatet. Om det är ett självsignerat certifikat måste du generera ett giltigt certifikat och överföra rotcertifikatet till HTTP-inställningarna för Programgateway. Det gör du genom att följa dessa steg:

1.  Öppna HTTP-inställningarna för programgateway i portalen.

1.  Välj den inställning som har det utgångna certifikatet, välj **Lägg till certifikat**och öppna den nya certifikatfilen.

1.  Ta bort det gamla certifikatet med hjälp av ikonen **Ta bort** bredvid certifikatet och välj sedan **Spara**.

#### <a name="certificate-verification-failed"></a>Certifikatverifieringen misslyckades

**Meddelande:** Det gick inte att verifiera giltigheten för serverdcertifikatet. Om du vill ta reda på orsaken kontrollerar du OpenSSL-diagnostik för meddelandet som är associerat med felkoden {errorCode}

**Orsak:** Det här felet uppstår när Application Gateway inte kan verifiera certifikatets giltighet.

**Lösning:** LÃ¶s problemet genom att kontrollera att certifikatet på servern har skapats korrekt. Du kan till exempel använda [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) för att verifiera certifikatet och dess egenskaper och sedan försöka läsa upp certifikatet igen till HTTP-inställningarna för Programgateway.

<a name="backend-health-status-unknown"></a>Serverningshälsostatus: okänd
-------------------------------
Om serverdhälsan visas som Okänd kommer portalvyn att likna följande skärmbild:

![Serverningshälsa för programgateway - Okänd](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Detta kan inträffa av en eller flera av följande orsaker:

1.  NSG på undernätet Application Gateway blockerar inkommande åtkomst till portarna 65503-65534 (v1 SKU) eller 65200-65535 (v2 SKU) från "Internet".
1.  UDR:et i undernätet Programgateway är inställt på standardvägen (0.0.0.0/0) och nästa hopp anges inte som "Internet".
1.  Standardvägen annonseras av en ExpressRoute/VPN-anslutning till ett virtuellt nätverk via BGP.
1.  Den anpassade DNS-servern är konfigurerad i ett virtuellt nätverk som inte kan matcha offentliga domännamn.
1.  Application Gateway är i feltillstånd.

**Lösning:**

1.  Kontrollera om din NSG blockerar åtkomsten till portarna 65503-65534 (v1 SKU) eller 65200-65535 (v2 SKU) från **Internet:**

    a.  Välj länken **Virtuellt nätverk/undernät** på fliken **Översikt** för programgateway.

    b.  På fliken **Undernät** i det virtuella nätverket väljer du det undernät där Application Gateway har distribuerats.

    c.  Kontrollera om nsg är konfigurerad.

    d.  Om en NSG är konfigurerad söker du efter NSG-resursen på fliken **Sök** eller under **Alla resurser**.

    e.  I avsnittet **Inkommande regler** lägger du till en inkommande regel för att tillåta målportintervall 65503-65534 för v1 SKU eller 65200-65535 v2 SKU med **källuppsättningen** som **Någon** eller **Internet**.

    f.  Välj **Spara** och kontrollera att du kan visa backend som felfri. Alternativt kan du göra det via [PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

1.  Kontrollera om din UDR har en standardväg (0.0.0.0/0) med nästa hopp inte inställt som **Internet:**
    
    a.  Följ steg 1a och 1b för att bestämma ditt undernät.

    b.  Kontrollera om det finns någon UDR konfigurerad. Om det finns, sök efter resursen i sökfältet eller under **Alla resurser**.

    c.  Kontrollera om det finns några standardvägar (0.0.0.0/0) med nästa hopp inte inställt som **Internet**. Om inställningen är virtuell **installation** eller **virtuell nätverksgateway**måste du se till att den virtuella installationen eller den lokala enheten kan dirigera paketet tillbaka till internetmålet utan att ändra paketet.

    d.  Annars ändrar du nästa hopp till **Internet**, väljer **Spara**och verifierar att du har en sökning efter det att den är hälsotillståndet för säkerhetskopiering.

1.  Standardväg som annonseras av ExpressRoute/VPN-anslutningen till det virtuella nätverket via BGP:

    a.  Om du har en ExpressRoute/VPN-anslutning till det virtuella nätverket via BGP, och om du annonserar en standardväg, måste du se till att paketet dirigeras tillbaka till internetmålet utan att ändra det. Du kan verifiera genom att använda alternativet **AnslutningsfelsÃ¶kning** i Application Gateway-portalen.

    b.  Välj målet manuellt som vilken IP-adress som helst som 1.1.1.1. Ange målporten som vad som helst och verifiera anslutningen.

    c.  Om nästa hopp är virtuell nätverksgateway kan det finnas en standardväg som annonseras via ExpressRoute eller VPN.

1.  Om det finns en anpassad DNS-server som konfigurerats i det virtuella nätverket kontrollerar du att servern (eller servrarna) kan lösa offentliga domäner. Upplösning av offentliga domännamn kan krävas i scenarier där Application Gateway måste nå ut till externa domäner som OCSP-servrar eller för att kontrollera certifikatets återkallningsstatus.

1.  Om du vill kontrollera att Application Gateway är felfri och körs går du till alternativet **Resurshälsa** i portalen och kontrollerar att tillståndet är **felfritt**. Om du ser ett **feltillstånd** eller **försämrat** tillstånd [kontaktar du supporten](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Nästa steg
----------

Läs mer om [diagnostik och loggning av Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
