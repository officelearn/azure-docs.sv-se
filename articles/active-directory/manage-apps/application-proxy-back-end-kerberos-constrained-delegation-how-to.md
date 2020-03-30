---
title: Felsöka Kerberos begränsad delegering - App Proxy
description: Felsöka Kerberos-begränsade delegeringskonfigurationer för programproxy
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5e866f61409960447e17ecb50b035eabd53dc38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275680"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Felsöka Kerberos-begränsade delegeringskonfigurationer för programproxy

De metoder som finns tillgängliga för att uppnå SSO till publicerade program kan variera från ett program till ett annat. Ett alternativ som Azure Active Directory (Azure AD) Application Proxy erbjuder som standard är Kerberos begränsad delegering (KCD). Du kan konfigurera en anslutningsapp för användarna så att den kör begränsad Kerberos-autentisering till backend-program.

Proceduren för att aktivera KCD är enkel. Det kräver bara en allmän förståelse av de olika komponenter och autentiseringsflöde som stöder SSO. Men ibland fungerar inte KCD SSO som förväntat. Du behöver bra informationskällor för att felsöka dessa scenarier.

Den här artikeln innehåller en enda referenspunkt som hjälper till att felsöka och själv åtgärda några av de vanligaste problemen. Det omfattar också diagnos av mer komplexa genomförandeproblem.

Denna artikel gör följande antaganden:

