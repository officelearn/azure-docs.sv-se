---
title: Lokal återställning av lösenordsåterskrivning med Azure AD SSPR - Azure Active Directory
description: Få molnlösenord skrivna tillbaka till lokal AD-infrastruktur
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fe58ae95c8d9c6b93c7e92e093541af009781ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454439"
---
# <a name="what-is-password-writeback"></a>Vad är tillbakaskrivning av lösenord?

Att ha ett molnbaserat verktyg för återställning av lösenord är bra men de flesta företag har fortfarande en lokal katalog där användarna finns. Hur stöder Microsoft att hålla traditionella lokala Active Directory (AD) synkroniserade med lösenordsändringar i molnet? Återställning av lösenord är en funktion som är aktiverad med [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) som gör att lösenordsändringar i molnet kan skrivas tillbaka till en befintlig lokal katalog i realtid.

Återställning av lösenord stöds i miljöer som använder:

* [Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)
* [Synkronisering av lösenordshash](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Direktautentisering](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> Återställning av lösenord slutar fungera för kunder som använder Azure AD Connect-versioner 1.0.8641.0 och äldre när [Azure Access Control-tjänsten (ACS) dras tillbaka den 7 november 2018](../azuread-dev/active-directory-acs-migration.md). Azure AD Connect-versioner 1.0.8641.0 och äldre tillåter inte längre tillbakaskrivning av lösenord vid den tidpunkten eftersom de är beroende av ACS för den funktionen.
>
> För att undvika avbrott i tjänsten, uppgradera från en tidigare version av Azure AD Connect till en nyare version, se artikeln [Azure AD Connect: Upgrade från en tidigare version till den senaste](../hybrid/how-to-upgrade-previous-version.md)
>

Återställning av lösenord ger:

* **Tvingande av lokala Active Directory-lösenordsprinciper**: När en användare återställer sitt lösenord kontrolleras det för att säkerställa att den uppfyller din lokala Active Directory-princip innan den begår den till den katalogen. Den här granskningen omfattar kontroll av historik, komplexitet, ålder, lösenordsfilter och andra lösenordsbegränsningar som du har definierat i lokal Active Directory.
* **Feedback med noll fördröjning:** Tillbakaskrivning av lösenord är en synkron åtgärd. Användarna meddelas omedelbart om deras lösenord inte uppfyllde principen eller inte kunde återställas eller ändras av någon anledning.
* **Stöder lösenordsändringar från åtkomstpanelen och Office 365:** När synkroniserade användare med federerade eller lösenordshämtade användare kommer att ändra sina utgångna eller icke-utgångna lösenord skrivs dessa lösenord tillbaka till din lokala Active Directory-miljö.
* **Stöder återställning av lösenord när en administratör återställer dem från Azure-portalen:** När en administratör återställer en användares lösenord i [Azure-portalen,](https://portal.azure.com)om användaren är federerad eller lösenordsh hash synkroniserad, skrivs lösenordet tillbaka till lokalt. Den här funktionen stöds för närvarande inte i Office-administratörsportalen.
* **Kräver inga regler för inkommande brandvägg:** Tillbakaskrivning av lösenord använder ett Azure Service Bus-relä som en underliggande kommunikationskanal. All kommunikation är utgående över port 443.

> [!NOTE]
> Administratörskonton som finns inom skyddade grupper i lokala AD kan användas med tillbakaskrivning av lösenord. Administratörer kan ändra sitt lösenord i molnet men kan inte använda återställning av lösenord för att återställa ett glömt lösenord. Mer information om skyddade grupper finns i [Skyddade konton och grupper i Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

## <a name="licensing-requirements-for-password-writeback"></a>Licenskrav för återställning av lösenord

**Återställning/ändring/låsning/upplåsning med lokal tillbakaskrivning med självbetjäning är en premiumfunktion i Azure AD**. Mer information om licensiering finns på [prisplatsen för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Om du vill använda tillbakaskrivning av lösenord måste du ha någon av följande licenser tilldelade på din klientorganisation:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Säkerhet E3 eller A3
* Enterprise Mobility + Säkerhet E5 eller A5
* Microsoft 365 E3 eller A3
* Microsoft 365 E5 eller A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Fristående Office 365-licensplaner *stöder inte "Självbetjäningslösenordsåterställning/ändring/låsning med lokal tillbakaskrivning"* och kräver att du har ett av de föregående abonnemangen för att den här funktionen ska fungera.

## <a name="how-password-writeback-works"></a>Så fungerar tillbakaskrivning av lösenord

När en synkroniserad användare med federerade eller lösenordsvrediga användare försöker återställa eller ändra sitt lösenord i molnet inträffar följande åtgärder:

1. En kontroll utförs för att se vilken typ av lösenord användaren har. Om lösenordet hanteras lokalt:
   * En kontroll utförs för att se om tillbakaskrivningstjänsten är igång. Om så är fallet kan användaren fortsätta.
   * Om återskrivningstjänsten är nere informeras användaren om att deras lösenord inte kan återställas just nu.
1. Därefter skickar användaren lämpliga autentiseringsgrindar och når sidan **Återställ lösenord.**
1. Användaren väljer ett nytt lösenord och bekräftar det.
1. När användaren väljer **Skicka**krypteras lösenordet för klartext med en symmetrisk nyckel som skapats under inställningsprocessen för tillbakaskrivning.
1. Det krypterade lösenordet ingår i en nyttolast som skickas via en HTTPS-kanal till ditt klientspecifika servicebussrelä (som har ställts in åt dig under återskrivningsinställningsprocessen). Det här reläet skyddas av ett slumpmässigt genererat lösenord som bara din lokala installation känner till.
1. När meddelandet når servicebussen aktiveras slutpunkten för återställning av lösenord automatiskt och ser att en återställningsbegäran väntar.
1. Tjänsten söker sedan efter användaren med hjälp av molnet ankare attribut. För att sökningen ska lyckas:

   * Användarobjektet måste finnas i Active Directory-anslutningsutrymmet.
   * Användarobjektet måste vara länkat till motsvarande metaversumobjekt (MV).
   * Användarobjektet måste vara länkat till motsvarande Azure Active Directory-anslutningsobjekt.
   * Länken från Active Directory-anslutningsobjektet till MV `Microsoft.InfromADUserAccountEnabled.xxx` måste ha synkroniseringsregeln på länken.
   
   När anropet kommer in från molnet använder synkroniseringsmotorn **attributet cloudAnchor** för att slå upp Azure Active Directory-anslutningsutrymmesobjektet. Den följer sedan länken tillbaka till MV-objektet och följer sedan länken tillbaka till Active Directory-objektet. Eftersom det kan finnas flera Active Directory-objekt (multi-skog) för samma `Microsoft.InfromADUserAccountEnabled.xxx` användare, förlitar sig synkroniseringsmotorn på länken för att välja rätt.

1. När användarkontot har hittats görs ett försök att återställa lösenordet direkt i lämplig Active Directory-skog.
1. Om åtgärden för lösenordsuppsättningen lyckas får användaren veta att lösenordet har ändrats.
   > [!NOTE]
   > Om användarens lösenordshhÃ¤nde synkroniseras med Azure AD med hjälp av synkronisering av lösenord hash, finns det en risk att den lokala lösenordsprincipen är svagare än principen för molnlösenord. I det här fallet tillämpas den lokala principen. Den här principen säkerställer att din lokala princip tillämpas i molnet, oavsett om du använder synkronisering av lösenord hash-synkronisering eller federation för att tillhandahålla enkel inloggning.

1. Om åtgärden för lösenordsuppsättning misslyckas uppmanas användaren att försöka igen. Åtgärden kan misslyckas eftersom:
    * Tjänsten var nere.
    * Det valda lösenordet uppfyllde inte organisationens principer.
    * Det gick inte att hitta användaren i den lokala Active Directory.

      Felmeddelandena ger vägledning till användare så att de kan försöka lösa utan administratörsingripar.

## <a name="password-writeback-security"></a>Säkerhet för återställning av lösenord

Tillbakaskrivning av lösenord är en mycket säker tjänst. För att säkerställa att din information är skyddad aktiveras en säkerhetsmodell med fyra nivåer som följande beskriver:

* **Bostadsrättsspecifikt servicebussrelä**
   * När du konfigurerar tjänsten ställs ett klientspecifikt servicebussrelä in som skyddas av ett slumpmässigt genererat starkt lösenord som Microsoft aldrig har åtkomst till.
* **Låst, kryptografiskt stark krypteringsnyckel för lösenord**
   * När servicebussreläet har skapats skapas en stark symmetrisk nyckel som används för att kryptera lösenordet när det kommer över kabeln. Den här nyckeln lever bara i företagets hemliga butik i molnet, som är kraftigt låst och granskad, precis som alla andra lösenord i katalogen.
* **Säkerhet för transportlager (TLS) för transportlager (branschstandard)**
   1. När en lösenordsåterställning eller ändringsåtgärd sker i molnet krypteras lösenordet för klartext med din offentliga nyckel.
   1. Det krypterade lösenordet placeras i ett HTTPS-meddelande som skickas via en krypterad kanal med hjälp av Microsoft TLS/SSL-certs till servicebussreläet.
   1. När meddelandet anländer till servicebussen väcks och autentiseras servicebussen med hjälp av det starka lösenord som tidigare genererades.
   1. Den lokala agenten hämtar det krypterade meddelandet och dekrypterar det med hjälp av den privata nyckeln.
   1. Den lokala agenten försöker ange lösenordet via AD DS SetPassword API. Det här steget är det som gör det möjligt att tillämpa din lokala lösenordsprincip i Active Directory (till exempel komplexitet, ålder, historik och filter) i molnet.
* **Principer för förfallodatum för meddelanden**
   * Om meddelandet sitter i servicebussen eftersom din lokala service är nere, tidsfördärvar och tas bort efter flera minuter. Time-out och borttagning av meddelandet ökar säkerheten ytterligare.

### <a name="password-writeback-encryption-details"></a>Krypteringsinformation för lösenordsrestitution

När en användare har skickat en återställning av lösenord går återställningsbegäran igenom flera krypteringssteg innan den anländer till din lokala miljö. Dessa krypteringssteg säkerställer maximal tjänsttillförlitlighet och säkerhet. De beskrivs på följande sätt:

* **Steg 1: Lösenordskryptering med 2048-bitars RSA-nyckel:** När en användare skickar ett lösenord som ska skrivas tillbaka till lokala krypteras själva det inskickade lösenordet med en 2048-bitars RSA-nyckel.
* **Steg 2: Kryptering på paketnivå med AES-GCM:** Hela paketet, lösenordet plus de metadata som krävs, krypteras med hjälp av AES-GCM. Den här krypteringen förhindrar att alla som har direkt åtkomst till den underliggande ServiceBus-kanalen visar eller manipulerar innehållet.
* **Steg 3: All kommunikation sker via TLS/SSL:** All kommunikation med ServiceBus sker i en SSL/TLS-kanal. Denna kryptering säkrar innehållet från obehörig tredje part.
* **Automatisk nyckelrullning var sjätte månad:** Alla nycklar rullas över var sjätte månad, eller varje gång tillbakaskrivning av lösenord inaktiveras och sedan återaktiverat på Azure AD Connect, för att säkerställa maximal servicesäkerhet och säkerhet.

### <a name="password-writeback-bandwidth-usage"></a>Bandbreddsanvändning för återställning av lösenord

Återställning av lösenord är en tjänst med låg bandbredd som bara skickar förfrågningar tillbaka till den lokala agenten under följande omständigheter:

* Två meddelanden skickas när funktionen är aktiverad eller inaktiverad via Azure AD Connect.
* Ett meddelande skickas en gång var femte minut som ett serviceslagslag så länge tjänsten körs.
* Två meddelanden skickas varje gång ett nytt lösenord skickas:
   * Det första meddelandet är en begäran om att utföra åtgärden.
   * Det andra meddelandet innehåller resultatet av åtgärden och skickas under följande omständigheter:
      * Varje gång ett nytt lösenord skickas in under en självbetjäningsåterställning av användaren.
      * Varje gång ett nytt lösenord skickas in under en åtgärd för ändring av användarlösenord.
      * Varje gång ett nytt lösenord skickas under en administratörsinitierad återställning av användarlösenord (endast från Azure-administratörsportalerna).

#### <a name="message-size-and-bandwidth-considerations"></a>Meddelandestorlek och bandbreddsöverväganden

Storleken på vart och ett av de meddelanden som beskrivits tidigare är vanligtvis under 1 KB. Även under extrema belastningar, lösenordet tillbakaskrivning tjänsten själv förbrukar några kilobit per sekund av bandbredd. Eftersom varje meddelande skickas i realtid, endast när det krävs av en lösenordsuppdateringsåtgärd, och eftersom meddelandestorleken är så liten, är bandbreddsanvändningen för tillbakaskrivningsfunktionen för liten för att ha en mätbar effekt.

## <a name="supported-writeback-operations"></a>Tillbakaskrivningsåtgärder som stöds

Lösenord skrivs tillbaka i alla följande situationer:

* **Slutanvändares åtgärder som stöds**
   * Alla slutanvändares självbetjäningslösenordsåtgärd
   * Alla självbetjäningsförbehör för slutanvändaren ändrar lösenord, till exempel förfallodatum för lösenord
   * Återställning av självbetjäning för slutanvändare som kommer från [portalen](https://passwordreset.microsoftonline.com) för återställning av lösenord
* **Administratörsåtgärder som stöds**
   * Alla självbetjäningslösenordsåtgärder
   * Alla självbetjäningsförbehör för administratörer ändrar lösenord, till exempel förfallodatum för lösenord
   * Återställning av självbetjäningslösenord som kommer från [portalen](https://passwordreset.microsoftonline.com) för återställning av lösenord
   * Återställning av administratörsinitierade slutanvändarens lösenord från [Azure-portalen](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Tillbakaskrivningsåtgärder som inte stöds

Lösenord skrivs *inte* tillbaka i någon av följande situationer:

* **Slutanvändarens åtgärder som inte stöds**
   * Alla slutanvändare som återställer sitt eget lösenord med PowerShell version 1, version 2 eller Microsoft Graph API
* **Administratörsåtgärder som inte stöds**
   * Återställning av administratörsinitierat lösenord från Slutanvändaren från PowerShell version 1, version 2 eller Microsoft Graph API
   * Återställning av administratörsinitierat lösenord från [Microsoft 365-administrationscentret](https://admin.microsoft.com)

> [!WARNING]
> Användning av kryssrutan "Användaren måste ändra lösenord vid nästa inloggning" i lokala administrationsverktyg för Active Directory, till exempel Active Directory – användare och datorer eller Active Directory Administrative Center stöds som en förhandsgranskningsfunktion i Azure AD Connect. Mer information finns i artikeln [Implementera synkronisering av lösenordshã¤nning med Azure AD Connect-synkronisering](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Nästa steg

Aktivera tillbakaskrivning av lösenord med hjälp av självstudien: [Aktivera tillbakaskrivning av lösenord](tutorial-enable-writeback.md)
