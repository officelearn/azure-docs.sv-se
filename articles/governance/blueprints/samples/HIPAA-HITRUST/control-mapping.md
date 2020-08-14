---
title: HIPAA HITRUST skiss exempel kontroller
description: Styr mappningen av HIPAA HITRUST-skiss exemplen. Varje kontroll mappas till en eller flera Azure-principer som hjälper till med utvärderingen.
ms.date: 08/03/2020
ms.topic: sample
ms.openlocfilehash: 10b771e3cfb18a28bd720332a26e13bb1d1f6022
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209415"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Kontroll mappning av HIPAA HITRUST skiss-exemplet

Följande artikel beskriver hur Azure-skisser HIPAA HITRUST skiss-exemplet mappar till HIPAA HITRUST-kontrollerna. Mer information om kontrollerna finns i [HIPAA HiTRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Följande mappningar är till **HIPAA HiTRUST** -kontrollerna. Använd navigeringen till höger om du vill gå direkt till en bestämd kontroll mappning. Många av de mappade kontrollerna implementeras med ett [Azure policy](../../../policy/overview.md) initiativ. Om du vill granska hela initiativet öppnar du **princip** i Azure Portal och väljer sidan **definitioner** . Leta sedan reda på och välj ** \[ förhands granskningen \] : Audit HIPAA HiTRUST kontrollerar** det inbyggda princip initiativet.

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../../../policy/overview.md) -definitioner. Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som avser själva principerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Standarden för efterlevnad innehåller dessutom kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan kontroller och Azure Policy definitioner för det här skiss exemplet för efterlevnad kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Kontroll mot skadlig kod

Den här skissen hjälper dig att hantera Endpoint Protection, inklusive skadlig kod skydd, genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som övervakas för saknade Endpoint Protection på virtuella datorer i Azure Security Center och tillämpa Microsoft Antimalware-lösningen på virtuella Windows-datorer.

- Microsoft Antimalware för Azure bör konfigureras att automatiskt uppdatera skydds-signaturer
- Övervaka saknade Endpoint Protection i Azure Security Center
- Endpoint Protection-lösningen bör installeras på virtuella datorers skalnings uppsättningar
- Anpassningsbara program kontroller ska vara aktiverade på virtuella datorer


## <a name="management-of-removable-media"></a>Hantering av flyttbara medier

Organisationen, baserat på data klassificerings nivån, registrerar media (inklusive bärbara datorer) före användningen, placerar rimliga begränsningar för hur sådana medier används och ger en lämplig nivå av fysiskt och logiskt skydd (inklusive kryptering) för medier som innehåller information som omfattas tills den är korrekt förstörd eller sanerad.

- transparent datakryptering på SQL-databaser ska aktive ras
- Disk kryptering bör tillämpas på virtuella datorer
- Ej anslutna diskar ska vara krypterade
- Kräv kryptering för Data Lake Store-konton
- SQL Server TDE-skyddskomponenten bör vara krypterat med din egen nyckel
- SQL-hanterad instans TDE-skydd ska vara krypterat med din egen nyckel

## <a name="control-of-operational-software"></a>Kontroll över operativa program 

Organisationen identifierar otillåten program vara i informations systemet, inklusive servrar, arbets stationer och bärbara datorer, använder en princip för Tillåt-alla, neka-by-Exception för att förhindra körning av kända otillåten program vara på informations systemet och granskar och uppdaterar listan över obehöriga program regelbundet, men inte mindre än årligen.

- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas
- Säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas
- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Anpassningsbara program kontroller ska vara aktiverade på virtuella datorer

## <a name="change-control-procedures"></a>Ändra kontroll procedurer

Integriteten för alla avbildningar av virtuella datorer är alltid säkerställd genom att logga och höja en avisering för alla ändringar som görs i avbildningar av virtuella datorer och göra dem tillgängliga för företags ägare och/eller kunder via elektroniska metoder (t. ex. portaler eller aviseringar) resultatet av en ändring eller flyttning och den efterföljande verifieringen av avbildningens integritet.

- \[Förhandsgranska \] visar gransknings resultat från konfiguration av virtuella Windows-datorer i system gransknings principer-detaljerad spårning
- \[För hands version \] : Visa gransknings resultat från konfigurationer för virtuella Windows-datorer i system gransknings principer-detaljerad spårning

