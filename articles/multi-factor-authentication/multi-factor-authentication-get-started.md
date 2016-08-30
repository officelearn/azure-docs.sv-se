<properties 
    pageTitle="Komma igång med Azure Multi-Factor Authentication" 
    description="Välj den Multi-Factor Authentication-säkerhetslösning som passar dig bäst genom att fråga dig vad du vill skydda och var dina användare finns.  Välj sedan molnet, MFA Server eller AD FS." 
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

#Välj rätt Multi-Factor Authentication-säkerhetslösning för dig

Eftersom det finns flera varianter av Azure Multi-Factor Authentication måste vi kontrollera några saker för att ta reda på vilken version som du bör använda.  Närmare bestämt:

-   [Vad vill du skydda?](#what-am-i-trying-to-secure)
-   [Var finns användarna?](#where-are-the-users-located)

Följande avsnitt hjälper dig att besvara dessa frågor.

## Vad vill du skydda?

För att fastställa vilken Multi-Factor Authentication-lösning som är rätt för dig måste vi först besvara frågan vad du försöker skydda med en andra autentiseringsmetod.  Är det ett program som finns i Azure?  Är det till exempel ett fjärråtkomstsystem.  Genom att fastställa vad vi vill skydda ska vi försöka besvara frågan var multifaktorautentisering måste vara aktiverat.  


Vad försöker du skydda?| Multi-Factor Authentication i molnet|Multi-Factor Authentication Server 
------------- | :-------------: | :-------------: |
Första parts Microsoft-appar|* |* |
Saas-appar i appgalleriet|* |* |
IIS-program publicerade via Azure AD App Proxy|* |* |
IIS-program som inte är publicerade via Azure AD App Proxy | |* |
Fjärråtkomst som VPN eller Fjärrskrivbordsgateway (RDG)| |* |



## Var finns användarna?

Beroende på var användarna finns kan vi sedan komma fram till rätt lösning för dig, oavsett om det är Multi-Factor Authentication i molnet eller lokalt med hjälp av MFA-servern.



Användarplats| Lösning
------------- | :------------- | 
Azure Active Directory| Multi-Factor Authentication i molnet|
Azure AD och lokalt AD med federation med AD FS| Både MFA i molnet och MFA Server är tillgängliga alternativ 
Azure AD och lokalt AD med DirSync, Azure AD Sync, Azure AD Connect – utan lösenordssynkronisering|Både MFA i molnet och MFA Server är tillgängliga alternativ 
Azure AD och lokalt AD med DirSync, Azure AD Sync, Azure AD Connect – med lösenordssynkronisering|Multi-Factor Authentication i molnet
Lokalt Active Directory|Multi-Factor Authentication Server

I följande tabell jämförs funktionerna som är tillgängliga med Multi-Factor Authentication i molnet och med Multi-Factor Authentication Server.

 | Multi-Factor Authentication i molnet | Multi-Factor Authentication Server
------------- | :-------------: | :-------------: |
Meddelanden via mobilapp som en andra faktor | ● | ● |
Verifieringskod via mobilapp som en andra faktor | ● | ●
Telefonsamtal som en andra faktor | ● | ● 
Enkelriktad SMS som en andra faktor | ● | ●
Dubbelriktad SMS som en andra faktor |  | ● 
Maskinvarutoken som en andra faktor |  | ● 
Applösenord för klienter som inte stöder MFA | ● |  
Administratörskontroll över autentiseringsmetoder | ● | ● 
PIN-läge |  | ●
Bedrägerivarning | ● | ●
MFA-rapporter | ● | ● 
Engångsförbikoppling |  | ● 
Anpassade hälsningar för telefonsamtal | ● | ● 
Anpassningsbar nummerpresentation för telefonsamtal | ● | ● 
Tillförlitliga IP-adresser | ● | ● 
MFA sparas för betrodda enheter  | ● |  
Villkorlig åtkomst | ● | ● 
Cache |  | ● 

Nu när vi har fastställt om Multi-Factor Authentication i molnet eller lokala MFA Server ska användas kan vi börja konfigurera och använda Azure Multi-Factor Authentication.   **Välj den ikon som representerar ditt scenario!**

<center>




[![Molnet](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>





<!--HONumber=jun16_HO2-->


