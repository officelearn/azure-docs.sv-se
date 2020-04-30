---
title: HIPAA HITRUST skiss exempel kontroller
description: Styr mappningen av HIPAA HITRUST-skiss exemplen. Varje kontroll mappas till en eller flera Azure-principer som hjälper till med utvärderingen.
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "75472312"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Kontroll mappning av HIPAA HITRUST skiss-exemplet

Följande artikel beskriver hur Azure-skisser HIPAA HITRUST skiss-exemplet mappar till HIPAA HITRUST-kontrollerna. Mer information om kontrollerna finns i [HIPAA HiTRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Följande mappningar är till **HIPAA HiTRUST** -kontrollerna. Använd navigeringen till höger om du vill gå direkt till en bestämd kontroll mappning. Många av de mappade kontrollerna implementeras med ett [Azure policy](../../../policy/overview.md) initiativ. Om du vill granska hela initiativet öppnar du **princip** i Azure Portal och väljer sidan **definitioner** . Leta sedan reda på ** \[och välj förhands\]granskningen: Audit HIPAA HiTRUST kontrollerar** det inbyggda princip initiativet.

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../../../policy/overview.md) -definitioner. Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som avser själva principerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Standarden för efterlevnad innehåller dessutom kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan kontroller och Azure Policy definitioner för det här skiss exemplet för efterlevnad kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Kontroll mot skadlig kod

Den här skissen hjälper dig att hantera Endpoint Protection, inklusive skadlig kod skydd, genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som övervakas för saknade Endpoint Protection på virtuella datorer i Azure Security Center och tillämpa Microsoft Antimalware-lösningen på virtuella Windows-datorer.

- Distribuera standard tillägget Microsoft IaaS program mot skadlig kod för Windows Server
- Diagnostikloggar i batch-konton måste vara aktiverade
- Systemuppdateringar ska ha installerats på dina datorer


## <a name="management-of-removable-media"></a>Hantering av flyttbara medier

Organisationen, baserat på data klassificerings nivån, registrerar media (inklusive bärbara datorer) före användningen, placerar rimliga begränsningar för hur sådana medier används och ger en lämplig nivå av fysiskt och logiskt skydd (inklusive kryptering) för medier som innehåller information som omfattas tills den är korrekt förstörd eller sanerad.

- Kräv kryptering för Data Lake Store-konton
- SQL-hanterad instans TDE-skydd ska vara krypterat med din egen nyckel
- Disk kryptering bör tillämpas på virtuella datorer
- transparent datakryptering på SQL-databaser ska aktive ras


## <a name="information-exchange-policies-and-procedures"></a>Information Exchange-principer och-procedurer

Moln tjänst leverantörer använder en molnbaserad plattform för virtualiseringsplattformen och standardformat för virtualisering (t. ex. Open Virtualization Format OVF) för att säkerställa samverkan och har dokumenterat anpassade ändringar som gjorts i alla hypervisorer som används och alla lösningar specifika Virtualization-hookar som är tillgängliga för kund granskning.

- Distribuera krav för att granska virtuella Windows-datorer som inte har de angivna programmen installerade

## <a name="control-of-operational-software"></a>Kontroll över operativa program 

Organisationen identifierar otillåten program vara i informations systemet, inklusive servrar, arbets stationer och bärbara datorer, använder en princip för Tillåt-alla, neka-by-Exception för att förhindra körning av kända otillåten program vara på informations systemet och granskar och uppdaterar listan över obehöriga program regelbundet, men inte mindre än årligen.

- \[Förhandsgranska\] visar gransknings resultat från konfigurationer för virtuella Windows-datorer i säkerhets alternativ – granskning
- \[Förhandsgranska\] visar gransknings resultat från konfigurationer för virtuella Windows-datorer i "system gransknings principer-konto hantering"

## <a name="change-control-procedures"></a>Ändra kontroll procedurer

Integriteten för alla avbildningar av virtuella datorer är alltid säkerställd genom att logga och höja en avisering för alla ändringar som görs i avbildningar av virtuella datorer och göra dem tillgängliga för företags ägare och/eller kunder via elektroniska metoder (t. ex. portaler eller aviseringar) resultatet av en ändring eller flyttning och den efterföljande verifieringen av avbildningens integritet.

