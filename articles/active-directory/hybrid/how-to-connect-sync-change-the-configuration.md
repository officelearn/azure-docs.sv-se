---
title: 'Azure AD Connect-synkronisering: Gjort en konfigurationsändring i Azure AD Connect-synkronisering | Microsoft Docs'
description: Går igenom hur du gör en ändring i konfigurationen i Azure AD Connect-synkronisering.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: ebc32f59539e4e4e94f7f68d5d164cf2b04cc4c2
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488534"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Azure AD Connect-synkronisering: Gör en ändring i standardkonfigurationen
Syftet med den här artikeln är att hjälper dig att göra ändringar i standardkonfigurationen i Azure Active Directory (Azure AD) Connect-synkronisering. Den innehåller steg för några vanliga scenarier. Med denna kunskap kan ska du kunna göra enkla ändringar i din egen konfiguration baserat på dina egna affärsregler.

> [!WARNING]
> Om du gör ändringar i standardregler för synkronisering sedan dessa ändringar kommer att skrivas över vid nästa Azure AD Connect har uppdaterats, vilket resulterar i oväntade och sannolikt oönskad Synkroniseringsresultat.
>
> Out-of-box Synkroniseringsregler har ett tumavtryck. Om du gör en ändring i de här reglerna matchar tumavtrycket inte längre. Du kan få problem i framtiden när du försöker installera en ny version av Azure AD Connect. Endast göra ändringar på sätt som det beskrivs i den här artikeln.

