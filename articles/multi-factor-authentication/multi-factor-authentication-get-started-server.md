---
title: "Kom igång med Azure Multi-Factor Authentication-server | Microsoft Docs"
description: "Det här är sidan om Azure Multi-Factor Authentication som beskriver hur du kommer igång med Azure MFA Server."
services: multi-factor-authentication
keywords: "autentiseringsserver, azure multifaktor autentisering appaktiveringssida, hämtning autentiseringsserver"
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: e94120e4-ed77-44b8-84e4-1c5f7e186a6b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/26/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 4235dfd0e17b9892787dd86d807b8f1f6e360675
ms.contentlocale: sv-se
ms.lasthandoff: 06/30/2017

---

# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Komma igång med Azure Multi-Factor Authentication Server

<center>![MFA lokalt](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Nu när vi har bestämt oss för att använda en lokal Multi-Factor Authentication-server, är det dags att sätta igång. Den här sidan innehåller anvisningar för hur du installerar servern och konfigurerar den med ditt lokala Active Directory. Om du redan har installerat MFA-servern och vill uppgradera så kan du läsa artikeln om att [uppgradera till den senaste Azure Multi-Factor Authentication-servern](multi-factor-authentication-server-upgrade.md). Om du vill veta hur du endast installerar webbtjänsten kan du läsa artikeln [Aktivera mobilappautentisering och Azure Multi-Factor Authentication-servern](multi-factor-authentication-get-started-server-webservice.md).
 
## <a name="plan-your-deployment"></a>Planera distributionen

Innan du laddar ned Azure Multi-Factor Authentication-servern måste du överväga dina krav på belastningar och hög tillgänglighet. Använd den här informationen till att bestämma hur och var du ska distribuera servern. 

En tumregel för mängden minne som behövs utgår från antalet användare som förväntas autentisera sig regelbundet. 

| Användare | RAM |
| ----- | --- |
| 1-10,000 | 4 GB |
| 10,001-50,000 | 8 GB |
| 50,001-100,000 | 12 GB |
| 100,000-200,001 | 16 GB |
| 200,001+ | 32 GB |

Behöver du konfigurera flera servrar för hög tillgänglighet eller belastningsutjämning? Det finns ett antal olika sätt att ordna den här konfigurationen med Azure MFA-servern. När du installerar din första Azure MFA-server blir den till huvudserver. Eventuella ytterligare servrar blir underordnade och synkroniserar automatiskt användare och eventuell konfiguration. Sedan kan du konfigurera en primär server och använda resten som reserver, eller så kan du konfigurera belastningsutjämning mellan alla servrarna. 

När en Azure MFA-huvudserver kopplas från kan de underordnade servrarna fortfarande bearbeta begäranden om tvåstegsverifiering. Du kan dock inte lägga till nya användare, och befintliga användare kan inte uppdatera sina inställningar förrän huvudservern är tillbaka online eller en underordnad server blir befordrad. 

## <a name="prepare-your-environment"></a>Förbered din miljö

Se till att den server du använder för Azure Multi-Factor Authentication uppfyller följande krav:

| Krav för Azure Multi-Factor Authentication Server | Beskrivning |
|:--- |:--- |
| Maskinvara |<li>200 MB ledigt hårddiskutrymme</li><li>x32- eller x64-processor</li><li>Minst 1 GB RAM-minne</li> |
| Programvara |<li>Windows Server 2008 eller senare om värden är ett serveroperativsystem</li><li>Windows 7 eller senare om värden är ett klientoperativsystem</li><li>Microsoft .NET 4.0 Framework</li><li>IIS 7.0 eller senare om du installerar användarportalen eller webbtjänst-SDK</li> |

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Krav för Azure Multi-Factor Authentication Server-brandvägg
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

Om du inte använder funktionen Händelsebekräftelse och om användarna inte använder mobilappar för verifiering från enheter i företagets nätverk så behöver du bara följande intervall:

| IP-undernät | Nätmask | IP-intervall |
|:--- |:--- |:--- |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79 |
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79 |
| 70.37.154.200/29 |255.255.255.248 |70.37.154.201 – 70.37.154.206 |

## <a name="download-the-azure-multi-factor-authentication-server"></a>Ladda ned Azure Multi-Factor Authentication Server
Du kan ladda ned Azure Multi-Factor Authentication Server på två sätt. Både görs via Azure-portalen. Det första är genom att hantera Multi-Factor Auth-providern direkt. Det andra är via tjänstinställningarna. Det andra alternativet kräver antingen en Multi-Factor Auth-provider eller en Azure MFA-, Azure AD Premium- eller Enterprise Mobility Suite-licens.

> [!Important]
> De här två alternativen kan verka liknande, men det är viktigt att veta vilken som ska användas. Om dina användare har licenser där MFA (Azure MFA, Azure AD Premium eller Enterprise Mobility + Security) ingår skapar du inte en Multi-Factor Authentication-provider för att komma till servernedladdningen. Använd istället alternativ 2 för att hämta servern från tjänstinställningssidan. 

### <a name="option-1-download-azure-multi-factor-authentication-server-from-the-azure-classic-portal"></a>Alternativ 1: Hämta Azure Multi-Factor Authentication-server från den klassiska Azure-portalen

Använd det här hämtningsalternativet om du redan har en multifaktorautentiseringsprovider, eftersom du betalar för MFA per aktiverad användare eller per autentisering. 

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) som administratör.
2. Välj **Active Directory** till vänster.
3. På Active Directory-sidan klickar du på **Multi-Factor Auth Providers** ![Multi-Factor Auth Providers](./media/multi-factor-authentication-get-started-server/authproviders.png) (Multi-Factor Auth-providers)
4. Klicka på **Hantera** längst ned. En ny sida öppnas.
5. Klicka på **Hämtningsbara filer**.
6. Klicka på länken **Ladda ned**.
   ![Ladda ned](./media/multi-factor-authentication-get-started-server/download4.png)
