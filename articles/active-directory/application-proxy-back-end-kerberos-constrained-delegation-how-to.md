---
title: "Felsökning av Kerberos-begränsad delegering konfigurationer för Application Proxy | Microsoft Docs"
description: "Felsökning av Kerberos-begränsad delegering konfigurationer för Application Proxy."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 7b31f53e14e3f9a175e5dda95a18eb89dbca99dc
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Felsökning av Kerberos-begränsad delegering konfigurationer för Application Proxy

Metoderna för att uppnå SSO till publicerade program kan variera något från ett program till en annan. En av de alternativ som Azure Application Proxy ger som standard är Kerberos-begränsad delegering (KCD). Du kan konfigurera en koppling för att utföra begränsad Kerberos-autentisering till backend-program för användare.

Den faktiska proceduren för att aktivera KCD är relativt enkla och kräver normalt inte fler än en förståelse av de olika komponenter och autentiseringsflödet som underlättar enkel inloggning. Hitta bra informationskällor för att felsöka scenarier där KCD SSO inte fungerar som förväntat, kan vara null-optimerad.

Därför försöker den här artikeln ger en enda åtkomstpunkt för referens som bör underlätta felsökning och automatisk reparera några av de vanligaste problem som visas. Erbjuder ytterligare vägledning för att diagnostisera komplexa och skadade genomförande på samma gång.

Den här artikeln gör följande antaganden:

-   Distributionen av Azure Application Proxy per [dokumentationen](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) och allmän åtkomst till icke KCD program fungerar som förväntat.

-   Publicerade målprogrammet baseras på IIS och Microsofts implementering av Kerberos.