- \[Förhandsgranska\] visar gransknings resultat från konfiguration av virtuella Windows-datorer i system gransknings principer-detaljerad spårning

## <a name="inventory-of-assets"></a>Inventering av till gångar 

En förteckning över till gångar och tjänster underhålls

- Diagnostikloggar i search Services ska vara aktive rad.
- \[För\] hands version distribuera krav för att granska Windows-konfigurationer för virtuella datorer i "säkerhets alternativ-Microsoft-nätverks server"
- \[För\] hands versions distributions krav för att granska Windows-konfigurationer för virtuella datorer i "administrativa mallar-nätverk"

## <a name="control-of-technical-vulnerabilities"></a>Kontroll av tekniska sårbarheter 

Det finns en strikt konfigurations standard för alla system-och nätverks komponenter.

- Granska Virtual Machines utan haveri beredskap har kon figurer ATS
- Sårbarhets bedömning ska vara aktiverat på SQL-hanterade instanser
- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning

## <a name="segregation-in-networks"></a>Uppdelning i nätverk

Organisationens säkerhetsgatewayer (t. ex. brand väggar) tillämpar säkerhets principer och är konfigurerade för att filtrera trafik mellan domäner, blockera obehörig åtkomst och används för att upprätthålla uppdelning mellan interna tråd bundna, interna trådlösa och externa nätverks segment (t. ex. Internet), inklusive DMZs och tvingande principer för åtkomst kontroll för varje domän.

- Automatisk etablering av Security Monitoring Agent
- Distribuera nätverks övervakare när virtuella nätverk skapas

## <a name="input-data-validation"></a>Verifiering av indata

För alla offentliga webb program implementeras brand väggar på program nivå för att kontrol lera trafiken. För offentliga program som inte är webbaserade har organisationen implementerat en nätverks baserad brand vägg som är specifik för program typen. Om trafiken till det offentliga programmet är krypterad är enheten antingen bakom krypteringen eller kan dekryptera trafiken innan du analyserar.

- \[Förhandsgranska\] visar gransknings resultat från konfiguration av virtuella Windows-datorer i "egenskaper för Windows-brandväggen"


## <a name="network-connection-control"></a>Nätverks anslutnings kontroll

Nätverks trafiken kontrol leras i enlighet med organisationens princip för åtkomst kontroll via brand vägg och andra nätverksrelaterade begränsningar för varje nätverks åtkomst punkt eller extern telekommunikations tjänsts hanterade gränssnitt.

- Åtkomst via Internet-riktade slut punkter bör begränsas
- SQL-hanterad instans TDE-skydd ska vara krypterat med din egen nyckel
- Fjärrfelsökning bör inaktive ras för API Apps

## <a name="network-controls"></a>Nätverks kontroller

Organisationen använder säkra och krypterade kommunikations kanaler när du migrerar fysiska servrar, program eller data till virtualiserade servrar.

- Disk kryptering bör tillämpas på virtuella datorer
- SQL Server TDE-skyddskomponenten bör vara krypterat med din egen nyckel
- \[Förhandsgranska\] visar granskningar från virtuella Windows-konfigurationer i säkerhets alternativ-nätverks åtkomst
- Granska obegränsad nätverks åtkomst till lagrings konton
- \[Förhandsgranska\] visar granskningar resultat från konfiguration av virtuella Windows-datorer i "egenskaper för Windows-brandväggen"
- Distribuera diagnostikinställningar från nätverks säkerhets grupper
- Åtkomst via Internet-riktade slut punkter bör begränsas

## <a name="sensitive-system-isolation"></a>Känslig system isolering

Delade system resurser (t. ex. register, huvud minne, sekundär lagring) släpps tillbaka till systemet, skyddas från avslöjande till andra system/program/användare och användare kan inte avsiktligt eller oavsiktligt komma åt information som rester.

- Virtual Machines ska migreras till nya Azure Resource Managers resurser

## <a name="security-of-network-services"></a>Säkerhet för Network Services

