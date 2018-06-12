---
title: Felsökning av Kerberos-begränsad delegering konfigurationer för Application Proxy | Microsoft Docs
description: Felsökning av Kerberos-begränsad delegering konfigurationer för Application Proxy
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: 933a5c8a9cde9134071a12dd0aed684358cc8696
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292862"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Felsökning av Kerberos-begränsad delegering konfigurationer för Application Proxy

Metoderna för att uppnå SSO till publicerade program kan variera från ett program till en annan. Ett alternativ som Azure Active Directory (Azure AD) Application Proxy ger som standard är Kerberos-begränsad delegering (KCD). Du kan konfigurera en koppling för dina användare att köra begränsad Kerberos-autentisering till backend-program.

Proceduren för att aktivera KCD är enkelt. Det krävs mer än en förståelse av de olika komponenter och autentiseringsflödet som har stöd för enkel inloggning. Men ibland KCD SSO fungerar inte som förväntat. Du måste goda informationskällor för felsökning av dessa scenarier.

Den här artikeln innehåller en enda åtkomstpunkt för referens som hjälper dig att felsöka och reparera några av de vanligaste problem själv. Den omfattar också diagnos av mer komplicerade problem i implementeringen.

Den här artikeln gör följande antaganden:

-   Distribution av Azure AD Application Proxy per [Kom igång med Application Proxy](manage-apps/application-proxy-enable.md) och allmän åtkomst till icke-KCD program fungerar som förväntat.

-   Publicerade målprogrammet baseras på Internet Information Services (IIS) och Microsofts implementering av Kerberos.