## <a name="synchronization-rules-editor"></a>Synchronization Rules Editor
Synchronization Rules Editor som används för att se och ändra standardkonfigurationen. Du hittar den på den **starta** menyn under den **Azure AD Connect** grupp.  
![Start-menyn med Sync Rule Editor](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

När du öppnar Redigeraren visas standardregler för out-of-box.

![Synkronisera Rule Editor](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigera i Redigeraren
Med listrutorna överst i redigeraren kan hitta du snabbt en specifik regel. Om du vill visa reglerna där attributet proxyAddresses inkluderas kan du till exempel ändra listrutorna så här:  
![SRE filtrering](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Tryck på F5 på tangentbordet för att återställa filtrering och läsa in en ny konfiguration.

I det övre högra hörnet är den **Lägg till ny regel** knappen. Du kan använda den här knappen för att skapa en egen anpassad regel.

Längst ned på sidan finns knappar för agerar på en synkroniseringsregel för valda. **Redigera** och **ta bort** göra vad du förväntade dig. **Exportera** producerar ett PowerShell-skript för att återskapa synkroniseringsregel. Med den här proceduren kan du flytta en synkroniseringsregel från en server till en annan.

## <a name="create-your-first-custom-rule"></a>Skapa din första anpassad regel
De vanligaste förändringar är att attributflöden. Data i källkatalogen kanske inte är samma som i Azure AD. I exemplet i det här avsnittet, se till att det angivna namnet för en användare är alltid i *skiftlägeskonvertera*.

### <a name="disable-the-scheduler"></a>Inaktivera scheduler
Den [scheduler](how-to-connect-sync-feature-scheduler.md) körs var 30: e minut som standard. Kontrollera att den inte startar när du gör ändringar och felsöka din nya regler. För att tillfälligt inaktivera scheduler, starta PowerShell och kör `Set-ADSyncScheduler -SyncCycleEnabled $false`.

![Inaktivera scheduler](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Skapa regeln
1. Klicka på **Lägg till ny regel**.
2. På den **beskrivning** anger du följande:  
   ![Inkommande regel filtrering](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Namn**: Ge regeln ett beskrivande namn.
   * **Beskrivning**: Ge klargöranden så att någon annan kan förstå vad regeln för.
   * **Anslutna System**: Det här är systemet där objektet finns. I det här fallet väljer **Active Directory-koppling**.
   * **Anslutna System/metaversum-objekttyp**: Välj **användaren** och **Person**respektive.
   * **Länktyp**: Ändra det här värdet till **ansluta**.
   * **Prioritet**: Ange ett värde som är unikt i systemet. Lägre numeriska värdet anger högre prioritet.
   * **Taggen**: Lämna det tomt. Endast out-of-box regler från Microsoft ska ha den här rutan som fyllts med ett värde.
3. På den **Scoping filter** anger **givenName ISNOTNULL**.  
   ![Regel för inkommande trafik Omfångsfilter](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Det här avsnittet används för att definiera vilka objekt som regeln gäller. Om det lämnas tomt används skulle regeln gälla för alla användarobjekt. Dock innehålla som konferensrum tjänstkonton och andra icke-personer användarobjekt.
4. På den **ansluta regler** lämnar fältet tomt.
5. På den **transformationer** , ändra **ExchangeRate för FlowType** till **uttryck**. För **målattribut**väljer **givenName**. Och för **källa**, ange **PCase([givenName])**.
   ![Regel för inkommande trafik omvandlingar](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   Synkroniseringsmotorn är skiftlägeskänsliga för både funktionsnamnet och namnet på attributet. Om du skriver något fel, visas en varning när du lägger till regeln. Du kan spara och fortsätta, men du måste öppna och rätta till regeln.
6. Klicka på **Lägg till** spara regeln.

Den nya anpassa regeln ska visas med de andra reglerna för synkronisering i systemet.

### <a name="verify-the-change"></a>Bekräfta ändringen
Med den här nya ändringar som du vill kontrollera att den fungerar som förväntat och inte kastar eventuella fel. Beroende på antalet objekt som du har, finns det två sätt att göra det här steget:

- Kör en fullständig synkronisering för alla objekt.
- Kör en förhandsversion och en fullständig synkronisering på ett enda objekt.

Öppna den **synkroniseringstjänsten** från den **starta** menyn. Stegen i det här avsnittet finns i det här verktyget.

**Fullständig synkronisering för alla objekt**  

   1. Välj **kopplingar** högst upp. Identifiera den koppling som du har ändrat i föregående avsnitt (i det här fallet Active Directory Domain Services) och markera den. 
   2. För **åtgärder**väljer **kör**.
   3. Välj **fullständig synkronisering**, och välj sedan **OK**.
   ![Fullständig synkronisering](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   Objekten uppdateras nu i metaversum. Verifiera dina ändringar genom att titta på objektet i metaversum.

**Förhandsgranskning och en fullständig synkronisering på ett enda objekt**  

   1. Välj **kopplingar** högst upp. Identifiera den koppling som du har ändrat i föregående avsnitt (i det här fallet Active Directory Domain Services) och markera den.
   2. Välj **söka Anslutarplats**. 
   3. Använd **omfång** att hitta ett objekt som du vill använda för att testa ändringen. Markera objektet och klicka på **förhandsversion**. 
   4. I det nya fönstret, Välj **genomför förhandsversion**.  
   ![Genomför förhandsversion](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   Ändringen genomförs nu till metaversum.

**Visa objektet i metaversum**  

1. Välj ett par exempel objekt att se till att värdet är förväntat och att regeln tillämpas. 
2. Välj **Metaversumsökning** högst upp. Lägg till eventuella filter som du behöver för att hitta motsvarande objekt. 
3. Öppna ett objekt från sökresultatet. Titta på attributvärdena och Kontrollera också i den **Synkroniseringsregler** kolumn som regeln tillämpas som förväntat.  
![Metaversumsökning](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Aktivera scheduler
Om allt är som förväntat, kan du aktivera scheduler igen. Från PowerShell, kör `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Andra vanliga attributändringar för flow
Föregående avsnitt beskrivs hur du gör ändringar i ett attributflöde. I det här avsnittet finns några ytterligare exempel. Anvisningar att skapa synkroniseringsregel för förkortas, men du kan hitta detaljerade anvisningar i föregående avsnitt.

### <a name="use-an-attribute-other-than-the-default"></a>Använd ett attribut än standardvärdet
I det här Fabrikam-scenariot finns det en skog där lokala alfabetet används för förnamn, efternamn och visningsnamn. Latinska teckenrepresentation av dessa attribut finns i tilläggsattribut. För att skapa en global adress lista i Azure AD och Office 365, organisationen vill använda dessa attribut i stället.

Med en standardkonfiguration ett objekt från den lokala skogen som ser ut så här:  
![Attributflöde 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Skapa en regel med andra attributflöden genom att göra följande:

1. Öppna den **Synchronization Rules Editor** från den **starta** menyn.
2. Med **inkommande** fortfarande markerat till vänster, klickar du på den **Lägg till ny regel** knappen.
3. Ge regeln ett namn och beskrivning. Välj en lokal Active Directory-instans och de relevanta objekttyperna. I **länktyp**väljer **ansluta**. För **prioritet**, Välj ett tal som inte används av en annan regel. Out-of-box-regler som börjar med 100, så värdet 50 kan användas i det här exemplet.
  ![Attributflöde 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Lämna **Scoping filter** tom. (Det vill säga den ska tillämpas på alla användarobjekt i skogen.)
5. Lämna **ansluta regler** tom. (Det vill säga kan hantera alla kopplingar out-of-box regeln.)
6. I **transformationer**, skapa följande flöden:  
  ![Attributflöde 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Klicka på **Lägg till** spara regeln.
8. Gå till **hanteraren för synkroniseringstjänsten**. På **kopplingar**, Välj den koppling som där du lade till regeln. Välj **kör**, och välj sedan **fullständig synkronisering**. En fullständig synkronisering beräknar om alla objekt med hjälp av de aktuella reglerna.

Det här är resultatet för samma objekt med den här anpassade regeln:  
![Attributflöde 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Längden på attribut
Strängattribut finns indexeras som standard den maximala längden är 448 tecken. Om du arbetar med strängattribut som kan innehålla fler, se till att inkludera följande i attributflödet:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Ändra userPrincipalSuffix
UserPrincipalName-attribut i Active Directory inte alltid är känt av användarna och kanske inte lämpligt som inloggnings-ID. Med installationsguiden för Azure AD Connect-synkronisering kan du välja ett annat attribut, till exempel *e*. Men i vissa fall kan attributet måste beräknas.

Företaget Contoso har till exempel två Azure AD-kataloger, en för produktion och en för testning. De vill att användare i deras test-klient du använder en annan suffix i inloggnings-ID:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

I det här uttrycket ta allt till vänster på först @-sign (Word) och sammanfoga med en fast sträng.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Konvertera ett attribut med flera värden till enskilt värde
Vissa attribut i Active Directory är flera värden i schemat, även om de letar enkelvärdesattribut i Active Directory-användare och datorer. Ett exempel är Beskrivningsattributet:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

I det här uttrycket om attributet har ett värde, ta det första objektet (*objekt*) i attributet, ta bort inledande och avslutande blanksteg (*trimma*), och sedan fortsätta först 448 tecken (*vänster* ) i strängen.

### <a name="do-not-flow-an-attribute"></a>Inte flöda ett attribut
Bakgrundsinformation på scenariot för det här avsnittet finns [kontrollera attributet flow processen](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Det finns två sätt att flöda inte ett attribut. Först är genom att använda installationsguiden för att [ta bort markerade attribut](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering). Det här alternativet fungerar om du aldrig har synkroniserat attribut innan. Om du har startat att synkronisera det här attributet och senare tar bort den med den här funktionen, men lämnas synkronisering motorn stannar hantera attributet och de befintliga värdena i Azure AD.

Om du vill ta bort värdet för ett attribut och kontrollera att det inte flödar i framtiden, måste du skapa en anpassad regel.

I det här Fabrikam-scenariot har vi insåg att några av de attribut som vi synkroniseras till molnet inte får vara det. Vi vill se till att dessa attribut tas bort från Azure AD.  
![Felaktig tilläggsattribut](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Skapa en ny regel för inkommande synkronisering och Fyll i beskrivningen.
  ![Beskrivningar](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Skapa attributflöden med **uttryck** för **ExchangeRate för FlowType** och med **AuthoritativeNull** för **källa**. Literalen **AuthoritativeNull** anger att värdet ska vara tomt i metaversum, även om en lägre prioritet synkroniseringsregel försöker att fylla i värdet.
  ![Transformering för tilläggsattribut](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Spara regeln synkronisering. Starta den **synkroniseringstjänsten**hittar anslutningsprogram, markera **kör**, och välj sedan **fullständig synkronisering**. Det här steget beräknar om alla attributflöden.
4. Kontrollera att de avsedda ändringarna är på väg att exporteras genom att söka i Anslutarplatsen.
  ![Mellanlagrad delete](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Skapa regler med PowerShell
Med hjälp av Regelredigeraren synkronisering fungerar bra när du behöver bara göra några ändringar. Om du behöver göra ändringar av kan PowerShell vara ett bättre alternativ. Vissa avancerade funktioner är bara tillgängliga med PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Hämta PowerShell-skriptet för en out-of-box-regel
I PowerShell-skript som skapats av en regel för out-of-box, väljer du regeln i Regelredigeraren synkronisering och klicka på **exportera**. Den här åtgärden ger dig PowerShell-skriptet som skapade regeln.

### <a name="advanced-precedence"></a>Avancerade prioritet
Out-of-box Synkroniseringsregler börjar med ett prioritetsvärde mellan 100. Om du har många skogar och du behöver göra många anpassade ändringar, sedan kanske 99 Synkroniseringsregler inte finns tillräckligt med.

Du kan instruera Synkroniseringsmotorn som du vill ha ytterligare regler som infogas innan out-of-box-regler. Följ dessa steg för att få det här beteendet kan:

1. Markera den första synkroniseringsregel för out-of-box (**i från AD-användare ansluta**) i Regelredigeraren för synkronisering och välj **exportera**. Kopiera SR ID-värde.  
![PowerShell före ändringen](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Skapa den nya regeln för synkronisering. Du kan använda regelredigeraren sync för att skapa den. Exportera regeln till ett PowerShell-skript.
3. I egenskapen **PrecedenceBefore**, infoga ID-värde från out-of-box-regeln. Ange den **prioritet** till **0**. Kontrollera att attributet ID: T är unikt och att du inte återanvänder en GUID från en annan regel. Också kontrollera att den **ImmutableTag** egenskapen har inte angetts. Den här egenskapen ska anges endast för en regel för out-of-box.
4. Spara PowerShell-skriptet och kör den. Resultatet är att anpassade regeln tilldelas prioritetsvärde mellan 100 och alla andra regler i out-of-box ökas.  
![PowerShell efter ändring](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

Du kan ha många anpassade Synkroniseringsregler genom att använda samma **PrecedenceBefore** värdet vid behov.

## <a name="enable-synchronization-of-usertype"></a>Aktivera synkronisering av UserType
Azure AD Connect stöder synkronisering av den **UserType** attributet för **användaren** objekt i version 1.1.524.0 och senare. Mer specifikt kan följande ändringar har införts:

- Schemat för objekttypen **användaren** i Azure AD Connector utökas för att inkludera UserType-attributet som är av typen sträng och är ett enkelvärdesattribut.
- Schemat för objekttypen **Person** i metaversum utökas för att inkludera UserType-attributet som är av typen sträng och är ett enkelvärdesattribut.

Som standard aktiveras inte UserType-attributet för synkronisering eftersom det finns inga motsvarande UserType-attributet i en lokal Active Directory. Du måste manuellt Aktivera synkronisering. Innan du gör detta måste du ta del av följande beteenden som tillämpas av Azure AD:

- Azure AD accepterar bara två värden för UserType-attributet: **Medlemmen** och **gäst**.
- Om UserType-attributet inte är aktiverad för synkronisering i Azure AD Connect, Azure AD-användare som skapats via katalogsynkronisering skulle ha UserType-attributet **medlem**.
- Azure AD tillåter inte UserType-attributet på befintliga Azure AD-användare ändras av Azure AD Connect. Det kan bara anges under genereringen av Azure AD-användare.

Innan du aktiverar synkronisering av UserType-attributet måste du först bestämma hur attributet härleds från en lokal Active Directory. Följande är de vanligaste metoderna:

- Ange en oanvända lokalt AD-attribut (till exempel extensionAttribute1) för att användas som källattributet. Den angivna lokala AD-attribut ska vara av typen **sträng**finnas enkelvärdesattribut och innehålla värdet **medlem** eller **gäst**. 

    Om du väljer den här metoden måste du kontrollera att det angivna attributet fylls med rätt värde för alla befintliga användarobjekt i den lokala Active Directory som synkroniseras till Azure AD innan du aktiverar synkronisering av UserType-attributet .

- Du kan också härleda värdet för UserType-attributet för andra egenskaper. Exempelvis kan du vill synkronisera alla användare som **gäst** om sina lokala AD userPrincipalName-attribut som slutar med domändelen <em>@partners.fabrikam123.org</em>. 

    Som tidigare nämnts är tillåter inte UserType-attributet på befintliga Azure AD-användare ändras av Azure AD Connect i Azure AD Connect. Därför måste du säkerställa att den logik som du har valt är konsekvent med hur UserType-attributet har redan konfigurerats för alla befintliga Azure AD-användare i din klient.

Stegen för att aktivera synkronisering av UserType-attributet kan sammanfattas som:

1.  Inaktivera sync scheduler och kontrollera att det finns ingen synkronisering pågår.
2.  Lägg till attribut för datakälla till lokalerna AD-koppling schemat.
3.  Lägga till UserType i Azure AD Connector-schemat.
4.  Skapa en regel för inkommande synkronisering för att flöda attribut-värde från lokala Active Directory.
5.  Skapa en utgående synkroniseringsregel för att flöda attributvärdet till Azure AD.
6.  Kör en fullständig synkroniseringscykel.
7.  Aktivera sync scheduler.

>[!NOTE]
> Resten av det här avsnittet beskriver de här stegen. De beskrivs i samband med en Azure AD-distribution med topologi med en enda skog och utan anpassade Synkroniseringsregler. Om du har flera skogar topologi anpassade Synkroniseringsregler konfigurerats eller har en fristående server, måste du anpassa stegen i enlighet med detta.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Steg 1: Inaktivera sync scheduler och verifiera det finns ingen synkronisering pågår
Se till att ingen synkronisering sker när du är mitt uppdaterar Synkroniseringsregler för att undvika exporterar oönskade ändringar till Azure AD. Inaktivera inbyggda sync scheduler:

 1. Starta en PowerShell-session på Azure AD Connect-servern.
 2. Inaktivera schemalagd synkronisering genom att köra cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 3. Öppna Hanteraren för synkroniseringstjänsten genom att gå till **starta** > **synkroniseringstjänsten**.
 4. Gå till den **Operations** fliken och bekräfta att ingen åtgärd med statusen *pågår*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Steg 2: Lägg till attribut för datakälla till lokalerna AD anslutningsschema
Inte alla Azure AD-attribut har importerats till lokalt AD-anslutningsplatsen. Lägga till källattributet i listan över importerade attribut:

 1. Gå till den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
 2. Högerklicka på lokalt AD-koppling och välj **egenskaper**.
 3. I popup-rutan, gå till den **Välj attribut** fliken.
 4. Kontrollera att källattributet är markerat i attributlistan.
 5. Klicka på **OK** att spara.
![Lägg till attribut för datakälla till en lokal AD anslutningsschema](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>Steg 3: Lägga till UserType i Azure AD Connector-schemat
Som standard är UserType-attributet inte importerad till Azure AD Connect utrymme. Lägga till UserType-attributet i listan över importerade attribut:

 1. Gå till den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
 2. Högerklicka på den **Azure AD Connector** och välj **egenskaper**.
 3. I popup-rutan, gå till den **Välj attribut** fliken.
 4. Kontrollera att UserType-attributet är markerat i attributlistan.
 5. Klicka på **OK** att spara.

![Lägga till källattributet schemat för Azure AD-koppling](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Steg 4: Skapa en regel för inkommande synkronisering för att flöda attribut-värde från lokala Active Directory
Regel för inkommande synkronisering tillåter attributvärdet som flödar från källattributet från en lokal Active Directory till metaversum:

1. Öppna Synchronization Rules Editor genom att gå till **starta** > **Synchronization Rules Editor**.
2. Ange sökfiltret **riktning** vara **inkommande**.
3. Klicka på den **Lägg till ny regel** för att skapa en ny inkommande regel.
4. Under den **beskrivning** fliken tillhandahåller följande konfiguration:

    | Attribut | Värde | Information |
    | --- | --- | --- |
    | Namn | *Ange ett namn* | Till exempel *i från AD – användaren UserType* |
    | Beskrivning | *Ange en beskrivning* |  |
    | Anslutna System | *Välj lokalt AD-koppling* |  |
    | Anslutna System objekttyp | **Användaren** |  |
    | Typ av Metaversumobjekt | **Person** |  |
    | Länktyp | **Anslut dig** |  |
    | Prioritet | *Välj ett tal mellan 1 – 99* | 1 – 99 är reserverad för av anpassade Synkroniseringsregler. Välj inte ett värde som används av andra Synkroniseringsregler. |

5. Gå till den **Scoping filter** fliken och Lägg till en **enda gemensam Filtergrupp** med följande sats:

    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | administratörsbeskrivning | NOTSTARTWITH | Användaren\_ |

    Omfångsfilter anger till vilken den lokala AD-objekt den här regeln för inkommande synkronisering tillämpas. I det här exemplet använder vi samma Omfångsfilter som används i den *i från AD – vanliga användare* synkroniseringsregel för out-of-box, vilket förhindrar att synkroniseringsregeln tillämpas på objekt som skapas via Azure AD-användare funktionen för tillbakaskrivning av. Du kan behöva justera Omfångsfilter enligt din Azure AD Connect-distribution.

6. Gå till den **omvandling** fliken och implementera den önskade omvandlingsregeln. Exempel: Om du har angett en oanvända lokala AD-attribut (till exempel extensionAttribute1) som källattributet för UserType, du kan implementera en direkt attributflöde:

    | Flow-typ | Målattribut | Källa | Använda en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    | Direkt | UserType | extensionAttribute1 | Alternativet är avmarkerat | Uppdatering |

    Ett annat exempel är som du vill erhålla värde för UserType-attributet för andra egenskaper. Exempelvis kan du vill synkronisera alla användare som gäst om sina lokala AD userPrincipalName-attribut som slutar med domändelen <em>@partners.fabrikam123.org</em>. Du kan implementera ett uttryck så här:

    | Flow-typ | Målattribut | Källa | Använda en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    | Direkt | UserType | IIf(IsPresent([userPrincipalName]),IIf(CBool(Instr(LCase([userPrincipalName]) ”,@partners.fabrikam123.org”)=0) ”medlem”, ”Gäst”), fel (”UserPrincipalName finns inte att fastställa UserType”)) | Alternativet är avmarkerat | Uppdatering |

7. Klicka på **Lägg till** att skapa regel för inkommande trafik.

![Skapa regel för inkommande synkronisering](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Steg 5: Skapa en utgående synkroniseringsregel för att flöda attributvärdet till Azure AD
Den utgående synkroniseringsregeln tillåter attributvärdet som flödar från metaversum till UserType-attributet i Azure AD:

1. Gå till Synchronization Rules Editor.
2. Ange sökfiltret **riktning** vara **utgående**.
3. Klicka på den **Lägg till ny regel** knappen.
4. Under den **beskrivning** fliken tillhandahåller följande konfiguration:

    | Attribut | Värde | Information |
    | ----- | ------ | --- |
    | Namn | *Ange ett namn* | Till exempel *ut till AAD – användaren UserType* |
    | Beskrivning | *Ange en beskrivning* ||
    | Anslutna System | *Välj den AAD-kopplingen* ||
    | Anslutna System objekttyp | **Användaren** ||
    | Typ av Metaversumobjekt | **Person** ||
    | Länktyp | **Anslut dig** ||
    | Prioritet | *Välj ett tal mellan 1 – 99* | 1 – 99 är reserverad för av anpassade Synkroniseringsregler. Välj inte ett värde som används av andra Synkroniseringsregler. |

5. Gå till den **Scoping filter** fliken och Lägg till en **enda gemensam Filtergrupp** med två satser:

    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Användare |
    | cloudMastered | NOTEQUAL | True |

    Omfångsfilter fastställer som Azure AD objekt utgående synkroniseringsregeln gäller. I det här exemplet använder vi samma Omfångsfilter från den *till AD – användaridentitet* synkroniseringsregel för out-of-box. Det förhindrar att synkroniseringsregeln tillämpas på objekt som inte är synkroniserade från en lokal Active Directory. Du kan behöva justera Omfångsfilter enligt din Azure AD Connect-distribution.

6. Gå till den **omvandling** fliken och implementera följande omvandlingsregeln:

    | Flow-typ | Målattribut | Källa | Använda en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    | Direkt | UserType | UserType | Alternativet är avmarkerat | Uppdatering |

7. Klicka på **Lägg till** att skapa regel för utgående trafik.

![Skapa regel för utgående synkronisering](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Steg 6: Kör en fullständig synkroniseringscykel
I allmänhet krävs en fullständig synkroniseringscykel eftersom vi har lagt till nya attribut till både Active Directory och Azure AD Connector scheman och introducerades anpassade Synkroniseringsregler. Du vill kontrollera ändringarna innan du exporterar dem till Azure AD. 

Du kan använda följande steg för att bekräfta ändringarna när du kör de steg som utgör en fullständig synkroniseringscykel manuellt.

1. Kör en **fullständig import** på den **lokala AD Connector**:

   1. Gå till den **Operations** fliken i hanteraren för synkroniseringstjänsten.
   2. Högerklicka på den **lokala AD Connector** och välj **kör**.
   3. I popup-dialogrutan Välj **fullständig Import** och klicka sedan på **OK**.
   4. Vänta tills åtgärden slutförs.

    > [!NOTE]
    > Du kan hoppa över en fullständig import på lokalt AD-koppling om källattributet ingår redan i listan över importerade attribut. Med andra ord du inte göra några ändringar under [steg 2: Lägg till attribut för datakälla till lokalerna AD anslutningsschema](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Kör en **fullständig import** på den **Azure AD Connector**:

   1. Högerklicka på den **Azure AD Connector** och välj **kör**.
   2. I popup-dialogrutan Välj **fullständig Import** och klicka sedan på **OK**.
   3. Vänta tills åtgärden slutförs.

3. Bekräfta synkronisering regeln ändringarna på ett befintligt användarobjekt:

    Källattribut från lokala Active Directory och UserType från Azure AD har importerats till sina respektive kopplingens utrymmen. Innan du fortsätter med en fullständig synkronisering ska göra en **förhandsversion** på en befintlig användare-objektet i lokalt AD-anslutningsplatsen. Det objekt som du har valt bör ha fyllts i källattributet.
    
    Ett lyckat **förhandsversion** är en bra indikator som du har konfigurerat synkroniseringen regler korrekt med UserType ifylld i metaversum. Information om hur du gör en **förhandsversion**, finns i avsnittet [bekräfta ändringen](#verify-the-change).

4. Kör en **fullständig synkronisering** på den **lokala AD Connector**:

   1. Högerklicka på den **lokala AD Connector** och välj **kör**.
   2. I popup-dialogrutan Välj **fullständig synkronisering** och klicka sedan på **OK**.
   3. Vänta tills åtgärden slutförs.

5. Kontrollera **väntande exporter** till Azure AD:

   1. Högerklicka på den **Azure AD Connector** och välj **Search Connector Space**.
   2. I den **Search Connector Space** popup-dialogrutan:

      - Ange **omfång** till **väntande Export**.
      - Markera alla tre kryssrutorna: **Lägg till**, **ändra**, och **ta bort**.
      - Klicka på den **Search** knappen för att hämta listan över objekt med ändringar som ska exporteras. Dubbelklicka på objektet om du vill kontrollera ändringarna för ett angivet objekt.
      - Kontrollera att ändringarna är förväntat.

6. Kör **exportera** på den **Azure AD Connector**:

   1. Högerklicka på den **Azure AD Connector** och välj **kör**.
   2. I den **kör anslutningstjänsten** popup-dialogrutan **exportera** och klicka sedan på **OK**.
   3. Vänta tills exporten till Azure AD ska slutföras.

> [!NOTE]
> De här stegen inte inkludera den fullständiga synkroniseringen och exportera steg i Azure AD Connector. Dessa steg behövs inte eftersom attributvärdena som flödar in från en lokal Active Directory till Azure AD.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Steg 7: Återaktivera sync scheduler
Återaktivera inbyggda sync scheduler:

1. Starta en PowerShell-session.
2. Aktivera schemalagd synkronisering igen genom att köra cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $true`.


## <a name="next-steps"></a>Nästa steg
* Läs mer om konfigurationsmodellen i [förstå deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Läs mer om Uttrycksspråk i [förstå uttryck för deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
