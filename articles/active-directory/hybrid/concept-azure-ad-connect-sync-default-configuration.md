---
title: 'Azure AD Connect-synkronisering: Förstå standardkonfigurationen | Microsoft Docs'
description: Den här artikeln beskriver standardkonfigurationen i Azure AD Connect-synkronisering.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 493dc834319d8f5842a7f9f5c4ca568742264df9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180474"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Azure AD Connect-synkronisering: Förstå standardkonfigurationen
Den här artikeln förklarar konfigurationsregler för out-of-box. Det dokument som reglerna och hur de här reglerna påverkar konfigurationen. Vi går också igenom standardkonfigurationen för Azure AD Connect-synkronisering. Målet är att läsaren förstår hur Konfigurationsmodell, med namnet deklarativ etablering fungerar i ett verkliga exempel. Den här artikeln förutsätter att du redan har installerat och konfigurera Azure AD Connect-synkronisering med hjälp av installationsguiden.

För att förstå vilka konfigurationsmodellen kan läsa [förstå deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Out-of-box-regler från en lokal plats till Azure AD
Följande uttryck kan hittas i out-of-box-konfigurationen.

### <a name="user-out-of-box-rules"></a>Användaren out-of-box-regler
Dessa regler gäller även för typen av iNetOrgPerson-objekt.

Ett användarobjekt måste uppfylla följande som ska synkroniseras:

* Måste ha en sourceAnchor.
* När objektet har skapats i Azure AD, kan inte sourceAnchor ändra. Om värdet är ändrade lokala, stoppar objektet synkronisera förrän sourceAnchor ändras tillbaka till det tidigare värdet.
* Attributet accountEnabled (userAccountControl) måste ifyllda. Med en lokal Active Directory är det här attributet alltid finns och är ifyllt.

Följande användarobjekt är **inte** synkroniseras till Azure AD:

* `IsPresent([isCriticalSystemObject])`. Se till att många out-of-box-objekt i Active Directory, till exempel det inbyggda administratörskontot, är inte synkroniserade.
* `IsPresent([sAMAccountName]) = False`. Se till att inte synkroniseras användarobjekt med något sAMAccountName-attribut. Det här fallet skulle bara praktiskt taget inträffa i en domän som uppgraderats från NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Synkronisera inte tjänstkontot som används av Azure AD Connect-synkronisering och dess tidigare versioner.
* Synkronisera inte Exchange-konton som inte skulle fungera i Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Synkronisera inte objekt som inte skulle fungera i Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Den här bitmask (& H21C07000) filtrerar ut följande objekt:
  * E-postaktiverad offentlig mapp (i förhandsversion från och med version 1.1.524.0)
  * Överför de system-postlåda
  * Postlåda databasen postlåda (System-postlåda)
  * Universell säkerhetsgrupp (inte gäller för en användare, men finns äldre skäl)
  * Icke-universell grupp (inte gäller för en användare, men finns äldre skäl)
  * Plan för postlåda
  * Identifiering av postlåda
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synkronisera inte eventuella replikering victim-objekt.

Följande gäller attribut:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. Attributet sourceAnchor är inte som tillförts från en länkad postlåda. Det förutsätts att om en länkad postlåda har hittats, det aktuella kontot är ansluten senare.
* Exchange-relaterade attribut synkroniseras enbart om attributet **mailNickName** har ett värde.
* När det finns flera skogar, används attribut i följande ordning:
  1. Attribut som rör inloggning (till exempel userPrincipalName) tillhandahålls från skogen med ett aktiverat konto.
  2. Attribut som finns i en Exchange-GAL (globala adresslistan) tillhandahålls från skogen med en Exchange-postlåda.
  3. Om inga postlådan finns, kan dessa attribut kommer från en skog.
  4. Exchange-relaterade attribut (inte synligt i den globala Adresslistan tekniska attribut) tillhandahålls från skogen där `mailNickname ISNOTNULL`.
  5. Om det finns flera skogar som skulle uppfylla ett av de här reglerna, används ordningen som skapas (datum/tid) av kopplingar (skog) för att avgöra vilken skog bidrar attribut.

### <a name="contact-out-of-box-rules"></a>Kontakta out-of-box-regler
Ett kontaktobjekt måste uppfylla följande som ska synkroniseras:

* Kontaktens måste vara e-postaktiverade. Det har verifierats med följande regler:
  * `IsPresent([proxyAddresses]) = True)`. Attributet proxyAddresses måste fyllas i.
  * En primär e-postadress kan hittas i attributet proxyAddresses eller e-postattributet. Förekomsten av en \@ används för att kontrollera att innehållet är en e-postadress. En av dessa två regler måste utvärderas till SANT.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Det finns en post med ”SMTP”: och om det finns en \@ finns i strängen?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Är e-postattributet fylls och om det är en \@ finns i strängen?

Kontakta följande objekt är **inte** synkroniseras till Azure AD:

* `IsPresent([isCriticalSystemObject])`. Se till att inga kontaktobjekt som markerats som kritiskt är synkroniserade. Får inte vara någon med en standardkonfiguration.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. De här objekten fungerar inte i Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synkronisera inte eventuella replikering victim-objekt.

### <a name="group-out-of-box-rules"></a>Gruppera out-of-box-regler
Ett gruppobjekt måste uppfylla följande som ska synkroniseras:

* Måste ha färre än 50 000 medlemmar. Det här antalet är antalet medlemmar i den lokala gruppen.
  * Om den har fler medlemmar innan synkroniseringen startar första gången är gruppen inte synkroniserad.
  * Om antalet medlemmar växa från när den först skapades, sedan när den når 50 000 medlemmar stoppar synkronisering tills antal programmedlemskap är lägre än 50 000 igen.
  * Obs! 50 000 medlemskap antal tillämpas också av Azure AD. Det går inte att synkronisera grupper med fler medlemmar, även om du ändrar eller tar bort den här regeln.
* Om gruppen är en **distributionsgrupp**, måste det också vara e-postaktiverad. Se [kontakta out-of-box regler](#contact-out-of-box-rules) för den här regeln tillämpas.

Följande gruppobjekt är **inte** synkroniseras till Azure AD:

* `IsPresent([isCriticalSystemObject])`. Se till att många out-of-box-objekt i Active Directory, till exempel den inbyggda administratörsgruppen, är inte synkroniserade.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Äldre grupp som används av DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Rollgrupp.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synkronisera inte eventuella replikering victim-objekt.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal out-of-box-regler
FSP: er som är anslutna till ”alla” (\*) objektet i metaversum. I verkligheten kan sker endast den här kopplingen för användare och säkerhetsgrupper. Den här konfigurationen säkerställer att mellan skogar medlemskap är löst och visas korrekt i Azure AD.

### <a name="computer-out-of-box-rules"></a>Datorn out-of-box-regler
Ett objekt måste uppfylla följande som ska synkroniseras:

* `userCertificate ISNOTNULL`. Endast Windows 10-datorer fylla i det här attributet. Alla datorobjekt med ett värde i det här attributet är synkroniserade.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Förstå regler för out-of-box-scenario
I det här exemplet använder vi en distribution med en kontoskog (A), en resursskog (R) och en Azure AD-katalog.

![Bild med scenariobeskrivning](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

I den här konfigurationen förutsätts det finns ett aktiverat konto i skogen och ett inaktiverat konto i resursskogen med en länkad postlåda.

Vårt mål med standardkonfigurationen är:

* Attribut som rör inloggning synkroniseras från skogen med aktiverat konto.
* Attribut som finns i den globala Adresslistan (globala adresslistan) synkroniseras från skogen med postlådan. Om inga postlådan finns, används en annan skog.
* Om en länkad postlåda hittas, måste det länkade aktiverat kontot hittas för objektet som ska exporteras till Azure AD.

### <a name="synchronization-rule-editor"></a>Synchronization Rule Editor
Konfigurationen kan visas och ändras med verktyget synkronisering regler redigeraren (SRE) och en genväg till den finns i start-menyn.

![Synkronisering Regelredigeraren ikon](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

SRE är ett verktyg för resource kit och det är installerat med Azure AD Connect-synkronisering. För att kunna starta den, måste du vara medlem i ADSyncAdmins-gruppen. När den startar ser ungefär så här:

![Synkroniseringsregler inkommande](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

I det här fönstret kan se du alla Synkroniseringsregler som skapats för din konfiguration. Varje rad i tabellen är en Synkroniseringsregel. Till vänster under regeltyper visas två olika typer: Inkommande och utgående. Inkommande och utgående är från vyn i metaversum. Du kommer främst att fokusera på regler för inkommande trafik i den här översikten. Den aktuella listan över Synkroniseringsregler beror på identifierade schemat i AD. I bilden ovan kontoskog (fabrikamonline.com) har inte några tjänster, till exempel Exchange och Lync, och inga Synkroniseringsregler har skapats för dessa tjänster. Men i resursskogen (res.fabrikamonline.com) finns Synkroniseringsregler för dessa tjänster. Innehållet i reglerna är olika beroende på vilken version som har identifierats. Till exempel i en distribution med Exchange 2013 finns mer attributflöden som har konfigurerats än i Exchange 2010/2007.

### <a name="synchronization-rule"></a>Synkroniseringsregel
En Synkroniseringsregel är ett konfigurationsobjekt med en uppsättning attribut som flödar när ett villkor är uppfyllt. Den används också för att beskriva hur ett objekt i en anslutarplats är kopplad till ett objekt i metaversumet kallas **join** eller **matchar**. De Synkroniseringsregler som har en högre prioritetsvärde som anger hur de relaterar till varandra. En Synkroniseringsregel med ett lägre numeriskt värde har högre prioritet och i ett flöde konflikt attributet högre prioritet wins konfliktlösningen.

Exempelvis titta på Synkroniseringsregeln **i från AD – användaren AccountEnabled**. Markera den här raden i SRE och välj **redigera**.

Eftersom den här regeln är en regel för out-of-box, får du en varning när du öppnar regeln. Du bör inte göra någon [ändringar av out-of-box regler](how-to-connect-sync-best-practices-changing-default-configuration.md), så att du får din avsikt är. I detta fall använder vill du bara visa regeln. Välj **nr**.

![Synkronisering regler varning](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

En Synkroniseringsregel har fyra konfigurationsavsnitt: Beskrivning, Scoping filter, Join regler och transformeringar.

#### <a name="description"></a>Beskrivning
Det första avsnittet innehåller grundläggande information, till exempel namn och beskrivning.

![Beskrivning av fliken synkroniserade regeln editor ](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Du hittar även information om vilka anslutna system med den här regeln är relaterad till, vilket objekt av typen i den anslutna system som gäller för och metaversum-objekttyp. Typ av metaversumobjekt är alltid person oavsett när typ av datakällobjekt är en användare, iNetOrgPerson eller kontakt. Typ av metaversumobjekt bör aldrig ändras så att den har skapats som en allmän typ. Länktypen kan ställas in att Join, StickyJoin eller etablera. Den här inställningen fungerar tillsammans med avsnittet ansluta regler och beskrivs senare.

Du kan också se att den här synkroniseringsregel används för synkronisering av lösenord. Om en användare finns i omfattningen för den här regeln för synkronisering, synkroniseras lösenordet från en lokal plats till molnet (förutsatt att du har aktiverat funktionen för synkronisering av lösenord).

#### <a name="scoping-filter"></a>Omfångsfilter
Avsnittet Omfångsfilter används för att konfigurera en Synkroniseringsregel skall gälla. Eftersom namnet på Synkroniseringsregel som du tittar på indikerar den bör endast användas för aktiverade användare, omfång har konfigurerats så att den AD-attribut **userAccountControl** måste inte har den 2 biten. När Synkroniseringsmotorn hittar en användare i AD, tillämpas den här synkroniseringen regeln när **userAccountControl** har angetts till decimalvärde 512 (aktiverad normal användare). Den gäller inte regeln när användaren har **userAccountControl** inställd 514 (inaktiverad normal användare).

![Konfigurera fliken i synkronisering regeln editor ](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

Omfångsfilter har grupper och villkor som kan vara inkapslade. Alla satser i en grupp måste vara uppfyllda för en Synkroniseringsregel ska tillämpas. När flera grupper har definierats måste i minst en grupp vara uppfyllda för regeln ska gälla. Det vill säga ett logiskt OR utvärderas mellan grupper och en logisk och utvärderas i en grupp. Ett exempel på den här konfigurationen finns i den utgående Synkroniseringsregeln **ut till AAD – grupp ansluta**. Det finns flera filter synkroniseringsgrupper, till exempel en för säkerhetsgrupper (`securityEnabled EQUAL True`) och en för distributionsgrupper (`securityEnabled EQUAL False`).

![Konfigurera fliken i synkronisering regeln editor ](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Den här regeln används för att definiera vilka grupper som ska etableras till Azure AD. Distributionsgrupper måste vara e-postaktiverad som ska synkroniseras med Azure AD, men för säkerhetsgrupper ett e-postmeddelande krävs inte.

#### <a name="join-rules"></a>Ansluta till regler
Det tredje avsnittet används för att konfigurera hur objekt i anslutarplatsen som är relaterade till objekt i metaversum. Regeln som du har tittat i tidigare inte har någon konfiguration för regler för att ansluta till istället du ska titta på **i från AD – användare ansluta**.

![Häng med fliken regler i synkronisering regeln editor ](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

Innehållet i join-regel beror på de matchande alternativ som valts i installationsguiden. Utvärderingen börjar med ett objekt i anslutarplatsen källa för en regel för inkommande, och varje grupp i join-reglerna utvärderas i ordning. Om ett källobjekt utvärderas för att matcha exakt ett objekt i metaversumet med någon av join-regler och är objekt som anslutna. Om alla regler har utvärderats och det finns ingen matchning, används länktyp på sidan beskrivning. Om den här konfigurationen är inställd på **etablera**, och sedan ett nytt objekt skapas i målet, metaversum. Att etablera ett nytt objekt att metaversum kallas även att **projekt** ett objekt till metaversum.

Join-reglerna utvärderas bara en gång. När en anslutarplatsen och ett metaversumobjekt ansluten, förblir de kopplade så länge som omfattningen för Synkroniseringsregeln är fortfarande nöjd.

När du utvärderar Synkroniseringsregler måste endast en regel för synkronisering med definierade join-regler vara i omfånget. Ett fel genereras om det finns flera Synkroniseringsregler med anslutning till regler för ett visst objekt. Därför är det bästa sättet att ha endast en regel för synkronisering med join definieras när flera Synkroniseringsregler är inom omfånget för ett objekt. I out-of-box-konfigurationen för Azure AD Connect-synkronisering dessa regler kan hittas genom att titta på namnet och hitta de med ordet **ansluta** i slutet av namnet. En Synkroniseringsregel utan några definierade join-regler gäller attributflöden när andra Synkroniseringsregler sammankopplade objekten eller etablerat ett nytt objekt i målet.

Om du tittar på i bilden ovan, kan du se att regeln försöker ansluta till **objectSID** med **msExchMasterAccountSid** (Exchange) och **msRTCSIP-OriginatorSid** (Lync) vilket är vad vi förväntar oss i en topologi med en skog resursen för lagringskonton. Du hittar samma regeln på alla skogar. Antas att varje skog kan vara ett konto eller en resurs skog. Den här konfigurationen fungerar även om du har konton som bor i en enda skog och behöver inte vara ansluten.

#### <a name="transformations"></a>Transformationer
Transformationsavsnittet definierar alla attributflöden som gäller för målobjektet när objekten kopplas och Omfångsfilter är uppfyllt. Gå tillbaka till den **i från AD – användaren AccountEnabled** Synkroniseringsregeln du hittar följande omvandlingar:

![Transformationer fliken synkroniserade regeln editor ](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Om du vill placera den här konfigurationen i kontexten i en skog distribution resursen för Lagringskonton förväntas att hitta ett aktiverat konto i kontoskogen och ett inaktiverat konto i resursskogen med inställningar för Exchange och Lync. Synkroniseringsregeln som du tittar på innehåller attributen som krävs för att logga in och dessa attribut ska flöda från skogen där det finns ett aktiverat konto. Alla dessa attributflöden placeras tillsammans i en regel för synkronisering.

En omvandling kan ha olika typer: Konstant Direct och uttryck.

* Ett konstant flöde flödar alltid en hårdkodad-värde. I fallet anger det alltid värdet **SANT** i metaversum-attribut med namnet **accountEnabled**.
* Värdet för attributet i källan till Målattributet som flödar alltid i ett direkt flöde-är.
* Den tredje flow-typen är uttryck och det gör att mer avancerade konfigurationer.

Uttryck-språket är VBA (Visual Basic for Applications), så personer med erfarenhet av Microsoft Office eller VBScript identifierar formatet. Attribut är inom hakparenteser [attributeName]. Attributnamn och funktionsnamnen är skiftlägeskänsliga, men Synchronization Rules Editor som utvärderar uttrycken och skicka någon varning om uttrycket inte är giltig. Alla uttryck uttrycks på en enda rad med kapslade funktioner. För att visa kraften i configuration-språket, är här flödet för pwdLastSet, men med ytterligare kommentarer infogas:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Se [förstå uttryck för deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) mer information om Uttrycksspråk för attributflöden.

### <a name="precedence"></a>Prioritet
Du nu har tittat på vissa enskilda Synkroniseringsregler, men reglerna fungerar tillsammans i konfigurationen. I vissa fall kan bidragit ett attributvärde från flera Synkroniseringsregler till samma Målattributet. I det här fallet används Attributprioritet för att avgöra vilket attribut wins. Exempelvis kan du titta på attributet sourceAnchor. Det här attributet är ett viktigt attribut för att kunna logga in på Azure AD. Du kan hitta ett attributflöde för det här attributet i två olika Synkroniseringsregler, **i från AD – användaren AccountEnabled** och **i från AD – användaren vanliga**. På grund av Synkroniseringsregeln prioritet attributet sourceAnchor är som tillförts från skogen med ett aktiverat konto först när det finns flera objekt som är anslutna till metaversumobjekt. Om det finns inga aktiverade konton Synkroniseringsmotorn och använder sedan den övergripande Synkroniseringsregeln **i från AD – användaren vanliga**. Den här konfigurationen garanterar att även för konton som är inaktiverade, det finns fortfarande ett sourceAnchor.

![Synkroniseringsregler inkommande](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Prioritet för Synkroniseringsregler som anges i grupper med installationsguiden. Alla regler i en grupp har samma namn, men de är anslutna till olika anslutna kataloger. Installationsguiden ger regeln **i från AD – användare ansluta** högsta prioritet och itererar över alla anslutna AD-kataloger. Det fortsätter sedan med nästa grupper av regler i en viss ordning. I en grupp läggs reglerna i den ordning som kopplingarna som har lagts till i guiden. Om en annan koppling har lagts till via guiden, synkroniseringsreglerna sorteras och den nya kopplingen regler infogas senast i varje grupp.

### <a name="putting-it-all-together"></a>Färdigställa allt
Vi nu vet hur Synkroniseringsregler för att kunna förstå hur konfigurationen fungerar med olika Synkroniseringsregler. Om du tittar på en användare och attribut som har bidragit till metaversum tillämpas reglerna i följande ordning:

| Namn | Kommentar |
|:--- |:--- |
| I från AD-koppling av användare |Regel för att ansluta till utrymme anslutningsobjekt med metaversum. |
| I från AD-användarkonto är aktiverat |Attribut som krävs för att logga in till Azure AD och Office 365. Vi vill att dessa attribut från aktiverat konto. |
| I från AD – användaren gemensamma från Exchange |Attribut hittades i den globala adresslistan. Vi förutsätter att datakvaliteten är bäst i skogen där vi har hittat användarens postlåda. |
| I från AD – vanliga användare |Attribut hittades i den globala adresslistan. Om vi inte kunde hitta en postlåda, kan andra kopplade objekt bidra attributvärdet. |
| I från AD – användaren Exchange |Finns bara om Exchange har identifierats. Den förs vidare alla infrastruktur Exchange-attribut. |
| I från AD – användaren Lync |Finns bara om Lync har identifierats. Den förs vidare alla infrastruktur Lync-attribut. |

## <a name="next-steps"></a>Nästa steg
* Läs mer om konfigurationsmodellen i [förstå deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Läs mer om Uttrycksspråk i [förstå uttryck för deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Fortsätt att läsa hur out-of-box-konfigurationen fungerar i [förstå användare och kontakter](concept-azure-ad-connect-sync-user-and-contacts.md)
* Se hur du ändrar något praktiskt med deklarativ etablering i [hur du gör en ändring i standardkonfigurationen](how-to-connect-sync-change-the-configuration.md).

**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

