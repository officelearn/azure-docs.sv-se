---
title: 'Azure AD Connect: Förstå deklarativ etablering | Microsoft Docs'
description: Förklarar deklarativ etablering konfigurationsmodellen i Azure AD Connect.
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
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 45b145d9a8922bc3da50cef7d9fa7aacf260417d
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54471786"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect-synkronisering: Förstå deklarativ etablering
Det här avsnittet beskriver Konfigurationsmodell i Azure AD Connect. Modellen kallas deklarativ etablering och det kan du se en konfigurationsändring enkelt. Många saker som beskrivs i det här avsnittet avancerade och krävs inte för de flesta kundscenarier med.

## <a name="overview"></a>Översikt
Deklarativ etablering bearbetar objekt som kommer från en ansluten källkatalog och avgör hur objekt och attribut ska omvandlas från en källa till ett mål. Ett objekt ska bearbetas i en pipeline för synkronisering och pipelinen är densamma för inkommande och utgående regler. En regel för inkommande trafik från en anslutarplats till metaversum och en utgående regel är från metaversum till en anslutarplats.

![Synkronisera pipeline](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

Pipelinen har flera olika moduler. Var och en ansvarar för ett begrepp i synkronisering av objektet.

![Synkronisera pipeline](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Källa, källobjektet
* [Omfång](#scope), söker efter alla Synkroniseringsregler som är inom omfånget
* [Ansluta till](#join), anger förhållandet mellan connector space och metaverse
* [Omvandla](#transform), beräknar hur attribut ska omvandlas och flow
* [Prioritet](#precedence), matchas som i konflikt attributet bidrag
* Mål, målobjektet

## <a name="scope"></a>Scope
Utvärderar ett objekt i omfånget-modulen och anger de regler som omfattas och ska tas med i bearbetningen. Beroende på värden för attributen för objektet utvärderas olika Synkroniseringsregler i omfånget. Exempelvis kan har en inaktiverad användare med ingen Exchange-postlåda olika regler än en aktiverad användare med en postlåda.  
![Scope](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

Omfånget är definierad som grupper och -satser. Paragrafer finns i en grupp. Ett logiskt AND används mellan alla satser i en grupp. Till exempel (avdelning = IT och land = Danmark). Ett logiskt OR används mellan grupper.

![Scope](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
Detta scope i den här bilden ska läsas som (avdelning = IT och land = Danmark) eller (land = Sverige). Om grupp 1 eller grupp 2 utvärderas till sant kommer regeln finns i omfattningen.

Följande åtgärder har stöd för scope-modulen.

| Åtgärd | Beskrivning |
| --- | --- |
| LIKA MED, NOTEQUAL |En sträng jämför som utvärderar om värdet är lika med värdet i attributet. Se ISIN och ISNOTIN för flera värden attribut. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Jämför en sträng som utvärderar om värdet är mindre än värdet i attributet. |
| INNEHÅLLER, NOTCONTAINS |En sträng jämför som utvärderar om värde finns någonstans i värdet i attributet. |
| STARTSWITH, NOTSTARTSWITH |En sträng jämför som utvärderar om värdet är i början av värdet i attributet. |
| ENDSWITH NOTENDSWITH |En sträng jämför som utvärderar om värdet är i slutet av värdet i attributet. |
| GREATERTHAN GREATERTHAN_OR_EQUAL |En sträng jämför som utvärderar om värdet är större än värdet i attributet. |
| ISNULL ISNOTNULL |Utvärderar om attributet saknas från objektet. Om attributet inte är närvarande och därför null, är regeln i omfånget. |
| ISIN ISNOTIN |Utvärderar om värdet finns i attributet definierad. Den här åtgärden är flera värden variant av EQUAL och NOTEQUAL. Attributet ska vara ett flervärdesattribut och om värdet finns i någon av attributvärdena, sedan regeln i omfånget. |
| ISBITSET ISNOTBITSET |Utvärderar om en viss biten är aktiverad. Exempelvis kan användas för att utvärdera bits i userAccountControl att se om en användare är aktiverat eller inaktiverat. |
| ISMEMBEROF ISNOTMEMBEROF |Värdet ska innehålla ett unikt namn för Bindning till en grupp i anslutarplatsen. Om objektet är medlem i den angivna gruppen, är regeln i omfånget. |

## <a name="join"></a>Slå ihop
Join-modulen i pipelinen synkronisering ansvarar för att hitta relationen mellan objektet i källan och ett objekt i målet. På en ingående regel är den här relationen ett objekt i en anslutarplats att söka efter en relation till ett objekt i metaversum.  
![Ansluta till mellan cs och mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
Målet är att se om det finns ett objekt redan i metaversum skapats av en annan anslutning, bör det associeras med. Till exempel bör en konto-resursskog användaren från kontoskogen vara ansluten med användaren från resursskogen.

Kopplingar används huvudsakligen för regler för inkommande trafik för att ansluta till anslutningsobjekt som utrymme tillsammans att samma metaversumobjekt.

Kopplingarna definieras som en eller flera grupper. Du har satser i en grupp. Ett logiskt AND används mellan alla satser i en grupp. Ett logiskt OR används mellan grupper. Grupperna som bearbetas i ordning från början till slutet. När en grupp har hittat exakt en matchning med ett objekt i målet, utvärderas inga andra join-regler. Om noll eller fler än ett objekt hittas, fortsätter bearbetningen till nästa grupp av regler. Därför ska reglerna skapas i den ordning som de flesta explicit först och mer fuzzy i slutet.  
![Ansluta till definition](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
Kopplingar i den här bilden bearbetas uppifrån och ned. Först ser pipelinen synkronisering om det finns en matchning på employeeID. Om inte, den andra regeln ser om kontonamnet kan användas för att ansluta till objekten tillsammans. Om detta inte är en matchning antingen, är tredje och sista regeln en mer ungefärlig matchning med hjälp av namnet på användaren.

Om alla join-regler har utvärderats och det är inte exakt en matchning på **länktyp** på den **beskrivning** sidan används. Om det här alternativet är inställt på **etablera**, skapas ett nytt objekt i målet.  
![Tilldela eller ta bort koppling](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Ett-objekt bör bara ha en enda synkroniseringsregel med anslutning till regler i omfånget. Om det finns flera Synkroniseringsregler där join definieras, uppstår ett fel. Prioritet används inte för att lösa konflikter med anslutning till. Ett objekt måste ha en join-regel i omfånget för attribut flöda med samma inkommande/utgående riktning. Om du behöver användarflödets attribut både inkommande och utgående trafik till samma objekt, måste du ha både ett inkommande och en utgående synkroniseringsregel med join.

Utgående anslutning till har särskilda beteenden vid försök att etablera ett objekt till en mål-anslutningsplatsen. DN-attributet används för att först prova en omvänd-koppling. Om det finns redan ett objekt i anslutarplatsen mål med samma DN, är objekt som anslutna.

Modulen join utvärderas bara när när en ny synkroniseringsregel kommer in omfång. När ett objekt har anslutit det inte koppla från även om kopplingsvillkor är inte längre uppfyllt. Om du vill sedan en frånkoppling av ett objekt måste synkroniseringsregel som anslutna objekten hamnar utanför omfånget.

### <a name="metaverse-delete"></a>Ta bort metaversum
En metaversumobjekt finns kvar så länge det finns en synkroniseringsregel i omfång med **länktyp** inställd **etablera** eller **StickyJoin**. En StickyJoin används när en koppling inte är tillåtet att etablera ett nytt objekt att metaversum, men när den har anslutit, det måste tas bort i källan innan metaversumobjekt tas bort.

När en metaversumobjekt raderas alla objekt som är associerade med en utgående synkroniseringsregel som markerats för **etablera** markeras för en borttagning.

## <a name="transformations"></a>Transformationer
Omvandlingarna används för att definiera hur attributen ska flöda från källan till målet. Flöden kan ha något av följande **flow typer**: Direkt-konstant eller -uttryck. Ett direkt flöde flödar ett attributvärde som – är utan ytterligare omvandlingar. Ett konstant värde anger det angivna värdet. Ett uttryck som använder deklarativ etablering Uttrycksspråk för att uttrycka hur transformationen ska vara. Information om uttryck som språket finns i den [förstå deklarativ etablering Uttrycksspråk](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) avsnittet.

![Tilldela eller ta bort koppling](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

Den **gäller en gång** kryssrutan definierar att attributet ska bara anges när objektet skapas. Den här konfigurationen kan till exempel användas för att ställa in ett initialt lösenord för ett nytt användarobjekt.

### <a name="merging-attribute-values"></a>Sammanslagning av attributvärden
I attributflöden finns en inställning som avgör om flera värden attribut ska sammanfogas från flera olika anslutningar. Standardvärdet är **uppdatering**, vilket betyder att synkroniseringsregel med högsta prioritet bör vinna.

![Sammanfoga typer](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

Det finns också **sammanfoga** och **MergeCaseInsensitive**. Dessa alternativ kan du sammanfoga värden från olika källor. Exempelvis kan användas den för att sammanfoga attributet medlem eller proxyAddresses från flera olika skogar. När du använder det här alternativet kan måste alla Synkroniseringsregler i omfånget för ett objekt använda samma kopplingstyp. Du kan inte definiera **uppdatering** från ett anslutningsprogram och **sammanfoga** från en annan. Om du försöker felmeddelande du ett.

Skillnaden mellan **sammanfoga** och **MergeCaseInsensitive** så att bearbeta dubblerade attributvärden. Synkroniseringsmotorn ser till att duplicerade värden inte infogas i Målattributet. Med **MergeCaseInsensitive**, dubblettvärden med endast en skillnad om inte kommer att finnas. Exempel: du bör inte se både ”SMTP:bob@contoso.com” och ”smtp:bob@contoso.com” i Målattributet. **Sammanfoga** bara tittar på exakta värden och flera värden där det endast finns en skillnad i fallet kanske finns.

Alternativet **Ersätt** är samma som **uppdatering**, men den inte används.

### <a name="control-the-attribute-flow-process"></a>Kontrollera attributet flow processen
Om flera regler för inkommande synkronisering har konfigurerats för att bidra till samma attribut för metaversum, används prioritet för att fastställa vinnaren. Synkroniseringsregel med högst prioritet (lägsta numeriska värde) kommer att bidra med värdet. Samma sker för utgående regler. Synkroniseringen regel med högsta prioritet wins och bidra värdet till den anslutna katalogen.

I vissa fall kan i stället för att bidra med ett värde bestämma på synkroniseringsregel hur andra regler ska fungera. Det finns några särskilda litteraler som används för det här fallet.

Regler för inkommande synkronisering trafik literalen **NULL** kan användas för att indikera att flödet har inte något värde att bidra. En annan regel med lägre prioritet kan bidra med ett värde. Om ingen regel bidragit med ett värde, bort attribut för metaversum. För en utgående regel om **NULL** är det slutliga värdet när alla Synkroniseringsregler har bearbetats, tas värdet bort i den anslutna katalogen.

Literalen **AuthoritativeNull** liknar **NULL** men med skillnaden att inga regler med lägre prioritet kan bidra med ett värde.

Ett attributflöde kan också använda **IgnoreThisFlow**. Det liknar NULL i den mening att det indikerar att det finns inget att bidra. Skillnaden är att det inte tar bort ett redan befintligt värde i målet. Det är som om attributflödet har aldrig varit där.

Här är ett exempel:

I *ut till AD - användaren Exchange-hybrid* följande flöde finns:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Det här uttrycket ska läsas som: om postlådan finns i Azure AD, sedan flow attribut från Azure AD till AD. Annars kan du inte flödar något tillbaka till Active Directory. Det skulle i så fall behålla det befintliga värdet i AD.

### <a name="importedvalue"></a>ImportedValue
Funktionen ImportedValue skiljer sig från alla andra funktioner eftersom attributnamnet måste stå inom citattecken i stället för hakparenteser:  
`ImportedValue("proxyAddresses")`.

Vanligtvis under synkroniseringen använder det förväntade värdet av ett attribut även om den inte har exporterats ännu eller ett fel togs emot under export (”överst på tower”). En inkommande synkronisering förutsätter att ett attribut som ännu inte har nått en ansluten katalog så småningom når den. I vissa fall är det viktigt att synkronisera bara ett värde som har godkänts av den anslutna katalogen (”hologram och delta importera tower”).

Ett exempel på den här funktionen finns i Synkroniseringsregel för out-of-box *i från AD – vanliga användare från Exchange*. I Hybrid Exchange ska mervärde i Exchange online bara synkroniseras när den har bekräftats att värdet har exporterats:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Prioritet
När flera Synkroniseringsregler försöker bidra med samma attribut-värde till målet, används värdet för prioritet att fastställa vinnaren. Regeln med högst prioritet, lägsta numeriska värdet kommer att bidra med attributet i en konflikt.

![Sammanfoga typer](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Den här sorteringen kan användas för att definiera mer exakt attributflöden för en mindre deluppsättning objekt. Exempel: out-för-box-reglerna Kontrollera som attribut från ett aktiverat konto (**användaren AccountEnabled**) har högre prioritet från andra konton.

Prioritet kan definieras mellan kopplingar. Som tillåter anslutningar med bättre data kan bidra värden först.

### <a name="multiple-objects-from-the-same-connector-space"></a>Flera objekt från samma anslutningsplatsen
Om du har flera objekt i samma anslutningsplatsen anslutna till samma metaversumobjekt måste prioritet justeras. Om flera objekt finns i omfattningen av samma synkroniseringsregel, är Synkroniseringsmotorn inte kunna avgöra prioritet. Det är tvetydig vilka källobjektet bör bidra värdet till metaversum. Den här konfigurationen har rapporterats som tvetydig även om attributen i källan har samma värde.  
![Flera objekt som är anslutna till samma mv-objekt](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

Du behöver ändra omfånget för reglerna för synkronisering så att källobjekt har olika Synkroniseringsregler i omfånget för det här scenariot. Där du kan definiera olika prioritet.  
![Flera objekt som är anslutna till samma mv-objekt](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Nästa steg
* Läs mer om Uttrycksspråk i [förstå uttryck för deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Se hur deklarativ etablering är att använda out-of-box i [förstå standardkonfigurationen](concept-azure-ad-connect-sync-default-configuration.md).
* Se hur du ändrar något praktiskt med deklarativ etablering i [hur du gör en ändring i standardkonfigurationen](how-to-connect-sync-change-the-configuration.md).
* Fortsätta att läsa hur användare och kontakter fungerar tillsammans [förstå användare och kontakter](concept-azure-ad-connect-sync-user-and-contacts.md).

**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)

**Referensämnen**

* [Azure AD Connect-synkronisering: Referens för funktioner](reference-connect-sync-functions-reference.md)
