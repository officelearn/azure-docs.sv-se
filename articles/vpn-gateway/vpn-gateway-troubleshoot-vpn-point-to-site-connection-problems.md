---
title: Felsöka problem med Azure point-to-site-anslutning
titleSuffix: Azure VPN Gateway
description: Läs om hur du felsöker anslutningsproblem mellan punkt och plats.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: genli
ms.openlocfilehash: 119f9c28b5413b8d2db5fa14ea839d1743f3d64a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297631"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Felsökning: Problem med Azure point-to-site-anslutning

I den här artikeln visas vanliga anslutningsproblem mellan punkt och plats som kan uppstå. Man diskuterar också möjliga orsaker och lösningar på dessa problem.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>VPN-klientfel: Det gick inte att hitta ett certifikat

### <a name="symptom"></a>Symptom

NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k att ansluta till ett virtuellt Azure-nätverk med VPN-klienten visas fÃ¶1 ã¤ nã¤r du fÃ¶rsÃ¶k

**Det gick inte att hitta ett certifikat som kan användas med det här utökningsbara autentiseringsprotokollet. (Fel 798)**

### <a name="cause"></a>Orsak

Det hÃ¤r problemet uppstÃ¥r om klientcertifikatet **saknas frÃ¥r Certifikat - Aktuella user\Personal\Certificates**.

### <a name="solution"></a>Lösning

LÃ¶s problemet genom att sÃ¥ att lÃ¶sa:

1. Öppna Certifikathanteraren: Klicka på **Start,** skriv **hantera datorcertifikat**och klicka sedan på **Hantera datorcertifikat** i sökresultatet.

2. Kontrollera att följande certifikat är på rätt plats:

    | Certifikat | Location |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktuell användare\Personliga\Certifikat |
    | AzureRoot.cer (på andra)    | Lokala dator\Betrodda rotcertifieringsmyndigheter|

3. Gå till C:\Användares\<användarnamn>\AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID>, installera certifikatet (*.cer-filen manuellt) i användarens och datorns arkiv.

