---
title: Förstå Azure AD-programproxyanslutningar | Microsoft-dokument
description: Täcker grunderna om Azure AD Application Proxy-kopplingar.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: b097ce3781a77a8c5e8a94b9c2bf0977f3efcfd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481338"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Förstå Azure AD-programproxy-kopplingar

Kopplingar är det som gör Azure AD Application Proxy möjligt. De är enkla, lätta att distribuera och underhålla och superkraftskraft. I den här artikeln beskrivs vilka kopplingar som är, hur de fungerar och några förslag på hur du optimerar distributionen.

## <a name="what-is-an-application-proxy-connector"></a>Vad är en Application Proxy-anslutning?

Kopplingar är lätta agenter som sitter lokalt och underlättar den utgående anslutningen till application proxy-tjänsten. Kopplingar måste vara installerade på en Windows Server som har åtkomst till serverdprogrammet. Du kan ordna kopplingar i anslutningsgrupper, där varje grupp hanterar trafik till specifika program.

## <a name="requirements-and-deployment"></a>Krav och distribution

Om du vill distribuera Programproxy behöver du minst en anslutningsapp, men vi rekommenderar två eller fler för större återhämtning. Installera anslutningen på en dator som kör Windows Server 2012 R2 eller senare. Anslutningsappen måste kommunicera med tjänsten Programproxy och de lokala program som du publicerar.

### <a name="windows-server"></a>Windows-server
Du behöver en server som kör Windows Server 2012 R2 eller senare där du kan installera Application Proxy-anslutningen. Servern måste ansluta till Application Proxy-tjänsterna i Azure och de lokala program som du publicerar.

Windows-servern måste ha TLS 1.2 aktiverat innan du installerar Application Proxy-anslutningen. Så här aktiverar du TLS 1.2 på servern:

1. Ange följande registernycklar:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Starta om servern

