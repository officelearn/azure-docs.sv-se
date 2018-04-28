---
title: Felsökning av anslutningsproblem med Azure punkt-till-plats-| Microsoft Docs
description: Lär dig att felsöka anslutningsproblem med punkt-till-plats.
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
ms.date: 02/23/2018
ms.author: genli
ms.openlocfilehash: 503dcd87bf87ffb204b03780fc11f30825ac35ef
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Felsökning: Anslutningsproblem med Azure punkt-till-plats

Den här artikeln innehåller vanliga anslutningsproblem som kan uppstå i punkt-till-plats. Här beskrivs också möjliga orsaker och lösningar på problemen.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>VPN-klientfel: Det gick inte att hitta ett certifikat

### <a name="symptom"></a>Symtom

När du försöker ansluta till Azure-nätverk med hjälp av VPN-klienten visas följande felmeddelande:

**Det gick inte att hitta ett certifikat som kan användas med Extensible Authentication Protocol. (Fel 798)**

### <a name="cause"></a>Orsak

Det här problemet uppstår om klientcertifikatet saknas från **certifikat - aktuell User\Personal\Certificates**.

### <a name="solution"></a>Lösning

Följ dessa steg för att lösa problemet:

1. Öppna Certifikathanteraren: Klicka på **starta**, typen **hantera datorcertifikat**, och klicka sedan på **hantera datorcertifikat** i sökresultatet.

2. Se till att följande certifikat är på rätt plats:

    | Certifikat | Plats |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktuella User\Personal\Certificates |
    | Azuregateway-*GUID*.cloudapp.net  | Aktuella User\Trusted rotcertifikatutfärdare|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Lokal dator\Betrodda certifikatutfärdare|

3. Gå till användare\<användarnamn > \AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID > manuellt installera certifikatet (*.cer-fil) på användaren och datorns Arkiv.

