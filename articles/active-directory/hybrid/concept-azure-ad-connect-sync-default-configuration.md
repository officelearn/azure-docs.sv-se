---
title: 'Synkronisering av Azure AD Connect: Förstå standardkonfigurationen | Microsoft-dokument'
description: I den här artikeln beskrivs standardkonfigurationen i Azure AD Connect-synkronisering.
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
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2886b842aab81732beec0fdd7957aab8e2b4f5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548874"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Azure AD Connect-synkronisering: Förstå standardkonfigurationen
I den här artikeln beskrivs de färdiga konfigurationsreglerna. Den dokumenterar reglerna och hur dessa regler påverkar konfigurationen. Den går också igenom standardkonfigurationen för Azure AD Connect-synkronisering. Målet är att läsaren förstår hur konfigurationsmodellen, med namnet deklarativ etablering, fungerar i ett verkligt exempel. Den här artikeln förutsätter att du redan har installerat och konfigurerar Azure AD Connect-synkronisering med installationsguiden.

Om du vill förstå detaljerna i konfigurationsmodellen läser du [Förstå deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Färdiga regler från lokalt till Azure AD
Följande uttryck finns i den färdiga konfigurationen.

### <a name="user-out-of-box-rules"></a>Färdiga regler för användare
Dessa regler gäller även för objekttypen iNetOrgPerson.

Ett användarobjekt måste uppfylla följande för att synkroniseras:

* Måste ha en källaAnchor.
* När objektet har skapats i Azure AD kan sourceAnchor inte ändras. Om värdet ändras lokalt slutar objektet synkroniseras tills sourceAnchor ändras tillbaka till sitt tidigare värde.
* Måste ha attributet accountEnabled (userAccountControl) ifyllt. Med en lokal Active Directory finns det här attributet alltid och fylls i.

Följande användarobjekt synkroniseras **inte** med Azure AD:

* `IsPresent([isCriticalSystemObject])`. Kontrollera att många färdiga objekt i Active Directory, till exempel det inbyggda administratörskontot, inte synkroniseras.
* `IsPresent([sAMAccountName]) = False`. Kontrollera att användarobjekt utan sAMAccountName-attribut inte synkroniseras. Detta fall skulle bara praktiskt taget hända i en domän som uppgraderas från NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Synkronisera inte tjänstkontot som används av Azure AD Connect-synkroniseringen och dess tidigare versioner.
* Synkronisera inte Exchange-konton som inte fungerar i Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Synkronisera inte objekt som inte fungerar i Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Denna bitmask (&H21C07000) skulle filtrera bort följande objekt:
  * E-postaktiverad gemensam mapp (i förhandsgranskning från och med version 1.1.524.0)
  * Systemdrådarbrevlåda
  * Postlåda databaspostlåda (systempostlåda)
  * Universal Security Group (skulle inte gälla för en användare, men finns av äldre skäl)
  * Icke-Universell grupp (skulle inte gälla för en användare, men finns av äldre skäl)
  * Plan för postlåda
  * Identifieringspostlåda
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synkronisera inte några replikeringsofferobjekt.

Följande attributregler gäller:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. Attributet sourceAnchor har inte bidragit från en länkad postlåda. Det förutsätts att om en länkad postlåda har hittats, är det faktiska kontot ansluten senare.
* Exchange-relaterade attribut synkroniseras endast om attributet **mailNickName** har ett värde.
* När det finns flera skogar förbrukas attribut i följande ordning:
  1. Attribut relaterade till inloggning (till exempel userPrincipalName) bidrar från skogen med ett aktiverat konto.
  2. Attribut som finns i en Exchange GAL (Global Adresslista) bidrar från skogen med en Exchange-postlåda.
  3. Om ingen postlåda kan hittas kan dessa attribut komma från vilken skog som helst.
  4. Exchange-relaterade attribut (tekniska attribut som inte visas i GAL) bidrar från skogen där `mailNickname ISNOTNULL`.
  5. Om det finns flera skogar som uppfyller någon av dessa regler används skapandeordningen (datum/tid) för kopplingarna (skogarna) för att avgöra vilken skog som bidrar med attributen. Den första skogen som är ansluten blir den första skogen som synkroniseras. 

### <a name="contact-out-of-box-rules"></a>Kontakta out-of-box regler
Ett kontaktobjekt måste uppfylla följande för att synkroniseras:

* Kontakten måste vara e-postaktiverad. Det kontrolleras med följande regler:
  * `IsPresent([proxyAddresses]) = True)`. Attributet proxyAddresses måste fyllas i.
  * En primär e-postadress finns i attributet proxyAddresses eller e-postattributet. Förekomsten av \@ en används för att kontrollera att innehållet är en e-postadress. En av dessa två regler måste utvärderas till True.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Finns det en post med "SMTP:" och \@ om det finns, kan en hittas i strängen?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Finns e-postattributet och om \@ det är det, kan ett finnas i strängen?

Följande kontaktobjekt synkroniseras **inte** med Azure AD:

* `IsPresent([isCriticalSystemObject])`. Kontrollera att inga kontaktobjekt som är markerade som kritiska synkroniseras. Bör inte vara någon med en standardkonfiguration.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Dessa objekt skulle inte fungera i Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synkronisera inte några replikeringsofferobjekt.

### <a name="group-out-of-box-rules"></a>Gruppera färdiga regler
Ett gruppobjekt måste uppfylla följande för att synkroniseras:

* Måste ha färre än 50 000 medlemmar. Det här antalet är antalet medlemmar i den lokala gruppen.
  * Om den har fler medlemmar innan synkroniseringen startar första gången synkroniseras inte gruppen.
  * Om antalet medlemmar växer från när det skapades från början, slutar det synkronisera när det når 50 000 medlemmar tills antalet medlemmar är lägre än 50 000 igen.
  * Antalet 50 000 medlemmar tillämpas också av Azure AD. Du kan inte synkronisera grupper med fler medlemmar även om du ändrar eller tar bort den här regeln.
* Om gruppen är en **distributionsgrupp**måste den också vara e-postaktiverad. Se [Kontaktregler](#contact-out-of-box-rules) för den här regeln tillämpas.

Följande gruppobjekt synkroniseras **inte** med Azure AD:

* `IsPresent([isCriticalSystemObject])`. Kontrollera att många färdiga objekt i Active Directory, till exempel den inbyggda administratörsgruppen, inte synkroniseras.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Äldre grupp som används av DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. rollgrupp.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Synkronisera inte några replikeringsofferobjekt.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal out-of-box regler
FSPs är kopplade till\*"alla" ( ) objekt i metaversum. I verkligheten händer den här kopplingen bara för användare och säkerhetsgrupper. Den här konfigurationen säkerställer att medlemskap mellan skogar matchas och representeras korrekt i Azure AD.

### <a name="computer-out-of-box-rules"></a>Regler för datorns färdiga regler
Ett datorobjekt måste uppfylla följande för att synkroniseras:

* `userCertificate ISNOTNULL`. Endast Windows 10-datorer fyller i det här attributet. Alla datorobjekt med ett värde i det här attributet synkroniseras.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Förstå scenariot med färdiga regler
I det här exemplet använder vi en distribution med en kontoskog (A), en resursskog (R) och en Azure AD-katalog.

![Bild med scenariobeskrivning](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

I den här konfigurationen antas det att det finns ett aktiverat konto i kontoskogen och ett inaktiverat konto i resursskogen med en länkad postlåda.

Vårt mål med standardkonfigurationen är:

* Attribut relaterade till inloggning synkroniseras från skogen med det aktiverade kontot.
* Attribut som finns i GAL (Global Adresslista) synkroniseras från skogen med postlådan. Om ingen postlåda kan hittas används någon annan skog.
* Om en länkad postlåda hittas måste det länkade aktiverade kontot hittas för att objektet ska exporteras till Azure AD.

### <a name="synchronization-rule-editor"></a>Redigerare för synkroniseringsregel
Konfigurationen kan visas och ändras med verktyget Synkroniseringsregler Editor (SRE) och en genväg till det finns i start-menyn.

![Ikon för Redigerare av synkroniseringsregler](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

SRE är ett resurspaketverktyg och det installeras med Azure AD Connect-synkronisering. För att kunna starta den måste du vara medlem i ADSyncAdmins-gruppen. När det börjar ser du ungefär så här:

![Inkommande synkroniseringsregler](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

I det här fönstret visas alla synkroniseringsregler som skapats för konfigurationen. Varje rad i tabellen är en synkroniseringsregel. Till vänster under Regeltyper visas de två olika typerna: Inkommande och Utgående. Inkommande och Utgående är från synen på metaversum. Du kommer främst att fokusera på de inkommande reglerna i denna översikt. Den faktiska listan över synkroniseringsregler beror på det identifierade schemat i AD. I bilden ovan har kontoskogen (fabrikamonline.com) inga tjänster, till exempel Exchange och Lync, och inga synkroniseringsregler har skapats för dessa tjänster. I resursskogen (res.fabrikamonline.com) hittar du dock synkroniseringsregler för dessa tjänster. Innehållet i reglerna är olika beroende på vilken version som har identifierats. I en distribution med Exchange 2013 finns det till exempel fler attributflöden konfigurerade än i Exchange 2010/2007.

### <a name="synchronization-rule"></a>Synkroniseringsregel
En synkroniseringsregel är ett konfigurationsobjekt med en uppsättning attribut som flödar när ett villkor är uppfyllt. Den används också för att beskriva hur ett objekt i ett kopplingsutrymme är relaterat till ett objekt i metaversumet, så kallat **koppling** eller **matchning**. Synkroniseringsreglerna har ett prioritetsvärde som anger hur de relaterar till varandra. En synkroniseringsregel med ett lägre numeriskt värde har en högre prioritet och i en attributflödeskonflikt vinner högre prioritet konfliktlösningen.

Som ett exempel kan du titta på synkroniseringsregeln **i från AD – AnvändarkontoEnabled**. Markera den här raden i SRE och välj **Redigera**.

Eftersom den här regeln är en out-of-box-regel får du en varning när du öppnar regeln. Du bör inte göra några [ändringar i out-of-box regler,](how-to-connect-sync-best-practices-changing-default-configuration.md)så du får frågan vad dina avsikter är. I det här fallet vill du bara visa regeln. Välj **Nej**.

![Varning för synkroniseringsregler](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

En synkroniseringsregel har fyra konfigurationsavsnitt: Beskrivning, omfångsfilter, Kopplingsregler och Omvandlingar.

#### <a name="description"></a>Beskrivning
Det första avsnittet innehåller grundläggande information, till exempel ett namn och en beskrivning.

![Fliken Beskrivning i redigeraren för synkroniseringsregel](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Du hittar också information om vilket anslutet system som den här regeln är relaterad till, vilken objekttyp i det anslutna systemet som den gäller för och objekttypen metaversum. Metaversumobjekttypen är alltid person oavsett när källobjekttypen är en användare, iNetOrgPerson eller kontakt. Metaversumobjekttypen bör aldrig ändras så den skapas som en generisk typ. Länktypen kan ställas in på Join, StickyJoin eller Provision. Den här inställningen fungerar tillsammans med avsnittet Gå ihop regler och beskrivs senare.

Du kan också se att den här synkroniseringsregeln används för lösenordssynkronisering. Om en användare är i omfånget för den här synkroniseringsregeln synkroniseras lösenordet från lokalt till moln (förutsatt att du har aktiverat funktionen för synkronisering av lösenord).

#### <a name="scoping-filter"></a>Omfångsfilter
Avsnittet Omfångsfilter används för att konfigurera när en synkroniseringsregel ska gälla. Eftersom namnet på synkroniseringsregeln som du tittar på anger att den endast ska tillämpas för aktiverade användare, konfigureras scopet så att **AD-attributanvändarenAccountControl** inte får ha bit 2 inställt. När synkroniseringsmotorn hittar en användare i AD tillämpas den här synkroniseringsregeln när **userAccountControl** anges till decimalvärdet 512 (aktiverad normal användare). Den tillämpar inte regeln när användaren har **användarenAccountControl** inställd på 514 (inaktiverad normal användare).

![Fliken Omfång i redigeraren för synkroniseringsregel](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

Omfångsfiltret har grupper och satser som kan kapslas. Alla satser i en grupp måste vara uppfyllda för att en synkroniseringsregel ska gälla. När flera grupper definieras måste minst en grupp vara nöjd för att regeln ska gälla. Det vill än, en logisk ELLER utvärderas mellan grupper och en logisk OCH utvärderas i en grupp. Ett exempel på den här konfigurationen finns i den utgående synkroniseringsregeln **ut till AAD – Gruppkoppling**. Det finns flera synkroniseringsfiltergrupper, till exempel`securityEnabled EQUAL True`en för säkerhetsgrupper`securityEnabled EQUAL False`( ) och en för distributionsgrupper ( ).

![Fliken Omfång i redigeraren för synkroniseringsregel](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Den här regeln används för att definiera vilka grupper som ska etableras till Azure AD. Distributionsgrupper måste vara e-postaktiverade för att synkroniseras med Azure AD, men för säkerhetsgrupper krävs inget e-postmeddelande.

#### <a name="join-rules"></a>Gå med i regler
Det tredje avsnittet används för att konfigurera hur objekt i kopplingsutrymmet relaterar till objekt i metaversumet. Regeln som du har tittat på tidigare har ingen konfiguration för join rules, så istället ska du titta på **In från AD – Användarkoppling**.

![Fliken Kopplingsregler i redigeraren för synkroniseringsregel](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

Innehållet i kopplingsregeln beror på vilket matchningsalternativ som valts i installationsguiden. För en inkommande regel börjar utvärderingen med ett objekt i källkopplingsutrymmet och varje grupp i kopplingsreglerna utvärderas i följd. Om ett källobjekt utvärderas så att det matchar exakt ett objekt i metaversumet med hjälp av en av kopplingsreglerna sammanfogas objekten. Om alla regler har utvärderats och det inte finns någon matchning används länktypen på beskrivningssidan. Om den här konfigurationen är inställd på **Etablera**, skapas ett nytt objekt i målet, metaversumet, om det finns minst ett attribut i kopplingsvillkoren (har ett värde). För att etablera ett nytt objekt till metaversumet kallas det också att **projicera** ett objekt till metaversumet.

Kopplingsreglerna utvärderas bara en gång. När ett kopplingsutrymmesobjekt och ett metaversumobjekt är sammanfogade förblir de kopplade så länge synkroniseringsregelns omfattning fortfarande är uppfyllt.

Vid utvärdering av synkroniseringsregler måste endast en synkroniseringsregel med anslutningsregler definieras vara i omfattning. Om flera synkroniseringsregler med anslutningsregler hittas för ett objekt genereras ett fel. Därför är det bästa att endast ha en synkroniseringsregel med koppling definierad när flera synkroniseringsregler är i omfång för ett objekt. I den färdiga konfigurationen för Azure AD Connect-synkronisering kan dessa regler hittas genom att titta på namnet och hitta dem med ordet **Anslut** i slutet av namnet. En synkroniseringsregel utan några definierade kopplingsregler tillämpar attributflödena när en annan synkroniseringsregel sammanfogade objekten eller etablerade ett nytt objekt i målet.

Om du tittar på bilden ovan kan du se att regeln försöker koppla **objektSID** med **msExchMasterAccountSid** (Exchange) och **msRTCSIP-OriginatorSid** (Lync), vilket är vad vi förväntar oss i en kundtopologi för kontoresurs. Du hittar samma regel på alla skogar. Antagandet är att varje skog kan vara antingen ett konto eller en resursskog. Den här konfigurationen fungerar även om du har konton som bor i en enda skog och inte behöver anslutas.

#### <a name="transformations"></a>Transformationer
Omformningsavsnittet definierar alla attributflöden som gäller för målobjektet när objekten är sammanfogade och scopefiltret är uppfyllt. Om du går tillbaka till regeln **In från AD – User AccountEnabled** Synchronization hittar du följande omvandlingar:

![Fliken Omvandlingar i redigeraren för synkroniseringsregel](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Om du vill placera den här konfigurationen i kontext, i en kontoresursskogsdistribution, förväntas det hitta ett aktiverat konto i kontoskogen och ett inaktiverat konto i resursskogen med Exchange- och Lync-inställningar. Synkroniseringsregeln som du tittar på innehåller de attribut som krävs för inloggning och dessa attribut ska flöda från skogen där det finns ett aktiverat konto. Alla dessa attributflöden sätts samman i en synkroniseringsregel.

En omvandling kan ha olika typer: Konstant, Direkt och Uttryck.

* Ett konstant flöde flödar alltid ett hårdkodat värde. I fallet ovan anger den alltid värdet **True** i metaversumattributet **accountEnabled**.
* Ett direkt flöde flödar alltid värdet för attributet i källan till målattributet som det är.
* Den tredje flödestypen är Expression och möjliggör mer avancerade konfigurationer.

Uttrycksspråket är VBA (Visual Basic for Applications), så personer med erfarenhet av Microsoft Office eller VBScript känner igen formatet. Attributen omges av hakparenteser, [attributeName]. Attributnamn och funktionsnamn är skiftlägeskänsliga, men Redigeraren för synkroniseringsregler utvärderar uttrycken och ger en varning om uttrycket inte är giltigt. Alla uttryck uttrycks på en enda rad med kapslade funktioner. För att visa kraften i konfigurationsspråket, här är flödet för pwdLastSet, men med ytterligare kommentarer infogas:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .NET datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Mer information om uttrycksspråket för attributflöden finns i [Förstå deklarativa etableringsuttrycken.](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)

### <a name="precedence"></a>Prioritet
Du har nu tittat på några enskilda synkroniseringsregler, men reglerna fungerar tillsammans i konfigurationen. I vissa fall bidrar ett attributvärde från flera synkroniseringsregler till samma målattribut. I det här fallet används attributprioritet för att avgöra vilket attribut som vinner. Som ett exempel, titta på attributet sourceAnchor. Det här attributet är ett viktigt attribut för att kunna logga in på Azure AD. Du kan hitta ett attributflöde för det här attributet i två olika synkroniseringsregler, **In från AD – AnvändarkontoEnabled** och In från AD – User **Common**. På grund av synkroniseringsregelprioritet bidrar attributet sourceAnchor från skogen med ett aktiverat konto först när det finns flera objekt kopplade till metaversumobjektet. Om det inte finns några aktiverade konton använder synkroniseringsmotorn synkroniseringsregeln **catch-all In från AD – User Common**. Den här konfigurationen säkerställer att även för konton som är inaktiverade finns det fortfarande en sourceAnchor.

![Inkommande synkroniseringsregler](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Prioriteten för synkroniseringsregler anges i grupper av installationsguiden. Alla regler i en grupp har samma namn, men de är anslutna till olika anslutna kataloger. Installationsguiden ger regeln **In från AD – User Join** högsta prioritet och itererar över alla anslutna AD-kataloger. Den fortsätter sedan med nästa grupp av regler i en fördefinierad ordning. I en grupp läggs reglerna till i den ordning som kopplingarna lades till i guiden. Om en annan koppling läggs till via guiden ordnas synkroniseringsreglerna om och den nya kopplingens regler infogas sist i varje grupp.

### <a name="putting-it-all-together"></a>Färdigställa allt
Vi vet nu tillräckligt om synkroniseringsregler för att kunna förstå hur konfigurationen fungerar med de olika synkroniseringsreglerna. Om du tittar på en användare och de attribut som bidrar till metaversumet tillämpas reglerna i följande ordning:

| Namn | Kommentar |
|:--- |:--- |
| In från AD – Användarkoppling |Regel för att sammanfoga kopplingsutrymmesobjekt med metaversum. |
| In från AD – UserAccount aktiverat |Attribut som krävs för inloggning till Azure AD och Office 365. Vi vill ha dessa attribut från det aktiverade kontot. |
| In från AD – Användare som är vanliga från Exchange |Attribut som finns i den globala adresslistan. Vi antar att datakvaliteten är bäst i skogen där vi har hittat användarens postlåda. |
| In från AD – Användare Vanliga |Attribut som finns i den globala adresslistan. Om vi inte hittade en postlåda kan alla andra sammanfogade objekt bidra med attributvärdet. |
| In från AD – Användarutbyte |Det finns bara om Exchange har upptäckts. Den flödar alla Exchange-attribut för infrastruktur. |
| In från AD – User Lync |Det finns bara om Lync har identifierats. Den flödar alla Lync-attribut för infrastruktur. |

## <a name="next-steps"></a>Nästa steg
* Läs mer om konfigurationsmodellen i [Förstå deklarativa etablering .](concept-azure-ad-connect-sync-declarative-provisioning.md)
* Läs mer om uttrycksspråket i [Förstå deklarativa etableringsuttryck](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Fortsätt läsa hur den färdiga konfigurationen fungerar i [Förstå användare och kontakter](concept-azure-ad-connect-sync-user-and-contacts.md)
* Se hur du gör en praktisk ändring med deklarativ etablering i [Så här gör du en ändring av standardkonfigurationen](how-to-connect-sync-change-the-configuration.md).

**Avsnitt om översikt**

* [Synkronisering av Azure AD Connect: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

