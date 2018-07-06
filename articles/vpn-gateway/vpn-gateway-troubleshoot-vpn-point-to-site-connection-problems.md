---
title: Felsöka problem med Azure punkt-till-plats-anslutning | Microsoft Docs
description: Lär dig hur du felsöker problem med punkt-till-plats-anslutning.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: cd89c41b43be1da339ca7dcc64110e7145a93903
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857338"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Felsökning: Problem med Azure punkt-till-plats-anslutning

Den här artikeln innehåller vanliga anslutningsproblem med punkt-till-plats-som kan uppstå. Det diskuterar även möjliga orsaker och lösningar för dessa problem.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>VPN-klientfel: Det gick inte att hitta något certifikat

### <a name="symptom"></a>Symtom

När du försöker ansluta till ett Azure-nätverk med hjälp av VPN-klienten, visas följande felmeddelande visas:

**Det gick inte att hitta ett certifikat som kan användas med Extensible Authentication Protocol. (Fel 798)**

### <a name="cause"></a>Orsak

Det här problemet uppstår om klientcertifikatet saknas från **certifikat – aktuell användare\personligt\certifikat**.

### <a name="solution"></a>Lösning

Följ dessa steg för att lösa problemet:

1. Öppna Certifikathanteraren: Klicka på **starta**, typ **hantera datorcertifikat**, och klicka sedan på **hantera datorcertifikat** i sökresultatet.

2. Se till att följande certifikat är på rätt plats:

    | Certifikat | Plats |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktuell användare\personligt\certifikat |
    | Azuregateway-*GUID*.cloudapp.net  | Aktuella User\Trusted rotcertifikatutfärdare|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Lokal dator\Betrodda certifikatutfärdare|

3. Gå till användare\<användarnamn > \AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID >, manuellt installera certifikatet (*.cer-fil) på användaren och datorns store.