-   Server- och värdar placeras i en enda Active Directory-domän. Detaljerad information om mellan domäner och skogar scenarier kan hittas i den [KCD whitepaper](http://aka.ms/KCDPaper).

-   Programmet har publicerats i en Azure-klient med förautentisering aktiverade och användare förväntas att autentisera till Azure via formulärbaserad autentisering. Rich-klient autentiseringsscenarier omfattas inte av den här artikeln, men läggas på någon punkt i framtiden.

## <a name="prerequisites"></a>Förutsättningar

Azure Application Proxy kan distribueras till många typer av infrastrukturer eller miljöer och arkitekturerna säkert varierar mellan olika företag. En av de vanligaste orsakerna till KCD-relaterade problem är inte miljöer, men ganska enkelt att konfigurationer eller allmänna tillsyn.

Därför är det alltid bäst att starta genom att kontrollera att du har uppfyllt alla krav som anges i [med KCD enkel inloggning med Application Proxy-artikel](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) innan du startar felsökning.

Särskilt avsnittet om hur du konfigurerar KCD på 2012R2, eftersom det använder ett helt olika sätt att konfigurera KCD på tidigare versioner av Windows, utan även när du är uppmärksam på flera saker:

-   Det är inte ovanligt att en domänmedlemsserver att öppna en dialogruta med säker kanal med en specifik domänkontrollant. Flytta sedan till en annan dialogruta samtidigt, så connector värdar inte bör begränsas till att kunna kommunicera med endast specifika lokala plats domänkontrollanter.

-   Liknar den föregående punkten över domäner scenarier som förlitar sig på referenser som en värd för anslutningen till domänkontrollanter som finns utanför nätverket perimeternätverket. I det här fallet är det lika viktigt att se till att du även tillåter trafik och senare till domänkontrollanter som representerar andra respektive domäner eller annan delegering misslyckas.

-   Om möjligt bör bör du inte släpps alla aktiva IP-adresser/ID: N mellan värddatorer för anslutningen och domänkontrollanter, eftersom dessa ibland över påträngande och störa core RPC-trafik

Det rekommenderas att testa delegering i den enklaste av scenarier. Flera variabler du införa, Ju mer du kan behöva tävla med. Till exempel begränsa testningen till en enskild koppling kan spara värdefull tid och ytterligare kopplingar kan läggas till efter problemen har lösts.

Vissa miljöfaktorer kan även bidra till ett ärende. Enklare att arkitekturen minsta att undvika dessa miljöfaktorer under testningen. Till exempel felkonfigurerad intern brandvägg ACL: er är inte ovanliga, om möjligt har all trafik från en koppling får rakt igenom domänkontrollanter och backend-programmet. 

Det absoluta enklast att placera kopplingar är faktiskt så nära sina mål kan vara. Med en brandvägg lö infogad medan testning bara lägger till onödiga komplexitet och kan bara förlänga din undersökningar.

Så, vad som utgör en KCD problemet ändå? Det finns flera klassiska indikationer på KCD SSO misslyckas och de första tecknen på ett problem manifest vanligtvis sig själva i webbläsaren.

   ![Felaktig KCD-konfigurationsfel](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![Auktoriseringen misslyckades på grund av saknade behörigheter](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

alla de bär samma symtom på att utföra en enkel inloggning inte och därför neka användaråtkomst till programmet.

## <a name="troubleshooting"></a>Felsökning

Hur du sedan felsöka beror på problemet och observeras symptom. Utforska följande länkar innan skickas vidare, eftersom de innehåller användbar information som du inte får ännu har komma över:

-   [Felsökning av problem med Application Proxy och felmeddelanden](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)

-   [Kerberos-fel och problem](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#kerberos-errors)

-   [Arbeta med enkel inloggning när lokalt och i molnet identiteter är inte identiska](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd#working-with-sso-when-on-premises-and-cloud-identities-are-not-identical)

Om du har den här långt, sedan viktigaste problemet definitivt finns. Starta genom att avgränsa flödet in i tre olika steg som du kan felsöka.

**Förautentisering av klient** – den externa användaren autentiserar till Azure via en webbläsare.

Att kunna autentisera före till Azure är absolut nödvändigt för KCD SSO till funktionen. Detta bör testas och åtgärdas först om det uppstår några problem. Mellanlagring av förautentisering har ingen relation till KCD eller publicerade program. Det bör vara ganska enkelt att korrigera skillnader genom förstånd kontrollerar ämne kontot finns i Azure och att det har inaktiverats/inte blockeras. Felsvar i webbläsaren är vanligtvis tillräckligt beskrivande att förstå orsaken. Du kan också kontrollera vår andra felsöka dokumentation för att kontrollera om du är osäker.

**Delegering service** – Azure Proxy-anslutningen att få en Kerberos-tjänstbiljett från en KDC (Kerberos Distribution Center) åt användare.

Extern kommunikation mellan klienten och Azure klientdelen bör har ingen betydelse KCD över huvud taget, andra än att säkerställa att det fungerar. Detta är så Azure Proxy-tjänst kan tillhandahållas med ett giltigt användar-ID som används för att få en Kerberos-biljett. Utan detta, KCD är inte möjligt och skulle misslyckas.

Som tidigare nämnts innehåller felmeddelanden webbläsare normalt vissa tecken på varför saker inte. Se till att notera aktivitets-ID och tidsstämpel i svaret som du kan korrelera funktionssätt faktiska händelser i händelseloggen Azure Proxy.

   ![Felaktig KCD-konfigurationsfel](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Och motsvarande poster visas i händelseloggen skulle ses som händelser 13019 eller 12027. Du kan hitta connector händelseloggarna i **program- och tjänstloggar** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Connector**&gt;**Admin**.

   ![Händelsen 13019 från Application Proxy-händelseloggen](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![Händelsen 12027 från Application Proxy-händelseloggen](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

-   Använd en A-post i din interna DNS för programmets adress och inte en CNAME-post

-   Bekräfta att kopplingen värden har beviljats behörighet att delegera till den avsedda målkonto SPN och **Använd valfritt autentiseringsprotokoll** är markerad. Mer information om det här avsnittet finns [SSO configuration artikel](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)

-   Kontrollera att det finns bara en instans av SPN finns i AD genom att utfärda en `setspn -x` från en kommandotolk på alla värddatorer för medlem av domänen

-   Kontrollera om en domänprincip tillämpas för att begränsa den [max storlek på utfärdade token för Kerberos](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/)eftersom detta förhindrar anslutningen från att erhålla ett token om att hitta överdriven

En spårning i nätverket som avbildar utbyten mellan connector-värd och en domän KDC blir bästa nästa steg att få låg nivå detalj på problemen. Mer information finns [ingående felsöka papper](https://aka.ms/proxytshootpaper).

Du bör se en händelse i loggarna om att autentiseringen misslyckades på grund av det program som returnerar ett 401 om biljetter ser bra ut. Detta tyder oftast som målprogrammet avvisa din biljett, så gå vidare med följande nästa steg.

**Målprogrammet** -konsumenter av Kerberos-biljetten som tillhandahålls av kopplingen

I det här skedet kopplingen förväntas har skickat en Kerberos-tjänsten biljetten serverdelen, som en rubrik i den första programbegäran.

-   Med hjälp av programmets Intern URL som definierats i portalen, verifiera att programmet är tillgänglig direkt från webbläsaren på värden för anslutningen. Du kan logga sedan in korrekt. Information om hur du gör detta finns på sidan connector felsökning.

-   Bekräfta att autentiseringen mellan webbläsaren och programmet använder Kerberos, genom att göra något av följande fortfarande på connector-värden:

1.  Köra verktyg för utveckling (**F12**) i Internet Explorer eller Använd [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) från connector-värden. Gå till det program som använder intern URL och inspektera de erbjudna WWW auktorisering huvuden som returnerades i svaret från programmet, så som antingen förhandla eller Kerberos finns. En efterföljande Kerberos-blob som returneras i svaret från webbläsaren till programmet normalt börjar med **YII**, så det här är en bra indikation på Kerberos som i play. NTLM å andra sidan börjar alltid med **TlRMTVNTUAAB**, som läser NTLMSSP när avkoda från Base64. Om du ser **TlRMTVNTUAAB** i början av blob, det innebär att Kerberos **inte** tillgängliga. Om du inte ser detta Kerberos sannolikt tillgängliga.
    > [!NOTE]
    > Om du använder Fiddler, kräver den här metoden tillfälligt inaktivera utökat skydd på programmets konfiguration i IIS.

     ![Webbläsarfönster Nätverkskontroll](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    *Bild:* eftersom det inte börjar med TIRMTVNTUAAB, detta är ett exempel som att Kerberos är tillgänglig. Detta är ett exempel på en Kerberos-Blob som inte börjar med YII.

2.  Ta bort NTLM tillfälligt från listan med providers på IIS-webbplats och åtkomst app direkt från Internet Explorer på värden för anslutningen. Du ska kunna komma åt programmet med hjälp av Kerberos endast med NTLM inte längre i listan med providers. Om detta misslyckas sedan som tyder på att det finns ett problem med programmets konfiguration och Kerberos-autentisering fungerar inte.

Om Kerberos inte finns, är Kontrollera programmets autentiseringsinställningarna i IIS för att se till att förhandla listas översta med NTLM under den. (Inte förhandla: kerberos eller Negotiate: PKU2U). Fortsätt bara om Kerberos fungerar.

   ![Windows autentiseringsproviders](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
-   Med Kerberos- och NTLM på plats kan du tillfälligt inaktivera nu förautentisering för programmet i portalen. Se om du kan komma åt den från internet med hjälp av den externa Webbadressen. Du ska bli ombedd att autentisera och ska kunna göra det med samma konto som används i föregående steg. Om inte det här tyder på problem med backend-programmet och inte KCD alls.

-   Nu återaktivera förautentisering i portalen och autentisering via Azure genom att försöka ansluta till programmet via den externa URL: en. Om enkel inloggning har misslyckats bör du se felmeddelandet otillåtna i webbläsaren, samt händelsen 13022 i loggen:

    *Microsoft AAD Application Proxy Connector autentisera inte användaren eftersom backend-servern svarar på Kerberos autentiseringsförsök med ett HTTP 401-fel.*

    ![HTTTP 401 förbjudet fel](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

-   Kontrollera IIS-program så konfigurerade programpoolen är konfigurerad för att använda samma konto som SPN har konfigurerats mot i AD, genom att navigera i IIS som i följande bild

    ![IIS-konfigurationsfönstret](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

    När du känner till identiteten konfigureras definitivt problemet följande från en kommandotolk för att se till att det här kontot med SPN i fråga. Till exempel`setspn –q http/spn.wacketywack.com`

    ![SetSPN-kommandofönster](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

-   Kontrollera att SPN definieras programinställningar i portalen är samma SPN konfigurerats mot AD-målkontot som används av programpoolen app

   ![SPN-konfigurationen i Azure Portal](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
-   Gå till IIS och välj den **Konfigurationsredigeraren** för programmet och gå till **system.webServer/security/authentication/windowsAuthentication** att kontrollera att värdet **UseAppPoolCredentials** är **SANT**

   ![IIS-konfiguration app pooler autentiseringsuppgifter alternativet](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

När är användbart i bättre prestanda för Kerberos-åtgärder, lämnar Kernel-läge har aktiverats även gör biljett för den begärda tjänsten som ska dekrypteras med hjälp av datorkontot. Detta kallas även det lokala systemet så har det angetts till true break KCD när programmet ligger över flera servrar i en grupp.

-   Som en ytterligare kontroll du även vill inaktivera den **utökad** skydd för. Det har förekommit påträffade scenarier där detta har visat för att bryta KCD när aktiverat i specifika konfigurationer där ett program publiceras som en undermapp för standardwebbplatsen. Den här själva har konfigurerats för anonym autentisering, lämnar hela dialogrutorna nedtonad tyder på underordnade objekt inte kan ärva active inställningar. Men där möjliga alltid rekommenderar vi med alternativet är aktiverat, så av alla menat testa, men glöm inte att återställa den till aktiverad.

Dessa ytterligare kontroller bör ha spärra du spåra att börja använda ditt publicerade program. Du kan gå vidare och få igång ytterligare kopplingar som också har konfigurerats för att delegera, men om saker är ingen ytterligare sedan skulle föreslår vi en läsning av våra mer ingående tekniska genomgången [fullständig guide för felsökning av Azure AD Application Proxy](https://aka.ms/proxytshootpaper)

Om du är fortfarande inte vidare problemet gärna stöd mer än hjälpa och fortsätta härifrån. Skapa ett supportärende direkt i portalen (en ingenjör att nå ut till dig).

## <a name="other-scenarios"></a>Andra scenarier

-   Azure Application Proxy begär en Kerberos-biljett innan du skickar en begäran till ett program. Vissa program från tredje part, till exempel Tableau Server som inte den här metoden för att autentisera och snarare förväntar sig mer konventionella förhandlingarna ska kunna utföras. Den första begäranden är anonyma, så att programmet att svara med autentiseringstyper stöder via ett 401.

-   Dubbla hopp autentisering - ofta används i scenarier där ett program nivåer, med en serverdelen och front end båda kräver autentisering, till exempel SQL Reporting Services.

## <a name="next-steps"></a>Nästa steg
[Konfigurera kerberos-begränsad delegering (KCD) på en hanterad domän](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-enable-kcd)
