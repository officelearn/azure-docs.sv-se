---
title: "Azure AD Connect-synkronisering: Förstå standardkonfigurationen | Microsoft Docs"
description: "Den här artikeln beskriver standardkonfigurationen i Azure AD Connect-synkronisering."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 32a693c059a1b4261f33a3d6f50f397365e9dac4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Azure AD Connect-synkronisering: Förstå standardkonfigurationen
Den här artikeln förklarar konfigurationsregler för out-of-box. Regler och hur dessa regler påverkar konfigurationen dokumenterat. Även vägleder dig genom standardkonfigurationen av Azure AD Connect-synkronisering. Målet är att läsaren förstår hur Konfigurationsmodell med namnet deklarativ etablering fungerar i ett verkliga exempel. Den här artikeln förutsätter att du redan har installerat och konfigurera Azure AD Connect-synkronisering med hjälp av installationsguiden.

För att förstå information om konfigurationsmodellen läsa [förstå deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Out-of-box regler från lokal till Azure AD
Följande uttryck kan hittas i out-of-box-konfigurationen.

### <a name="user-out-of-box-rules"></a>Användaren out-of-box-regler
Dessa regler gäller även för typ av iNetOrgPerson-objekt.

Ett användarobjekt måste uppfylla följande som ska synkroniseras:

* Måste ha en sourceAnchor.
* När objektet har skapats i Azure AD, kan inte sourceAnchor ändra. Om värdet är ändrade lokalt, stoppar objektet synkroniserar förrän sourceAnchor ändras tillbaka till dess tidigare värde.
* Ha måste attributet accountEnabled (userAccountControl) fyllts på. Med en lokal Active Directory är det här attributet alltid finns och är ifyllda.

Följande användarobjekt är **inte** synkroniseras till Azure AD:

* `IsPresent([isCriticalSystemObject])`. Se till att många out-of-box-objekt i Active Directory, till exempel det inbyggda administratörskontot, synkroniseras inte.
* `IsPresent([sAMAccountName]) = False`. Kontrollera användarobjekt med några sAMAccountName attribut inte synkroniseras. Det här fallet händer endast praktiskt taget i en domän som uppgraderats från NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Synkronisera inte tjänstkontot som används av Azure AD Connect-synkronisering och dess tidigare versioner.
* Synkronisera inte Exchange-konton som inte skulle fungera i Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Synkronisera inte objekt som inte fungerar i Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Den här bitmask (& H21C07000) filtrerar ut följande objekt:
  * E-postaktiverade offentlig mapp
  * System till postlåda
  * Postlåda databasen postlåda (System-postlåda)
  * Universell säkerhetsgrupp (inte gäller för en användare, men finns äldre skäl)
  * Icke-universell grupp (inte gäller för en användare, men finns äldre skäl)
  * Planera för postlåda
  * Identifiering av postlåda
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synkronisera inte alla objekt som är drabbade replikering.

Följande gäller attribut:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. Attributet sourceAnchor är inte bidragit från en länkad postlåda. Det förutsätts att om en länkad postlåda har hittats, det aktuella kontot är ansluten senare.
* Exchange-relaterade attribut synkroniseras bara om attributet **mailNickName** har ett värde.
* När det finns flera skogar, förbrukas attribut i följande ordning:
  1. Attribut som är relaterade till inloggning (till exempel userPrincipalName) tillhandahålls från skogen med ett aktiverat konto.
  2. Attribut som kan finnas i en Exchange-GAL (globala adresslistan) tillhandahålls från skogen med en Exchange-postlåda.
  3. Om ingen postlåda kan hittas kan dessa attribut kommer från en skog.
  4. Exchange-relaterade attribut (inte synligt i den globala Adresslistan tekniska attribut) tillhandahålls från skogen där `mailNickname ISNOTNULL`.
  5. Om det finns flera skogar som för att uppfylla dessa regler, används ordningen som skapas (tidsvärdet) till kopplingar (skog) för att avgöra vilken skog bidrar attribut.

### <a name="contact-out-of-box-rules"></a>Kontakta out-of-box-regler
En kontaktobjekt måste uppfylla följande som ska synkroniseras:

* Kontakten måste vara e-postfunktioner. Det har verifierats med följande regler:
  * `IsPresent([proxyAddresses]) = True)`. Attributet proxyAddresses måste fyllas i.
  * En primär e-postadress kan hittas i attributet proxyAddresses eller e-postattributet. Förekomsten av en @ används för att kontrollera att innehållet är en e-postadress. En av dessa två regler måste utvärderas till SANT.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Det finns en post med ”SMTP”: om det finns en @ hittas i strängen?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. E-postattributet fylls och om det är en @ hittas i strängen?

Kontakta följande objekt är **inte** synkroniseras till Azure AD:

* `IsPresent([isCriticalSystemObject])`. Se till att inga kontakta objekt som är markerat som kritiskt är synkroniserade. Får inte vara någon med en standardkonfiguration.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Dessa objekt fungerar inte i Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synkronisera inte alla objekt som är drabbade replikering.

### <a name="group-out-of-box-rules"></a>Gruppera out-of-box-regler
Ett gruppobjekt måste uppfylla följande som ska synkroniseras:

* Måste ha färre än 50 000 medlemmar. Det här antalet är antalet medlemmar i den lokala gruppen.
  * Om den har fler medlemmar innan synkroniseringen startas första gången, är gruppen inte synkroniserad.
  * Om antalet medlemmar växa från när den ursprungligen skapades, sedan när den når 50 000 medlemmar slutar den att synkronisera tills antalet medlemskap är lägre än 50 000 igen.
  * Obs: Antalet 50 000 medlemskap används också av Azure AD. Det går inte att synkronisera grupper med fler medlemmar, även om du ändrar eller tar bort den här regeln.
* Om gruppen är en **distributionsgrupp**, måste det också vara aktiverad e-post. Se [kontakta out-of-box regler](#contact-out-of-box-rules) för den här regeln tillämpas.

Följande gruppobjekt är **inte** synkroniseras till Azure AD:

* `IsPresent([isCriticalSystemObject])`. Se till att många out-of-box-objekt i Active Directory, till exempel den inbyggda administratörsgruppen, synkroniseras inte.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Äldre grupp som används av DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Rollen grupp.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synkronisera inte alla objekt som är drabbade replikering.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal out-of-box-regler
FSP: er som är anslutna till ”någon” (\*) objekt i metaversumet. I verkligheten sker bara den här kopplingen för användare och säkerhetsgrupper. Den här konfigurationen garanterar att mellan skogar medlemskap är löst och visas korrekt i Azure AD.

### <a name="computer-out-of-box-rules"></a>Datorn out-of-box-regler
Ett datorobjekt måste uppfylla följande som ska synkroniseras:

* `userCertificate ISNOTNULL`. Lägg till Windows 10-datorer i det här attributet. Alla datorobjekt som har ett värde i det här attributet är synkroniserade.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Förstå regler för out-of-box-scenario
I det här exemplet använder vi en distribution med en kontoskog (A), en resursskog (R) och en Azure AD-katalog.

![Bild med scenario-beskrivning](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

I den här konfigurationen förutsätts det finns ett aktiverat konto i skogen och ett inaktiverat konto i resursskogen med en länkad postlåda.

Vårt mål med standardkonfigurationen är:

* Attribut som rör inloggning synkroniseras från skogen med aktiverat konto.
* Attribut som finns i den globala Adresslistan (globala adresslistan) synkroniseras från skogen med postlådan. Om ingen postlåda hittar du används alla andra skogar.
* Om en länkad postlåda hittas hittas länkade aktiverat konto för objektet som ska exporteras till Azure AD.

### <a name="synchronization-rule-editor"></a>Synchronization Rule Editor
Konfigurationen kan visas och ändras med verktyget synkronisering regler Editor (SRE) och en genväg till den finns i start-menyn.

![Synkronisering sänder ikon](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

SRE är ett resource kit verktyg och den är installerad med Azure AD Connect-synkronisering. För att kunna starta den, måste du vara medlem i gruppen ADSyncAdmins. När den startar ser du ut så här:

![Inkommande Synkroniseringsregler](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

I det här fönstret kan se du alla Synkroniseringsregler som skapats för din konfiguration. Varje rad i tabellen är en regel för synkronisering. Till vänster under regeltyper visas två olika typer: inkommande och utgående. Inkommande och utgående är från vyn i metaversum. Du ska främst fokus på regler för inkommande trafik i den här översikten. Den aktuella listan över Synkroniseringsregler beror på identifierade schemat i AD. I bilden ovan kontoskog (fabrikamonline.com) har inte några tjänster, till exempel Exchange och Lync, och inga regler för synkronisering har skapats för dessa tjänster. Men i resursskogen (res.fabrikamonline.com) finns Synkroniseringsregler för dessa tjänster. Innehållet i reglerna är olika beroende på vilken version som har identifierats. Till exempel i en distribution med Exchange 2013 finns mer attributflöden konfigurerats än i Exchange 2010 2007.

### <a name="synchronization-rule"></a>Regel för synkronisering
En Synkroniseringsregel är ett konfigurationsobjekt med en uppsättning attribut som flödar när villkoret är uppfyllt. Det används också för att beskriva hur ett objekt i en anslutningsplatsen är relaterad till ett objekt i metaversumet kallas **koppling** eller **matchar**. De Synkroniseringsregler som har ett prioritet-värde som anger hur de relaterar till varandra. En Synkroniseringsregel med ett lägre numeriskt värde har högre prioritet och i ett flöde konflikt attributet högre prioritet wins konfliktlösningen.

Exempelvis kan titta på Synkroniseringsregeln **i från AD-användare AccountEnabled**. Markera den här raden i SRE och välj **redigera**.

Eftersom den här regeln är en regel för out-of-box, visas ett varningsmeddelande när du öppnar regeln. Du bör inte göra någon [ändringar av out-of-box regler](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), så uppmanas du din avsikt är. I detta fall kan vill du bara visa regeln. Välj **nr**.

![Synkronisering regler varning](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

En Synkroniseringsregel har fyra konfigurationsavsnitt: beskrivning, scope filter, koppling regler och transformationer.

#### <a name="description"></a>Beskrivning
Det första avsnittet innehåller grundläggande information, till exempel namn och beskrivning.

![Beskrivning av fliken synkroniserade Regelredigeraren ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

Du hittar även information om vilka anslutna systemet den här regeln gäller, vilket objekt av typen i det anslutna systemet som den gäller och metaversum-objekttyp. Typ av metaversumobjekt är alltid person oavsett när objektet källtyp är en användare, iNetOrgPerson eller kontakta. Typ av metaversumobjekt bör aldrig ändras så att den har skapats som en generisk typ. Länktypen kan anges för att ansluta till, StickyJoin eller etablera. Den här inställningen fungerar tillsammans med avsnittet ansluta regler och beskrivs senare.

Du kan också se att regeln synkronisering används för synkronisering av lösenord. Om en användare finns i omfånget för den här regeln för synkronisering, synkroniseras lösenordet från lokalt till molnet (förutsatt att du har aktiverat funktionen för synkronisering av lösenord).

#### <a name="scoping-filter"></a>Omfångsfilter
Avsnittet Omfångsfiltret används för att konfigurera när en Synkroniseringsregel ska tillämpas. Eftersom namnet på Synkroniseringsregeln tittar du anger bör endast användas för aktiverade användare, omfång har konfigurerats så att den AD-attributet **userAccountControl** får inte ha bitar 2 inställt. När Synkroniseringsmotorn hittar en användare i AD, gäller den synkroniseringen regeln när **userAccountControl** är inställd på decimalvärde 512 (aktiverad normal användare). Den gäller inte regeln när användaren har **userAccountControl** inställd på 514 (inaktiverad normal användare).

![Scope-fliken i Redigeraren för regel för synkronisering ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

Målgrupp filtret har grupper och satser som kan vara kapslade. Alla satser i en grupp måste vara uppfyllda för en Synkroniseringsregel ska gälla. När flera grupper har definierats måste i minst en grupp vara uppfyllda för regeln ska gälla. Det vill säga ett logiskt OR utvärderas mellan grupper och en logisk och utvärderas i en grupp. Ett exempel på den här konfigurationen finns i den utgående Synkroniseringsregel **på AAD – grupp ansluta**. Det finns flera filter synkroniseringsgrupper, till exempel en för säkerhetsgrupper (`securityEnabled EQUAL True`) och en för distributionsgrupper (`securityEnabled EQUAL False`).

![Scope-fliken i Redigeraren för regel för synkronisering ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Den här regeln används för att definiera vilka grupper som ska etableras till Azure AD. Distributionsgrupper måste vara e-post aktiverad synkroniseras med Azure AD, men för säkerhetsgrupper ett e-postmeddelande krävs inte.

#### <a name="join-rules"></a>Ansluta till regler
Det tredje avsnittet används för att konfigurera hur objekt i anslutningsplatsen relaterar till objekt i metaversumet. Regeln som du har tittat i tidigare har inte någon konfiguration för regler för att ansluta till istället du ska titta på **i från AD-användare ansluta**.

![Ansluta till fliken regler i Redigeraren för regel för synkronisering ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

Innehållet i join-regeln är beroende av det matchande alternativ som valts i installationsguiden. Utvärderingen startar med ett objekt i anslutningsplatsen källan för en regel för inkommande trafik och varje grupp i join-reglerna utvärderas i ordning. Om ett källobjekt utvärderas för att matcha exakt ett objekt i metaversumet med hjälp av en av reglerna för anslutning till är objekt som anslutna. Om alla regler har utvärderats och det finns ingen matchning, används länktypen på sidan beskrivning. Om den här konfigurationen är inställd på **etablera**, skapas ett nytt objekt i målet metaversum. Att etablera ett nytt objekt att metaversum kallas även att **projekt** ett objekt i metaversum.

Koppling reglerna utvärderas bara en gång. När ett utrymme connector-objekt och en metaversumobjekt ansluten, förblir de kopplade så länge omfånget för Synkroniseringsregeln är fortfarande nöjd.

När du utvärderar Synkroniseringsregler måste endast en regel för synkronisering med anslutning till definierade regler vara i omfånget. Ett fel returneras om det finns flera regler för synkronisering med anslutning till regler för ett objekt. Därför är det bästa sättet att endast en regel för synkronisering med Anslut definieras när flera Synkroniseringsregler som ingår i omfattningen för ett objekt. I out-of-box-konfigurationen för Azure AD Connect-synkronisering reglerna kan hittas genom att titta på namnet och hitta dem med ordet **ansluta** i slutet av namnet. En Synkroniseringsregel utan några definierade regler för koppling gäller attributflöden när andra Synkroniseringsregler sammankopplade objekten eller etablera ett nytt objekt i målet.

Om du tittar på bilden ovan kan du se att regeln försöker ansluta till **objectSID** med **msExchMasterAccountSid** (Exchange) och **msRTCSIP-OriginatorSid** (Lync), vilket är den förväntade i en skog konto-resurs-topologi. Du hittar samma regel i alla skogar. Antas att varje skog kan vara ett konto eller resurs-skog. Den här konfigurationen fungerar även om du har konton som finns i en enda skog och behöver inte vara ansluten.

#### <a name="transformations"></a>Omvandlingar
Transformationsavsnittet definierar alla attributflöden som gäller för målobjektet när objekten är anslutna och scope-filtret är uppfyllt. Gå tillbaka till den **i från AD-användare AccountEnabled** Synkroniseringsregeln du hittar följande omvandlingar:

![Transformationer fliken synkroniserade Regelredigeraren ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

För att placera den här konfigurationen i kontexten i en skog distribution konto-resurs förväntas att hitta ett aktiverat konto i kontoskogen och ett inaktiverat konto i resursskogen med inställningar för Exchange och Lync. Synkroniseringsregeln du tittar på innehåller attribut som krävs för inloggning och attributen ska flödas från skogen där det finns ett aktiverat konto. Alla dessa attributflöden placeras tillsammans i en regel för synkronisering.

En omvandling kan ha olika typer: konstant, direkt och uttryck.

* Ett konstant flöde flödar alltid en hårdkodad värde. I fallet alltid anges värdet **SANT** i metaversum-attribut med namnet **accountEnabled**.
* Ett direkt flöde flödar alltid värdet för attributet i källan till Målattributet som-är.
* Tredje flödestyp är uttryck och den kan användas för mer avancerade konfigurationer.

Det uttryck som språket är VBA (Visual Basic for Applications), så personer har erfarenhet av Microsoft Office eller VBScript ska identifiera format. Attribut omges av hakparenteser [attributeName]. Funktionsnamnen och attributnamn är skiftlägeskänsliga, men redigeraren synkronisering regler utvärderar uttrycken och ange en varning om uttrycket inte är giltigt. Alla uttryck anges på en enda rad med kapslade funktioner. Om du vill visa power för språket som konfiguration, är här flödet för pwdLastSet, men med ytterligare kommentarer infogas:

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

Se [förstå uttryck för deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) för mer information om uttryck språket för attributflöden.

### <a name="precedence"></a>Prioritet
Du nu har tittat på vissa enskilda regler för synkronisering, men reglerna tillsammans i konfigurationen. I vissa fall kan bidragit ett attributvärde från flera Synkroniseringsregler till samma Målattributet. I det här fallet används Attributprioritet för att avgöra vilket attribut wins. Exempelvis kan du titta på attributet sourceAnchor. Det här attributet är ett viktigt attribut för att kunna logga in på Azure AD. Du hittar ett attributflöde för det här attributet i två olika Synkroniseringsregler, **i från AD-användare AccountEnabled** och **i från AD-användaren vanliga**. På grund av Synkroniseringsregeln prioritet är attributet sourceAnchor bidragit från skogen med ett aktiverat konto först när det finns flera objekt som är anslutna till metaversumobjekt. Om det finns inga aktiverade konton Synkroniseringsmotorn och använder sedan Synkroniseringsregeln fångar in alla **i från AD-användaren vanliga**. Den här konfigurationen garanterar att även för konton som är inaktiverade, det finns fortfarande en sourceAnchor.

![Inkommande Synkroniseringsregler](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

Prioritet för Synkroniseringsregler som anges i grupper av installationsguiden. Alla regler i en grupp har samma namn, men de är anslutna till olika anslutna kataloger. Installationsguiden ger regeln **i från AD-användare ansluta** högsta prioritet och itererar över alla anslutna AD-kataloger. Det fortsätter sedan med nästa grupper av regler i en viss ordning. I en grupp läggs reglerna i den ordning som kopplingarna som har lagts till i guiden. Om du lägger till en annan koppling med guiden, synkroniseringsreglerna sorteras och den nya kopplingen regler senast infogas i varje grupp.

### <a name="putting-it-all-together"></a>Alltihop
Vi nu vet hur Synkroniseringsregler för att kunna förstå hur konfigurationen fungerar med olika regler för synkronisering. Om du tittar på en användare och de attribut som bidrog till metaversum regler i följande ordning:

| Namn | Kommentar |
|:--- |:--- |
| I från AD-användare ansluta till |Regel för att ansluta till koppling utrymme objekt med metaversum. |
| I från AD – UserAccount aktiverad |Attribut som krävs för inloggning till Azure AD och Office 365. Vi vill attributen från aktiverat konto. |
| I från AD-användaren gemensamma från Exchange |Attribut hittades i den globala adresslistan. Vi förutsätter att data quality är bäst i skogen där vi har hittat användarens postlåda. |
| I från AD-användaren gemensamma |Attribut hittades i den globala adresslistan. Om vi hittade en postlåda bidrar andra anslutna objektet attributvärdet. |
| I från AD-användaren Exchange |Finns bara om Exchange har upptäckts. Den förs vidare alla infrastruktur Exchange-attribut. |
| I från AD-användaren Lync |Finns bara om Lync har upptäckts. Den förs vidare alla infrastruktur Lync-attribut. |

## <a name="next-steps"></a>Nästa steg
* Läs mer om Konfigurationsmodell i [förstå deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Läs mer om Uttrycksspråk i [förstå uttryck för deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
* Fortsätta att läsa hur out-of-box-konfigurationen fungerar i [förstå användare och kontakter](active-directory-aadconnectsync-understanding-users-and-contacts.md)
* Se hur du ändrar något praktiska med deklarativ etablering i [hur du gör en ändring i standardkonfigurationen](active-directory-aadconnectsync-change-the-configuration.md).

**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)