## <a name="control-of-technical-vulnerabilities"></a>Kontroll av tekniska sårbarheter 

Det finns en strikt konfigurations standard för alla system-och nätverks komponenter.

- Sårbarhets bedömning bör vara aktiverat på dina SQL-servrar
- Sårbarhets bedömning ska vara aktiverat på SQL-hanterade instanser
- \[För hands version \] av sårbarhets bedömning ska aktive ras på Virtual Machines
- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning
- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas
- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas
- Säkerhets risker i SQL-databaser bör åtgärdas
- \[För hands version \] : Pod-säkerhetsprinciper bör definieras för Kubernetes-tjänster

## <a name="segregation-in-networks"></a>Uppdelning i nätverk

Organisationens säkerhetsgatewayer (t. ex. brand väggar) tillämpar säkerhets principer och är konfigurerade för att filtrera trafik mellan domäner, blockera obehörig åtkomst och används för att upprätthålla uppdelning mellan interna tråd bundna, interna trådlösa och externa nätverks segment (t. ex. Internet), inklusive DMZs och tvingande principer för åtkomst kontroll för varje domän.

- Undernät ska associeras med en nätverks säkerhets grupp
- Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk
- Virtuella datorer ska associeras med en nätverks säkerhets grupp
- Service Bus bör använda en tjänst slut punkt för virtuellt nätverk
- App Service bör använda en tjänst slut punkt för virtuellt nätverk
- SQL Server bör använda en tjänst slut punkt för virtuellt nätverk
- Event Hub bör använda en tjänst slut punkt för virtuellt nätverk
- Cosmos DB bör använda en tjänst slut punkt för virtuellt nätverk
- Key Vault bör använda en tjänst slut punkt för virtuellt nätverk
- Gateway-undernät ska inte konfigureras med en nätverks säkerhets grupp
- Lagrings konton bör använda en tjänst slut punkt för virtuellt nätverk
- \[För hands version \] : container Registry bör använda en tjänst slut punkt för virtuellt nätverk
- Rekommendationer för anpassningsbar nätverks härdning bör tillämpas på interna virtuella datorer som är riktade mot varandra

## <a name="network-connection-control"></a>Nätverks anslutnings kontroll

Nätverks trafiken kontrol leras i enlighet med organisationens princip för åtkomst kontroll via brand vägg och andra nätverksrelaterade begränsningar för varje nätverks åtkomst punkt eller extern telekommunikations tjänsts hanterade gränssnitt.

- Säker överföring till lagringskonton ska vara aktiverat
- Den senaste TLS-versionen ska användas i API-AP
- Den senaste TLS-versionen ska användas i din webbapp
- Den senaste TLS-versionen ska användas i Funktionsapp
- Funktionsapp bör endast vara tillgängligt via HTTPS
- Webb program bör endast vara tillgängliga via HTTPS
- API-appen bör bara vara tillgänglig via HTTPS
- Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar
- Tvinga SSL-anslutning ska vara aktive rad för PostgreSQL-databas servrar
- Endast säkra anslutningar till din Redis Cache ska vara aktiverade
- Undernät ska associeras med en nätverks säkerhets grupp
- NSG: er-reglerna för webb program på IaaS bör vara härdade
- Regler för nätverks säkerhets grupper för virtuella datorer som riktas mot Internet bör vara skärpta
- Virtuella datorer ska anslutas till ett godkänt virtuellt nätverk
- Virtuella datorer ska associeras med en nätverks säkerhets grupp

## <a name="network-controls"></a>Nätverks kontroller

Organisationen använder säkra och krypterade kommunikations kanaler när du migrerar fysiska servrar, program eller data till virtualiserade servrar.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer
- Rekommendationer för anpassningsbar nätverks härdning bör tillämpas på interna virtuella datorer som är riktade mot varandra
- Service Bus bör använda en tjänst slut punkt för virtuellt nätverk
- App Service bör använda en tjänst slut punkt för virtuellt nätverk
- SQL Server bör använda en tjänst slut punkt för virtuellt nätverk
- Event Hub bör använda en tjänst slut punkt för virtuellt nätverk
- Cosmos DB bör använda en tjänst slut punkt för virtuellt nätverk
- Key Vault bör använda en tjänst slut punkt för virtuellt nätverk
- Granska obegränsad nätverks åtkomst till lagrings konton
- Lagrings konton bör använda en tjänst slut punkt för virtuellt nätverk
- \[För hands version \] : container Registry bör använda en tjänst slut punkt för virtuellt nätverk