Överenskomna tjänster som tillhandahålls av en nätverks tjänst leverantör eller hanterare hanteras formellt och övervakas för att säkerställa att de tillhandahålls på ett säkert sätt.

- Virtual Machines ska migreras till nya Azure Resource Managers resurser

## <a name="network-routing-control"></a>Nätverks routnings kontroll

Routing-kontroller implementeras via säkerhets-gatewayer (t. ex. brand väggar) som används mellan interna och externa nätverk (t. ex. Internet-och tredje parts nätverk).

- Anpassningsbara program kontroller ska vara aktiverade på virtuella datorer

## <a name="information-exchange-policies-and-procedures"></a>Information Exchange-principer och-procedurer

Organisationen begränsar användningen av organisations kontrollerade flyttbara lagrings medier av behöriga personer på externa informations system.

- Granska obegränsad nätverks åtkomst till lagrings konton
- Fjärrfelsökning bör inaktive ras för webb program
- APi-appen bör bara vara tillgänglig via HTTPS

## <a name="electronic-messaging"></a>Elektroniska meddelanden

Godkännanden hämtas innan du använder externa offentliga tjänster, inklusive snabb meddelanden eller fildelning.

- \[Förhandsgranska\] visar gransknings resultat från virtuella Linux-datorer som inte har lösen ords fil behörigheterna inställda på 0644

## <a name="on-line-transactions"></a>Online-transaktioner

Organisationen kräver att kryptering används mellan, och användningen av elektroniska signaturer av, var och en av de parter som ingår i transaktionen. Organisationen säkerställer att lagringen av transaktions informationen finns utanför alla offentligt tillgängliga miljöer (t. ex. på en lagrings plattform som finns på organisationens intranät) och inte behålls och exponeras på ett lagrings medium som är direkt tillgängligt från Internet. där en betrodd utfärdare används (t. ex. för att utfärda och underhålla digitala signaturer och/eller digitala certifikat) är säkerheten integrerad och inbäddad i hela processen för hantering av certifikat/signaturer i hela slut punkt till slut punkt.

- Disk kryptering bör tillämpas på virtuella datorer
- \[Förhandsgranska\] visar gransknings resultat från virtuella Windows-datorer som inte innehåller de angivna certifikaten i den betrodda roten

## <a name="password-management"></a>Lösen ords hantering

Lösen ord krypteras vid överföring och lagring på alla system komponenter.

- \[Förhandsgranska\] visar gransknings resultat från virtuella Windows-datorer där inställningen för lösen ords komplexitet är aktive rad

## <a name="user-authentication-for-external-connections"></a>Användarautentisering för externa anslutningar

Starka autentiseringsmetoder, till exempel Multi-Factor, RADIUS eller Kerberos (för privilegie rad åtkomst) och CHAP (för kryptering av autentiseringsuppgifter för fjärr anslutnings metoder) implementeras för alla externa anslutningar till organisationens nätverk.

- MFA ska vara aktiverade konton med Skriv behörighet för din prenumeration
- Just-in-Time-kontroll för nätverks åtkomst ska tillämpas på virtuella datorer

## <a name="user-identification-and-authentication"></a>Användar identifiering och autentisering

Användare som utförde privilegierade funktioner (t. ex. system administration) använder separata konton när de utför de privilegierade funktionerna. Multi-Factor Authentication-metoder används i enlighet med organisations principer (t. ex. för fjärråtkomst till fjärrnätverk).

- MFA ska vara aktiverade konton med Skriv behörighet för din prenumeration
- Just-in-Time-kontroll för nätverks åtkomst ska tillämpas på virtuella datorer

## <a name="privilege-management"></a>Behörighets hantering

Åtkomst till hanterings funktioner eller administrativa konsoler för system som är värdar för virtualiserade system är begränsade till personal baserat på principen om minsta behörighet och support genom tekniska kontroller.

- Just-in-Time-kontroll för nätverks åtkomst ska tillämpas på virtuella datorer
- \[Förhands granskning\] av rollbaserad Access Control (RBAC) ska användas på Kubernetes Services

## <a name="review-of-user-access-rights"></a>Granskning av användar behörighet

