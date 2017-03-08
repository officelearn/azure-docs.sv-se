---
title: MFA Server med Windows Server 2012 R2 AD FS | Microsoft Docs
description: "Den här artikeln beskriver hur du kommer igång med Azure Multi-Factor Authentication och AD FS i Windows Server 2012 R2."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 57208068-1e55-45b6-840f-fdcd13723074
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/19/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: e4ef137656c12cf6495a00450eed308ac6a8a872
ms.openlocfilehash: 7fd5c4edadc6d9cc070dff937a963f9a83ec66c2
ms.lasthandoff: 02/28/2017

---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-with-ad-fs-in-windows-server-2012-r2"></a>Konfigurera Azure Multi-Factor Authentication Server så att den fungerar med AD FS i Windows Server 2012 R2
Om du använder Active Directory Federation Services (AD FS) och vill skydda molnresurser eller lokala resurser kan du konfigurera Azure Multi-Factor Authentication Server att fungera med AD FS. Den här konfigurationen utlöser tvåstegsverifiering för slutpunkter med högt värde.

I den här artikeln diskuterar vi hur du använder Azure Multi-Factor Authentication Server med AD FS i Windows Server 2012 R2. Mer information finns i [Skydda molnresurser och lokala resurser med hjälp av Azure Multi-Factor Authentication Server med AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## <a name="secure-windows-server-2012-r2-ad-fs-with-azure-multi-factor-authentication-server"></a>Skydda Windows Server 2012 R2 AD FS med Azure Multi-Factor Authentication Server
När du installerar Azure Multi-Factor Authentication Server kan du välja mellan följande alternativ:

* Installera Azure Multi-Factor Authentication Server lokalt på samma server som AD FS.
* Installera Azure Multi-Factor Authentication-adaptern lokalt på AD FS-servern och installera sedan Multi-Factor Authentication-servern på en annan dator.

Innan du börjar bör du vara medveten om följande:

* Du behöver inte installera Azure Multi-Factor Authentication Server på AD FS-servern. Dock måste du installera Multi-Factor Authentication-adaptern för AD FS på en Windows Server 2012 R2 som kör AD FS. Du kan installera servern på en annan dator, förutsatt att det är en version som stöds och installera AD FS-adaptern separat på AD FS-federationsservern. Se följande information om hur du installerar adaptern separat.
* När MFA-serverns AD FS-adapter skapades förväntades det att AD FS kunde överföra namnet på den förlitande parten till adaptern. Den förlitande partens namn kunde sedan användas som ett program. Det visade sig dock inte vara fallet. Om din organisation använder verifieringsmetoder med SMS eller mobilappar innehåller strängarna som definierats i företagsinställningarna platshållaren <$*application_name*$>. Den här platshållaren ersätts inte automatiskt när du använder AD FS-adaptern. Vi rekommenderar att du tar bort platshållaren från relevanta strängar när du skyddar AD FS.
* Det konto som du använder för att logga in måste ha användarbehörighet att skapa säkerhetsgrupper i Active Directory-tjänsten.
* Installationsguiden för Multi-Factor Authentication AD FS-adapter skapar en säkerhetsgrupp med namnet PhoneFactor Admins i din instans av Active Directory. AD FS-tjänstkontot för federationstjänsten läggs sedan till i den här gruppen. Vi rekommenderar att du kontrollerar att gruppen PhoneFactor Admins verkligen har skapats och att AD FS-tjänstkontot är medlem i den här gruppen på domänkontrollanten. Om det behövs lägger du till AD FS-tjänstkontot i gruppen PhoneFactor Admins på domänkontrollanten manuellt.
* Information om hur du installerar webbtjänst-SDK med användarportalen finns i [Distribuera användarportalen för Azure Multi-Factor Authentication Server.](multi-factor-authentication-get-started-portal.md)

### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Installera Azure Multi-Factor Authentication Server lokalt på AD FS-servern
1. Hämta och installera Azure Multi-Factor Authentication Server på AD FS-servern. Mer information om installationen finns i [Komma igång med Azure Multi-Factor Authentication Server](multi-factor-authentication-get-started-server.md).
2. I Azure Multi-Factor Authentication Server-hanteringskonsolen klickar du på **AD FS**-ikonen och väljer sedan alternativen **Tillåt användarregistrering** och **Tillåt användare att välja metod**.
3. Välj ytterligare alternativ som du vill ange för din organisation.
4. Klicka på **Installera AD FS-adapter**.
   <center>![Moln](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Om Active Directory-fönstret visas, betyder det två saker. Datorn är ansluten till en domän och Active Directory-konfigurationen för att skydda kommunikationen mellan AD FS-adaptern och Multi-Factor Authentication-tjänsten är ofullständig. Klicka på **Nästa** för att slutföra konfigurationen automatiskt, eller markera kryssrutan **Hoppa över automatisk konfigurering av Active Directory och konfigurera inställningarna manuellt** och klicka på **Nästa**.
6. Om fönstret Lokal grupp visas, betyder det två saker. Datorn är inte ansluten till en domän och konfigurationen för lokal grupp för att skydda kommunikationen mellan AD FS-adaptern och Multi-Factor Authentication-tjänsten är ofullständig. Klicka på **Nästa** för att slutföra konfigurationen automatiskt, eller markera kryssrutan **Hoppa över automatisk konfigurering av lokal grupp och konfigurera inställningarna manuellt** och klicka på **Nästa**.
7. Klicka på **Nästa** i installationsguiden. Azure Multi-Factor Authentication Server skapar gruppen PhoneFactor Admins och lägger till AD FS-tjänstkontot i PhoneFactor Admins-gruppen.
   <center>![Moln](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Klicka på **Nästa** på sidan **Installationsprogram**.
9. Klicka på **Nästa** i installationsprogrammet för Multi-Factor Authentication AD FS-adaptern.
10. Klicka på **Stäng** när installationen är klar.
11. När adaptern har installerats måste du registrera den med AD FS. Öppna Windows PowerShell och kör följande kommando:<br>
    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
    <center>![Moln](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. För att använda den nyligen registrerade adaptern, redigera den globala autentiseringsprincipen i AD FS. I AD FS-hanteringskonsolen går du till noden **Autentiseringsprinciper**. I avsnittet **Multi-Factor Authentication** klickar du på länken **Redigera** bredvid avsnittet **Globala inställningar**. I fönstret **Redigera global autentiseringsprincip** väljer du **Multi-Factor Authentication** som ytterligare en autentiseringsmetod och klickar på **OK**. Adaptern registreras som WindowsAzureMultiFactorAuthentication. Starta om AD FS-tjänsten för att registreringen ska börja gälla.

<center>![Moln](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

Nu är Multi-Factor Authentication-servern konfigurerad som ytterligare en autentiseringsprovider för användning med AD FS.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Installera en fristående instans av AD FS-adaptern med webbtjänst-SDK
1. Installera webbtjänst-SDK på servern som kör Multi-Factor Authentication Server.
2. Kopiera följande filer från katalogen \Program\Multi-Factor Authentication Server till den server där du planerar att installera AD FS-adaptern:
   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config
3. Kör installationsfilen MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. Starta installationen genom att klicka på **Nästa** i installationsprogrammet för Multi-Factor Authentication AD FS-adaptern.
5. Klicka på **Stäng** när installationen är klar.

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>Redigera filen MultiFactorAuthenticationAdfsAdapter.config
Redigera filen MultiFactorAuthenticationAdfsAdapter.config genom att följa stegen nedan:

1. Ange noden **UseWebServiceSdk** till **sant**.  
2. Ange värdet för **WebServiceSdkUrl** till URL:en för webbtjänst-SDK för Multi-Factor Authentication. Exempel:  *https://contoso.com/&lt;certifikatnamn&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx* där ”certifikatnamn” är namnet på certifikatet.  
3. Redigera skriptet Register-MultiFactorAuthenticationAdfsAdapter.ps1 genom att lägga till *-ConfigurationFilePath &lt;sökväg&gt;* i slutet av kommandot `Register-AdfsAuthenticationProvider`, där *&lt;sökväg&gt;* är den fullständiga sökvägen till filen MultiFactorAuthenticationAdfsAdapter.config.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>Konfigurera webbtjänst-SDK med ett användarnamn och lösenord
Det finns två alternativ för att konfigurera webbtjänst-SDK. Det första alternativet är att använda ett användarnamn och lösenord, det andra är att använda ett klientcertifikat. Följ dessa steg för det första alternativet eller hoppa framåt till det andra.  

1. Ange värdet för **WebServiceSdkUsername** till ett konto som är medlem i säkerhetsgruppen PhoneFactor Admins. Använd formatet &lt;domän&gt;&#92;&lt;användarnamn&gt;.  
2. Ange värdet för **WebServiceSdkPassword** till lämpligt kontolösenord.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>Konfigurera webbtjänst-SDK med ett klientcertifikat
Följ dessa steg om du vill konfigurera webbtjänst-SDK med ett klientcertifikat om du inte vill använda ett användarnamn och lösenord.

1. Skaffa ett klientcertifikat från en certifikatutfärdare för servern som kör webbtjänst-SDK. Lär dig hur du [skaffar klientcertifikat](https://technet.microsoft.com/library/cc770328.aspx).  
2. Importera klientcertifikatet till den lokala datorns personliga certifikatarkiv på servern som kör webbtjänst-SDK. Kontrollera att certifikatutfärdarens offentliga certifikat finns i certifikatarkivet för betrodda rotcertifikat.  
3. Exportera de offentliga och privata nycklarna för klientcertifikatet till en PFX-fil.  
4. Exportera den offentliga nyckeln i Base64-format till en CER-fil.  
5. I Serverhanteraren kontrollerar du att funktionen Web Server (IIS)\Web Server\Security\IIS Client Certificate Mapping Authentication är installerad. Om den inte är installerad väljer du **Lägg till roller och funktioner** för att lägga till funktionen.  
6. I IIS-hanteraren dubbelklickar du på **Konfigurationsredigeraren** i webbplatsen som innehåller den virtuella katalogen för webbtjänst-SDK. Det är viktigt att du markerar webbplatsen och inte den virtuella katalogen.  
7. Gå till avsnittet **system.webServer/security/authentication/iisClientCertificateMappingAuthentication**.  
8. Ange aktiverat till **sant**.  
9. Ange oneToOneCertificateMappingsEnabled till **sant**.  
10. Klicka på knappen **...** bredvid oneToOneMappings och klicka sedan på länken **Lägg till**.  
11. Öppna CER-filen med Base64-format som du exporterade tidigare. Ta bort *-----BEGIN CERTIFICATE-----*, *-----END CERTIFICATE-----* och eventuella radbrytningar. Kopiera den resulterande strängen.  
12. Ange certificate till strängen som du kopierade i föregående steg.  
13. Ange aktiverat till **sant**.  
14. Ange userName till ett konto som är medlem i säkerhetsgruppen PhoneFactor Admins. Använd formatet &lt;domän&gt;&#92;&lt;användarnamn&gt;.  
15. Ange lösenordet till lämpligt kontolösenord och stäng Konfigurationsredigeraren.  
16. Klicka på länken **Använd**.  
17. I den virtuella katalogen för webbtjänst-SDK dubbelklickar du på **Autentisering**.  
18. Kontrollera att ASP.NET-personifiering och Grundläggande autentisering är **aktiverat** och att alla andra objekt är **inaktiverade**.  
19. Dubbelklicka på **SSL-inställningar** i den virtuella katalogen för webbtjänst-SDK.  
20. Ange Klientcertifikat till **Acceptera** och klicka på **Använd**.  
21. Kopiera PFX-filen som du exporterade tidigare till servern som kör AD FS-adaptern.  
22. Importera PFX-filen till den lokala datorns personliga certifikatarkiv.  
23. Högerklicka och välj **Hantera privata nycklar** och ge sedan läsbehörighet till kontot som du använde för att logga in till AD FS-tjänsten.  
24. Öppna klientcertifikatet och kopiera tumavtrycket från fliken **Information**.  
25. I filen MultiFactorAuthenticationAdfsAdapter.config anger du **WebServiceSdkCertificateThumbprint** till strängen som du kopierade i föregående steg.  

Registrera adaptern genom att köra skriptet \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 i PowerShell. Adaptern registreras som WindowsAzureMultiFactorAuthentication. Starta om AD FS-tjänsten för att registreringen ska börja gälla.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Skydda Azure AD-resurser med hjälp av AD FS
Ställ in en anspråksregel så att Active Directory Federation Services genererar multipleauthn-kravet när en användare utför tvåstegsverifiering om du vill skydda din molnresurs. Det här anspråket överförs till Azure AD. Följ dessa steg:

1. Öppna AD FS-hantering.
2. Välj **Förlitande partsförtroenden** till vänster.
3. Högerklicka på **Microsoft Office 365 Identity Platform** och välj **Redigera anspråksregler...**

   ![Molnet](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)

4. För Utfärdande av transformeringsregler klickar du på **Lägg till regel.**

   ![Molnet](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)

5. I guiden Lägg till anspråksregel för transformering väljer du **Släpp igenom eller Filtrera ett inkommande anspråk** i listrutan och klickar sedan på **Nästa**.

   ![Molnet](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)

6. Namnge din regel. 
7. Välj **Autentiseringsmetodreferenser** som den inkommande anspråkstypen.
8. Välj **Släpp igenom alla anspråksvärden**.
    ![Guiden Lägg till anspråksregel för transformering](./media/multi-factor-authentication-get-started-adfs-cloud/configurewizard.png)
9. Klicka på **Slutför**. Stäng AD FS-hanteringskonsolen.

## <a name="related-topics"></a>Relaterade ämnen
Hjälp med felsökning finns i [Azure Multi-Factor Authentication FAQs (Vanliga frågor och svar om Azure Multi-Factor Authentication)](multi-factor-authentication-faq.md)

