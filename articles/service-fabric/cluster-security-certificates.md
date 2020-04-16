---
title: X.509 Certifikatbaserad autentisering i ett service fabric-kluster
description: Lär dig mer om certifikatbaserad autentisering i Service Fabric-kluster och hur du identifierar, minskar och åtgärdar certifikatrelaterade problem.
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 699015e322c599dea996b3a8b9dbc0a4589440ab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429673"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>X.509 Certifikatbaserad autentisering i Service Fabric-kluster

Den här artikeln kompletterar introduktionen till [service fabric-klustersäkerhet](service-fabric-cluster-security.md)och går in på information om certifikatbaserad autentisering i Service Fabric-kluster. Vi antar att läsaren är bekant med grundläggande säkerhetsbegrepp, och även med de kontroller som Service Fabric exponerar för att styra säkerheten för ett kluster.  

Ämnen som omfattas av denna titel:

* Grundläggande om certifikatbaserad autentisering
* Identiteter och deras respektive roller
* Regler för certifikatkonfiguration
* Felsökning och vanliga frågor

## <a name="certificate-based-authentication-basics"></a>Grundläggande om certifikatbaserad autentisering
Som en kort repetitionskurs, i säkerhet, är ett certifikat ett instrument avsett att binda information om en enhet (ämnet) till deras innehav av ett par asymmetriska kryptografiska nycklar, och utgör därför en kärnkonstruktion av kryptering av offentliga nycklar. De nycklar som ett certifikat representerar kan användas för att skydda data eller för att styrka nyckelinnehavarnas identitet. När ett certifikat används tillsammans med ett PKI-system (Public Key Infrastructure) kan ett certifikat representera ytterligare egenskaper hos ämnet, till exempel äganderätten till en Internet-domän eller vissa privilegier som utfärdats av certifikatutfärdaren (känd som certifikatutfärdare eller certifikatutfärdare). En vanlig tillämpning av certifikat stöder kryptografiska protokoll (Transport Layer Security), vilket möjliggör säker kommunikation via ett datornätverk. Specifikt använder klienten och servern certifikat för att säkerställa integriteten och integriteten i sin kommunikation, och även för att utföra ömsesidig autentisering.

I Service Fabric bygger det grundläggande lagret i ett kluster (Federation) också på TLS (bland andra protokoll) för att uppnå ett tillförlitligt och säkert nätverk av deltagande noder. Anslutningar till klustret via Api:er för tjänstinfrastruktur använder TLS för att skydda trafiken och även för att fastställa identiteterna för parterna. När ett certifikat används för autentisering i Service Fabric kan det användas för att bevisa följande anspråk: a) presentatören av certifikatautentiseringslicensen har behörighet att inneha certifikatets privata nyckel b) certifikatets SHA-1-hash ("tumavtryck") matchar en deklaration som ingår i klusterdefinitionen, eller c) certifikatets unika ämne gemensamt namn matchar en deklaration som ingår i klusterdefinitionen och certifikatets utfärdare är känd eller betrodd.

I listan ovan kallas "b" i dagligt tal "tumavtrycksnålning". I detta fall avser deklarationen ett särskilt certifikat och styrkan i autentiseringssystemet bygger på antagandet att det är beräkningsmässigt omöjligt att förfalska ett intyg som ger samma hash-värde som ett annat, samtidigt som det är ett giltigt, välformulerat föremål i alla andra avseenden. Punkt c utgör en alternativ form av intyg, om stödordningens styrka vilar på kombinationen av föremålet för intyget och den utfärdande myndigheten. I detta fall avser deklarationen en klass av certifikat - två certifikat med samma egenskaper anses vara helt likvärdiga. 

I följande avsnitt beskrivs ingående hur Service Fabric-körningen använder och validerar certifikat för att säkerställa klustersäkerhet.

