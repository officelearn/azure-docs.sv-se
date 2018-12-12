---
title: Hybrid identity design – data protection strategi Azure | Microsoft Docs
description: Du definierar data protection strategin för din hybrididentitetslösning att uppfylla de affärskrav som du har definierat.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 20782679a4e73c029d6c83fd3bc57914b4d28691
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865054"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definiera en strategi för skydd av data för din hybrididentitetslösning
I det här steget definierar du strategin för din hybrididentitetslösning att uppfylla affärskrav som du definierade i dataskydd:

* [Fastställa kraven på dataskydd](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Fastställa krav för innehållshantering](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Fastställa krav på åtkomstkontroll](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Fastställa kraven på incidentsvar](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definiera alternativ för skydd av data
Enligt beskrivningen i [fastställa katalogsynkroniseringskrav](plan-hybrid-identity-design-considerations-directory-sync-requirements.md), Microsoft Azure AD kan synkronisera med din lokala Active Directory Domain Services (AD DS). Den här integrationen kan organisationer använda Azure AD kan verifiera användarnas autentiseringsuppgifter när de försöker komma åt företagets resurser. Du kan göra detta för båda scenarierna: data vid rest både lokalt och i molnet. Åtkomst till data i Azure AD kräver användarautentisering via en säkerhetstokentjänst (STS).

När autentiseringen är klar läses användarens huvudnamn (UPN) från autentiseringstoken. Auktorisering systemet avgör sedan replikerade partition och behållare som motsvarar användarens domän. Information om användarens förekomst aktiverade tillstånd och rollen sedan hjälper auktorisering systemet avgöra om åtkomst till Målklienten är auktoriserad för användaren i den aktuella sessionen. Vissa auktoriserade åtgärder (mer specifikt kan skapa användare och lösenordsåterställning) skapa en verifieringskedja som en Innehavaradministratör använder sedan för att hantera arbete för efterlevnad eller undersökningar.

Flyttar data från ditt lokala datacenter till Azure Storage via en Internetanslutning kan inte alltid är möjligt på grund av datavolymen, bandbreddstillgänglighet eller annat att tänka på. Den [Azure Storage Import/Export-tjänsten](../../storage/common/storage-import-export-service.md) erbjuder en maskinvarubaserad alternativ för att placera/hämtning av stora mängder data i blob storage. Du kan skicka [BitLocker-krypterad](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) hårddiskar direkt till ett Azure-datacenter där molnoperatörer Överför innehållet till ditt storage-konto eller de kan ladda ned dina Azure-data till dina enheter att återgå till dig. Endast krypterade diskar accepteras för den här processen (med en BitLocker-nyckel som genereras av själva tjänsten under installationen av jobb). BitLocker-nyckel har angetts till Azure separat, vilket ger utanför band viktiga delar.

Eftersom data under överföringen kan ske i olika scenarier, är också relevant för vet att Microsoft Azure använder [virtuella nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) att isolera klienternas trafik från varandra, använda åtgärder, till exempel värd - och gästnivå brandväggar, IP-paketfilter, port blockerar och HTTPS-slutpunkter. De flesta av Azures intern kommunikation, inklusive infrastruktur-infrastruktur och infrastruktur-kund (lokalt), är också krypterade. Ett annat viktigt scenario är kommunikation inom Azure-Datacenter; Microsoft hanterar nätverk om du vill säkerställa att ingen virtuell dator kan personifiera eller eavesdrop på IP-adressen för en annan. TLS/SSL används vid åtkomst till Azure Storage eller SQL-databaser eller när du ansluter till molntjänster. I det här fallet ansvarar kund-administratören för att erhålla ett TLS/SSL-certifikat och distribuera den till den klient-infrastrukturen. Flyttning av data trafik mellan virtuella datorer i samma distribution eller mellan klienter i en enda distribution via Microsoft Azure Virtual Network kan skyddas genom krypterad kommunikationsprotokoll, till exempel HTTPS eller SSL/TLS.

Beroende på hur du besvarade frågorna i [avgöra kraven på dataskydd](plan-hybrid-identity-design-considerations-dataprotection-requirements.md), du ska kunna avgöra hur du vill skydda dina data och hur hybrididentitetslösning kan hjälpa dig med den här processen. I följande tabell visas de alternativ som stöds av Azure och som är tillgängliga för varje scenario för skydd av data.

| Alternativ för skydd av data | I vila i molnet | Vid rest på plats | Under överföring |
| --- | --- | --- | --- |
| BitLocker-diskkryptering |X |X | |
| SQL Server för att kryptera databaser |X |X | |
| VM-till-VM-kryptering | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Läs [efterlevnad av funktion](https://azure.microsoft.com/support/trust-center/services/) på [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) vill veta mer om de certifieringar som Azure-tjänster är kompatibla med.
> Jämförelse mellan dessa alternativ kan inte användas för den här uppgiften eftersom alternativen för att skydda data använder en multilayer metod. Se till att du använder sig av alla alternativ som är tillgängliga för varje status för data.
>
>

## <a name="define-content-management-options"></a>Definiera alternativ för innehållshantering

En fördel med att använda Azure AD för att hantera en hybrididentitetsinfrastruktur är att processen är helt transparent från slutanvändarens perspektiv. Användaren försöker få åtkomst till en delad resurs, resursen kräver autentisering, som användaren måste skicka en autentiseringsbegäran om till Azure AD för att hämta token och åtkomst till resursen. Hela processen sker i bakgrunden utan interaktion från användaren. 

Organisationer som har problem om datasekretess vanligtvis kräver dataklassificering för sin lösning. Om den aktuella lokala infrastrukturen redan använder klassificering, är det möjligt att använda Azure AD som huvudlagringsplatsen för användarens identitet. Ett vanligt Verktyg att det är användas lokalt för dataklassificering kallas [Dataklassificeringsverktygslådan](https://msdn.microsoft.com/library/Hh204743.aspx) för Windows Server 2012 R2. Det här verktyget kan hjälpa till att identifiera, klassificera och skydda data på filservrar i ditt privata moln. Det är också möjligt att använda den [automatisk Filklassificering](https://technet.microsoft.com/library/hh831672.aspx) i Windows Server 2012 för att åstadkomma detta.

Om din organisation har inte klassificering av data på plats, men behöver skydda känsliga filer utan att lägga till nya servrar på plats, använder de Microsoft [Azure Rights Management-tjänsten](https://technet.microsoft.com/library/JJ585026.aspx).  Azure RMS använder krypterings-, identitets- och auktoriseringsprinciper för att skydda dina filer och e-post och fungerar på flera olika enheter – telefoner, surfplattor och datorer. Eftersom Azure RMS är en molnbaserad tjänst, finns behöver du inte uttryckligen konfigurera förtroenden med andra organisationer innan du kan dela skyddat innehåll med dem. Samarbete mellan olika organisationer stöds automatiskt om de redan har en Office 365 eller Azure AD-katalog. Du kan också synkronisera enbart det katalogattribut som Azure RMS måste ha stöd för en gemensam identitet för din lokala Active Directory-konton med hjälp av Azure Active Directory Sync Services (AAD Sync) eller Azure AD Connect.

En viktig del av innehållshantering är att förstå vem har åtkomst till vilken resurs, en omfattande loggningsfunktioner är därför viktigt för att hantera identiteter. Azure AD tillhandahåller logg över 30 dagar, inklusive:

* Ändringar i rollmedlemskap (ex: användare som lagts till i rollen som Global administratör)
* Uppdateringar av autentiseringsuppgifter (t.ex.: lösenordsändringar)
* Domänhantering (ex: Verifiera en anpassad domän, ta bort en domän)
* Att lägga till eller ta bort program
* Hantering av användare (ex: lägga till, ta bort, uppdatera en användare)
* Att lägga till eller ta bort licenser

> [!NOTE]
> Läs [Microsoft Azure-säkerhet och hantering av granskningslogg](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) vill veta mer om funktioner för loggning i Azure.
> Beroende på hur du besvarade frågorna i [fastställa krav för innehållshantering](plan-hybrid-identity-design-considerations-contentmgt-requirements.md), du ska kunna avgöra hur du vill att innehållet ska hanteras i din hybrididentitetslösning. Alla alternativ som visas i tabell 6 är kan integreras med Azure AD, är det viktigt att definiera vilket är mer lämpliga för dina affärsbehov.
>
>

| Alternativ för innehållshantering | Fördelar | Nackdelar |
| --- | --- | --- |
| Lokalt centraliserad (Active Directory Rights Management Server) |Fullständig kontroll över server-infrastrukturen ansvarar för att klassificera data <br> Med den inbyggda funktionen i Windows Server, utan behovet av extra licens eller prenumeration <br> Kan integreras med Azure AD i ett hybridscenario <br> Stöd för information rights management (IRM) funktioner i Microsoft Online services, till exempel Exchange Online och SharePoint Online, samt Office 365 <br> Har stöd för lokala Microsoft server-produkter, till exempel Exchange Server, SharePoint Server och filservrar som kör Windows Server och Filklassificeringsinfrastruktur (FCI). |Högre Underhåll (hålla jämna steg med uppdateringar, konfiguration och eventuella uppgraderingar), eftersom IT äger servern <br> Kräv en serverinfrastruktur på plats<br> Internt utnyttja inte Azure-funktioner |
| Centraliserad i molnet (Azure RMS) |Enklare att hantera jämfört med lokal lösning <br> Kan integreras med AD DS i ett scenario med hybridanvändning <br>  Helt integrerat med Azure AD <br> Du behöver en lokal server för att distribuera tjänsten <br> Har stöd för lokala Microsoft-serverprodukter som Exchange Server, SharePoint, Server och filservrar som kör Windows Server och Filklassificering (FCI) för <br> IT-avdelningen, kan ha fullständig kontroll över sin klients nyckeln med BYOK-funktioner. |Din organisation måste ha en molnprenumeration med stöd för RMS <br> Din organisation måste ha en Azure AD-katalog för stöd av användarautentisering för RMS |
| Hybrid (Azure RMS som är integrerad med, en lokal Active Directory Rights Management Server) |Det här scenariot ackumuleras fördelarna med både, centraliserad lokalt och i molnet. |Din organisation måste ha en molnprenumeration med stöd för RMS <br> Din organisation måste ha en Azure AD-katalog för stöd av användarautentisering för RMS, <br> Kräver en anslutning mellan Azure-molntjänst och lokal infrastruktur |

## <a name="define-access-control-options"></a>Definiera alternativ för åtkomstkontroll
Du kan aktivera ditt företag att använda en central identitetsdatabas samtidigt som användarna genom att använda autentisering, auktorisering och få tillgång till kontroll funktioner tillgängliga i Azure AD och partner att använda enkel inloggning (SSO), enligt följande bild:

![](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Centraliserad hantering och helt integrering med andra kataloger

Azure Active Directory tillhandahåller enkel inloggning till tusentals SaaS-program och lokala webbprogram. Se den [kompatibilitetslistan för Azure Active Directory federation: identitetsleverantörer från tredje part som kan användas för att implementera enkel inloggning](how-to-connect-fed-compatibility.md) nedan för mer information om den enkel inloggning från tredje parten som har testats av Microsoft. Den här funktionen gör det möjligt för organisation att implementera en mängd B2B-scenarier och bibehålla kontrollen över identitets- och åtkomsthantering. Under B2B är utforma processen dock viktigt att förstå den autentiseringsmetod som används av partnern och kontrollera om den här metoden stöds av Azure. Följande metoder är för närvarande stöds av Azure AD:

* Security Assertion Markup Language (SAML)
* OAuth
* Kerberos
* Token
* Certifikat

> [!NOTE]
> läsa [Azure Active Directory-autentiseringsprotokoll](https://msdn.microsoft.com/library/azure/dn151124.aspx) du mer information om varje protokoll och dess funktioner i Azure.
>
>

Med stöd för Azure AD, kan mobila program använder samma enkelt mobiltjänster autentisering kan låta de anställda att logga in på sina mobila program med sina företagsuppgifter för Active Directory. Med den här funktionen stöds Azure AD som identitetsprovider i mobiltjänster tillsammans med de andra identitetsprovidrar som redan stöds (bland annat Microsoft-Accounts, Facebook-ID, Google-ID och Twitter-ID). Om lokala appar använder användarens autentiseringsuppgifter i företagets AD DS, vara åtkomst från partner och användare som kommer från molnet transparent. Du kan hantera användares villkorlig åtkomstkontroll till (molnbaserade) webbprogram, webb-API, Microsoft-molntjänster, SaaS-program från tredje part och interna (mobila) klientprogram och få fördelarna med säkerhet, granskning, rapporterar med någon Placera. Vi rekommenderar dock att validera implementeringen i en icke-produktionsmiljö eller med ett begränsat antal användare.

> [!TIP]
> Det är viktigt att nämna att Azure AD inte har en grupprincip som har AD DS. Om du vill framtvinga princip för enheter du behöver en lösning för hantering av mobila enheter som [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).
>
>

När användaren autentiseras med hjälp av Azure AD, är det viktigt att utvärdera åtkomstnivån som användaren har. Åtkomstnivån som användaren har över en resurs kan variera. Azure AD kan lägga till ett extra säkerhetslager genom att kontrollera åtkomst till vissa resurser, Tänk på att själva resursen kan också ha sin egen åtkomstkontrollistan separat, till exempel åtkomstkontroll för filer som finns på en filserver. Nedan sammanfattas nivåerna av åtkomstkontroll som du kan ha i ett scenario med hybridanvändning:

![](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Varje interaktion i diagrammet visade i bild X representerar en access control-scenario som kan omfattas av Azure AD. Nedan finner du en beskrivning av varje scenario:

  1. Villkorlig åtkomst till program som finns lokalt: du kan använda registrerade enheter med åtkomstprinciper för program som är konfigurerade för att använda AD FS i Windows Server 2012 R2.

  2. Åtkomstkontroll till Azure portal: Azure kan du styra åtkomst till portalen med hjälp av rollbaserad åtkomstkontroll (RBAC)). Den här metoden gör det möjligt för företaget att begränsa antalet åtgärder som en person kan göra i Azure-portalen. Genom att använda RBAC för att styra åtkomst till portalen kan kan IT-administratörer delegera åtkomst med hjälp av följande metoder för hantering av åtkomst:

   - Gruppbaserad rolltilldelning: du kan tilldela åtkomst till Azure AD-grupper som kan synkroniseras från din lokala Active Directory. På så sätt kan du utnyttja befintliga investeringar som din organisation har gjort i verktyg och processer för att hantera grupper. Du kan också använda delegerad grupp-funktionen för hantering av Azure AD Premium.
   - Använd inbyggda roller i Azure: du kan använda tre roller, ägare, deltagare och läsare, så att användare och grupper har behörighet till endast de aktiviteter som de behöver för att göra sina jobb.
   -  Detaljerad åtkomst till resurser: du kan tilldela roller till användare och grupper för en viss prenumeration, resursgrupp eller en enskild Azure-resurs, till exempel en webbplats eller i databasen. På så sätt kan du se till att användarna har åtkomst till alla resurser som de behöver och ingen åtkomst till resurser som de inte behöver hantera.

   > [!NOTE]
   > Om du skapar program och vill anpassa åtkomstkontrollen för dem, är det också möjligt att använda Azure AD-programroller för auktorisering. Den här [WebApp-RoleClaims-DotNet exempel](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) om hur du skapar din app för att använda den här funktionen.


  3. Villkorlig åtkomst för Office 365-program med Microsoft Intune: IT-administratörer kan etablera enhetsprinciper för villkorlig åtkomst för att skydda företagsresurser och samtidigt ge informationsarbetare på kompatibla enheter kommer åt tjänsterna. 
  
  4. Villkorlig åtkomst för Saas-appar: [den här funktionen](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) kan du konfigurera regler för åtkomst av programspecifika multifaktorautentisering och möjligheten att blockera åtkomst för användare inte på ett betrott nätverk. Du kan använda Multi-Factor authentication-regler för alla användare som är kopplade till programmet, eller enbart för användare i de angivna säkerhetsgrupperna. Användare kan uteslutas från Multi-Factor authentication-krav om de kommer åt programmet från en IP-adress som i i organisationens nätverk.

Jämförelse mellan dessa alternativ kan inte användas för den här uppgiften eftersom alternativen för åtkomstkontroll använder en multilayer metod. Se till att du använder sig av alla alternativ som är tillgängliga för varje scenario som kräver att du kan styra åtkomsten till dina resurser.

## <a name="define-incident-response-options"></a>Definiera alternativ för incidenthantering
Azure AD kan hjälpa IT-avdelningen att identitet potentiella säkerhetsrisker i miljön genom att övervaka användarens aktivitet. IT kan använda Azure AD Access och rapporter för att få insyn i integriteten och säkerheten i din organisations katalog i användning. Med den här informationen kan kan IT-administratör bättre avgöra var potentiella säkerhetsrisker finns så att de kan planera på lämpligt sätt du minskar riskerna.  [Azure AD Premium-prenumeration](../fundamentals/active-directory-get-started-premium.md) har en uppsättning av säkerhetsrapporter som kan aktivera IT för att hämta den här informationen. [Azure AD-rapporter](../reports-monitoring/overview-reports.md) kategoriseras enligt följande:

* **Avvikelseidentifiering rapporter**: innehålla inloggningshändelser som har identifierats vara avvikande. Målet är att se till att du är medveten om sådan aktivitet och gör att du kan fastställa om huruvida en händelse är misstänkta.
* **Integrerade program rapporten**: ger insikter om hur molnappar används i din organisation. Azure Active Directory möjliggör integrering med tusentals molnprogram.
* **Felrapporter**: visar fel som kan uppstå vid etablering av konton för att externa program.
* **Användarspecifika rapporter**: visa enheten/logga i aktivitetsdata för en viss användare.
* **Aktivitetsloggar**: innehåller en post för alla granskade händelser inom de senaste 24 timmarna, senaste 7 dagarna eller senaste 30 dagarna, samt ändringar av aktivitet och aktiviteten för återställning och registrering av lösenord.

> [!TIP]
> En annan rapport som kan också incidenthantering-teamet arbetar med ett ärende är den [användare med läckta autentiseringsuppgifter](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) rapporten. Den här rapporten ser du alla matchningar mellan listan läckta autentiseringsuppgifter och din klient.
>


Andra viktiga inbyggda rapporter i Azure AD som kan användas vid en incidenthanteringsundersökning och är:

* **Återställningsaktivitet för lösenord**: ge administratören insikter om hur aktivt för återställning av lösenord som används i organisationen.
* **Återställningsaktivitet för lösenord registrering**: innehåller information som användare har registrerat sina metoder för återställning av lösenord, och vilka metoder som de har valt.
* **Gruppera aktivitet**: innehåller en historik över ändringar i gruppen (ex: användare har lagts till eller tas bort) som inleddes i åtkomstpanelen.

Förutom core rapporteringsfunktionen i Azure AD Premium som du kan använda under en undersökning incidenthanteringsprocessen, IT-avdelningen kan också dra nytta av granskningsrapporten att få information som:

* Ändringar i rollmedlemskap (till exempel användare som lagts till i rollen som Global administratör)
* Uppdateringar av autentiseringsuppgifter (till exempel lösenordsändringar)
* Domänhantering (t.ex, verifiera en anpassad domän, ta bort en domän)
* Att lägga till eller ta bort program
* Hantering av användare (till exempel att lägga till, ta bort, uppdatera en användare)
* Att lägga till eller ta bort licenser

Jämförelse mellan dessa alternativ kan inte användas för den här uppgiften eftersom alternativen för incidenthantering använder en multilayer metod. Se till att du använder sig av alla alternativ som är tillgängliga för varje scenario som kräver att du kan använda rapporteringsfunktionen i Azure AD som en del av ditt företags incidenthanteringsprocess.

## <a name="next-steps"></a>Nästa steg
[Fastställa hanteringsuppgifter för hybrid identity](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Se även
[Översikt över design-överväganden](plan-hybrid-identity-design-considerations-overview.md)
