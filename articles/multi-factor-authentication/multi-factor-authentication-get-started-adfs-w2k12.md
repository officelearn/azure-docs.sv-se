<properties 
    pageTitle="Skydda molnresurser och lokala resurser med hjälp av Azure MFA Server med Windows Server 2012 R2 AD FS" 
    description="Det här är sidan om Azure Multi-Factor Authentication som beskriver hur du kommer igång med Azure MFA på Windows Server 2012 R2." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtland"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>


# Skydda molnresurser och lokala resurser med Azure Multi-Factor Authentication Server med Windows Server 2012 R2 AD FS

Om din organisation är federerad med AD och du har resurser som är lokala eller i molnet som du vill skydda kan du göra det med hjälp av Azure Multi-Factor Authentication Server och konfigurera tjänsten för AD FS så att multifaktorautentisering används för värdefulla slutpunkter.

Den här dokumentationen gäller Azure Multi-Factor Authentication Server med AD FS i Windows Server 2012 R2.  Information om hur du använder Azure Multi-Factor Authentication med AD FS 2.0 finns i [Skydda molnresurser och lokala resurser med Azure Multi-Factor Authentication Server med AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## Skydda Windows Server 2012 R2 AD FS med Azure Multi-Factor Authentication Server

När du installerar Azure Multi-Factor Authentication-servern har du följande två alternativ:

- Installera Azure Multi-Factor Authentication Server lokalt på samma server som AD FS 
- Installera Azure Multi-Factor Authentication Adapter lokalt på AD FS-servern och installera MFA-servern på en annan dator

Innan du börjar bör du vara medveten om följande:

- Det är inte ett krav att Azure Multi-Factor Authentication-servern är installerad på AD FS-federationsservern men Multi-Factor Authentication Adapter för AD FS måste vara installerad på en Windows Server 2012 R2 som kör AD FS. Du kan installera servern på en annan dator, förutsatt att det är en version som stöds och installera AD FS-adaptern separat på AD FS-federationsservern. Anvisningar för hur du installerar adaptern separat finns i procedurerna nedan.
- När Multi-Factor Authentication-serverns AD FS-adapter utformades förutsåg man att AD FS kunde skicka namnet på den förlitande parten till adaptern som kunde användas som ett programnamn.  Det visade sig dock inte vara fallet.  Om du använder autentiseringsmetoder via SMS eller mobilappar innehåller strängarna som definierats i företagsinställningarna platshållaren ”<$application_name$>”.  Den här platshållaren ersätts inte när du använder AD FS-adaptern.  Därför rekommenderar vi att du tar bort platshållaren från relevanta strängar när du skyddar AD FS.

- Det inloggade kontot måste ha behörighet att skapa säkerhetsgrupper i Active Directory.

- Installationsguiden för Multi-Factor Authentication AD FS Adapter skapar en säkerhetsgrupp med namnet PhoneFactor Admins i Active Directory och lägger sedan till AD FS-tjänstkontot för federationstjänsten i den här gruppen. Vi rekommenderar att du kontrollerar att gruppen PhoneFactor Admins verkligen har skapats på domänkontrollanten och att AD FS-tjänstkontot är medlem i den här gruppen. Om det behövs lägger du till AD FS-tjänstkontot i gruppen PhoneFactor Admins på domänkontrollanten manuellt.
- Information om hur du installerar webbtjänst-SDK med användarportalen finns i [Distribuera användarportalen för Azure Multi-Factor Authentication Server.](multi-factor-authentication-get-started-portal.md)
  

### Så här installerar du Azure Multi-Factor Authentication Server lokalt på samma server som AD FS

1. Hämta och installera Azure Multi-Factor Authentication Server på AD FS-federationsservern. Information om hur du installerar Azure Multi-Factor Authentication-servern finns i [Komma igång med Azure Multi-Factor Authentication Server](multi-factor-authentication-get-started-server.md)
2. Välj AD FS-ikonen i Azure Multi-Factor Authentication-serverns användargränssnitt och välja alternativ för Tillåt användarregistrering och Tillåt användare att välja metod.
3. Välj eventuella ytterligare alternativ.
4. Klicka på Installera AD FS-adapter.
<center>![Molnet](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Om datorn är ansluten till en domän och Active Directory-konfigurationen för att skydda kommunikationen mellan AD FS-adaptern och Multi-Factor Authentication-tjänsten är ofullständig, visas Active Directory-steget.  Klicka på Nästa för att automatiskt slutföra den här konfigurationen eller markera kryssrutan Hoppa över automatisk konfigurering av Active Directory och konfigurera inställningarna manuellt. Avsluta med att klicka på Nästa.
6. Om datorn inte är ansluten till en domän och konfigurationen av Lokal grupp för att skydda kommunikationen mellan AD FS-adaptern och Multi-Factor Authentication-tjänsten är ofullständig, visas steget Lokal grupp.  Klicka på Nästa för att automatiskt slutföra den här konfigurationen eller markera kryssrutan Hoppa över automatisk konfigurering av lokal grupp och konfigurera inställningarna manuellt. Avsluta med att klicka på Nästa.
7. Nu öppnas installationsguiden. Klicka på Nästa om du vill tillåta att Azure Multi-Factor Authentication-servern skapar gruppen PhoneFactor Admins och lägger till AD FS-tjänstkontot i gruppen PhoneFactor Admins.
<center>![Molnet](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Klicka på Nästa i steget Installationsprogram.
9. Klicka på Nästa i installationsprogrammet för Multi-Factor Authentication AD FS Adapter.
10. Klicka på Stäng när installationen har slutförts.
11. Nu när adaptern har installerats måste den registreras med AD FS. Öppna Windows PowerShell och kör följande: C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 <center>![Molnet](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Nu måste vi redigera den globala autentiseringsprincipen i AD FS så att den använder den registrerade adaptern. Gå till noden Autentiseringsprinciper i AD FS-hanteringskonsolen och klicka på länken Redigera bredvid underavsnittet Globala inställningar under avsnittet Multi-Factor Authentication. I fönstret Redigera global autentiseringsprincip väljer du Multi-Factor Authentication som ytterligare en autentiseringsmetod och klickar på OK. Adaptern registreras som WindowsAzureMultiFactorAuthentication.  Du måste starta om AD FS-tjänsten för att registreringen ska börja gälla.

<center>![Molnet](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

Nu är Multi-Factor Authentication-servern konfigurerad som ytterligare en autentiseringsprovider för användning med AD FS.

## Så här installerar du AD FS-adaptern separat med webbtjänst-SDK
1. Installera webbtjänst-SDK på servern som kör Multi-Factor Authentication Server.
2. Kopiera filerna MultiFactorAuthenticationAdfsAdapterSetup64.msi, Register-MultiFactorAuthenticationAdfsAdapter.ps1, Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 och MultiFactorAuthenticationAdfsAdapter.config från katalogen \Program Files\Multi-Factor Authentication Server till den server som du ska installera AD FS-adaptern på.
3. Kör MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. Slutför installationen genom att klicka på Nästa i installationsprogrammet för Multi-Factor Authentication AD FS-adaptern.
5. Klicka på Stäng när installationen har slutförts.
6. Redigera filen MultiFactorAuthenticationAdfsAdapter.config genom att göra följande:

MultiFactorAuthenticationAdfsAdapter.config-steget| Delsteg
:------------- | :------------- |
Ange UseWebServiceSdk-noden till true.||
Ange WebServiceSdkUrl till URL:en för webbtjänst-SDK för Multi-Factor Authentication.||
Alternativ 1 – Konfigurera webbtjänst-SDK med ett användarnamn och lösenord.|<ol><li>Ange WebServiceSdkUsername till ett konto som är medlem i säkerhetsgruppen PhoneFactor Admins.  Använd formatet <domain>\<användarnamn>.<li>Ange WebServiceSdkPassword till lämpligt kontolösenord.</li></ol>
Alternativ 2 – Konfigurera webbtjänst-SDK med ett klientcertifikat.|<ol><li>Skaffa ett klientcertifikat från en certifikatutfärdare för servern som kör webbtjänst-SDK.  Mer information om hur du skaffar ett certifikat finns i [Skaffa ett klientcertifikat](https://technet.microsoft.com/library/cc770328(v=ws.10).aspx).</li><li>Importera klientcertifikatet till den lokala datorns personliga certifikatarkiv på servern som kör webbtjänst-SDK.  Obs! Kontrollera att certifikatutfärdarens offentliga certifikat finns i Betrodda rotcertifikat.</li><li>Exportera de offentliga och privata nycklarna för klientcertifikatet till en PFX-fil.</li><li>Exportera den offentliga nyckeln i Base64-format till en CER-fil.</li><li>I Serverhanteraren kontrollerar du att funktionen Web Server (IIS)\Web Server\Security\IIS Client Certificate Mapping Authentication är installerad.</li><li>Om den inte är installerad lägger du till funktionen genom att välja Lägg till roller och funktioner.</li><li>I IIS-hanteraren dubbelklickar du på Konfigurationsredigeraren i webbplatsen som innehåller den virtuella katalogen för webbtjänst-SDK.  Obs! Det är viktigt att du gör detta på webbplatsnivå och inte på nivån för den virtuella katalogen.</li><li>Gå till avsnittet system.webServer/security/authentication/iisClientCertificateMappingAuthentication.</li><li>Ange enabled till true.</li><li>Ange oneToOneCertificateMappingsEnabled till true.</li><li>Klicka på ”...”-knappen bredvid oneToOneMappings.</li><li>Klicka på länken Lägg till.</li><li>Öppna CER-filen i base-64-format som du exporterade tidigare.  Ta bort -----BEGIN CERTIFICATE-----, -----END CERTIFICATE----- och eventuella radbrytningar.  Kopiera den resulterande strängen.</li><li>Ange certificate till strängen som du kopierade i föregående steg.</li><li>Ange enabled till true.</li><li>Ange userName till ett konto som är medlem i säkerhetsgruppen PhoneFactor Admins.  Använd formatet <domain>\<användarnamn>.</li><li>Ange password till lämplig kontolösenord.</li><li>Stäng mängdredigeraren.</li><li>Klicka på länken Verkställ.</li><li>Gå till den virtuella katalogen för webbtjänst-SDK.</li><li>Dubbelklicka på Autentisering.</li><li>Kontrollera att ASP.NET-personifiering och Grundläggande autentisering är aktiverat och att alla andra objekt är inaktiverade.</li><li>Gå till den virtuella katalogen för webbtjänst-SDK igen.</li><li>Dubbelklicka på SSL-inställningar.</li><li>Ange Klientcertifikat till Acceptera och klicka på Verkställ.</li><li>Kopiera PFX-filen som du exporterade till servern som kör AD FS-adaptern.</li><li>Importera PFX-filen till den lokala datorns personliga certifikatarkiv.</li><li>Välj Hantera privata nycklar från snabbmenyn och bevilja läsåtkomst till kontot som Active Directory Federation Services-tjänsten är inloggad som.</li><li>Öppna klientcertifikatet och kopiera tumavtrycket från fliken Information.</li><li>I filen MultiFactorAuthenticationAdfsAdapter.config anger du WebServiceSdkCertificateThumbprint till strängen som du kopierade i föregående steg.</li></ol>
Redigera skriptet Register-MultiFactorAuthenticationAdfsAdapter.ps1 genom att lägga till -ConfigurationFilePath <path> i slutet av kommandot Register-AdfsAuthenticationProvider där <path> är den fullständiga sökvägen till filen MultiFactorAuthenticationAdfsAdapter.config.|


Registrera adaptern genom att köra skriptet \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 i PowerShell.  Adaptern registreras som WindowsAzureMultiFactorAuthentication.  Du måste starta om AD FS-tjänsten för att registreringen ska börja gälla. 




























 

 


 

 


 





 


 

























































































 


 

 






 


<!--HONumber=jun16_HO2-->