- Distribution av Azure AD Application Proxy per [Kom igång med programproxy](application-proxy-add-on-premises-application.md) och allmän åtkomst till icke-KCD-program fungerar som förväntat.
- Det publicerade målprogrammet baseras på Internet Information Services (IIS) och Microsofts implementering av Kerberos.
- Server- och programvärdarna finns i en enda Azure Active Directory-domän. Detaljerad information om scenarier mellan domäner och skogar finns i [KCD:s vitbok](https://aka.ms/KCDPaper).
- Ämnesprogrammet publiceras i en Azure-klient med förautentisering aktiverad. Användare förväntas autentisera till Azure via formulärbaserad autentisering. Omfattande klientautentiseringsscenarier omfattas inte av den här artikeln. De kan läggas till någon gång i framtiden.

## <a name="prerequisites"></a>Krav

Azure AD Application Proxy kan distribueras till många typer av infrastrukturer eller miljöer. Arkitekturerna varierar från organisation till organisation. De vanligaste orsakerna till KCD-relaterade problem är inte miljöerna. Enkla felkonfigurationer eller allmänna misstag orsakar de flesta problem.

Därför är det bäst att se till att du har uppfyllt alla förutsättningar i [Att använda KCD SSO med programproxyn](application-proxy-configure-single-sign-on-with-kcd.md) innan du börjar felsöka. Observera avsnittet om konfigurera Kerberos begränsad delegering på 2012R2. Den här processen använder en annan metod för att konfigurera KCD i tidigare versioner av Windows. Också, vara uppmärksam på dessa överväganden:

- Det är inte ovanligt att en domänmedlemsserver öppnar en dialogruta för säker kanal med en viss domänkontrollant (DC). Då kan servern flyttas till en annan dialogruta vid en given tidpunkt. Anslutningsvärdar är därför inte begränsade till kommunikation med endast specifika lokala webbplatsDC:er.
- Scenarier mellan domäner är beroende av hänvisningar som dirigerar en anslutningsvärd till domänkontrollanter som kan vara utanför den lokala nätverksper perimetern. I dessa fall är det lika viktigt att också skicka trafik vidare till domänkontrollanter som representerar andra respektive domäner. Om inte, misslyckas delegeringen.
- Undvik om möjligt att placera aktiva IPS- eller IDS-enheter mellan anslutningsvärdar och DC.Om möjligt bör du undvika att placera aktiva IPS- eller IDS-enheter mellan anslutningsvärdar och DC.Om möjligt, undvik att placera aktiva IPS- eller IDS- Dessa enheter är ibland för påträngande och stör kärn-RPC-trafik.

Testa delegering i enkla scenarier. Ju fler variabler du introducerar, desto mer kan du behöva brottas med. Om du vill spara tid begränsar du testningen till en enda koppling. Lägg till ytterligare kopplingar när problemet har lösts.

Vissa miljöfaktorer kan också bidra till ett problem. Undvik dessa faktorer genom att minimera arkitekturen så mycket som möjligt under testningen. Felkonfigurerade interna brandväggs-ACL:er är till exempel vanliga. Om möjligt skickar du all trafik från en anslutning direkt till DC: erna och backend-programmet.

Den bästa platsen att placera kopplingar är så nära som möjligt till sina mål. En brandvägg som sitter i linje när testning lägger till onödig komplexitet och kan förlänga dina undersökningar.

Vad visar ett KCD-problem? Det finns flera vanliga indikationer på att KCD SSO misslyckas. De första tecknen på ett problem visas i webbläsaren.

![Exempel: Felaktigt KCD-konfigurationsfel](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Exempel: Auktoriseringen misslyckades på grund av saknade behörigheter](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Båda dessa bilder visar samma symptom: SSO-fel. Användaråtkomst till programmet nekas.

## <a name="troubleshooting"></a>Felsökning

Hur du felsöker beror på problemet och vilka symptom du observerar. Innan du går längre, utforska följande artiklar. De ger användbar felsökningsinformation:

- [Felsöka problem med programproxy och felmeddelanden](application-proxy-troubleshoot.md)
- [Kerberos-fel och -symptom](application-proxy-troubleshoot.md#kerberos-errors)
- [Arbeta med SSO när lokala och molnidentiteter inte är identiska](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Om du kom till denna punkt, då din huvudsakliga frågan finns. Du kan börja med att separera flödet i följande tre steg som du kan felsöka.

### <a name="client-pre-authentication"></a>Förautentisering av klienter

Den externa användaren autentiserar till Azure via en webbläsare. Möjligheten att förbe autentisera till Azure är nödvändig för att KCD SSO ska fungera. Testa och åtgärda den här möjligheten om det finns några problem. Fasen före autentisering är inte relaterad till KCD eller det publicerade programmet. Det är enkelt att korrigera eventuella avvikelser genom att kontrollera att ämneskontot finns i Azure. Kontrollera också att den inte är inaktiverad eller blockerad. Felsvaret i webbläsaren är tillräckligt beskrivande för att förklara orsaken. Om du är osäker kan du kontrollera andra felsökningsartiklar från Microsoft för att verifiera.

### <a name="delegation-service"></a>Delegeringstjänst

Azure Proxy-anslutningsappen som får en Kerberos-tjänstbiljett för användare från ett Kerberos Key Distribution Center (KCD).

Den externa kommunikationen mellan klienten och Azure-klienten har ingen betydelse för KCD. Dessa meddelanden ser bara till att KCD fungerar. Azure Proxy-tjänsten tillhandahålls ett giltigt användar-ID som används för att hämta en Kerberos-biljett. Utan detta ID är KCD inte möjligt och misslyckas.

Som tidigare nämnts, webbläsaren felmeddelanden ger några bra ledtrådar om varför saker misslyckas. Se till att anteckna aktivitets-ID och tidsstämpel i svaret. Den här informationen hjälper dig att korrelera beteendet till faktiska händelser i Azure Proxy-händelseloggen.

![Exempel: Felaktigt KCD-konfigurationsfel](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Motsvarande poster som visas i händelseloggen visas som händelser 13019 eller 12027. Hitta anslutningshändelseloggarna i **Program och tjänster Loggar** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Connector** &gt; **Admin**.

![Händelse 13019 från händelseloggen programproxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Händelse 12027 från händelseloggen programproxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Använd en **A-post** i din interna DNS för programmets adress, inte ett **CName**.
1. Bekräfta att anslutningsvärden har beviljats rätt att delegera till det angivna målkontots SPN. Bekräfta att **Använda alla autentiseringsprotokoll** är markerat. Mer information finns i [artikeln SSO-konfiguration](application-proxy-configure-single-sign-on-with-kcd.md).
1. Kontrollera att det bara finns en instans av SPN som finns i Azure AD. Problem `setspn -x` från en kommandotolk på en domänmedlemsvärd.
1. Kontrollera att en domänprincip tillämpas som begränsar den [maximala storleken på utfärdade Kerberos-token](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Den här principen hindrar kopplingen från att hämta en token om den visar sig vara överdriven.

En nätverksspårning som fångar utbyten mellan anslutningsvärden och en domän KDC är det näst bästa steget för att få mer lågnivådetalj om problemen. Mer information finns i [djupdykningspapperet](https://aka.ms/proxytshootpaper).

Om biljettförsäljningen ser bra ut visas en händelse i loggarna om att autentisering misslyckades eftersom programmet returnerade en 401. Den här händelsen indikerar att målansökan avvisade din biljett. Gå till nästa steg.

### <a name="target-application"></a>Målansökan

Konsumenten av Kerberos-biljetten som tillhandahålls av anslutningen. I det här skedet förväntar du dig att kopplingen ska ha skickat en Kerberos-tjänstbiljett till serverdelen. Den här biljetten är en rubrik i den första ansökningsbegäran.

1. Genom att använda programmets interna URL som definierats i portalen, verifiera att programmet är tillgängligt direkt från webbläsaren på anslutningsvärden. Sedan kan du logga in. Information finns på **felsöksidan** för kopplingen.
1. På anslutningsvärden bekräftar du att autentiseringen mellan webbläsaren och programmet använder Kerberos. Välj en av följande åtgärder:
1. Kör DevTools (**F12**) i Internet Explorer eller använd [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) från anslutningsvärden. Gå till programmet med hjälp av den interna URL:en. Kontrollera de erbjudna WWW-auktoriseringshuvudena som returneras i svaret från programmet för att se till att det finns antingen förhandla eller Kerberos.

   - Nästa Kerberos-blob som returneras i svaret från webbläsaren till programmet börjar med **YII**. De här breven säger att Kerberos är igång. Microsoft NT LAN Manager (NTLM), å andra sidan, börjar alltid med **TlRMTVNTUAAB**, som lyder NTLM Security Support Provider (NTLMSSP) när den avkodas från Base64. Om du ser **TlRMTVNTUAAB** i början av blobben är Kerberos inte tillgängligt. Om du inte ser **TlRMTVNTUAAB**är Kerberos troligen tillgängligt.

      > [!NOTE]
      > Om du använder Fiddler kräver den här metoden att du tillfälligt inaktiverar utökat skydd för programmets konfiguration i IIS.

      ![Fönstret För inspektion av webbläsarnätverk](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - Blobben i den här figuren börjar inte med **TIRMTVNTUAAB**. Så i det här exemplet är Kerberos tillgängligt och Kerberos-blobben börjar inte med **YII**.

1. Ta tillfälligt bort NTLM från providers-listan på IIS-webbplatsen. Öppna appen direkt från Internet Explorer på anslutningsvärden. NTLM finns inte längre med i listan över leverantörer. Du kan komma åt programmet endast med Kerberos. Om åtkomsten misslyckas kan det vara problem med programmets konfiguration. Kerberos-autentisering fungerar inte.

   - Om Kerberos inte är tillgängligt kontrollerar du programmets autentiseringsinställningar i IIS. Se till att **Negotiate** är listad högst upp, med NTLM precis under den. Om du ser **Inte förhandla,** **Kerberos eller Förhandla**eller **PKU2U**fortsätter du bara om Kerberos fungerar.

     ![Windows-autentiseringsleverantörer](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Med Kerberos och NTLM på plats inaktiverar du tillfälligt förautentisering för programmet i portalen. Försök att komma åt den från Internet med hjälp av den externa webbadressen. Du uppmanas att autentisera. Du kan göra det med samma konto som användes i föregående steg. Om inte, det finns ett problem med backend-programmet, inte KCD.
   - Aktivera förautentisering i portalen igen. Autentisera via Azure genom att försöka ansluta till programmet via dess externa URL. Om SSO misslyckas visas ett förbjudet felmeddelande i webbläsaren och händelse 13022 i loggen:

     *Det går inte att autentisera användaren i Microsoft AAD Application Proxy Connector eftersom serverdservern svarar på Kerberos-autentiseringsförsök med ett HTTP 401-fel.*

      ![Visar HTTTP 401 förbjudet fel](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Kontrollera IIS-programmet. Kontrollera att den konfigurerade programpoolen och SPN är konfigurerade för att använda samma konto i Azure AD. Navigera i IIS enligt följande bild:

      ![Konfigurationsfönster för IIS-program](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      När du känner till identiteten kontrollerar du att det här kontot är konfigurerat med SPN i fråga. Ett exempel är `setspn –q http/spn.wacketywack.com`. Ange följande text i en kommandotolk:

      ![Visar kommandofönstret SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Kontrollera det SPN som definierats mot programmets inställningar i portalen. Kontrollera att samma SPN som konfigurerats mot målet Azure AD-konto används av programmets apppool.

      ![SPN-konfiguration i Azure-portalen](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Gå till IIS och välj alternativet **Configuration Editor** för programmet. Navigera till **system.webServer/security/authentication/windowsAuthentication**. Kontrollera att värdet **UseAppPoolCredentials** är **sant**.

      ![Autentiseringsalternativ för IIS-konfigurationsapppooler](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Ändra det här värdet till **Sant**. Ta bort alla cachelagrade Kerberos-biljetter från serverdelsservern genom att köra följande kommando:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Mer information finns i [Rensa Kerberos-klientbiljettcachen för alla sessioner](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).

Om du låter Kernel-läget vara aktiverat förbättrar det prestandan för Kerberos-åtgärder. Men det gör också att biljetten för den begärda tjänsten dekrypteras med hjälp av maskinkontot. Det här kontot kallas också det lokala systemet. Ange det här värdet till **True** för att bryta KCD när programmet finns på mer än en server i en server.

- Som en extra kontroll, inaktivera **utökat** skydd också. I vissa fall bröt **utökat** skydd KCD när det aktiverades i specifika konfigurationer. I dessa fall publicerades ett program som en undermapp till standardwebbplatsen. Det här programmet är endast konfigurerat för anonym autentisering. Alla dialogrutor är nedtonade, vilket tyder på att underordnade objekt inte ärver några aktiva inställningar. Vi rekommenderar att du testar, men glöm inte att återställa det här värdet till **aktiverat**, där det är möjligt.

  Den här ytterligare kontrollen gör att du är på rätt spår för att använda det publicerade programmet. Du kan skapa ytterligare kopplingar som också är konfigurerade för att delegera. Mer information finns i den mer djupgående tekniska [genomsökningen, Felsökning av Azure AD Application Proxy](https://aka.ms/proxytshootpaper).

Om du fortfarande inte kan göra framsteg kan Microsoft-supporten hjälpa dig. Skapa en supportbiljett direkt i portalen. En ingenjör kommer att kontakta dig.

## <a name="other-scenarios"></a>Andra scenarier

- Azure Application Proxy begär en Kerberos-biljett innan den skickas till ett program. Vissa program från tredje part gillar inte den här metoden för autentisering. Dessa ansökningar förväntar sig att de mer konventionella förhandlingarna kommer att äga rum. Den första begäran är anonym, vilket gör att programmet kan svara med de autentiseringstyper som den stöder via en 401.
- Multi-hop-autentisering används ofta i scenarier där ett program är nivåinriktat, med en serverdel och klientdel, där båda kräver autentisering, till exempel SQL Server Reporting Services. Om du vill konfigurera scenariot med flera hopp läser du stödartikeln [Kerberos Constrained Delegation May Require Protocol Transition in Multi-hop Scenarios](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Nästa steg

[Konfigurera KCD på en hanterad domän](../../active-directory-domain-services/deploy-kcd.md).
