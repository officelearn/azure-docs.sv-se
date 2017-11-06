---
title: "Azure Active Directory hybrid identity designöverväganden - definiera en strategi för data protection | Microsoft Docs"
description: "Du måste ange data protection strategin för din hybrididentitetslösning att uppfylla kraven som du har definierat."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 6c9b7423fa56886104bc6060d25904277b75f30c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definiera en strategi för din hybrididentitetslösning dataskydd
I den här uppgiften definierar du strategin för din hybrididentitetslösning att uppfylla affärskrav som du definierade i dataskydd:

* [Fastställa kraven på dataskydd](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [Ange krav för innehållshantering](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Fastställa krav på åtkomstkontroll](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Fastställa kraven på incidentsvar](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definiera alternativ för skydd av data
Som anges i [fastställa kraven för directory-synkronisering](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md), Microsoft Azure AD kan synkronisera med din Active Directory Domain Services (AD DS) finns lokalt. Den här integreringen kan organisationer utnyttja Azure AD för att verifiera användarens autentiseringsuppgifter när de försöker komma åt företagets resurser. Detta kan göras för båda scenarierna: data i vila på lokalt och i molnet.  Åtkomst till data i Azure AD kräver användarautentisering via en säkerhetstokentjänst (STS).

När autentiseras, användarens huvudnamn (UPN) läses från token för autentisering och replikerade partition och behållare som motsvarar användarens domän är klart. Information om användarens existens aktiverat läge och rollen används av systemets tillstånd för att avgöra om den begärda åtkomsten till mål-klienten har behörighet för den här användaren i den här sessionen. Vissa auktoriserade åtgärder (skapa mer specifikt användare, återställning av lösenord) skapa en granskningslogg som en Innehavaradministratör kan använda för att hantera efterlevnad arbete eller undersökningar.

Flytta data från ditt lokala datacenter i Azure Storage via en Internet-anslutning kan inte alltid möjligt på grund av datavolymen, bandbreddstillgänglighet eller andra överväganden. Den [Azure Storage Import/Export Service](../storage/common/storage-import-export-service.md) innehåller en maskinvarubaserad alternativ för att placera/hämtning av stora mängder data i blob storage. Du kan skicka [BitLocker-krypterad](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) hårddiskar direkt till ett Azure-datacenter där molnoperatörer kommer att överföra innehållet till ditt lagringskonto eller de kan hämta Azure data till dina enheter att återgå till dig. Endast krypterade diskar accepteras för den här processen (med en BitLocker-nyckel som genererats av själva tjänsten under installationen jobb). BitLocker-nyckel har angetts för Azure separat, vilket ger out-of-band viktiga delar.

Eftersom data under överföringen kan ske i olika scenarier, är också relevant för vet att Microsoft Azure använder [virtuella nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) att isolera klienternas trafik från varandra, med åtgärder som värd och Gäst nivå brandväggar, IP-paketfilter, port blockerar och HTTPS-slutpunkter. De flesta av Azures intern kommunikation, inklusive infrastruktur-infrastruktur och infrastruktur-till-kund (lokalt), är också krypterade. Ett annat viktigt scenario är kommunikation inom Azure-Datacenter; Microsoft hanterar nätverk för att garantera att inga Virtuella kan personifiera eller eavesdrop på en annan IP-adress. TLS/SSL används för att få åtkomst till Azure Storage eller Azure SQL-databaser eller när du ansluter till molntjänster. I det här fallet ansvarar kundens administratör för att erhålla ett TLS/SSL-certifikat och distribuera den till sina innehavare infrastruktur. Data flyttas trafik mellan virtuella datorer i samma distribution eller mellan klienter i en enda distribution via Microsoft Azure Virtual Network kan skyddas via krypterad kommunikation, till exempel HTTPS eller SSL/TLS-protokoll.

Beroende på hur du besvarade frågorna i [fastställa kraven på dataskydd](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md), du ska kunna avgöra hur du vill skydda dina data och hur hybrididentitetslösning kan hjälpa dig på den. Tabellen visar de alternativ som stöds av Azure och som är tillgängliga för varje scenario för skydd av data.

| Alternativ för skydd av data | I vila i molnet | Vid rest lokala | Under överföring |
| --- | --- | --- | --- |
| BitLocker-diskkryptering |X |X | |
| SQL Server för att kryptera databaser |X |X | |
| VM-till-VM-kryptering | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Läs [efterlevnad av funktionen](https://azure.microsoft.com/support/trust-center/services/) på [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) vill veta mer om certifikat som överensstämmer med varje Azure-tjänsten.
> Jämförelse mellan dessa alternativ kan inte användas för den här uppgiften eftersom alternativen för data protection använder en multilayer metod. Se till att du använder sig av alla alternativ som är tillgängliga för varje tillstånd som data ska vara.
>
>

## <a name="define-content-management-options"></a>Definiera alternativ för innehållshantering
Fördelen med att använda Azure AD för att hantera en hybrididentitetsinfrastruktur är att processen är helt transparent ur användarens perspektiv. Användaren försöker komma åt en delad resurs, resursen kräver autentisering måste användaren skicka en autentiseringsbegäran till Azure AD för att hämta token och åtkomst till resursen. Hela processen sker i bakgrunden utan interaktion från användaren. Det är också möjligt att tilldela behörighet för en [grupp](active-directory-manage-groups.md#getting-started-with-access-management) användare så att de kan utföra vissa vanliga åtgärder.

Organisationer som är skadliga om datasekretess vanligtvis kräver dataklassificering för sina lösningar. Om den aktuella lokala infrastrukturen redan använder dataklassificering, är det möjligt att använda Azure AD som den centrala lagringsplatsen för användarens identitet. Ett gemensamma verktyg för att det är används lokalt för dataklassificering kallas [Dataklassificering](https://msdn.microsoft.com/library/Hh204743.aspx) för Windows Server 2012 R2. Det här verktyget kan bidra till att identifiera, klassificera och skydda data på filservrar i ditt privata moln. Det är också möjligt att utnyttja den [automatisk Filklassificering](https://technet.microsoft.com/library/hh831672.aspx) i Windows Server 2012 för att åstadkomma detta.

Om din organisation inte har dataklassificering på plats, men behöver skydda känsliga filer utan att lägga till nya servrar lokalt, kan de använda Microsoft [Azure Rights Management Service](https://technet.microsoft.com/library/JJ585026.aspx).  Azure RMS använder krypterings-, identitets- och auktoriseringsprinciper för att skydda dina filer och e-post och den fungerar på flera enheter – telefoner, surfplattor och datorer. Eftersom Azure RMS är en molnbaserad tjänst, finns behöver du inte uttryckligen konfigurera förtroenden med andra organisationer innan du kan dela skyddat innehåll med dem. Samarbete mellan olika organisationer stöds automatiskt om de redan har en Office 365 eller Azure AD-katalog. Du kan också synkronisera enbart det katalogattribut som Azure RMS måste ha stöd för en gemensam identitet för din lokala Active Directory-konton med hjälp av Azure Active Directory Sync Services (AAD Sync) eller Azure AD Connect.

En viktig del av innehållshantering är att förstå vem har åtkomst till vilka resurser, en omfattande loggningsfunktioner är därför viktigt för lösning för Identitetshantering. Azure AD innehåller logg över 30 dagar, inklusive:

* Ändringar i medlemskap (ex: användare som lagts till i rollen Global administratör)
* Autentiseringsuppgifter uppdateringar (ex: lösenordsändringar)
* Domänhantering (ex: Verifiera en anpassad domän, ta bort en domän)
* Lägga till eller ta bort program
* Användarhantering (ex: lägga till, ta bort, uppdaterar en användare)
* Lägga till eller ta bort licenser

> [!NOTE]
> Läs [Microsoft Azure-säkerhet och hantering av granskningslogg](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) vill veta mer om funktioner för loggning i Azure.
> Beroende på hur du besvarade frågorna i [fastställa kraven för innehållshantering](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md), du ska kunna bestämma hur du vill att innehållet som ska hanteras i din hybrididentitetslösning. Alla alternativ som visas i tabell 6 är kan integreras med Azure AD, är det viktigt att definiera vilket är mer lämpliga för dina affärsbehov.
>
>

| Alternativen för innehållshantering | Fördelar | Nackdelar |
| --- | --- | --- |
| Lokalt centraliserad (Active Directory Rights Management Server) |Fullständig kontroll över serverinfrastrukturen ansvarar för att klassificera data <br> Inbyggda funktioner i Windows Server, behöver inte extra licens eller prenumeration <br> Kan integreras med Azure AD i ett hybridscenario <br> Stöder information rights management (IRM) funktioner i Microsoft Online services, till exempel Exchange Online och SharePoint Online, samt Office 365 <br> Har stöd för lokala Microsoft server-produkter, till exempel Exchange Server, SharePoint Server och filservrar som kör Windows Server och Filklassificeringsinfrastruktur (FCI). |Högre, Underhåll (hålla jämna steg med uppdateringar, konfiguration och eventuella uppgraderingar), eftersom IT äger servern <br> Kräv en serverinfrastruktur lokala<br> Doesn'tleverage funktioner i Azure internt |
| Centraliserad i molnet (Azure RMS) |Enklare att hantera jämfört med lösningen lokalt <br> Kan integreras med AD DS i ett hybridscenario <br>  Helt integrerat med Azure AD <br> Kräver inte en server lokalt för att distribuera tjänsten <br> Har stöd för lokala Microsoft server-produkter, till exempel Exchange Server, SharePoint, Server och filservrar som kör Windows Server och File Classification Infrastructure (FCI) <br> IT-avdelningen, kan ha fullständig kontroll över sina klientnyckel med BYOK-kapacitet. |Din organisation måste ha en molnprenumeration med stöd för RMS <br> Din organisation måste ha en Azure AD-katalog för stöd av användarautentisering för RMS |
| Hybrid (Azure RMS är integrerad med, lokala Active Directory Rights Management Server) |Det här scenariot ackumuleras fördelar för både, centraliserad lokalt och i molnet. |Din organisation måste ha en molnprenumeration med stöd för RMS <br> Din organisation måste ha en Azure AD-katalog för stöd av användarautentisering för RMS, <br> Kräver en anslutning mellan Azure-molntjänst och lokal infrastruktur |

## <a name="define-access-control-options"></a>Definiera alternativ för åtkomstkontroll
Genom att använda autentisering, auktorisering och styra funktioner som är tillgängliga i Azure AD som du kommer att kunna aktivera ditt företag för att använda en central identitetsdatabas samtidigt som användarna och partners för att använda enkel inloggning (SSO) som visas i bilden nedan:

![](./media/hybrid-id-design-considerations/centralized-management.png)

Centraliserad hantering och fullständigt integrering med andra kataloger

Azure Active Directory tillhandahåller enkel inloggning till tusentals SaaS-program och lokala webbprogram. Läs den [kompatibilitetslista för Azure Active Directory federation: tredjeparts identitetsleverantörer som kan användas för att implementera enkel inloggning](https://msdn.microsoft.com/library/azure/jj679342.aspx) artikeln för mer information om den SSO från tredje parten som har testats av Microsoft. Den här funktionen gör det möjligt för organisation att implementera en mängd olika B2B-scenarier samtidigt som kontrollen över identitets- och åtkomsthantering. Under B2B är designa processen dock viktigt att förstå den autentiseringsmetod som ska användas av partnern och kontrollera om den här metoden stöds av Azure. Det här är för närvarande metoder som stöds av Azure AD:

* Security Assertion Markup Language (SAML)
* OAuth
* Kerberos
* Token
* Certifikat

> [!NOTE]
> läsa [Azure Active Directory-autentiseringsprotokoll](https://msdn.microsoft.com/library/azure/dn151124.aspx) vill veta mer information om varje protokoll och dess funktioner i Azure.
>
>

Använda Azure AD-stöd, mobil business program kan använda samma enkelt autentiseringsupplevelse för Mobile Services så att anställda kan logga in på sina mobila program med sina företagsuppgifter för Active Directory. Med den här funktionen stöds Azure AD som en identitetsleverantör i Mobile Services tillsammans med de andra identitetsleverantörer vi stöder redan (som omfattar Microsoft Accounts, Facebook-ID, ID för Google och Twitter-ID). Om lokala appar använder användarens autentiseringsuppgifter på företagets AD DS, bör du vara transparent åtkomst från partner och användare som kommer från molnet. Du kan hantera användares villkorlig åtkomstkontroll till (molnbaserad) webbprogram, webb-API, Microsoft-molntjänster, 3 part SaaS-program och interna () mobilklientprogram och få fördelarna med säkerhet, granskning, rapportering allt på ett ställe. Det rekommenderas dock kontrollera detta i en produktionsmiljö eller med en begränsad mängd användare.

> [!TIP]
> Det är viktigt att nämna att Azure AD inte har en grupprincip som har AD DS. För att framtvinga principer för enheter som du behöver en lösning för hantering av mobila enheter som [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).
>
>

När användaren autentiseras med hjälp av Azure AD, är det viktigt att utvärdera åtkomstnivån som användaren ska ha. Åtkomstnivån som användaren ska ha över en resurs kan variera, Azure AD kan lägga till ett ytterligare säkerhetsskikt genom att kontrollera åtkomst till vissa resurser, du måste också tänka på att resursen själva kan också ha sin egen åtkomstkontrollistan separat, till exempel åtkomstkontroll för filer som finns på en filserver. Bilden nedan sammanfattas nivåer för åtkomstkontroll som du kan ha i ett hybridscenario:

![](./media/hybrid-id-design-considerations/accesscontrol.png)

Varje interaktion i diagrammet på bild X representerar en access control-scenario som kan omfattas av Azure AD. Nedan finner du en beskrivning av varje scenario:

1. Villkorlig åtkomst till program som finns lokalt: du kan använda registrerade enheter med åtkomstprinciper för program som är konfigurerade för att använda AD FS i Windows Server 2012 R2. Mer information om hur du konfigurerar lokal villkorlig åtkomst finns i [Konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory Device Registration](active-directory-conditional-access.md).

2. Åtkomstkontroll på Azure-portalen: Azure kan du styra åtkomst till portalen genom att använda rollbaserad åtkomstkontroll (RBAC)). Den här metoden kan företag att begränsa mängden operationer som en enskild person kan göra i Azure-portalen. Genom att använda RBAC för att styra åtkomsten till portalen kan kan IT-administratörer delegera åtkomst med hjälp av följande metoder för hantering av åtkomst:

* Gruppbaserade rolltilldelning: du kan tilldela åtkomst till Azure AD-grupper som kan synkroniseras från din lokala Active Directory. På så sätt kan du utnyttja befintliga investeringar som organisationen har gjort i verktygsuppsättning och processer för att hantera grupper. Du kan också använda funktionen för hantering av delegerad grupp i Azure AD Premium.
* Utnyttjar inbyggda roller i Azure: du kan använda tre roller, ägare, deltagare och läsare, så att användare och grupper har behörighet att utföra de uppgifter som de behöver för att utföra sitt arbete.
* Detaljerade åtkomst till resurser: du kan tilldela roller till användare och grupper för en viss prenumeration, resursgrupp eller en enskild Azure resurs, till exempel en webbplats eller i databasen. På så sätt kan du se till att användarna har åtkomst till alla resurser som de behöver och ingen åtkomst till resurser som de inte behöver hantera.

> [!NOTE]
> Om du skapar program och vill anpassa åtkomstkontrollen för dem, är det också möjligt att använda Azure AD Application roller för auktorisering. Granska det [WebApp-RoleClaims-DotNet exempel](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) på hur du skapar din app om du vill använda den här funktionen.
>
>

3. Villkorlig åtkomst för Office 365-program med Microsoft Intune: IT-administratörer kan etablera enhetsprinciper för villkorlig åtkomst för att skydda företagsresurser och samtidigt ge informationsarbetare på kompatibla enheter åtkomst till tjänsterna. Mer information finns i [Enhetsprinciper för villkorlig åtkomst för Office 365-tjänster](active-directory-conditional-access-device-policies.md).

4. Villkorlig åtkomst för Saas-appar: [funktionen](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx) kan du konfigurera regler för åtkomst av programspecifika multifaktorautentisering och möjligheten att blockera åtkomst för användare inte på ett betrott nätverk. Du kan använda regler för multifaktorautentisering för alla användare som är kopplade till programmet, eller enbart för användare i de angivna säkerhetsgrupperna. Användare kan uteslutas från Multi-Factor authentication-krav om de använder programmet från en IP-adress som i i organisationens nätverk.

Jämförelse mellan dessa alternativ kan inte användas för den här uppgiften eftersom alternativen för åtkomstkontroll använder en multilayer metod. Se till att du använder sig av alla alternativ som är tillgängliga för varje scenario som kräver att du kan styra åtkomst till resurser.

## <a name="define-incident-response-options"></a>Definiera alternativ för incidenter
Azure AD hjälper IT att identitet potentiella säkerhetsrisker i miljön genom att övervaka användarens aktivitet IT kan dra nytta av Azure AD-åtkomst och rapporter möjlighet att få insyn i integritet och säkerheten för din organisations katalog. Med den här informationen kan bestämma IT-administratör bättre där möjliga säkerhetsrisker kan ligga så att de kan planera på lämpligt sätt du minskar riskerna.  [Azure AD Premium-prenumeration](active-directory-get-started-premium.md) har en uppsättning säkerhetsrapporter som gör att IT-avdelningen att hämta den informationen. [Azure AD-rapporter](active-directory-view-access-usage-reports.md) kategoriseras enligt nedan:

* **Avvikelseidentifiering rapporter**: innehåller händelser som vi avvikande inloggning. Vårt mål är att se till att du är medveten om sådan aktivitet och gör att du ska kunna fastställa om en händelse är misstänkt.
* **Integrerade program rapporten**: ger insikter om hur molnprogram används i din organisation. Azure Active Directory möjliggör integrering med tusentals molnprogram.
* **Felrapporter**: ange fel som kan uppstå vid etablering av konton till externa program.
* **Användarspecifika rapporter**: visa enheten/logga i aktivitetsdata för en viss användare.
* **Aktivitetsloggar**: innehåller en post på alla granskade händelser inom de senaste 24 timmarna, senaste 7 dagarna eller senaste 30 dagarna, samt ändringar av aktiviteten och aktiviteten för återställning och registrering av lösenord.

> [!TIP]
> En annan rapport som kan också Incident svarsgrupp som arbetar med ett ärende är den [användare med läckta autentiseringsuppgifter](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx) rapporten.  Den här rapporten hämtar alla matchningar mellan dessa läckta autentiseringsuppgifter lista och din klient.
>
>

Andra viktiga inbyggda rapporter i Azure AD som kan användas vid en undersökning av incidenter och är:

* **Lösenordsåterställningsaktivitet**: ge administratören insikter om hur aktivt återställning av lösenord som används i organisationen.
* **Återställningsaktivitet för lösenord registrering**: ger insikter som användare har registrerat sina metoder för återställning av lösenord, och vilka metoder som de har valt.
* **Gruppera aktiviteten**: innehåller en historik över ändringarna i gruppen (ex: användare läggs till eller tas bort) som har initierats på åtkomstpanelen.

Förutom den core reporting funktionen som är tillgängliga i Azure AD Premium kan utnyttjas under en Incident svar undersökningen IT kan utnyttja granska rapporten för att få information som:

* Ändringar i medlemskap (ex: användare som lagts till i rollen Global administratör)
* Autentiseringsuppgifter uppdateringar (ex: lösenordsändringar)
* Domänhantering (ex: Verifiera en anpassad domän, ta bort en domän)
* Lägga till eller ta bort program
* Användarhantering (ex: lägga till, ta bort, uppdaterar en användare)
* Lägga till eller ta bort licenser

Jämförelse mellan dessa alternativ kan inte användas för den här uppgiften eftersom alternativen för incidenter använder en multilayer metod. Se till att du använder sig av alla alternativ som är tillgängliga för varje scenario som kräver att du använder Azure AD reporting kapaciteten som en del av ditt företags incidenter.

## <a name="next-steps"></a>Nästa steg
[Fastställa hanteringsuppgifter för hybrid identity](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Se även
[Översikt över design-överväganden](active-directory-hybrid-identity-design-considerations-overview.md)
