---
title: 'Azure AD Connect: Förstå deklarativ etablering | Microsoft-dokument'
description: Förklarar konfigurationsmodellen för deklarativ etablering i Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 543c1a6706f794b81c4f93fc6fff3a61ed3fb9e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60246370"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Synkronisering av Azure AD Connect: Förstå deklarativ etablering
I det här avsnittet beskrivs konfigurationsmodellen i Azure AD Connect. Modellen kallas Declarative Etablering och det gör att du enkelt kan göra en konfigurationsändring. Många saker som beskrivs i det här avsnittet är avancerade och krävs inte för de flesta kundscenarier.

## <a name="overview"></a>Översikt
Deklarativ etablering bearbetar objekt som kommer in från en källa ansluten katalog och bestämmer hur objektet och attributen ska omvandlas från en källa till ett mål. Ett objekt bearbetas i en synkroniseringspipeline och pipelinen är densamma för inkommande och utgående regler. En inkommande regel är från ett kopplingsutrymme till metaversumet och en utgående regel är från metaversum till ett anslutningsutrymme.

![Synkronisera pipeline](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

Rörledningen har flera olika moduler. Var och en ansvarar för ett koncept i objektsynkronisering.

![Synkronisera pipeline](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Källa, Källobjektet
* [Scope](#scope), Söker efter alla synkroniseringsregler som finns i omfånget
* [Gå med](#join), Bestämmer förhållandet mellan kopplingsutrymme och metaversum
* Transformera, beräknar hur attribut ska omvandlas och flöda
* [Prioritet](#precedence), Löser attributbidrag i konflikt
* Mål, Målobjektet

## <a name="scope"></a>Omfång
Scopemodulen utvärderar ett objekt och bestämmer vilka regler som finns i omfånget och ska inkluderas i bearbetningen. Beroende på attributvärdena för objektet utvärderas olika synkroniseringsregler så att de är i omfånget. En inaktiverad användare utan Exchange-postlåda har till exempel andra regler än en aktiverad användare med en postlåda.  
![Omfång](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

Omfattningen definieras som grupper och satser. Satserna finns i en grupp. Ett logiskt OCH används mellan alla satser i en grupp. Till exempel (avdelning =IT OCH land = Danmark). Ett logiskt ELLER används mellan grupper.

![Omfång](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
Omfattningen i denna bild ska läsas som (avdelning = IT OCH land = Danmark) ELLER (land=Sverige). Om antingen grupp 1 eller grupp 2 utvärderas till true, är regeln i omfång.

Scopemodulen stöder följande åtgärder.

| Åtgärd | Beskrivning |
| --- | --- |
| LIKA, ANTECKNINGSKVALITET |En strängjämförelse som utvärderar om värdet är lika med värdet i attributet. För attribut med flera värden finns i ISIN och ISNOTIN. |
| LESSTHAN, LESSTHAN_OR_EQUAL |En strängjämförelse som utvärderar om värdet är mindre än värdet i attributet. |
| INNEHÅLLER, INTECONTAINS |En strängjämförelse som utvärderar om värdet kan hittas någonstans inuti värdet i attributet. |
| BÖRJAR MED, BÖRJAR MED |En strängjämförelse som utvärderar om värdet finns i början av värdet i attributet. |
| SLUTAR MED, INTE MED |En strängjämförelse som utvärderar om värdet finns i slutet av värdet i attributet. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |En strängjämförelse som utvärderar om värdet är större än värdet i attributet. |
| ISNULL, ISNOTNULL |Utvärderar om attributet saknas i objektet. Om attributet inte finns och därför null, är regeln i omfång. |
| ISIN, ISNOTIN |Utvärderar om värdet finns i det definierade attributet. Den här åtgärden är den flervärderade variationen mellan EQUAL och NOTEQUAL. Attributet är tänkt att vara ett attribut med flera värden och om värdet finns i något av attributvärdena, är regeln i omfånget. |
| ISBITSET, ISNOTBITSET |Utvärderar om en viss bit är inställd. Kan till exempel användas för att utvärdera bitarna i userAccountControl för att se om en användare är aktiverad eller inaktiverad. |
| ISMEDLEM, ISNOTMEDLEM |Värdet ska innehålla en DN till en grupp i kopplingsutrymmet. Om objektet är medlem i den angivna gruppen är regeln i omfånget. |

## <a name="join"></a>Slå ihop
Kopplingsmodulen i synkroniseringspipelinen är ansvarig för att hitta relationen mellan objektet i källan och ett objekt i målet. På en inkommande regel skulle den här relationen vara ett objekt i ett kopplingsutrymme och hitta en relation till ett objekt i metaversumet.  
![Gå mellan cs och mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
Målet är att se om det finns ett objekt som redan finns i metaversumet, skapat av en annan koppling, som det ska associeras med. I en kontoresursskog ska till exempel användaren från kontoskogen anslutas till användaren från resursskogen.

Kopplingar används främst på inkommande regler för att koppla ihop kopplingsutrymmesobjekt till samma metaversumobjekt.

Kopplingarna definieras som en eller flera grupper. I en grupp har du satser. Ett logiskt OCH används mellan alla satser i en grupp. Ett logiskt ELLER används mellan grupper. Grupperna bearbetas i ordning uppifrån och ned. När en grupp har hittat exakt en matchning med ett objekt i målet utvärderas inga andra kopplingsregler. Om noll eller fler än ett objekt hittas fortsätter bearbetningen till nästa regelgrupp. Därför bör reglerna skapas i den ordning som de mest explicita först och mer luddiga i slutet.  
![Definition av koppling](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
Kopplingarna i den här bilden bearbetas uppifrån och ned. Först synkpipelinen ser om det finns en matchning på employeeID. Om inte, ser den andra regeln om kontonamnet kan användas för att koppla ihop objekten. Om det inte heller är en matchning är den tredje och sista regeln en mer suddig matchning med hjälp av namnet på användaren.

Om alla kopplingsregler har utvärderats och det inte finns exakt en matchning används **länktypen** på sidan **Beskrivning.** Om det här alternativet är inställt **på Etablera**skapas ett nytt objekt i målet.  
![Etablera eller gå med](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Ett objekt ska bara ha en enda synkroniseringsregel med kopplingsregler i omfånget. Om det finns flera synkroniseringsregler där koppling definieras uppstår ett fel. Prioritet används inte för att lösa kopplingskonflikter. Ett objekt måste ha en kopplingsregel i omfånget för att attribut ska flöda med samma inkommande/utgående riktning. Om du behöver flöda attribut både inkommande och utgående till samma objekt, måste du ha både en inkommande och en utgående synkroniseringsregel med koppling.

Utgående koppling har ett speciellt beteende när den försöker etablera ett objekt till ett målkopplingsutrymme. DN-attributet används för att först försöka med en omvänd koppling. Om det redan finns ett objekt i målkopplingsutrymmet med samma DN sammanfogas objekten.

Kopplingsmodulen utvärderas bara en gång när en ny synkroniseringsregel kommer in i omfånget. När ett objekt har gått samman är det inte att skilja även om kopplingsvillkoren inte längre är uppfyllda. Om du vill skilja mellan ett objekt måste synkroniseringsregeln som gick med objekten gå utanför omfånget.

### <a name="metaverse-delete"></a>Ta bort metaversum
Ett metaversumobjekt finns kvar så länge det finns en synkroniseringsregel i omfånget med **länktyp** inställd på **Etablera** eller **StickyJoin**. En StickyJoin används när en koppling inte tillåts etablera ett nytt objekt till metaversumet, men när den har gått samman måste den tas bort i källan innan metaversumobjektet tas bort.

När ett metaversumobjekt tas bort markeras alla objekt som är associerade med en utgående synkroniseringsregel som markerats för **etablering** för en borttagning.

## <a name="transformations"></a>Transformationer
Omvandlingarna används för att definiera hur attribut ska flöda från källan till målet. Flödena kan ha en av följande **flödestyper:** Direct, Constant eller Expression. Ett direkt flöde, flöden ett attributvärde som är utan ytterligare omvandlingar. Ett konstant värde anger det angivna värdet. Ett uttryck använder det deklarativa etableringsuttrycksspråket för att uttrycka hur omvandlingen ska vara. Detaljerna för uttrycksspråket finns i avsnittet om förståelse av [deklarativa etablerande uttrycksspråk.](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)

![Etablera eller gå med](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

Kryssrutan **Använd en gång** definierar att attributet endast ska anges när objektet skapas. Den här konfigurationen kan till exempel användas för att ange ett första lösenord för ett nytt användarobjekt.

### <a name="merging-attribute-values"></a>Slå samman attributvärden
I attributflödena finns en inställning för att avgöra om attribut med flera värden ska slås samman från flera olika kopplingar. Standardvärdet är **Uppdatera**, vilket anger att synkroniseringsregeln med högst prioritet ska vinna.

![Koppla typer](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

Det finns också **Merge** och **MergeCaseInsensitive**. Med de här alternativen kan du slå samman värden från olika källor. Den kan till exempel användas för att slå samman attributet member eller proxyAddresses från flera olika skogar. När du använder det här alternativet måste alla synkroniseringsregler i omfånget för ett objekt använda samma kopplingstyp. Du kan inte definiera **Uppdatera** från en kopplingskoppling och **koppla** från en annan. Om du försöker visas ett felmeddelande.

Skillnaden mellan **Merge** och **MergeCaseInsensitive** är hur du bearbetar dubblettattributvärden. Synkroniseringsmotorn ser till att dubblettvärden inte infogas i målattributet. Med **MergeCaseInsensitive**kommer dubblettvärden med endast en skillnad i fall inte att finnas. Du bör till exempel inteSMTP:bob@contoso.comsesmtp:bob@contoso.combåde " " och " " i målattributet. **Sammanfogning** tittar bara på exakta värden och flera värden där det bara finns en skillnad i fall kan förekomma.

Alternativet **Ersätt** är detsamma som **Uppdatera**, men det används inte.

### <a name="control-the-attribute-flow-process"></a>Styra attributflödesprocessen
När flera inkommande synkroniseringsregler är konfigurerade för att bidra till samma metaversumattribut används prioritet för att bestämma vinnaren. Synkroniseringsregeln med högst prioritet (lägsta numeriskt värde) kommer att bidra med värdet. Samma sak händer för utgående regler. Synkroniseringsregeln med högst prioritet vinner och bidrar värdet till den anslutna katalogen.

I vissa fall bör synkroniseringsregeln avgöra hur andra regler ska bete sig i stället för att bidra med ett värde. Det finns några speciella litteraler som används för detta fall.

För inkommande synkroniseringsregler kan den litterala **NULL-värdet** användas för att ange att flödet inte har något värde att bidra med. En annan regel med lägre prioritet kan bidra med ett värde. Om ingen regel har bidragit med ett värde tas metaversumattributet bort. Om NULL är det slutliga värdet när alla synkroniseringsregler har bearbetats tas värdet bort i den anslutna katalogen om **NULL** är det slutliga värdet när alla synkroniseringsregler har bearbetats.

Den bokstavliga **AuktoritäraNullen** liknar **NULL** men med skillnaden att inga lägre prioritetsregler kan bidra med ett värde.

Ett attributflöde kan också använda **IgnoreThisFlow**. Det liknar NULL i den meningen att det visar att det inte finns något att bidra med. Skillnaden är att det inte tar bort ett redan befintligt värde i målet. Det är som om attributflödet aldrig har funnits där.

Här är ett exempel:

I *Ut till AD - User Exchange hybrid* följande flöde kan hittas:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Det här uttrycket ska läsas som: om användarpostlådan finns i Azure AD och sedan flödar attributet från Azure AD till AD. Om inte, flöda inte något tillbaka till Active Directory. I det här fallet skulle det behålla det befintliga värdet i AD.

### <a name="importedvalue"></a>ImportedValue
Funktionen ImportedValue skiljer sig från alla andra funktioner, eftersom attributnamnet måste omges av citattecken i stället för hakparenteser:  
`ImportedValue("proxyAddresses")`.

Vanligtvis under synkroniseringen använder ett attribut det förväntade värdet, även om det inte har exporterats ännu eller om ett fel har tagits emot under exporten ("toppen av tornet"). En inkommande synkronisering förutsätter att ett attribut som ännu inte har nått en ansluten katalog så småningom når den. I vissa fall är det viktigt att bara synkronisera ett värde som har bekräftats av den anslutna katalogen ("hologram och delta importtorn").

Ett exempel på den här funktionen finns i den färdiga synkroniseringsregeln *i från AD – Användare som är vanlig från Exchange*. I Hybrid Exchange ska mervärdet av Exchange online endast synkroniseras när det har bekräftats att värdet har exporterats:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Prioritet
När flera synkroniseringsregler försöker bidra med samma attributvärde till målet används prioritetsvärdet för att bestämma vinnaren. Regeln med högst prioritet, lägsta numeriska värde, kommer att bidra med attributet i en konflikt.

![Koppla typer](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Den här ordningen kan användas för att definiera mer exakta attributflöden för en liten delmängd av objekt. Till exempel kontrollerar out-of-box-reglerna att attribut från ett aktiverat konto (**User AccountEnabled**) har företräde från andra konton.

Prioritet kan definieras mellan kopplingar. Det gör att kopplingar med bättre data kan bidra med värden först.

### <a name="multiple-objects-from-the-same-connector-space"></a>Flera objekt från samma kopplingsutrymme
Om du har flera objekt i samma kopplingsutrymme sammanfogat till samma metaversumobjekt måste prioriteten justeras. Om flera objekt omfattas av samma synkroniseringsregel kan synkroniseringsmotorn inte bestämma prioriteten. Det är tvetydigt vilket källobjekt som ska bidra med värdet till metaversumet. Den här konfigurationen rapporteras som tvetydig även om attributen i källan har samma värde.  
![Flera objekt kopplade till samma mv-objekt](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

I det här fallet måste du ändra omfattningen av synkroniseringsreglerna så att källobjekten har olika synkroniseringsregler i omfånget. Det gör att du kan definiera olika prioritet.  
![Flera objekt kopplade till samma mv-objekt](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Nästa steg
* Läs mer om uttrycksspråket i [Förstå deklarativa etableringsuttryck](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Se hur deklarativ etablering används direkt i [Förstå standardkonfigurationen](concept-azure-ad-connect-sync-default-configuration.md).
* Se hur du gör en praktisk ändring med deklarativ etablering i [Så här gör du en ändring av standardkonfigurationen](how-to-connect-sync-change-the-configuration.md).
* Fortsätt att läsa hur användare och kontakter arbetar tillsammans i [Förstå användare och kontakter](concept-azure-ad-connect-sync-user-and-contacts.md).

**Avsnitt om översikt**

* [Synkronisering av Azure AD Connect: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

**Referensämnen**

* [Synkronisering av Azure AD Connect: Funktionsreferens](reference-connect-sync-functions-reference.md)
