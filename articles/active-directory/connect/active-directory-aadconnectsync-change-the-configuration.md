---
title: "Azure AD Connect-synkronisering: gjort en konfigurationsändring i Azure AD Connect-synkronisering | Microsoft Docs"
description: "Vägleder dig igenom hur du gör en ändring i konfigurationen i Azure AD Connect-synkronisering."
services: active-directory
documentationcenter: 
author: andkjell
manager: mtillman
editor: 
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 3dc6be73abafb99772ed428bd4f22c1797c9b1bc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-sync-how-to-make-a-change-to-the-default-configuration"></a>Azure AD Connect-synkronisering: hur du gör en ändring i standardkonfigurationen
Syftet med det här avsnittet är leder dig igenom hur du gör ändringar i standardkonfigurationen i Azure AD Connect-synkronisering. Den innehåller steg för några vanliga scenarier. Med denna kunskap kan ska du kunna göra några enkla förändringar i din egen konfiguration baserat på dina egna regler.

## <a name="synchronization-rules-editor"></a>Redigeraren för regler för synkronisering
Redigeraren för regler för synkronisering används för att se och ändra standardkonfigurationen. Du hittar i Start-menyn under den **Azure AD Connect** grupp.  
![Start-menyn med synkronisering Rule Editor](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

När du öppnar den ser du standardregler för out-of-box.

![Synkronisera Rule Editor](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigera i Redigeraren
Listrutor längst upp i Redigeraren för kan du snabbt hitta en viss regel. Om du vill visa reglerna där attributet proxyAddresses ingår ändrar du till exempel av listrutorna för följande:  
![SRE filtrering](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
Om du vill återställa filtrering och läsa in en ny konfiguration, trycker du på **F5** på tangentbordet.

Upp till höger, du har en knapp **Lägg till ny regel**. Den här knappen används för att skapa en egen anpassad regel.

Längst ned ha knappar för agerar på valda synkroniseringsregel. **Redigera** och **ta bort** göra vad du förväntade dig. **Exportera** genererar ett PowerShell-skript för att återskapa regeln synkronisering. Den här proceduren kan du flytta en synkroniseringsregel från en server till en annan.

## <a name="create-your-first-custom-rule"></a>Skapa din första anpassad regel
Ändringar av attributflöden är de vanligaste ändringen. Data i källkatalogen kanske inte som Azure AD. I exemplet i det här avsnittet, som du vill kontrollera att det angivna namnet för en användare är alltid i **rätt skiftläge**.

### <a name="disable-the-scheduler"></a>Inaktivera Schemaläggaren
Den [scheduler](active-directory-aadconnectsync-feature-scheduler.md) körs var 30: e minut som standard. Du vill kontrollera att den inte startar när du gör ändringar och felsöka din nya regler. För att tillfälligt inaktivera Schemaläggaren, starta PowerShell och kör`Set-ADSyncScheduler -SyncCycleEnabled $false`

![Inaktivera Schemaläggaren](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Skapa regel
1. Klicka på **Lägg till ny regel**.
2. På den **beskrivning** sidan anger du följande:  
   ![Inkommande regel filtrering](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
   * Namn: Ge regeln ett beskrivande namn.
   * Beskrivning: Vissa förtydligande så att någon annan kan förstå vad regeln för.
   * Anslutna system: objektet finns i systemet. I det här fallet, Välj den Active Directory-koppling.
   * Anslutna System/metaversum-objekttyp: Välj **användare** och **Person** respektive.
   * Länktypen: Ändra värdet till **ansluta**.
   * Prioritet: Ange ett värde som är unikt i systemet. Ett lägre numeriskt värde anger högre prioritet.
   * Tagg: Lämna tomt. Endast out-of-box regler från Microsoft ska ha den här rutan fylls i med ett värde.
3. På den **Scoping filter** anger **givenName ISNOTNULL**.  
   ![Regel för inkommande trafik Omfångsfilter](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
   Det här avsnittet används för att definiera vilka objekt som regeln ska gälla för. Om den lämnas tom skulle regeln gälla för alla användarobjekt. Men som även konferensrum, tjänstkonton och andra icke personer användarobjekt.
4. På den **ansluta regler**, lämna det tomt.
5. På den **transformationer** ändrar ExchangeRate för FlowType till **uttryck**. Välj målattribut **givenName**, och ange i källan `PCase([givenName])`.
   ![Regel för inkommande trafik omvandlingar](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
   Synkroniseringsmotorn är skiftlägeskänslig både funktionsnamnet och namnet på attributet. Om du skriver något fel, se en varning när du lägger till regeln. Redigeraren för kan du spara och fortsätta, så du måste öppna regeln och rätta till regeln.
6. Klicka på **Lägg till** spara regeln.

Den nya anpassa regeln ska visas med andra regler för synkronisering i systemet.

### <a name="verify-the-change"></a>Bekräfta ändringen
Med den här nya ändringar som du vill kontrollera att den fungerar som väntat och inte som utlöste eventuella fel. Beroende på antalet objekt som du har, finns det två sätt att göra det här steget.

1. Kör en fullständig synkronisering för alla objekt
2. Kör en förhandsgranskning och en fullständig synkronisering på ett objekt

Starta **synkroniseringstjänsten** från start-menyn. Stegen i det här avsnittet finns i det här verktyget.

1. **Fullständig synkronisering för alla objekt**  
   Välj **kopplingar** längst upp. Identifiera kopplingen du har gjort en ändring i föregående avsnitt i det här fallet Active Directory Domain Services och markera den. Välj **kör** åtgärder och välj **fullständig synkronisering** och **OK**.
   ![Fullständig synkronisering](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
   Objekt har uppdaterats i metaversum. Nu vill du titta på objektet i metaversum.
2. **Förhandsgranskning och fullständig synkronisering på ett objekt**  
   Välj **kopplingar** längst upp. Identifiera kopplingen du har gjort en ändring i föregående avsnitt i det här fallet Active Directory Domain Services och markera den. Välj **söka Anslutarplats**. Använd scope för att hitta ett objekt som du vill använda för att testa ändringen. Markera objektet och klicka på **Preview**. I det nya fönstret Välj **genomför Preview**.  
   ![Genomför preview](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
   Ändringen genomförs nu till metaversum.

**Titta på objektet i metaversum**  
Nu vill du plocka några exempel objekt för att kontrollera att värdet är förväntat och att regeln tillämpas. Välj **metaversumsökningen** från början. Lägg till eventuella filter som du behöver för att hitta relevanta objekt. Öppna ett objekt i sökresultatet. Titta på attributvärdena och Kontrollera också i den **Sync regler** kolumn som regeln tillämpas som förväntat.  
![Metaversumsökning](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Aktivera Schemaläggaren
Om allt är som förväntat, kan du aktivera Schemaläggaren igen. Kör från PowerShell `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Andra vanliga flödet attributändringar
I föregående avsnitt beskrivs hur du gör ändringar i ett attributflöde. I det här avsnittet finns ytterligare exempel. Anvisningar att skapa regeln sync förkortas, men du kan hitta fullständig stegen i föregående avsnitt.

### <a name="use-another-attribute-than-the-default"></a>Använd ett annat attribut än standardvärdet
Det finns en skog där lokala alfabetet används för förnamn, efternamn och namn på Fabrikam. Latinska teckenrepresentation av dessa attribut finns i tilläggsattribut. När du skapar den globala adresslistan i Azure AD och Office 365 organisationen vill dessa attribut som ska användas i stället.

Med en standardkonfiguration ett objekt från den lokala skogen som ser ut så här:  
![Attributflöde 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Om du vill skapa en regel med andra attributflöden, gör du följande:

* Starta **Synchronization Rule Editor** från start-menyn.
* Med **inkommande** fortfarande markerat till vänster, klicka på knappen **Lägg till ny regel**.
* Ge regeln ett namn och beskrivning. Välj den lokala Active Directory och de relevanta objekttyperna. I **länktypen**väljer **ansluta**. Välj ett värde som inte används av en annan regel för prioritet. Out-of-box-regler som börjar med 100, så värdet 50 kan användas i det här exemplet.
  ![Attributflöde 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
* Lämna det tomt omfång (det vill säga ska gälla för alla användarobjekt i skogen).
* Lämna det tomt koppling regler (det vill säga att out-of-box regeln hanterar alla kopplingar).
* Skapa följande flödena i omvandlingar:  
  ![Attributflöde 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
* Klicka på **Lägg till** spara regeln.
* Gå till **Synchronization Service Manager**. På **kopplingar**, Välj den anslutning där vi har lagt till regeln. Välj **kör**, och **fullständig synkronisering**. En fullständig synkronisering beräknar alla objekt med de aktuella reglerna.

Det är resultatet för samma objekt med den här anpassade regeln:  
![Attributflöde 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Längden på attribut
Attribut för anslutningssträngen är som standard till att indexeras och den maximala längden är 448 tecken. Om du arbetar med string-attribut som kan innehålla fler sedan måste du ta följande i attributflöde:  
`attributeName` <- `Left([attributeName],448)`

### <a name="changing-the-userprincipalsuffix"></a>Ändra userPrincipalSuffix
Attributet userPrincipalName i Active Directory inte alltid är känt av användarna och kanske inte är lämpligt som inloggnings-ID. I Azure AD Connect sync-installationsguiden kan välja ett annat attribut, till exempel e-post. Men i vissa fall attributet måste beräknas. Företaget Contoso har till exempel två Azure AD-kataloger, en för produktion och en för testning. Användare i testklienten att använda en annan suffix i inloggnings-ID.  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

Ta allt i det här uttrycket till vänster i först @-sign (Word) och sammanfoga med en fast sträng.

### <a name="convert-a-multi-value-to-a-single-value"></a>Konvertera flera värden till ett enda värde
Vissa attribut i Active Directory är flera värden i schemat, även om de letar enda värden i Active Directory-användare och datorer. Ett exempel är Beskrivningsattributet.  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

I det här uttrycket om attributet har ett värde, tar det första objektet (objekt) i attributet, ta bort inledande och avslutande blanksteg (Trim), och sedan hålla först 448 tecken (till vänster) i strängen.

### <a name="do-not-flow-an-attribute"></a>Flöda inte ett attribut
Bakgrundsinformation om scenariot för det här avsnittet finns [kontrollera attributet flödet processen](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Det finns två sätt att flöda inte ett attribut. Först är tillgänglig i installationsguiden och låter dig [ta bort markerade attributen](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Det här alternativet fungerar om du inte har synkroniserat attribut innan. Om du har startat att synkronisera det här attributet och tar bort med den här funktionen kan lämnas sync motorn stannar hantera attributet och befintliga värden i Azure AD.

Om du vill ta bort värdet för ett attribut och kontrollera att den inte flödar i framtiden måste du skapa en anpassad regel i stället.

På Fabrikam, har vi insåg att vissa av de attribut som vi synkronisera till molnet inte får vara det. Vi vill se till att dessa attribut tas bort från Azure AD.  
![Felaktig tilläggsattribut](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

* Skapa en ny regel för inkommande synkronisering och fylla i beskrivningen ![beskrivningar](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
* Skapa attributflöden av typen **uttryck** och med källan **AuthoritativeNull**. Literalen **AuthoritativeNull** anger att värdet ska vara tomt i MV även om en lägre prioritet synkroniseringsregel försöker fylla värdet.
  ![Omvandlingen för tilläggsattribut](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
* Spara regeln synkronisering. Starta **synkroniseringstjänsten**, hitta kopplingen, väljer **kör**, och **fullständig synkronisering**. Det här steget beräknar alla attributflöden.
* Kontrollera att de avsedda ändringarna är på väg att exporteras genom att söka anslutningsplatsen.
  ![Stegvis ta bort](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Skapa regler med PowerShell
Med hjälp av sync regeln editor fungerar bra när du har bara göra några ändringar. Om du behöver göra ändringar av kan PowerShell vara ett bättre alternativ. Vissa avancerade funktioner är bara tillgängliga med PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Hämta PowerShell-skriptet för en out-of-box-regel
Se PowerShell-skript som skapats av en regel för out-of-box, väljer du regeln i Redigeraren för regler för synkronisering och klicka på **exportera**. Den här åtgärden ger PowerShell-skript som skapade regeln.

### <a name="advanced-precedence"></a>Avancerade prioritet
Regler för synkronisering av out-of-box börja med högre prioriteringsvärdet 100. Om du har många skogar och du behöver göra många anpassade ändringar sedan kanske 99 sync regler inte finns tillräckligt med.

Du kan instruera Synkroniseringsmotorn som du vill att ytterligare regler som infogas innan out-of-box-regler. Följ dessa steg för att få det här problemet:

1. Markera den första regeln i out-of-box sync (den här regeln är den **i från AD-användare ansluta**) i Redigeraren för synkronisering av regeln och välj **exportera**. Kopiera värdet SR identifierare.  
![PowerShell före ändringen](./media/active-directory-aadconnectsync-change-the-configuration/powershell1.png)  
2. Skapa ny synkroniseringsregel. Du kan använda redigeraren för regel för synkronisering för att skapa den. Exportera regeln till ett PowerShell-skript.
3. I egenskapen **PrecedenceBefore**, infoga ID-värde från regeln för out-of-box. Ange den **prioritet** till **0**. Kontrollera att attributet ID är unikt och du inte återanvänder en GUID från en annan regel. Också kontrollera att den **ImmutableTag** anges inte egenskapen; den här egenskapen får endast anges för en out-of-box-regel. Spara PowerShell-skriptet och kör den. Resultatet är att en anpassad regel är tilldelad prioritet värde på 100 och alla andra regler i out-of-box ökas.  
![PowerShell efter ändring](./media/active-directory-aadconnectsync-change-the-configuration/powershell2.png)  

Du kan ha många anpassade sync-regler som använder samma **PrecedenceBefore** värdet vid behov.


## <a name="enable-synchronization-of-preferreddatalocation"></a>Aktivera synkroniseringen av PreferredDataLocation
Azure AD Connect har stöd för synkronisering av den **PreferredDataLocation** attribut för **användaren** objekt i version 1.1.524.0 och efter. Följande ändringar har införts mer specifikt:

* Schemat för objekttypen **användaren** i Azure AD-koppling har utökats för att inkludera PreferredDataLocation attribut som är av typen string och enkelvärdesattribut.

* Schemat för objekttypen **Person** i metaversum utökas för att inkludera PreferredDataLocation attribut som är av typen string och enkelvärdesattribut.

Som standard aktiveras inte attributet PreferredDataLocation för synkronisering eftersom det inte finns något motsvarande attribut för PreferredDataLocation i lokala Active Directory. Du måste manuellt Aktivera synkronisering.

> [!IMPORTANT]
> Azure AD kan för närvarande attributet PreferredDataLocation på både synkroniserade objekt och i molnet användaren objekt för att vara direkt konfigureras med Azure AD PowerShell. När du har aktiverat synkronisering av attributet PreferredDataLocation, måste du sluta använda Azure AD PowerShell för att konfigurera attributet på **synkroniseras användarobjekt** som Azure AD Connect åsidosätter dem baserat på käll-attributvärden i lokala Active Directory.

> [!IMPORTANT]
> I September 1 2017, Azure AD inte längre att tillåta attributet PreferredDataLocation på **synkroniseras användarobjekt** konfigureras direkt med hjälp av Azure AD PowerShell. Om du vill konfigurera PreferredLocation attribut synkroniserade objekt, måste du använda Azure AD Connect endast.

Innan du aktiverar synkronisering av attributet PreferredDataLocation, måste du:

 * Bestäm vilka lokala Active Directory-attribut som ska användas som källattributet först. Det ska vara av typen **sträng** och **enstaka**.

 * Om du tidigare har konfigurerat attributet PreferredDataLocation på befintliga synkroniseras användarobjekt i Azure AD med hjälp av Azure AD PowerShell, måste du **backport** attributvärden till motsvarande användarobjekt i lokala Active Directory.
 
    > [!IMPORTANT]
    > Om du gör inte backport attributvärden till motsvarande användarobjekt i lokala Active Directory, tar Azure AD Connect bort de befintliga attributvärdena i Azure AD när synkronisering för attributet PreferredDataLocation har aktiverats.

 * Bör du konfigurera källattributet på minst några lokala AD-användare objekt nu, som kan användas för verifiering senare.
 
Stegen för att aktivera synkronisering för attributet PreferredDataLocation kan sammanfattas som:

1. Inaktivera sync scheduler och kontrollera att det finns ingen synkronisering pågår

2. Lägg till källattributet för lokalt AD-koppling schema

3. Lägga till PreferredDataLocation i Azure AD Connector-schema

4. Skapa en regel för inkommande synkronisering för att flöda attributvärdet från lokala Active Directory

5. Skapa en synkroniseringsregel för utgående för att flöda attributvärdet till Azure AD

6. Kör fullständig synkroniseringscykel

7. Aktivera sync scheduler

> [!NOTE]
> Resten av det här avsnittet beskriver de här stegen i information. De beskrivs i kontexten för en Azure AD-distribution med en skog topologi och utan anpassade Synkroniseringsregler. Om du har flera skogar topologi anpassade Synkroniseringsregler konfigurerats eller har en fristående server, måste du anpassa stegen i enlighet med detta.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Steg 1: Inaktivera sync scheduler och kontrollera att det finns ingen synkronisering pågår
Kontrollera sker ingen synkronisering när du arbetar på att uppdatera Synkroniseringsregler för att undvika oväntade ändringar exporteras till Azure AD. Inaktivera inbyggda sync scheduler:

 1. Starta PowerShell-session på Azure AD Connect-servern.

 2. Inaktivera schemalagda synkroniseringen genom att köra cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $false`
 
 3. Starta den **Synchronization Service Manager** genom att gå till START → synkroniseringstjänsten.
 
 4. Gå till den **Operations** fliken och bekräfta att ingen åtgärd vars status är *”pågår”.*

![Synchronization Service Manager - Kontrollera att inga åtgärder pågår](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step1.png)

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Steg 2: Lägg till källattributet för lokalt AD-koppling schema
Inte alla AD-attribut har importerats till lokalt AD-anslutningsplatsen. Lägga till källattributet i listan över importerade attribut:

 1. Gå till den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
 
 2. Högerklicka på den **lokala AD-koppling** och välj **egenskaper**.
 
 3. I popup-fönstret, går du till den **Välj attribut** fliken.
 
 4. Kontrollera att källattributet är markerat i attributlistan.
 
 5. Klicka på **OK** att spara.

![Lägg till källattributet i lokala AD-koppling schema](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step2.png)

### <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Steg 3: Lägga till PreferredDataLocation i Azure AD Connector-schema
Som standard är PreferredDataLocation attributet inte importerad till Azure AD Connect utrymme. Lägga till attributet PreferredDataLocation i listan över importerade attribut:

 1. Gå till den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.

 2. Högerklicka på den **Azure AD Connector** och välj **egenskaper**.

 3. I popup-fönstret, går du till den **Välj attribut** fliken.

 4. Kontrollera att attributet PreferredDataLocation är markerat i attributlistan.

 5. Klicka på **OK** att spara.

![Lägga till källattributet Azure AD Connector-schema](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step3.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Steg 4: Skapa en regel för inkommande synkronisering för att flöda attributvärdet från lokala Active Directory
Regel för inkommande synkronisering tillåter attributvärdet som ska flödas från källattributet från lokala Active Directory för metaversum:

1. Starta den **Synchronization regler Editor** genom att gå till START → Synchronization regler Editor.

2. Ange sökfilter **riktning** ska **inkommande**.

3. Klicka på **Lägg till ny regel** för att skapa en ny inkommande regel.

4. Under den **beskrivning** fliken, ange följande konfiguration:
 
    | Attribut | Värde | Information |
    | --- | --- | --- |
    | Namn | *Ange ett namn* | Till exempel *”i från AD-användaren PreferredDataLocation”* |
    | Beskrivning | *Ange en beskrivning* |  |
    | Det anslutna systemet | *Välj lokalt AD-koppling* |  |
    | Anslutna System objekttyp | **Användaren** |  |
    | Typ av Metaversumobjekt | **Person** |  |
    | Länktypen | **Anslut dig** |  |
    | Prioritet | *Välj ett tal mellan 1 – 99* | 1 – 99 är reserverat för synkronisering av anpassade regler. Välj inte ett värde som används av en annan regel för synkronisering. |

5. Gå till den **Scoping filter** fliken och Lägg till en **målgrupp filter grupp med följande sats**:
 
    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | administratörsbeskrivning | NOTSTARTWITH | Användaren\_ | 
 
    Målgrupp filter avgör vilka lokala AD-objekt den här regeln för inkommande synkronisering tillämpas på. I det här exemplet använder vi samma målgrupp filter som används som *”i från AD-användaren gemensamma”* OOB synkroniseringsregeln, vilket förhindrar att synkroniseringsregeln tillämpas på objekt som skapas via funktionen för tillbakaskrivning av Azure AD-användare. Du kan behöva justera filtret målgrupp enligt din Azure AD Connect-distribution.

6. Gå till den **omvandling fliken** och implementera följande omvandlingsregeln:
 
    | Flöde | Målattribut | Källa | Använda en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    | Direkt | PreferredDataLocation | Välj källattributet | Alternativet är avmarkerat | Uppdatering |

7. Klicka på **Lägg till** att skapa regel för inkommande trafik.

![Skapa regel för inkommande synkronisering](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step4.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Steg 5: Skapa en regel för utgående synkronisering för att flöda attributvärdet till Azure AD
Utgående synkroniseringsregel tillåter attributvärdet som ska flödas från Metaversumsökning till attributet PreferredDataLocation i Azure AD:

1. Gå till den **Synkroniseringsregler** Editor.

2. Ange sökfilter **riktning** ska **utgående**.

3. Klicka på **Lägg till ny regel** knappen.

4. Under den **beskrivning** fliken, ange följande konfiguration:

    | Attribut | Värde | Information |
    | --- | --- | --- |
    | Namn | *Ange ett namn* | Till exempel ”ut till AAD – användaren PreferredDataLocation” |
    | Beskrivning | *Ange en beskrivning* |
    | Det anslutna systemet | *Välj den AAD-kopplingen* |
    | Anslutna System objekttyp | Användare ||
    | Typ av Metaversumobjekt | **Person** ||
    | Länktypen | **Anslut dig** ||
    | Prioritet | *Välj ett tal mellan 1 – 99* | 1 – 99 är reserverat för synkronisering av anpassade regler. YDo inte hämtar ett värde som används av en annan regel för synkronisering. |

5. Gå till den **Scoping filter** fliken och Lägg till en **målgrupp filter grupp med två klausuler**:
 
    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | sourceObjectType | LIKA MED | Användare |
    | cloudMastered | NOTEQUAL | True |

    Målgrupp filter avgör vilka Azure AD-objekt den här utgående synkroniseringsregeln tillämpas på. I det här exemplet använder vi ”Out till AD-användaridentitet” samma målgrupp filtret OOB synkroniseringsregeln. Det förhindrar att synkroniseringsregeln tillämpas på objekt som inte synkroniseras från lokala Active Directory. Du kan behöva justera filtret målgrupp enligt din Azure AD Connect-distribution.
    
6. Gå till den **omvandling** fliken och implementera följande omvandlingsregeln:

    | Flöde | Målattribut | Källa | Använda en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    | Direkt | PreferredDataLocation | PreferredDataLocation | Alternativet är avmarkerat | Uppdatering |

7. Stäng **Lägg till** att skapa regel för utgående trafik.

![Skapa regel för utgående synkronisering](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step5.png)

### <a name="step-6-run-full-synchronization-cycle"></a>Steg 6: Kör fullständig synkroniseringscykel
I allmänhet fullständig synkroniseringscykel krävs eftersom vi har lagt till nya attribut i både AD och Azure AD Connector schemat och introducerades anpassade Synkroniseringsregler. Vi rekommenderar att du kontrollerar ändringarna innan du exporterar dem till Azure AD. Du kan använda följande steg för att bekräfta ändringarna när du kör de steg som utgör en fullständig synkroniseringscykel manuellt. 

1. Kör **fullständig import** steg på den **lokala AD-koppling**:

   1. Gå till den **Operations** fliken i hanteraren för synkroniseringstjänsten.

   2. Högerklicka på den **lokala AD-koppling** och välj **kör...**

   3. I popup-fönstret väljer **fullständig Import** och på **OK**.
    
   4. Vänta tills åtgärden har slutförts.

    > [!NOTE]
    > Du kan hoppa över fullständig Import på lokalt AD-koppling om källattributet ingår redan i listan över importerade attribut. Med andra ord du inte behöver göra några ändringar under [steg 2: Lägg till källattributet för lokalt AD-koppling schemat](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Kör **fullständig import** steg på den **Azure AD Connector**:

   1. Högerklicka på den **Azure AD Connector** och välj **kör...**

   2. I popup-fönstret väljer **fullständig Import** och på **OK**.
   
   3. Vänta tills åtgärden har slutförts.

3. Kontrollera synkronisering regeländringar på ett befintligt användarobjekt:

Källattribut från en lokal Active Directory och PreferredDataLocation från Azure AD har importerats till området respektive anslutningen. Innan du fortsätter med fullständig synkronisering steg, bör du göra en **Preview** på en befintlig användares objekt i lokalt AD-anslutningsplatsen. Det objekt du valt bör ha källattributet fylls i. En lyckad **Preview** med PreferredDataLocation i metaversum är en bra indikator som du har konfigurerat synkronisering regler på rätt sätt. Information om hur du gör en **Preview**, läser du avsnittet [bekräfta ändringen](#verify-the-change).

4. Kör **fullständig synkronisering** steg på den **lokala AD-koppling**:

   1. Högerklicka på den **lokala AD-koppling** och välj **kör...**
  
   2. I popup-fönstret väljer **fullständig synkronisering** och på **OK**.
   
   3. Vänta tills åtgärden har slutförts.

5. Kontrollera **väntande exporter** till Azure AD:

   1. Högerklicka på den **Azure AD Connector** och välj **söka Anslutarplats**.

   2. I dialogrutan Sök anslutningsplatsen popup:

      1. Ange **omfång** till **väntande Export**.
      
      2. Markera alla tre kryssrutorna, inklusive **lägga till, ändra och ta bort**.
      
      3. Klicka på den **Sök** för att få en lista med objekt med ändringar som ska exporteras. Dubbelklicka på objektet om du vill granska ändringarna för ett angivet objekt.
      
      4. Kontrollera ändringarna förväntas.

6. Kör **exportera** steg på den **Azure AD-koppling**
      
   1. Högerklicka på den **Azure AD Connector** och välj **kör...**
   
   2. Välj i dialogrutan Kör Connector popup- **exportera** och på **OK**.
   
   3. Vänta tills exporten till Azure AD för att slutföra.

> [!NOTE]
> Det kan hända att steg som inte innehåller den fullständiga synkroniseringen steg och Export i Azure AD Connector. Steg som krävs inte eftersom attributvärdena som flödar in från lokala Active Directory till Azure AD.

### <a name="step-7-re-enable-sync-scheduler"></a>Steg 7: Återaktivera sync scheduler
Återaktivera inbyggda sync scheduler:

1. Starta PowerShell-session.

2. Aktivera schemalagd synkronisering igen genom att köra cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $true`



## <a name="next-steps"></a>Nästa steg
* Läs mer om Konfigurationsmodell i [förstå deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Läs mer om Uttrycksspråk i [förstå uttryck för deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
