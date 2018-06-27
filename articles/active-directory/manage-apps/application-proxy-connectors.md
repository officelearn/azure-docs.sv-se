---
title: Förstå Azure AD Application Proxy kopplingar | Microsoft Docs
description: Beskriver grunderna om Azure AD Application Proxy-kopplingar.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 74e6428cf0536a7c8016be6cdf29071128bf4a3b
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025973"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Förstå Azure AD Application Proxy-kopplingar

Kopplingar är vad gör att Azure AD Application Proxy är möjligt. De är enkla lätt att distribuera och underhålla och super kraftfulla. Den här artikeln beskriver vilka kopplingar är hur de fungerar och några förslag på hur du optimerar distributionen. 

## <a name="what-is-an-application-proxy-connector"></a>Vad är en Application Proxy connector?

Kopplingar är förenklad agenter som sitter lokalt och underlätta utgående anslutning till tjänsten Application Proxy. Kopplingar måste installeras på en Windows-Server som har åtkomst till backend-programmet. Du kan sortera kopplingar i kopplingen grupper med varje grupp som hanterar trafik till specifika program. Kopplingar belastningen automatiskt, och kan hjälpa dig för att optimera nätverksinfrastrukturen. 

## <a name="requirements-and-deployment"></a>Krav och distribution

Om du vill distribuera Application Proxy har du behöver minst en koppling, men vi rekommenderar två eller fler för större flexibilitet. Installera anslutningen på en Windows Server 2012 R2 eller 2016-dator. Anslutningen måste kunna kommunicera med tjänsten Application Proxy samt lokala program som du publicerar. 

Mer information om nätverkskraven för connector-servern finns [komma igång med Application Proxy och installera en koppling](application-proxy-enable.md).

## <a name="maintenance"></a>Underhåll
Kopplingar och tjänsten tar hand om alla aktiviteter för hög tillgänglighet. De kan läggas till eller tas bort dynamiskt. Varje gång en ny begäran kommer dirigeras till en av de kopplingar som är tillgänglig. Om en koppling inte är tillgänglig, kan den inte svara på den här trafiken.

Kopplingarna är tillståndslösa och har några konfigurationsdata på datorn. De enda data som lagras är inställningarna för att ansluta tjänsten och dess certifikat för serverautentisering. När de ansluter till tjänsten hämtar alla konfigurationsdata som krävs och uppdatera den varje par minuter.

Kopplingar avsöka också servern för att ta reda på om det finns en nyare version av kopplingen. Om en sådan finns, uppdateras kopplingar.

Du kan övervaka dina kopplingar från den dator som körs på, med hjälp av du händelseloggen och prestandaräknare. Eller så kan du visa deras status från sidan Application Proxy på Azure-portalen:

 ![AzureAD Application Proxy kopplingar](./media/application-proxy-connectors/app-proxy-connectors.png)

Du behöver inte manuellt ta bort kopplingar som inte används. När du kör en koppling förblir den aktiv när den ansluter till tjänsten. Oanvända anslutningar märks _inaktiva_ och tas bort efter 10 dagar av inaktivitet. Om du vill avinstallera en koppling, men avinstallera både anslutningstjänsten och uppdateringstjänsten från servern. Ta bort tjänsten helt och hållet genom att starta om datorn.

## <a name="automatic-updates"></a>Automatiska uppdateringar

Azure AD innehåller automatiska uppdateringar för alla kopplingar som du distribuerar. Kopplingar uppdateras automatiskt så länge som Application Proxy Connector Updater-tjänsten körs. Om du inte ser Connector Updater-tjänsten på servern, behöver du [installerar om din anslutning](application-proxy-enable.md) att hämta uppdateringar. 

Om du inte vill vänta på en automatisk uppdatering att gå till din koppling, kan du utföra en manuell uppgradering. Gå till den [connector hämtningssidan](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) på den server där din koppling finns och välj **hämta**. Den här processen som aktiveras av en uppgradering för den lokala anslutningen. 

För klienter med flera kopplingar mål automatiska uppdateringar en anslutning i taget i varje grupp för att förhindra avbrott i din miljö. 

Driftstopp uppstår när din anslutningstjänst uppdateringar om:  
- Du kan bara ha en anslutning. Om du vill undvika det här driftstopp och förbättra hög tillgänglighet rekommenderar vi du installerar ytterligare en koppling och [skapar du en koppling grupp](application-proxy-connector-groups.md).  
- En koppling var mitt i en transaktion när uppdateringen påbörjades. Även om den första transaktionen har gått förlorade måste webbläsaren ska automatiskt försök igen eller så kan du uppdatera sidan. När begäran skickas igen dirigeras trafiken till en säkerhetskopiering koppling.