7. Spara den nedladdade filen.

### <a name="option-2-download-azure-multi-factor-authentication-server-from-the-service-settings"></a>Alternativ 2: Hämta Azure Multi-Factor Authentication-server från tjänstinställningarna

Använd det här hämtningsalternativet om du har licenser för Enterprise Mobility Suite, Azure AD Premium eller Enterprise Cloud Suite. 

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) som administratör.
2. Välj **Active Directory** till vänster.
3. Dubbelklicka på din instans av Azure AD.
4. Klicka på **Konfigurera** längst upp.
5. Rulla ned till avsnittet **Multi-Factor Authentication** och välj **Hantera tjänstinställningar**.
6. På sidan för tjänstinställningar klickar du på **Gå till portalen** längst ned på sidan. En ny sida öppnas.
   ![Ladda ned](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. Klicka på **Hämtningsbara filer**.
8. Klicka på länken **Ladda ned**.
    ![Ladda ned](./media/multi-factor-authentication-get-started-server/download4.png)
9. Spara den nedladdade filen.

## <a name="install-and-configure-the-azure-multi-factor-authentication-server"></a>Installera och konfigurera Azure Multi-Factor Authentication Server
Nu när du har laddat ned servern kan du installera och konfigurera den.  Se till att servern du installerar den på uppfyller kraven i planeringsavsnittet. 

I de här stegen görs en snabbinstallation i konfigurationsguiden. Om du inte ser guiden eller om du vill köra den igen så kan du välja den från menyn **Verktyg** på servern.

1. Dubbelklicka på den körbara filen. 
2. Kontrollera att mappen på skärmen Välj installationsmapp stämmer och klicka på **Nästa**.
3. När installationen har slutförts klickar du på **Slutför**.  Konfigurationsguiden startar.
4. Markera **Hoppa över autentiseringskonfigurationsguiden** på välkomstskärmen i konfigurationsguiden och klicka på **Nästa**.  Guiden stängs och servern startas.
    ![Molnet](./media/multi-factor-authentication-get-started-server/skip2.png)
5. Tillbaka på sidan som vi laddade ned servern från klickar du på **Skapa autentiseringsuppgifter för aktivering**. Kopiera den här informationen till Azure MFA Server i de tillgängliga rutorna och klicka på **Aktivera**.

## <a name="import-users-from-active-directory"></a>Importera användare från Active Directory
Nu när servern har installerats och konfigurerats kan du snabbt importera användare till Azure MFA Server.

1. Välj **Användare** i Azure MFA Server till vänster.
2. Välj **Importera från Active Directory** längst ned på sidan.
3. Nu kan du antingen söka efter enskilda användare eller söka i AD-katalogen efter organisationsenheter som innehåller användare.  I det här exemplet anger vi organisationsenheten för användare.
4. Markera alla användare till höger och klicka på **Importera**.  Ett popup-meddelande visas som informerar dig om att åtgärden lyckades.  Stäng importfönstret.
   ![Molnet](./media/multi-factor-authentication-get-started-server/import2.png)

## <a name="send-users-an-email"></a>Skicka ett e-postmeddelande till användare
Nu när du har importerat användarna till MFA-servern ska du skicka ett e-postmeddelande om att de har registrerats för tvåstegsverifiering.

Innehållet i e-postmeddelandet som du skickar beror på hur du konfigurerade användarna för tvåstegsverifiering. Om du till exempel importerade telefonnummer från företagets katalog bör e-postmeddelandet innehålla de fördefinierade telefonnumren så att användarna vet vad de ska förvänta sig. Om du inte har importerat användarnas telefonnummer, eller om användarna kommer att använda mobilappen, skickar du ett e-postmeddelande där de uppmanas att slutföra kontoregistreringen. Inkludera en hyperlänk till användarportalen för Azure Multi-Factor Authentication i e-postmeddelandet.

Innehållet i e-postmeddelandet varierar också beroende på vilken verifieringsmetod som har konfigurerats för användarna (telefonsamtal, SMS eller mobilapp).  Om användarna till exempel måste använda en PIN-kod när de autentiserar så meddelar e-postmeddelandet vilken deras ursprungliga PIN-kod är.  Användarna uppmanas att ändra sina PIN-koder under den första verifieringen.


### <a name="configure-email-and-email-templates"></a>Konfigurera e-post och e-postmallar
Klicka på e-postikonen till vänster för att konfigurera inställningarna för e-postmeddelanden som du skickar. På den är sidan kan du ange SMTP-information för din e-postserver och skicka e-post genom att markera kryssrutan **Skicka e-post till användare**.

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

## <a name="next-steps"></a>Nästa steg

- Skapa och konfigurera [användarportalen](multi-factor-authentication-get-started-portal.md) för självbetjäning.

- Installera och konfigurera Azure MFA Server med [Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md), [RADIUS-autentisering](multi-factor-authentication-get-started-server-radius.md) eller [LDAP-autentisering](multi-factor-authentication-get-started-server-ldap.md).

- Konfigurera [Remote Desktop Gateway och Azure Multi-Factor Authentication Server med hjälp av RADIUS](multi-factor-authentication-get-started-server-rdg.md). 

- [Distribuera webbtjänsten Azure Multi-Factor Authentication Server Mobile App](multi-factor-authentication-get-started-server-webservice.md).

- [Avancerade scenarier med Azure Multi-Factor Authentication och virtuella privata nätverk från tredje part](multi-factor-authentication-advanced-vpn-configurations.md).