## <a name="security-of-network-services"></a>Säkerhet för Network Services

Överenskomna tjänster som tillhandahålls av en nätverks tjänst leverantör eller hanterare hanteras formellt och övervakas för att säkerställa att de tillhandahålls på ett säkert sätt.

- \[För hands version \] : data insamlings agenten för nätverks trafik bör installeras på virtuella Windows-datorer
- \[För hands version \] : agenten för data insamling för nätverks trafik bör installeras på virtuella Linux-datorer Network Watcher måste vara aktive rad

## <a name="information-exchange-policies-and-procedures"></a>Information Exchange-principer och-procedurer

Organisationen begränsar användningen av organisations kontrollerade flyttbara lagrings medier av behöriga personer på externa informations system.

- Se till att WEBBAPP har "klient certifikat (inkommande klient certifikat)" inställd på "på"
- CORS bör inte tillåta alla resurser åtkomst till dina webb program
- CORS bör inte tillåta alla resurser att komma åt dina funktions program
- CORS bör inte tillåta alla resurser åtkomst till din API-app
- Fjärrfelsökning bör inaktive ras för webb program
- Fjärrfelsökning bör inaktive ras för Function-appar
- Fjärrfelsökning bör inaktive ras för API Apps

## <a name="on-line-transactions"></a>Online-transaktioner

Organisationen kräver att kryptering används mellan, och användningen av elektroniska signaturer av, var och en av de parter som ingår i transaktionen. Organisationen säkerställer att lagringen av transaktions informationen finns utanför alla offentligt tillgängliga miljöer (t. ex. på en lagrings plattform som finns på organisationens intranät) och inte behålls och exponeras på ett lagrings medium som är direkt tillgängligt från Internet. där en betrodd utfärdare används (t. ex. för att utfärda och underhålla digitala signaturer och/eller digitala certifikat) är säkerheten integrerad och inbäddad i hela processen för hantering av certifikat/signaturer i hela slut punkt till slut punkt.

- Säker överföring till lagringskonton ska vara aktiverat
- Den senaste TLS-versionen ska användas i API-AP
- Den senaste TLS-versionen ska användas i din webbapp
- Den senaste TLS-versionen ska användas i Funktionsapp
- Funktionsapp bör endast vara tillgängligt via HTTPS
- Webb program bör endast vara tillgängliga via HTTPS
- API-appen bör bara vara tillgänglig via HTTPS
- Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar
- Tvinga SSL-anslutning ska vara aktive rad för PostgreSQL-databas servrar
- Endast säkra anslutningar till din Redis Cache ska vara aktiverade
- \[För hands version \] : Distribuera krav för att granska virtuella Windows-datorer som inte innehåller de angivna certifikaten i den betrodda roten
- \[För hands version \] : Visa gransknings resultat från virtuella Windows-datorer som inte innehåller de angivna certifikaten i den betrodda roten

## <a name="user-password-management"></a>Hantering av användar lösen ord

Lösen ord krypteras vid överföring och lagring på alla system komponenter.

- \[Förhandsgranska \] virtuella datorer med säkerhets inställningar för lösen ord

## <a name="user-authentication-for-external-connections"></a>Användarautentisering för externa anslutningar

Starka autentiseringsmetoder, till exempel Multi-Factor, RADIUS eller Kerberos (för privilegie rad åtkomst) och CHAP (för kryptering av autentiseringsuppgifter för fjärr anslutnings metoder) implementeras för alla externa anslutningar till organisationens nätverk.

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- MFA ska vara aktiverat på konton med Skriv behörighet för din prenumeration
- MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration
- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer

## <a name="user-identification-and-authentication"></a>Användar identifiering och autentisering