## <a name="creating-connector-groups"></a>Skapa grupper för anslutningstjänsten

Kopplingen grupper kan du tilldela specifika kopplingar för att hantera specifika program. Du kan gruppera ett antal kopplingar och sedan tilldela varje program till en grupp. 

Kopplingen grupper gör det enklare att hantera stora distributioner. De kan också förbättra svarstiden för klienter som har program som finns i olika regioner, eftersom du kan skapa platsbaserad connector grupper för att hantera endast lokala program. 

Mer information om koppling grupper finns [publicera program på separata nätverk och platser med hjälp av grupper för anslutningstjänsten](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Kapacitetsplanering 

Kopplingar kommer automatiskt att belastningsutjämna i en grupp för anslutningen, men det är också viktigt att se till att du har planerat tillräckligt med kapacitet mellan kopplingar ska hantera den förväntade trafikvolymen är. I allmänhet fler användare som du har större en dator som du behöver. Nedan finns en tabell med en beskrivning av volymen olika datorer kan hantera. Observera den är baserad på förväntad transaktioner Per andra (Transaktionsprogram) i stället av användaren sedan användning mönster varierar och kan inte användas för att förutsäga belastningen.  Observera också att det blir vissa skillnader baserat på storleken på svar och svarstiden för backend-programmet - större svar storlekar och längre svarstider leder till en lägre Max TPS.

|Kärnor|RAM|Förväntat svarstid (MS)-P99|Max Transaktionsprogram|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|
\* Den här datorn har en anslutningsgränsen 800. Vi använde 200 anslutning Standardgränsen för alla andra datorer.
 
>[!NOTE]
>Det finns inte mycket skillnaden i maximala Transaktionsprogram mellan 4, 8 och 16 kärnor datorer. Den största skillnaden mellan dem är i den förväntade svarstiden.  

## <a name="security-and-networking"></a>Säkerhet och nätverk

Kopplingar kan installeras var som helst i nätverket som kan användas för att skicka begäranden till Application Proxy-tjänsten. Vad är viktigt är att den dator som kör anslutningstjänsten även har åtkomst till dina appar. Du kan installera kopplingar inuti företagsnätverket eller på en virtuell dator som körs i molnet. Kopplingar kan köras inom en demilitariserad zon (DMZ), men det är inte nödvändigt eftersom all trafik är utgående så nätverket förblir säkra.

Kopplingar kan bara skicka utgående förfrågningar. Utgående trafik som skickas till Application Proxy-tjänsten och till publicerade program. Du behöver öppna ingående portar eftersom trafiken flödar båda sätten när en session har upprättats. Du behöver inte konfigurera belastningsutjämning mellan kopplingarna eller konfigurera inkommande åtkomst via dina brandväggar. 

Mer information om hur du konfigurerar utgående brandväggsregler finns [fungerar med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md).


## <a name="performance-and-scalability"></a>Prestanda och skalbarhet

Skala för tjänsten Application Proxy är transparent, men skalan är en faktor för kopplingar. Du måste ha tillräckligt med kopplingar ska hantera högtrafik. Dock behöver du inte konfigurera belastningsutjämning eftersom alla kopplingar i en grupp för anslutningen automatiskt belastningsutjämna.

Eftersom kopplingar tillståndslös bör påverkas de inte av antalet användare eller sessioner. I stället svarar de på antalet begäranden och deras nyttolastens storlek. Med standard webbtrafik, kan en genomsnittlig virtuell dator hantera några tusen begäranden per sekund. Den aktuella kapaciteten beror på de exakta dator egenskaperna. 

Prestanda för kopplingen bundna av processor- och nätverk. CPU-prestanda krävs för SSL-kryptering och dekryptering, medan nätverk är viktigt att få en snabb anslutning till program och tjänsten online i Azure.

Minnet är däremot mindre problem för kopplingar. Tjänsten online hand tar om mycket bearbetning och all trafik som inte har autentiserats. Allt som kan göras i molnet görs i molnet. 

Belastningsutjämning sker mellan kopplingar i ett angivet kopplings-gruppen. Vi gör en variation av resursallokering att avgöra vilka connector i gruppen hanterar en viss begäran. Om du av någon anledning att kopplingen eller datorn blir otillgänglig, trafiken börjar gå till en annan koppling i gruppen. Den här återhämtning är också varför vi rekommenderar att du har flera kopplingar.

En annan faktor som påverkar prestanda är kvaliteten på nätverk mellan kopplingar, inklusive: 

* **Tjänsten online**: långsam eller lång tidsfördröjning anslutningar till Application Proxy-tjänsten i Azure påverkan prestanda för kopplingen. Anslut din organisation till Azure med Express Route för bästa prestanda. I annat fall har teamet nätverk, se till att anslutningar till Azure hanteras så effektivt som möjligt. 
* **Backend-program**: I vissa fall finns proxy mellan kopplingen och backend-program som kan långsamma eller förhindra anslutningar. Om du vill felsöka det här scenariot öppna en webbläsare från anslutningsservern och försök få åtkomst till programmet. Om du kör kopplingarna i Azure, men program som finns lokalt, kanske upplevelsen inte användarna förväntar dig.
* **Domänkontrollanterna**: om kopplingarna utför enkel inloggning med hjälp av Kerberos-begränsad delegering kan de kontakta domänkontrollanter innan begäran skickades till serverdelen. Kopplingar som har en cache med Kerberos-biljetter, men i en miljö med upptagen svarstiderna domänkontrollanter kan påverka prestanda. Det här problemet är vanligare för kopplingar som körs i Azure men kommunicerar med domänkontrollanter som finns lokalt. 

Mer information om hur du optimerar nätverket finns [nätverk topologiöverväganden när du använder Azure Active Directory Application Proxy](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Domänanslutning

Kopplingar kan köras på en dator som inte är ansluten till domänen. Om du vill använda enkel inloggning (SSO) för program som använder integrerad autentisering IWA (Windows) måste dock en domänansluten dator. I det här fallet connector-datorer måste vara ansluten till en domän som kan utföra [Kerberos](https://web.mit.edu/kerberos) begränsad delegering för användare av publicerade program.

Kopplingar kan också anslutas till domäner eller skogar som har ett partiellt förtroende eller till skrivskyddade domänkontrollanter.

## <a name="connector-deployments-on-hardened-environments"></a>Kopplingen distributioner på strikt miljöer

Vanligtvis är enkla connector distribution och kräver ingen särskild konfiguration. Det finns emellertid vissa unika villkor som ska övervägas:

* Organisationer som begränsar den utgående trafiken måste [öppna portar som krävs](application-proxy-enable.md#open-your-ports).
* FIPS-kompatibla datorer kan krävas för att ändra konfigurationen så att connector-processer att generera och lagra ett certifikat.
* Organisationer som låsa deras miljö baserat på de processer som utfärdar nätverk begäranden har se till att båda connector-tjänster är aktiverade för åtkomst till alla portar som krävs och IP-adresser.
* I vissa fall utgående vidarebefordra proxyservrar Bryt dubbelriktat certifikatautentisering och orsaka kommunikationen misslyckas.

## <a name="connector-authentication"></a>Kopplingen autentisering

För att tillhandahålla en säker service kopplingar som har att autentisera mot tjänsten och tjänsten har att autentisera mot kopplingen. Den här autentiseringen görs med hjälp av klient- och servercertifikat när kopplingarna upprätta anslutningen. Det här sättet administratörens användarnamn och lösenord lagras inte på datorn för anslutningen.

De certifikat som används är specifika för tjänsten Application Proxy. De skapas under registreringen och automatiskt förnyas av kopplingarna varje par månader. 

Om en anslutning inte är ansluten till tjänsten för flera månader kan certifikat vara inaktuell. I det här fallet avinstallera och installera om kopplingen för att registrering av utlösare. Du kan köra följande PowerShell-kommandon:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Under huven

Kopplingar är baserade på Windows Server Web Application Proxy, så de har de flesta av samma hanteringsverktyg, inklusive Windows-händelseloggar

 ![Hantera händelseloggar med Loggboken](./media/application-proxy-connectors/event-view-window.png)

och Windows-prestandaräknare. 

 ![Lägga till räknare i kopplingen med Resursövervakaren](./media/application-proxy-connectors/performance-monitor.png)

Anslutningarna har både admin och sessionen loggar. Administratörsloggar innehåller viktiga händelser och deras fel. Sessionsloggar innehåller alla transaktioner och information om bearbetning. 

Loggarna, gå till Loggboken, öppna den **visa** -menyn och aktivera **visa analytiska loggar och felsökningsloggar**. Aktivera sedan att börja samla in händelser. Dessa loggar visas inte i Web Application Proxy i Windows Server 2012 R2 som kopplingarna baseras på en senare version.

Du kan undersöka statusen för tjänsten i fönstret tjänster. Kopplingen består av två Windows-tjänster: den faktiska anslutningen och uppdateringsfilen. Båda måste köras hela tiden.

 ![AzureAD tjänster lokalt](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Nästa steg


* [Publicera program på separata nätverk och platser med hjälp av connector-grupper](application-proxy-connector-groups.md)
* [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)
* [Felsöka Application Proxy och connector](application-proxy-troubleshoot.md)
* [Tyst installation av Azure AD Application Proxy Connector](application-proxy-register-connector-powershell.md)

