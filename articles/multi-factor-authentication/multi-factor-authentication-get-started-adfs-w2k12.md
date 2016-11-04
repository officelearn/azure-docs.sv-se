---
title: MFA Server med Windows Server 2012 R2 AD FS | Microsoft Docs
description: Den här artikeln beskriver hur du kommer igång med Azure Multi-Factor Authentication och AD FS i Windows Server 2012 R2.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtland

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/22/2016
ms.author: kgremban

---
# Skydda molnresurser och lokala resurser med Azure Multi-Factor Authentication Server med AD FS i Windows Server 2012 R2
Om din organisation använder Active Directory Federation Services (AD FS) och du vill skydda molnresurser eller lokala resurser kan du distribuera och konfigurera Azure Multi-Factor Authentication Server att fungera med AD FS. Den här konfigurationen utlöser multifaktorautentisering för slutpunkter med högt värde.

I den här artikeln diskuterar vi hur du använder Azure Multi-Factor Authentication Server med AD FS i Windows Server 2012 R2. Mer information finns i [Skydda molnresurser och lokala resurser med hjälp av Azure Multi-Factor Authentication Server med AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## Skydda Windows Server 2012 R2 AD FS med Azure Multi-Factor Authentication Server
När du installerar Azure Multi-Factor Authentication Server kan du välja mellan följande alternativ:

* Installera Azure Multi-Factor Authentication Server lokalt på samma server som AD FS.
* Installera Azure Multi-Factor Authentication-adaptern lokalt på AD FS-servern och installera sedan Multi-Factor Authentication-servern på en annan dator.

Innan du börjar bör du vara medveten om följande:

* Du behöver inte installera Azure Multi-Factor Authentication Server på AD FS-servern. Dock måste du installera Multi-Factor Authentication-adaptern för AD FS på en Windows Server 2012 R2 som kör AD FS. Du kan installera servern på en annan dator, förutsatt att det är en version som stöds och installera AD FS-adaptern separat på AD FS-federationsservern. Se följande information om hur du installerar adaptern separat.
* När AD FS-adaptern för Multi-Factor Authentication Server utformades förutsåg man att AD FS kunde skicka namnet på den förlitande parten till adaptern som kunde användas som ett programnamn. Det visade sig dock inte vara fallet. Om din organisation använder autentiseringsmetoder med SMS eller mobilappar innehåller strängarna som definierats i företagsinställningarna platshållaren <$*application_name*$>. Den här platshållaren ersätts inte automatiskt när du använder AD FS-adaptern. Vi rekommenderar att du tar bort platshållaren från relevanta strängar när du skyddar AD FS.
* Det konto som du använder för att logga in måste ha användarbehörighet att skapa säkerhetsgrupper i Active Directory-tjänsten.
* Installationsguiden för Multi-Factor Authentication AD FS-adaptern skapar en säkerhetsgrupp med namnet PhoneFactor Admins i din instans av Active Directory och lägger sedan till AD FS-tjänstkontot för din federationstjänst till den här gruppen. Vi rekommenderar att du kontrollerar att gruppen PhoneFactor Admins verkligen har skapats och att AD FS-tjänstkontot är medlem i den här gruppen på domänkontrollanten. Om det behövs lägger du till AD FS-tjänstkontot i gruppen PhoneFactor Admins på domänkontrollanten manuellt.
* Information om hur du installerar webbtjänst-SDK med användarportalen finns i [Distribuera användarportalen för Azure Multi-Factor Authentication Server.](multi-factor-authentication-get-started-portal.md)

### Installera Azure Multi-Factor Authentication Server lokalt på AD FS-servern
1. Hämta och installera Azure Multi-Factor Authentication Server på AD FS-federationsservern. Mer information om installationen finns i [Komma igång med Azure Multi-Factor Authentication Server](multi-factor-authentication-get-started-server.md).
2. I Azure Multi-Factor Authentication Server-hanteringskonsolen klickar du på **AD FS**-ikonen och väljer sedan alternativen **Tillåt användarregistrering** och **Tillåt användare att välja metod**.
3. Välj ytterligare alternativ som du vill ange för din organisation.
4. Klicka på **Installera AD FS-adapter**.
   <center>![Molnet](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Om datorn är ansluten till en domän och Active Directory-konfigurationen för att skydda kommunikationen mellan AD FS-adaptern och Multi-Factor Authentication-tjänsten är ofullständig, visas **Active Directory**-fönstret. Klicka på **Nästa** för att slutföra konfigurationen automatiskt, eller markera kryssrutan **Hoppa över automatisk konfigurering av Active Directory och konfigurera inställningarna manuellt** och klicka på **Nästa**.
6. Om datorn inte är ansluten till en domän och konfigurationen av Lokal grupp för att skydda kommunikationen mellan AD FS-adaptern och Multi-Factor Authentication-tjänsten är ofullständig, visas fönstret **Lokal grupp**. Klicka på **Nästa** för att slutföra konfigurationen automatiskt, eller markera kryssrutan **Hoppa över automatisk konfigurering av lokal grupp och konfigurera inställningarna manuellt** och klicka på **Nästa**.
7. Klicka på **Nästa** i installationsguiden. Azure Multi-Factor Authentication Server skapar gruppen PhoneFactor Admins och lägger till AD FS-tjänstkontot i PhoneFactor Admins-gruppen.
   <center>![Molnet](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Klicka på **Nästa** på sidan **Installationsprogram**.
9. Klicka på **Nästa** i installationsprogrammet för Multi-Factor Authentication AD FS-adaptern.
10. Klicka på **Stäng** när installationen är klar.
11. När adaptern har installerats måste du registrera den med AD FS. Öppna Windows PowerShell och kör följande kommando:<br>
    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
    <center>![Molnet](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Redigera den globala autentiseringsprincipen i AD FS att använda den nyligen registrerade adaptern. I AD FS-hanteringskonsolen går du till noden **Autentiseringsprinciper**. I avsnittet **Multi-Factor Authentication** klickar du på länken **Redigera** bredvid avsnittet **Globala inställningar**. I fönstret **Redigera global autentiseringsprincip** väljer du **Multi-Factor Authentication** som ytterligare en autentiseringsmetod och klickar på **OK**. Adaptern registreras som WindowsAzureMultiFactorAuthentication. Du måste starta om AD FS-tjänsten för att registreringen ska börja gälla.

<center>![Molnet](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

Nu är Multi-Factor Authentication-servern konfigurerad som ytterligare en autentiseringsprovider för användning med AD FS.

## Installera en fristående instans av AD FS-adaptern med webbtjänst-SDK
1. Installera webbtjänst-SDK på servern som kör Multi-Factor Authentication Server.
2. Kopiera följande filer från katalogen \Program\Multi-Factor Authentication Server till den server där du planerar att installera AD FS-adaptern:
   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config
3. Kör installationsfilen MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. Slutför installationen genom att klicka på **Nästa** i installationsprogrammet för Multi-Factor Authentication AD FS-adaptern.
5. Klicka på **Stäng** när installationen är klar.

## Redigera filen MultiFactorAuthenticationAdfsAdapter.config
Redigera filen MultiFactorAuthenticationAdfsAdapter.config genom att följa stegen nedan:

1. Ange noden **UseWebServiceSdk** till **sant**.  
2. Ange värdet för **WebServiceSdkUrl** till URL:en för webbtjänst-SDK för Multi-Factor Authentication. Exempel:  **https://contoso.com/&lt;certifikatnamn&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx** där ”certifikatnamn” är namnet på certifikatet.  
3. Redigera skriptet Register-MultiFactorAuthenticationAdfsAdapter.ps1 genom att lägga till *-ConfigurationFilePath &lt;sökväg&gt;* i slutet av kommandot `Register-AdfsAuthenticationProvider`, där *&lt;sökväg&gt;* är den fullständiga sökvägen till filen MultiFactorAuthenticationAdfsAdapter.config.

### Konfigurera webbtjänst-SDK med ett användarnamn och lösenord
Det finns två alternativ för att konfigurera webbtjänst-SDK. Det första alternativet är att använda ett användarnamn och lösenord, det andra är att använda ett klientcertifikat. Följ dessa steg för det första alternativet eller hoppa framåt till det andra.  

1. Ange värdet för **WebServiceSdkUsername** till ett konto som är medlem i säkerhetsgruppen PhoneFactor Admins. Använd formatet &lt;domän&gt;&#92;&lt;användarnamn&gt;.  
2. Ange värdet för **WebServiceSdkPassword** till lämpligt kontolösenord.

### Konfigurera webbtjänst-SDK med ett klientcertifikat
Följ dessa steg om du vill konfigurera webbtjänst-SDK med ett klientcertifikat om du inte vill använda ett användarnamn och lösenord.

1. Skaffa ett klientcertifikat från en certifikatutfärdare för servern som kör webbtjänst-SDK. Lär dig hur du [skaffar klientcertifikat](https://technet.microsoft.com/library/cc770328.aspx).  
2. Importera klientcertifikatet till den lokala datorns personliga certifikatarkiv på servern som kör webbtjänst-SDK. Obs! Kontrollera att certifikatutfärdarens offentliga certifikat finns i certifikatarkivet för betrodda rotcertifikat.  
3. Exportera de offentliga och privata nycklarna för klientcertifikatet till en PFX-fil.  
4. Exportera den offentliga nyckeln i Base64-format till en CER-fil.  
5. I Serverhanteraren kontrollerar du att funktionen Web Server (IIS)\Web Server\Security\IIS Client Certificate Mapping Authentication är installerad. Om den inte är installerad väljer du **Lägg till roller och funktioner** för att lägga till funktionen.  
6. I IIS-hanteraren dubbelklickar du på **Konfigurationsredigeraren** i webbplatsen som innehåller den virtuella katalogen för webbtjänst-SDK. Obs! Det är viktigt att du gör detta på webbplatsnivå och inte på nivån för den virtuella katalogen.  
7. Gå till avsnittet **system.webServer/security/authentication/iisClientCertificateMappingAuthentication**.  
8. Ange **enabled** till **true**.  
9. Ange **oneToOneCertificateMappingsEnabled** till **true**.  
10. Klicka på knappen **...** bredvid **oneToOneMappings** och klicka sedan på länken **Lägg till**.  
11. Öppna CER-filen med Base64-format som du exporterade tidigare. Ta bort *-----BEGIN CERTIFICATE-----*, *-----END CERTIFICATE-----* och eventuella radbrytningar. Kopiera den resulterande strängen.  
12. Ange **certificate** till strängen som du kopierade i föregående steg.  
13. Ange **enabled** till **true**.  
14. Ange **userName** till ett konto som är medlem i säkerhetsgruppen PhoneFactor Admins. Använd formatet &lt;domän&gt;&#92;&lt;användarnamn&gt;.  
15. Ange lösenordet till lämpligt kontolösenord och stäng Konfigurationsredigeraren.  
16. Klicka på länken **Använd**.  
17. I den virtuella katalogen för webbtjänst-SDK dubbelklickar du på **Autentisering**.  
18. Kontrollera att **ASP.NET-personifiering** och **Grundläggande autentisering** är **aktiverat** och att alla andra objekt är **inaktiverade**.  
19. Dubbelklicka på **SSL-inställningar** i den virtuella katalogen för webbtjänst-SDK.  
20. Ange **Klientcertifikat** till **Acceptera** och klicka på **Använd**.  
21. Kopiera PFX-filen som du exporterade tidigare till servern som kör AD FS-adaptern.  
22. Importera PFX-filen till den lokala datorns personliga certifikatarkiv.  
23. Högerklicka och välj **Hantera privata nycklar** och ge sedan läsbehörighet till kontot som du använde för att logga in till AD FS-tjänsten.  
24. Öppna klientcertifikatet och kopiera tumavtrycket från fliken **Information**.  
25. I filen MultiFactorAuthenticationAdfsAdapter.config anger du **WebServiceSdkCertificateThumbprint** till strängen som du kopierade i föregående steg.  

Registrera adaptern genom att köra skriptet \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 i PowerShell. Adaptern registreras som WindowsAzureMultiFactorAuthentication. Du måste starta om AD FS-tjänsten för att registreringen ska börja gälla.

## Relaterade ämnen
Hjälp med felsökning finns i [Azure Multi-Factor Authentication FAQs (Vanliga frågor och svar om Azure Multi-Factor Authentication)](multi-factor-authentication-faq.md)

<!--HONumber=Sep16_HO4-->


