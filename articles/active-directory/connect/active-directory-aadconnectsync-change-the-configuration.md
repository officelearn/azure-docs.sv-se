---
title: "Azure AD Connect-synkronisering: gjort en konfigurationsändring i Azure AD Connect-synkronisering | Microsoft Docs"
description: "Vägleder dig igenom hur du gör en ändring i konfigurationen i Azure AD Connect-synkronisering."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2018
ms.author: billmath
ms.openlocfilehash: e97d3e3e35ee87864c5d38e75e08e62088e25fdb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Azure AD Connect-synkronisering: gör en ändring i standardkonfigurationen
Syftet med den här artikeln är leder dig igenom hur du gör ändringar i standardkonfigurationen i Azure Active Directory (AD Azure) Connect-synkronisering. Den innehåller steg för några vanliga scenarier. Med denna kunskap kan ska du kunna göra enkla ändringar i din egen konfiguration baserat på dina egna regler.

## <a name="synchronization-rules-editor"></a>Redigeraren för regler för synkronisering
Redigeraren för regler för synkronisering används för att se och ändra standardkonfigurationen. Du hittar på den **starta** menyn under den **Azure AD Connect** grupp.  
![Start-menyn med synkronisering Rule Editor](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

När du öppnar Redigeraren ser du standardregler för out-of-box.

![Synkronisera Rule Editor](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigera i Redigeraren
Med hjälp av listrutorna överst i redigeraren, kan du snabbt hitta en specifik regel. Om du vill visa reglerna där attributet proxyAddresses ingår ändra du av listrutorna för följande:  
![SRE filtrering](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
Tryck på F5 på tangentbordet för att återställa filtrering och läsa in en ny konfiguration.

I det övre högra är den **Lägg till ny regel** knappen. Du kan använda den här knappen för att skapa en egen anpassad regel.

Är knappar för att agera för en vald synkroniseringsregel längst ned. **Redigera** och **ta bort** göra vad du förväntade dig. **Exportera** genererar ett PowerShell-skript för att skapa synkroniseringsregel för på nytt. Med den här proceduren kan du flytta en synkroniseringsregel från en server till en annan.

## <a name="create-your-first-custom-rule"></a>Skapa din första anpassad regel
De vanligaste förändringar är att attributflöden. Data i din katalog kan inte vara desamma som för Azure AD. I exemplet i det här avsnittet, se till att det angivna namnet för en användare är alltid i *rätt skiftläge*.

### <a name="disable-the-scheduler"></a>Inaktivera Schemaläggaren
Den [scheduler](active-directory-aadconnectsync-feature-scheduler.md) körs var 30: e minut som standard. Kontrollera att den inte startar när du gör ändringar och felsöka din nya regler. För att tillfälligt inaktivera Schemaläggaren, starta PowerShell och kör `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Inaktivera Schemaläggaren](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Skapa regel
1. Klicka på **Lägg till ny regel**.
2. På den **beskrivning** anger du följande:  
   ![Inkommande regel filtrering](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
   * **Namnet**: ge regeln ett beskrivande namn.
   * **Beskrivning**: ge vissa information så att någon annan kan förstå vad regeln för.
   * **Anslutna System**: Detta är ett system där du kan hitta objektet. I det här fallet markerar **Active Directory-kopplingen**.
   * **Anslutna System/metaversum-objekttyp**: Välj **användare** och **Person**respektive.
   * **Länka typen**: ändra värdet till **ansluta**.
   * **Prioritet**: Ange ett värde som är unikt i systemet. Ett lägre numeriskt värde anger högre prioritet.
   * **Taggen**: lämna detta tomt. Endast out-of-box regler från Microsoft ska ha den här rutan fylls i med ett värde.
3. På den **Scoping filter** anger **givenName ISNOTNULL**.  
   ![Regel för inkommande trafik Omfångsfilter](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
   Det här avsnittet används för att definiera vilka objekt som regeln gäller. Om den lämnas tom, skulle regeln gälla för alla användarobjekt. Men som även konferensrum, tjänstkonton och andra icke personer användarobjekt.
4. På den **ansluta regler** lämnar fältet tomt.
5. På den **transformationer** ändrar **ExchangeRate för FlowType** till **uttryck**. För **målattribut**väljer **givenName**. Och för **källa**, ange **PCase([givenName])**.
   ![Regel för inkommande trafik omvandlingar](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
   Synkroniseringsmotorn är skiftlägeskänslig för både funktionsnamnet och namnet på attributet. Om du skriver något fel, se en varning när du lägger till regeln. Du kan spara och fortsätta, men du måste öppna och rätta till regeln.
6. Klicka på **Lägg till** spara regeln.

Den nya anpassa regeln ska visas med andra regler för synkronisering i systemet.

### <a name="verify-the-change"></a>Bekräfta ändringen
Med den här nya ändringar som du vill kontrollera att den fungerar som väntat och inte som utlöste eventuella fel. Beroende på antalet objekt som du har, finns det två sätt att göra det här steget:

- Kör en fullständig synkronisering för alla objekt.
- Kör en förhandsgranskning och en fullständig synkronisering på ett enskilt objekt.

Öppna den **synkroniseringstjänsten** från den **starta** menyn. Stegen i det här avsnittet finns i det här verktyget.

**Fullständig synkronisering för alla objekt**  

   1. Välj **kopplingar** längst upp. Identifiera den koppling som du har ändrat i föregående avsnitt (i det här fallet Active Directory Domain Services) och markera den. 
   2. För **åtgärder**väljer **kör**.
   3. Välj **fullständig synkronisering**, och välj sedan **OK**.
   ![Fullständig synkronisering](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
   Objekt har uppdaterats i metaversum. Kontrollera dina ändringar genom att titta på objektet i metaversum.

**Förhandsgranskning och fullständig synkronisering på ett objekt**  

   1. Välj **kopplingar** längst upp. Identifiera den koppling som du har ändrat i föregående avsnitt (i det här fallet Active Directory Domain Services) och markera den.
   2. Välj **söka Anslutarplats**. 
   3. Använd **omfång** att hitta ett objekt som du vill använda för att testa ändringen. Markera objektet och klicka på **Preview**. 
   4. I det nya fönstret, Välj **genomför Preview**.  
   ![Genomför preview](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
   Ändringen genomförs nu till metaversum.

**Visa objekt i metaversum**  

1. Välj ett par exempel objekt och kontrollera att värdet är förväntat och att regeln tillämpas. 
2. Välj **metaversumsökningen** från början. Lägg till ett filter som du vill söka efter relevanta objekt. 
3. Öppna ett objekt i sökresultatet. Titta på attributvärdena och Kontrollera också i den **Sync regler** kolumn som regeln tillämpas som förväntat.  
![Metaversumsökning](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Aktivera Schemaläggaren
Om allt är som förväntat, kan du aktivera Schemaläggaren igen. Kör från PowerShell `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Andra vanliga flödet attributändringar
I föregående avsnitt beskrivs hur du gör ändringar i ett attributflöde. I det här avsnittet finns ytterligare exempel. Anvisningar att skapa regeln sync förkortas, men du kan hitta fullständig stegen i föregående avsnitt.

### <a name="use-an-attribute-other-than-the-default"></a>Använd ett attribut än standardvärdet
I det här scenariot Fabrikam har en skog där lokala alfabetet används för förnamn, efternamn och visningsnamn. Latinska teckenrepresentation av dessa attribut finns i tilläggsattribut. För att skapa en global adress listas i Azure AD och Office 365, organisationen vill använda dessa attribut i stället.

Med en standardkonfiguration ett objekt från den lokala skogen som ser ut så här:  
![Attributflöde 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Om du vill skapa en regel med andra attributflöden, gör du följande:

1. Öppna den **Synchronization regler Editor** från den **starta** menyn.
2. Med **inkommande** fortfarande markerat till vänster, klicka på den **Lägg till ny regel** knappen.
3. Ge regeln ett namn och beskrivning. Välj den lokala Active Directory-instansen och de relevanta objekttyperna. I **länktypen**väljer **ansluta**. För **prioritet**, Välj ett värde som inte används av en annan regel. Out-of-box-regler som börjar med 100, så värdet 50 kan användas i det här exemplet.
  ![Attributflöde 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
4. Lämna **Scoping filter** tom. (Det vill säga den ska gälla för alla användarobjekt i skogen.)
5. Lämna **ansluta regler** tom. (Det vill säga att out-of-box regeln hanterar alla kopplingar.)
6. I **transformationer**, skapa följande flöden:  
  ![Attributflöde 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
7. Klicka på **Lägg till** spara regeln.
8. Gå till **Synchronization Service Manager**. På **kopplingar**, Välj den koppling som du har lagt till regeln. Välj **kör**, och välj sedan **fullständig synkronisering**. En fullständig synkronisering beräknar alla objekt med hjälp av de aktuella reglerna.

Det är resultatet för samma objekt med den här anpassade regeln:  
![Attributflöde 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Längden på attribut
Attribut för anslutningssträngen är indexeras som standard och den maximala längden är 448 tecken. Om du arbetar med string-attribut som kan innehålla fler se till att inkludera följande i attributflödet:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Ändra userPrincipalSuffix
Attributet userPrincipalName i Active Directory inte alltid är känt av användarna och kanske inte är lämpligt som inloggnings-ID. Med installationsguiden för Azure AD Connect-synkronisering kan du välja ett annat attribut, till exempel *e*. Men i vissa fall attributet måste beräknas.

Företaget Contoso har till exempel två Azure AD-kataloger, en för produktion och en för testning. De vill att användarna i klientorganisationen test för att använda en annan suffix i inloggnings-ID:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

Ta allt i det här uttrycket till vänster i först @-sign (Word) och sammanfoga med en fast sträng.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Konvertera ett attribut med flera värden i värde
Vissa attribut i Active Directory är flera värden i schemat, även om de letar enkelvärdesattribut i Active Directory-användare och datorer. Ett exempel är Beskrivningsattributet:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

I det här uttrycket om attributet har ett värde, tar det första objektet (*objektet*) i attributet, ta bort inledande och avslutande blanksteg (*Trim*), och sedan hålla först 448 tecken (*vänster* ) i strängen.

### <a name="do-not-flow-an-attribute"></a>Flöda inte ett attribut
Bakgrundsinformation om scenariot för det här avsnittet finns [kontrollera attributet flödet processen](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Det finns två sätt att flöda inte ett attribut. Först är med hjälp av installationsguiden för att [ta bort markerade attributen](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Det här alternativet fungerar om du inte har synkroniserat attribut innan. Om du har startat att synkronisera det här attributet och tar bort med den här funktionen kan dock lämnas sync motorn stannar hantera attributet och de befintliga värdena i Azure AD.

Om du vill ta bort värdet för ett attribut och kontrollera att den inte flödar i framtiden måste du skapa en anpassad regel.

I detta scenario Fabrikam har vi insåg att vissa av de attribut som vi synkronisera till molnet inte får vara det. Vi vill se till att dessa attribut tas bort från Azure AD.  
![Felaktig tilläggsattribut](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

1. Skapa en ny regel för inkommande synkronisering och fylla i beskrivningen.
  ![Beskrivningar](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
2. Skapa attributflöden med **uttryck** för **ExchangeRate för FlowType** och **AuthoritativeNull** för **källa**. Literalen **AuthoritativeNull** anger att värdet ska vara tom i metaversum, även om en synkroniseringsregel med lägre prioritet görs ett försök att fylla värdet.
  ![Omvandlingen för tilläggsattribut](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
3. Spara regeln synkronisering. Starta den **synkroniseringstjänsten**, hitta kopplingen, väljer **kör**, och välj sedan **fullständig synkronisering**. Det här steget beräknar alla attributflöden.
4. Kontrollera att de avsedda ändringarna är på väg att exporteras genom att söka anslutningsplatsen.
  ![Stegvis ta bort](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Skapa regler med PowerShell
Med hjälp av sync regeln editor fungerar bra när du har bara göra några ändringar. Om du behöver göra ändringar av kan PowerShell vara ett bättre alternativ. Vissa avancerade funktioner är bara tillgängliga med PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Hämta PowerShell-skriptet för en out-of-box-regel
Se PowerShell-skript som skapats av en regel för out-of-box, väljer du regeln i Redigeraren för regler för synkronisering och klicka på **exportera**. Den här åtgärden ger PowerShell-skript som skapade regeln.

### <a name="advanced-precedence"></a>Avancerade prioritet
Regler för synkronisering av out-of-box börja med högre prioriteringsvärdet 100. Om du har många skogar och du behöver göra många anpassade ändringar sedan kanske 99 sync regler inte finns tillräckligt med.

Du kan instruera Synkroniseringsmotorn som du vill använda ytterligare regler som infogas innan out-of-box-regler. Följ dessa steg för att få det här problemet:

1. Markera den första regeln i out-of-box-synkronisering (**i från AD-användare ansluta**) i Redigeraren för regler för synkronisering och välj **exportera**. Kopiera värdet SR identifierare.  
![PowerShell före ändringen](./media/active-directory-aadconnectsync-change-the-configuration/powershell1.png)  
2. Skapa ny synkroniseringsregel. Du kan använda redigeraren för regler för synkronisering för att skapa den. Exportera regeln till ett PowerShell-skript.
3. I egenskapen **PrecedenceBefore**, infoga ID-värde från regeln för out-of-box. Ange den **prioritet** till **0**. Kontrollera att attributet ID är unikt och att du inte återanvänder en GUID från en annan regel. Också kontrollera att den **ImmutableTag** egenskapen har inte angetts. Den här egenskapen ska bara anges för en regel för out-of-box.
4. Spara PowerShell-skriptet och kör den. Resultatet är att en anpassad regel är tilldelad prioritet värde på 100 och alla andra regler i out-of-box ökas.  
![PowerShell efter ändring](./media/active-directory-aadconnectsync-change-the-configuration/powershell2.png)  

Du kan ha många anpassade sync-regler med hjälp av samma **PrecedenceBefore** värdet vid behov.

## <a name="enable-synchronization-of-usertype"></a>Aktivera synkroniseringen av UserType
Azure AD Connect har stöd för synkronisering av den **UserType** attribut för **användaren** objekt i version 1.1.524.0 och senare. Följande ändringar har införts mer specifikt:

- Schemat för objekttypen **användaren** i Azure AD-koppling har utökats för att inkludera UserType-attributet som är av typen string och har ett enstaka.
- Schemat för objekttypen **Person** i metaversum utökas för att inkludera UserType-attributet som är av typen string och har ett enstaka.

Som standard aktiveras inte UserType-attributet för synkronisering eftersom det inte finns något motsvarande UserType-attribut i lokala Active Directory. Du måste manuellt Aktivera synkronisering. Innan du gör detta måste du ta del av följande som tillämpas av Azure AD:

- Azure AD accepterar bara två värden för UserType-attributet: **medlem** och **gäst**.
- Om UserType-attributet inte är aktiverat för Azure AD Connect-synkronisering, Azure AD-användare som skapats via katalogsynkronisering skulle ha UserType-attributet inställt på **medlem**.
- Azure AD tillåter inte UserType-attributet på befintliga Azure AD-användare som ska ändras av Azure AD Connect. Det kan endast anges under genereringen av Azure AD-användare.

Innan du aktiverar synkronisering av UserType-attributet måste du först bestämma hur attributet härleds från lokala Active Directory. Följande är de vanligaste metoderna:

- Ange ett oanvänt lokala AD-attributet (till exempel extensionAttribute1) som ska användas som källattribut. Den avsedda lokala AD-attributet ska vara av typen **sträng**finnas enkelvärdesattribut och innehålla värdet **medlem** eller **gäst**. 

    Om du väljer den här metoden måste du se till att attributet avsedda fylls med rätt värde för alla befintliga användarobjekt i lokala Active Directory som synkroniseras till Azure AD innan du aktiverar synkronisering av UserType-attributet .

- Du kan också härleda värdet för UserType-attributet från andra egenskaper. Till exempel du vill synkronisera alla användare som **gäst** om sina lokala AD userPrincipalName attributet slutar med domändelen  *@partners.fabrikam123.org* . 

    Som tidigare nämnts tillåter inte UserType-attributet på befintliga Azure AD-användare som ska ändras av Azure AD Connect i Azure AD Connect. Därför måste du säkerställa att den logik som du har valt är konsekvent med hur UserType-attributet har redan konfigurerats för alla befintliga Azure AD-användare i din klient.

Steg för att aktivera synkronisering av UserType-attributet kan sammanfattas som:

1.  Inaktivera sync scheduler och kontrollera att det finns ingen synkronisering pågår.
2.  Lägg till källattributet för lokalt AD-koppling schemat.
3.  Lägga till UserType i Azure AD Connector-schema.
4.  Skapa en regel för inkommande synkronisering för att flöda attributvärdet från lokala Active Directory.
5.  Skapa en regel för utgående synkronisering för att flöda attributvärdet till Azure AD.
6.  Kör en fullständig synkroniseringscykel.
7.  Aktivera sync scheduler.

>[!NOTE]
> Resten av det här avsnittet beskriver de här stegen. De beskrivs i kontexten för en Azure AD-distribution med en skog topologi och utan anpassade Synkroniseringsregler. Om du har flera skogar topologi anpassade Synkroniseringsregler konfigurerats eller har en fristående server, måste du anpassa stegen i enlighet med detta.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Steg 1: Inaktivera sync scheduler och kontrollera att det finns ingen synkronisering pågår
Se till att ingen synkronisering sker när du arbetar i mitten uppdaterar Synkroniseringsregler för att undvika oväntade ändringar exporterar till Azure AD. Inaktivera inbyggda sync scheduler:

 1. Starta en PowerShell-session på Azure AD Connect-servern.
 2. Inaktivera schemalagda synkroniseringen genom att köra cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 3. Öppna Hanteraren för synkroniseringstjänsten genom att gå till **starta** > **synkroniseringstjänsten**.
 4. Gå till den **Operations** fliken och bekräfta att ingen åtgärd med statusen *pågår*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Steg 2: Lägg till källattributet för lokalt AD-koppling schema
Inte alla Azure AD-attribut har importerats till lokalt AD-anslutningsplatsen. Lägga till källattributet i listan över importerade attribut:

 1. Gå till den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
 2. Högerklicka på lokalt AD-koppling och välj **egenskaper**.
 3. I dialogrutan popup går du till den **Välj attribut** fliken.
 4. Kontrollera att källattributet är markerat i attributlistan.
 5. Klicka på **OK** att spara.
![Lägg till källattributet i lokala AD-koppling schema](./media/active-directory-aadconnectsync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>Steg 3: Lägga till UserType i Azure AD Connector-schema
Som standard är UserType-attributet inte importerad till Azure AD Connect utrymme. Lägga till UserType-attributet i listan över importerade attribut:

 1. Gå till den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
 2. Högerklicka på den **Azure AD Connector** och välj **egenskaper**.
 3. I dialogrutan popup går du till den **Välj attribut** fliken.
 4. Kontrollera att attributet PreferredDataLocation är markerat i attributlistan.
 5. Klicka på **OK** att spara.

![Lägga till källattributet Azure AD Connector-schema](./media/active-directory-aadconnectsync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Steg 4: Skapa en regel för inkommande synkronisering för att flöda attributvärdet från lokala Active Directory
Regel för inkommande synkronisering tillåter attributvärdet som ska flödas från källattributet från lokala Active Directory för metaversum:

1. Öppna Redigeraren för regler för synkronisering genom att gå till **starta** > **Synchronization regler Editor**.
2. Ange sökfilter **riktning** ska **inkommande**.
3. Klicka på den **Lägg till ny regel** för att skapa en ny inkommande regel.
4. Under den **beskrivning** fliken, ange följande konfiguration:

    | Attribut | Värde | Information |
    | --- | --- | --- |
    | Namn | *Ange ett namn* | Till exempel *i från AD-användaren UserType* |
    | Beskrivning | *Ange en beskrivning* |  |
    | Det anslutna systemet | *Välj lokalt AD-koppling* |  |
    | Anslutna System objekttyp | **Användaren** |  |
    | Typ av Metaversumobjekt | **Person** |  |
    | Länktypen | **Anslut dig** |  |
    | Prioritet | *Välj ett tal mellan 1 – 99* | 1 – 99 är reserverat för synkronisering av anpassade regler. Välj inte ett värde som används av en annan regel för synkronisering. |

5. Gå till den **Scoping filter** fliken och Lägg till en **målgrupp filter grupp** med följande sats:

    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | administratörsbeskrivning | NOTSTARTWITH | Användaren\_ |

    Målgrupp filtret anger till vilken lokala AD-objekt inkommande synkroniseringsregeln gäller. I det här exemplet använder vi samma målgrupp filter som används i den *i från AD-användaren vanliga* out-of-box synkroniseringsregeln, vilket förhindrar att synkroniseringsregeln tillämpas på objekt som skapas via Azure AD-användare tillbakaskrivning av funktionen. Du kan behöva justera filtret målgrupp enligt din Azure AD Connect-distribution.

6. Gå till den **omvandling** fliken och implementera önskade omvandlingsregeln. Om du har angett en oanvända exempelvis lokala AD-attributet (till exempel extensionAttribute1) du kan implementera en direkt attributflöde som källattributet för UserType:

    | Flöde | Målattribut | Källa | Använda en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    | Direkt | UserType | extensionAttribute1 | Alternativet är avmarkerat | Uppdatering |

    Ett annat exempel är som du vill erhålla värde för UserType-attributet från andra egenskaper. Till exempel du vill synkronisera alla användare som gäst om sina lokala AD userPrincipalName attributet slutar med domändelen  *@partners.fabrikam123.org* . Du kan implementera ett uttryck så här:

    | Flöde | Målattribut | Källa | Använda en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    | Direkt | UserType | IIf(IsPresent([userPrincipalName]),IIf(CBool(Instr(LCase([userPrincipalName]) ”@partners.fabrikam123.org”)=0) ”medlem”, ”Gäst”), fel (”UserPrincipalName finns inte att fastställa UserType”)) | Alternativet är avmarkerat | Uppdatering |

7. Klicka på **Lägg till** att skapa regel för inkommande trafik.

![Skapa regel för inkommande synkronisering](./media/active-directory-aadconnectsync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Steg 5: Skapa en regel för utgående synkronisering för att flöda attributvärdet till Azure AD
Utgående synkroniseringsregel tillåter attributvärdet som ska flödas från metaversumsökning till attributet PreferredDataLocation i Azure AD:

1. Gå till Redigeraren för regler för synkronisering.
2. Ange sökfilter **riktning** ska **utgående**.
3. Klicka på den **Lägg till ny regel** knappen.
4. Under den **beskrivning** fliken, ange följande konfiguration:

    | Attribut | Värde | Information |
    | ----- | ------ | --- |
    | Namn | *Ange ett namn* | Till exempel *på AAD-användaren UserType* |
    | Beskrivning | *Ange en beskrivning* ||
    | Det anslutna systemet | *Välj den AAD-kopplingen* ||
    | Anslutna System objekttyp | **Användaren** ||
    | Typ av Metaversumobjekt | **Person** ||
    | Länktypen | **Anslut dig** ||
    | Prioritet | *Välj ett tal mellan 1 – 99* | 1 – 99 är reserverat för synkronisering av anpassade regler. Välj inte ett värde som används av en annan regel för synkronisering. |

5. Gå till den **Scoping filter** fliken och Lägg till en **målgrupp filter grupp** med två klausuler:

    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | sourceObjectType | LIKA MED | Användare |
    | cloudMastered | NOTEQUAL | True |

    Målgrupp filtret anger som Azure AD-objekt utgående synkroniseringsregeln gäller. I det här exemplet använder vi samma målgrupp filter från den *ut till AD-användaridentitet* synkroniseringsregel för out-of-box. Det förhindrar att synkroniseringsregeln tillämpas på objekt som inte är synkroniserade från lokala Active Directory. Du kan behöva justera filtret målgrupp enligt din Azure AD Connect-distribution.

6. Gå till den **omvandling** fliken och implementera följande omvandlingsregeln:

    | Flöde | Målattribut | Källa | Använda en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    | Direkt | UserType | UserType | Alternativet är avmarkerat | Uppdatering |

7. Klicka på **Lägg till** att skapa regel för utgående trafik.

![Skapa regel för utgående synkronisering](./media/active-directory-aadconnectsync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Steg 6: Kör en fullständig synkroniseringscykel
I allmänhet krävs en fullständig synkroniseringscykel eftersom vi har lagt till nya attribut till både Active Directory och Azure AD Connector scheman och introduceras anpassade Synkroniseringsregler. Du vill bekräfta ändringarna innan du exporterar dem till Azure AD. 

Du kan använda följande steg för att bekräfta ändringarna när du kör de steg som utgör en fullständig synkroniseringscykel manuellt.

1. Kör en **fullständig import** på den **lokala AD-koppling**:

   1. Gå till den **Operations** fliken i hanteraren för synkroniseringstjänsten.
   2. Högerklicka på den **lokala AD-koppling** och välj **kör**.
   3. Välj i dialogrutan popup- **fullständig Import** och klicka sedan på **OK**.
   4. Vänta tills åtgärden är klar.

    > [!NOTE]
    > Du kan hoppa över en fullständig import på lokalt AD-koppling om källattributet ingår redan i listan över importerade attribut. Med andra ord du hade inte göra några ändringar under [steg 2: Lägg till källattributet för lokalt AD-koppling schemat](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Kör en **fullständig import** på den **Azure AD Connector**:

   1. Högerklicka på den **Azure AD Connector** och välj **kör**.
   2. Välj i dialogrutan popup- **fullständig Import** och klicka sedan på **OK**.
   3. Vänta tills åtgärden är klar.

3. Kontrollera synkronisering regeländringar på ett befintligt användarobjekt:

    Källattribut från en lokal Active Directory och UserType från Azure AD har importerats till sina respektive kopplingens utrymmen. Innan du fortsätter med en fullständig synkronisering, gör en **Preview** på en befintlig användares objekt i lokalt AD-anslutningsplatsen. Det objekt som du har valt ska ha källattributet fylls i.
    
    En lyckad **Preview** med UserType i metaversum är en bra indikator som du har konfigurerat synkronisering regler på rätt sätt. Information om hur du gör en **Preview**, finns i avsnittet [bekräfta ändringen](#verify-the-change).

4. Kör en **fullständig synkronisering** på den **lokala AD-koppling**:

   1. Högerklicka på den **lokala AD-koppling** och välj **kör**.
   2. Välj i dialogrutan popup- **fullständig synkronisering** och klicka sedan på **OK**.
   3. Vänta tills åtgärden är klar.

5. Kontrollera **väntande exporter** till Azure AD:

   1. Högerklicka på den **Azure AD Connector** och välj **söka Anslutarplats**.
   2. I den **söka Anslutarplats** dialogruta:

      - Ange **omfång** till **väntande Export**.
      - Markera alla tre kryssrutor: **Lägg till**, **ändra**, och **ta bort**.
      - Klicka på den **Sök** för att få en lista med objekt med ändringar som ska exporteras. Dubbelklicka på objektet om du vill granska ändringarna för ett angivet objekt.
      - Kontrollera att ändringarna är förväntat.

6. Kör **exportera** på den **Azure AD Connector**:

   1. Högerklicka på den **Azure AD Connector** och välj **kör**.
   2. I den **kör Connector** popup-dialogrutan **exportera** och klicka sedan på **OK**.
   3. Vänta tills exporten till Azure AD för att avsluta.

> [!NOTE]
> De här stegen inte innehålla den fullständiga synkroniseringen och exportera steg i Azure AD Connector. Dessa steg behövs inte eftersom attributvärdena som flödar in från lokala Active Directory till Azure AD.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Steg 7: Återaktivera sync scheduler
Återaktivera inbyggda sync scheduler:

1. Starta en PowerShell-session.
2. Aktivera schemalagd synkronisering på nytt genom att köra cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $true`.


## <a name="next-steps"></a>Nästa steg
* Läs mer om Konfigurationsmodell i [förstå deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Läs mer om Uttrycksspråk i [förstå uttryck för deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
