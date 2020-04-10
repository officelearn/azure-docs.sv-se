---
title: Säkerhetskonfiguration för delad koppling
description: Konfigurera x409-certifikat för kryptering med tjänsten split/merge för elastisk skala.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: a5ea0fd252d1792d4c40cc6d7869f4ba57edc1ad
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011369"
---
# <a name="split-merge-security-configuration"></a>Säkerhetskonfiguration för delad koppling

Om du vill använda tjänsten Dela/slå samman måste du konfigurera säkerheten korrekt. Tjänsten är en del av funktionen Elastisk skala i Microsoft Azure SQL Database. Mer information finns i [Självstudiekurs för elastisk skala split och koppla tjänst](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Konfigurera certifikat

Certifikat konfigureras på två sätt. 

1. [Så här konfigurerar du TLS/SSL-certifikatet](#to-configure-the-tlsssl-certificate)
2. [Så här konfigurerar du klientcertifikat](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Så här hämtar du certifikat

Certifikat kan erhållas från offentliga certifikatutfärdare eller från [Windows-certifikattjänsten](https://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Dessa är de metoder som föredras för att hämta certifikat.

Om dessa alternativ inte är tillgängliga kan du generera **självsignerade certifikat**.

## <a name="tools-to-generate-certificates"></a>Verktyg för att generera certifikat

* [makecert.exe](https://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](https://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Så här kör du verktygen

* I Kommandotolken för Utvecklare för Visual Studios finns i [Kommandotolken i Visual Studio](https://msdn.microsoft.com/library/ms229859.aspx) 
  
    Om det är installerat går du till:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* Hämta WDK från [Windows 8.1: Ladda ned paket och verktyg](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-tlsssl-certificate"></a>Så här konfigurerar du TLS/SSL-certifikatet

Ett TLS/SSL-certifikat krävs för att kryptera kommunikationen och autentisera servern. Välj den mest tillämpliga av de tre scenarierna nedan och kör alla dess steg:

### <a name="create-a-new-self-signed-certificate"></a>Skapa ett nytt självsignerat certifikat

1. [Skapa ett självsignerat certifikat](#create-a-self-signed-certificate)
2. [Skapa PFX-fil för självsignerat TLS/SSL-certifikat](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Ladda upp TLS/SSL-certifikat till molntjänsten](#upload-tlsssl-certificate-to-cloud-service)
4. [Uppdatera TLS/SSL-certifikat i tjänstkonfigurationsfilen](#update-tlsssl-certificate-in-service-configuration-file)
5. [Importera TLS/SSL-certifikatutfärdare](#import-tlsssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Så här använder du ett befintligt certifikat från certifikatarkivet
1. [Exportera TLS/SSL-certifikat från certifikatarkivet](#export-tlsssl-certificate-from-certificate-store)
2. [Ladda upp TLS/SSL-certifikat till molntjänsten](#upload-tlsssl-certificate-to-cloud-service)
3. [Uppdatera TLS/SSL-certifikat i tjänstkonfigurationsfilen](#update-tlsssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Så här använder du ett befintligt certifikat i en PFX-fil
1. [Ladda upp TLS/SSL-certifikat till molntjänsten](#upload-tlsssl-certificate-to-cloud-service)
2. [Uppdatera TLS/SSL-certifikat i tjänstkonfigurationsfilen](#update-tlsssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Så här konfigurerar du klientcertifikat
Klientcertifikat krävs för att autentisera begäranden till tjänsten. Välj den mest tillämpliga av de tre scenarierna nedan och kör alla dess steg:

### <a name="turn-off-client-certificates"></a>Inaktivera klientcertifikat
1. [Inaktivera klientcertifikatbaserad autentisering](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Utfärda nya självsignerade klientcertifikat
1. [Skapa en självsignerad certifikatutfärdare](#create-a-self-signed-certification-authority)
2. [Ladda upp CA-certifikat till molntjänsten](#upload-ca-certificate-to-cloud-service)
3. [Uppdatera certifikatutfärdarcertifikat i tjänstkonfigurationsfilen](#update-ca-certificate-in-service-configuration-file)
4. [Utfärda klientcertifikat](#issue-client-certificates)
5. [Skapa PFX-filer för klientcertifikat](#create-pfx-files-for-client-certificates)
6. [Importera klientcertifikat](#import-client-certificate)
7. [Tumavtryck för klientcertifikat](#copy-client-certificate-thumbprints)
8. [Konfigurera tillåtna klienter i tjänstkonfigurationsfilen](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Använda befintliga klientcertifikat
1. [Hitta den offentliga certifikatutfärdaren](#find-ca-public-key)
2. [Ladda upp CA-certifikat till molntjänsten](#upload-ca-certificate-to-cloud-service)
3. [Uppdatera certifikatutfärdarcertifikat i tjänstkonfigurationsfilen](#update-ca-certificate-in-service-configuration-file)
4. [Tumavtryck för klientcertifikat](#copy-client-certificate-thumbprints)
5. [Konfigurera tillåtna klienter i tjänstkonfigurationsfilen](#configure-allowed-clients-in-the-service-configuration-file)
6. [Konfigurera kontroll av återkallning av klientcertifikat](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Åtkomst till tjänstslutpunkterna kan begränsas till specifika intervall av IP-adresser.

## <a name="to-configure-encryption-for-the-store"></a>Så här konfigurerar du kryptering för arkivet
Ett certifikat krävs för att kryptera autentiseringsuppgifterna som lagras i metadataarkivet. Välj den mest tillämpliga av de tre scenarierna nedan och kör alla dess steg:

### <a name="use-a-new-self-signed-certificate"></a>Använda ett nytt självsignerat certifikat
1. [Skapa ett självsignerat certifikat](#create-a-self-signed-certificate)
2. [Skapa PFX-fil för självsignerat krypteringscertifikat](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Ladda upp krypteringscertifikat till molntjänsten](#upload-encryption-certificate-to-cloud-service)
4. [Uppdatera krypteringscertifikat i tjänstkonfigurationsfilen](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Använda ett befintligt certifikat från certifikatarkivet
1. [Exportera krypteringscertifikat från certifikatarkivet](#export-encryption-certificate-from-certificate-store)
2. [Ladda upp krypteringscertifikat till molntjänsten](#upload-encryption-certificate-to-cloud-service)
3. [Uppdatera krypteringscertifikat i tjänstkonfigurationsfilen](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Använda ett befintligt certifikat i en PFX-fil
1. [Ladda upp krypteringscertifikat till molntjänsten](#upload-encryption-certificate-to-cloud-service)
2. [Uppdatera krypteringscertifikat i tjänstkonfigurationsfilen](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Standardkonfigurationen
Standardkonfigurationen nekar all åtkomst till HTTP-slutpunkten. Detta är den rekommenderade inställningen, eftersom begäranden till dessa slutpunkter kan innehålla känslig information som databasautentiseringsuppgifter.
Standardkonfigurationen tillåter all åtkomst till HTTPS-slutpunkten. Den här inställningen kan begränsas ytterligare.

### <a name="changing-the-configuration"></a>Ändra konfigurationen
Den grupp av åtkomstkontrollregler som gäller för och slutpunkt konfigureras i avsnittet ** \<EndpointAcls>** i **tjänstkonfigurationsfilen**.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

Reglerna i en åtkomstkontrollgrupp konfigureras \<i avsnittet AccessControl name=""> i tjänstkonfigurationsfilen. 

Formatet förklaras i dokumentationen för kontrolllistor för nätverksåtkomst.
Om du till exempel bara vill tillåta IPs i intervallet 100.100.0.0 till 100.100.255.255 för att komma åt HTTPS-slutpunkten, skulle reglerna se ut så här:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Förebyggande av överbelastning
Det finns två olika mekanismer som stöds för att upptäcka och förhindra Denial of Service-attacker:

* Begränsa antalet samtidiga begäranden per fjärrvärd (inaktivera som standard)
* Begränsa åtkomsthastigheten per fjärrvärd (på som standard)

Dessa baseras på de funktioner som ytterligare dokumenteras i dynamisk IP-säkerhet i IIS. När du ändrar den här konfigurationen akta dig för följande faktorer:

* Beteendet hos proxyservrar och nätverksadressöversättningsenheter över fjärrvärdinformationen
* Varje begäran till alla resurser i webbrollen beaktas (t.ex. läsa in skript, bilder osv.)

## <a name="restricting-number-of-concurrent-accesses"></a>Begränsa antalet samtidiga åtkomster
De inställningar som konfigurerar det här beteendet är:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Ändra DynamicIpRestrictionDenyByConcurrentRequests till true för att aktivera detta skydd.

## <a name="restricting-rate-of-access"></a>Begränsa åtkomstgraden
De inställningar som konfigurerar det här beteendet är:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Konfigurera svaret på en nekad begäran
Följande inställning konfigurerar svaret på en nekad begäran:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

Mer information om dynamisk IP-säkerhet i IIS finns i dokumentationen för dynamisk IP-säkerhet i IIS.

## <a name="operations-for-configuring-service-certificates"></a>Åtgärder för att konfigurera tjänstcertifikat
Det här avsnittet är endast till referens. Följ konfigurationsstegen som beskrivs i:

* Konfigurera TLS/SSL-certifikatet
* Konfigurera klientcertifikat

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat
Utföra:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha256 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Så här anpassar du:

* -n med tjänstens URL. Jokertecken ("CN=*.cloudapp.net") och alternativa namn ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") stöds.
* -e med certifikatets utgångsdatum Skapa ett starkt lösenord och ange det när du uppmanas att göra det.

## <a name="create-pfx-file-for-self-signed-tlsssl-certificate"></a>Skapa PFX-fil för självsignerat TLS/SSL-certifikat
Utföra:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Ange lösenord och exportera sedan certifikat med följande alternativ:

* Ja, exportera den privata nyckeln
* Exportera alla utökade egenskaper

## <a name="export-tlsssl-certificate-from-certificate-store"></a>Exportera TLS/SSL-certifikat från certifikatarkivet
* Hitta certifikat
* Klicka på Åtgärder -> Alla aktiviteter -> Exportera...
* Exportera certifikat till en . PFX-fil med följande alternativ:
  * Ja, exportera den privata nyckeln
  * Inkludera alla certifikat i certifieringssökvägen om möjligt *Exportera alla utökade egenskaper

## <a name="upload-tlsssl-certificate-to-cloud-service"></a>Ladda upp TLS/SSL-certifikat till molntjänsten
Ladda upp certifikat med befintliga eller genererade . PFX-fil med TLS-tangentparet:

* Ange lösenordet som skyddar den privata nyckelinformationen

## <a name="update-tlsssl-certificate-in-service-configuration-file"></a>Uppdatera TLS/SSL-certifikat i tjänstkonfigurationsfilen
Uppdatera tumavtrycksvärdet för följande inställning i tjänstkonfigurationsfilen med tumavtrycket för certifikatet som överförs till molntjänsten:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-tlsssl-certification-authority"></a>Importera TLS/SSL-certifikatutfärdare
Följ dessa steg i alla konto/datorer som kommunicerar med tjänsten:

* Dubbelklicka på . CER-fil i Utforskaren
* Klicka på Installera certifikat i dialogrutan Certifikat...
* Importera certifikat till arkivet betrodda rotcertifikatutfärdare

## <a name="turn-off-client-certificate-based-authentication"></a>Inaktivera klientcertifikatbaserad autentisering
Endast klientcertifikatbaserad autentisering stöds och inaktivera den ger offentlig åtkomst till tjänstens slutpunkter, såvida inte andra mekanismer finns på plats (till exempel Microsoft Azure Virtual Network).

Ändra de här inställningarna till false i tjänstkonfigurationsfilen för att stänga av funktionen:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Kopiera sedan samma tumavtryck som TLS/SSL-certifikatet i certifikatinställningen för certifikatutfärdaren:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Skapa en självsignerad certifikatutfärdare
Kör följande steg för att skapa ett självsignerat certifikat för att fungera som certifikatutfärdare:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha256 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Så här anpassar du den

* -e med certifieringen utgångsdatum

## <a name="find-ca-public-key"></a>Hitta den offentliga certifikatutfärdaren
Alla klientcertifikat måste ha utfärdats av en certifikatutfärdare som är betrodd av tjänsten. Hitta den offentliga nyckeln till certifikatutfärdaren som utfärdade klientcertifikaten som ska användas för autentisering för att ladda upp den till molntjänsten.

Om filen med den offentliga nyckeln inte är tillgänglig exporterar du den från certifikatarkivet:

* Hitta certifikat
  * Sök efter ett klientcertifikat utfärdat av samma certifikatutfärdare
* Dubbelklicka på certifikatet.
* Välj fliken Certifieringssökväg i dialogrutan Certifikat.
* Dubbelklicka på ca-posten i sökvägen.
* Gör anteckningar om certifikategenskaperna.
* Stäng **dialogrutan Certifikat.**
* Hitta certifikat
  * Sök efter ca som anges ovan.
* Klicka på Åtgärder -> Alla aktiviteter -> Exportera...
* Exportera certifikat till en . CER med dessa alternativ:
  * **Nej, exportera inte den privata nyckeln**
  * Inkludera om möjligt alla certifikat i certifieringssökvägen.
  * Exportera alla utökade egenskaper.

## <a name="upload-ca-certificate-to-cloud-service"></a>Ladda upp CA-certifikat till molntjänst
Ladda upp certifikat med befintliga eller genererade . CER-fil med den offentliga ca-nyckeln.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Uppdatera certifikatutfärdarcertifikat i tjänstkonfigurationsfilen
Uppdatera tumavtrycksvärdet för följande inställning i tjänstkonfigurationsfilen med tumavtrycket för certifikatet som överförs till molntjänsten:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Uppdatera värdet för följande inställning med samma tumavtryck:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Utfärda klientcertifikat
Varje person som har behörighet att komma åt tjänsten bör ha ett klientcertifikat utfärdat för exklusiv användning och bör välja sitt eget starka lösenord för att skydda sin privata nyckel. 

Följande steg måste utföras på samma dator där det självsignerade certifikatutfärdarcertifikatet genererades och lagras:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha256 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Anpassa:

* -n med ett ID för klienten som ska autentiseras med det här certifikatet
* -e med certifikatets utgångsdatum
* MyID.pvk och MyID.cer med unika filnamn för det här klientcertifikatet

Det här kommandot kommer att fråga efter att ett lösenord ska skapas och sedan användas en gång. Använd ett starkt krypterat lösenord.

## <a name="create-pfx-files-for-client-certificates"></a>Skapa PFX-filer för klientcertifikat
Kör för varje genererat klientcertifikat:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Anpassa:

    MyID.pvk and MyID.cer with the filename for the client certificate

Ange lösenord och exportera sedan certifikat med följande alternativ:

* Ja, exportera den privata nyckeln
* Exportera alla utökade egenskaper
* Den person som certifikatet utfärdas till bör välja exportlösenordet

## <a name="import-client-certificate"></a>Importera klientcertifikat
Varje individ för vilken ett klientcertifikat har utfärdats bör importera nyckelparet i de maskiner som de använder för att kommunicera med tjänsten:

* Dubbelklicka på . PFX-fil i Utforskaren
* Importera certifikat till det personliga arkivet med minst det här alternativet:
  * Inkludera alla utökade egenskaper markerade

## <a name="copy-client-certificate-thumbprints"></a>Kopiera tumavtryck för klientcertifikat
Varje person för vilken ett klientcertifikat har utfärdats måste följa dessa steg för att få tumavtrycket av sitt certifikat, som kommer att läggas till i tjänstkonfigurationsfilen:

* Kör certmgr.exe
* Välj fliken Personligt
* Dubbelklicka på klientcertifikatet som ska användas för autentisering
* Välj fliken Information i dialogrutan Certifikat som öppnas
* Kontrollera att Visa visar alla
* Markera fältet Med namnet Tumavtryck i listan
* Kopiera tumavtryckets värde
  * Ta bort icke-synliga Unicode-tecken framför den första siffran
  * Ta bort alla blanksteg

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Konfigurera tillåtna klienter i tjänstkonfigurationsfilen
Uppdatera värdet för följande inställning i tjänstkonfigurationsfilen med en kommaavgränsad lista över tumavtrycken för klientcertifikaten som får åtkomst till tjänsten:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Konfigurera kontroll av återkallning av klientcertifikat
Standardinställningen kontrollerar inte med certifikatutfärdaren om klientcertifikatåterkallningsstatus. Om den certifikatutfärdare som utfärdade klientcertifikaten stöder sådana kontroller aktiverar du följande inställning med ett av de värden som definierats i X509RevocationMode-uppräkningen:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Skapa PFX-fil för självsignerade krypteringscertifikat
Kör för ett krypteringscertifikat:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Anpassa:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Ange lösenord och exportera sedan certifikat med följande alternativ:

* Ja, exportera den privata nyckeln
* Exportera alla utökade egenskaper
* Du behöver lösenordet när du laddar upp certifikatet till molntjänsten.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exportera krypteringscertifikat från certifikatarkivet
* Hitta certifikat
* Klicka på Åtgärder -> Alla aktiviteter -> Exportera...
* Exportera certifikat till en . PFX-fil med följande alternativ: 
  * Ja, exportera den privata nyckeln
  * Inkludera alla certifikat i certifieringssökvägen om möjligt 
* Exportera alla utökade egenskaper

## <a name="upload-encryption-certificate-to-cloud-service"></a>Ladda upp krypteringscertifikat till molntjänst
Ladda upp certifikat med befintliga eller genererade . PFX-fil med krypteringsnyckelparet:

* Ange lösenordet som skyddar den privata nyckelinformationen

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Uppdatera krypteringscertifikat i tjänstkonfigurationsfilen
Uppdatera tumavtrycksvärdet för följande inställningar i tjänstkonfigurationsfilen med tumavtrycket för certifikatet som överförs till molntjänsten:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Vanliga certifikatåtgärder
* Konfigurera TLS/SSL-certifikatet
* Konfigurera klientcertifikat

## <a name="find-certificate"></a>Hitta certifikat
Följ de här stegen:

1. Kör mmc.exe.
2. Fil -> Lägg till/ta bort snapin-modulen...
3. Välj **Certifikat**.
4. Klicka på **Lägg till**.
5. Välj certifikatarkivets plats.
6. Klicka på **Slutför**.
7. Klicka på **OK**.
8. Expandera **certifikat**.
9. Expandera noden för certifikatarkiv.
10. Expandera den underordnade certifikatnoden.
11. Välj ett certifikat i listan.

## <a name="export-certificate"></a>Exportera certifikatet
I **guiden Exportera certifikat:**

1. Klicka på **Nästa**.
2. Välj **Ja**och exportera sedan **den privata nyckeln**.
3. Klicka på **Nästa**.
4. Välj önskat utdatafilformat.
5. Kontrollera de önskade alternativen.
6. Kontrollera **lösenord**.
7. Ange ett starkt lösenord och bekräfta det.
8. Klicka på **Nästa**.
9. Skriv eller bläddra i ett filnamn där certifikatet ska lagras (använd ett . PFX-förlängning).
10. Klicka på **Nästa**.
11. Klicka på **Slutför**.
12. Klicka på **OK**.

## <a name="import-certificate"></a>Importera certifikatet
I guiden Importera certifikat:

1. Välj butiksplats.
   
   * Välj **Aktuell användare** om endast processer som körs under aktuell användare kommer åt tjänsten
   * Välj **Lokal dator** om andra processer i den här datorn kommer åt tjänsten
2. Klicka på **Nästa**.
3. Om du importerar från en fil bekräftar du sökvägen.
4. Om du importerar en . PFX-fil:
   1. Ange lösenordet som skyddar den privata nyckeln
   2. Välj importalternativ
5. Välj "Placera" certifikat i följande butik
6. Klicka på **Browse** (Bläddra).
7. Välj önskad butik.
8. Klicka på **Slutför**.
   
   * Om arkivet betrodd rotcertifikatutfärdare har valts klickar du på **Ja**.
9. Klicka på **OK** i alla dialogfönster.

## <a name="upload-certificate"></a>Överför certifikat
I [Azure Portal](https://portal.azure.com/)

1. Välj **Molntjänster**.
2. Välj molntjänsten.
3. Klicka på **Certifikat**på den övre menyn .
4. Klicka på Ladda **upp**i det nedre fältet .
5. Markera certifikatfilen.
6. Om det är en . PFX-filen anger du lösenordet för den privata nyckeln.
7. När du är klar kopierar du certifikatets tumavtryck från den nya posten i listan.

## <a name="other-security-considerations"></a>Andra säkerhetsöverväganden
TLS-inställningarna som beskrivs i det här dokumentet krypterar kommunikationen mellan tjänsten och dess klienter när HTTPS-slutpunkten används. Detta är viktigt eftersom autentiseringsuppgifter för databasåtkomst och potentiellt annan känslig information finns i kommunikationen. Observera dock att tjänsten behåller intern status, inklusive autentiseringsuppgifter, i sina interna tabeller i Microsoft Azure SQL-databasen som du har angett för metadatalagring i din Microsoft Azure-prenumeration. Databasen definierades som en del av följande inställning i tjänstkonfigurationsfilen (. CSCFG-fil): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

Autentiseringsuppgifter som lagras i den här databasen krypteras. Se dock till att både webb- och arbetarrollerna för dina tjänstdistributioner hålls uppdaterade och säkra eftersom de båda har åtkomst till metadatadatabasen och certifikatet som används för kryptering och dekryptering av lagrade autentiseringsuppgifter. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