-   Server- och värdar placeras i en enda Azure Active Directory-domän. Detaljerad information om scenarier för domäner och skogar finns i [KCD vitboken](https://aka.ms/KCDPaper).

-   Programmet har publicerats i en Azure-klient med förautentisering aktiverad. Användare förväntas att autentisera till Azure via formulärbaserad autentisering. Autentiseringsscenarier för Rich-klient omfattas inte av den här artikeln. De kan läggas på någon punkt i framtiden.

## <a name="prerequisites"></a>Förutsättningar

Azure AD Application Proxy kan distribueras till många typer av infrastrukturer eller miljöer. Arkitekturerna varierar mellan olika företag. De vanligaste orsakerna till KCD-relaterade problem är inte i miljöer. Felaktig konfiguration av enkla eller allmänt fel medföra de flesta problem.

Därför är det bäst att kontrollera att du har uppfyllt alla krav i [med KCD enkel inloggning med programproxyn](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md) innan du felsöker. Observera i avsnittet om hur du konfigurerar Kerberos-begränsad delegering på 2012R2. Den här processen använder en annan metod för att konfigurera KCD på tidigare versioner av Windows. Dessutom måste du vara uppmärksam på dessa överväganden:

-   Det är inte ovanligt att en domänmedlemsserver att öppna en dialogruta med säker kanal med en specifik domänkontrollant (DC). Servern kan gå sedan till en annan dialogruta vid en given tidpunkt. Connector värdar inte så är begränsad till kommunikation med specifika lokala plats domänkontrollanter.

-   Mellan domäner scenarier som förlitar sig på referenser som en värd för anslutningen till domänkontrollanter som kan vara utanför nätverket perimeternätverket. I dessa fall är det lika viktigt att även skicka trafik och framåt till domänkontrollanter som representerar andra respektive domäner. Om inte, delegering misslyckas.

-   Om möjligt bör du undvika att placera alla aktiva IP-adresser eller ID enheter mellan värddatorer för anslutningen och domänkontrollanter. Dessa enheter är det ibland overintrusive och störa core RPC-trafik.

Testa delegering i enkla scenarier. Flera variabler du införa, Ju mer du kan behöva tävla med. Spara tid genom att begränsa testningen till en enskild koppling. Lägga till ytterligare kopplingar när problemet har lösts.

Vissa miljöfaktorer kan även bidra till ett problem. För att undvika de här faktorerna kan minimera arkitektur så mycket som möjligt under testningen. Till exempel är felkonfigurerad intern brandvägg ACL: er gemensamma. Om möjligt skicka all trafik från en koppling raka via till domänkontrollanter och backend-programmet.

Den bästa platsen att placera kopplingar är så nära som möjligt till sina mål. En brandvägg som placeras infogade vid testning lägger till onödiga komplexitet och kan förlänga din undersökningar.

Vad visar KCD problem? Det finns flera vanliga uppgifter som KCD SSO misslyckas. De första tecken på ett problem visas i webbläsaren.

   ![Felaktig KCD-konfigurationsfel](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![Auktoriseringen misslyckades på grund av saknad behörighet](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Båda dessa bilder visar samma symptom: SSO-fel. Nekad användaråtkomst till programmet.

## <a name="troubleshooting"></a>Felsökning

Hur du felsöker beror på problemet och de problem du se. Innan du går någon längre utforska i följande artiklar. De ger användbar felsökningsinformation:

-   [Felsökning av problem med Application Proxy och felmeddelanden](manage-apps/application-proxy-troubleshoot.md)

-   [Kerberos-fel och problem](manage-apps/application-proxy-troubleshoot.md#kerberos-errors)

-   [Arbeta med enkel inloggning när lokalt och i molnet identiteter är inte identiska](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Om du har fått hittills finns viktigaste problemet. Om du vill starta, avgränsa flödet in i följande tre steg som du kan felsöka.

### <a name="client-pre-authentication"></a>Förautentisering av klient 
Den externa användaren autentiserar till Azure via en webbläsare. Möjligheten att före autentisera till Azure är nödvändiga för KCD SSO ska fungera. Testa och åtgärda denna möjlighet om det uppstår några problem. Mellanlagring av förautentisering är inte relaterad till KCD eller det publicerade programmet. Det är enkelt att korrigera skillnader genom att kontrollera förstånd som ämne kontot finns i Azure. Kontrollera också att det inte har inaktiverats eller blockeras. Felsvar i webbläsaren är beskrivande som förklarar orsaken. Kontrollera andra artiklar för Microsoft-felsökning att kontrollera om du är osäker.

### <a name="delegation-service"></a>Delegering service 
Azure Proxy-koppling som hämtar en Kerberos-tjänstbiljett för användare från en Kerberos Key Distribution Center (KCD).

Extern kommunikation mellan klienten och Azure klientdelen har ingen betydelse KCD. Dessa meddelanden bara se till att KCD fungerar. Tjänsten Azure Proxy finns ett giltigt användar-ID som används för att få en Kerberos-biljett. Utan detta ID KCD är inte möjligt och misslyckas.

Som tidigare nämnts innehåller felmeddelanden som webbläsaren några bra uppfattning om varför saker misslyckas. Se till att notera aktivitets-ID och tidsstämpel i svaret. Den här informationen hjälper dig att korrelera funktionssätt faktiska händelser i händelseloggen Azure Proxy.

   ![Felaktig KCD-konfigurationsfel](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Motsvarande poster visas i händelseloggen visa händelser 13019 eller 12027. Hitta connector händelseloggarna i **program- och tjänstloggar** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt;  **Anslutningen** &gt; **Admin**.

   ![Händelsen 13019 från Application Proxy-händelseloggen](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![Händelsen 12027 från Application Proxy-händelseloggen](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1.   Använd en **A** posten i din interna DNS för programmets-adressen är inte en **CName**.

2.   Bekräfta att kopplingen värden har beviljats behörighet att delegera till den avsedda målkonto SPN. Bekräfta som **Använd valfritt autentiseringsprotokoll** är markerad. Mer information finns i [SSO configuration artikel](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md).

3.   Kontrollera att det finns endast en instans av SPN finns i Azure AD. Problemet `setspn -x` från en kommandotolk på alla värddatorer för medlem av domänen.

4.   Kontrollera att en domänprincip tillämpas som begränsar den [maxstorleken för utfärdade token för Kerberos](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Den här principen stoppar anslutningen från att få en token om det visar sig vara mycket.

En spårning i nätverket som samlar in utbyten mellan connector-värd och en domän KDC är bästa nästa steg att få närmare låg nivå på problemen. Mer information finns i [ingående felsöka papper](https://aka.ms/proxytshootpaper).

Om du ser bra ut biljetter ser du en händelse i loggarna om att autentiseringen misslyckades eftersom programmet returnerade ett 401. Den här händelsen indikerar att målprogrammet nekas din biljett. Gå till nästa steg.

### <a name="target-application"></a>Målprogram 
Konsumenter av Kerberos-biljetten som tillhandahålls av kopplingen. I det här skedet räknar med att anslutning har skickat en Kerberos-tjänstbiljett till serverdelen. Den här biljetten är en rubrik i den första programbegäran.

1.   Verifiera att programmet är tillgänglig direkt från webbläsaren på connector-värden med hjälp av interna programmets URL som definierats i portalen. Sedan kan du logga in utan problem. Information finns i anslutningen **Felsök** sidan.

2.   Bekräfta att autentiseringen mellan webbläsaren och programmet använder Kerberos fortfarande på connector-värden. Vidta någon av följande åtgärder:

3.  Kör DevTools (**F12**) i Internet Explorer eller Använd [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) från connector-värden. Gå till programmet genom att använda Intern URL. Granska de erbjudna WWW auktorisering rubriker som returneras i svaret från programmet för att se till att antingen förhandla eller Kerberos finns. 

    a. Nästa Kerberos blob som returneras i svaret från webbläsaren till programmet som börjar med **YII**. Dessa bokstäverna anger att Kerberos körs. Microsoft NT LAN Manager (NTLM), å andra sidan börjar alltid med **TlRMTVNTUAAB**, som läser NTLM Security Support Provider (NTLMSSP) när avkoda från Base64. Om du ser **TlRMTVNTUAAB** i början av blob Kerberos finns inte. Om du inte ser **TlRMTVNTUAAB**, Kerberos sannolikt tillgängliga.

       > [!NOTE]
       > Om du använder Fiddler kräver den här metoden att tillfälligt inaktivera utökat skydd på programmets konfiguration i IIS.

       ![Webbläsarfönster Nätverkskontroll](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    b. Blob i den här bilden inte börjar med **TIRMTVNTUAAB**. Kerberos är tillgänglig i det här exemplet och Kerberos-blob som inte börjar med **YII**.

4.  Ta bort NTLM tillfälligt från listan med providers på IIS-webbplatsen. Använda appen direkt från Internet Explorer på värden för anslutningen. NTLM är inte längre i listan med providers. Du kan komma åt programmet med hjälp av Kerberos endast. Om programmet inte kan det finnas ett problem med programmets konfiguration. Kerberos-autentisering fungerar inte.

    a. Om Kerberos inte är tillgänglig, kontrollera programmets autentiseringsinställningarna i IIS. Kontrollera att **Negotiate** visas överst med NTLM under den. Om du ser **inte förhandla**, **Kerberos eller Negotiate**, eller **PKU2U**, Fortsätt bara om Kerberos fungerar.

       ![Windows autentiseringsproviders](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
    b. Med Kerberos och NTLM på plats kan du tillfälligt inaktivera förautentisering för programmet i portalen. Försök att komma åt den från internet med hjälp av den externa Webbadressen. Du uppmanas att autentisera. Det går att göra det med samma konto som används i föregående steg. Om inte, det finns ett problem med backend-programmet, inte KCD.

    c. Återaktivera förautentisering i portalen. Autentisera via Azure genom att försöka ansluta till programmet via den externa URL: en. Om SSO misslyckas, kan du se ett förbjudet felmeddelande i webbläsaren och händelsen 13022 i loggen:

       *Microsoft AAD Application Proxy Connector autentisera inte användaren eftersom backend-servern svarar på Kerberos autentiseringsförsök med ett HTTP 401-fel.*

       ![HTTTP 401 förbjudet fel](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

    d. Kontrollera IIS-program. Kontrollera att konfigurerade programpoolen och SPN är konfigurerade för att använda samma konto i Azure AD. Navigera i IIS som visas i följande bild:

       ![IIS-konfigurationsfönstret](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

       När du känner till identiteten kontrollerar du det här kontot har konfigurerats med SPN i fråga. Ett exempel är `setspn –q http/spn.wacketywack.com`. Skriv följande i Kommandotolken:

       ![SetSPN-kommandofönster](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

    e. Kontrollera SPN definieras programinställningar i portalen. Kontrollera att samma SPN konfigurerats mot Azure AD-kontot används av programpoolen app.

       ![SPN-konfigurationen i Azure-portalen](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
    f. Gå till IIS och välj den **Konfigurationsredigeraren** alternativ för programmet. Gå till **system.webServer/security/authentication/windowsAuthentication**. Kontrollera att värdet **UseAppPoolCredentials** är **SANT**.

       ![IIS-konfiguration app pooler autentiseringsuppgifter alternativet](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

       Ändra värdet till **SANT**. Ta bort alla cachelagrade Kerberos-biljetter från backend-servern genom att köra följande kommando:

       ```powershell
       Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
       ``` 

Mer information finns i [Rensa Kerberos-biljetten klientcachen för alla sessioner](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).



Om du lämnar Kernel-läge har aktiverats, förbättrar prestanda för Kerberos-åtgärder. Men det gör även biljett för den begärda tjänsten som ska dekrypteras med hjälp av datorkontot. Det här kontot kallas även det lokala systemet. Det här värdet till **SANT** att bryta KCD när programmet ligger över flera servrar i en servergrupp.

-   En ytterligare kontroll inaktivera **utökad** skydd för. I vissa scenarier, **utökad** skydd bröt mot KCD när den har aktiverats i specifika konfigurationer. I sådana fall kan har ett program publicerats som en undermapp till standardwebbplatsen. Det här programmet har konfigurerats för anonym autentisering. Alla dialogrutor är nedtonade, vilket tyder på underordnade objekt skulle ärva inställningar för alla aktiva. Vi rekommenderar att du testar, men glöm inte att återställa det här värdet till **aktiverad**, där det är möjligt.

    Den här extra kontroll placerar du följer för att använda ditt publicerade program. Du kan få upp hastigheten ytterligare kopplingar som också har konfigurerats för att delegera. Mer information finns i mer ingående tekniska genomgången [felsökning av Azure AD Application Proxy](https://aka.ms/proxytshootpaper).

Om du fortfarande inte kan göra förlopp hjälpa Microsoft support dig. Skapa ett supportärende direkt i portalen. En ingenjör att kontakta dig.

## <a name="other-scenarios"></a>Andra scenarier

- Azure Application Proxy begär en Kerberos-biljett innan du skickar en begäran till ett program. Vissa program från tredje part som Tableau Server som inte den här metoden för att autentisera. Dessa program förväntas mer konventionella förhandlingarna ska kunna utföras. Den första begäranden är anonyma, vilket gör att programmet att svara med autentiseringstyper stöder via ett 401.

- Multi-hop autentisering används ofta i scenarier där ett program nivåer, med en serverdel och klientdelen där både kräver autentisering, till exempel SQL Server Reporting Services. Om du vill konfigurera multihop scenario finns support-artikeln [Kerberos-begränsad delegering kan krävs Protokollövergång i scenarier med flera hopp](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Nästa steg
[Konfigurera KCD på en hanterad domän](../active-directory-domain-services/active-directory-ds-enable-kcd.md).