## <a name="identities-and-their-respective-roles"></a>Identiteter och deras respektive roller
Innan du dyker in i information om autentisering eller säkra kommunikationskanaler, är det viktigt att lista de deltagande aktörerna och motsvarande roller de spelar i ett kluster:
- Service Fabric-körningen, kallad "system": den uppsättning tjänster som tillhandahåller de abstraktioner och funktioner som representerar klustret. När vi refererar till kommunikation i klustret mellan systeminstanser använder vi termen klusteridentitet. När vi refererar till klustret som mottagare/mål för trafik utanför klustret använder vi termen "serveridentitet".
- värdprogram, kallade "program": kod som tillhandahålls av klustrets ägare, som är iscensatt och körs i klustret
- klienter: entiteter som tillåts ansluta till och köra funktioner i ett kluster, enligt klusterkonfigurationen. Vi skiljer mellan två nivåer av privilegier - "användare" och "admin", respektive. En användarklient är begränsad främst till skrivskyddade åtgärder (men inte alla skrivskyddade funktioner), medan en "admin"-klient har obegränsad åtkomst till klustrets funktioner. (Mer information finns [i Säkerhetsroller i ett Service Fabric-kluster](service-fabric-cluster-security-roles.md).)
- (Azure-only) de Service Fabric-tjänster som dirigerar och exponerar kontroller för drift och hantering av Service Fabric-kluster, så kallade helt enkelt "tjänst". Beroende på miljön kan tjänsten referera till Azure Service Fabric Resource Provider eller andra resursleverantörer som ägs och drivs av Service Fabric-teamet.

I ett säkert kluster kan var och en av dessa roller konfigureras med sin egen, distinkta identitet, deklareras som parning av ett fördefinierat rollnamn och motsvarande autentiseringsuppgifter. Service Fabric stöder att deklarera autentiseringsuppgifter som certifikat eller domänbaserade tjänsthuvudnamn. (Windows-/Kerberos-baserade identiteter stöds också, men ligger utanför den här artikelns omfattning, se [Windows-baserad säkerhet i Service Fabric-kluster](service-fabric-windows-cluster-windows-security.md).) I Azure-kluster kan klientroller också deklareras som [Azure Active Directory-baserade identiteter](service-fabric-cluster-creation-setup-aad.md).

Som nämnts ovan definierar Service Fabric-körningen två behörighetsnivåer i ett kluster: "admin" och "användare". En administratörsklient och en "system"-komponent skulle båda fungera med "admin"-privilegier, och så är omöjliga att skilja från varandra. När du upprättar en anslutning i/till klustret beviljas en autentiserade anropare av Service Fabric-körningen en av de två rollerna som bas för den efterföljande auktoriseringen. Vi undersöker autentiseringen på djupet i följande avsnitt.

## <a name="certificate-configuration-rules"></a>Regler för certifikatkonfiguration
### <a name="validation-rules"></a>Valideringsregler
Säkerhetsinställningarna för ett Service Fabric-kluster beskriver i princip följande aspekter:
- autentiseringstypen. Detta är en skapelse-tid, oföränderlig egenskap hos klustret. Exempel på sådana inställningar är "ClusterCredentialType", "ServerCredentialType" och tillåtna värden är "none", 'x509' eller 'windows'. Den här artikeln fokuserar på autentisering av x509-typ.
- Verifieringsreglerna (autentisering). Dessa inställningar ställs in av klusterägaren och beskriver vilka autentiseringsuppgifter som ska accepteras för en viss roll. Exempel kommer att undersökas ingående omedelbart nedan.
- inställningar som används för att justera eller subtilt ändra resultatet av autentisering; Exempel här är flaggor (de-)som begränsar verkställigheten av listor över återkallade certifikat etc.

> [!NOTE]
> Exempel på klusterkonfiguration nedan är utdrag från klustermanifestet i XML-format, som det mest smälta formatet som stöder direkt service fabric-funktionen som beskrivs i den här artikeln. Samma inställningar kan uttryckas direkt i JSON-representationerna för en klusterdefinition, oavsett om det finns ett fristående json-klustermanifest eller en Azure Resource Mangement-mall.

En regel för certifikatvalidering omfattar följande element:
- motsvarande roll: klient, administratörsklient (privilegierad roll)
- den autentiseringsuppgifter som godkänts för rollen, deklarerad antingen med tumavtryck eller ämnesnamn

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Valideringsdeklarationer för tumavtryck
När det gäller tumavtrycksbaserade valideringsregler valideras autentiseringsuppgifterna som presenteras av en uppringare som begär en anslutning i/till klustret enligt följande:
  - autentiseringsuppgifterna är ett giltigt, välformulerad certifikat: dess kedja kan byggas, signaturer matchar
  - certifikatet är giltigt (Inte innan <= nu < NotAfter)
  - certifikatets SHA-1-hash matchar deklarationen, som en skiftlägesokänslig strängjämförelse exklusive alla blanktecken

