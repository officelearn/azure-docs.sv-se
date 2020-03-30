---
title: 'Synkronisering av Azure AD Connect: Gör en konfigurationsändring i Azure AD Connect-synkronisering'
description: Lär dig hur du gör en ändring av konfigurationen i Azure AD Connect-synkronisering.
services: active-directory
author: billmath
manager: daveba
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d77882817934d5ad98f16965aeb9dc246931c495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261169"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Synkronisering av Azure AD Connect: Gör en ändring av standardkonfigurationen
Syftet med den här artikeln är att gå igenom hur du gör ändringar i standardkonfigurationen i Azure Active Directory (Azure AD) Connect-synkronisering. Det innehåller steg för några vanliga scenarier. Med denna kunskap bör du kunna göra enkla ändringar i din egen konfiguration baserat på dina egna affärsregler.

> [!WARNING]
> Om du gör ändringar i standardregler för ingående synkronisering skrivs dessa ändringar över nästa gång Azure AD Connect uppdateras, vilket resulterar i oväntade och sannolikt oönskade synkroniseringsresultat.
>
> Standardsynkroniseringsreglerna för out-of-box har ett tumavtryck. Om du gör en ändring av dessa regler matchar tumavtrycket inte längre. Du kan ha problem i framtiden när du försöker tillämpa en ny version av Azure AD Connect. Gör bara ändringar på det sätt som beskrivs i den här artikeln.