Mer information om nätverkskraven för anslutningsservern finns i [Komma igång med Programproxy och installera en anslutning](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Underhåll

Kontakterna och tjänsten tar hand om alla uppgifter med hög tillgänglighet. De kan läggas till eller tas bort dynamiskt. Varje gång en ny begäran anländer dirigeras den till en av de kopplingar som för närvarande är tillgänglig. Om en anslutningsapp inte är tillgänglig för tillfället svarar den inte på den här trafiken.

Kopplingarna är tillståndslösa och har inga konfigurationsdata på datorn. Den enda data de lagrar är inställningarna för att ansluta tjänsten och dess autentiseringscertifikat. När de ansluter till tjänsten hämtar de alla nödvändiga konfigurationsdata och uppdaterar den varannan minut.

Kopplingar avsöker också servern för att ta reda på om det finns en nyare version av anslutningen. Om en sådan hittas uppdateras anslutningarna själva.

Du kan övervaka dina kontakter från den dator de körs på, antingen med hjälp av händelseloggen och prestandaräknarna. Du kan också visa deras status från sidan Programproxy i Azure-portalen:

![Exempel: Azure AD Application Proxy-kopplingar](./media/application-proxy-connectors/app-proxy-connectors.png)

Du behöver inte ta bort kopplingar som inte används manuellt. När en anslutning körs förblir den aktiv när den ansluter till tjänsten. Oanvända kopplingar taggas som _inaktiva_ och tas bort efter 10 dagars inaktivitet. Om du vill avinstallera en anslutningsapp avinstallerar du dock både Anslutningstjänsten och Updater-tjänsten från servern. Ta bort tjänsten helt och hållet genom att starta om datorn.

## <a name="automatic-updates"></a>Automatiska uppdateringar

Azure AD tillhandahåller automatiska uppdateringar för alla kopplingar som du distribuerar. Så länge tjänsten Application Proxy Connector Updater körs uppdateras anslutningsapparna automatiskt. Om du inte ser tjänsten Anslutningsuppdatering på servern måste du [installera om kopplingen](application-proxy-add-on-premises-application.md) för att få uppdateringar.

Om du inte vill vänta på att en automatisk uppdatering ska komma till din kontakt kan du göra en manuell uppgradering. Gå till [hämtningssidan för anslutningsappen](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) på servern där anslutningen finns och välj **Hämta**. Den här processen startar en uppgradering för den lokala anslutningen.

För klienter med flera kopplingar riktar sig automatiska uppdateringar mot en koppling i taget i varje grupp för att förhindra driftstopp i din miljö.

Det kan uppstå driftstopp när anslutningen uppdateras om:
  
- Du har bara en koppling som vi rekommenderar att du installerar en andra koppling och [skapar en kopplingsgrupp](application-proxy-connector-groups.md). Detta kommer att undvika driftstopp och ge högre tillgänglighet.  
- En koppling var mitt i en transaktion när uppdateringen startade. Även om den ursprungliga transaktionen går förlorad, bör din webbläsare automatiskt försöka åtgärden eller så kan du uppdatera din sida. När begäran skickas skickas skickas dirigeras trafiken till en anslutningsapp.

Information om tidigare utgivna versioner och vilka ändringar de innehåller finns i [Application Proxy- Versionsversionshistorik](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Skapa kopplingsgrupper

Med anslutningsgrupper kan du tilldela specifika kopplingar för att betjäna specifika program. Du kan gruppera ett antal kopplingar tillsammans och sedan tilldela varje program till en grupp.

Anslutningsgrupper gör det enklare att hantera stora distributioner. De förbättrar också svarstiden för klienter som har program som finns i olika regioner, eftersom du kan skapa platsbaserade anslutningsgrupper för att endast betjäna lokala program.

Mer information om anslutningsgrupper finns i [Publicera program i separata nätverk och platser med hjälp av anslutningsgrupper](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Kapacitetsplanering

Det är viktigt att se till att du har planerat tillräckligt med kapacitet mellan kopplingar för att hantera den förväntade trafikvolymen. Vi rekommenderar att varje kopplingsgrupp har minst två kopplingar för att ge hög tillgänglighet och skala. Att ha tre kontakter är optimalt om du kan behöva serva en maskin när som helst.

I allmänhet, ju fler användare du har, desto större en maskin behöver du. Nedan finns en tabell som ger en översikt över volymen och förväntad latens som olika datorer kan hantera. Observera att allt är baserat på förväntade transaktioner per sekund (TPS) snarare än av användaren eftersom användningsmönstren varierar och inte kan användas för att förutsäga belastning. Det kommer också att finnas vissa skillnader baserat på storleken på svaren och backend ansökan svarstid - större svarsstorlekar och långsammare svarstider kommer att resultera i en lägre Max TPS. Vi rekommenderar också att du har ytterligare maskiner så att den distribuerade belastningen över maskinerna alltid ger gott om buffert. Den extra kapaciteten säkerställer att du har hög tillgänglighet och återhämtning.

|Kärnor|RAM|Förväntad svarstid (MS)-P99|Max TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\*Den här datorn använde en anpassad inställning för att höja några av standardanslutningsgränserna utöver .NET-rekommenderade inställningar. Vi rekommenderar att du kör ett test med standardinställningarna innan du kontaktar supporten för att få den här gränsen ändrad för din klient.

> [!NOTE]
> Det är inte så stor skillnad i den maximala TPS mellan 4, 8 och 16 kärnmaskiner. Den största skillnaden mellan dessa är i den förväntade svarstiden.
>
> Den här tabellen fokuserar också på den förväntade prestandan för en anslutning baserat på vilken typ av dator den är installerad på. Detta är skilt från programproxytjänstens begränsningsgränser, se [Tjänstbegränsningar och begränsningar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="security-and-networking"></a>Säkerhet och nätverk

Anslutningar kan installeras var som helst i nätverket som gör att de kan skicka begäranden till tjänsten Application Proxy. Det viktiga är att datorn som kör anslutningsappen också har åtkomst till dina appar. Du kan installera kopplingar i företagets nätverk eller på en virtuell dator som körs i molnet. Anslutningar kan köras i ett perimeternätverk, även känd som en demilitariserad zon (DMZ), men det är inte nödvändigt eftersom all trafik är utgående så att nätverket förblir säkert.

Anslutningsappar skickar bara utgående begäranden. Den utgående trafiken skickas till tjänsten Application Proxy och till de publicerade programmen. Du behöver inte öppna inkommande portar eftersom trafiken flödar åt båda hållen när en session har upprättats. Du behöver inte heller konfigurera inkommande åtkomst via brandväggarna.

Mer information om hur du konfigurerar regler för utgående brandväggar finns i [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Prestanda och skalbarhet

Skalan för application proxy-tjänsten är transparent, men skala är en faktor för kopplingar. Du måste ha tillräckligt med kontakter för att hantera topptrafik. Eftersom kopplingar är tillståndslösa påverkas de inte av antalet användare eller sessioner. I stället svarar de på antalet förfrågningar och deras nyttolaststorlek. Med vanlig webbtrafik kan en genomsnittlig maskin hantera ett par tusen begäranden per sekund. Den specifika kapaciteten beror på de exakta maskinegenskaperna.

Anslutningsprestandan är bunden av CPU och nätverk. CPU-prestanda behövs för TLS-kryptering och dekryptering, medan nätverk är viktigt för att få snabb anslutning till programmen och onlinetjänsten i Azure.

Däremot är minnet mindre av ett problem för kopplingar. Online-tjänsten tar hand om mycket av behandlingen och all oautentiserade trafik. Allt som kan göras i molnet görs i molnet.

Om kontakten eller datorn av någon anledning blir otillgänglig börjar trafiken gå till en annan koppling i gruppen. Den här återhämtningen är också anledningen till att vi rekommenderar att ha flera kopplingar.

En annan faktor som påverkar prestanda är kvaliteten på nätverken mellan anslutningarna, inklusive:

- **Onlinetjänsten**: Långsamma eller hög latensanslutningar till application proxy-tjänsten i Azure påverkar anslutningsprestanda. För bästa prestanda kan du ansluta din organisation till Azure med Express Route. Annars har ditt nätverksteam se till att anslutningar till Azure hanteras så effektivt som möjligt.
- **Backend-programmen**: I vissa fall finns det ytterligare proxyservrar mellan anslutningen och backend-programmen som kan sakta ned eller förhindra anslutningar. Om du vill felsöka det här scenariot öppnar du en webbläsare från anslutningsservern och försöker komma åt programmet. Om du kör anslutningsapparna i Azure men programmen är lokala kanske upplevelsen inte är vad användarna förväntar sig.
- **Domänkontrollanterna**: Om kopplingarna utför enkel inloggning (SSO) med Kerberos Constrained Delegation kontaktar de domänkontrollanterna innan de skickar begäran till backend. Kopplingarna har en cache med Kerberos-biljetter, men i en upptagen miljö kan domänkontrollanternas svarstider påverka prestanda. Det här problemet är vanligare för kopplingar som körs i Azure men kommunicerar med domänkontrollanter som är lokala.

Mer information om hur du optimerar nätverket finns i [Nätverkstopologiöverväganden när du använder Azure Active Directory Application Proxy](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Domän anslutande

Kopplingar kan köras på en dator som inte är domänansluten. Men om du vill ha enkel inloggning (SSO) till program som använder integrerad Windows-autentisering (IWA), behöver du en domän-ansluten dator. I det här fallet måste anslutningsdatorerna vara kopplade till en domän som kan utföra [Kerberos](https://web.mit.edu/kerberos) ConstrainedDelegering för användarna för de publicerade programmen.

Kopplingar kan också anslutas till domäner eller skogar som har ett partiellt förtroende eller till skrivskyddade domänkontrollanter.

## <a name="connector-deployments-on-hardened-environments"></a>Kopplingsdistributioner i härdade miljöer

Vanligtvis är anslutningsdistributionen enkel och kräver ingen särskild konfiguration. Det finns dock några unika villkor som bör beaktas:

- Organisationer som begränsar den utgående trafiken måste [öppna obligatoriska portar](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- FIPS-kompatibla datorer kan krävas för att ändra konfigurationen så att anslutningsprocesserna kan generera och lagra ett certifikat.
- Organisationer som låser sin miljö baserat på de processer som utfärdar nätverksbegäranden måste se till att båda anslutningstjänsterna är aktiverade för att komma åt alla nödvändiga portar och IPs.
- I vissa fall kan utgående framåt proxyservrar bryta tvåvägscertifikatautentisering och orsaka att kommunikationen misslyckas.

## <a name="connector-authentication"></a>Autentisering av anslutning

För att tillhandahålla en säker tjänst måste kopplingar autentisera mot tjänsten och tjänsten måste autentisera mot kopplingen. Den här autentiseringen görs med klient- och servercertifikat när anslutningarna initierar anslutningen. På så sätt lagras inte administratörens användarnamn och lösenord på anslutningsdatorn.

De certifikat som används är specifika för tjänsten Programproxy. De skapas under den första registreringen och förnyas automatiskt av kontakterna varannan månad.

Om en anslutning inte är ansluten till tjänsten på flera månader kan dess certifikat vara inaktuella. I det här fallet avinstallerar och installerar du om kopplingen för att utlösa registrering. Du kan köra följande PowerShell-kommandon:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Under huven

Kopplingar baseras på Windows Server Web Application Proxy, så de har de flesta av samma hanteringsverktyg, inklusive Windows-händelseloggar

![Hantera händelseloggar med Loggboken](./media/application-proxy-connectors/event-view-window.png)

och Windows-prestandaräknare.

![Lägga till räknare i kontakten med prestandaövervakaren](./media/application-proxy-connectors/performance-monitor.png)

Kontakterna har både administratörs- och sessionsloggar. Administratörsloggarna innehåller viktiga händelser och deras fel. Sessionsloggarna innehåller alla transaktioner och deras bearbetningsinformation.

Om du vill se loggarna går du till Loggboken, öppnar **Visa-menyn** och aktiverar **Visa analytiska och felsöka loggar**. Gör det möjligt för dem att börja samla in händelser. Dessa loggar visas inte i webbprogramproxy i Windows Server 2012 R2, eftersom kopplingarna baseras på en nyare version.

Du kan undersöka tjänstens tillstånd i fönstret Tjänster. Anslutningsappen består av två Windows Services: den faktiska anslutningen och updater. Båda måste springa hela tiden.

 ![Exempel: Tjänstefönster som visar Azure AD-tjänster lokala](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Nästa steg

- [Publicera program i separata nätverk och platser med hjälp av anslutningsgrupper](application-proxy-connector-groups.md)
- [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)
- [Felsöka programproxy och anslutningsfel](application-proxy-troubleshoot.md)
- [Så här installerar du Azure AD Application Proxy Connector tyst](application-proxy-register-connector-powershell.md)