Alla förtroendefel som påträffas under kedjebyggnad eller validering kommer att undertryckas för tumavtrycksbaserade deklarationer, med undantag för utgångna certifikat - även om det finns bestämmelser för det fallet också. Felaktigt fel relaterade till: återkallningsstatus är okänd eller offline, ej betrodd rot, ogiltig nyckelanvändning, partiell kedja anses vara icke-allvarliga fel. Utgångspunkten, i detta fall, är att certifikatet är bara ett kuvert för ett nyckelpar - säkerheten ligger i det faktum att klusterägaren har satt på platser åtgärd för att skydda den privata nyckeln.

Följande utdrag från ett klustermanifest exemplifierar en sådan uppsättning tumavtrycksbaserade valideringsregler:

```xml
<Section Name="Security">
  <Parameter Name="ClusterCredentialType" Value="X509" />
  <Parameter Name="ServerAuthCredentialType" Value="X509" />
  <Parameter Name="AdminClientCertThumbprints" Value="d5ec...4264" />
  <Parameter Name="ClientCertThumbprints" Value="7c8f...01b0" />
  <Parameter Name="ClusterCertThumbprints" Value="abcd...1234,ef01...5678" />
  <Parameter Name="ServerCertThumbprints" Value="ef01...5678" />
</Section>
```

Var och en av posterna ovan hänvisar till en specifik identitet som beskrivits tidigare; Varje post gör det också möjligt att ange flera värden, som kommaavgränsad lista med strängar. I det här exemplet, när du har validerat inkommande autentiseringsuppgifter, presentatören av ett certifikat med SHA-1 tumavtryck 'd5ec... 4264" kommer att beviljas rollen "admin". Omvänt, en uppringare autentisera med certifikat '7c8f ... 01b0" kommer att beviljas en "användarroll" som begränsas till i första hand skrivskyddade åtgärder. En inkommande ringer som presenterar ett certifikat vars tumavtryck är antingen abcd ... 1234 eller "ef01... 5678' accepteras som en peer-nod i klustret. Slutligen förväntar sig en klient som ansluter till en hanteringsslutpunkt för klustret att tumavtrycket för servercertifikatet ska vara ef01... 5678'. 

Som tidigare nämnts gör Service Fabric bestämmelser för att acceptera utgångna certifikat; Anledningen är att certifikat har en begränsad livslängd och, när de deklareras med tumavtryck (som refererar till en viss certifikatinstans), så kommer ett certifikat att upphöra att gälla resultera i antingen fel att ansluta till klustret eller en direkt kollaps av klustret. Det är alltför lätt att glömma eller försumma roterande ett tumavtryck-nålas certifikat, och tyvärr återhämtningen från en sådan situation är svårt.

I detta syfte kan klusterägaren uttryckligen ange att självsignerade certifikat som deklarerats med tumavtryck ska anses giltiga enligt följande:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
Detta omfattar inte certifikatutfärdare som utfärdats. Om så vore fallet kan ett återkallat, känt och komprometterat certifikat bli "giltigt" så snart det inte längre skulle finnas med i certifikatutfärdarens förteckning över återkallade certifikat och därmed utgöra en säkerhetsrisk. Med självsignerade certifikat anses klusterägaren vara den enda part som är ansvarig för att skydda certifikatets privata nyckel, vilket inte är fallet med certifikatutfärdare utfärdade certifikat - klusterägaren kanske inte är medveten om hur eller när deras certifikat deklarerades komprometterat.

#### <a name="common-name-based-certificate-validation-declarations"></a>Vanliga namnbaserade certifikatverifieringsdeklarationer
Vanliga namnbaserade deklarationer har något av följande former:
- ämne vanligt namn (endast)
- ämne vanligt namn med emittenten nåla

Låt oss först överväga ett utdrag från ett klustermanifest som exemplifierar båda deklarationsstilarna:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
Deklarationerna refererar till server- respektive klusteridentiteterna. Observera att DE CN-baserade deklarationerna har sina egna avsnitt i klustermanifestet, åtskilda från standarden "Säkerhet". I båda deklarationerna representerar namnet det unika ämnets gemensamma namn, och fältet "Värde" representerar den förväntade utfärdaren enligt följande:

- I det första fallet anges i deklarationen att det gemensamma namnelementet i det unika ämnet för servercertifikatet förväntas matcha strängen "server.demo.system.servicefabric.azure-int". Det tomma fältet Värde anger förväntningen att certifikatkedjans rot är betrodd på den nod/dator där servercertifikatet valideras. i Windows innebär detta att certifikatet kan kedja upp till något av de certifikat som är installerade i arkivet Betrodd rotcertifikatutfärdare.
- I det andra fallet anges i deklarationen att presentatören av ett certifikat accepteras som en peer-nod i klustret om certifikatets gemensamma namn matchar strängen "cluster.demo.system.servicefabric.azure-int", *och* tumavtrycket för certifikatets direkta utfärdare matchar en av de kommaavgränsade posterna i fältet Värde. (Den här regeltypen kallas i dagligt tal "vanligt namn med emittentnålning".)

