---
title: "Azure AD Connect: Förstå deklarativ etablering | Microsoft Docs"
description: "Förklarar deklarativ etablering Konfigurationsmodell i Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: mtillman
editor: 
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 7e299fb33bdbd514a8fbc96c6953c9a8ca70f54a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect-synkronisering: Förstå deklarativ etablering
Det här avsnittet beskriver Konfigurationsmodell i Azure AD Connect. Modellen kallas deklarativ etablering och du kan göra en konfigurationsändring med enkel. Många saker som beskrivs i det här avsnittet avancerade och krävs inte för de flesta kundscenarier.

## <a name="overview"></a>Översikt
Deklarativ etablering bearbetar objekt som kommer från en ansluten källkatalog och avgör hur objekt och attribut ska omvandlas från en källa för ett mål. Ett objekt har bearbetats i en pipeline för synkronisering och pipelinen är densamma för inkommande och utgående regler. En inkommande regel är från en anslutningsplatsen till metaversum och en utgående regel är från metaversum till en anslutningsplatsen.

![Synkronisera pipeline](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

Pipelinen har flera olika moduler. Var och en ansvarar för ett begrepp i objektet synkroniseringen.

![Synkronisera pipeline](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

* Källa, källobjektet
* [Scope](#scope), hittar alla sync-regler som ingår i omfattningen
* [Anslut](#join), anger förhållandet mellan anslutningsutrymme och metaversum
* [Transformera](#transform), beräknar hur attribut ska transformeras och flöde
* [Prioritet](#precedence), matchar motstridiga attributet bidrag
* Mål, målobjektet

## <a name="scope"></a>Omfång
Scope-modulen utvärderar ett objekt och anger de regler som ingår i omfattningen och ska tas med i bearbetningen. Beroende på värdena attribut på objektet utvärderas synkronisering av olika regler i omfånget. Till exempel har en inaktiverad användare med någon Exchange-postlåda olika regler än en aktiverad användare med en postlåda.  
![Omfång](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

Omfånget är definierad som grupper och -satser. Satserna är inuti en grupp. En logisk AND används mellan alla satser i en grupp. Till exempel (avdelning = IT och land = Danmark). Ett logiskt OR används mellan grupper.

![Omfång](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
Detta scope i den här bilden ska läsas som (avdelning = IT och land = Danmark) eller (land = Sverige). Om grupp 1 eller grupp 2 utvärderas till sant kommer regeln finns i omfattningen.

Scope-modulen stöder följande åtgärder.

| Åtgärd | Beskrivning |
| --- | --- |
| LIKA, NOTEQUAL |En sträng jämför som utvärderar om värdet är lika med värdet i attributet. Flera värden attribut finns i ISIN och ISNOTIN. |
| LESSTHAN LESSTHAN_OR_EQUAL |Jämför en sträng som utvärderar om värdet är mindre än värdet i attributet. |
| INNEHÅLLER, NOTCONTAINS |En sträng jämför som utvärderar om värde finns någonstans i värdet i attributet. |
| STARTSWITH, NOTSTARTSWITH |En sträng jämför som utvärderar om värdet är i början av värdet i attributet. |
| ENDSWITH NOTENDSWITH |En sträng jämför som utvärderar om värdet är i slutet av värdet i attributet. |
| GREATERTHAN GREATERTHAN_OR_EQUAL |En sträng jämför som utvärderar om värdet är större än värdet i attributet. |
| ISNULL ISNOTNULL |Utvärderar om attributet saknas från objektet. Om attributet inte är närvarande och därför null är regeln i sitt omfång. |
| ISIN ISNOTIN |Utvärderar om värdet i attributet definierad. Den här åtgärden är flera värden variant av LIKVÄRDIGT och NOTEQUAL. Attributet ska vara ett flervärdesattribut om värdet finns i någon av attributvärdena, sedan regeln i sitt omfång. |
| ISBITSET ISNOTBITSET |Utvärderar om en viss biten är aktiverad. Till exempel kan användas för att utvärdera bitar i userAccountControl att se om en användare är aktiverat eller inaktiverat. |
| ISMEMBEROF ISNOTMEMBEROF |Värdet måste innehålla ett unikt namn i en grupp i anslutningsplatsen. Om objektet är en medlem av gruppen som anges, är regeln i sitt omfång. |

## <a name="join"></a>Slå ihop
Join-modul i pipeline för synkronisering ansvarar för att hitta relationen mellan objektet i källan och ett objekt i målet. På en ingående regel är den här relationen ett objekt i kopplingen kan söka efter en relation till ett objekt i metaversumet.  
![Anslut mellan cs och mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
Målet är att se om det finns ett objekt redan i metaversum skapats av en annan koppling det ska kopplas till. Till exempel ska användaren från kontoskogen ett konto resursskogen kopplas till användaren från resursskogen.

Kopplingar används främst på regler för inkommande trafik för att ansluta utrymme kopplingsobjekt tillsammans på samma metaversum-objekt.

Kopplingarna definieras som en eller flera grupper. Du har satser i en grupp. En logisk AND används mellan alla satser i en grupp. Ett logiskt OR används mellan grupper. Grupperna bearbetas i ordning uppifrån och ned. När en grupp har hittat en matchning med ett objekt i målet, utvärderas inga andra join-regler. Om noll eller fler än ett objekt hittades, fortsätter bearbetning till nästa grupp av regler. Därför ska reglerna skapas i den ordning som de flesta explicit först och mer fuzzy i slutet.  
![Ansluta till definition](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
Kopplingar i den här bilden bearbetas uppifrån och ned. Synkronisera pipeline ser först om det finns en matchning på employeeID. Om inte, den andra regeln ser om kontonamnet kan användas för att ansluta till objekten tillsammans. Om det inte matchar antingen, är tredje och sista regeln en mer fuzzy matchning med hjälp av namnet på användaren.

Om alla join-regler har utvärderats och det är inte exakt en matchning av **länktypen** på den **beskrivning** sidan används. Om det här alternativet är inställt på **etablera**, skapas ett nytt objekt i målet.  
![Etablera eller koppling](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

Ett objekt ska bara ha en enda synkroniseringsregel med anslutning till regler i sitt omfång. Om det finns flera regler för synkronisering där koppling definieras, uppstår ett fel. Prioritet används inte för att lösa konflikter mellan koppling. Ett objekt måste ha en join-regel i omfånget för attribut för att flöda med samma inkommande/utgående riktning. Om du behöver flödet attribut både inkommande och utgående till samma objekt, måste du ha både ett inkommande och en utgående synkroniseringsregel med Anslut.

Utgående koppling har särskilda beteenden när den försöker etablera ett objekt till en mål-anslutningsplatsen. DN-attributet används för att först prova en omvänd-koppling. Om det finns redan ett objekt i anslutningsplatsen mål med samma DN, är objekt som anslutna.

Modulen koppling utvärderas bara när när en ny synkroniseringsregel kommer in scope. När du har anslutit till ett objekt, det inte koppla från även om kopplingsvillkor är inte längre uppfyllt. Om du vill sedan en frånkoppling av ett objekt måste gå synkroniseringsregel som ansluten objekt utanför omfånget.

### <a name="metaverse-delete"></a>Ta bort metaversum
En metaversumobjekt förblir så länge det finns en synkroniseringsregel i omfång med **länktypen** inställd på **etablera** eller **StickyJoin**. En StickyJoin används när en koppling inte är tillåtet att etablera ett nytt objekt att metaversum, men när den har anslutits kan det måste tas bort i källan innan metaversum-objekt tas bort.

När en metaversumobjekt tas bort, alla objekt som är associerade med ett utgående synkroniseringsregel som markerats för **etablera** markeras för en borttagning.

## <a name="transformations"></a>Omvandlingar
Omvandlingarna används för att definiera hur attribut ska flödas från källan till målet. Flödena kan ha något av följande **flöda typer**: direkta eller konstant uttryck. Ett direkt flöde flödar ett attributvärde som-är utan ytterligare omvandlingar. Ett konstant värde anger det angivna värdet. Ett uttryck använder deklarativ etablering Uttrycksspråk för att uttrycka hur omvandlingen ska vara. Information om Uttrycksspråk kan hittas i den [förstå deklarativ etablering Uttrycksspråk](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) avsnittet.

![Etablera eller koppling](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

Den **tillämpas när** kryssrutan som anger att attributet ska endast anges när objektet skapas. Den här konfigurationen kan till exempel användas för att ange ett initialt lösenord för nytt användarobjekt.

### <a name="merging-attribute-values"></a>Koppla samman attributvärden
I attributflöden finns en inställning som avgör om flera värden attribut ska sammanfogas från flera olika kopplingar. Standardvärdet är **uppdatering**, vilket tyder på att synkronisering regeln med högst prioritet bör win.

![Sammanfoga typer](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

Det finns också **sammanfoga** och **MergeCaseInsensitive**. Dessa alternativ kan du koppla värden från olika källor. Till exempel kan den användas för att sammanfoga attributet medlem eller proxyAddresses från flera olika skogar. När du använder det här alternativet kommer alla regler för synkronisering i omfånget för ett objekt måste använda samma kopplingstyp. Du kan inte definiera **uppdatering** från en koppling och **sammanfoga** från en annan. Om du försöker får felmeddelande du ett.

Skillnaden mellan **sammanfoga** och **MergeCaseInsensitive** så att bearbeta dubblerade attributvärden. Synkroniseringsmotorn säkerställer dubblettvärden inte infogas i Målattributet. Med **MergeCaseInsensitive**, dubblettvärden med bara en skillnad om inte kommer att finnas. Till exempel du ska inte visas både ”SMTP:bob@contoso.com” och ”smtp:bob@contoso.com” i Målattributet. **Sammanfoga** bara titta på de exakta värdena och flera värden där det endast finns en skillnad i fall kan vara tillgänglig.

Alternativet **ersätta** är samma som **uppdatering**, men används inte.

### <a name="control-the-attribute-flow-process"></a>Kontrollera attributet flödet processen
När flera regler för inkommande synkronisering är konfigurerade för att bidra till samma attribut för metaversum används prioritet för att fastställa vinnaren. Synkroniseringsregel med högsta prioriteten (lägsta numeriska värde) kommer att bidra med värdet. Samma sker regler för utgående trafik. Synkroniseringen regel med högsta prioritet wins och bidra värdet till ansluten katalog.

I vissa fall kan i stället för att bidra med ett värde bestämmer på synkroniseringsregel hur andra regler ska uppträda. Det finns några särskilda literaler som används för det här fallet.

Regler för inkommande synkronisering trafik literalen **NULL** kan användas för att indikera att flödet har inget värde med egna bidrag. En annan regel med lägre prioritet kan ge ett värde. Om ingen regel bidrog med ett värde, bort metaversum-attribut. För en regel för utgående trafik om **NULL** är det sista värdet när alla regler för synkronisering har bearbetats, tas värdet bort i katalogen anslutna.

Literalen **AuthoritativeNull** liknar **NULL** men med skillnaden att inga regler med lägre prioritet kan bidra till ett värde.

Ett attributflöde kan också använda **IgnoreThisFlow**. Den liknar NULL som anger det finns inget att bidra. Skillnaden är att det inte tar bort ett redan befintligt värde i målet. Den fungerar som attributflödet har aldrig varit det.

Här är ett exempel:

I *ut till AD - användaren Exchange hybrid* finns följande flöde:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Det här uttrycket ska läsas som: om postlådan finns i Azure AD, sedan flöda attribut från Azure AD AD. Om inte, flödar inte något tillbaka till Active Directory. I det här fallet, skulle den behålla det befintliga värdet i AD.

### <a name="importedvalue"></a>ImportedValue
Funktionen ImportedValue skiljer sig från andra funktioner eftersom attributets namn måste stå inom citattecken i stället för hakparenteser:  
`ImportedValue("proxyAddresses")`.

Vanligtvis under synkroniseringen använder attributet det förväntade värdet, även om den inte har exporterats ännu eller ett fel togs emot under exporten (”överst på torn”). En inkommande synkronisering förutsätter att ett attribut som ännu inte har nått en ansluten katalog så småningom når den. I vissa fall är det viktigt att synkronisera bara ett värde som har bekräftats av ansluten katalog (”hologram och delta importera torn”).

Ett exempel på den här funktionen finns i out-of-box Synkroniseringsregeln *i från AD-användaren vanliga från Exchange*. I Exchange Hybrid ska mervärde i Exchange online endast synkroniseras när det har bekräftats att värdet har exporterats:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Prioritet
När flera regler för synkronisering försöker bidra med samma attributvärde till målet används prioritet värdet för att fastställa vinnaren. Regeln med högst prioritet, lägsta numeriska värdet, kommer att bidra med attributet i en konflikt.

![Sammanfoga typer](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

Den här ordningen kan användas för att definiera mer exakta attributflöden för en mindre deluppsättning objekt. Till exempel out-för-box-reglerna Kontrollera som attribut från ett aktiverat konto (**användare AccountEnabled**) har högre prioritet från andra konton.

Prioritet kan definieras mellan kopplingar. Som tillåter anslutningar med bättre data med egna bidrag värden först.

### <a name="multiple-objects-from-the-same-connector-space"></a>Flera objekt från samma anslutningsplatsen
Om du har flera objekt i samma anslutningsplatsen anslutna till samma metaversumobjekt måste prioritet justeras. Synkroniseringsmotorn är inte kan avgöra prioritet om flera objekt finns i omfattningen av samma synkroniseringsregel. Det är tvetydig vilka källobjektet bör bidra värdet till metaversum. Den här konfigurationen har rapporterats som tvetydig även om attributen i källan har samma värde.  
![Flera objekt som är anslutna till samma mv-objekt](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

Du måste ändra omfånget för reglerna för synkronisering så att källobjekt har olika sync regler i omfånget för det här scenariot. Gör att du kan definiera olika prioritet.  
![Flera objekt som är anslutna till samma mv-objekt](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Nästa steg
* Läs mer om Uttrycksspråk i [förstå uttryck för deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
* Se hur deklarativ etablering är att använda out-of-box i [förstå standardkonfigurationen](active-directory-aadconnectsync-understanding-default-configuration.md).
* Se hur du ändrar något praktiska med deklarativ etablering i [hur du gör en ändring i standardkonfigurationen](active-directory-aadconnectsync-change-the-configuration.md).
* Fortsätta att läsa hur användare och kontakter tillsammans [förstå användare och kontakter](active-directory-aadconnectsync-understanding-users-and-contacts.md).

**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)

**Referensinformation**

* [Azure AD Connect-synkronisering: funktioner referens](active-directory-aadconnectsync-functions-reference.md)
