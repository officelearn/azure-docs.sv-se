---
title: Felsök problem med Server delens hälsa i Azure Application Gateway
description: Den här artikeln vägleder dig vid fel sökning av Server dels hälso problem för Azure Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: 1fd4e9156e29133b1db4fe9ab9a0825eb1aa3b55
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097583"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Felsök problem med Server delens hälsa i Application Gateway
==================================================

<a name="overview"></a>Översikt
--------

Application Gateway som standard avsöker backend-servrarna för att kontrol lera hälso statusen och om de är redo att betjäna begär Anden. Användare kan skapa anpassade avsökningar och ange värd namnet, sökvägen som ska avsökas och status koderna ska godkännas som felfria. I båda fallen, om backend-servern inte svarar utan problem, kommer Application Gateway att markera servern som ohälsosam och sluta vidarebefordra begäran till servern. När servern har börjat svara kommer den att fortsätta att betjäna begär Anden.

### <a name="how-to-check-backend-health"></a>Så här kontrollerar du Server dels hälsa

Om du vill kontrol lera hälso tillståndet för din backend-pool kan du använda sidan "Server dels hälsa" i Azure Portal. Du kan också använda [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0), [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)eller [REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth) för att hämta hälso tillståndet. Läs den länkade artikeln mot varje metod för att få mer information.

Statusen som hämtats av någon metod som anges ovan kan vara av tre typer, som är:

1.  Felfri

2.  Skadad

3.  Okänt

Om Server delens hälso status är felfri, betyder det att Application Gateway vidarebefordrar begär anden till den servern. Men om Server delens hälso tillstånd för alla servrar i en backend-pool inte är felfria eller okända, kan det uppstå problem under program åtkomsten. Det här dokumentet beskriver symptom, orsak och lösning för varje fel som visas när backend-servrarna kan vara felaktiga eller okända.

<a name="backend-health-status-unhealthy"></a>Hälso status för Server delen är inte felfri
-------------------------------

Om Server delens hälso status visas som ohälsosam, visas den på portalen som skärm bilden nedan:

