---
title: "Azure AD Connect-synkronisering: Konfigurera önskad plats för flera Geo funktioner i Office 365 | Microsoft Docs"
description: "Beskriver hur du lägger din Office 365 användarresurser nära användare med Azure AD Connect-synkronisering."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: billmath
ms.openlocfilehash: 021f009e66e57665a2252646b210f0e6dc55d33c
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="azure-ad-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Azure AD Connect-synkronisering: Konfigurera önskad plats för Office 365-resurser
Syftet med det här avsnittet är leder dig igenom hur du konfigurerar PreferredDataLocation i Azure AD Connect Sync. När en kund använder flera Geo funktionerna i Office 365, används det här attributet för att ange den geografiska platsen användardata Office 365. Villkoren **region** och **Geo** används utbytbara.

> [!IMPORTANT]
> Flera Geo är för närvarande under förhandsgranskning. Kontakta din Microsoft-representant om du vill delta i förhandsgranskningsprogrammet.
>
>

## <a name="enable-synchronization-of-preferreddatalocation"></a>Aktivera synkroniseringen av PreferredDataLocation
Som standard finns Office 365-resurser för dina användare i samma geo som Azure AD-klienten. Till exempel om din klient finns i Nordamerika finns sedan Exchange-postlådor användare också i Nordamerika. För en organisation med flera nationella kanske detta inte optimalt. Genom att ange attributet preferredDataLocation kan användarens geo definieras.

Med inställningarna för det här attributet kan du ha användarens Office 365 resurser, till exempel postlåda och OneDrive, i samma geo som användaren och fortfarande har en klient för hela organisationen.

> [!IMPORTANT]
> För att få flera Geo, måste du ha minst 5000 platser i din prenumeration på Office 365
>
>

