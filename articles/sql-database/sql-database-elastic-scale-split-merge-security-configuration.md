---
title: Dela / sammanslå säkerhetskonfiguration | Microsoft Docs
description: Konfigurera x409 certifikat för kryptering med dela/sammanslå-tjänsten för elastisk skala.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 7ca7e653cc42323f4313ef955de40416154b4ecf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60335232"
---
# <a name="split-merge-security-configuration"></a>Dela / sammanslå säkerhetskonfiguration

Om du vill använda tjänsten dela/Sammanslå, måste du konfigurera säkerhet på rätt sätt. Tjänsten är en del av funktionen elastisk skalning av Microsoft Azure SQL Database. Mer information finns i [elastisk skala dela och slå samman Service-självstudie](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Konfigurera certifikat

Certifikat är konfigurerade på två sätt. 

1. [Konfigurera SSL-certifikat](#to-configure-the-ssl-certificate)
2. [Konfigurera klientcertifikat](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Hämta certifikat

Certifikat kan hämtas från offentliga certifikatutfärdare (CA) eller från den [Windows Certifikattjänsten](https://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Det här är de här metoderna att hämta certifikat.

Om dessa alternativ inte är tillgängliga kan du generera **självsignerade certifikat**.

## <a name="tools-to-generate-certificates"></a>Verktyg för att generera certifikat

* [makecert.exe](https://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](https://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Att köra verktyg

* Från en Kommandotolken för utvecklare för Visual Studios Se [Visual Studio-kommandotolk](https://msdn.microsoft.com/library/ms229859.aspx) 
  
    Om installerat kan du gå till:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* Hämta WDK från [Windows 8.1: Hämta paket och verktyg](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Konfigurera SSL-certifikat

Ett SSL-certifikat krävs för att kryptera kommunikationen och autentisera servern. Välj den lämpligaste av tre scenarierna nedan och kör alla steg:

### <a name="create-a-new-self-signed-certificate"></a>Skapa ett nytt självsignerat certifikat

1. [Skapa ett självsignerat certifikat](#create-a-self-signed-certificate)
2. [Skapa PFX-filen för självsignerat SSL-certifikat](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Ladda upp SSL-certifikat till Molntjänsten](#upload-ssl-certificate-to-cloud-service)
4. [Uppdatera SSL-certifikatet i Tjänstkonfigurationsfil](#update-ssl-certificate-in-service-configuration-file)
5. [Importera SSL-certifikatutfärdare](#import-ssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Du använder ett befintligt certifikat från arkivet
1. [Exportera SSL-certifikat från certifikat Store](#export-ssl-certificate-from-certificate-store)
2. [Ladda upp SSL-certifikat till Molntjänsten](#upload-ssl-certificate-to-cloud-service)
3. [Uppdatera SSL-certifikatet i Tjänstkonfigurationsfil](#update-ssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Du använder ett befintligt certifikat i en PFX-fil
1. [Ladda upp SSL-certifikat till Molntjänsten](#upload-ssl-certificate-to-cloud-service)
2. [Uppdatera SSL-certifikatet i Tjänstkonfigurationsfil](#update-ssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Konfigurera klientcertifikat
Klientcertifikat krävs för att autentisera begäranden till tjänsten. Välj den lämpligaste av tre scenarierna nedan och kör alla steg:

### <a name="turn-off-client-certificates"></a>Stäng av klientcertifikat
1. [Inaktivera klienten certifikatbaserad autentisering](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Utfärda nya självsignerade certifikat
1. [Skapa en självsignerade certifikatutfärdare](#create-a-self-signed-certification-authority)
2. [Ladda upp CA-certifikat till Molntjänsten](#upload-ca-certificate-to-cloud-service)
3. [Uppdatera CA-certifikat i Tjänstekonfigurationsfilen](#update-ca-certificate-in-service-configuration-file)
4. [Utfärda klientcertifikat](#issue-client-certificates)
5. [Skapa PFX-filer för klientcertifikat](#create-pfx-files-for-client-certificates)
6. [Importera klientcertifikatet](#import-client-certificate)
7. [Kopiera Certifikattumavtryck för klienten](#copy-client-certificate-thumbprints)
8. [Konfigurera tillåtna klienter i Tjänstekonfigurationsfilen](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Använda befintliga certifikat
1. [Hitta Certifikatutfärdarens offentliga nyckel](#find-ca-public-key)
2. [Ladda upp CA-certifikat till Molntjänsten](#upload-ca-certificate-to-cloud-service)
3. [Uppdatera CA-certifikat i Tjänstekonfigurationsfilen](#update-ca-certificate-in-service-configuration-file)
4. [Kopiera Certifikattumavtryck för klienten](#copy-client-certificate-thumbprints)
5. [Konfigurera tillåtna klienter i Tjänstekonfigurationsfilen](#configure-allowed-clients-in-the-service-configuration-file)
6. [Konfigurera klienten Återkallningskontrollen för lövcertifikatet](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Åtkomst till Tjänsteslutpunkter kan begränsas till specifika intervall med IP-adresser.

## <a name="to-configure-encryption-for-the-store"></a>Konfigurera kryptering för store
Det krävs ett certifikat för att kryptera autentiseringsuppgifterna som lagras i arkivet för metadata. Välj den lämpligaste av tre scenarierna nedan och kör alla steg:

### <a name="use-a-new-self-signed-certificate"></a>Använda ett nytt självsignerat certifikat
1. [Skapa ett självsignerat certifikat](#create-a-self-signed-certificate)
2. [Skapa PFX-filen för självsignerat certifikat för kryptering](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Ladda upp krypteringscertifikat till Molntjänsten](#upload-encryption-certificate-to-cloud-service)
4. [Uppdatera krypteringscertifikat i Tjänstkonfigurationsfil](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Använda ett befintligt certifikat från certifikatarkivet
1. [Exportera krypteringscertifikat från certifikatet Store](#export-encryption-certificate-from-certificate-store)
2. [Ladda upp krypteringscertifikat till Molntjänsten](#upload-encryption-certificate-to-cloud-service)
3. [Uppdatera krypteringscertifikat i Tjänstkonfigurationsfil](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Använda ett befintligt certifikat i en PFX-fil
1. [Ladda upp krypteringscertifikat till Molntjänsten](#upload-encryption-certificate-to-cloud-service)
2. [Uppdatera krypteringscertifikat i Tjänstkonfigurationsfil](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Standardkonfigurationen
Standardkonfigurationen nekar all åtkomst till HTTP-slutpunkten. Det här är den rekommenderade inställningen, eftersom begäranden till dessa slutpunkter kan utföra känslig information som Databasautentiseringsuppgifter.
Standardkonfigurationen kan all åtkomst till HTTPS-slutpunkten. Den här inställningen kan begränsas ytterligare.

### <a name="changing-the-configuration"></a>Ändra konfigurationen
Grupp av regler för åtkomstkontroll som gäller för och slutpunkten har konfigurerats i den  **\<EndpointAcls >** i avsnittet den **tjänstkonfigurationsfilen**.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

Regler i en grupp för kontroll av åtkomst har konfigurerats i en \<AccessControl name = ”” > avsnittet i tjänstekonfigurationsfilen. 

Formatet förklaras i nätverket åtkomstkontrollistor-dokumentationen.
Om du vill tillåta endast IP-adresser i intervallet 100.100.0.0 100.100.255.255 att komma åt HTTPS-slutpunkt, till exempel ut reglerna så här:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>DOS-skydd för tjänsten
Det finns två olika sätt som stöd för att identifiera och förhindra DOS-attacker:

* Begränsa antalet samtidiga begäranden per fjärrvärden (inaktiverat som standard)
* Begränsa åtkomst per fjärrvärden frekvensen (på som standard)

Dessa är baserade på de funktioner som beskrivs ytterligare i den dynamiska IP-säkerhet i IIS. När du ändrar den här konfigurationen varning för följande faktorer:

* Beteendet för proxyservrar och Network Address Translation enheter över fjärrvärden-information
* Varje begäran till alla resurser i webbrollen anses (till exempel inläsning av skript, bilder, osv)

## <a name="restricting-number-of-concurrent-accesses"></a>Att begränsa antalet samtidiga åtkomster
De inställningar som konfigurerar det här beteendet är:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Ändra DynamicIpRestrictionDenyByConcurrentRequests till true för att aktivera skyddet.

## <a name="restricting-rate-of-access"></a>Begränsa mängden åtkomst
De inställningar som konfigurerar det här beteendet är:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Konfigurera svar på en nekade begäran
Följande inställning konfigurerar svar på en nekade begäran:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

I dokumentationen för den dynamiska IP-säkerhet i IIS för andra värden som stöds.

## <a name="operations-for-configuring-service-certificates"></a>Åtgärder för att konfigurera service-certifikat
Det här avsnittet är endast för referens. Följ konfigurationsstegen i:

* Konfigurera SSL-certifikat
* Konfigurera klientcertifikat

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat
Kör:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha256 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Så här anpassar:

* -n med tjänstens Webbadress. Jokertecken (”CN = * .cloudapp .net”) och alternativa namn (”CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net”) som stöds.
* -e med certifikatets förfallodatum skapa ett starkt lösenord och ange den när du uppmanas till detta.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Skapa PFX-filen för självsignerat SSL-certifikat
Kör:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Ange lösenord och sedan exportera certifikat med dessa alternativ:

* Ja, exportera den privata nyckeln
* Exportera alla utökade egenskaper

## <a name="export-ssl-certificate-from-certificate-store"></a>Exportera SSL-certifikat från certifikatarkivet
* Hitta certifikat
* Klicka på Åtgärder -> alla uppgifter -> Exportera...
* Exportera certifikatet till en. PFX-fil med dessa alternativ:
  * Ja, exportera den privata nyckeln
  * Inkludera om möjligt alla certifikat i certifieringssökvägen * exportera alla utökade egenskaper

## <a name="upload-ssl-certificate-to-cloud-service"></a>Ladda upp SSL-certifikat till Molntjänsten
Ladda upp certifikatet med den befintliga eller skapas. PFX-fil med SSL-nyckelpar:

* Ange det lösenord som skyddar informationen om privata nyckeln

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Uppdatera SSL-certifikatet i tjänstkonfigurationsfil
Uppdatera värdet för tumavtrycket för följande inställning i tjänstekonfigurationsfilen med tumavtrycket för certifikatet som har laddats upp till Molntjänsten:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Importera SSL-certifikatutfärdare
Följ dessa steg i alla konto/datorn som ska kommunicera med tjänsten:

* Dubbelklicka på den. CER-filen i Windows Explorer
* I dialogrutan certifikat klickar du på Installera certifikat...
* Importera certifikatet till arkivet Betrodda rotcertifikatutfärdare

## <a name="turn-off-client-certificate-based-authentication"></a>Inaktivera klienten certifikatbaserad autentisering
Endast klienten certifikatbaserad autentisering stöds och inaktiverar den tillåter för offentlig åtkomst till tjänstslutpunkter, såvida inte andra mekanismer som är på plats (till exempel Microsoft Azure Virtual Network).

Ändra dessa inställningar till false i konfigurationsfilen för tjänsten att stänga av funktionen:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Kopiera sedan samma tumavtryck som SSL-certifikat i CA-certifikat inställningen:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Skapa en självsignerade certifikatutfärdare
Kör följande steg för att skapa ett självsignerat certifikat kan fungera som en certifikatutfärdare:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha256 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Du anpassar den

* -e med utgångsdatum för certifiering

## <a name="find-ca-public-key"></a>Hitta Certifikatutfärdarens offentliga nyckel
Alla klientcertifikat som måste ha utfärdats av en certifikatutfärdare som betrodd av tjänsten. Hitta den offentliga nyckeln till certifikatutfärdaren som utfärdade certifikat som ska användas för autentisering för att överföra den till Molntjänsten för klienten.

Om filen med den offentliga nyckeln inte är tillgänglig kan du exportera det från certifikatarkivet:

* Hitta certifikat
  * Sök efter ett klientcertifikat utfärdat av samma certifikatutfärdare
* Dubbelklicka på certifikatet.
* Välj fliken certifieringssökvägen i dialogrutan för certifikatet.
* Dubbelklicka på CA-post i sökvägen.
* Anteckna av egenskaper för certifikat.
* Stäng den **certifikat** dialogrutan.
* Hitta certifikat
  * Sök efter den Certifikatutfärdare som anges ovan.
* Klicka på Åtgärder -> alla uppgifter -> Exportera...
* Exportera certifikatet till en. CER med dessa alternativ:
  * **Nej, exportera inte den privata nyckeln**
  * Inkludera om möjligt alla certifikat i certifieringssökvägen.
  * Exportera alla utökade egenskaper.

## <a name="upload-ca-certificate-to-cloud-service"></a>Ladda upp CA-certifikat till Molntjänsten
Ladda upp certifikatet med den befintliga eller skapas. CER-fil med den offentliga nyckeln för Certifikatutfärdaren.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Uppdatera CA-certifikat i tjänstekonfigurationsfilen
Uppdatera värdet för tumavtrycket för följande inställning i tjänstekonfigurationsfilen med tumavtrycket för certifikatet som har laddats upp till Molntjänsten:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Uppdatera värdet för följande inställning med samma tumavtryck:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Utfärda klientcertifikat
Varje person som har behörighet att komma åt tjänsten ska ha ett klientcertifikat utfärdat för eget bruk och bör välja sina egna starkt lösenord för att skydda den privata nyckeln. 

Följande steg måste köras i samma dator där det självsignerade certifikatet för CA: N skapas och lagras:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha256 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Anpassa:

* -n med ID för att den klient som autentiseras med det här certifikatet
* -e med certifikatets förfallodatum
* MyID.pvk och MyID.cer med unikt filnamn för det här klientcertifikatet

Det här kommandot ombeds du att ange ett lösenord för att skapas och sedan användas en gång. Använd ett starkt lösenord.

## <a name="create-pfx-files-for-client-certificates"></a>Skapa PFX-filer för klienten certifikat
För varje genererad klientcertifikat, kör du:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Anpassa:

    MyID.pvk and MyID.cer with the filename for the client certificate

Ange lösenord och sedan exportera certifikat med dessa alternativ:

* Ja, exportera den privata nyckeln
* Exportera alla utökade egenskaper
* Den person som det här certifikatet utfärdas ska välja exportlösenord

## <a name="import-client-certificate"></a>Importera klientcertifikatet
Varje enskild person som ett certifikat har utfärdats ska importera nyckelpar i datorer som de ska använda för att kommunicera med tjänsten:

* Dubbelklicka på den. PFX-filen i Windows Explorer
* Importera certifikatet till personligt lagra med minst det här alternativet:
  * Inkludera alla utökade egenskaper markerat

## <a name="copy-client-certificate-thumbprints"></a>Kopiera certifikattumavtryck för klienten
Varje enskild person som ett certifikat har utfärdats måste följa de här stegen för att kunna hämta tumavtrycket för deras certifikat som ska läggas till tjänstkonfigurationsfilen:

* Kör certmgr.exe
* Välj fliken personlig
* Dubbelklicka på klientcertifikatet som ska användas för autentisering
* I dialogrutan certifikat som öppnas väljer du fliken information
* Se till att visa visar alla
* Välj fältet med namnet tumavtrycket i listan
* Kopiera värdet för tumavtrycket
  * Ta bort icke-synliga Unicode-tecken framför den första siffran
  * Ta bort alla blanksteg

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Konfigurera tillåtna klienter i tjänstekonfigurationsfilen
Uppdatera värdet för följande inställning i tjänstekonfigurationsfilen med en kommaavgränsad lista med tumavtrycken för de klientcertifikat som får åtkomst till tjänsten:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Konfigurera klienten Återkallningskontrollen för lövcertifikatet
Standardinställningen kontrollerar inte med certifikatutfärdaren för certifikatåterkallningsstatus för klienten. Aktivera kontrollerna om certifikatutfärdaren som utfärdade certifikaten för klienten har stöd för sådana kontroller genom att ändra följande inställning med någon av de värden som definieras i uppräkningen X509RevocationMode:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Skapa PFX-filen för självsignerat krypteringscertifikat
För ett certifikat för kryptering, kör du:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Anpassa:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Ange lösenord och sedan exportera certifikat med dessa alternativ:

* Ja, exportera den privata nyckeln
* Exportera alla utökade egenskaper
* Du behöver lösenordet när du laddar upp certifikatet till Molntjänsten.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exportera certifikatet från certifikatarkivet
* Hitta certifikat
* Klicka på Åtgärder -> alla uppgifter -> Exportera...
* Exportera certifikatet till en. PFX-fil med dessa alternativ: 
  * Ja, exportera den privata nyckeln
  * Inkludera om möjligt alla certifikat i certifieringssökvägen 
* Exportera alla utökade egenskaper

## <a name="upload-encryption-certificate-to-cloud-service"></a>Ladda upp krypteringscertifikat till Molntjänsten
Ladda upp certifikatet med den befintliga eller skapas. PFX-fil med nyckelparet kryptering:

* Ange det lösenord som skyddar informationen om privata nyckeln

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Uppdatera krypteringscertifikat i tjänstkonfigurationsfil
Uppdatera värdet för tumavtrycket för följande inställningar i tjänstekonfigurationsfilen med tumavtrycket för certifikatet som har laddats upp till Molntjänsten:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Vanliga certifikatåtgärder
* Konfigurera SSL-certifikat
* Konfigurera klientcertifikat

## <a name="find-certificate"></a>Hitta certifikat
Följ de här stegen:

1. Kör mmc.exe.
2. Arkiv -> Lägg till/ta bort snapin-modulen...
3. Välj **certifikat**.
4. Klicka på **Lägg till**.
5. Välj platsen för certifikatarkiv.
6. Klicka på **Slutför**.
7. Klicka på **OK**.
8. Expandera **certifikat**.
9. Expandera noden certificate store.
10. Expandera noden certifikat underordnade.
11. Välj ett certifikat i listan.

## <a name="export-certificate"></a>Exportera certifikatet
I den **certifikat exportguiden**:

1. Klicka på **Nästa**.
2. Välj **Ja**, sedan **exportera den privata nyckeln**.
3. Klicka på **Nästa**.
4. Välj önskad utdatafilen.
5. Kontrollera önskade alternativ.
6. Kontrollera **lösenord**.
7. Ange ett starkt lösenord och bekräfta att den.
8. Klicka på **Nästa**.
9. Skriv eller bläddra dig fram ett filnamn var du vill lagra certifikatet (använda en. PFX-tillägg).
10. Klicka på **Nästa**.
11. Klicka på **Slutför**.
12. Klicka på **OK**.

## <a name="import-certificate"></a>Importera certifikatet
I guiden Importera certifikat:

1. Välj lagringsplatsen.
   
   * Välj **aktuell användare** om bara processer som körs under aktuell användare ska få åtkomst till tjänsten
   * Välj **lokal dator** om andra processer i den här datorn ska få åtkomst till tjänsten
2. Klicka på **Nästa**.
3. Om importera från en fil, kontrollerar du sökvägen till filen.
4. Om du importerar en. PFX-fil:
   1. Ange det lösenord som skyddar den privata nyckeln
   2. Välj importalternativ
5. Välj ”plats”-certifikat i nedanstående Arkiv
6. Klicka på **Browse** (Bläddra).
7. Välj önskat Arkiv.
8. Klicka på **Slutför**.
   
   * Om arkivet för betrodda rotcertifikatutfärdare har valts klickar du på **Ja**.
9. Klicka på **OK** på alla fönster för dialogrutan.

## <a name="upload-certificate"></a>Överför certifikat
I [Azure Portal](https://portal.azure.com/)

1. Välj **molntjänster**.
2. Välj Molntjänsten.
3. Klicka på den översta menyn **certifikat**.
4. Klicka på det nedre fältet **överför**.
5. Markera den certifikatfil.
6. Om det är en. PFX och ange lösenordet för den privata nyckeln.
7. När klar kopiera tumavtrycket för certifikatet från den nya posten i listan.

## <a name="other-security-considerations"></a>Andra säkerhetsöverväganden
SSL-inställningarna som beskrivs i det här dokumentet kryptera kommunikation mellan tjänsten och dess klienter när HTTPS-slutpunkten används. Detta är viktigt eftersom autentiseringsuppgifter för åtkomst till databasen och eventuellt andra känslig information finns i kommunikationen. Observera dock att tjänsten kvarstår interna status, inklusive autentiseringsuppgifter, i dess interna tabeller i Microsoft Azure SQL-databasen som du har angett för lagring av metadata i Microsoft Azure-prenumerationen. Databasen har definierats som en del av följande inställning i din tjänstekonfigurationsfil (. CSCFG-fil): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

Autentiseringsuppgifterna som lagras i den här databasen krypteras. Men som bör du kontrollera att både webb- och worker-roller för dina tjänstdistributioner hålls uppdaterade och säkra som de båda ha åtkomst till metadata-databasen och certifikatet som används för kryptering och dekryptering av lagrade autentiseringsuppgifter. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