![Application Gateway server dels hälsa – ej felfri](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Om du använder Azure PowerShell-, CLI-eller Azure REST API-fråga visas ett svar som liknar det nedan:
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
När du ser backend-serverns status som ohälsosam för alla servrar i en backend-pool vidarebefordras inte begär anden till dem och Application Gateway returnerar 502 Felaktig gateway-fel till den begär ande klienten. Felsök problemet genom att kontrol lera kolumnen information på fliken Server dels hälsa.

Det meddelande som visas i kolumnen information på fliken Server dels hälsa ger mer detaljerade insikter om problemet och utifrån dem kan vi börja felsöka problemet.

> [!NOTE]
> Standard avsöknings förfrågan skickas i formatet \<protokoll\>://127.0.0.1:\<port\>/, till exempel <http://127.0.0.1/> för en http-avsökning på port 80 och endast ett svar på HTTP-statuskod 200-399 som felfritt svar. Protokollet och mål porten ärvs från HTTP-inställningar. Om du vill Application Gateway avsöka på ett annat protokoll, värdnamn eller sökväg och acceptera en annan status kod som felfri, konfigurerar du en anpassad avsökning och kopplar den till HTTP-inställningarna.

<a name="error-messages"></a>Felmeddelanden
------------------------
#### <a name="backend-server-timeout"></a>Timeout för backend-server

**Meddelande** Tiden det tar för Server delen att svara på Application\'Gateway s hälso avsökning är över tröskelvärdet för avsöknings inställningen.

**Orsak** När Application Gateway skickar en HTTP (S)-avsöknings förfrågan till backend-servern väntar den på svar från backend-servern under en viss tids period som kon figurer ATS. Om backend-servern inte svarar inom timeout-värdet markeras den som ej felfri tills den börjar svara inom tids gränsen.

**Lösning** Kontrol lera på backend-servern eller programmet att det inte svarar inom timeout-konfigurationen och kontrol lera även program beroenden, till exempel om databasen har problem, vilket kan leda till fördröjningen i svaret. Om du är medveten om programmets beteende och det bara ska svara efter timeout-värdet, ökar du timeout-värdet från inställningarna för anpassad avsökning.
Du måste ha en anpassad avsökning för att ändra värdet för timeout. Om du vill veta hur du konfigurerar en anpassad avsökning, [Se sidan dokumentation](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Om du vill öka tids gränsen följer du stegen nedan:

1.  Få åtkomst till backend-servern direkt och kontrol lera hur lång tid det tar för servern att svara på den sidan. Du kan använda valfritt verktyg för att komma åt, inklusive en webbläsare med utvecklarverktyg.

2.  När du har tagit reda på hur lång tid det tar för programmet att svara klickar du på fliken hälso avsökningar och väljer den avsökning som är associerad med dina HTTP-inställningar.

3.  Ange ett högre timeout-värde än programmets svars tid i sekunder.

4.  Spara inställningarna för anpassad avsökning och kontrol lera Server dels hälsan om den visas felfritt nu.

#### <a name="dns-resolution-error"></a>DNS-matchnings fel

**Meddelande** Det gick inte att skapa en avsökning för den här server delen i Application Gateway. Detta inträffar vanligt vis när FQDN för Server delen inte har angetts korrekt. 

**Orsak** Om backend-poolen är av typen IP-adress/FQDN eller App Service, matchar Application Gateway IP-adressen för FQDN som anges med DNS-server (anpassat eller Azure standard) och försöker ansluta till servern på den TCP-port som anges i HTTP-inställningarna. Men om det här meddelandet visas föreslår det att Application Gateway inte kunde matcha IP-adressen för det FQDN som angavs.

**Lösning**

1.  Kontrol lera att det fullständiga domän namnet som angavs i backend-poolen är korrekt och om det är en offentlig domän, försök att lösa problemet från den lokala datorn.

2.  Om du kan matcha IP-adressen kan det vara något fel med DNS-konfigurationen i VNet.

3.  Kontrol lera om VNet har kon figurer ATS med en anpassad DNS-server. I så fall kan du kontrol lera DNS-servern på varför den inte kan matcha IP-adressen för angivet FQDN.

4.  Om det är Azure standard DNS, kontrollerar du med domän namns registratorn om rätt A-post eller CNAME-Postmappning har gjorts.

5.  Om domänen är privat/intern, försöker du lösa den från en virtuell dator i samma VNet och om du kan lösa det startar du om Application Gateway och kontrollerar igen. Om du vill starta om Application Gateway måste du [stoppa](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) och [börja](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) använda PowerShell-kommandona som finns i respektive dokument länkar.

#### <a name="tcp-connect-error"></a>TCP Connect-fel

**Meddelande** Application Gateway kunde inte ansluta till Server delen.
Kontrol lera att Server delen svarar på den port som används för avsökningen.
Kontrol lera också om någon NSG/UDR/brand vägg blockerar åtkomsten till IP-adressen och porten för den här server delen

**Orsak** Efter DNS-lösnings fasen försöker Application Gateway ansluta till backend-servern på den TCP-port som är konfigurerad i HTTP-inställningarna. Om Application Gateway inte kan upprätta en TCP-session på den angivna porten markeras avsökningen som ohälsosam med det här meddelandet.

**Lösning:** Om det här felet visas kontrollerar du följande:

1.  Kontrol lera om du kan ansluta till backend-servern på den port som anges i HTTP-inställningarna med hjälp av en webbläsare eller med hjälp av PowerShell, kan du till exempel använda kommandot: Test-NetConnection-ComputerName www.bing.com-port 443

2.  Om den port som anges inte är den önskade porten anger du rätt port nummer för Application Gateway för att ansluta till backend-servern

3.  Om du inte kan ansluta till porten från den lokala datorn kan du också:

    a.  Kontrol lera NSG-inställningarna för backend-serverns nätverkskort och undernät och kontrol lera om de inkommande anslutningarna till den konfigurerade porten är tillåtna. Om de inte är tillåtna skapar du en ny regel som tillåter anslutningarna. Information om hur du skapar NSG-regler finns [i dokumentations sidan](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Kontrol lera NSG-inställningarna för Application Gatewayens undernät om utgående offentlig och privat trafik tillåts, så att anslutningen kan göras. Se dokumentet som är länkat i (a) om du vill veta mer om hur du skapar NSG-regler.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Kontrol lera UDR-inställningarna för Application Gateway och backend-serverns undernät för eventuella väg avvikelser. Se till att UDR inte dirigerar trafiken från backend-undernätet. Sök till exempel efter vägar till virtuella nätverks enheter eller standard vägar som annonseras till Application Gateway-undernätet via ExpressRoute/VPN.

    d.  Om du vill kontrol lera de effektiva vägarna och reglerna för ett nätverkskort kan du använda följande PowerShell-kommandon.
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
4.  Om du inte hittar några problem med NSG eller UDR kontrollerar du Server dels servern för programrelaterade problem på grund av vilka klienter inte kan upprätta en TCP-session på de portar som kon figurer ATS. Några saker att kontrol lera:

    e.  Öppna kommando tolken (Win + R\> -cmd) och ange netstat och tryck på RETUR

    f.  Kontrol lera om servern lyssnar på den port som har kon figurer ATS.
        Exempel:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    g.  Om det inte är det kontrollerar du inställningarna för webb servern, till exempel webbplats bindningar i IIS, Server block i NGINX och virtuell värd i Apache

    h.  Kontrol lera inställningarna för OS-brandväggen för att se till att inkommande trafik till porten tillåts

#### <a name="http-status-code-mismatch"></a>Felaktig HTTP-statuskod

**Meddelande** Status koden för Server delens\'http-svar matchade inte avsöknings inställningen. Förväntades: {HTTPStatusCode0} togs emot: {HTTPStatusCode1}.

**Orsak** När TCP-anslutningen har upprättats och SSL-handskakning har gjorts (om SSL är aktiverat) skickar Application Gateway avsökningen som en HTTP GET-begäran till backend-servern. Som nämnts ovan är standard avsökningen till \<protokoll\>://127.0.0.1:\<port\>/, och den tar hänsyn till svars status koder i Rage 200-399 som felfri. Om servern returnerar någon annan status kod så markeras den som ohälsosam med det här meddelandet.

**Lösning:** Beroende på backend-serverns svarskod kan du utföra följande steg. Några av de vanliga status koderna visas här:

| **Fels** | **Åtgärder** |
| --- | --- |
| Felaktig matchning av status kod för avsökning: Mottaget 401 | Kontrol lera om backend-servern kräver autentisering. Application Gateway-avsökningar kan inte skicka autentiseringsuppgifter för autentisering just nu. Antingen tillåter \"HTTP 401\" i en avsöknings status kod matchning eller avsökning till en sökväg där servern inte kräver autentisering. | |
| Felaktig matchning av status kod för avsökning: Mottaget 403 | Åtkomst förbjuden. Kontrol lera om åtkomst till sökvägen tillåts på backend-servern. | |
| Felaktig matchning av status kod för avsökning: Mottaget 404 | Sidan hittades inte. Kontrol lera hostname-sökvägen om den är tillgänglig på backend-servern. Ändra värd namnet eller Sök vägs parametern till ett tillgängligt värde. | |
| Felaktig matchning av status kod för avsökning: Mottaget 405 | Application Gateways avsöknings begär Anden använder HTTP GET-metoden. Kontrol lera om servern tillåter det. | |
| Felaktig matchning av status kod för avsökning: Mottaget 500 | Internt Server fel. Kontrol lera Server delens hälso tillstånd och om tjänsterna körs. | |
| Felaktig matchning av status kod för avsökning: Mottaget 503 | Tjänsten är inte tillgänglig. Kontrol lera Server delens hälso tillstånd och om tjänsterna körs. | |

Eller också kan du skapa en anpassad avsökning om du tror att svaret är legit och du vill Application Gateway acceptera andra status koder som felfria. Att ändra detta är användbart i situationer där Server dels webbplatsen behöver autentisering och eftersom avsöknings begär Anden inte innehåller några användarautentiseringsuppgifter, och en status kod för HTTP 401 kommer att returneras av backend-servern.

Om du vill skapa en anpassad avsökning följer du stegen som anges [här](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

#### <a name="http-response-body-mismatch"></a>Felaktigt innehåll i HTTP-svar

**Meddelande** Innehållet i Server delens\'http-svar matchade inte avsöknings inställningen. Mottagen svars texten innehåller inte {String}.

**Orsak** När du skapar en anpassad avsökning har du ett alternativ för att markera en backend-server som är felfri genom att matcha en sträng från svars texten. Du kan till exempel konfigurera Application Gateway att godkänna "obehörig" som en sträng som ska matchas. Om Server delens svar för avsöknings förfrågan innehåller strängen "obehörig", markeras den som felfri.
Annars kommer den att markeras som felaktig i det här meddelandet.

**Lösning:** Du kan lösa problemet genom att följa stegen nedan:

1.  Få åtkomst till backend-servern lokalt eller från en klient dator på avsöknings Sök vägen och kontrol lera svars texten.

2.  Kontrol lera Application Gateway anpassad avsöknings konfiguration för att kontrol lera om svars texten matchar vad som är konfigurerat.

3.  Om de inte matchar, ändra avsöknings konfigurationen med rätt sträng värde att acceptera.

Du kan läsa mer om att Application Gateway avsöknings matchning [här](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching).

#### <a name="backend-server-certificate-invalid-ca"></a>Backend-servercertifikat ogiltigt CA

**Meddelande** Server certifikatet som används av Server delen är inte signerat av en välkänd certifikat utfärdare (CA). Vitlista Server delen på Application Gateway genom att ladda upp rot certifikatet för Server certifikatet som används av Server delen.

**Orsak** End-to-end-SSL med Application Gateway v2 kräver att backend-serverns certifikat verifieras för att anse att servern är felfri.
För att ett SSL-certifikat ska vara betrott måste certifikatet på backend-servern ha utfärdats av en certifikat utfärdare som ingår i den betrodda lagringen av Application Gateway. Om certifikatet inte har utfärdats av en betrodd certifikat utfärdare, till exempel självsignerade certifikat, ska användarna ladda upp utfärdarens certifikat till Application Gateway.

**Lösning:** Följ stegen nedan för att exportera och ladda upp det betrodda rot certifikatet till Application Gateway (stegen nedan gäller för Windows-klienter)

1.  Logga in på den dator där ditt program finns

2.  Öppna kör genom att trycka på Win + R eller högerklicka på Start-knappen och välj Kör

3.  Ange certmgr. msc och tryck på RETUR. Du kan också söka efter Certificate Manager på Start-menyn.

4.  Leta upp certifikatet, vanligt vis \'i certifikat – aktuella\\användares\'personliga\\certifikat och öppna certifikatet

5.  I certifikat egenskaperna väljer du fliken certifierings Sök väg

6.  Välj rot certifikat och välj alternativet Visa certifikat

7.  I certifikat egenskaperna, växlar du till fliken information

8.  På fliken information väljer du alternativet "Kopiera till fil" och sparar filen i Base-64-kodad X. 509 (. CER-format

9.  När du har sparat öppnar du sidan Application Gateway HTTP-inställningar i Azure Portal

10. Öppna HTTP-inställningarna och klicka på Lägg till certifikat och leta upp certifikat filen som vi sparade nyligen

11. Klicka på Spara för att spara HTTP-inställningarna

Alternativt kan du exportera rot certifikatet från en klient dator genom att direkt komma åt servern (kringgå Application Gateway) med hjälp av en webbläsare och exportera rot certifikatet från webbläsaren.

Detaljerade anvisningar om hur du extraherar och laddar upp betrodda rot certifikat i Application Gateway finns [här](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Matchnings fel för betrodda rot certifikat

**Meddelande** Rot certifikatet för Server certifikatet som används av Server delen stämmer inte överens med det betrodda rot certifikat som har lagts till i Application Gateway. Se till att du lägger till rätt rot certifikat för att vitlista Server delen

**Orsak** End-to-end-SSL med Application Gateway v2 kräver att backend-serverns certifikat verifieras för att anse att servern är felfri.
För att ett SSL-certifikat ska vara betrott måste certifikatet på backend-servern ha utfärdats av en certifikat utfärdare som ingår i den betrodda lagringen av Application Gateway. Om certifikatet inte har utfärdats av en betrodd certifikat utfärdare, till exempel självsignerade certifikat, ska användarna ladda upp utfärdarens certifikat till Application Gateway.

Det certifikat som har överförts till Application Gateway HTTP-inställningar måste matcha rot certifikatet för Server delens Server certifikat.

**Lösning:** Om du ser ovanstående fel meddelande innebär det att det finns ett matchnings fel mellan det certifikat som har överförts till Application Gateway och den som överförts till backend-servern.

Följ stegen 1-11 ovan för att överföra rätt betrott rot certifikat till Application Gateway.

Detaljerade anvisningar om hur du extraherar och laddar upp betrodda rot certifikat i Application Gateway finns [här](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Felet som nämns kan också inträffa om backend-servern inte utbyter den fullständiga kedjan av certifikatet, inklusive rot > mellanliggande (om tillämpligt)-> löv under TLS-handskakningen. För att verifiera kan du använda OpenSSL-kommandon från vilken klient som helst och ansluta till backend-servern med de konfigurerade inställningarna i Application Gateway avsökningen.

Exempel:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Om utdata inte visar den fullständiga kedjan av det certifikat som returneras exporterar du certifikatet igen med den fullständiga kedjan inklusive rot certifikatet och konfigurerar det på backend-servern. 

ANSLUTEN (00000188) \
djup = 0 ou = domän kontroll verifierad, CN = \*. example.com \
verifiera fel: NUM = 20: det går inte att hämta det lokala utfärdarens certifikat \
verifiera RETUR: 1 \
djup = 0 ou = domän kontroll verifierad, CN = \*. example.com \
verifiera fel: NUM = 21: det går inte att verifiera det första certifikatet \
verifiera RETUR: 1 \
\-\-\-\
Certifikat kedja \
 0 s:/OU = domän kontroll verifierad/CN = *. exempel. com \
   i:/C = US/St = Arizona/L = Scottsdale/O = GoDaddy. com, Inc./ou =http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority-G2 \
\-----BEGIN CERTIFICATE-----\
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
\-----SLUT CERTIFIKAT-----

#### <a name="backend-certificate-invalid-common-name-cn"></a>Backend-certifikatets ogiltiga nätverks namn (CN)

**Meddelande** Server dels certifikatets eget namn (CN) matchar inte avsökningens värd huvud.

**Orsak** Application Gateway validerar om det värdnamn som anges i Server delens http-inställning matchar det egna namnet (CN) som visas av backend-serverns SSL-certifikat. Detta är endast ett Standard_v2-och WAF_v2 SKU-beteende. Standard-och WAF SKU: n för SNI har angetts som FQDN i Server delens adresspool.

Om det finns en standard avsökning i v2-SKU: n (ingen anpassad avsökning har kon figurer ATS/associerad), kommer SNI att ställas in från det värdnamn som anges i HTTP-inställningarna eller om "Välj värdnamn från Server dels adress" anges i HTTP-inställningarna, där backend-adresspoolen innehåller en giltigt fullständigt domän namn.

Om det finns en anpassad avsökning kopplad till HTTP-inställningarna kommer SNI att ställas in från det värdnamn som anges i konfigurationen för anpassad avsökning, eller om alternativet Välj värd namn från Server delens HTTP-inställningar är markerat i den anpassade avsökningen kommer den att ställas in från det värdnamn som anges i HTTP Autentiseringsinställningar.

Om Välj värd namn från backend-adress har ställts in i HTTP-inställningarna måste backend-adresspoolen innehålla ett giltigt fullständigt domän namn.

Om du ser ovanstående fel meddelande innebär det att det egna namnet (CN) för Server dels certifikatet inte matchar värd namnet som kon figurer ATS i den anpassade avsökningen eller HTTP-inställningarna (om du väljer Välj värd namn från Server dels HTTP-inställningar). Om du använder en standard avsökning skulle värd namnet anges som "127.0.0.1". Om detta inte är ett önskat värde bör du skapa en anpassad avsökning och koppla den till HTTP-inställningarna.

**Lösning:**

Följ stegen nedan för att lösa problemet:

För Windows:

1.  Logga in på den dator där ditt program finns

2.  Öppna kör genom att trycka på Win + R eller högerklicka på Start-knappen och välj Kör

3.  Ange certmgr. msc och tryck på RETUR. Du kan också söka efter Certificate Manager på Start-menyn.

4.  Leta upp certifikatet, vanligt vis \'i certifikat – aktuella\\användares\'personliga\\certifikat och öppna certifikatet

5.  På fliken information kontrollerar du certifikatets ämne

6.  Kontrol lera certifikatets CN-namn från informationen och ange samma i fältet hostname för den anpassade avsökningen eller HTTP-inställningarna (i fall "Välj värd namn från Server delens HTTP-inställningar" är valt). Om det inte är det önskade värd namnet för din webbplats måste du skaffa ett certifikat för domänen eller ange rätt värdnamn i konfigurationen för anpassad avsökning/http-inställning.

För Linux med hjälp av OpenSSL

1.  Kör det här kommandot i OpenSSL 
```
openssl x509 -in certificate.crt -text -noout
```

2.  I de egenskaper som visas söker du efter CN för certifikatet och anger samma namn i fältet hostname i http-inställningarna. Om det inte är det önskade värd namnet för din webbplats måste du skaffa ett certifikat för domänen eller ange rätt värdnamn i konfigurationen för anpassad avsökning/http-inställning.

#### <a name="backend-certificate-is-invalid"></a>Server dels certifikatet är ogiltigt

**Meddelande** Server dels certifikatet är ogiltigt. Det aktuella datumet ligger inte inom \"det giltiga\" från \"och det\" giltiga datum intervallet för certifikatet.

**Orsak** Varje certifikat har en giltighet och HTTPS-anslutningen är inte säker om serverns SSL-certifikat är giltigt.
Aktuella data måste vara inom giltigt från och giltigt till-intervallet. Om inte, kommer certifikatet att anses vara ogiltigt och det är ett säkerhets problem. I det här läget kommer Application Gateway att markera backend-servern som ohälsosam.

**Lösning:** Om SSL-certifikatet har upphört att gälla förnyar du certifikatet hos leverantören och uppdaterar Server inställningarna med det nya certifikatet. Om det är ett självsignerat certifikat måste du generera ett giltigt certifikat och överföra rot certifikatet till Application Gateway HTTP-inställningar. Det gör du genom att följa stegen nedan:

1.  Öppna Application Gateway HTTP-inställningar i portalen

2.  Välj de HTTP-inställningar som har det utgångna certifikatet, Välj Lägg till certifikat och öppna den nya certifikat filen

3.  Ta bort det gamla certifikatet med ikonen Ta bort bredvid certifikatet och klicka på Spara

#### <a name="certificate-verification-failed"></a>Certifikat verifieringen misslyckades

**Meddelande** Det gick inte att verifiera Server dels certifikatets giltighet. Ta reda på orsaken genom att markera öppna SSL-diagnostik för meddelandet som är associerat med felkoden {errorCode}

**Orsak** Felet uppstår när Application Gateway inte kan verifiera certifikatets giltighet.

**Lösning:** Lös problemet genom att kontrol lera certifikatet på servern om det har skapats korrekt. Du kan till exempel använda [openssl](https://www.openssl.org/docs/man1.0.2/man1/verify.html) för att verifiera certifikatet och dess egenskaper och försöka att ladda upp certifikatet till Application Gateway http-inställningar.

<a name="backend-health-status-unknown"></a>Okänd hälso status för Server delen
-------------------------------
Om Server delens hälso tillstånd visas som Okänd visas den i portalen som skärm bilden nedan:

![Application Gateway server dels hälsa – okänd](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Om Server delens hälso tillstånd visas som okänd kan det bero på en eller flera av följande orsaker:

1.  NSG på Application Gateway-undernätet blockerar inkommande åtkomst till portarna 65503-65534 (v1 SKU) eller 65200-65535 (v2 SKU) från "Internet"
2.  UDR på Application Gateway under nätet med standard väg (0.0.0.0/0) med nästa hopp inte som Internet
3.  Standard väg annonseras av ExpressRoute/VPN-anslutning till VNet över BGP
4.  Anpassad DNS-server har kon figurer ATS i det virtuella nätverket som inte kan matcha offentliga domän namn
5.  Application Gateway är i ett ohälsosamt tillstånd

**Lösning:**

1.  Kontrol lera om din NSG blockerar åtkomsten till portarna 65503-65534 (v1 SKU) eller 65200-65535 (v2 SKU) från Internet

    a.  På fliken Application Gateway översikt väljer du länken Virtual Network/undernät

    b.  På fliken undernät i Virtual Network väljer du det undernät där Application Gateway har distribuerats

    c.  Kontrol lera om några NSG har kon figurer ATS

    d.  Om det finns en sådan kan du söka efter den NSG-resursen på fliken Sök eller under alla resurser

    e.  I avsnittet regler för inkommande trafik lägger du till en regel för inkommande trafik som tillåter mål Port intervallet 65503-65534 för v1 SKU eller 65200-65535 v2 SKU med källa som valfri eller Internet

    f.  Klicka på Spara och kontrol lera om du kan visa Server delens felfritt

    g.  Alternativt kan du göra det via [PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

2.  Kontrol lera om din UDR har en standard väg (0.0.0.0/0) med nästa hopp inte som Internet
    
    a.  Följ steg 1. a och 1. b för att fastställa under nätet

    b.  Kontrol lera om några UDR har kon figurer ATS. Om det finns en sådan söker du efter resursen i Sök fältet eller under alla resurser

    c.  Kontrol lera om det finns några standard vägar (0.0.0.0/0) med nästa hopp som inte är Internet. Om det är en virtuell installation eller Virtual Network Gateway måste du se till att den virtuella datorn eller den lokala enheten kommer att kunna dirigera paketet tillbaka till Internet-målet utan att ändra paketet

    d.  Annars ändrar du nästa hopp till Internet, klickar på Spara och kontrollerar Server dels hälsan

3.  Standard väg annonseras av ExpressRoute/VPN-anslutning till VNet över BGP

    a.  Om du har en ExpressRoute/VPN-anslutning till VNet över BGP och om du annonserar standard väg, måste du se till att paketet dirigeras tillbaka till Internet-målet utan att ändra det

    b.  Du kan kontrol lera genom att använda alternativet "anslutnings fel sökning" i Application Gateway Portal

    c.  Välj målet manuellt som valfri IP-adress för Internet-routning, till exempel "1.1.1.1" och målport som allt och kontrol lera anslutningen.

    d.  Om nästa hopp är Virtual Network Gateway, kan det finnas en standard väg som annonseras via ExpressRoute eller VPN

4.  Om det finns en anpassad DNS-server som kon figurer ATS i VNet kontrollerar du att servrarna kan matcha offentliga domäner. Offentlig domän namn matchning kan krävas i scenarier där Application Gateway måste kontakta externa domäner som OCSP-servrar eller för att kontrol lera certifikatets återkallnings status osv.

5.  För att kontrol lera om Application Gateway är felfri och körs går du till Resource Health alternativ i portalen och kontrollerar om det är "felfri". [Kontakta supporten](https://azure.microsoft.com/support/options/)om du ser ohälsosamt eller försämrat tillstånd.

<a name="next-steps"></a>Nästa steg
----------

Mer information om Application Gateway diagnostik och loggning finns [här](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