Mer information om hur du installerar klientcertifikatet finns i [Generera och exportera certifikat för punkt-till-plats-anslutningar](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> När du importerar klientcertifikatet ska du inte välja alternativet **Aktivera starkt skydd för privata nyckel.**

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>Det gick inte att upprätta nätverksanslutningen mellan datorn och VPN-servern eftersom fjärrservern inte svarar

### <a name="symptom"></a>Symptom

NÃ¤r du fÃ¶rsÃ¶k och ansluter till en Azure-gateway för virtuella nätverk med IKEv2 i Windows visas fÃ¶1 ã¤strã¶ing:

**Det gick inte att upprätta nätverksanslutningen mellan datorn och VPN-servern eftersom fjärrservern inte svarar**

### <a name="cause"></a>Orsak
 
 Problemet uppstår om versionen av Windows inte har stöd för IKE-fragmentering
 
### <a name="solution"></a>Lösning

IKEv2 stöds på Windows 10 och Server 2016. Om du vill använda IKEv2 måste du installera uppdateringar och ange ett registreringsnyckelvärde lokalt. Operativsystemversioner före Windows 10 stöds inte och kan endast använda SSTP.

Förbereda Windows 10 eller Server 2016 för IKEv2:

1. Installera uppdateringen.

   | Operativsystemversion | Datum | Antal/länk |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10, version 1607 | 17 januari 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10, version 1703 | 17 januari 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 Version 1709 | den 22 mars 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. Ange registernyckelvärdet. Skapa eller `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload` ange REG_DWORD nyckel i registret till 1.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN-klientfel: Meddelandet som togs emot var oväntat eller felaktigt formaterat

### <a name="symptom"></a>Symptom

NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k att ansluta till ett virtuellt Azure-nätverk med VPN-klienten visas fÃ¶1 ã¤ nã¤r du fÃ¶rsÃ¶k

**Meddelandet som togs emot var oväntat eller felaktigt formaterat. (Fel 0x80090326)**

### <a name="cause"></a>Orsak

Det här problemet uppstår om något av följande villkor är sant:

- UDR (Use User-defined routes) med standardväg i gateway-undernätet är felaktigt inställd.
- Den offentliga nyckeln för rotcertifikat överförs inte till Azure VPN-gatewayen. 
- Nyckeln är skadad eller har upphört att gälla.

### <a name="solution"></a>Lösning

LÃ¶s problemet genom att sÃ¥ att lÃ¶sa:

1. Ta bort UDR i gateway-undernätet. Se till att UDR vidarebefordrar all trafik på rätt sätt.
2. Kontrollera status för rotcertifikatet i Azure-portalen för att se om det har återkallats. Om det inte återkallas försöker du ta bort rotcertifikatet och hämta igen. Mer information finns i [Skapa certifikat](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN-klientfel: En certifikatkedja bearbetas men avslutas 

### <a name="symptom"></a>Symptom 

NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k att ansluta till ett virtuellt Azure-nätverk med VPN-klienten visas fÃ¶1 ã¤ nã¤r du fÃ¶rsÃ¶k

**En certifikatkedja bearbetas men avslutas i ett rotcertifikat som inte är betrodd av förtroendeprovidern.**

### <a name="solution"></a>Lösning

1. Kontrollera att följande certifikat är på rätt plats:

    | Certifikat | Location |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktuell användare\Personliga\Certifikat |
    | Azuregateway-*GUID*.cloudapp.net  | Aktuella användare\Betrodda rotcertifieringsutfärdare|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Lokala dator\Betrodda rotcertifieringsmyndigheter|

2. Om certifikaten redan finns på platsen försöker du ta bort certifikaten och installera om dem. **Azuregateway-*GUID*.cloudapp.net-certifikatet** finns i KONFIGURATIONSPAKETET för VPN-klienten som du hämtade från Azure-portalen. Du kan använda filar archivers för att extrahera filerna från paketet.

## <a name="file-download-error-target-uri-is-not-specified"></a>Filhämtningsfel: Mål-URI har inte angetts

### <a name="symptom"></a>Symptom

Följande felmeddelande visas:

**Filhämtningsfel. Mål-URI har inte angetts.**

### <a name="cause"></a>Orsak 

Det här problemet uppstår på grund av en felaktig gatewaytyp. 

### <a name="solution"></a>Lösning

VPN-gatewaytypen måste vara **VPN**och VPN-typen måste vara **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>VPN-klientfel: Azure VPN-anpassat skript misslyckades 

### <a name="symptom"></a>Symptom

NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k att ansluta till ett virtuellt Azure-nätverk med VPN-klienten visas fÃ¶1 ã¤ nã¤r du fÃ¶rsÃ¶k

**Det gick inte att uppdatera operationsföljdstabellen(för att uppdatera routningstabellen). (Fel 8007026f)**

### <a name="cause"></a>Orsak

Det hÃ¤r problemet kan uppstÃ¥ om du fÃ¶rsÃ¶rsÃ¶r att öppna VPN-anslutningen från plats till punkt med hjälp av en genväg.

### <a name="solution"></a>Lösning 

Öppna VPN-paketet direkt istället för att öppna det från genvägen.

## <a name="cannot-install-the-vpn-client"></a>Det går inte att installera VPN-klienten

### <a name="cause"></a>Orsak 

Ytterligare ett certifikat krävs för att lita på VPN-gatewayen för ditt virtuella nätverk. Certifikatet ingår i konfigurationspaketet för VPN-klienten som genereras från Azure-portalen.

### <a name="solution"></a>Lösning

Extrahera KONFIGURATIONSPAKETET för VPN-klienten och leta reda på .cer-filen. Så här installerar du certifikatet:

1. Öppna mmc.exe.
2. Lägg till snapin-modulen **Certifikat.**
3. Välj **datorkontot** för den lokala datorn.
4. Högerklicka på noden **Betrodda rotcertifikatutfärdare.** Klicka på > **Importera**alla uppgifter och bläddra till den **CER-fil**som du extraherade från KONFIGURATIONSPAKETET för VPN-klienten.
5. Starta om datorn. 
6. Försök att installera VPN-klienten.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Azure-portalfel: Det gick inte att spara VPN-gatewayen och data är ogiltiga

### <a name="symptom"></a>Symptom

NÃ¤r du fÃ¶rsÃ¶ks FÃ¶rsÃ¶k att spara ändringarna fÃ¶r VPN-gatewayen i Azure-portalen visas fÃ¶10 felmeddelandet:

**Det gick inte &lt;att spara *gateway-namnet*&gt;för virtuella nätverk. Data för &lt; *certifikatcertifikat-ID* &gt; är ogiltiga.**

### <a name="cause"></a>Orsak 

Det här problemet kan uppstå om den offentliga rotcertifikatnyckeln som du har laddat upp innehåller ett ogiltigt tecken, till exempel ett blanksteg.

### <a name="solution"></a>Lösning

Kontrollera att data i certifikatet inte innehåller ogiltiga tecken, till exempel radbrytningar (vagnreturer). Hela värdet bör vara en lång rad. Följande text är ett exempel på certifikatet:

    -----BEGIN CERTIFICATE-----
    MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
    MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
    MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
    AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
    sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
    G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
    407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
    5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
    sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
    Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
    DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
    /0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
    gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
    2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
    U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
    e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
    -----END CERTIFICATE-----

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Azure-portalfel: Det gick inte att spara VPN-gatewayen och resursnamnet är ogiltigt

### <a name="symptom"></a>Symptom

NÃ¤r du fÃ¶rsÃ¶ks FÃ¶rsÃ¶k att spara ändringarna fÃ¶r VPN-gatewayen i Azure-portalen visas fÃ¶10 felmeddelandet: 

**Det gick inte &lt;att spara *gateway-namnet*&gt;för virtuella nätverk. &lt; *Resursnamncertifikatnamn som du försöker överföra* &gt; är ogiltigt**.

### <a name="cause"></a>Orsak

Det här problemet beror på att namnet på certifikatet innehåller ett ogiltigt tecken, till exempel ett blanksteg. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure portal fel: VPN-paketfil nedladdningsfel 503

### <a name="symptom"></a>Symptom

NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k fÃ¶rsÃ¶k fÃ¶r VPN-klientens konfigurationspaket visas fÃ¶ndesÃ¶k:

**Det gick inte att hämta filen. Felinformation: fel 503. Servern är upptagen.**
 
### <a name="solution"></a>Lösning

Det här felet kan orsakas av ett tillfälligt nätverksproblem. Försök att ladda ner VPN-paketet igen efter några minuter.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Uppgradering av Azure VPN Gateway: Alla Point to Site-klienter kan inte ansluta

### <a name="cause"></a>Orsak

Om certifikatet är mer än 50 procent under sin livstid rullas certifikatet över.

### <a name="solution"></a>Lösning

Lös problemet genom att hämta och distribuera om paketet Peka till plats på alla klienter.

## <a name="too-many-vpn-clients-connected-at-once"></a>För många VPN-klienter anslutna samtidigt

Det maximala antalet tillåtna anslutningar uppnås. Du kan se det totala antalet anslutna klienter i Azure-portalen.

## <a name="vpn-client-cannot-access-network-file-shares"></a>VPN-klienten kan inte komma åt nätverksfilresurser

### <a name="symptom"></a>Symptom

VPN-klienten har anslutit till det virtuella Azure-nätverket. Klienten kan dock inte komma åt nätverksresurser.

### <a name="cause"></a>Orsak

SMB-protokollet används för filresursåtkomst. När anslutningen initieras lägger VPN-klienten till sessionsautentiseringsuppgifterna och felet inträffar. När anslutningen har upprättats tvingas klienten använda cacheautentiseringsuppgifterna för Kerberos-autentisering. Den här processen initierar frågor till Key Distribution Center (en domänkontrollant) för att hämta en token. Eftersom klienten ansluter från Internet kanske den inte når domänkontrollanten. Klienten kan därför inte växla över från Kerberos till NTLM. 

Den enda gången klienten uppmanas att ange en autentiseringsfråga är när den har ett giltigt certifikat (med SAN=UPN) utfärdat av den domän som den är ansluten till. Klienten måste också vara fysiskt ansluten till domännätverket. I det här fallet försöker klienten använda certifikatet och når ut till domänkontrollanten. Då returnerar key distributionscenter ett "KDC_ERR_C_PRINCIPAL_UNKNOWN"-fel. Klienten tvingas växla över till NTLM. 

### <a name="solution"></a>Lösning

Du kan lösa problemet genom att inaktivera cachelagring av domänautentiseringsuppgifter från följande registerundernyckel: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Det går inte att hitta VPN-anslutningen för punkt till plats i Windows efter ominstallation av VPN-klienten

### <a name="symptom"></a>Symptom

Du tar bort vpn-anslutningen från punkt till plats och installerar sedan om VPN-klienten. I det här fallet är VPN-anslutningen inte konfigurerad. Du ser inte VPN-anslutningen i **nätverksanslutningsinställningarna** i Windows.

### <a name="solution"></a>Lösning

LÃ¶s problemet genom att ta bort de gamla VPN-klientkonfigurationsfilerna **frÃ¥r\<C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections VirtualNetworkId>** och kÃ¶nde sedan VPN-klientinstallationsprogrammet igen.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>VPN-klienten på plats kan inte lösa FQDN för resurserna i den lokala domänen

### <a name="symptom"></a>Symptom

När klienten ansluter till Azure med hjälp av punkt-till-plats-VPN-anslutning kan den inte lösa FQDN för resurserna i den lokala domänen.

### <a name="cause"></a>Orsak

Vpn-klienten från punkt till plats använder Azure DNS-servrar som är konfigurerade i det virtuella Azure-nätverket. Azure DNS-servrarna har företräde framför de lokala DNS-servrar som är konfigurerade i klienten, så alla DNS-frågor skickas till Azure DNS-servrarna. Om Azure DNS-servrarna inte har posterna för de lokala resurserna misslyckas frågan.

### <a name="solution"></a>Lösning

LÃ¶s problemet genom att kontrollera att Azure DNS-servrarna som anhÃ¶rs i det virtuella Azure-nätverket kan lÃ¶sa DNS-posterna för lokala resurser. För att göra detta kan du använda DNS-vidarebefordrare eller villkorliga vidarebefordrare. Mer information finns i [Namnmatchning med din egen DNS-server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>VPN-anslutningen för punkt till plats upprättas, men du kan fortfarande inte ansluta till Azure-resurser 

### <a name="cause"></a>Orsak

Det här problemet kan uppstå om VPN-klienten inte hämtar vägarna från Azure VPN-gatewayen.

### <a name="solution"></a>Lösning

LÃ¶s problemet genom att [återställa Azure VPN gateway](vpn-gateway-resetgw-classic.md). För att se till att de nya vägarna används måste VPN-klienterna från punkt till plats hämtas igen efter att virtuell nätverks peering har konfigurerats.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Fel: "Återkallningsfunktionen kunde inte kontrollera återkallandet eftersom återkallningsservern var offline. (Fel 0x80092013)"

### <a name="causes"></a>Orsaker
Det här felmeddelandet visas http://crl3.digicert.com/ssca-sha2-g1.crl om http://crl4.digicert.com/ssca-sha2-g1.crlklienten inte kan komma åt och .  Återkallningskontrollen kräver åtkomst till dessa två platser.  Det här problemet uppstår vanligtvis på klienten som har konfigurerad proxyserver. I vissa miljöer, om begäranden inte går via proxyservern, kommer det att nekas vid Edge-brandväggen.

### <a name="solution"></a>Lösning

Kontrollera proxyserverns inställningar, se till http://crl3.digicert.com/ssca-sha2-g1.crl att http://crl4.digicert.com/ssca-sha2-g1.crlklienten kan komma åt och .

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>VPN-klientfel: Anslutningen förhindrades på grund av en princip som konfigurerats på RAS/VPN-servern. (Fel 812)

### <a name="cause"></a>Orsak

Det här felet uppstår om RADIUS-servern som du använde för att autentisera VPN-klienten har felaktiga inställningar eller om Azure Gateway inte kan nå Radius-servern.

### <a name="solution"></a>Lösning

Kontrollera att RADIUS-servern är korrekt konfigurerad. Mer information finns i [Integrera RADIUS-autentisering med Azure Multi-Factor Authentication Server](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Fel 405" när du hämtar rotcertifikat från VPN Gateway

### <a name="cause"></a>Orsak

Rotcertifikatet hade inte installerats. Rotcertifikatet installeras i klientens **betrodda certifikatarkiv.**

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>VPN-klientfel: Fjärranslutningen gjordes inte eftersom vpn-tunnlarna misslyckades. (Fel 800) 

### <a name="cause"></a>Orsak

Nätverkskortet-drivrutinen är föråldrad.

### <a name="solution"></a>Lösning

Uppdatera drivrutinen för nätverkskortet:

1. Klicka på **Start**, skriv **Enhetshanteraren**och markera den i resultatlistan. Om du uppmanas att ange ett administratörslösenord eller en bekräftelse skriver du lösenordet eller ger bekräftelse.
2. Leta reda på det nätverkskort som du vill uppdatera i kategorierna **Nätverkskort.**  
3. Dubbelklicka på enhetens namn, välj **Uppdatera drivrutin**, välj **Sök automatiskt efter uppdaterad drivrutin**.
4.  Om Windows inte hittar någon kan du söka efter en drivrutin på enhetstillverkarens webbplats och installera den enligt deras instruktioner.
5. Starta om datorn och försök igen.

## <a name="vpn-client-error-dialing-vpn-connection-vpn-connection-name-status--vpn-platform-did-not-trigger-connection"></a>VPN-klientfel: Ringa <VPN Connection Name>VPN-anslutning , Status = VPN-plattformen utlöste inte anslutning

Du kan också se följande fel i Loggboken från <User> RasClient: <VPN Connection Name> "Användaren ringde en anslutning med namnet som har misslyckats. Felkoden som returneras vid fel är 1460."

### <a name="cause"></a>Orsak

Azure VPN-klienten har inte appbehörigheten "Bakgrundsappar" aktiverad i Appinställningar för Windows.

### <a name="solution"></a>Lösning

1. I Windows går du till Inställningar -> Sekretess -> bakgrundsappar
2. Växla "Låt appar köras i bakgrunden" till På

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Fel: "Hämtningsfel för filmålinformation har inte angetts"

### <a name="cause"></a>Orsak

Detta orsakas av en felaktig gatewaytyp är konfigurerad.

### <a name="solution"></a>Lösning

Azure VPN-gatewaytypen måste vara VPN och VPN-typen måste vara **RouteBased**.

## <a name="vpn-package-installer-doesnt-complete"></a>INSTALLATIONsprogrammet för VPN-paket slutförs inte

### <a name="cause"></a>Orsak

Det här problemet kan orsakas av tidigare VPN-klientinstallationer. 

### <a name="solution"></a>Lösning

Ta bort de gamla KONFIGURATIONSFILERNA för VPN-klienten från **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections\<VirtualNetworkId>** och kör VPN-klientinstallationsprogrammet igen. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>VPN-klienten försvinnar eller sover efter en tid

### <a name="solution"></a>Lösning

Kontrollera inställningarna för viloläge och viloläge i datorn som VPN-klienten körs på.