I båda fallen är certifikatets kedja byggd och förväntas vara felfri. det vill än är att återkallningsfel, partiella kedje- eller tidsfel anses vara allvarliga och certifikatvalideringen misslyckas. Om emittenterna fästs kommer det att leda till att statusen "ej betrodd root" ses som ett icke-allvarligt fel. trots framträdanden är detta en striktare form av validering, eftersom det gör det möjligt för klusterägaren att begränsa uppsättningen auktoriserade/accepterade emittenter till sin egen PKI.

När certifikatkedjan har skapats valideras den mot en standard-TLS/SSL-princip med det deklarerade ämnet som fjärrnamn. Ett certifikat betraktas som en matchning om dess ämnesnamn eller något av dess alternativa namn matchar CN-deklarationen från klustermanifestet. Jokertecken stöds i det här fallet och strängmatchningen är skiftlägesokänslig.

(Vi bör klargöra att sekvensen som beskrivs ovan kan köras för varje typ av nyckelanvändning som deklarerats av certifikatet. I händelse av framgång slutförs utvärderingen och valideringen lyckas. Om certifikatet inte har klientautentiseringsanvändningen, eller om valideringen misslyckades, skapas och utvärderas kedjan för serverautentisering.)

För att slutföra exemplet illustrerar följande utdrag deklarera klientcertifikat med gemensamt namn:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

Deklarationerna ovan motsvarar administratörs- respektive användaridentiteterna. validering av certifikat som deklarerats på detta sätt är exakt som beskrivs för tidigare exempel, av kluster- och servercertifikat.

> [!NOTE]
> Vanliga namnbaserade deklarationer är avsedda att förenkla rotation, och i allmänhet hantering av klustercertifikat. Vi rekommenderar dock att du följer följande rekommendationer för att säkerställa att klustret fortsätter att vara tillgängliga och säkra:
  * föredrar att emittenten sätter fast vid att förlita sig på betrodda rötter
  * undvika att blanda emittenter från olika PKIs
  * se till att alla förväntade emittenter finns med i certifikatdeklarationen, en felmatchning av utfärdaren kommer att resultera i en misslyckad validering
  * se till att PKI:s slutpunkter för certifikatprinciper är upptäckbara, tillgängliga och tillgängliga - det innebär att slutpunkterna AIA, CRL eller OCSP deklareras på lövcertifikatet och att de är tillgängliga så att certifikatkedjebyggnaden kan slutföras.

När service fabric-körningen knyts samman, när du tar emot en begäran om en anslutning i ett kluster som är skyddat med X.509-certifikat, används klustrets säkerhetsinställningar för att validera fjärrpartens autentiseringsuppgifter enligt beskrivningen ovan. Om det lyckas anses den som ringer/fjärrparten vara autentiserade. Om autentiseringsuppgifterna matchar flera verifieringsregler ger körningen anroparen den högst privilegierade rollen för någon av de matchade reglerna. 

### <a name="presentation-rules"></a>Presentationsregler
I föregående avsnitt beskrivs hur autentisering fungerar i ett certifikatbesatt kluster. Det här avsnittet förklarar hur själva service fabric-körningen identifierar och läser in certifikat som används för kommunikation i klustret. vi kallar dessa "presentation" regler.

Precis som med verifieringsreglerna anger presentationsreglerna en roll och den associerade autentiseringsuppgifterna, uttryckt antingen med tumavtryck eller vanligt namn. Till skillnad från valideringsreglerna har gemensamma namnbaserade deklarationer inga bestämmelser för emittentsnålning. detta möjliggör större flexibilitet och förbättrad prestanda. Presentationsreglerna deklareras i avsnittet "NodeType" i klustermanifestet för varje enskild nodtyp. Inställningarna delas från säkerhetsavsnitten i klustret så att varje nodtyp kan ha sin fullständiga konfiguration i ett enda avsnitt. I Azure Service Fabric-kluster är nodcertifikatdeklarationerna standard för motsvarande inställningar i avsnittet Säkerhet i definitionen av klustret.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Presentationsdeklarationer för tumavtryck
Som tidigare beskrivits skiljer Service Fabric-körningen mellan dess roll som peer för andra noder i klustret och som server för klusterhanteringsåtgärder. I princip kan dessa inställningar konfigureras tydligt, men i praktiken tenderar de att justera. För resten av den här artikeln antar vi att inställningarna matchar för enkelhetens skull.