Användare som utförde privilegierade funktioner (t. ex. system administration) använder separata konton när de utför de privilegierade funktionerna. Multi-Factor Authentication-metoder används i enlighet med organisations principer (t. ex. för fjärråtkomst till fjärrnätverk).

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- MFA ska vara aktiverat på konton med Skriv behörighet för din prenumeration
- MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration
- Högst 3 ägare bör anges för din prenumeration
- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Distribuera krav för att granska virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna
- Visa gransknings resultat från virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna
- Distribuera krav för att granska virtuella Windows-datorer där gruppen administratörer inte innehåller alla angivna medlemmar
- Visa gransknings resultat från virtuella Windows-datorer där gruppen administratörer inte innehåller alla angivna medlemmar
- Distribuera krav för att granska virtuella Windows-datorer där gruppen administratörer inte bara innehåller de angivna medlemmarna
- Visa gransknings resultat från virtuella Windows-datorer där gruppen administratörer inte bara innehåller de angivna medlemmarna

## <a name="privilege-management"></a>Behörighets hantering

Åtkomst till hanterings funktioner eller administrativa konsoler för system som är värdar för virtualiserade system är begränsade till personal baserat på principen om minsta behörighet och support genom tekniska kontroller.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer
- Hanterings portar bör stängas på dina virtuella datorer
- Högst 3 ägare bör anges för din prenumeration
- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Granska användningen av anpassade RBAC-regler
- Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services

## <a name="review-of-user-access-rights"></a>Granskning av användar behörighet

Organisationen underhåller en dokumenterad lista över behöriga användare av informations till gångar.

- Granska användningen av anpassade RBAC-regler

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Fjärrdiagnostik och konfiguration av port skydd

Portar, tjänster och liknande program som är installerade på en dator eller ett nätverks system, som inte särskilt krävs för företags funktioner, är inaktiverade eller borttagna.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer
- Hanterings portar bör stängas på dina virtuella datorer
- Fjärrfelsökning bör inaktive ras för webb program
- Fjärrfelsökning bör inaktive ras för Function-appar
- Fjärrfelsökning bör inaktive ras för API Apps
- Anpassningsbara program kontroller ska vara aktiverade på virtuella datorer

## <a name="audit-logging"></a>Gransknings loggning

Loggar av meddelanden som skickas och tas emot underhålls inklusive datum, tid, ursprung och mål för meddelandet, men inte innehållet. Granskning är alltid tillgängligt när systemet är aktivt och spårar viktiga händelser, lyckade/misslyckade data åtkomst, ändringar i systemets säkerhets konfiguration, privilegie rad eller användning, eventuella larm, aktivering och inaktive ring av skydds system (t. ex. A/V och ID), aktivering och inaktive ring av identifierings-och autentiseringsmekanismer samt skapande och borttagning av objekt på objekt.

- Diagnostikloggar i Azure Data Lake Store ska vara aktive rad
- Diagnostikloggar i Logic Apps ska vara aktive rad 
- Diagnostikloggar i IoT Hub ska vara aktive rad 
- Diagnostikloggar i batch-konton måste vara aktiverade 
- Diagnostikloggar i Virtual Machine Scale Sets ska vara aktive rad 
- Diagnostikloggar i Händelsehubben måste vara aktive rad 
- Diagnostikloggar i search Services ska vara aktive rad 
- Diagnostikloggar i App Services ska vara aktive rad 
- Diagnostikloggar i Data Lake Analytics ska vara aktive rad 
- Diagnostikloggar i Key Vault ska vara aktive rad 
- Diagnostikloggar i Service Bus ska vara aktive rad
- Diagnostikloggar i Azure Stream Analytics ska vara aktive rad
- Granskning på SQL Server måste vara aktiverat
- Granska diagnostikinställning
- Azure Monitor ska samla in aktivitets loggar från alla regioner

## <a name="monitoring-system-use"></a>Övervaka system användning

Automatiserade system som distribueras i hela organisationens miljö används för att övervaka nyckel händelser och avvikande aktivitet och analysera system loggar, vars resultat granskas regelbundet. Övervakning omfattar privilegierade åtgärder, auktoriserad åtkomst eller obehörig åtkomst, inklusive försök att komma åt inaktiverade konton och system varningar eller-haverier.

- Azure Monitor ska samla in aktivitets loggar från alla regioner
- Log Analytics agenten ska installeras på virtuella datorer
- Log Analytics agenten ska installeras på Virtual Machine Scale Sets
- \[För hands version \] : Distribuera förutsättningar för att granska virtuella Windows-datorer där Log Analytics-agenten inte är ansluten som förväntat
- \[För hands version \] : Visa gransknings resultat från virtuella Windows-datorer där Log Analytics-agenten inte är ansluten som förväntat
- Azure Monitor logg profil ska samla in loggar för kategorier "Write," Delete "och" Action "
- Automatisk etablering av Log Analytics övervaknings agenten ska vara aktive rad för din prenumeration

