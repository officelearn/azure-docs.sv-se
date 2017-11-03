---
title: "Felsökning av anslutningsproblem med Azure punkt-till-plats-| Microsoft Docs"
description: "Lär dig att felsöka anslutningsproblem med punkt-till-plats."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/23/2017
ms.author: genli
ms.openlocfilehash: 76ab1600903705aad7f18f48f41cb7119c3c09bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
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

1. Se till att följande certifikat är på rätt plats:

    | Certifikat | Plats |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktuella User\Personal\Certificates |
    | Azuregateway -*GUID*. cloudapp.net  | Aktuella User\Trusted rotcertifikatutfärdare|
    | AzureGateway -*GUID*. cloudapp.net AzureRoot.cer    | Lokal dator\Betrodda certifikatutfärdare|

2. Gå till användare\<användarnamn > \AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID > manuellt installera certifikatet (*.cer-fil) på användaren och datorns Arkiv.

Mer information om hur du installerar klientcertifikatet finns [generera och exportera certifikat för plats-till-plats-anslutningar](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> När du importerar klientcertifikatet inte väljer det **aktivera starkt skydd av den privata nyckeln** alternativet.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN-klientfel: det mottagna meddelandet var oväntat eller felaktigt formaterat

### <a name="symptom"></a>Symtom

När du försöker ansluta till Azure-nätverk med hjälp av VPN-klienten visas följande felmeddelande:

**Det mottagna meddelandet var oväntat eller felaktigt formaterat. (Fel 0x80090326)**

### <a name="cause"></a>Orsak

Det här problemet uppstår om den offentliga nyckeln för rot-certifikatet inte har överförts till Azure VPN-gatewayen. Det kan också inträffa om nyckeln är skadad eller upphört att gälla.

### <a name="solution"></a>Lösning

Lös problemet genom att kontrollera status för rotcertifikat i Azure portal och se om det har återkallats. Om den inte har återkallats, försök att ta bort rotcertifikat och reupload. Mer information finns i [skapa certifikat](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts).

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN-klientfel: en certifikatkedja bearbetas men avslutades 

### <a name="symptom"></a>Symtom 

När du försöker ansluta till Azure-nätverk med hjälp av VPN-klienten visas följande felmeddelande:

**En certifikatkedja bearbetas men avslutades med ett rotcertifikat som inte är betrodd av den betrodda providern.**

### <a name="solution"></a>Lösning

1. Se till att följande certifikat är på rätt plats:

    | Certifikat | Plats |
    | ------------- | ------------- |
    | AzureClient.pfx  | Aktuella User\Personal\Certificates |
    | Azuregateway -*GUID*. cloudapp.net  | Aktuella User\Trusted rotcertifikatutfärdare|
    | AzureGateway -*GUID*. cloudapp.net AzureRoot.cer    | Lokal dator\Betrodda certifikatutfärdare|

2. Försök att ta bort certifikaten och installera om dem om certifikat som redan är på plats. Den  **azuregateway -*GUID*. cloudapp.net** certifikatet finns i VPN-klienten konfigurationspaketet som du hämtade från Azure-portalen. Du kan använda filen archivers för att extrahera filerna från paketet.

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

**Det gick inte att spara virtuell nätverksgateway &lt;* gatewaynamnet*&gt;. Data för certifikatet &lt; *certifikat ID* &gt; är invalid.* *

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

**Det gick inte att spara virtuell nätverksgateway &lt;* gatewaynamnet*&gt;. Resursnamnet &lt; *certifikatnamn som du försöker överföra* &gt; är ogiltig **.

### <a name="cause"></a>Orsak

Det här problemet beror på att namnet på certifikatet innehåller ett ogiltigt tecken, till exempel ett blanksteg. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure portal fel: VPN-paketet filen fel vid hämtning av 503

### <a name="symptom"></a>Symtom

När du försöker hämta konfigurationspaketet för VPN-klienten visas följande felmeddelande:

**Det gick inte att hämta filen. Information om felet: fel 503. Servern är upptagen.**
 
### <a name="solution"></a>Lösning

Det här felet kan bero på tillfälliga nätverksproblem. Försök att hämta VPN-paketet igen efter några minuter.

## <a name="azure-vpn-gateway-upgrade-all-p2s-clients-are-unable-to-connect"></a>Uppgraderingen av Azure VPN-Gateway: alla P2S-klienter kan inte ansluta

### <a name="cause"></a>Orsak

Om certifikatet är mer än 50 procent via dess livslängd certifikatet förnyas.

### <a name="solution"></a>Lösning

Lös problemet genom att skapa och distribuera nya certifikat till VPN-klienter. 

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

Lös problemet genom att ta bort de gamla klienten för VPN-konfigurationsfilerna från **C:\Users\TheUserName\AppData\Roaming\Microsoft\Network\Connections**, och kör sedan installationsprogrammet för VPN-klienten igen.