Låt oss överväga följande utdrag från ett kluster manifest:
```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindByThumbprint" X509FindValue="cc71...1984" X509FindValueSecondary="49e2...19d6" X509StoreName="my" Name="ClusterCertificate" />
        <ServerCertificate X509FindValue="cc71...1984" Name="ServerCertificate" />
        <ClientCertificate X509FindValue="cc71...1984" Name="ClientCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```
Elementet "ClusterCertificate" visar hela schemat, inklusive valfria parametrar ("X509FindValueSecondary") eller parametrar med lämpliga standardvärden ("X509StoreName"). De andra deklarationerna visar ett förkortat formulär. Klustercertifikatdeklarationen ovan anger att säkerhetsinställningarna för noder av typen nt1vm initieras med certifikatet 'cc71.. 1984" som den primära, och "49e2.. 19d6"-certifikat som sekundärt; båda certifikaten förväntas finnas i LocalMachine\'My-certifikatarkivet (eller motsvarande Linux-sökväg, *var/lib/sfcerts*).

#### <a name="common-name-based-certificate-presentation-declarations"></a>Vanliga namnbaserade certifikatpresentationsdeklarationer
Nodtypcertifikaten kan också deklareras efter ämnesnamn, vilket exemplifieras nedan:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

För någon typ av deklaration läser en Service Fabric-nod konfigurationen vid start, lokaliserar och läser in de angivna certifikaten och sorterar dem i fallande ordning efter attributet NotAfter. utgångna certifikat ignoreras och det första elementet i listan väljs som klientautentiseringsuppgifter för alla Service Fabric-anslutningar som försöks av den här noden. (I själva verket gynnar Service Fabric längst ut certifikatet.)

Observera att för vanliga namnbaserade presentationsdeklarationer betraktas ett certifikat som en matchning om dess vanliga namn är lika med fältet X509FindValue (eller X509FindValueSecondary) i deklarationen som en skiftlägeskänslig, exakt strängjämförelse. Detta står i kontrast till valideringsreglerna, som stöder matchning av jokertecken, samt jämförelser av skiftlägesokänsliga strängar.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Inställningar för diverse certifikatkonfigurationer
Det nämndes tidigare att säkerhetsinställningarna för ett Service Fabric-kluster också gör det möjligt att subtilt ändra autentiseringskodens beteende. Artikeln om [klusterinställningar för Service Fabric](service-fabric-cluster-fabric-settings.md) representerar den omfattande och senaste listan med inställningar, men vi utökar innebörden av några av de säkerhetsinställningar som finns här för att slutföra den fullständiga exponera på certifikatbaserad autentisering. För varje inställning förklarar vi avsikten, standardvärdet/beteendet, hur den påverkar autentiseringen och vilka värden som är acceptabla.