En lista över alla regioner för Office 365 finns i [där är dina data finns](https://aka.ms/datamaps).

Regioner i Office 365 för flera Geo är:

| Geografi | preferredDataLocation värde |
| --- | --- |
| Asien och stillahavsområdet | APC |
| Australien | AUS |
| Kanada | CAN |
| Europeiska unionen | EUR |
| Indien | IND |
| Japan | JPN |
| Sydkorea | KOR |
| Storbritannien | GBR |
| USA | NAM |

* Om en geo inte visas i den här tabellen, till exempel Sydamerika, kan sedan den inte användas för flera Geo.
* Indien och Sydkorea regioner är bara tillgängligt för kunder med fakturerings-adresser och licenser i dessa regioner.
* Inte alla Office 365-arbetsbelastningar kan använda för att en användares geo.

Azure AD Connect har stöd för synkronisering av den **PreferredDataLocation** attribut för **användaren** objekt i version 1.1.524.0 och efter. Följande ändringar har införts mer specifikt:

* Schemat för objekttypen **användaren** i Azure AD-koppling har utökats för att inkludera PreferredDataLocation attribut, som är av typen enstaka string.
* Schemat för objekttypen **Person** i metaversum utökas för att inkludera PreferredDataLocation attribut som är av typen string och enkelvärdesattribut.

Som standard aktiveras inte attributet PreferredDataLocation för synkronisering. Den här funktionen är avsedd för större organisationer och inte alla skulle dra nytta av den. Du måste också identifiera ett attribut för Office 365 geo för dina användare eftersom det inte finns några PreferredDataLocation attribut i lokala Active Directory. Detta kommer att vara olika för varje organisation.

> [!IMPORTANT]
> Azure AD kan för närvarande attributet PreferredDataLocation på både synkroniserade objekt och i molnet användaren objekt för att vara direkt konfigureras med Azure AD PowerShell. När du har aktiverat synkronisering av attributet PreferredDataLocation, måste du sluta använda Azure AD PowerShell för att konfigurera attributet på **synkroniseras användarobjekt** som Azure AD Connect åsidosätter dem baserat på käll-attributvärden i lokala Active Directory.

> [!IMPORTANT]
> Sedan den 1 September 2017 Azure AD inte längre kan attributet PreferredDataLocation på **synkroniseras användarobjekt** konfigureras direkt med hjälp av Azure AD PowerShell. Om du vill konfigurera PreferredLocation attribut synkroniserade objekt, måste du använda Azure AD Connect.

Innan du aktiverar synkronisering av attributet PreferredDataLocation, måste du:

* Bestäm vilka lokala Active Directory-attribut som ska användas som källattributet först. Det ska vara av typen **enstaka sträng**. I stegen nedan en av extensionAttributes används.
* Om du tidigare har konfigurerat attributet PreferredDataLocation på befintliga synkroniseras användarobjekt i Azure AD med hjälp av Azure AD PowerShell, måste du **backport** attributvärden till motsvarande användarobjekt i lokala Active Directory.

    > [!IMPORTANT]
    > Om du gör inte backport attributvärden till motsvarande användarobjekt i lokala Active Directory, tar Azure AD Connect bort de befintliga attributvärdena i Azure AD när synkronisering för attributet PreferredDataLocation har aktiverats.

* Bör du konfigurera källattributet på minst några lokala AD-användare objekt nu, som kan användas för verifiering senare.

Stegen för att aktivera synkronisering för attributet PreferredDataLocation kan sammanfattas som:

1. Inaktivera sync scheduler och kontrollera att det finns ingen synkronisering pågår
2. Lägga till källattributet lokalt ADDS connector schema
3. Lägga till PreferredDataLocation i Azure AD connector schema
4. Skapa en regel för inkommande synkronisering för att flöda attributvärdet från lokala Active Directory
5. Skapa en synkroniseringsregel för utgående för att flöda attributvärdet till Azure AD
6. Kör fullständig synkroniseringscykel
7. Aktivera sync scheduler
8. Kontrollera resultatet

> [!NOTE]
> Resten av det här avsnittet beskriver de här stegen i information. De beskrivs i kontexten för en Azure AD-distribution med en skog topologi och utan anpassade Synkroniseringsregler. Om du har en topologi med flera skogar, anpassade Synkroniseringsregler konfigurerats eller har en fristående server, måste du anpassa stegen i enlighet med detta.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Steg 1: Inaktivera sync scheduler och kontrollera att det finns ingen synkronisering pågår
Kontrollera sker ingen synkronisering när du arbetar på att uppdatera Synkroniseringsregler för att undvika oväntade ändringar exporteras till Azure AD. Inaktivera inbyggda sync scheduler:

1. Starta en PowerShell-session på Azure AD Connect-servern.
2. Inaktivera schemalagda synkroniseringen genom att köra cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Starta den **Synchronization Service Manager** genom att gå till **starta** > **synkroniseringstjänsten**.
4. Gå till den **Operations** fliken och bekräfta att ingen åtgärd med status *pågår*.

![Synchronization Service Manager - Kontrollera att inga åtgärder pågår](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema"></a>Steg 2: Lägga till källattributet i lokala ADDS connector schema
Inte alla AD-attribut har importerats till lokalt AD-anslutningsplatsen. Om du har valt för att använda ett attribut som synkroniseras inte som standard måste du importera den. Lägga till källattributet i listan över importerade attribut:

1. Gå till den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
2. Högerklicka på den **lokala AD-koppling** och välj **egenskaper**.
3. I popup-fönstret, går du till den **Välj attribut** fliken.
4. Kontrollera att du har valt för att använda källattributet är markerat i attributlistan. Klicka på kryssrutan ”Visa alla” om du inte ser ditt attribut.
5. Klicka på **OK** att spara.

![Lägg till källattributet i lokala AD-koppling schema](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Steg 3: Lägga till PreferredDataLocation i Azure AD connector schema
Som standard importeras inte attributet PreferredDataLocation till Azure AD-anslutningsplatsen. Lägga till attributet PreferredDataLocation i listan över importerade attribut:

1. Gå till den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
2. Högerklicka på den **Azure AD-koppling** och välj **egenskaper**.
3. I popup-fönstret, går du till den **Välj attribut** fliken.
4. Välj preferredDataLocation-attribut i attributlistan.
5. Klicka på **OK** att spara.

![Lägga till källattributet Azure AD Connector-schema](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Steg 4: Skapa en regel för inkommande synkronisering för att flöda attributvärdet från lokala Active Directory
Regel för inkommande synkronisering tillåter attributvärdet som ska flödas från källattributet från lokala Active Directory för metaversum:

1. Starta den **Synchronization regler Editor** genom att gå till **starta** > **Synchronization regler Editor**.
2. Ange sökfilter **riktning** ska **inkommande**.
3. Klicka på **Lägg till ny regel** för att skapa en ny inkommande regel.
4. Under den **beskrivning** fliken, ange följande konfiguration:

    | Attribut | Värde | Information |
    | --- | --- | --- |
    | Namn | *Ange ett namn* | Till exempel *”i från AD-användaren PreferredDataLocation”* |
    | Beskrivning | *Ange en egen beskrivning* |  |
    | Det anslutna systemet | *Välj lokalt AD-koppling* |  |
    | Anslutna System objekttyp | **Användaren** |  |
    | Typ av Metaversumobjekt | **Person** |  |
    | Länktypen | **Anslut dig** |  |
    | Prioritet | *Välj ett tal mellan 1 – 99* | 1 – 99 är reserverat för synkronisering av anpassade regler. Välj inte ett värde som används av en annan regel för synkronisering. |

5. Behåll den **Scoping filter** tom ska innehålla alla objekt. Du kan behöva justera filtret målgrupp enligt din Azure AD Connect-distribution.
6. Gå till den **omvandling fliken** och implementera följande omvandlingsregeln:

    | Flöde | Målattribut | Källa | Använda en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    |Direkt | PreferredDataLocation | Välj källattributet | Alternativet är avmarkerat | Uppdatering |

7. Klicka på **Lägg till** att skapa regel för inkommande trafik.

![Skapa regel för inkommande synkronisering](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Steg 5: Skapa en regel för utgående synkronisering för att flöda attributvärdet till Azure AD
Utgående synkroniseringsregel tillåter attributvärdet som ska flödas från Metaversumsökning till attributet PreferredDataLocation i Azure AD:

1. Gå till den **Synkroniseringsregler** Editor.
2. Ange sökfilter **riktning** ska **utgående**.
3. Klicka på **Lägg till ny regel** knappen.
4. Under den **beskrivning** fliken, ange följande konfiguration:

    | Attribut | Värde | Information |
    | ----- | ------ | --- |
    | Namn | *Ange ett namn* | Till exempel ”ut till AAD – användaren PreferredDataLocation” |
    | Beskrivning | *Ange en beskrivning* ||
    | Det anslutna systemet | *Välj den AAD-kopplingen* ||
    | Anslutna System objekttyp | Användare ||
    | Typ av Metaversumobjekt | **Person** ||
    | Länktypen | **Anslut dig** ||
    | Prioritet | *Välj ett tal mellan 1 – 99* | 1 – 99 är reserverat för synkronisering av anpassade regler. Välj inte ett värde som används av en annan regel för synkronisering. |

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

![Skapa regel för utgående synkronisering](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>Steg 6: Kör fullständig synkroniseringscykel
I allmänhet fullständig synkroniseringscykel krävs eftersom vi har lagt till nya attribut i både AD och Azure AD Connector schemat och introducerades anpassade Synkroniseringsregler. Vi rekommenderar att du kontrollerar ändringarna innan du exporterar dem till Azure AD. Du kan använda följande steg för att bekräfta ändringarna när du kör de steg som utgör en fullständig synkroniseringscykel manuellt.

1. Kör **fullständig import** steg på den **lokala AD-koppling**:

   1. Gå till den **Operations** fliken i hanteraren för synkroniseringstjänsten.
   2. Högerklicka på den **lokala AD-koppling** och välj **kör...** .
   3. I popup-fönstret väljer **fullständig Import** och på **OK**.
   4. Vänta tills åtgärden har slutförts.

    > [!NOTE]
    > Du kan hoppa över fullständig Import på lokalt AD-koppling om källattributet ingår redan i listan över importerade attribut. Med andra ord du inte behöver göra några ändringar under [steg 2: Lägg till källattributet för lokalt AD-koppling schemat](#step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema).

2. Kör **fullständig import** steg på den **Azure AD Connector**:

   1. Högerklicka på den **Azure AD Connector** och välj **kör...**
   2. I popup-fönstret väljer **fullständig Import** och på **OK**.
   3. Vänta tills åtgärden har slutförts.

3. Kontrollera synkronisering regeländringar på ett befintligt användarobjekt:

Källattribut från en lokal Active Directory och PreferredDataLocation från Azure AD har importerats till respektive anslutningsplatsen. Innan du fortsätter med fullständig synkronisering steg, bör du göra en **Preview** på en befintlig användares objekt i lokalt AD-anslutningsplatsen. Det objekt du valt bör ha källattributet fylls i. En lyckad **Preview** med PreferredDataLocation i metaversum är en bra indikator som du har konfigurerat synkronisering regler på rätt sätt. Information om hur du gör en **Preview**, läser du avsnittet [bekräfta ändringen](active-directory-aadconnectsync-change-the-configuration.md#verify-the-change).

4. Kör **fullständig synkronisering** steg på den **lokala AD-koppling**:

   1. Högerklicka på den **lokala AD-koppling** och välj **kör...** .
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

   1. Högerklicka på den **Azure AD Connector** och välj **kör...** .
   2. Välj i dialogrutan Kör Connector popup- **exportera** och på **OK**.
   3. Vänta tills exporten till Azure AD för att slutföra.

> [!NOTE]
> Det kan hända i stegen inte omfattar steget fullständig synkronisering i Azure AD Connector och exportera AD-anslutningen. Steg som krävs inte eftersom attributvärdena som flödar in från lokala Active Directory till Azure AD.

## <a name="step-7-re-enable-sync-scheduler"></a>Steg 7: Återaktivera sync scheduler
Återaktivera inbyggda sync scheduler:

1. Starta PowerShell-session.
2. Aktivera schemalagd synkronisering igen genom att köra cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>Steg 8: Kontrollera resultatet
Det är nu tid att verifiera konfigurationen och aktivera den för användarna.

1. Lägg till geografiska i det valda attributet för en användare. Listan över tillgängliga geo finns i [tabellen](#enable-synchronization-of-preferreddatalocation).  
![AD-attribut som läggs till en användare](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Vänta på attributet som ska synkroniseras till Azure AD.
3. Använder Exchange Online PowerShell, kontrollera att den postlåda regionen har ställts in korrekt.  
![Postlåda region som angetts för en användare i Exchange Online](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Under förutsättning att din klient har markerats för att kunna använda den här funktionen, flyttas postlådan till rätt geo. Detta kan kontrolleras genom att titta på namnet på server där postlådan finns.
4. Om du vill verifiera att den här inställningen har effektiva över många postlådor, använder du skriptet i den [Technet-galleriet](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e). Det här skriptet har också en lista över alla Office 365 Datacenter server prefix och vilka geo-replikering finns i. Den kan användas som en referens i föregående steg för att kontrollera platsen för postlådan.

## <a name="next-steps"></a>Nästa steg

**Läs mer om flera Geo i Office 365:**

* Flera Geo sessioner på Ignite: https://aka.ms/MultiGeoIgnite
* Flera Geo i OneDrive: https://aka.ms/OneDriveMultiGeo
* Flera Geo i SharePoint Online: https://aka.ms/SharePointMultiGeo

**Läs mer om Konfigurationsmodell i Synkroniseringsmotorn:**

* Läs mer om Konfigurationsmodell i [förstå deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Läs mer om Uttrycksspråk i [förstå uttryck för deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Översiktsavsnitt**

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
