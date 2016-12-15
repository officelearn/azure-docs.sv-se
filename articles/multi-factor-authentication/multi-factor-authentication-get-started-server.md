---
title: "Komma igång med Azure Multi-Factor Authentication Server"
description: "Det här är sidan om Azure Multi-Factor Authentication som beskriver hur du kommer igång med Azure MFA Server."
services: multi-factor-authentication
keywords: "autentiseringsserver, azure multifaktor autentisering appaktiveringssida, hämtning autentiseringsserver"
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: e94120e4-ed77-44b8-84e4-1c5f7e186a6b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/29/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2e2d680a0f54830f6086a4d6ac98f4a550f4ee46
ms.openlocfilehash: 66726c39c09ed867beb999f9589dfef3f7cf65bb

---

# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Komma igång med Azure Multi-Factor Authentication Server
<center>![MFA lokalt](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Nu när vi har bestämt oss för att använda Multi-Factor Authentication lokalt är det dags att sätta igång. Den här sidan innehåller anvisningar för hur du installerar servern och konfigurerar den med ditt lokala Active Directory. Om du redan har installerat PhoneFactor-servern och behöver uppgradera läser du avsnittet [Uppgradera till Azure Multi-Factor Server](multi-factor-authentication-get-started-server-upgrade.md). Om du letar efter information om hur du installerar bara webbtjänsten läser du [Distribuera webbtjänsten Azure Multi-Factor Authentication Server Mobile App](multi-factor-authentication-get-started-server-webservice.md).

## <a name="download-the-azure-multi-factor-authentication-server"></a>Ladda ned Azure Multi-Factor Authentication Server
Du kan ladda ned Azure Multi-Factor Authentication Server på två sätt. Både görs via Azure-portalen. Det första är genom att hantera Multi-Factor Auth-providern direkt. Det andra är via tjänstinställningarna. Det andra alternativet kräver antingen en Multi-Factor Auth-provider eller en Azure MFA-, Azure AD Premium- eller Enterprise Mobility Suite-licens.

### <a name="to-download-the-azure-multi-factor-authentication-server-from-the-azure-classic-portal"></a>Så här laddar du ned Azure Multi-Factor Authentication-servern från den klassiska Azure-portalen

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) som administratör.
2. Välj **Active Directory** till vänster.
3. På Active Directory-sidan klickar du på **Multi-Factor Auth Providers**
    ![Multi-Factor Auth Providers](./media/multi-factor-authentication-get-started-server/authproviders.png) (Multi-Factor Authentication-providrar)
4. Klicka på **Hantera** längst ned. En ny sida öppnas.
5. Klicka på **Hämtningsbara filer**.
6. Klicka på länken **Ladda ned** ovanför **Skapa autentiseringsuppgifter för aktivering**.
   ![Ladda ned](./media/multi-factor-authentication-get-started-server/download4.png)
7. Spara den nedladdade filen.

