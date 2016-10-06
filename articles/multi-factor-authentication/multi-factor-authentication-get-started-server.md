<properties 
    pageTitle="Komma igång med Azure Multi-Factor Authentication Server"
    description="Det här är sidan om Azure Multi-Factor Authentication som beskriver hur du kommer igång med Azure MFA Server."
    services="multi-factor-authentication"
    keywords="autentiseringsserver, azure multifaktor autentisering appaktiveringssida, hämtning autentiseringsserver"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="kgremban"/>


# Komma igång med Azure Multi-Factor Authentication Server




<center>![Molnet](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Nu när vi har bestämt om vi ska använda lokal multifaktorautentisering är det dags att sätta igång. Den här sidan innehåller anvisningar för hur du installerar servern och konfigurerar den med lokalt Active Directory. Om du redan har installerat PhoneFactor-servern och behöver uppgradera läser du avsnittet [Uppgradera till Azure Multi-Factor Server](multi-factor-authentication-get-started-server-upgrade.md). Om du letar efter information om hur du installerar bara webbtjänsten läser du [Distribuera webbtjänsten Azure Multi-Factor Authentication Server Mobile App](multi-factor-authentication-get-started-server-webservice.md).


## Ladda ned Azure Multi-Factor Authentication Server



Du kan ladda ned Azure Multi-Factor Authentication Server på två sätt. Både görs via Azure-portalen. Det första är genom att hantera Multi-Factor Auth-providern direkt. Det andra är via tjänstinställningarna. Det andra alternativet kräver antingen en Multi-Factor Auth-provider eller en Azure MFA-, Azure AD Premium- eller Enterprise Mobility Suite-licens.


### Så här laddar du ned Azure Multi-Factor Authentication-servern från Azure-portalen
--------------------------------------------------------------------------------

1. Logga in på Azure Portal som administratör.
2. Välj Active Directory till vänster.
3. Klicka på **Multi-Factor Auth-providers** längst upp på sidan Active Directory.
4. Klicka på **Hantera** längst ned.
5. En ny sida öppnas.  Klicka på **Hämtningsbara filer.**
![Ladda ned](./media/multi-factor-authentication-sdk/download.png)
6. Ovanför **Skapa autentiseringsuppgifter för aktivering** klickar du på **.**
![Ladda ned](./media/multi-factor-authentication-get-started-server/download4.png)
7. Spara den nedladdade filen.



### Så här laddar du ned Azure Multi-Factor Authentication Server via tjänstinställningarna


1. Logga in på Azure Portal som administratör.
2. Välj Active Directory till vänster.
3. Dubbelklicka på din instans av Azure AD.
4. Klicka längst upp på **Konfigurera.**
![Ladda ned](./media/multi-factor-authentication-sdk/download2.png)
5. Under Multi-Factor Authentication väljer du **Hantera tjänstinställningar**.
6. På sidan för tjänstinställningar klickar du på **Gå till portalen** längst ned på sidan.
![Ladda ned](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. En ny sida öppnas. Klicka på **Hämtningsbara filer**.
8. Ovanför **Skapa autentiseringsuppgifter för aktivering** klickar du på **Ladda ned.**
9. Spara den nedladdade filen.




## Installera och konfigurera Azure Multi-Factor Authentication Server
Nu när du har laddat ned servern kan du installera och konfigurera den.  Se till att servern som du installerar den på uppfyller följande krav:



Krav för Azure Multi-Factor Authentication Server|Beskrivning|
:------------- | :------------- |
Maskinvara|<li>200 MB ledigt hårddiskutrymme</li><li>x32- eller x64-processor</li><li>Minst 1 GB RAM-minne</li>
Programvara|<li>Windows Server 2008 eller senare om värden är ett serveroperativsystem</li><li>Windows 7 eller senare om värden är ett klientoperativsystem</li><li>Microsoft .NET 4.0 Framework</li><li>IIS 7.0 eller senare om du installerar användarportalen eller webbtjänst-SDK</li>

### Krav för Azure Multi-Factor Authentication Server-brandvägg
--------------------------------------------------------------------------------
Varje MFA-server måste kunna kommunicera på port 443 för utgående trafik till följande:

- https://pfd.phonefactor.net
- https://pfd2.phonefactor.net
- https://css.phonefactor.net

Om utgående trafik i brandväggarna är begränsad på port 443 måste du öppna följande IP-adressintervall:

IP-undernät|Nätmask|IP-intervall
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128|134.170.116.1 – 134.170.116.126
134.170.165.0/25|255.255.255.128|134.170.165.1 – 134.170.165.126
70.37.154.128/25|255.255.255.128|70.37.154.129 – 70.37.154.254

Om du inte använder funktioner för Azure Multi-Factor Authentication-händelsebekräftelser och om användarna inte autentiserar med Multi-Factor Authentication-mobilapparna från enheter i företagsnätverket kan IP-intervallen begränsas till följande:


IP-undernät|Nätmask|IP-intervall
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72 – 134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72 – 134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201 – 70.37.154.206


### Så här installerar du och konfigurerar Azure Multi-Factor Authentication-servern
--------------------------------------------------------------------------------


1. Dubbelklicka på den körbara filen. Nu startar installationen.
2. Kontrollera att mappen på skärmen Välj installationsmapp stämmer och klicka på Nästa.
3. När installationen har slutförts klickar du på Slutför.  Nu startar konfigurationsguiden.
4. Markera **Hoppa över autentiseringskonfigurationsguiden** på välkomstskärmen i konfigurationsguiden och klicka på **Nästa**.  Nu stängs guiden och servern startar.
    ![Molnet](./media/multi-factor-authentication-get-started-server/skip2.png)
5. Tillbaka på sidan som vi laddade ned servern från klickar du på **Skapa autentiseringsuppgifter för aktivering**.  Kopiera den här informationen till Azure MFA Server i de tillgängliga rutorna och klicka på **Aktivera**.


Stegen ovan demonstrerar en snabbinstallation med konfigurationsguiden.  Du kan köra autentiseringsguiden igen genom att välja den från Verktyg-menyn på servern.



##Importera användare från Active Directory

Nu när servern har installerats och konfigurerats kan du snabbt importera användare till Azure MFA Server.

### Så här importerar du användare från Active Directory
--------------------------------------------------------------------------------


1. Välj **Användare** i Azure MFA Server till vänster.
2. Välj **Importera från Active Directory** längst ned på sidan.
3. Nu kan du antingen söka efter enskilda användare eller söka i AD-katalogen efter organisationsenheter som innehåller användare.  I det här fallet ska vi välja användare i organisationsenheter.
4. Markera alla användare till höger och klicka på **Importera**.  Ett popup-meddelande visas som informerar dig om att åtgärden lyckades.  Stäng importfönstret.

![Molnet](./media/multi-factor-authentication-get-started-server/import2.png)

## Skicka ett e-postmeddelande till användare
Nu när du har importerat användarna till Azure Multi-Factor Authentication-servern rekommenderar vi att du skickar ett e-postmeddelande till dem som informerar dem om att de har registrerats i Multi-Factor Authentication.

Med Multi-Factor Authentication Server kan du konfigurera dina användare för multifaktorautentisering på flera sätt.  Om du till exempel känner till användarnas telefonnummer eller om du har importerat telefonnumren till Azure Multi-Factor Authentication Server från användarnas företagskatalog så kan e-postmeddelandet informera användarna om att de har konfigurerats att använda Azure Multi-Factor Authentication, innehålla anvisningar för hur de använder Azure Multi-Factor Authentication, samt informera dem om vilket telefonnummer de får sina autentiseringar på.  

Innehållet i e-postmeddelandet varierar beroende på vilken autentiseringsmetod som har angetts för användaren (till exempel telefonsamtal, SMS eller mobilapp).  Om användarna till exempel måste använda en PIN-kod när de autentiserar så meddelar e-postmeddelandet vilken deras ursprungliga PIN-kod är.  Användare måste normalt ändra sina PIN-koder vid den första autentiseringen.

Om användarnas telefonnummer inte har konfigurerats eller importerats till Azure Multi-Factor Authentication Server, eller om användarna har förkonfigurerats att använda mobilappen för autentisering, kan du skicka ett e-postmeddelande som informerar dem om att de har konfigurerats att använda Azure Multi-Factor Authentication och som uppmanar dem att slutföra kontoregistreringen via Azure Multi-Factor Authentication- användarportalen.  En hyperlänk infogas som användarna kan klicka på för att få åtkomst till användarportalen. När användaren klickar på hyperlänken öppnas deras webbläsare och de kommer till företagets Azure Multi-Factor Authentication-användarportal.   


### Konfigurera e-post och e-postmallar

Du kan konfigurera inställningar för dessa e-postmeddelanden genom att klicka på e-postikonen till vänster.  Det är här du kan ange SMTP-information för din e-postserver och skicka ett generellt e-postmeddelande till många användare genom att markera kryssrutan Skicka e-post till användare.

![E-postinställningar](./media/multi-factor-authentication-get-started-server/email1.png)

Fliken E-postinnehåll innehåller alla olika e-postmallar som du kan välja mellan.  Så, beroende på hur du har konfigurerat dina användare att använda Multi-Factor Authentication, kan du välja den mall som passar dig bäst.

![E-postmallar](./media/multi-factor-authentication-get-started-server/email2.png)

## Hur Azure Multi-Factor Authentication Server hanterar användardata

När du använder MFA Server (Multi-Factor Authentication) lokalt lagras en användares data på de lokala servrarna. Inga beständiga användardata lagras i molnet. När användaren utför en tvåfaktorsautentisering skickar MFA Server data till Azure MFA-molntjänsterna för att utföra autentiseringen. När dessa autentiseringsförfrågningar skickas till molntjänsten skickas följande fält i begäran och loggar så att de är tillgängliga i kundens autentiserings-/användningsrapporter. Vissa av fälten är valfria och kan därför aktiveras eller inaktiveras i Multi-Factor Authentication Server. Kommunikationen från MFA Server till MFA-molntjänsten använder SSL/TLS över port 443 för utgående trafik. Dessa fält är:

- Unikt ID – antingen användarnamnet eller internt MFA Server-ID
- För- och efternamn – valfritt
- E-postadress – valfritt
- Telefonnummer – vid autentisering via röstsamtal eller SMS
- Enhetstoken – vid autentisering via mobilapp
- Autentiseringsläge
- Autentiseringsresultat
- MFA Server-namn
- MFA Server-IP
- Klientens IP – om det är tillgängligt



Förutom fälten ovan lagras även autentiseringsresultatet (lyckades/avvisades) och orsaken till ett eventuellt avvisande tillsammans med autentiseringsinformationen och är tillgängligt i autentiserings-/användningsrapporter.


## Avancerade Azure Multi-Factor Authentication Server-konfigurationer
Ytterligare information om avancerade inställningar och konfigurationsinformation finns i tabellen nedan.

Metod|Beskrivning
:------------- | :------------- |
[Användarportalen](multi-factor-authentication-get-started-portal.md)|  Information om hur du installerar och konfigurerar användarportalen, inklusive distribution och självbetjäning.
[Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md)|Information om hur du  konfigurerar Azure Multi-Factor Authentication med AD FS.
[RADIUS-autentisering](multi-factor-authentication-get-started-server-radius.md)|  Information om hur du installerar och konfigurerar Azure MFA Server med RADIUS.
[IIS-autentisering](multi-factor-authentication-get-started-server-iis.md)|Information om hur du installerar och konfigurerar Azure MFA Server med IIS.
[Windows-autentisering](multi-factor-authentication-get-started-server-windows.md)|  Information om hur du installerar och konfigurerar Azure MFA Server med Windows-autentisering.
[LDAP-autentisering](multi-factor-authentication-get-started-server-ldap.md)|Information om hur du installerar och konfigurerar Azure MFA Server med LDAP-autentisering.
[Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS](multi-factor-authentication-get-started-server-rdg.md)|  Information om hur du installerar och konfigurerar Azure MFA Server med Fjärrskrivbordsgateway med RADIUS.
[Synkronisera med Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md)|Information om hur du installerar och konfigurerar synkronisering mellan Active Directory och Azure MFA Server.
[Distribuera webbtjänsten Azure Multi-Factor Authentication Server Mobile App](multi-factor-authentication-get-started-server-webservice.md)|Information om hur du installerar och konfigurerar Azure MFA Server-webbtjänsten.



<!--HONumber=Sep16_HO4-->


