---
title: Dela dokument säkerhetskonfiguration | Microsoft Docs
description: Ställ in x409 certifikat för kryptering med delade/kopplingstjänsten för elastisk skalbarhet.
metakeywords: Elastic Database certificates security
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 694c1755b5263d48fdfa5cad3c0b13e42bfb8522
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="split-merge-security-configuration"></a>Dela dokument säkerhetskonfiguration
Om du vill använda delade/kopplingstjänsten måste du konfigurera säkerhet på rätt sätt. Tjänsten är en del av funktionen elastisk skalbarhet i Microsoft Azure SQL Database. Mer information finns i [elastisk skala dela och sammanfoga Service kursen](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Konfigurera certifikat
Certifikat är konfigurerade på två sätt. 

1. [Så här konfigurerar du SSL-certifikat](#to-configure-the-ssl-certificate)
2. [Så här konfigurerar du klientcertifikat](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Hämta certifikat
Certifikat kan hämtas från offentlig certifikatutfärdare (CA) eller från den [Windows certifikattjänst](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Dessa är de här metoderna för att hämta certifikat.

Om dessa alternativ inte är tillgängliga kan du generera **självsignerade certifikat**.

## <a name="tools-to-generate-certificates"></a>Verktyg för att generera certifikat
* [makecert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Att köra verktyg
* Från en utvecklare kommandotolk för Visual Studios finns [Kommandotolken Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx) 
  
    Om installerat, gå till:
  
        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
* Hämta WDK från [Windows 8.1: Hämta paket och verktyg](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Så här konfigurerar du SSL-certifikat
Ett SSL-certifikat krävs för att kryptera kommunikationen och autentisera servern. Välj den lämpligaste av tre scenarier nedan och kör alla steg:

### <a name="create-a-new-self-signed-certificate"></a>Skapa ett nytt självsignerat certifikat
1. [Skapa ett självsignerat certifikat](#create-a-self-signed-certificate)
2. [Skapa PFX-fil för ett självsignerat SSL-certifikat](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Överför SSL-certifikat för Molntjänsten](#upload-ssl-certificate-to-cloud-service)
4. [Uppdatera SSL-certifikat i Tjänstkonfigurationsfilen](#update-ssl-certificate-in-service-configuration-file)
5. [Importera SSL-certifikatutfärdare](#import-ssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Att använda ett befintligt certifikat från certifikatarkivet
1. [Exportera SSL-certifikatet från certifikatarkivet](#export-ssl-certificate-from-certificate-store)
2. [Överför SSL-certifikat för Molntjänsten](#upload-ssl-certificate-to-cloud-service)
3. [Uppdatera SSL-certifikat i Tjänstkonfigurationsfilen](#update-ssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Att använda ett befintligt certifikat i en PFX-fil
1. [Överför SSL-certifikat för Molntjänsten](#upload-ssl-certificate-to-cloud-service)
2. [Uppdatera SSL-certifikat i Tjänstkonfigurationsfilen](#update-ssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Så här konfigurerar du klientcertifikat
Klientcertifikat krävs för att autentisera förfrågningar till tjänsten. Välj den lämpligaste av tre scenarier nedan och kör alla steg:

### <a name="turn-off-client-certificates"></a>Stäng av klientcertifikat
1. [Inaktivera certifikatbaserad klientautentisering](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Utfärda nya självsignerade certifikat
1. [Skapa en självsignerat certifikatutfärdare](#create-a-self-signed-certification-authority)
2. [Ladda upp CA-certifikat för Molntjänsten](#upload-ca-certificate-to-cloud-service)
3. [Uppdatera CA-certifikat i Tjänstkonfigurationsfilen](#update-ca-certificate-in-service-configuration-file)
4. [Utfärda klientcertifikat](#issue-client-certificates)
5. [Skapa PFX-filer för klientcertifikat](#create-pfx-files-for-client-certificates)
6. [Importera klientcertifikatet](#Import-Client-Certificate)
7. [Kopiera Certifikattumavtryck för klienten](#copy-client-certificate-thumbprints)
8. [Konfigurera tillåtna klienter i Tjänstkonfigurationsfilen](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Använd befintlig klientcertifikat
1. [Hitta Certifikatutfärdarens offentliga nyckel](#find-ca-public-key)
2. [Ladda upp CA-certifikat för Molntjänsten](#Upload-CA-certificate-to-cloud-service)
3. [Uppdatera CA-certifikat i Tjänstkonfigurationsfilen](#Update-CA-Certificate-in-Service-Configuration-File)
4. [Kopiera Certifikattumavtryck för klienten](#Copy-Client-Certificate-Thumbprints)
5. [Konfigurera tillåtna klienter i Tjänstkonfigurationsfilen](#configure-allowed-clients-in-the-service-configuration-file)
6. [Konfigurera klienten Återkallningskontrollen för lövcertifikatet](#Configure-Client-Certificate-Revocation-Check)

## <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Åtkomst till Tjänsteslutpunkter kan begränsas till specifika intervall med IP-adresser.

## <a name="to-configure-encryption-for-the-store"></a>Konfigurera kryptering för store
Ett certifikat krävs för att kryptera autentiseringsuppgifterna som lagras i arkivet för metadata. Välj den lämpligaste av tre scenarier nedan och kör alla steg:

### <a name="use-a-new-self-signed-certificate"></a>Använda ett nytt självsignerat certifikat
1. [Skapa ett självsignerat certifikat](#create-a-self-signed-certificate)
2. [Skapa PFX-fil för ett självsignerat certifikat för kryptering](#create-pfx-file-for-self-signed-ssl-certificate)
3. [Överför certifikat för kryptering för Molntjänsten](#upload-encryption-certificate-to-cloud-service)
4. [Uppdatera certifikat för kryptering i konfigurationsfilen för tjänsten](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Använda ett befintligt certifikat från certifikatarkivet
1. [Exportera krypteringscertifikat från certifikatarkivet](#export-encryption-certificate-from-certificate-store)
2. [Överför certifikat för kryptering för Molntjänsten](#upload-encryption-certificate-to-cloud-service)
3. [Uppdatera certifikat för kryptering i konfigurationsfilen för tjänsten](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Använd ett befintligt certifikat i en PFX-fil
1. [Överför certifikat för kryptering för Molntjänsten](#upload-encryption-certificate-to-cloud-service)
2. [Uppdatera certifikat för kryptering i konfigurationsfilen för tjänsten](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Standardkonfigurationen
Standardkonfigurationen nekar åtkomst till HTTP-slutpunkten. Det här är den rekommenderade inställningen, eftersom förfrågningar till dessa slutpunkter kan utföra känslig information, till exempel autentiseringsuppgifter på databasen.
Standardkonfigurationen kan all åtkomst till HTTPS-slutpunkten. Den här inställningen kan begränsas ytterligare.

### <a name="changing-the-configuration"></a>Ändringar av konfigurationen
Grupp av regler för åtkomstkontroll som gäller för och slutpunkten har konfigurerats i den **<EndpointAcls>** under den **tjänstkonfigurationsfilen**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

Regler i en åtkomstgrupp för kontrollen har konfigurerats i en <AccessControl name=""> i tjänstkonfigurationsfil. 

Formatet förklaras i dokumentationen för nätverket ACL-listor.
Om du vill tillåta endast IP-adresser i intervallet 100.100.0.0 100.100.255.255 åtkomst till HTTPS-slutpunkt, till exempel ut regler så här:

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name="denial-of-service-prevention"></a>För tjänsten förebyggande
Det finns två olika metoder som stöds för att upptäcka och förhindra Denial of Service-attacker:

* Begränsa antalet samtidiga förfrågningar per fjärrvärden (inaktiverat som standard)
* Begränsa antalet åtkomst per fjärrvärden (på som standard)

De bygger på funktionerna för ytterligare dokumenteras i den dynamiska IP-säkerhet i IIS. När du ändrar den här konfigurationen Tänk på följande faktorer:

* Beteendet för proxyservrar och Network Address Translation enheter över fjärrvärden-information
* Varje begäran till alla resurser i webbrollen anses (t.ex. läser in skript, bilder, osv)

## <a name="restricting-number-of-concurrent-accesses"></a>Begränsa antalet samtidiga användningar
De inställningar som konfigurerar det här beteendet är:

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Ändra DynamicIpRestrictionDenyByConcurrentRequests till true för att aktivera skyddet.

## <a name="restricting-rate-of-access"></a>Begränsa antalet åtkomst
De inställningar som konfigurerar det här beteendet är:

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## <a name="configuring-the-response-to-a-denied-request"></a>Konfigurera svar på en nekade begäran
Följande inställning konfigurerar svar på en nekade begäran:

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Finns i dokumentationen för den dynamiska IP-säkerhet i IIS för andra värden som stöds.

## <a name="operations-for-configuring-service-certificates"></a>Åtgärder för att konfigurera tjänstcertifikat
Det här avsnittet är endast för referens. Följ konfigurationsstegen i:

* Konfigurera SSL-certifikatet
* Konfigurera klientcertifikat

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat
Kör:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Så här anpassar:

* -n med tjänstens Webbadress. Jokertecken (”CN = * .cloudapp .net”) och alternativa namn (”CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net”) stöds.
* e - med certifikatets förfallodatum skapa ett starkt lösenord och ange den när du tillfrågas.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Skapa PFX-filen för självsignerade SSL-certifikatet
Kör:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Ange lösenordet och sedan exportera certifikat med dessa alternativ:

* Ja, exportera den privata nyckeln
* Exportera alla utökade egenskaper

## <a name="export-ssl-certificate-from-certificate-store"></a>Exportera SSL-certifikatet från certifikatarkivet
* Hitta certifikat
* Klicka på Åtgärder -> alla uppgifter -> Exportera...
* Exportera certifikatet till en. PFX-filen med dessa alternativ:
  * Ja, exportera den privata nyckeln
  * Inkludera om möjligt alla certifikat i certifieringssökvägen * exportera alla utökade egenskaper

## <a name="upload-ssl-certificate-to-cloud-service"></a>Överför SSL-certifikatet till Molntjänsten
Överför certifikat med den befintliga eller skapas. PFX-filen med SSL-nyckelpar:

* Ange det lösenord som skyddar informationen om privat nyckel

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Uppdatera SSL-certifikat i tjänstkonfigurationsfilen
Uppdatera värdet tumavtrycket för följande inställning i tjänstkonfigurationsfilen med tumavtrycket för certifikatet som har överförts till Molntjänsten:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Importera SSL-certifikatutfärdare
Följ dessa steg på alla konto/datorn som kommunicerar med tjänsten:

* Dubbelklicka på den. CER-filen i Utforskaren i Windows
* I dialogrutan certifikat klickar du på Installera certifikat...
* Importera certifikatet till arkivet Betrodda rotcertifikatutfärdare

## <a name="turn-off-client-certificate-based-authentication"></a>Inaktivera certifikatbaserad klientautentisering
Endast certifikatbaserad klientautentisering stöds och inaktiverar den tillåter för offentlig åtkomst till Tjänsteslutpunkter, såvida inte andra mekanismer finns på plats (t.ex. Microsoft Azure Virtual Network).

Ändra dessa inställningar till false i konfigurationsfilen för tjänsten att stänga av funktionen:

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

Kopiera sedan samma tumavtryck som SSL-certifikat i inställningen för CA-certifikat:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-a-self-signed-certification-authority"></a>Skapa en självsignerat certifikatutfärdare
Utför följande steg för att skapa ett självsignerat certifikat ska fungera som en certifikatutfärdare:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Anpassa den

* -e med utgångsdatum för certifikatutfärdare

## <a name="find-ca-public-key"></a>Hitta Certifikatutfärdarens offentliga nyckel
Alla certifikat måste har utfärdats av en certifikatutfärdare som betrodd av tjänsten. Hitta den offentliga nyckeln till certifikatutfärdaren som utfärdade certifikat som ska användas för autentisering för att överföra den till Molntjänsten för klienten.

Om filen med den offentliga nyckeln inte är tillgänglig kan du exportera det från certifikatarkivet:

* Hitta certifikat
  * Sök efter ett klientcertifikat utfärdat av samma certifikatutfärdare
* Dubbelklicka på certifikatet.
* Välj fliken certifieringssökvägen i dialogrutan certifikat.
* Dubbelklicka på posten Certifikatutfärdare i sökvägen.
* Anteckna för certifikategenskaperna.
* Stäng den **certifikat** dialogrutan.
* Hitta certifikat
  * Sök efter den Certifikatutfärdare som nämns ovan.
* Klicka på Åtgärder -> alla uppgifter -> Exportera...
* Exportera certifikatet till en. CER med dessa alternativ:
  * **Nej, exportera inte den privata nyckeln**
  * Inkludera om möjligt alla certifikat i certifieringssökvägen.
  * Exportera alla utökade egenskaper.

## <a name="upload-ca-certificate-to-cloud-service"></a>Ladda upp CA-certifikat till molntjänst
Överför certifikat med den befintliga eller skapas. CER-filen med den offentliga nyckeln för Certifikatutfärdaren.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Uppdatera CA-certifikat i tjänstkonfigurationsfilen
Uppdatera värdet tumavtrycket för följande inställning i tjänstkonfigurationsfilen med tumavtrycket för certifikatet som har överförts till Molntjänsten:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Uppdatera värdet för följande inställning med samma tumavtryck:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certificates"></a>Utfärda klientcertifikat
Varje enskild person behörighet att komma åt tjänsten ska ha ett klientcertifikat utfärdat för his/hers exklusiv använder och bör välja his/hers äger starkt lösenord för att skydda den privata nyckeln. 

Följande steg måste utföras i samma dator där det självsignerade certifikatet för CA: N skapas och lagras:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Anpassa:

* -n med ett ID för att den klient som autentiseras med det här certifikatet
* e - med certifikatets förfallodatum
* MyID.pvk och MyID.cer med unika filnamn för det här klientcertifikatet

Det här kommandot ombeds du att ange ett lösenord för att skapa och sedan används en gång. Använd ett starkt lösenord.

## <a name="create-pfx-files-for-client-certificates"></a>Skapa PFX-filer för klienten certifikat
För varje genererade klientcertifikat, kör du:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Anpassa:

    MyID.pvk and MyID.cer with the filename for the client certificate

Ange lösenordet och sedan exportera certifikat med dessa alternativ:

* Ja, exportera den privata nyckeln
* Exportera alla utökade egenskaper
* Personen som det här certifikatet utfärdas bör välja export-lösenord

## <a name="import-client-certificate"></a>Importera klientcertifikatet
Varje enskild person som ett certifikat har utfärdats ska importera nyckelpar i datorer som han eller hon ska användas för kommunikation med tjänsten:

* Dubbelklicka på den. PFX-filen i Utforskaren i Windows
* Importera till personligt certifikatarkivet med minst det här alternativet:
  * Inkludera alla utökade egenskaper markerat

## <a name="copy-client-certificate-thumbprints"></a>Kopiera certifikattumavtryck för klienten
Varje enskild person som ett certifikat har utfärdats måste följa stegen för att kunna hämta tumavtrycket för his/hers certifikat som ska läggas till tjänstkonfigurationsfilen:

* Kör certmgr.exe
* Välj fliken personlig
* Dubbelklicka på klientcertifikatet som ska användas för autentisering
* Välj fliken information i dialogrutan som öppnas för certifikat
* Se till att visa visas alla
* Markera det fält som heter tumavtrycket i listan
* Kopiera värdet för tumavtrycket ** ta bort icke-synliga Unicode-tecken framför den första siffran ** ta bort alla blanksteg

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Konfigurera tillåtna klienter i tjänstkonfigurationsfilen
Uppdatera värdet för följande inställning i tjänstkonfigurationsfilen med en kommaavgränsad lista över tumavtryck klientcertifikat som har åtkomst till tjänsten:

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-certificate-revocation-check"></a>Konfigurera klienten Återkallningskontrollen för lövcertifikatet
Standardinställningen kontrollerar inte med certifikatutfärdaren för certifikatåterkallningsstatus för klienten. Om du vill aktivera kontrollerna om certifikatutfärdaren som utfärdade certifikaten för klienten har stöd för dessa kontroller, ändrar du följande inställning med något av de värden som definieras i uppräkningen X509RevocationMode:

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Skapa PFX-filen för självsignerade krypteringscertifikat
För ett certifikat för kryptering, kör du:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Anpassa:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Ange lösenordet och sedan exportera certifikat med dessa alternativ:

* Ja, exportera den privata nyckeln
* Exportera alla utökade egenskaper
* Du behöver lösenordet när du laddar upp certifikatet till Molntjänsten.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exportera krypteringscertifikat från certifikatarkivet
* Hitta certifikat
* Klicka på Åtgärder -> alla uppgifter -> Exportera...
* Exportera certifikatet till en. PFX-filen med dessa alternativ: 
  * Ja, exportera den privata nyckeln
  * Inkludera om möjligt alla certifikat i certifieringssökvägen 
* Exportera alla utökade egenskaper

## <a name="upload-encryption-certificate-to-cloud-service"></a>Överför krypteringscertifikat till Molntjänsten
Överför certifikat med den befintliga eller skapas. PFX-filen med nyckelparet kryptering:

* Ange det lösenord som skyddar informationen om privat nyckel

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Uppdatera certifikat för kryptering i konfigurationsfilen för tjänsten
Uppdatera tumavtrycksvärde av följande inställningar i tjänstkonfigurationsfilen med tumavtrycket för certifikatet som har överförts till Molntjänsten:

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="common-certificate-operations"></a>Vanliga certifikatåtgärder
* Konfigurera SSL-certifikatet
* Konfigurera klientcertifikat

## <a name="find-certificate"></a>Hitta certifikat
Följ de här stegen:

1. Kör mmc.exe.
2. Arkiv -> Lägg till/ta bort snapin-modulen...
3. Välj **certifikat**.
4. Klicka på **Lägg till**.
5. Välj lagringsplatsen för certifikatet.
6. Klicka på **Slutför**.
7. Klicka på **OK**.
8. Expandera **certifikat**.
9. Expandera noden certificate store.
10. Expandera noden certifikat.
11. Välj ett certifikat i listan.

## <a name="export-certificate"></a>Exportera certifikat
I den **guiden Exportera certifikat**:

1. Klicka på **Nästa**.
2. Välj **Ja**, sedan **exportera den privata nyckeln**.
3. Klicka på **Nästa**.
4. Välj önskad utdatafilen.
5. Kontrollera önskade alternativ.
6. Kontrollera **lösenord**.
7. Ange ett starkt lösenord och bekräfta.
8. Klicka på **Nästa**.
9. Skriv eller bläddra fram ett filnamn där certifikatet ska lagras (använder en. Filnamnstillägget PFX).
10. Klicka på **Nästa**.
11. Klicka på **Slutför**.
12. Klicka på **OK**.

## <a name="import-certificate"></a>Importera certifikat
I guiden Importera certifikat:

1. Välj lagringsplatsen.
   
   * Välj **aktuell användare** om endast processer som körs under aktuell användare kommer åt tjänsten
   * Välj **lokal dator** om andra processer i den här datorn ansluter till tjänsten
2. Klicka på **Nästa**.
3. Om du importerar från en fil, kontrollera sökvägen till filen.
4. Om du importerar en. PFX-filen:
   1. Ange det lösenord som skyddar den privata nyckeln
   2. Välj alternativ för import
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
4. Klicka på raden längst **överför**.
5. Välj certifikatfilen.
6. Om det är en. PFX-fil, ange lösenordet för den privata nyckeln.
7. När klar, kan du kopiera tumavtrycket för certifikatet från den nya posten i listan.

## <a name="other-security-considerations"></a>Andra säkerhetsaspekter
SSL-inställningarna som beskrivs i det här dokumentet kryptera kommunikationen mellan service och dess klienter när du använder HTTPS-slutpunkten. Detta är viktigt eftersom autentiseringsuppgifter för åtkomst till databasen och eventuellt andra känslig information finns i kommunikationen. Observera dock att tjänsten kvarstår interna status, inklusive inloggningsuppgifter, i dess interna tabeller i Microsoft Azure SQL-databasen som du har angett för lagring av metadata i din Microsoft Azure-prenumeration. Databasen har definierats som en del av följande inställning i din tjänstekonfigurationsfil (. CSCFG-fil): 

    <Setting name="ElasticScaleMetadata" value="Server=…" />

Autentiseringsuppgifterna som lagras i den här databasen krypteras. Dock som bästa praxis bör du kontrollera att både webb- och arbetsroller roller för dina tjänstdistributioner hålls uppdaterade och säkra som de båda har åtkomst till metadata-databasen och certifikatet som används för kryptering och dekryptering av lagrade autentiseringsuppgifter. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