### <a name="to-download-the-azure-multi-factor-authentication-server-from-the-service-settings"></a>Så här laddar du ned Azure Multi-Factor Authentication Server från tjänstinställningarna
1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) som administratör.
2. Välj **Active Directory** till vänster.
3. Dubbelklicka på din instans av Azure AD.
4. Klicka på **Konfigurera** längst upp.
5. Rulla ned till avsnittet **Multi-Factor Authentication** och välj **Hantera tjänstinställningar**.
6. På sidan för tjänstinställningar klickar du på **Gå till portalen** längst ned på sidan. En ny sida öppnas.
   ![Ladda ned](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. Klicka på **Hämtningsbara filer**.
8. Klicka på länken **Ladda ned** ovanför **Skapa autentiseringsuppgifter för aktivering**.
    ![Ladda ned](./media/multi-factor-authentication-get-started-server/download4.png)
9. Spara den nedladdade filen.

## <a name="install-and-configure-the-azure-multi-factor-authentication-server"></a>Installera och konfigurera Azure Multi-Factor Authentication Server
Nu när du har laddat ned servern kan du installera och konfigurera den.  Se till att servern som du installerar den på uppfyller följande krav:

| Krav för Azure Multi-Factor Authentication Server | Beskrivning |
|:--- |:--- |
| Maskinvara |<li>200 MB ledigt hårddiskutrymme</li><li>x32- eller x64-processor</li><li>Minst 1 GB RAM-minne</li> |
| Programvara |<li>Windows Server 2008 eller senare om värden är ett serveroperativsystem</li><li>Windows 7 eller senare om värden är ett klientoperativsystem</li><li>Microsoft .NET 4.0 Framework</li><li>IIS 7.0 eller senare om du installerar användarportalen eller webbtjänst-SDK</li> |

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Krav för Azure Multi-Factor Authentication Server-brandvägg
- - -
Varje MFA-server måste kunna kommunicera på port 443 för utgående trafik till följande adresser:

* https://pfd.phonefactor.net
* https://pfd2.phonefactor.net
* https://css.phonefactor.net

Om brandväggar för utgående trafik är begränsade på port 443 öppnar du följande IP-adressintervall:

| IP-undernät | Nätmask | IP-intervall |
|:--- |:--- |:--- |
| 134.170.116.0/25 |255.255.255.128 |134.170.116.1 – 134.170.116.126 |
| 134.170.165.0/25 |255.255.255.128 |134.170.165.1 – 134.170.165.126 |
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129 – 70.37.154.254 |

Om du inte använder funktionen Händelsebekräftelse, och om användarna inte använder mobilappar för att verifiera från enheter i företagets nätverk, kan IP-adresserna begränsas till följande intervall:

| IP-undernät | Nätmask | IP-intervall |
|:--- |:--- |:--- |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79 |
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79 |
| 70.37.154.200/29 |255.255.255.248 |70.37.154.201 – 70.37.154.206 |

### <a name="to-install-and-configure-the-azure-multi-factor-authentication-server"></a>Så här installerar du och konfigurerar Azure Multi-Factor Authentication-servern

1. Dubbelklicka på den körbara filen. När du gör det startar installationen.
2. Kontrollera att mappen på skärmen Välj installationsmapp stämmer och klicka på **Nästa**.
3. När installationen har slutförts klickar du på **Slutför**.  Konfigurationsguiden startar.
4. Markera **Hoppa över autentiseringskonfigurationsguiden** på välkomstskärmen i konfigurationsguiden och klicka på **Nästa**.  När du gör det stängs guiden och servern startas.
    ![Molnet](./media/multi-factor-authentication-get-started-server/skip2.png)
5. Tillbaka på sidan som vi laddade ned servern från klickar du på **Skapa autentiseringsuppgifter för aktivering**. Kopiera den här informationen till Azure MFA Server i de tillgängliga rutorna och klicka på **Aktivera**.

Stegen ovan demonstrerar en snabbinstallation med konfigurationsguiden.  Du kan köra autentiseringsguiden igen genom att välja den från Verktyg-menyn på servern.

## <a name="import-users-from-active-directory"></a>Importera användare från Active Directory
Nu när servern har installerats och konfigurerats kan du snabbt importera användare till Azure MFA Server.

1. Välj **Användare** i Azure MFA Server till vänster.
2. Välj **Importera från Active Directory** längst ned på sidan.
3. Nu kan du antingen söka efter enskilda användare eller söka i AD-katalogen efter organisationsenheter som innehåller användare.  I det här exemplet anger vi organisationsenheten för användare.
4. Markera alla användare till höger och klicka på **Importera**.  Ett popup-meddelande visas som informerar dig om att åtgärden lyckades.  Stäng importfönstret.

![Molnet](./media/multi-factor-authentication-get-started-server/import2.png)

## <a name="send-users-an-email"></a>Skicka ett e-postmeddelande till användare
Nu när du har importerat användarna till MFA-servern rekommenderar vi att du skickar ett e-postmeddelande för att meddela dem att de har registrerats för tvåstegsverifiering.

Innehållet i e-postmeddelandet som du skickar beror på hur du konfigurerade användarna för tvåstegsverifiering. Om du till exempel importerade dina användares telefonnummer från företagets katalog bör e-postmeddelandet innehålla de fördefinierade telefonnumren så att användarna vet vad de ska förvänta sig. Om du inte importerade användarnas telefonnummer, eller om användarna har konfigurerats att använda mobilappen, skickar du på motsvarande sätt ett e-postmeddelande som uppmanar dem att slutföra kontoregistreringen genom att klicka på en länk till användarportalen för Azure Multi-Factor Authentication.

Innehållet i e-postmeddelandet varierar också beroende på vilken verifieringsmetod som har konfigurerats för användarna (telefonsamtal, SMS eller mobilapp).  Om användarna till exempel måste använda en PIN-kod när de autentiserar så meddelar e-postmeddelandet vilken deras ursprungliga PIN-kod är.  Användarna uppmanas att ändra sina PIN-koder under den första verifieringen.


### <a name="configure-email-and-email-templates"></a>Konfigurera e-post och e-postmallar
Klicka på e-postikonen till vänster för att konfigurera inställningarna för e-postmeddelanden som du skickar. Här kan du ange SMTP-information för din e-postserver och skicka e-post genom att markera kryssrutan **Skicka e-post till användare**.

![E-postinställningar](./media/multi-factor-authentication-get-started-server/email1.png)

På fliken E-postinnehåll kan du se de e-postmallar som du kan välja mellan. Välj lämplig mall beroende på hur du har konfigurerat användarna för tvåstegsverifiering.

![E-postmallar](./media/multi-factor-authentication-get-started-server/email2.png)

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Hur Azure Multi-Factor Authentication Server hanterar användardata
När du använder MFA Server (Multi-Factor Authentication) lokalt lagras en användares data på de lokala servrarna. Inga beständiga användardata lagras i molnet. När användaren utför en tvåstegsverifiering skickar MFA-servern data till Azure MFA-molntjänsten för att genomföra verifieringen. När dessa autentiseringsförfrågningar skickas till molntjänsten skickas följande fält i begäran och loggar så att de är tillgängliga i kundens autentiserings-/användningsrapporter. Vissa av fälten är valfria och kan därför aktiveras eller inaktiveras i Multi-Factor Authentication Server. Kommunikationen från MFA Server till MFA-molntjänsten använder SSL/TLS över port 443 för utgående trafik. Dessa fält är:

* Unikt ID – antingen användarnamnet eller internt MFA Server-ID
* För- och efternamn (valfritt)
* E-postadress (valfritt)
* Telefonnummer – vid autentisering via röstsamtal eller SMS
* Enhetstoken – vid autentisering via mobilapp
* Autentiseringsläge
* Autentiseringsresultat
* MFA Server-namn
* MFA Server-IP
* Klientens IP – om det är tillgängligt

Förutom fälten ovan lagras även verifieringsresultatet (lyckades/avvisades) och orsaken till ett eventuellt avvisande tillsammans med autentiseringsinformationen och är tillgängligt i autentiserings-/användningsrapporterna.

## <a name="advanced-azure-multi-factor-authentication-server-configurations"></a>Avancerade Azure Multi-Factor Authentication Server-konfigurationer
Mer information om avancerade inställningar och konfigurationsinformation finns i följande tabell:

| Metod | Beskrivning |
|:--- |:--- |
| [Användarportal](multi-factor-authentication-get-started-portal.md) |Information om hur du installerar och konfigurerar användarportalen, inklusive distribution och självbetjäning. |
| [Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md) |Information om hur du  konfigurerar Azure Multi-Factor Authentication med AD FS. |
| [RADIUS-autentisering](multi-factor-authentication-get-started-server-radius.md) |Information om hur du installerar och konfigurerar Azure MFA Server med RADIUS. Genom att använda RADIUS kan du integrera olika tredjepartssystem med Azure MFA Server. |
| [IIS-autentisering](multi-factor-authentication-get-started-server-iis.md) |Information om hur du installerar och konfigurerar Azure MFA Server med IIS. Genom att använda IIS kan du integrera olika tredjepartssystem med Azure MFA Server. |
| [Windows-autentisering](multi-factor-authentication-get-started-server-windows.md) |Information om hur du installerar och konfigurerar Azure MFA Server med Windows-autentisering. |
| [LDAP-autentisering](multi-factor-authentication-get-started-server-ldap.md) |Information om hur du installerar och konfigurerar Azure MFA Server med LDAP-autentisering. Genom att använda LDAP kan du integrera olika tredjepartssystem med Azure MFA Server. |
| [Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS](multi-factor-authentication-get-started-server-rdg.md) |Information om hur du installerar och konfigurerar Azure MFA Server med Fjärrskrivbordsgateway med RADIUS. |
| [Synkronisera med Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md) |Information om hur du installerar och konfigurerar synkronisering mellan Active Directory och Azure MFA Server. |
| [Distribuera webbtjänsten Azure Multi-Factor Authentication Server Mobile App](multi-factor-authentication-get-started-server-webservice.md) |Information om hur du installerar och konfigurerar Azure MFA Server-webbtjänsten. |
| [Avancerade scenarier med Azure Multi-Factor Authentication och virtuella privata nätverk från tredje part](multi-factor-authentication-advanced-vpn-configurations.md) | Stegvisa konfigurationsguider för Cisco-, Citrix- och Juniper-baserade VPN-tillämpningar. |



<!--HONumber=Dec16_HO1-->


