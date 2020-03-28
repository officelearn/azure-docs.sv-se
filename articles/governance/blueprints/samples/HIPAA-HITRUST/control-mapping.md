---
title: HIPAA HITRUST-provkontroller för skiss
description: Kontrollkartläggning av HIPAA HITRUST-skissproverna. Varje kontroll mappas till en eller flera Azure-principer som hjälper till med utvärdering.
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75472312"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Kontrollkartläggning av HIPAA HITRUST-skissprovet

I följande artikel beskrivs hur azure blueprints HIPAA HITRUST-skissens exempel mappar till HIPAA HITRUST-kontrollerna. Mer information om kontrollerna finns i [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Följande mappningar är till **HIPAA HITRUST-kontrollerna.** Använd navigeringen till höger för att hoppa direkt till en specifik kontrollmappning. Många av de mappade kontrollerna implementeras med ett [Azure Policy-initiativ.](../../../policy/overview.md) Om du vill granska hela initiativet öppnar du **Princip** i Azure-portalen och väljer sidan **Definitioner.** Leta sedan reda ** \[på\]och välj förhandsversionen: Granska HIPAA HITRUST-kontroller** som är inbyggda i principinitiativet.

> [!IMPORTANT]
> Varje kontroll nedan är associerad med en eller flera [Azure-principdefinitioner.](../../../policy/overview.md) Dessa policyer kan hjälpa dig att [bedöma efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta inte en 1:1 eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan refererar **kompatibel** i Azure-princip endast till principerna själva. Detta säkerställer inte att du är helt kompatibel med alla krav på en kontroll. Dessutom innehåller efterlevnadsstandarden kontroller som inte åtgärdas av några Azure-principdefinitioner just nu. Därför är efterlevnad i Azure Policy bara en partiell bild av din övergripande efterlevnadsstatus. Associationerna mellan kontroller och Azure-principdefinitioner för det härmplet för efterlevnadsritning kan ändras med tiden. Om du vill visa ändringshistoriken läser du [GitHub Commit-historiken](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Kontroll mot skadlig kod

Den här skissen hjälper dig att hantera slutpunktsskydd, inklusive skadligt kodskydd, genom att tilldela [Azure Policy-definitioner](../../../policy/overview.md) som övervakar saknade slutpunktsskydd på virtuella datorer i Azure Security Center och framtvingar Microsofts lösning mot skadlig kod på virtuella Windows-datorer.

- Distribuera standardtillägget Microsoft IaaS Antimalware för Windows Server
- Diagnostikloggar i batchkonton ska aktiveras
- Systemuppdateringar ska ha installerats på dina datorer


## <a name="management-of-removable-media"></a>Hantering av flyttbara medier

Organisationen, baserat på dataklassificeringsnivån, registrerar media (inklusive bärbara datorer) före användning, lägger rimliga begränsningar för hur sådana medier används och ger en lämplig nivå av fysiskt och logiskt skydd (inklusive kryptering) för mediet som innehåller information som omfattas tills den är ordentligt förstörd eller sanerad.

- Kräv kryptering på DataSjölagringskonton
- SQL-hanterad instans TDE-skydd ska krypteras med din egen nyckel
- Diskkryptering bör tillämpas på virtuella datorer
- Transparent datakryptering i SQL-databaser bör aktiveras


## <a name="information-exchange-policies-and-procedures"></a>Riktlinjer och förfaranden för informationsutbyte

Molntjänstleverantörer använder en branscherkänd virtualiseringsplattform och standardvirtualiseringsformat (t.ex. lösningsspecifika virtualiseringskrokar tillgängliga för kundgranskning.

- Distribuera förutsättningar för granskning av virtuella Windows-datorer som inte har de angivna programmen installerade

## <a name="control-of-operational-software"></a>Kontroll av operativ programvara 

Organisationen identifierar obehörig programvara på informationssystemet, inklusive servrar, arbetsstationer och bärbara datorer, använder en policy för att tillåta alla, neka-för-undantag för att förbjuda körning av känd obehörig programvara på informationssystemet, och granskar och uppdaterar listan över otillåten programvara med jämna mellanrum men inte mindre än årligen.

- \[Förhandsgranska\] Visa granskningsresultat från Windows virtuella datorer i "Säkerhetsalternativ-granskning"
- \[Förhandsgranska\] Visa granskningsresultat från Windows virtuella datorer i "Systemgranskningsprinciper- Kontohantering"

## <a name="change-control-procedures"></a>Ändra kontrollprocedurer

Integriteten för alla virtuella datoravbildningar säkerställs hela tiden genom att logga och höja en avisering om eventuella ändringar som görs i avbildningar för virtuella datorer och göra resultaten av en ändring eller flyttning tillgängliga för företagets ägare och/eller kunder genom elektroniska metoder (t.ex. portaler eller varningar) och efterföljande validering av bildens integritet.

- \[Förhandsgranska\] Visa granskningsresultat från windows-virtuella datorer i "Systemgranskningsprinciper -Detaljerad spårning"

## <a name="inventory-of-assets"></a>Lager av tillgångar 

En inventering av tillgångar och tjänster upprätthålls

- Diagnostikloggar i söktjänster bör aktiveras.
- \[Förhandsgranska\] distributionskrav för granskning av Windows-virtuella datorer i "Säkerhetsalternativ- Microsoft Network Server"
- \[Förhandsgranska\] distributionskrav för granskning av Windows virtuella datorer i "Administrativa mallar- nätverk"

## <a name="control-of-technical-vulnerabilities"></a>Kontroll av tekniska sårbarheter 

Det finns en härdad konfigurationsstandard för alla system- och nätverkskomponenter.

- Granska virtuella datorer utan haveriberedskap konfigurerad
- Sårbarhetsbedömning bör aktiveras på dina SQL-hanterade instanser
- Sårbarheten bör åtgärdas med en lösning för sårbarhetsbedömning

## <a name="segregation-in-networks"></a>Segregering i nätverk

Organisationens säkerhetsgateways (t.ex. brandväggar) tillämpar säkerhetsprinciper och är konfigurerade för att filtrera trafik mellan domäner, blockera obehörig åtkomst och används för att upprätthålla segregering mellan interna trådbundna, interna trådlösa och externa nätverkssegment (t.ex. Internet) inklusive DMZs och genomdriva principer för åtkomstkontroll för var och en av domänerna.

- Automatisk etablering av säkerhetsövervakningsagent
- Distribuera nätverksbevakare när virtuella nätverk skapas

## <a name="input-data-validation"></a>Validering av indata

För alla offentliga webbprogram implementeras brandväggar på programnivå för att styra trafiken. För program som inte är webbaserade för offentliga program har organisationen implementerat en nätverksbaserad brandvägg som är specifik för programtypen. Om trafiken till det offentliga programmet är krypterad sitter enheten antingen bakom krypteringen eller kan dekryptera trafiken före analysen.

- \[Förhandsgranska\] Visa granskningsresultat från windows-virtuella datorer i "Egenskaper för Windows-brandväggen"


## <a name="network-connection-control"></a>Kontroll av nätverksanslutning

Nätverkstrafiken styrs i enlighet med organisationens åtkomstkontrollprincip genom brandvägg och andra nätverksrelaterade begränsningar för varje nätverksåtkomstpunkt eller extern telekommunikationstjänsts hanterade gränssnitt.

- Åtkomst via internetvända slutpunkter bör begränsas
- SQL-hanterad instans TDE-skydd ska krypteras med din egen nyckel
- Fjärrfelsökning bör inaktiveras för API-appar

## <a name="network-controls"></a>Nätverkskontroller

Organisationen använder säkra och krypterade kommunikationskanaler när de migrerar fysiska servrar, program eller data till virtualiserade servrar.

- Diskkryptering bör tillämpas på virtuella datorer
- SQL-server TDE-skydd ska krypteras med din egen nyckel
- \[Förhandsgranska\] Visa granskningar resultat från Windows virtuella datorer konfigurationer i "Säkerhetsalternativ- Nätverksåtkomst"
- Granska obegränsad nätverksåtkomst till lagringskonton
- \[Förhandsgranska\] Visa granskningar resultat från Konfiguration av Virtuella datorer i Windows i "Egenskaper för Windows-brandväggen"
- Distribuera diagnostikinställningar från nätverkssäkerhetsgrupper
- Åtkomst via internetvända slutpunkter bör begränsas

## <a name="sensitive-system-isolation"></a>Isolering av känsliga system

Delade systemresurser (t.ex. register, huvudminne, sekundär lagring) släpps tillbaka till systemet, skyddas från utlämnande till andra system/program/användare och användarna kan inte avsiktligt eller oavsiktligt komma åt informationsrester.

- Virtuella datorer ska migreras till nya Azure Resource Manager-resurser

## <a name="security-of-network-services"></a>Säkerhet för nätverkstjänster

Överenskomna tjänster som tillhandahålls av en leverantör/förvaltare av nättjänster hanteras och övervakas formellt för att säkerställa att de tillhandahålls på ett säkert sätt.

- Virtuella datorer ska migreras till nya Azure Resource Manager-resurser

## <a name="network-routing-control"></a>Kontroll av nätverksdirigering

Routningskontroller implementeras via säkerhetsgateways (t.ex. brandväggar) som används mellan interna och externa nätverk (t.ex. Internet och tredjepartsnätverk).

- Adaptiva programkontroller bör aktiveras på virtuella datorer

## <a name="information-exchange-policies-and-procedures"></a>Riktlinjer och förfaranden för informationsutbyte

Organisationen begränsar användningen av organisationsstyrda bärbara lagringsmedier av behöriga personer på externa informationssystem.

- Granska obegränsad nätverksåtkomst till lagringskonton
- Fjärrfelsökning bör inaktiveras för webbprogram
- APi-app bör endast vara tillgänglig via HTTPS

## <a name="electronic-messaging"></a>Elektroniska meddelanden

Godkännanden erhålls innan externa offentliga tjänster, inklusive snabbmeddelanden eller fildelning.

- \[Förhandsgranska\] Visa granskningsresultat från virtuella Linux-datorer som inte har behörigheterna för lösenordsfilen inställd på 0644

## <a name="on-line-transactions"></a>Online-transaktioner

Organisationen kräver användning av kryptering mellan och användning av elektroniska signaturer av var och en av de parter som deltar i transaktionen. Organisationen säkerställer att lagringen av transaktionsinformationen finns utanför alla allmänt tillgängliga miljöer (t.ex. på en lagringsplattform som finns på organisationens intranät) och inte behålls och exponeras på ett lagringsmedium som är direkt tillgängligt från Internet.Där en betrodd myndighet används (t.ex. för att utfärda och underhålla digitala signaturer och/eller digitala certifikat), är säkerheten integrerad och inbäddad i hela slutkunds-till-slutcertifikat/signaturhantering Process.

- Diskkryptering bör tillämpas på virtuella datorer
- \[Förhandsgranska\] Visa granskningsresultat från virtuella windows-datorer som inte innehåller de angivna certifikaten i betrodd rot

## <a name="password-management"></a>Lösenordshantering

Lösenord krypteras under överföring och lagring på alla systemkomponenter.

- \[Förhandsgranska\] Visa granskningsresultat från virtuella datorer i windows som inte har inställningen för lösenordskomplexitet aktiverad

## <a name="user-authentication-for-external-connections"></a>Användarautentisering för externa anslutningar

Starka autentiseringsmetoder som multifaktor, Radius eller Kerberos (för privilegierad åtkomst) och CHAP (för kryptering av autentiseringsuppgifter för fjärranslutningsmetoder) implementeras för alla externa anslutningar till organisationsnätverket.

- MFA ska vara aktiverade konton med skrivbehörighet för din prenumeration
- Just In Time nätverksåtkomstkontroll bör tillämpas på virtuella datorer

## <a name="user-identification-and-authentication"></a>Identifiering och autentisering av användare

Användare som utförde privilegierade funktioner (t.ex. systemadministration) använder separata konton när de utför dessa privilegierade funktioner. Multifaktorautentiseringsmetoder används i enlighet med organisationsprincipen (t.ex. för fjärråtkomst till nätverk).

- MFA ska vara aktiverade konton med skrivbehörighet för din prenumeration
- Just In Time nätverksåtkomstkontroll bör tillämpas på virtuella datorer

## <a name="privilege-management"></a>Hantering av privilegier

Åtkomst till hanteringsfunktioner eller administrativa konsoler för system som är värdar för virtualiserade system är begränsade till personal baserat på principen om lägsta behörighet och stöds genom tekniska kontroller.

- Just In Time nätverksåtkomstkontroll bör tillämpas på virtuella datorer
- \[RBAC (Preview\] Role Based Access Control) ska användas på Kubernetes-tjänster

## <a name="review-of-user-access-rights"></a>Granskning av användaråtkomsträttigheter

Organisationen har en dokumenterad lista över behöriga användare av informationstillgångar.

- \[Förhandsgranska\] Visa granskningar resultat från Windows virtuella datorer konfigurationer i "Säkerhetsalternativ-Konton"

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Skydd mot fjärrdiagnostik och konfigurationsport

Portar, tjänster och liknande program som är installerade på en dator eller nätverkssystem, som inte specifikt krävs för affärsfunktioner, inaktiveras eller tas bort.

- Hanteringsportar bör stängas på dina virtuella datorer
- Sårbarheter i säkerhetskonfigurationen på dina skaluppsättningar för virtuella datorer bör åtgärdas

## <a name="audit-logging"></a>Granskningsloggning

Loggar över meddelanden som skickas och tas emot underhålls, inklusive datum, tid, ursprung och mål för meddelandet, men inte dess innehåll. Granskning är alltid tillgänglig medan systemet är aktivt och spårar viktiga händelser, lyckades/misslyckad dataåtkomst, systemsäkerhetskonfigurationsändringar, privilegierad eller nyttoanvändning, eventuella larm som utlöses, aktivering och avaktivering av skyddssystem (t.ex.

- Diagnostikloggar i Event Hub ska aktiveras
- Systemuppdateringar på skalningsuppsättningar för virtuella datorer bör installeras

## <a name="monitoring-system-use"></a>Övervakningssystemanvändning

Automatiserade system som distribueras i organisationens miljö används för att övervaka viktiga händelser och avvikande aktivitet, och analysera systemloggar, vars resultat granskas regelbundet. Övervakning omfattar privilegierade åtgärder, auktoriserad åtkomst eller obehöriga åtkomstförsök, inklusive försök att komma åt inaktiverade konton och systemaviseringar eller fel.

- Diagnostikloggar i skalningsuppsättningar för virtuella datorer bör aktiveras

## <a name="segregation-of-duties"></a>Segregering av arbetsuppgifter

Åtskillnad av arbetsuppgifter används för att begränsa risken för obehörig eller oavsiktlig modifiering av information och system. Ingen enskild person kan komma åt, ändra eller använda informationssystem utan tillstånd eller identifiering. Åtkomsten för personer som ansvarar för att administrera och få åtkomstkontroller är begränsad till vad som är absolut nödvändigt på grundval av varje användares roll och ansvar, och dessa personer kan inte få tillgång till granskningsfunktioner som är relaterade till dessa kontroller.

- MFA ska vara aktiverade konton med skrivbehörighet för din prenumeration

## <a name="administrator-and-operator-logs"></a>Administratörs- och operatörsloggar

Organisationen ser till att korrekt loggning är aktiverad för att granska administratörsaktiviteter. och granskar systemadministratör och operatörsloggar regelbundet.

- MFA ska vara aktiverade konton med skrivbehörighet för din prenumeration

## <a name="identification-of-risks-related-to-external-parties"></a>Identifiering av risker relaterade till externa parter

Fjärråtkomstanslutningar mellan organisationen och externa parter krypteras

- Diskkryptering bör tillämpas på virtuella datorer

## <a name="business-continuity-and-risk-assessment"></a>Kontinuitet och riskbedömning

Organisationen identifierar sina kritiska affärsprocesser och integrerar kraven på informationssäkerhetshantering för kontinuitet med andra kontinuitetskrav som rör verksamhet, bemanning, material, transport och faciliteter.

- \[Förhandsgranska\] Visa granskningsresultat från Windows-virtuella datorer i "Security Options- Recovery Console"

> [!NOTE]
> Tillgängligheten för specifika Azure-principdefinitioner kan variera i Azure Government och andra nationella moln. 

## <a name="next-steps"></a>Nästa steg

Du har granskat kontrollmappningen av HIPAA HITRUST-skissen. Gå sedan till följande artiklar om du vill veta mer om översikten och hur du distribuerar det här exemplet:

> [!div class="next step action"]
> [HIPAA HITRUST-ritning - Översikt](./control-mapping.md)
> [HIPAA HITRUST-ritning - Distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Läs mer om [skisslivscykeln](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