## <a name="synchronization-rules-editor"></a>Redigeraren för synkroniseringsregler
Redigeraren för synkroniseringsregler används för att se och ändra standardkonfigurationen. Du hittar den på **Start-menyn** under gruppen **Azure AD Connect.**  
![Start-menyn med Redigerare för synkroniseringsregler](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

När du öppnar redigeraren visas standardreglerna.

![Redigerare för synkroniseringsregel](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigera i redigeraren
Med hjälp av listrutan högst upp i redigeraren kan du snabbt hitta en specifik regel. Om du till exempel vill se reglerna där attributproxyadresserna ingår kan du ändra listrapparna till följande:  
![SRE-filtrering](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Om du vill återställa filtrering och läsa in en ny konfiguration trycker du på F5 på tangentbordet.

Längst upp till höger finns knappen **Lägg till ny regel.** Du använder den här knappen för att skapa en egen anpassad regel.

Längst ned finns knappar för att agera på en vald synkroniseringsregel. **Redigera** och **ta bort** gör vad du förväntar dig att de ska göra. **Export** skapar ett PowerShell-skript för att återskapa synkroniseringsregeln. Med den här proceduren kan du flytta en synkroniseringsregel från en server till en annan.

## <a name="create-your-first-custom-rule"></a>Skapa din första anpassade regel
De vanligaste ändringarna är attributflödena. Data i källkatalogen kanske inte är desamma som i Azure AD. I exemplet i det här avsnittet kontrollerar du att en användares förnamn alltid är i *rätt fall*.

### <a name="disable-the-scheduler"></a>Inaktivera schemaläggaren
[Schemaläggaren](how-to-connect-sync-feature-scheduler.md) körs som standard var 30:e minut. Kontrollera att den inte startar när du gör ändringar och felsöker dina nya regler. Om du tillfälligt vill inaktivera schemaläggaren `Set-ADSyncScheduler -SyncCycleEnabled $false`startar du PowerShell och kör .

![Inaktivera schemaläggaren](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Skapa regeln
1. Klicka på **Lägg till ny regel**.
2. På sidan **Beskrivning** anger du följande:  
   ![Filtrering av inkommande regel](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Namn**: Ge regeln ett beskrivande namn.
   * **Beskrivning**: Ge några förtydliganden så att någon annan kan förstå vad regeln är till för.
   * **Anslutet system:** Detta är det system där objektet kan hittas. I det här fallet väljer du **Active Directory Connector**.
   * **Objekttyp för anslutet system/metaversum:** Välj **användare** respektive **person.**
   * **Länktyp:** Ändra det här värdet så att **det går med.**
   * **Prioritet**: Ange ett värde som är unikt i systemet. Ett lägre numeriskt värde anger högre prioritet.
   * **Tag**: Lämna detta tomt. Endast färdiga regler från Microsoft bör ha den här rutan ifylld med ett värde.
3. På sidan **Omfångsfilter** anger du **givenName ISNOTNULL**.  
   ![Ingående regel omfångsfilter](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Det här avsnittet används för att definiera vilka objekt regeln ska gälla för. Om den lämnas tom gäller regeln för alla användarobjekt. Det skulle dock omfatta konferensrum, tjänstkonton och andra icke-personanvändarobjekt.
4. Lämna fältet tomt på sidan **Koppla regler.**
5. På sidan **Transformationer** ändrar du **FlowType** till **Expression**. För **Målattribut**väljer du **givenName**. Och för **Källa**anger du **PCase([givenName])**.
   ![Omvandlingar för inkommande regel](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   Synkroniseringsmotorn är skiftlägeskänslig för både funktionsnamnet och namnet på attributet. Om du skriver något fel visas en varning när du lägger till regeln. Du kan spara och fortsätta, men du måste öppna och korrigera regeln igen.
6. Klicka på **Lägg till** om du vill spara regeln.

Den nya anpassade regeln ska vara synlig med de andra synkroniseringsreglerna i systemet.

### <a name="verify-the-change"></a>Verifiera ändringen
Med den här nya ändringen vill du vara säker på att den fungerar som förväntat och inte kastar några fel. Beroende på hur många objekt du har finns det två sätt att göra det här steget:

- Kör en fullständig synkronisering på alla objekt.
- Kör en förhandsgranskning och fullständig synkronisering på ett enda objekt.

Öppna **synkroniseringstjänsten** på **Start-menyn.** Stegen i det här avsnittet finns i det här verktyget.

**Fullständig synkronisering på alla objekt**  

   1. Välj **Kontakter** högst upp. Identifiera kopplingen som du har ändrat i föregående avsnitt (i det här fallet Active Directory Domain Services) och markera den. 
   2. För **Åtgärder**väljer du **Kör**.
   3. Välj **Fullständig synkronisering**och välj sedan **OK**.
   ![Fullständig synkronisering](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   Objekten uppdateras nu i metaversumet. Verifiera ändringarna genom att titta på objektet i metaversumet.

**Förhandsgranska och synkronisera helt på ett enskilt objekt**  

   1. Välj **Kontakter** högst upp. Identifiera kopplingen som du har ändrat i föregående avsnitt (i det här fallet Active Directory Domain Services) och markera den.
   2. Välj **Sökkopplingsutrymme**. 
   3. Använd **Scope** för att hitta ett objekt som du vill använda för att testa ändringen. Markera objektet och klicka på **Förhandsgranska**. 
   4. På den nya skärmen väljer du **Commit Preview**.  
   ![Förhandsgranskning av genomför](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   Ändringen är nu engagerad i metaversumet.

**Visa objektet i metaversumet**  

1. Välj några exempelobjekt för att kontrollera att värdet är förväntat och att regeln tillämpas. 
2. Välj **Metaverse Search** högst upp. Lägg till alla filter som du behöver för att hitta relevanta objekt. 
3. Öppna ett objekt från sökresultatet. Titta på attributvärdena och kontrollera även i kolumnen **Synkroniseringsregler** att regeln tillämpas som förväntat.  
![Metaversumsökning](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Aktivera schemaläggaren
Om allt är som förväntat kan du aktivera schemaläggaren igen. Kör från PowerShell `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Andra vanliga attributflödesändringar
I föregående avsnitt beskrivs hur du gör ändringar i ett attributflöde. I det här avsnittet finns några ytterligare exempel. Stegen för hur du skapar synkroniseringsregeln förkortas, men du hittar hela stegen i föregående avsnitt.

### <a name="use-an-attribute-other-than-the-default"></a>Använda ett annat attribut än standardvärdet
I det här Fabrikam-scenariot finns det en skog där det lokala alfabetet används för förnamn, efternamn och visningsnamn. Den latinska teckenrepresentationen av dessa attribut finns i tilläggsattributen. För att skapa en global adresslista i Azure AD och Office 365 vill organisationen använda dessa attribut i stället.

Med en standardkonfiguration ser ett objekt från den lokala skogen ut så här:  
![Attributflöde 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Så här skapar du en regel med andra attributflöden:

1. Öppna **Redigeraren** för synkroniseringsregler på **Start-menyn.**
2. När **Inkommande** fortfarande är markerat till vänster klickar du på knappen **Lägg till ny regel.**
3. Ge regeln ett namn och en beskrivning. Markera den lokala Active Directory-instansen och relevanta objekttyper. Välj **Anslut**i **länktyp**. För **Prioritet**väljer du ett tal som inte används av en annan regel. Out-of-box-reglerna börjar med 100, så värdet 50 kan användas i det här exemplet.
  ![Attributflöde 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Lämna **Scoping-filtret** tomt. (Det vill säga, det ska gälla för alla användarobjekt i skogen.)
5. Lämna **join-reglerna** tomma. (Det vill säga låt regeln om out-of-box hantera alla kopplingar.)
6. Skapa följande flöden i **Transformations:**  
  ![Attributflöde 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Klicka på **Lägg till** om du vill spara regeln.
8. Gå till **Synkroniseringstjänsthanteraren**. På **Kopplingar**väljer du kopplingen där du lade till regeln. Välj **Kör**och välj sedan **Fullständig synkronisering**. En fullständig synkronisering beräknar om alla objekt med hjälp av de aktuella reglerna.

Detta är resultatet för samma objekt med den här anpassade regeln:  
![Attributflöde 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Attributens längd
Strängattribut kan visas som standard och den maximala längden är 448 tecken. Om du arbetar med strängattribut som kan innehålla fler måste du inkludera följande i attributflödet:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Ändra användarenPrincipalSuffix
Attributet userPrincipalName i Active Directory är inte alltid känt av användarna och kanske inte är lämpligt som inloggnings-ID. Med guiden Azure AD Connect-synkroniseringsinstallation kan du välja ett annat attribut – till exempel *e-post*. Men i vissa fall måste attributet beräknas.

Företaget Contoso har till exempel två Azure AD-kataloger, en för produktion och en för testning. De vill att användarna i sin testklient ska använda ett annat suffix i inloggnings-ID:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

I det här uttrycket tar @-sign du allt som finns kvar av det första (Word) och sammanfogar med en fast sträng.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Konvertera ett attribut med flera värden till ett värde
Vissa attribut i Active Directory värderas flera i schemat, även om de ser ett värde som ett värde i Active Directory – användare och datorer. Ett exempel är beskrivningsattributet:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

Om attributet har ett värde i det här uttrycket tar du det första objektet (*Objekt*) i attributet, tar bort inledande och avslutande blanksteg (*Trimma*) och behåller sedan de första 448 tecknen (*vänster*) i strängen.

### <a name="do-not-flow-an-attribute"></a>Flöda inte ett attribut
Bakgrundsinformation om scenariot för det här avsnittet finns i [Styra attributflödesprocessen](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Det finns två sätt att inte flöda ett attribut. Den första är att använda installationsguiden för att [ta bort markerade attribut](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering). Det här alternativet fungerar om du aldrig har synkroniserat attributet tidigare. Men om du har börjat synkronisera det här attributet och senare ta bort det med den här funktionen, slutar synkroniseringsmotorn att hantera attributet och de befintliga värdena finns kvar i Azure AD.

Om du vill ta bort värdet för ett attribut och se till att det inte flödar i framtiden, måste du skapa en anpassad regel.

I det här Fabrikam-scenariot har vi insett att några av de attribut som vi synkroniserar med molnet inte ska finnas där. Vi vill se till att dessa attribut tas bort från Azure AD.  
![Felaktiga tilläggsattribut](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Skapa en ny inkommande synkroniseringsregel och fyll i beskrivningen.
  ![Beskrivningar](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Skapa attributflöden med **Expression** för **FlowType** och med **AuthoritativeNull** for **Source**. Den litterala **auktoritativaNullen** anger att värdet ska vara tomt i metaversumet, även om en synkroniseringsregel med lägre prioritet försöker fylla i värdet.
  ![Omvandling för tilläggsattribut](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Spara synkroniseringsregeln. Starta **synkroniseringstjänsten,** leta reda på kopplingen, välj **Kör**och välj sedan **Fullständig synkronisering**. Det här steget beräknar om alla attributflöden.
4. Kontrollera att de avsedda ändringarna är på väg att exporteras genom att söka i anslutningsutrymmet.
  ![Stegvis borttagning](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Skapa regler med PowerShell
Att använda redigeraren för synkroniseringsregeln fungerar bra när du bara har några få ändringar att göra. Om du behöver göra många ändringar kan PowerShell vara ett bättre alternativ. Vissa avancerade funktioner är endast tillgängliga med PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Skaffa PowerShell-skriptet för en out-of-box-regel
Om du vill visa PowerShell-skriptet som skapade en out-of-box-regel markerar du regeln i redigeraren för synkroniseringsregler och klickar på **Exportera**. Den här åtgärden ger dig PowerShell-skriptet som skapade regeln.

### <a name="advanced-precedence"></a>Avancerad prioritet
De färdiga synkroniseringsreglerna börjar med ett prioritetsvärde på 100. Om du har många skogar och du behöver göra många anpassade ändringar kanske det inte räcker med 99 synkroniseringsregler.

Du kan instruera synkroniseringsmotorn att du vill att ytterligare regler ska infogas innan reglerna för out-of-box. Så här hämtar du följande steg:

1. Markera den första out-of-box synkroniseringsregeln (**In från AD-User Join**) i redigeraren för synkroniseringsregler och välj **Exportera**. Kopiera värdet SR-identifierare.  
![PowerShell före ändring](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Skapa den nya synkroniseringsregeln. Du kan använda redigeraren för synkroniseringsregler för att skapa den. Exportera regeln till ett PowerShell-skript.
3. I egenskapen **PrioritetFör infogar**du identifierarevärdet från direktrutan. Ange **prioriteten** till **0**. Kontrollera att identifier-attributet är unikt och att du inte återanvänder ett GUID från en annan regel. Kontrollera också att egenskapen **ImmutableTag** inte har angetts. Den här egenskapen bör endast anges för en out-of-box-regel.
4. Spara PowerShell-skriptet och kör det. Resultatet är att din anpassade regel tilldelas prioritetsvärdet 100 och alla andra färdiga regler ökas.  
![PowerShell efter ändring](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

Du kan ha många anpassade synkroniseringsregler genom att använda samma **PrioritetFörse** värde när det behövs.

## <a name="enable-synchronization-of-usertype"></a>Aktivera synkronisering av UserType
Azure AD Connect stöder synkronisering av **UserType-attributet** för **användarobjekt** i version 1.1.524.0 och senare. Mer specifikt har följande ändringar införts:

- Schemat för objekttypen **Användare** i Azure AD Connector utökas till att omfatta Attributet UserType, som är av typen sträng och är enkelvärderat.
- Schemat för objekttypen **Person** i metaversumet utökas till att omfatta Attributet UserType, som är av typen sträng och är enkelvärderat.

Som standard är Attributet UserType inte aktiverat för synkronisering eftersom det inte finns något motsvarande UserType-attribut i lokal Active Directory. Du måste aktivera synkronisering manuellt. Innan du gör det måste du ta del av följande beteende som tillämpas av Azure AD:

- Azure AD accepterar endast två värden för UserType-attributet: **Medlem** och **Gäst**.
- Om Attributet UserType inte är aktiverat för synkronisering i Azure AD Connect, skulle Azure AD-användare som skapats via katalogsynkronisering ha attributet UserType inställt på **Medlem**.
- Azure AD tillåter inte att UserType-attributet för befintliga Azure AD-användare ändras av Azure AD Connect. Den kan bara ställas in när Azure AD-användare skapas och [ändras via Powershell](/powershell/module/azuread/set-azureaduser?view=azureadps-2.0).

Innan du aktiverar synkronisering av UserType-attributet måste du först bestämma hur attributet härleds från lokal Active Directory. Följande är de vanligaste metoderna:

- Ange ett oanvänt lokalt AD-attribut (till exempel extensionAttribute1) som ska användas som källattribut. Det angivna lokala AD-attributet ska vara av typen **sträng,** vara ett värde och innehålla värdet **Medlem** eller **Gäst**. 

    Om du väljer den här metoden måste du se till att det angivna attributet fylls med rätt värde för alla befintliga användarobjekt i lokal Active Directory som synkroniseras med Azure AD innan synkroniseringen av UserType-attributet aktiveras .

- Du kan också härleda värdet för UserType-attributet från andra egenskaper. Du vill till exempel synkronisera alla användare som **gäst** om deras lokala AD-användarePrincipalName-attribut slutar med domändelen <em>@partners.fabrikam123.org</em>. 

    Som tidigare nämnts tillåter Azure AD Connect inte att UserType-attributet för befintliga Azure AD-användare ändras av Azure AD Connect. Därför måste du se till att logiken du har bestämt är förenlig med hur UserType-attributet redan är konfigurerat för alla befintliga Azure AD-användare i din klientorganisation.

Stegen för att aktivera synkronisering av UserType-attributet kan sammanfattas som:

1.  Inaktivera synkroniseringsschemat och kontrollera att ingen synkronisering pågår.
2.  Lägg till källattributet i det lokala AD Connector-schemat.
3.  Lägg till UserType i Azure AD Connector-schemat.
4.  Skapa en inkommande synkroniseringsregel för att flöda attributvärdet från lokal Active Directory.
5.  Skapa en utgående synkroniseringsregel för att flöda attributvärdet till Azure AD.
6.  Kör en fullständig synkroniseringscykel.
7.  Aktivera synkroniseringsschemat.

>[!NOTE]
> Resten av det här avsnittet beskriver dessa steg. De beskrivs i samband med en Azure AD-distribution med enskogstopologi och utan anpassade synkroniseringsregler. Om du har topologi för flera skogar, anpassade synkroniseringsregler konfigurerade eller har en mellanlagringsserver måste du justera stegen därefter.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Steg 1: Inaktivera synkroniseringsschemat och kontrollera att ingen synkronisering pågår
Om du vill undvika att exportera oavsiktliga ändringar till Azure AD kontrollerar du att ingen synkronisering sker när du är mitt uppe i att uppdatera synkroniseringsregler. Så här inaktiverar du den inbyggda synkroniseringsschemat:

 1. Starta en PowerShell-session på Azure AD Connect-servern.
 2. Inaktivera schemalagd synkronisering genom att `Set-ADSyncScheduler -SyncCycleEnabled $false`köra cmdleten .
 3. Öppna synkroniseringstjänsthanteraren genom att gå till **Starta** > **synkroniseringstjänsten**.
 4. Gå till fliken **Operationer** och bekräfta att det inte finns någon åtgärd med *statusen pågår*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Steg 2: Lägg till källattributet i det lokala AD Connector-schemat
Alla Azure AD-attribut importeras inte till det lokala AD Connector-utrymmet. Så här lägger du till källattributet i listan över importerade attribut:

 1. Gå till fliken **Kontakter** i Synkroniseringstjänsthanteraren.
 2. Högerklicka på den lokala AD-kopplingen och välj **Egenskaper**.
 3. Gå till fliken **Välj attribut** i popup-dialogrutan.
 4. Kontrollera att källattributet är markerat i attributlistan.
 5. Spara genom att klicka på **OK.**
![Lägga till källattribut i lokalt AD Connector-schema](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>Steg 3: Lägga till UserType i Azure AD Connector-schemat
Som standard importeras inte UserType-attributet till Azure AD Connect Space. Så här lägger du till Attributet UserType i listan över importerade attribut:

 1. Gå till fliken **Kontakter** i Synkroniseringstjänsthanteraren.
 2. Högerklicka på **Azure AD Connector** och välj **Egenskaper**.
 3. Gå till fliken **Välj attribut** i popup-dialogrutan.
 4. Kontrollera att Attributet UserType är markerat i attributlistan.
 5. Spara genom att klicka på **OK.**

![Lägga till källattribut i Azure AD Connector-schemat](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Steg 4: Skapa en inkommande synkroniseringsregel för att flöda attributvärdet från lokal Active Directory
Den inkommande synkroniseringsregeln gör att attributvärdet kan flöda från källattributet från lokal Active Directory till metaversumet:

1. Öppna redigeraren för synkroniseringsregler genom att gå till **Starta** > **Redigeraren för synkroniseringsregler**.
2. Ange att sökfiltret **Riktning** ska vara **inkommande**.
3. Klicka på knappen **Lägg till ny regel** om du vill skapa en ny inkommande regel.
4. Ange följande konfiguration under fliken **Beskrivning:**

    | Attribut | Värde | Information |
    | --- | --- | --- |
    | Namn | *Ange ett namn* | Till exempel *in från AD – User UserType* |
    | Beskrivning | *Ge en beskrivning* |  |
    | Anslutet system | *Välj den lokala AD-kopplingen* |  |
    | Ansluten systemobjekttyp | **Användare** |  |
    | Objekttyp för metaversum | **Person** |  |
    | Länktyp | **Anslut** |  |
    | Prioritet | *Välj ett nummer mellan 1–99* | 1–99 är reserverat för anpassade synkroniseringsregler. Välj inte ett värde som används av en annan synkroniseringsregel. |

5. Gå till fliken **Omfångsfilter** och lägg till en **enda filtergrupp** med följande sats:

    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | Admindescription | NOTSTARTWITH | Användaren\_ |

    Omfångsfiltret avgör på vilka lokala AD-objekt den här inkommande synkroniseringsregeln tillämpas. I det här exemplet använder vi samma omfångsfilter som används i synkroniseringsregeln *In från AD – Användare som inte* är i gång, vilket förhindrar att synkroniseringsregeln tillämpas på användarobjekt som skapats via funktionen för återskrivning av Azure AD-användare. Du kan behöva justera filtret för omfång enligt din Azure AD Connect-distribution.

6. Gå till fliken **Omformning** och implementera den önskade omvandlingsregeln. Om du till exempel har angett ett oanvänt lokalt AD-attribut (till exempel extensionAttribute1) som källattribut för UserType kan du implementera ett direkt attributflöde:

    | Flödestyp | Målattribut | Källa | Ansök en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | extensionAttribute1 | Avmarkerat | Uppdatering |

    I ett annat exempel vill du härleda värdet för UserType-attributet från andra egenskaper. Du vill till exempel synkronisera alla användare som gäst om deras lokala AD-användarePrincipalName-attribut slutar med domändelen <em>@partners.fabrikam123.org</em>. Du kan implementera ett uttryck så här:

    | Flödestyp | Målattribut | Källa | Ansök en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    | Uttryck | UserType | IIF(IsPresent([userPrincipalName]),IIF(CBool(InStr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0),"Medlem","Gäst"),Fel("UserPrincipalName finns inte för att fastställa UserType")) | Avmarkerat | Uppdatering |

7. Klicka på **Lägg till** om du vill skapa den inkommande regeln.

![Skapa regel för inkommande synkronisering](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Steg 5: Skapa en utgående synkroniseringsregel för att flöda attributvärdet till Azure AD
Regeln för utgående synkronisering gör att attributvärdet kan flöda från metaversum till UserType-attributet i Azure AD:

1. Gå till Redigeraren för synkroniseringsregler.
2. Ange att sökfiltret **Riktning** ska vara **utgående**.
3. Klicka på knappen **Lägg till ny regel.**
4. Ange följande konfiguration under fliken **Beskrivning:**

    | Attribut | Värde | Information |
    | ----- | ------ | --- |
    | Namn | *Ange ett namn* | Till exempel *ut till AAD – User UserType* |
    | Beskrivning | *Ge en beskrivning* ||
    | Anslutet system | *Markera AAD-kontakten* ||
    | Ansluten systemobjekttyp | **Användare** ||
    | Objekttyp för metaversum | **Person** ||
    | Länktyp | **Anslut** ||
    | Prioritet | *Välj ett nummer mellan 1–99* | 1–99 är reserverat för anpassade synkroniseringsregler. Välj inte ett värde som används av en annan synkroniseringsregel. |

5. Gå till fliken **Scoping-filter** och lägg till en **enda filtergrupp** med två satser:

    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | sourceObjectType | Lika | Användare |
    | cloudMastered | NOTER | True |

    Omfångsfiltret avgör på vilka Azure AD-objekt som den här utgående synkroniseringsregeln tillämpas på. I det här exemplet använder vi samma omfångsfilter från synkroniseringsregeln *Ut till AD – Användaridentitet.* Det förhindrar att synkroniseringsregeln tillämpas på användarobjekt som inte är synkroniserade från lokala Active Directory. Du kan behöva justera filtret för omfång enligt din Azure AD Connect-distribution.

6. Gå till fliken **Omformning** och implementera följande omvandlingsregel:

    | Flödestyp | Målattribut | Källa | Ansök en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | UserType | Avmarkerat | Uppdatering |

7. Klicka på **Lägg till** om du vill skapa den utgående regeln.

![Skapa utgående synkroniseringsregel](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Steg 6: Kör en fullständig synkroniseringscykel
I allmänhet krävs en fullständig synkroniseringscykel eftersom vi har lagt till nya attribut i både Active Directory- och Azure AD Connector-scheman och infört anpassade synkroniseringsregler. Du vill verifiera ändringarna innan du exporterar dem till Azure AD. 

Du kan använda följande steg för att verifiera ändringarna när du kör stegen manuellt för att skapa en fullständig synkroniseringscykel.

1. Kör en **fullständig import** på den **lokala AD-kopplingen:**

   1. Gå till fliken **Operationer** i Synkroniseringstjänsthanteraren.
   2. Högerklicka på den **lokala AD-kopplingen** och välj **Kör**.
   3. Välj **Fullständig import** i popup-dialogrutan och klicka sedan på **OK**.
   4. Vänta tills operationen är klar.

      > [!NOTE]
      > Du kan hoppa över en fullständig import på den lokala AD-kopplingen om källattributet redan finns med i listan över importerade attribut. Med andra ord behövde du inte göra några ändringar under [steg 2: Lägg till källattributet i det lokala AD Connector-schemat](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Kör en **fullständig import** på Azure **AD Connector:**

   1. Högerklicka på **Azure AD Connector** och välj **Kör**.
   2. Välj **Fullständig import** i popup-dialogrutan och klicka sedan på **OK**.
   3. Vänta tills operationen är klar.

3. Verifiera ändringar av synkroniseringsregeln för ett befintligt användarobjekt:

    Källattributet från lokala Active Directory och UserType från Azure AD har importerats till sina respektive Anslutningsutrymmen. Innan du fortsätter med en fullständig synkronisering gör du en **förhandsgranskning** av ett befintligt användarobjekt i det lokala AD Connector-utrymmet. Det objekt du valde ska ha källattributet ifyllt.
    
    En lyckad **förhandsgranskning** med UserType ifylld i metaversumet är en bra indikator på att du har konfigurerat synkroniseringsreglerna korrekt. Information om hur du gör en **förhandsgranskning**finns i avsnittet [Verifiera ändringen](#verify-the-change).

4. Kör en **fullständig synkronisering** på den **lokala AD-anslutningen:**

   1. Högerklicka på den **lokala AD-kopplingen** och välj **Kör**.
   2. Välj **Fullständig synkronisering** i popup-dialogrutan och klicka sedan på **OK**.
   3. Vänta tills operationen är klar.

5. **Verifiera väntande export till** Azure AD:

   1. Högerklicka på **Azure AD Connector** och välj **Sök kopplingsutrymme**.
   2. I popup-dialogrutan **Sökkopplingsutrymme:**

      - Ange **scope** till **väntande export**.
      - Markera alla tre kryssrutor: **Lägg till,** **Ändra**och **Ta bort**.
      - Klicka på **sökknappen** om du vill få en lista över objekt med ändringar som ska exporteras. Om du vill undersöka ändringarna för ett visst objekt dubbelklickar du på objektet.
      - Kontrollera att ändringarna förväntas.

6. Kör **export** på **Azure AD Connector:**

   1. Högerklicka på **Azure AD Connector** och välj **Kör**.
   2. Välj **Exportera** i popup-dialogrutan **Kör koppling** och klicka sedan på **OK**.
   3. Vänta tills exporten till Azure AD är klar.

> [!NOTE]
> De här stegen innehåller inte de fullständiga synkroniserings- och exportstegen på Azure AD Connector. Dessa steg krävs inte eftersom attributvärdena flödar från lokala Active Directory till Azure AD.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Steg 7: Återaktivera synkroniseringsschemat
Aktivera den inbyggda synkroniseringsschemat igen:

1. Starta en PowerShell-session.
2. Återaktivera schemalagd synkronisering genom att `Set-ADSyncScheduler -SyncCycleEnabled $true`köra cmdleten .


## <a name="next-steps"></a>Nästa steg
* Läs mer om konfigurationsmodellen i [Förstå deklarativa etablering .](concept-azure-ad-connect-sync-declarative-provisioning.md)
* Läs mer om uttrycksspråket i [Förstå deklarativa etableringsuttryck](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

**Avsnitt om översikt**

* [Synkronisering av Azure AD Connect: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