Mer information om hur du installerar klientcertifikatet finns [generera och exportera certifikat för plats-till-plats-anslutningar](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> När du importerar klientcertifikatet inte väljer det **aktivera starkt skydd av den privata nyckeln** alternativet.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN-klientfel: det mottagna meddelandet var oväntat eller felaktigt formaterat

### <a name="symptom"></a>Symtom

När du försöker ansluta till Azure-nätverk med hjälp av VPN-klienten visas följande felmeddelande:

**Det mottagna meddelandet var oväntat eller felaktigt formaterat. (Fel 0x80090326)**

### <a name="cause"></a>Orsak

Det här problemet uppstår om någon av följande villkor är uppfyllda:

- Använd användardefinierade vägar (UDR) med standardvägen på Gateway-undernätet har angetts felaktigt.
- Den offentliga nyckeln för rot-certifikatet har inte överförts till Azure VPN-gatewayen. 
- Nyckeln är skadad eller upphört att gälla.

### <a name="solution"></a>Lösning

Följ dessa steg för att lösa problemet:

1. Ta bort UDR på Gateway-undernätet. Kontrollera att UDR vidarebefordrar all trafik korrekt.
2. Kontrollera status för rotcertifikat i Azure portal och se om det har återkallats. Om den inte har återkallats, försök att ta bort rotcertifikat och reupload. Mer information finns i [skapa certifikat](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN-klientfel: en certifikatkedja bearbetas men avslutades 

### <a name="symptom"></a>Symtom 

När du försöker ansluta till Azure-nätverk med hjälp av VPN-klienten visas följande felmeddelande:

**En certifikatkedja bearbetas men avslutades med ett rotcertifikat som inte är betrodd av den betrodda providern.**

### <a name="solution"></a>Lösning

1. Se till att följande certifikat är på rätt plats:

    | Certifikat | Plats |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktuella User\Personal\Certificates |
    | Azuregateway-*GUID*.cloudapp.net  | Aktuella User\Trusted rotcertifikatutfärdare|
    | AzureGateway-*GUID*.cloudapp.net, AzureRoot.cer    | Lokal dator\Betrodda certifikatutfärdare|

2. Försök att ta bort certifikaten och installera om dem om certifikat som redan är på plats. Den **azuregateway -*GUID*. cloudapp.net** certifikatet finns i VPN-klienten konfigurationspaketet som du hämtade från Azure-portalen. Du kan använda filen archivers för att extrahera filerna från paketet.

## <a name="file-download-error-target-uri-is-not-specified"></a>Fel vid hämtning av filen: mål-URI har inte angetts

### <a name="symptom"></a>Symtom

Följande felmeddelande visas:

**Fel vid hämtning av filen. Mål-URI har inte angetts.**

### <a name="cause"></a>Orsak 

Det här problemet uppstår på grund av en felaktig gateway-typen. 

### <a name="solution"></a>Lösning

VPN gateway-typen måste vara **VPN**, och VPN-typ måste vara **RouteBased**.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>VPN-klientfel: Azure VPN-anpassade skript misslyckades 

### <a name="symptom"></a>Symtom

När du försöker ansluta till Azure-nätverk med hjälp av VPN-klienten visas följande felmeddelande:

**Det gick inte att anpassat skript (för att uppdatera routningstabellen). (Fel 8007026f)**

### <a name="cause"></a>Orsak

Det här problemet kan inträffa om du försöker öppna plats-till-punkt VPN-anslutning med hjälp av en genväg.

### <a name="solution"></a>Lösning 

Öppna direkt i stället för att öppna den från genvägen VPN-paketet.

## <a name="cannot-install-the-vpn-client"></a>Det går inte att installera VPN-klienten

### <a name="cause"></a>Orsak 

Det krävs en ytterligare certifikat ska lita på VPN-gateway för det virtuella nätverket. Certifikatet ingår i VPN-klienten konfigurationspaketet som genereras från Azure-portalen.

### <a name="solution"></a>Lösning

Extrahera VPN-klientpaketet för konfiguration och hitta .cer-fil. Följ dessa steg för att installera certifikat:

1. Öppna mmc.exe.
2. Lägg till den **certifikat** snapin-modulen.
3. Välj den **datorn** kontot för den lokala datorn.
4. Högerklicka på den **betrodda rotcertifikatutfärdare** nod. Klicka på **All aktivitet** > **Import**, och bläddra till den .cer-fil som du har extraherat från VPN-klientpaketet för konfigurationen.
5. Starta om datorn. 
6. Försök att installera VPN-klienten.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Azure portal fel: Det gick inte att spara VPN-gateway och data är ogiltiga

### <a name="symptom"></a>Symtom

När du försöker spara ändringarna för VPN-gateway i Azure-portalen, visas följande felmeddelande:

**Det gick inte att spara virtuell nätverksgateway &lt; *gatewaynamnet*&gt;. Data för certifikatet &lt; *certifikat ID* &gt; är ogiltig.**

### <a name="cause"></a>Orsak 

Det här problemet kan inträffa om rot certifikatets offentliga nyckel som du överfört innehåller ett ogiltigt tecken, till exempel ett blanksteg.

### <a name="solution"></a>Lösning

Se till att data i certifikatet inte innehåller ogiltiga tecken, till exempel radbrytningar (vagnreturer). Hela värdet ska vara en lång rad. Följande är ett exempel på certifikatet:

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

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Azure portal fel: Det gick inte att spara VPN-gateway och resursnamnet är ogiltigt

### <a name="symptom"></a>Symtom

När du försöker spara ändringarna för VPN-gateway i Azure-portalen, visas följande felmeddelande: 

**Det gick inte att spara virtuell nätverksgateway &lt; *gatewaynamnet*&gt;. Resursnamnet &lt; *certifikatnamn som du försöker överföra* &gt; är ogiltig**.

### <a name="cause"></a>Orsak

Det här problemet beror på att namnet på certifikatet innehåller ett ogiltigt tecken, till exempel ett blanksteg. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure portal fel: VPN-paketet filen fel vid hämtning av 503

### <a name="symptom"></a>Symtom

När du försöker hämta konfigurationspaketet för VPN-klienten visas följande felmeddelande:

**Det gick inte att hämta filen. Information om felet: fel 503. Servern är upptagen.**
 
### <a name="solution"></a>Lösning

Det här felet kan bero på tillfälliga nätverksproblem. Försök att hämta VPN-paketet igen efter några minuter.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Uppgraderingen av Azure VPN-Gateway: alla punkt till plats-klienter kan inte ansluta

### <a name="cause"></a>Orsak

Om certifikatet är mer än 50 procent via dess livslängd certifikatet förnyas.

### <a name="solution"></a>Lösning

Lös problemet genom att omdistribuera pekar på platsen paketet på alla klienter.

## <a name="too-many-vpn-clients-connected-at-once"></a>För många VPN-klienter anslutna samtidigt

För varje VPN-gateway är det maximala antalet tillåtna anslutningar 128. Du kan se det totala antalet anslutna klienter i Azure-portalen.

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-the-route-table"></a>Punkt-till-plats VPN felaktigt lägger till en väg för 10.0.0.0/8 routningstabellen

### <a name="symptom"></a>Symtom

När du ringer VPN-anslutningen på klienten punkt-till-plats VPN-klienten bör lägga till en väg till virtuella Azure-nätverket. IP-helper-tjänsten bör du lägga till en väg för undernätet för VPN-klienter. 

Intervallet för VPN-klienten tillhör ett mindre undernät 10.0.0.0/8, till exempel 10.0.12.0/24. I stället för en väg för 10.0.12.0/24 läggs en väg för 10.0.0.0/8 som har högre prioritet. 

Felaktig vägen bryts anslutningen till andra lokala nätverk som kanske tillhör ett annat undernät inom 10.0.0.0/8, till exempel 10.50.0.0/24, som inte har en specifik väg som har definierats. 

### <a name="cause"></a>Orsak

Det här beteendet är avsiktligt för Windows-klienter. När klienten använder PPP IPCP-protokollet, får IP-adressen för tunnelgränssnittet från servern (VPN-gateway i det här fallet). Men på grund av en begränsning i protokollet saknar klienten nätmask. Eftersom det inte finns något annat sätt att hämta den, försöker klienten att gissa nätmasken baserat på klass av gränssnittet IP-adressen för tunneln. 

Därför läggs en väg baserat på följande avbildningar: 

Om adressen hör till klass A gäller/8 som för-->

Om adressen hör till klass B--> gäller /16

Om adressen hör till klass C--> gäller /24

### <a name="solution"></a>Lösning

Ha vägar för andra nätverk som matas in i routningstabellen med längsta prefixmatchning eller lägre Mått (därför högre prioritet) än punkten till plats. 

## <a name="vpn-client-cannot-access-network-file-shares"></a>VPN-klienten kan inte komma åt filresurser över nätverket

### <a name="symptom"></a>Symtom

VPN-klienten har anslutit till Azure-nätverket. Dock klienten kan inte komma åt nätverksresurser.

### <a name="cause"></a>Orsak

SMB-protokollet används för åtkomst till resursen för filen. När anslutningen initieras, VPN-klienten lägger till sessionen autentiseringsuppgifter och felet inträffar. När anslutningen har upprättats tvingas klienten att använda cache-autentiseringsuppgifter för Kerberos-autentisering. Den här processen initierar frågor till Key Distribution Center (domänkontrollanten) att hämta en token. Eftersom klienten ansluter från Internet, går det kanske inte att nå domänkontrollanten. Därför kan klienten inte kan växla över från Kerberos NTLM. 

Den enda gången uppmanas klienten för en autentiseringsuppgift är när den har ett giltigt certifikat (med SAN = UPN) utfärdat av domänen som är ansluten. Klienten måste också vara fysiskt ansluten till nätverket domän. I så fall måste klienten försöker använda certifikatet och når till domänkontrollanten. Key Distribution Center returnerar ett ”KDC_ERR_C_PRINCIPAL_UNKNOWN”-fel. Klienten tvingas att växla över till NTLM. 

### <a name="solution"></a>Lösning

Undvik problemet genom att inaktivera cachelagring av autentiseringsuppgifter för domänen från följande registerundernyckel: 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Det går inte att hitta punkt-till-plats VPN-anslutningen i Windows efter ominstallationen VPN-klienten

### <a name="symptom"></a>Symtom

Du tar bort punkt-till-plats VPN-anslutningen och sedan installera om VPN-klienten. I den här situationen har VPN-anslutningen inte konfigurerats korrekt. Du inte ser VPN-anslutningen i den **nätverksanslutningar** inställningar i Windows.

### <a name="solution"></a>Lösning

Lös problemet genom att ta bort de gamla klienten för VPN-konfigurationsfilerna från **C:\users\username\AppData\Microsoft\Network\Connections\<VirtualNetworkId >**, och kör sedan installationsprogrammet för VPN-klienten igen.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Punkt-till-plats VPN-klienten inte kan matcha FQDN för resurser i den lokala domänen

### <a name="symptom"></a>Symtom

När klienten ansluter till Azure med hjälp av punkt-till-plats VPN-anslutning, kan den inte kan lösa FQND resurser i den lokala domänen.

### <a name="cause"></a>Orsak

Punkt-till-plats VPN-klienten använder Azure DNS-servrar som konfigurerats i Azure-nätverket. Azure DNS-servrar åsidosätter de lokala DNS-servrar som konfigurerats i klienten, så alla DNS-frågor skickas till Azure DNS-servrar. Om Azure DNS-servrar inte har på posterna för de lokala resurserna, misslyckas.

### <a name="solution"></a>Lösning

Lös problemet genom att kontrollera att Azure DNS-servrar som används på virtuella Azure-nätverket kan matcha DNS-posterna för lokala resurser. Om du vill göra detta måste använda du DNS-vidarebefordrare eller villkorlig vidarebefordran. Mer information finns i [namnmatchning med hjälp av DNS-servern](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>Punkt-till-plats VPN-anslutningen har upprättats, men du fortfarande inte kan ansluta till Azure-resurser 

### <a name="cause"></a>Orsak

Det här problemet kan uppstå om VPN-klienten inte får vägar från Azure VPN-gateway.

### <a name="solution"></a>Lösning

Lös problemet, [återställa Azure VPN-gateway](vpn-gateway-resetgw-classic.md).

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Fel: ”återkallade funktionen kunde inte återkallningskontroll eftersom återkallade server var offline. (Fel 0x80092013) ”

### <a name="causes"></a>Orsaker
Det här felmeddelandet visas om klienten inte kan komma åt http://crl3.digicert.com/ssca-sha2-g1.crl och http://crl4.digicert.com/ssca-sha2-g1.cr.  Återkallningskontroll kräver åtkomst till dessa två platser.  Det här problemet inträffar oftast på klienten som har en konfigurerad proxyserver. I vissa miljöer om begäranden inte kommer via proxyserver, kommer den att nekas på Gränsbrandväggen.

### <a name="solution"></a>Lösning

Kontrollera inställningarna för proxyservern, kontrollerar du att klienten kan komma åt http://crl3.digicert.com/ssca-sha2-g1.crl och http://crl4.digicert.com/ssca-sha2-g1.cr.

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>VPN-klientfel: Anslutningen kunde inte på grund av en princip som konfigurerats på RAS/VPN-servern. (Fel 812)

### <a name="cause"></a>Orsak

Det här felet uppstår om RADIUS-server som du använde för att autentisera VPN-klienten har felaktiga inställningar eller Azure Gateway inte kan nå Radius-servern.

### <a name="solution"></a>Lösning

Kontrollera att RADIUS-server är korrekt konfigurerad. Mer information finns i [integrera RADIUS-autentisering med Azure Multi-Factor Authentication-servern](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>”Error 405” när du hämtar rotcertifikat från VPN-Gateway

### <a name="cause"></a>Orsak

Rotcertifikatet har inte installerats. Rotcertifikatet är installerat i klientens **certifikat från betrodda** lagras.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>VPN-klientfel: Fjärranslutningen skapades inte eftersom försök VPN-tunnlar misslyckades. (Fel 800) 

### <a name="cause"></a>Orsak

NIC-drivrutinen är inaktuellt.

### <a name="solution"></a>Lösning

Uppdatera NIC-drivrutinen:

1. Klicka på **starta**, typen **Enhetshanteraren**, och markera den i listan över resultat. Om du uppmanas ange ett administratörslösenord eller en bekräftelse skriver du lösenordet eller lämna bekräftelse.
2. I den **nätverkskort** kategorier, hitta nätverkskort som du vill uppdatera.  
3. Dubbelklicka på namnet på enheten, Välj **Uppdatera drivrutin**väljer **Sök automatiskt efter uppdaterade drivrutiner**.
4. Om Windows inte finns en ny drivrutin, du följa instruktionerna du söka efter en på enhetstillverkarens webbplats.
5. Starta om datorn och försök igen.

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Fel: fel vid hämtning av mål-URI inte har angetts-filen'

### <a name="cause"></a>Orsak

Detta beror på typen är konfigurerad som en felaktig gateway.

### <a name="solution"></a>Lösning

Azure VPN gateway-typen måste vara VPN och VPN-typ måste vara **RouteBased**.

## <a name="vpn-package-installer-doesnt-complete"></a>VPN-installationspaketet slutföra inte

### <a name="cause"></a>Orsak

Det här problemet kan orsakas av föregående VPN-klientinstallationer. 

### <a name="solution"></a>Lösning

Ta bort de gamla klienten för VPN-konfigurationsfilerna från **C:\users\username\AppData\Microsoft\Network\Connections\<VirtualNetworkId >** och kör installationsprogrammet för VPN-klienten igen. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>VPN-klienten i viloläge eller strömsparläge efter en stund

### <a name="solution"></a>Lösning

Kontrollera strömsparläge och viloläge inställningar på datorn som VPN-klienten körs på.
