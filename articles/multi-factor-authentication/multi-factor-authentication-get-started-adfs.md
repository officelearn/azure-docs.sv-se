<properties 
    pageTitle="Komma igång med Azure Multi-Factor Authentication och Active Directory Federation Services" 
    description="Det här är sidan om Azure Multi-Factor Authentication som beskriver hur du kommer igång med Azure MFA och AD FS." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtland"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>

# Komma igång med Azure Multi-Factor Authentication och Active Directory Federation Services



<center>![Molnet](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Om din organisation har federerat det lokala Active Directory med Azure Active Directory med hjälp av AD FS är följande två Azure Multi-Factor Authentication-alternativ tillgängliga.

- Skydda molnresurser med Azure Multi-Factor Authentication eller Active Directory Federation Services 
- Skydda molnet och lokala resurser med Azure Multi-Factor Authentication Server 

Följande tabell sammanfattar autentiseringsupplevelsen när resurser skyddas med Azure Multi-Factor Authentication och AD FS

|Autentiseringsupplevelse – webbläsarbaserade appar|Autentiseringsupplevelse – appar som inte är webbläsarbaserade
:------------- | :------------- | :------------- |
Skydda Azure AD-resurser med hjälp av Azure Multi-Factor Authentication |<li>Den första  faktorn för autentisering utförs lokalt med hjälp av AD FS.</li> <li>Den andra faktorn är en telefonbaserad metod som utförs med hjälp av molnautentisering.</li>|Slutanvändare kan använda applösenord för att logga in.
Skydda Azure AD-resurser med hjälp av Active Directory Federation Services |<li>Den första  faktorn för autentisering utförs lokalt med hjälp av AD FS.</li><li>Den andra faktorn utförs lokalt genom att anspråket tillämpas.</li>|Slutanvändare kan använda applösenord för att logga in.

Varningar med applösenord för federerade användare: 

- Applösenord verifieras med molnautentisering och kringgår därför federationer. Federation används endast aktivt när applösenorden konfigureras.
- Inställningar för lokal klientåtkomstkontroll respekteras inte av applösenord.
- Du kan inte använda lokal autentiseringsloggning med applösenord.
- Inaktiveringen eller borttagningen av konton kan ta upp till tre timmar för dirsync, vilket försenar inaktiveringen eller borttagningen av applösenordet i molnidentiteten.

Information om hur du konfigurerar Azure Multi-Factor Authentication eller Azure Multi-Factor Authentication Server med AD FS finns här:

- [Skydda molnresurser med hjälp av Azure Multi-Factor Authentication och AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
- [Skydda molnresurser och lokala resurser med Azure Multi-Factor Authentication Server med Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
- [Skydda molnet och lokala resurser med Azure Multi-Factor Authentication Server med AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)







 


<!--HONumber=jun16_HO2-->