## <a name="segregation-of-duties"></a>Ansvars fördelning

Separering av uppgifter används för att begränsa risken för obehörig eller oavsiktlig ändring av information och system. Ingen enskild person kan komma åt, ändra eller använda informations system utan auktorisering eller identifiering. Åtkomst för personer som ansvarar för administration och åtkomst kontroll begränsas till den lägsta som krävs baserat på varje användares roll och ansvar och dessa personer kan inte komma åt gransknings funktioner som är relaterade till dessa kontroller.

- Rollbaserad Access Control (RBAC) ska användas på Kubernetes Services
- Granska användningen av anpassade RBAC-regler
- \[För hands version \] : Distribuera förutsättningar för att granska Windows-konfigurationer för virtuella datorer i tilldelning av användar rättigheter
- \[För hands version \] : Visa gransknings resultat från konfigurationer för virtuella Windows-datorer i tilldelning av användar rättigheter
- \[För hands version \] : Distribuera förutsättningar för att granska Windows-konfigurationer för virtuella datorer i säkerhets alternativ-User Account Control
- \[För hands version \] : Visa gransknings resultat från konfigurationer för virtuella Windows-datorer i säkerhets alternativ-User Account Control
- Roller för anpassade prenumerationer får inte finnas

## <a name="administrator-and-operator-logs"></a>Administratörs-och operatörs loggar

Organisationen ser till att korrekt loggning är aktiverat för att kunna granska administratörs aktiviteter. och granskar system administratören och operatörs loggar regelbundet.

- Det bör finnas en aktivitets logg avisering för vissa administrativa åtgärder

## <a name="identification-of-risks-related-to-external-parties"></a>Identifiering av risker relaterade till externa parter

Fjärråtkomstanslutningar mellan organisationen och externa parter är krypterade

- Säker överföring till lagringskonton ska vara aktiverat
- Funktionsapp bör endast vara tillgängligt via HTTPS
- Webb program bör endast vara tillgängliga via HTTPS
- API-appen bör bara vara tillgänglig via HTTPS
- Tvinga SSL-anslutning ska vara aktive rad för MySQL-databas servrar
- Tvinga SSL-anslutning ska vara aktive rad för PostgreSQL-databas servrar
- Endast säkra anslutningar till din Redis Cache ska vara aktiverade

## <a name="business-continuity-and-risk-assessment"></a>Verksamhets kontinuitet och riskbedömning

Organisationen identifierar sina kritiska affärs processer och integrerar kraven för informations säkerhets hantering i affärs kontinuitet med andra kontinuitets krav som rör sådana aspekter som drift, personal, material, transport och anläggningar.

- Granska virtuella datorer utan haveri beredskap har kon figurer ATS
- Key Vault objekt ska vara återställnings bara
- \[För hands version \] : Distribuera förutsättningar för att granska Windows-konfigurationer för virtuella datorer i säkerhets alternativ-återställnings konsolen
- \[Förhandsgranska \] visar gransknings resultat från konfiguration av virtuella Windows-datorer i säkerhets alternativ-återställnings konsolen

## <a name="back-up"></a>Säkerhetskopiera

Den här skissen tilldelar Azure Policy definitioner som granskar organisationens säkerhets kopierings information till den alternativa lagrings platsen elektroniskt. Överväg att använda Azure Data Box för fysisk leverans av metadata för lagring.

- Långsiktig Geo-redundant säkerhets kopiering ska aktive ras för Azure SQL-databaser
- Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MySQL
- Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for PostgreSQL
- Geo-redundant säkerhets kopiering måste aktive ras för Azure Database for MariaDB
- Azure Backup ska vara aktiverat för Virtual Machines

> [!NOTE]
> Tillgängligheten för vissa Azure Policy definitioner kan variera i Azure Government och andra nationella moln. 

## <a name="next-steps"></a>Nästa steg

Du har granskat kontroll mappningen av HIPAA HITRUST skiss-exemplet. Gå sedan till följande artiklar för att lära dig mer om översikten och hur du distribuerar det här exemplet:

> [!div class="next step action"]
> [HIPAA HiTRUST skiss – översikt](./control-mapping.md) 
>  [HIPAA HiTRUST skiss – distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