Organisationen underhåller en dokumenterad lista över behöriga användare av informations till gångar.

- \[Förhandsgranska\] visar granskningar resultat från konfigurationer för virtuella Windows-datorer i "säkerhets alternativ – konton"

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Fjärrdiagnostik och konfiguration av port skydd

Portar, tjänster och liknande program som är installerade på en dator eller ett nätverks system, som inte särskilt krävs för företags funktioner, är inaktiverade eller borttagna.

- Hanterings portar bör stängas på dina virtuella datorer
- Säkerhets problem i säkerhets konfiguration på dina virtuella datorers skalnings uppsättningar bör åtgärdas

## <a name="audit-logging"></a>Gransknings loggning

Loggar av meddelanden som skickas och tas emot underhålls inklusive datum, tid, ursprung och mål för meddelandet, men inte innehållet. Granskning är alltid tillgängligt när systemet är aktivt och spårar viktiga händelser, lyckade/misslyckade data åtkomst, ändringar i systemets säkerhets konfiguration, privilegie rad eller användning, eventuella larm, aktivering och inaktive ring av skydds system (t. ex. A/V och ID), aktivering och inaktive ring av identifierings-och autentiseringsmekanismer samt skapande och borttagning av objekt på objekt.

- Diagnostikloggar i Händelsehubben måste vara aktive rad
- System uppdateringar på virtuella datorers skalnings uppsättningar bör installeras

## <a name="monitoring-system-use"></a>Övervaka system användning

Automatiserade system som distribueras i hela organisationens miljö används för att övervaka nyckel händelser och avvikande aktivitet och analysera system loggar, vars resultat granskas regelbundet. Övervakning omfattar privilegierade åtgärder, auktoriserad åtkomst eller obehörig åtkomst, inklusive försök att komma åt inaktiverade konton och system varningar eller-haverier.

- Diagnostikloggar i skalnings uppsättningar för virtuella datorer ska aktive ras

## <a name="segregation-of-duties"></a>Ansvars fördelning

Separering av uppgifter används för att begränsa risken för obehörig eller oavsiktlig ändring av information och system. Ingen enskild person kan komma åt, ändra eller använda informations system utan auktorisering eller identifiering. Åtkomst för personer som ansvarar för administration och åtkomst kontroll begränsas till den lägsta som krävs baserat på varje användares roll och ansvar och dessa personer kan inte komma åt gransknings funktioner som är relaterade till dessa kontroller.

- MFA ska vara aktiverade konton med Skriv behörighet för din prenumeration

## <a name="administrator-and-operator-logs"></a>Administratörs-och operatörs loggar

Organisationen ser till att korrekt loggning är aktiverat för att kunna granska administratörs aktiviteter. och granskar system administratören och operatörs loggar regelbundet.

- MFA ska vara aktiverade konton med Skriv behörighet för din prenumeration

## <a name="identification-of-risks-related-to-external-parties"></a>Identifiering av risker relaterade till externa parter

Fjärråtkomstanslutningar mellan organisationen och externa parter är krypterade

- Disk kryptering bör tillämpas på virtuella datorer

## <a name="business-continuity-and-risk-assessment"></a>Verksamhets kontinuitet och riskbedömning

Organisationen identifierar sina kritiska affärs processer och integrerar kraven för informations säkerhets hantering i affärs kontinuitet med andra kontinuitets krav som rör sådana aspekter som drift, personal, material, transport och anläggningar.

- \[Förhandsgranska\] visar gransknings resultat från konfiguration av virtuella Windows-datorer i säkerhets alternativ-återställnings konsolen

> [!NOTE]
> Tillgängligheten för vissa Azure Policy definitioner kan variera i Azure Government och andra nationella moln. 

## <a name="next-steps"></a>Nästa steg

Du har granskat kontroll mappningen av HIPAA HITRUST skiss-exemplet. Gå sedan till följande artiklar för att lära dig mer om översikten och hur du distribuerar det här exemplet:

> [!div class="next step action"]
> [HIPAA HiTRUST skiss – översikt](./control-mapping.md)
> [HIPAA HiTRUST skiss – distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
