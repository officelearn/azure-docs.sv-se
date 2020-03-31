---
title: Hybrididentitetsdesign – dataskyddsstrategi Azure | Microsoft-dokument
description: Du definierar dataskyddsstrategin för din hybrididentitetslösning för att uppfylla de affärskrav som du har definierat.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0186d862968259aae73071cfecd7d62443d0256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109361"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definiera dataskyddsstrategi för din hybrididentitetslösning
I den här uppgiften definierar du dataskyddsstrategin för hybrididentitetslösningen för att uppfylla de affärskrav som du definierade i:

* [Bestäm dataskyddskrav](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Fastställa krav för innehållshantering](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Fastställa åtkomstkontrollkrav](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Fastställa krav för incidenthantering](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definiera alternativ för dataskydd
Som förklaras i [Bestäm krav på katalogsynkronisering](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)kan Microsoft Azure AD synkronisera med dina lokala ACTIVE Directory Domain Services (AD DS). Med den här integreringen kan organisationer använda Azure AD för att verifiera användarnas autentiseringsuppgifter när de försöker komma åt företagsresurser. Du kan göra detta för båda scenarierna: data lokalt och lokalt och i molnet. Åtkomst till data i Azure AD kräver användarautentisering via en säkerhetstokentjänst (STS).

När det har autentiserats läss användarens huvudnamn (UPN) från autentiseringstoken. Sedan bestämmer auktoriseringssystemet den replikerade partitionen och behållaren som motsvarar användarens domän. Information om användarens existens, aktiverat tillstånd och roll hjälper sedan auktoriseringssystemet att avgöra om åtkomst till målklienten är auktoriserad för användaren i den sessionen. Vissa behöriga åtgärder (särskilt skapa användar- och lösenordsåterställning) skapar en granskningsspårning som en klientadministratör sedan använder för att hantera efterlevnadsinsatser eller undersökningar.

Det kanske inte alltid är möjligt att flytta data från ditt lokala datacenter till Azure Storage via en Internetanslutning på grund av datavolym, bandbreddstillgänglighet eller andra överväganden. [Azure Storage Import/Export Service](../../storage/common/storage-import-export-service.md) är ett maskinvarubaserat alternativ för att placera/hämta stora mängder data i blob-lagring. Det låter dig skicka [BitLocker-krypterade](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) hårddiskar direkt till ett Azure-datacenter där molnoperatörer överför innehållet till ditt lagringskonto, eller så kan de ladda ned dina Azure-data till dina enheter för att återgå till dig. Endast krypterade diskar accepteras för den här processen (med hjälp av en BitLocker-nyckel som genereras av själva tjänsten under jobbinställningarna). BitLocker-nyckeln tillhandahålls till Azure separat, vilket ger out of band-tangentdelning.

Eftersom data under överföring kan ske i olika scenarier, är också relevant att veta att Microsoft Azure använder [virtuella nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) för att isolera klienters trafik från varandra, med åtgärder som värd- och gästnivå brandväggar, IP-paketfiltrering, portblockering och HTTPS-slutpunkter. Men de flesta av Azures interna kommunikation, inklusive infrastruktur till infrastruktur och infrastruktur-till-kund (lokalt), är också krypterade. Ett annat viktigt scenario är kommunikationen inom Azure-datacenter. Microsoft hanterar nätverk för att säkerställa att ingen virtuell dator kan personifiera eller tjuvlyssna på EN annans IP-adress. TLS/SSL används vid åtkomst till Azure Storage eller SQL-databaser, eller när du ansluter till Molntjänster. I det här fallet är kundadministratören ansvarig för att skaffa ett TLS/SSL-certifikat och distribuera det till sin klientinfrastruktur. Datatrafik som flyttas mellan virtuella datorer i samma distribution eller mellan klienter i en enda distribution via Microsoft Azure Virtual Network kan skyddas via krypterade kommunikationsprotokoll som HTTPS, SSL/TLS eller andra.

Beroende på hur du besvarade frågorna i [Bestäm dataskyddskraven](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)bör du kunna avgöra hur du vill skydda dina data och hur hybrididentitetslösningen kan hjälpa dig med den processen. I följande tabell visas de alternativ som stöds av Azure och som är tillgängliga för varje dataskyddsscenario.

| Alternativ för dataskydd | I vila i molnet | I vila lokalt | Under överföring |
| --- | --- | --- | --- |
| BitLocker-diskkryptering |X |X | |
| SQL Server för att kryptera databaser |X |X | |
| VM-till-VM-kryptering | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Läs [efterlevnad efter funktion](https://azure.microsoft.com/support/trust-center/services/) på Microsoft Azure Trust [Center](https://azure.microsoft.com/support/trust-center/) för att veta mer om de certifieringar som varje Azure-tjänst är kompatibel med.
> Eftersom alternativen för dataskydd använder en flerskiktsmetod är jämförelsen mellan dessa alternativ inte tillämplig för den här uppgiften. Se till att du använder alla tillgängliga alternativ för varje datatillstånd.
>
>

## <a name="define-content-management-options"></a>Definiera alternativ för innehållshantering

En fördel med att använda Azure AD för att hantera en hybrididentitetsinfrastruktur är att processen är helt transparent från slutanvändarens perspektiv. Användaren försöker komma åt en delad resurs, resursen kräver autentisering, användaren måste skicka en autentiseringsbegäran till Azure AD för att hämta token och komma åt resursen. Hela den här processen sker i bakgrunden, utan användarinteraktion. 

Organisationer som är oroliga för datasekretess kräver vanligtvis dataklassificering för sin lösning. Om deras nuvarande lokala infrastruktur redan använder dataklassificering är det möjligt att använda Azure AD som huvuddatabas för användarens identitet. Ett vanligt verktyg som används lokalt för dataklassificering kallas [Dataklassificeringsverktyg](https://msdn.microsoft.com/library/Hh204743.aspx) för Windows Server 2012 R2. Det här verktyget kan hjälpa dig att identifiera, klassificera och skydda data på filservrar i ditt privata moln. Det är också möjligt att använda den [automatiska filklassificeringen](https://technet.microsoft.com/library/hh831672.aspx) i Windows Server 2012 för att utföra den här uppgiften.

Om din organisation inte har dataklassificering på plats men behöver skydda känsliga filer utan att lägga till nya servrar lokalt kan de använda Microsoft [Azure Rights Management Service](https://technet.microsoft.com/library/JJ585026.aspx).  Azure RMS använder krypterings-, identitets- och auktoriseringsprinciper för att skydda dina filer och e-post, och det fungerar på flera enheter – telefoner, surfplattor och datorer. Eftersom Azure RMS är en molntjänst behöver du inte uttryckligen konfigurera förtroenden med andra organisationer innan du kan dela skyddat innehåll med dem. Samarbete mellan olika organisationer stöds automatiskt om de redan har en Office 365- eller Azure AD-katalog. Du kan också synkronisera bara de katalogattribut som Azure RMS behöver för att stödja en gemensam identitet för dina lokala Active Directory-konton med hjälp av Azure Active Directory Synchronization Services (AAD Sync) eller Azure AD Connect.

En viktig del av innehållshanteringen är att förstå vem som har åtkomst till vilken resurs, därför är en omfattande loggningskapacitet viktig för identitetshanteringslösningen. Azure AD tillhandahåller logg över 30 dagar inklusive:

* Ändringar i rollmedlemskap (t.ex. användare som läggs till i rollen Global admin)
* Uppdateringar av autentiseringsuppgifter (t.ex. lösenordsändringar)
* Domänhantering (t.ex. verifiera en anpassad domän, ta bort en domän)
* Lägga till eller ta bort program
* Användarhantering (t.ex. lägga till, ta bort, uppdatera en användare)
* Lägga till eller ta bort licenser

> [!NOTE]
> Läs [Microsoft Azure Security and Audit Log Management](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) om du vill veta mer om loggningsfunktioner i Azure.
> Beroende på hur du besvarade frågorna i [Bestäm innehållshanteringskrav](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)bör du kunna avgöra hur du vill att innehållet ska hanteras i hybrididentitetslösningen. Även om alla alternativ som exponeras i tabell 6 kan integreras med Azure AD, är det viktigt att definiera vilket som är lämpligare för dina affärsbehov.
>
>

| Alternativ för innehållshantering | Fördelar | Nackdelar |
| --- | --- | --- |
| Centraliserad lokal (Active Directory Rights Management Server) |Fullständig kontroll över serverinfrastrukturen som ansvarar för att klassificera data <br> Inbyggd funktion i Windows Server, inget behov av extra licens eller prenumeration <br> Kan integreras med Azure AD i ett hybridscenario <br> Stöder IRM-funktioner (Information Rights Management) i Microsoft Online-tjänster som Exchange Online och SharePoint Online samt Office 365 <br> Stöder lokala Microsoft-serverprodukter, till exempel Exchange Server, SharePoint Server och filservrar som kör Windows Server och File Classification Infrastructure (FCI). |Högre underhåll (håll jämna steg med uppdateringar, konfiguration och potentiella uppgraderingar), eftersom IT äger servern <br> Kräv en serverinfrastruktur lokalt<br> Utnyttjar inte Azure-funktioner internt |
| Centraliserad i molnet (Azure RMS) |Enklare att hantera jämfört med den lokala lösningen <br> Kan integreras med AD DS i ett hybridscenario <br>  Helt integrerad med Azure AD <br> Kräver inte en lokal server för att distribuera tjänsten <br> Stöder lokala Microsoft-serverprodukter som Exchange Server,SharePoint, Server och filservrar som kör Windows Server och Filklassificering, Infrastruktur (FCI) <br> IT, kan ha fullständig kontroll över sin klients nyckel med BYOK kapacitet. |Din organisation måste ha en molnprenumeration som stöder RMS <br> Din organisation måste ha en Azure AD-katalog för att stödja användarautentisering för RMS |
| Hybrid (Azure RMS integrerat med, lokal Active Directory Rights Management Server) |Det här scenariot ackumulerar fördelarna med både, centraliserade lokalt och i molnet. |Din organisation måste ha en molnprenumeration som stöder RMS <br> Din organisation måste ha en Azure AD-katalog för att kunna stödja användarautentisering för RMS, <br> Kräver en anslutning mellan Azure-molntjänst och lokal infrastruktur |

## <a name="define-access-control-options"></a>Definiera alternativ för åtkomstkontroll
Genom att utnyttja funktionerna för autentisering, auktorisering och åtkomstkontroll som är tillgängliga i Azure AD kan du göra det möjligt för ditt företag att använda en central identitetsdatabas samtidigt som användare och partner kan använda enkel inloggning (SSO) enligt följande bild:

![centraliserad förvaltning](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Centraliserad förvaltning och fullständig integration med andra kataloger

Azure Active Directory ger enkel inloggning till tusentals SaaS-program och lokala webbprogram. Mer information om den SSO-tredje part som testades av Microsoft finns i [listan över kompatibilitetskompatibiliteter](how-to-connect-fed-compatibility.md) för Azure Active Directory- federation. Den här funktionen gör det möjligt för organisationen att implementera en mängd olika B2B-scenarier samtidigt som du behåller kontrollen över identitets- och åtkomsthanteringen. Men under B2B-designprocessen är viktigt att förstå autentiseringsmetoden som används av partnern och validera om den här metoden stöds av Azure. För närvarande stöds följande metoder av Azure AD:

* Markeringsspråk för säkerhetspåstående (SAML)
* Oauth
* Kerberos
* Token
* Certifikat

> [!NOTE]
> läs [Azure Active Directory Authentication Protocols](https://msdn.microsoft.com/library/azure/dn151124.aspx) för att veta mer om varje protokoll och dess funktioner i Azure.
>
>

Med Azure AD-supporten kan mobila affärsprogram använda samma enkla autentiseringsupplevelse för mobila tjänster så att anställda kan logga in på sina mobila program med sina Active Directory-autentiseringsuppgifter för företag. Med den här funktionen stöds Azure AD som identitetsleverantör i Mobila tjänster tillsammans med andra identitetsleverantörer som redan stöds (som inkluderar Microsoft-konton, Facebook-ID, Google-ID och Twitter-ID). Om de lokala apparna använder användarens autentiseringsuppgifter som finns på företagets AD DS, bör åtkomsten från partner och användare som kommer från molnet vara transparent. Du kan hantera användarens kontroller för villkorlig åtkomst till (molnbaserade) webbprogram, webb-API, Microsofts molntjänster, SaaS-program från tredje part och inbyggda (mobila) klientprogram och har fördelarna med säkerhet, granskning, rapportering Plats. Vi rekommenderar dock att du validerar implementeringen i en icke-produktionsmiljö eller med ett begränsat antal användare.

> [!TIP]
> Det är viktigt att nämna att Azure AD inte har grupprincip som AD DS har. För att kunna tillämpa principen för enheter behöver du en lösning för hantering av mobila enheter, till exempel [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).
>
>

När användaren har autentiserats med Azure AD är det viktigt att utvärdera åtkomstnivån som användaren har. Åtkomstnivån som användaren har via en resurs kan variera. Azure AD kan lägga till ytterligare ett säkerhetslager genom att styra åtkomsten till vissa resurser, men tänk på att själva resursen också kan ha en egen åtkomstkontrolllista separat, till exempel åtkomstkontrollen för filer som finns i en filserver. Följande bild sammanfattar de åtkomstkontrollnivåer som du kan ha i ett hybridscenario:

![åtkomstkontroll](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Varje interaktion i diagrammet som visas i figur X representerar ett åtkomstkontrollscenario som kan omfattas av Azure AD. Nedan har du en beskrivning av varje scenario:

1. Villkorlig åtkomst till program som finns lokalt: Du kan använda registrerade enheter med åtkomstprinciper för program som är konfigurerade för att använda AD FS med Windows Server 2012 R2.

2. Åtkomstkontroll till Azure-portalen: Med Azure kan du också styra åtkomsten till portalen med hjälp av rollbaserad åtkomstkontroll (RBAC)). Med den här metoden kan företaget begränsa antalet åtgärder som en individ kan göra i Azure-portalen. Genom att använda RBAC för att kontrollera åtkomsten till portalen kan IT-administratörer delegera åtkomst med hjälp av följande åtkomsthanteringsmetoder:

   - Gruppbaserad rolltilldelning: Du kan tilldela åtkomst till Azure AD-grupper som kan synkroniseras från din lokala Active Directory. På så sätt kan du utnyttja de befintliga investeringar som din organisation har gjort i verktyg och processer för att hantera grupper. Du kan också använda den delegerade grupphanteringsfunktionen i Azure AD Premium.
   - Använd inbyggda roller i Azure: Du kan använda tre roller – Ägare, Deltagare och Läsare, för att säkerställa att användare och grupper har behörighet att bara utföra de uppgifter de behöver för att utföra sina jobb.
   -  Detaljerad åtkomst till resurser: Du kan tilldela roller till användare och grupper för en viss prenumeration, resursgrupp eller en enskild Azure-resurs, till exempel en webbplats eller databas. På så sätt kan du se till att användarna har åtkomst till alla resurser de behöver och ingen åtkomst till resurser som de inte behöver hantera.

   > [!NOTE]
   > Om du skapar program och vill anpassa åtkomstkontrollen för dem, är det också möjligt att använda Azure AD-programroller för auktorisering. Läs det här [WebApp-RoleClaims-DotNet-exemplet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) om hur du skapar din app för att använda den här funktionen.


3. Villkorlig åtkomst för Office 365-program med Microsoft Intune: IT-administratörer kan etablera enhetsprinciper för villkorlig åtkomst för att skydda företagsresurser, samtidigt som informationsarbetare på kompatibla enheter kan komma åt tjänsterna. 
  
4. Villkorlig åtkomst för Saas-appar: Med den [här funktionen](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) kan du konfigurera åtkomstregler för multifaktorautentisering per program och möjligheten att blockera åtkomst för användare som inte finns i ett betrott nätverk. Du kan tillämpa multifaktorautentiseringsreglerna för alla användare som har tilldelats programmet, eller endast för användare inom angivna säkerhetsgrupper. Användare kan uteslutas från multifaktorautentiseringskravet om de använder programmet från en IP-adress som finns i organisationens nätverk.

Eftersom alternativen för åtkomstkontroll använder en flerskiktsmetod är jämförelsen mellan dessa alternativ inte tillämplig för den här aktiviteten. Se till att du använder alla tillgängliga alternativ för varje scenario som kräver att du styr åtkomsten till dina resurser.

## <a name="define-incident-response-options"></a>Definiera alternativ för incidentsvar
Azure AD kan hjälpa IT att identifiera potentiella säkerhetsrisker i miljön genom att övervaka användarens aktivitet. IT kan använda Azure AD Access- och användningsrapporter för att få insyn i integriteten och säkerheten i organisationens katalog. Med den här informationen kan en IT-administratör bättre avgöra var eventuella säkerhetsrisker kan ligga så att de kan planera för att minska dessa risker på lämpligt sätt.  [Azure AD Premium-prenumeration](../fundamentals/active-directory-get-started-premium.md) har en uppsättning säkerhetsrapporter som kan göra det möjligt för IT-för att hämta den här informationen. [Azure AD-rapporter](../reports-monitoring/overview-reports.md) kategoriseras på följande sätt:

* **Avvikelserapporter**: Innehåller inloggningshändelser som visade sig vara avvikande. Målet är att göra dig medveten om sådan aktivitet och göra det möjligt för dig att avgöra om en händelse är misstänkt.
* **Integrerad programrapport**: Ger insikter om hur molnprogram används i din organisation. Azure Active Directory erbjuder integrering med tusentals molnprogram.
* **Felrapporter**: Ange fel som kan uppstå vid etablering av konton till externa program.
* **Användarspecifika rapporter**: Visa enhet/logga in aktivitetsdata för en viss användare.
* **Aktivitetsloggar**: Innehåller en post över alla granskade händelser under de senaste 24 timmarna, de senaste 7 dagarna eller de senaste 30 dagarna, samt ändringar av gruppaktivitet och återställning och registrering av lösenord.

> [!TIP]
> En annan rapport som också kan hjälpa incidentsvarsteamet som arbetar med ett ärende är [användaren med läckt autentiseringsuppgifter](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) rapport. Den här rapporten visar alla matchningar mellan listan över läckta autentiseringsuppgifter och din klient.
>


Andra viktiga inbyggda rapporter i Azure AD som kan användas under en incidenthanteringsutredning och är:

* **Aktivitet för återställning av lösenord**: ge administratören insikter om hur aktivt återställning av lösenord används i organisationen.
* **Registreringsaktivitet för lösenordsåterställning**: ger insikter om vilka användare som har registrerat sina metoder för återställning av lösenord och vilka metoder de har valt.
* **Gruppaktivitet:** innehåller en historik över ändringar i gruppen (t.ex. användare som lagts till eller tagits bort) som initierades på åtkomstpanelen.

Förutom den grundläggande rapporteringsfunktionen i Azure AD Premium som du kan använda under en incidentresponsundersökningsprocess kan IT-den också dra nytta av granskningsrapporten för att få information som:

* Ändringar i rollmedlemskap (till exempel användare som lagts till i rollen Global Admin)
* Uppdateringar av autentiseringsuppgifter (till exempel lösenordsändringar)
* Domänhantering (till exempel verifiera en anpassad domän, ta bort en domän)
* Lägga till eller ta bort program
* Användarhantering (till exempel lägga till, ta bort, uppdatera en användare)
* Lägga till eller ta bort licenser

Eftersom alternativen för incidenthantering använder en flerskiktsmetod är jämförelsen mellan dessa alternativ inte tillämplig för den här aktiviteten. Se till att du använder alla tillgängliga alternativ för varje scenario som kräver att du använder Azure AD-rapporteringsfunktionen som en del av företagets incidentsvarsprocess.

## <a name="next-steps"></a>Nästa steg
[Ta reda på hybrididentitetshanteringsuppgifter](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Se även
[Översikt över designöverväganden](plan-hybrid-identity-design-considerations-overview.md)