Som nämnts innebär certifikatvalidering alltid att bygga upp och utvärdera certifikatets kedja. För CA-utfärdade certifikat innebär detta till synes enkla OS API-anrop vanligtvis flera utgående anrop till olika slutpunkter för den utfärdande PKI, cachelagring av svar och så vidare. Med tanke på förekomsten av certifikatvalideringsanrop i ett Service Fabric-kluster kan eventuella problem i PKI:s slutpunkter resultera i minskad tillgänglighet för klustret eller direkt nedbrytning. Även om de utgående samtalen inte kan undertryckas (se nedan i avsnittet Vanliga frågor och svar om mer om detta), kan följande inställningar användas för att dölja valideringsfel som orsakas av misslyckade CRL-anrop.

  * CrlCheckingFlag - under avsnittet "Säkerhet" konverteras strängen till UINT. Värdet för den här inställningen används av Service Fabric för att maskera ut statusfel i certifikatkedjan genom att ändra beteendet för kedjebyggnad. Det skickas in till Win32 CryptoAPI [CertGetCertificateChain-anropet](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) som parametern "dwFlags" och kan ställas in på valfri giltig kombination av flaggor som accepteras av funktionen. Värdet 0 tvingar Service Fabric-körningen att ignorera eventuella förtroendestatusfel - detta rekommenderas inte, eftersom dess användning skulle utgöra en betydande säkerhetsexponering. Standardvärdet är 0x40000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  När du ska använda: för lokala tester, med självsignerade certifikat eller utvecklarcertifikat som inte är helt formade/inte har en lämplig infrastruktur för offentliga nycklar för att stödja certifikaten. Kan också användas som begränsning i luft-gapped miljöer under övergången mellan PKIs.

  Så här använder du: vi tar ett exempel som tvingar återkallningskontroll endast att komma åt cachelagrade webbadresser. Antar:
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  då deklarationen i klustret manifestet blir:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError - under avsnittet "Säkerhet" boolesk med ett standardvärde "false". Representerar en genväg för att undertrycka felstatusen "återkallning offline" kedjebyggnad (eller en efterföljande status för verifiering av kedjeprincipvalidering).

  När du ska använda: lokal testning eller med utvecklarcertifikat som inte stöds av en riktig PKI. Använd som begränsning i luftgapade miljöer eller när PKI är känt för att vara otillgänglig.

  Så här använder du:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Andra anmärkningsvärda inställningar (allt under avsnittet "Säkerhet"):
  * AcceptExpiredPinnedClusterCertificate - beskrivs i avsnittet tillägnad tumavtrycksbaserad certifikatvalidering; tillåter att acceptera utgångna självsignerade klustercertifikat. 
  * CertificateExpirySafetyMargin - intervall, uttryckt i minuter före certifikatets NotAfter-tidsstämpel, och under vilket certifikatet anses vara i riskzonen för utgångsdatum. Service Fabric övervakar klustercertifikat och avger regelbundet hälsorapporter om deras återstående tillgänglighet. Inom säkerhetsintervallet är dessa hälsorapporter upphöjda till "varningsstatus". Standardär 30 dagar.
  * CertificateHealthReportingInterval - styr frekvensen av hälsorapporter om den återstående tidsgiltigheten för klustercertifikat. Rapporter kommer endast att skickas ut en gång per detta intervall. Värdet uttrycks i sekunder, med en standard på 8 timmar.
  * EnforcePrevalidationOnSecurityChanges - boolean, styr beteendet för klusteruppgradering vid identifiering av ändringar av säkerhetsinställningar. Om värdet är "true" försöker klusteruppgraderingen se till att minst ett av certifikaten som matchar någon av presentationsreglerna kan passera en motsvarande verifieringsregel. Förhandsvalningen körs innan de nya inställningarna tillämpas på en nod, men körs bara på noden som är värd för den primära repliken för Klusterhanteraren-tjänsten när uppgraderingen initieras. När detta skrivs har inställningen en standard "false" och ställs in på "true" för nya Azure Service Fabric-kluster med en runtime-version som börjar med 7.1.
 
### <a name="end-to-end-scenario-examples"></a>Scenario från till (exempel)
Vi har tittat på presentationsregler, valideringsregler och tweaking flaggor, men hur fungerar allt detta tillsammans? I det här avsnittet arbetar vi igenom två heltäckande exempel som visar hur säkerhetsinställningarna kan utnyttjas för säkra klusteruppgraderingar. Observera att detta inte är avsett att vara en omfattande avhandling om korrekt certifikathantering i Service Fabric, leta efter en följeslagare artikel om detta ämne.

Uppdelningen av presentations- och valideringsregler ställer den uppenbara frågan (eller oron) om huruvida de kan avvika och vilka konsekvenserna skulle bli. Det är faktiskt möjligt att en nods val av ett autentiseringscertifikat inte klarar valideringsreglerna för en annan nod. Faktum är att den här diskrepansen är den främsta orsaken till autentiseringsrelaterade incidenter. Samtidigt gör separationen av dessa regler det möjligt för ett kluster att fortsätta fungera under en uppgradering som ändrar klustrets säkerhetsinställningar. Tänk på att genom att först utöka verifieringsreglerna som ett första steg, kommer alla klustrets noder att konvergera på de nya inställningarna medan de aktuella autentiseringsuppgifterna fortfarande används. 

Kom ihåg att i ett Service Fabric-kluster fortskrider en uppgradering genom (upp till 5) "uppgraderingsdomäner" eller UD:er. Endast noder i den aktuella UD uppgraderas/ändras vid en viss tidpunkt, och uppgraderingen fortsätter till nästa UD endast om klustrets tillgänglighet tillåter det. (Se uppgraderingar av [Service Fabric-kluster](service-fabric-cluster-upgrade.md) och andra artiklar om samma ämne för mer information.) Certifikat-/säkerhetsändringar är särskilt riskfyllda eftersom de kan isolera noder från klustret eller lämna klustret vid kanten av kvorumförlust.