Läs mer om hur du installerar klientcertifikatet [generera och exportera certifikat för punkt-till-plats-anslutningar](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> När du importerar klientcertifikatet inte väljer den **aktivera starkt skydd av den privata nyckeln** alternativet.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN-klientfel: det mottagna meddelandet var oväntad eller felaktigt formaterat

### <a name="symptom"></a>Symtom

När du försöker ansluta till ett Azure-nätverk med hjälp av VPN-klienten, visas följande felmeddelande visas:

**Det mottagna meddelandet var oväntad eller felaktigt formaterat. (Fel 0x80090326)**

### <a name="cause"></a>Orsak

Det här problemet uppstår om någon av följande villkor föreligger:

- Använda användardefinierade vägar (UDR) med standardvägen på Gateway-undernätet har angetts felaktigt.
- Den offentliga nyckeln för rot-certifikatet har inte överförts till Azure VPN-gatewayen. 
- Nyckeln är skadad eller har upphört att gälla.

### <a name="solution"></a>Lösning

Följ dessa steg för att lösa problemet:

1. Ta bort användardefinierade vägen i Gateway-undernätet. Kontrollera att UDR vidarebefordrar all trafik korrekt.
2. Kontrollera status för rotcertifikat i Azure portal för att se om det har återkallats. Om det inte har återkallats du försök ta bort rotcertifikat och reupload. Mer information finns i [skapa certifikat](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN-klientfel: en certifikatkedja bearbetas men avslutades 

### <a name="symptom"></a>Symtom 

När du försöker ansluta till ett Azure-nätverk med hjälp av VPN-klienten, visas följande felmeddelande visas:

**En certifikatkedja bearbetas men avslutades med ett rotcertifikat som inte är betrodd av för förtroendeprovidern.**

### <a name="solution"></a>Lösning

1. Se till att följande certifikat är på rätt plats:

    | Certifikat | Plats |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktuell användare\personligt\certifikat |
    | Azuregateway-*GUID*.cloudapp.net  | Aktuella User\Trusted rotcertifikatutfärdare|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Lokal dator\Betrodda certifikatutfärdare|

2. Försök att ta bort certifikaten och installera om dem om certifikat som redan är på plats. Den **azuregateway -*GUID*. cloudapp.net** certifikat finns i VPN-klientkonfigurationspaketet som du laddade ned från Azure-portalen. Du kan använda filen archivers för att extrahera filerna från paketet.

## <a name="file-download-error-target-uri-is-not-specified"></a>Hämta fel: mål-URI har inte angetts

### <a name="symptom"></a>Symtom

Du får följande felmeddelande visas:

**Fel vid hämtning av filen. Mål-URI har inte angetts.**

### <a name="cause"></a>Orsak 

Det här problemet inträffar på grund av en felaktig gateway-typen. 

### <a name="solution"></a>Lösning

Typ av VPN-gateway måste vara **VPN**, och VPN-typ måste vara **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>VPN-klientfel: Azure VPN-anpassat skript misslyckades 

### <a name="symptom"></a>Symtom

När du försöker ansluta till ett Azure-nätverk med hjälp av VPN-klienten, visas följande felmeddelande visas:

**Anpassat skript (för att uppdatera routningstabellen) misslyckades. (Fel 8007026f)**

### <a name="cause"></a>Orsak

Det här problemet kan inträffa om du försöker öppna VPN-anslutning för plats till plats med hjälp av en genväg.

### <a name="solution"></a>Lösning 

Öppna VPN-klientpaketet direkt i stället för att öppna den från genvägen.

## <a name="cannot-install-the-vpn-client"></a>Det går inte att installera VPN-klienten

### <a name="cause"></a>Orsak 

Ett ytterligare certifikat krävs för att lita på VPN-gateway för det virtuella nätverket. Certifikatet ingår i VPN-klientkonfigurationspaketet som genereras från Azure-portalen.

### <a name="solution"></a>Lösning

Extrahera konfigurationspaketet för VPN-klienten och hitta .cer-filen. Följ dessa steg om du vill installera certifikat:

1. Öppna mmc.exe.
2. Lägg till den **certifikat** snapin-modulen.
3. Välj den **datorn** för den lokala datorn.
4. Högerklicka på den **betrodda rotcertifikatutfärdare** noden. Klicka på **All-aktivitet** > **Import**, och bläddra till .cer-filen som du har extraherat från VPN-klientkonfigurationspaket.
5. Starta om datorn. 
6. Försök att installera VPN-klienten.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Azure portal fel: Det gick inte att spara VPN-gatewayen och data är ogiltiga

### <a name="symptom"></a>Symtom

När du försöker spara ändringarna för VPN-gateway i Azure-portalen, visas följande felmeddelande visas:

**Det gick inte att spara virtuell nätverksgateway &lt; *gatewaynamn*&gt;. Data för certifikatet &lt; *certifikat ID* &gt; är ogiltig.**

### <a name="cause"></a>Orsak 

Det här problemet kan inträffa om rot certifikatets offentliga nyckel som du överförde innehåller ett ogiltigt tecken, till exempel ett blanksteg.

### <a name="solution"></a>Lösning

Se till att data i certifikatet inte innehåller ogiltiga tecken, till exempel radbrytningar (vagnreturer). Hela värdet ska vara en lång rad. Följande text är ett exempel på certifikatet:

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

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Azure portal fel: Det gick inte att spara VPN-gatewayen och resursnamnet är ogiltigt

### <a name="symptom"></a>Symtom

När du försöker spara ändringarna för VPN-gateway i Azure-portalen, visas följande felmeddelande visas: 

**Det gick inte att spara virtuell nätverksgateway &lt; *gatewaynamn*&gt;. Resursnamnet &lt; *certifikatnamnet som du försöker överföra* &gt; är ogiltig**.

### <a name="cause"></a>Orsak

Det här problemet beror på att namnet på certifikatet innehåller ett ogiltigt tecken, till exempel ett blanksteg. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Fel när Azure portal: VPN-paketet filhämtningsfel 503

### <a name="symptom"></a>Symtom

När du försöker hämta konfigurationspaketet för VPN-klienten, visas följande felmeddelande visas:

**Det gick inte att hämta filen. Felinformation: fel 503. Servern är upptagen.**
 
### <a name="solution"></a>Lösning

Det här felet kan orsakas av tillfälliga nätverksproblem. Försök att hämta VPN-klientpaketet igen efter några minuter.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Azure VPN Gateway-uppgraderingen: alla punkt till plats klienter kan inte ansluta

### <a name="cause"></a>Orsak

Om certifikatet är mer än 50 procent via livslängden och certifikatet för kommande perioder.

### <a name="solution"></a>Lösning

Lös problemet genom att distribuera om punkten till plats paketet på alla klienter.

## <a name="too-many-vpn-clients-connected-at-once"></a>För många VPN-klienter är anslutna på samma gång

För varje VPN-gateway är det maximala antalet tillåtna anslutningar 128. Du kan se det totala antalet anslutna klienter i Azure-portalen.

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-the-route-table"></a>Punkt-till-plats VPN felaktigt lägger till en väg för 10.0.0.0/8 till i routningstabellen

### <a name="symptom"></a>Symtom

När man reglerar VPN-anslutningen på klienten punkt-till-plats VPN-klienten bör lägga till en väg mot Azure-nätverket. IP-helper-tjänsten bör lägga till en väg för undernätet för VPN-klienter. 

Intervall för VPN-klienten tillhör ett mindre undernätverk av 10.0.0.0/8, till exempel 10.0.12.0/24. I stället för en väg för 10.0.12.0/24 läggs en väg för 10.0.0.0/8 som har högre prioritet. 

Den här felaktiga vägen delar anslutningar med andra lokala nätverk som kan tillhör ett annat undernät inom 10.0.0.0/8, till exempel 10.50.0.0/24, som inte har en specifik väg som definierats. 

### <a name="cause"></a>Orsak

Det här beteendet är avsiktligt för Windows-klienter. När klienten använder PPP IPCP-protokollet, hämtar IP-adressen för tunnelgränssnittet från servern (VPN-gateway i det här fallet). Men på grund av en begränsning i protokollet saknar klienten nätmask. Eftersom det inte finns något annat sätt att hämta den, försöker klienten att gissa nätmasken baserat på klassen för tunnel gränssnittets IP-adress. 

Därför läggs en väg baserat på följande avbildningar: 

Om adressen hör till klass A gäller/8 som för-->

Om adressen hör till klass B--> tillämpa /16

Om adressen hör till klass C--> tillämpa /24

### <a name="solution"></a>Lösning

Ha vägar för andra nätverk att matas in i routningstabellen med lägre Mått (därför högre prioritet) än punkten till plats eller längsta prefix-matchningen. 

## <a name="vpn-client-cannot-access-network-file-shares"></a>VPN-klienten inte kan komma åt filresurser över nätverket

### <a name="symptom"></a>Symtom

VPN-klienten har anslutit till Azure-nätverk. Men klienten kan inte komma åt nätverksresurser.

### <a name="cause"></a>Orsak

SMB-protokollet används för åtkomst till resursen för filen. När anslutningen initieras VPN-klienten lägger till autentiseringsuppgifter för sessionen och felet inträffade. När anslutningen har upprättats, tvingas klienten att använda cache-autentiseringsuppgifter för Kerberos-autentisering. Den här processen initierar frågor till Key Distribution Center (en domänkontrollant) att hämta en token. Eftersom klienten ansluter från Internet, kan det inte att nå domänkontrollanten. Därför kan klienten inte kan överföras från Kerberos till NTLM. 

Den enda gången uppmanas klienten för en autentiseringsuppgift är när den har ett giltigt certifikat (med SAN-nätverk = UPN) utfärdat av domänen som den är ansluten. Klienten måste också vara fysiskt ansluten till domännätverket. I det här fallet klienten försöker använda certifikatet och når till domänkontrollanten. Key Distribution Center returnerar sedan ett ”KDC_ERR_C_PRINCIPAL_UNKNOWN”-fel. Klienten tvingas att växla över till NTLM. 

### <a name="solution"></a>Lösning

Undvik problemet genom att inaktivera cachelagring av autentiseringsuppgifter för domänen från följande registerundernyckel: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Det går inte att hitta punkt-till-plats VPN-anslutningen i Windows efter ominstallationen VPN-klienten

### <a name="symptom"></a>Symtom

Du tar bort punkt-till-plats VPN-anslutning och sedan installera om VPN-klienten. I det här fallet har VPN-anslutningen inte konfigurerats. Du ser inte VPN-anslutningen i den **nätverksanslutningar** inställningar i Windows.

### <a name="solution"></a>Lösning

Lös problemet genom att ta bort de gamla VPN-klientkonfigurationsfilerna från **C:\users\username\AppData\Microsoft\Network\Connections\<VirtualNetworkId >**, och kör sedan installationsprogrammet för VPN-klienten igen.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Punkt-till-plats VPN-klienten inte kan lösa det fullständiga Domännamnet för resurserna i den lokala domänen

### <a name="symptom"></a>Symtom

När klienten ansluter till Azure med hjälp av punkt-till-plats VPN-anslutning, kan den inte kan lösa FQND resurser i den lokala domänen.

### <a name="cause"></a>Orsak

Punkt-till-plats VPN-klienten använder Azure DNS-servrar som konfigurerats i Azure-nätverket. Azure DNS-servrar har företräde framför de lokala DNS-servrar som konfigurerats i klienten, så att alla DNS-frågor skickas till Azure DNS-servrar. Om Azure DNS-servrarna inte har på posterna för de lokala resurserna kan misslyckas.

### <a name="solution"></a>Lösning

Lös problemet genom att kontrollera att Azure DNS-servrar som används på virtuella Azure-nätverket kan matcha DNS-posterna för lokala resurser. Om du vill göra detta måste använda du DNS-vidarebefordrare eller villkorlig vidarebefordran. Mer information finns i [namnmatchning med hjälp av DNS-servern](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>Punkt-till-plats VPN-anslutningen har upprättats, men du kan fortfarande inte kan ansluta till Azure-resurser 

### <a name="cause"></a>Orsak

Det här problemet kan uppstå om VPN-klienten inte får vägarna från Azure VPN-gateway.

### <a name="solution"></a>Lösning

Du löser problemet, [återställa Azure VPN gateway](vpn-gateway-resetgw-classic.md).

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Fel: ”funktionen återkallade certifikat kunde inte kontrollera återkallade certifikat eftersom servern för certfikatåterkallning var offline. (Fel 0x80092013) ”

### <a name="causes"></a>Orsaker
Det här felmeddelandet visas om klienten inte kommer åt http://crl3.digicert.com/ssca-sha2-g1.crl och http://crl4.digicert.com/ssca-sha2-g1.crl.  Från Återkallelsekontrollen kräver åtkomst till dessa två platser.  Det här problemet inträffar vanligtvis på den klient som har en konfigurerad proxyserver. I vissa miljöer om begäranden inte ska via proxyservern kommer den att nekas på Gränsbrandväggen.

### <a name="solution"></a>Lösning

Kontrollera inställningarna för proxyservern, kontrollerar du att klienten kan komma åt http://crl3.digicert.com/ssca-sha2-g1.crl och http://crl4.digicert.com/ssca-sha2-g1.crl.

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>VPN-klientfel: Anslutningen kunde inte på grund av en princip som konfigurerats på RAS/VPN-servern. (Fel 812)

### <a name="cause"></a>Orsak

Det här felet uppstår om RADIUS-servern som du använde för att autentisera VPN-klienten har felaktiga inställningar eller Azure Gateway inte kan nå Radius-servern.

### <a name="solution"></a>Lösning

Se till att RADIUS-server är korrekt konfigurerad. Mer information finns i [integrera RADIUS-autentisering med Azure Multi-Factor Authentication Server](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>”Error 405” när du hämtar rotcertifikat från VPN-Gateway

### <a name="cause"></a>Orsak

Rotcertifikatet har inte installerats. Rotcertifikatet installeras i klientens **certifikat från betrodda** lagra.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>VPN-klientfel: Fjärranslutningen skapades inte eftersom försök VPN-tunnlar misslyckades. (Fel 800) 

### <a name="cause"></a>Orsak

NIC-drivrutinen är inaktuell.

### <a name="solution"></a>Lösning

Uppdatera NIC-drivrutinen:

1. Klicka på **starta**, typ **Enhetshanteraren**, och markera den i listan över resultat. Om du har ombetts ange ett administratörslösenord eller bekräfta, skriver du lösenordet eller ange bekräftelse.
2. I den **nätverkskort** kategorier, hitta det nätverkskort som du vill uppdatera.  
3. Dubbelklicka på namnet på enheten, Välj **Uppdatera drivrutin**väljer **Sök automatiskt efter uppdaterade drivrutiner**.
4. Om Windows inte hittar en ny drivrutin, kan du söka efter en på enhetstillverkarens webbplats och följ instruktionerna.
5. Starta om datorn och försök igen.

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Fel: fel vid hämtning av mål-URI inte har angetts ”fil”

### <a name="cause"></a>Orsak

Detta beror på typen är konfigurerad som en felaktig gateway.

### <a name="solution"></a>Lösning

Azure VPN gateway-typen måste vara VPN och VPN-typ måste vara **RouteBased**.

## <a name="vpn-package-installer-doesnt-complete"></a>VPN-installationspaketet slutförs inte

### <a name="cause"></a>Orsak

Det här problemet kan orsakas av föregående VPN-klientinstallationer. 

### <a name="solution"></a>Lösning

Ta bort de gamla VPN-klientkonfigurationsfilerna från **C:\users\username\AppData\Microsoft\Network\Connections\<VirtualNetworkId >** och kör installationsprogrammet för VPN-klienten igen. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>VPN-klienten i viloläge eller strömsparläge efter en stund

### <a name="solution"></a>Lösning

Kontrollera strömsparläge och viloläge inställningar i den dator som VPN-klienten körs på.