Vi använder följande notation för att beskriva en nods säkerhetsinställningar:

Nk: {P:{TP=A}, V:{TP=A}}, där:
  - "Nk" representerar en nod i uppgraderingsdomän *k*
  - "P" representerar nodens nuvarande presentationsregler (förutsatt att vi endast hänvisar till klustercertifikat). 
  - V representerar nodens aktuella verifieringsregler (endast klustercertifikat)
  - 'TP=A' representerar en tumavtrycksbaserad deklaration (TP), där "A" är ett certifikattumavtryck
  - "CN=B" representerar en gemensam namnbaserad deklaration (CN), där "B" är certifikatets gemensamma namn 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Rotera ett klustercertifikat som deklarerats med tumavtryck
Följande sekvens beskriver hur en 2-stegs uppgradering kan användas för att säkert införa ett sekundärt klustercertifikat, deklarerat med tumavtryck. Den första fasen införs den nya certifikatdeklarationen i valideringsreglerna, och i den andra fasen införs den i presentationsreglerna:
  - ursprungligt tillstånd: N0 = {P:{TP=A}, V:{TP=A}}, ... Nk = {P:{TP=A}, V:{TP=A}} - klustret är i vila, alla noder delar en gemensam konfiguration
  - när du har slutfört uppgraderingsdomänen 0: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A}} - noder i UD0 kommer att visa certifikat A och acceptera certifikat A eller B; alla andra noder som finns och accepterar certifikat A
  - när du har slutfört den senaste uppgraderingsdomänen: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}} - alla noder finns certifikat A, alla noder accepterar antingen certifikat A eller B
      
Nu är klustret återigen i jämvikt, och den andra fasen av säkerhetsinställningarna för uppgradering/ändring kan påbörjas:
  - när du slutför uppgraderingsdomänen 0: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}} - noder i UD0 börjar presentera B, som accepteras av någon annan nod i klustret.
  - när du har slutfört den senaste uppgraderingsdomänen: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A, TP=B}, V:{TP=A, TP=B}} - alla noder har bytt till att presentera certifikat B. Certifikat A kan nu dras tillbaka/tas bort från klusterdefinitionen med en efterföljande uppsättning uppgraderingar.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Konvertera ett kluster från tumavtryck till vanliga namnbaserade certifikatdeklarationer
På samma sätt följer ändringen av typen av certifikatdeklaration (från tumavtryck till vanligt namn) samma mönster som ovan. Observera att verifieringsregler tillåter att certifikaten för en viss roll deklareras med både tumavtryck och vanligt namn i samma klusterdefinition. Däremot tillåter presentationsreglerna endast en form av deklaration. För övrigt är den säkra metoden för att konvertera ett klustercertifikat från tumavtryck till vanligt namn att först införa det avsedda målcertifikatet med tumavtryck och sedan ändra deklarationen till ett gemensamt namnbaserat. I följande exempel antar vi att tumavtrycket "A" och ämnesnamn "B" refererar till samma certifikat. 

  - ursprungligt tillstånd: N0 = {P:{TP=A}, V:{TP=A}}, ... Nk = {P:{TP=A}, V:{TP=A}} - klustret är i vila, alla noder har en gemensam konfiguration, där A är det primära certifikatets tumavtryck
  - när du slutför uppgraderingsdomänen 0: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A}} - noder i UD0 kommer att visa certifikat A och acceptera certifikat med antingen tumavtryck A eller vanligt namn B. alla andra noder som finns och accepterar certifikat A
  - när du har slutfört den senaste uppgraderingsdomänen: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}} - alla noder finns certifikat A, alla noder accepterar antingen certifikat A (TP) eller B (CN)

På denna punkt kan vi fortsätta med att ändra presentationen regler med en efterföljande uppgradering:
  - när du slutför uppgraderingsdomänen 0: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}} - noder i UD0 kommer att visa certifikat B som hittats av CN och acceptera certifikat med antingen tumavtryck A eller vanligt namn B. alla andra noder som finns och accepterar certifikat A endast, markerade med tumavtryck
  - när du har slutfört den senaste uppgraderingsdomänen: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{CN=B}, V:{TP=A, CN=B}} - alla noder finns certifikat B som hittas av CN, alla noder accepterar antingen certifikat A (TP) eller B (CN)
    
Slutförandet av fas 2 markerar också omvandlingen av klustret till vanliga namnbaserade certifikat. De tumavtrycksbaserade valideringsdeklarationerna kan tas bort i en efterföljande klusteruppgradering.

> [!NOTE]
> I Azure Service Fabric-kluster är de arbetsflöden som presenteras ovan orkestrerade av Service Fabric Resource Provider. Klusterägaren är fortfarande ansvarig för att etablera certifikat i klustret enligt de angivna reglerna (presentation eller validering) och uppmuntras att utföra ändringar i flera steg.

I en separat artikel kommer vi att ta upp ämnet för att hantera och etablera certifikat i ett Service Fabric-kluster.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Felsökning och vanliga frågor
Det är inte lätt att felsöka autentiseringsrelaterade problem i Service Fabric-kluster, men vi hoppas att följande tips och tips kan vara till hjälp. Det enklaste sättet att börja undersökningar är att undersöka Service Fabric händelseloggar på noderna i klustret - inte nödvändigtvis bara de som visar symtom, men också noder som är upp men inte kan ansluta till en av sina grannar. I Windows loggas vanligtvis händelser av betydelse under kanalerna Program och tjänster\Microsoft-ServiceFabric\Admin respektive "Operativa". Ibland kan det vara bra att [aktivera CAPI2 loggning](https://docs.microsoft.com/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues), att fånga mer information om certifikatet validering, hämtning av CRL / CTL etc. (Kom ihåg att inaktivera den efter att ha slutfört repro, kan det vara ganska utförlig.)

Typiska symptom som yttrar sig i ett kluster som har autentiseringsproblem är: 
  - noder är nere/cykling 
  - anslutningsförsök avvisas
  - anslutningsförsök är time out

Var och en av symptomen kan orsakas av olika problem, och samma grundorsak kan visa olika manifestationer; Som sådan kommer vi bara lista ett litet urval av typiska problem, med rekommendationer för att åtgärda dem. 

* Noder kan utbyta meddelanden men kan inte ansluta. En möjlig orsak till anslutningsförsök som ska avslutas är felet "certifikat som inte matchar" - en av parterna i en Service Fabric-to-Service Fabric-anslutningar presenterar ett certifikat som misslyckas med mottagarens valideringsregler. Kan åtföljas av något av följande fel: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  För att diagnostisera/undersöka vidare: på var och en av noderna som försöker ansluta, avgöra vilket certifikat som presenteras; granska certifikatet och försöka emulera valideringsreglerna (kontrollera tumavtryck eller likhet med vanligt namn, kontrollera emittentens tumavtryck om det anges).

  En annan vanlig medföljande felkod kan vara:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  I det här fallet deklareras certifikatet med gemensamt namn och något av följande gäller:
    - emittenterna inte är fästa och rotcertifikatet är inte betrott, eller
    - emittenterna är fästa, men deklarationen innehåller inte tumavtrycket från den direkta utfärdaren av detta certifikat

* En nod är uppe, men kan inte ansluta till andra noder. andra noder tar inte emot inkommande trafik från den felaktiga noden. I det här fallet är det möjligt att certifikatinläsningen misslyckas på den lokala noden. Leta efter följande fel:
  - certifikatet hittades inte - se till att certifikaten som deklarerats i presentationsreglerna kan lösas av innehållet i certifikatarkivet LocalMachine\My (eller som angivet). 
    Möjliga orsaker till fel kan vara: 
      - ogiltiga tecken i tumavtrycksdeklarationen
      - certifikatet inte är installerat
      - certifikatet har upphört att gälla
      - den gemensamma namndeklarationen innehåller prefixet "CN="
      - deklarationen anger ett jokertecken och det finns ingen exakt matchning i cert-arkivet (deklarationen: CN=*.mydomain.com, faktiskt certifikat: CN=server.mydomain.com)

  - okända autentiseringsuppgifter - anger antingen en privat nyckel som saknas som motsvarar certifikatet, vanligtvis tillsammans med felkod: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    För att åtgärda, kontrollera förekomsten av den privata nyckeln; verifiera att SFAdmins beviljas "läs|kör" åtkomst till den privata nyckeln.

  - felaktig providertyp - anger ett CNG-certifikat (Crypto New Generation) ("Microsoft Software Key Storage Provider"); För närvarande stöder Service Fabric endast CAPI1-certifikat. Vanligtvis åtföljs av felkod:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    Återskapa klustercertifikatet med hjälp av en CAPI1-provider (t.ex. Mer information om kryptoleverantörer finns i [Förstå kryptografiska leverantörer](https://docs.microsoft.com/windows/win32/seccertenroll/understanding-cryptographic-providers)

