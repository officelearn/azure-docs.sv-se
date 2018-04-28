---
title: 'Azure Active Directory Connect-synkronisering: Konfigurera önskad plats för flera Geo funktioner i Office 365 | Microsoft Docs'
description: Beskriver hur du lägger din Office 365 användarresurser nära användare med Azure Active Directory Connect-synkronisering.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: billmath
ms.openlocfilehash: 0020ed42baaa32fbc5ae2d62b37558e491842d67
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Azure Active Directory Connect-synkronisering: Konfigurera önskad plats för Office 365-resurser
Syftet med det här avsnittet är leder dig igenom hur du konfigurerar attributet för önskad plats i Azure Active Directory (AD Azure) Connect-synkronisering. När någon använder flera Geo funktionerna i Office 365 kan du använda det här attributet för att ange den geografiska platsen användardata Office 365. (Villkoren *region* och *geo* används synonymt.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Aktivera synkroniseringen av prioriterade Dataplats
Som standard finns Office 365-resurser för dina användare i samma geo som Azure AD-klienten. Till exempel om din klient finns i Nordamerika, finns sedan användarnas postlådor i Exchange också i Nordamerika. För en flerspråkig miljö kanske det inte är optimala.

Genom att ange attributet **preferredDataLocation**, kan du definiera en användares geo. Du kan ha användarens Office 365 resurser, till exempel postlåda och OneDrive, i samma geo som användare, och fortfarande har en klient för hela organisationen.

> [!IMPORTANT]
> Flera Geo är tillgänglig för kunder med minst 5 000 prenumerationer på Office 365-tjänster. Kontakta din Microsoft-representant för mer information.
>
>

En lista över alla regioner för Office 365 finns i [där är dina data finns?](https://aka.ms/datamaps).

Regioner i Office 365 för flera Geo är:

| Geografi | preferredDataLocation värde |
| --- | --- |
| Asien och stillahavsområdet | APC |
| Australien | AUSTRALIEN |
| Kanada | KAN |
| Europeiska unionen | EUR |
| Indien | IND |
| Japan | JPN |
| Korea | KOR |
| Storbritannien | GBR |
| USA | NAM |

* Om en geo inte visas i den här tabellen (till exempel Sydamerika) kan sedan den inte användas för flera Geo.
* Indien geo är endast tillgängligt för kunder med fakturerings-adress och licenser i det här geo.
* Inte alla Office 365-arbetsbelastningar stöder för att en användares geo.

### <a name="azure-ad-connect-support-for-synchronization"></a>Azure AD Connect-stöd för synkronisering

Azure AD Connect har stöd för synkronisering av den **preferredDataLocation** attribut för **användaren** objekt i version 1.1.524.0 och senare. Närmare bestämt:

* Schemat för objekttypen **användare** i Azure AD-koppling har utökats för att inkludera den **preferredDataLocation** attribut. Attributet är av typen, enstaka string.
* Schemat för objekttypen **Person** i metaversum utökas för att inkludera den **preferredDataLocation** attribut. Attributet är av typen, enstaka string.

Som standard **preferredDataLocation** har inte aktiverats för synkronisering. Den här funktionen är avsedd för stora organisationer. Du måste också identifiera ett attribut för Office 365 geo för användarna, eftersom det inte finns några **preferredDataLocation** attribut i lokala Active Directory. Detta kommer att vara olika för varje organisation.

> [!IMPORTANT]
> Azure AD kan den **preferredDataLocation** attributet för **molnet användarobjekt** konfigureras direkt med hjälp av Azure AD PowerShell. Azure AD kan inte längre den **preferredDataLocation** attributet för **synkroniseras användarobjekt** konfigureras direkt med hjälp av Azure AD PowerShell. Konfigurera det här attributet på **synkroniseras användarobjekt**, måste du använda Azure AD Connect.

Innan du aktiverar synkronisering:

* Bestäm vilka lokala Active Directory-attribut som ska användas som källattribut. Det bör vara av typen **enstaka sträng**. I de steg som följer en av de **extensionAttributes** används.
* Om du tidigare har konfigurerat den **preferredDataLocation** attribut i befintliga **synkroniseras användarobjekt** i Azure AD med hjälp av Azure AD PowerShell måste du backport attributet värden till den motsvarande **användaren** objekt i lokal Active Directory.

    > [!IMPORTANT]
    > Om du gör inte backport dessa värden, Azure AD Connect tar bort de befintliga attributvärdena i Azure AD när synkroniseringen för den **preferredDataLocation** aktiverat.

* Konfigurera nu källattributet på minst ett par med lokala Active Directory-användaren objekt. Du kan använda den för senare verifiering.

Följande avsnitt innehåller steg för att aktivera synkronisering av den **preferredDataLocation** attribut.

> [!NOTE]
> Steg som beskrivs i kontexten för en Azure AD-distribution med en skog topologi och utan anpassade Synkroniseringsregler. Om du har en topologi med flera skogar, anpassade Synkroniseringsregler konfigurerats eller har en fristående server, bör du anpassa stegen i enlighet med detta.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Steg 1: Inaktivera sync scheduler och kontrollera att det finns ingen synkronisering pågår
Se till att ingen synkronisering sker när du arbetar i mitten uppdaterar Synkroniseringsregler för att undvika oväntade ändringar exporteras till Azure AD. Inaktivera inbyggda sync scheduler:

1. Starta en PowerShell-session på Azure AD Connect-servern.
2. Inaktivera schemalagda synkroniseringen genom att köra denna cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Starta den **Synchronization Service Manager** genom att gå till **starta** > **synkroniseringstjänsten**.
4. Välj den **Operations** och bekräfta att ingen åtgärd med status *pågår*.

![Skärmbild av Synchronization Service Manager](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Steg 2: Lägg till källattributet schemat lokala Active Directory-koppling
Inte alla Azure AD-attribut har importerats till det lokala Active Directory-kopplingen utrymmet. Om du har valt för att använda ett attribut som inte synkroniseras som standard måste du importera den. Lägga till källattributet i listan över importerade attribut:

1. Välj den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
2. Högerklicka på den lokala Active Directory-koppling och välj **egenskaper**.
3. I dialogrutan popup går du till den **Välj attribut** fliken.
4. Kontrollera att du har valt för att använda källattributet är markerat i attributlistan. Om du inte ser ditt attribut, väljer du den **visa alla** kryssrutan.
5. Välj för att spara **OK**.

![Dialogrutan Egenskaper och Skärmbild av Synchronization Service Manager](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Steg 3: Lägg till **preferredDataLocation** schemat för Azure AD-koppling
Som standard den **preferredDataLocation** attributet har inte importerats till Azure AD anslutningsplatsen. Lägga till den i listan över importerade attribut:

1. Välj den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
2. Högerklicka på Azure AD-koppling och välj **egenskaper**.
3. I dialogrutan popup går du till den **Välj attribut** fliken.
4. Välj den **preferredDataLocation** attribut i listan.
5. Välj för att spara **OK**.

![Dialogrutan Egenskaper och Skärmbild av Synchronization Service Manager](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule"></a>Steg 4: Skapa en regel för inkommande synkronisering
Regel för inkommande synkronisering tillåter attributvärdet som ska flödas från källattributet i lokala Active Directory till metaversum.

1. Starta den **Synchronization regler Editor** genom att gå till **starta** > **Synchronization regler Editor**.
2. Ange sökfilter **riktning** ska **inkommande**.
3. Om du vill skapa en ny inkommande regel **Lägg till ny regel**.
4. Under den **beskrivning** fliken, ange följande konfiguration:

    | Attribut | Värde | Information |
    | --- | --- | --- |
    | Namn | *Ange ett namn* | Till exempel ”i från AD-användaren preferredDataLocation” |
    | Beskrivning | *Ange en egen beskrivning* |  |
    | Det anslutna systemet | *Välj den lokala Active Directory-kopplingen* |  |
    | Anslutna System objekttyp | **Användaren** |  |
    | Typ av Metaversumobjekt | **Person** |  |
    | Länktypen | **Anslut dig** |  |
    | Prioritet | *Välj ett tal mellan 1 – 99* | 1 – 99 är reserverat för synkronisering av anpassade regler. Välj inte ett värde som används av en annan regel för synkronisering. |

5. Behåll den **Scoping filter** tom ska innehålla alla objekt. Du kan behöva justera filtret målgrupp enligt din Azure AD Connect-distribution.
6. Gå till den **omvandling fliken**, och implementera följande omvandlingsregeln:

    | Flöde | Målattribut | Källa | Använda en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    |Direkt | PreferredDataLocation | Välj källattributet | Alternativet är avmarkerat | Uppdatering |

7. Om du vill skapa regel för inkommande trafik väljer **Lägg till**.

![Skärmbild av Skapa regel för inkommande synkronisering](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule"></a>Steg 5: Skapa en synkroniseringsregel för utgående
Utgående synkroniseringsregel tillåter attributvärdet som ska flödas från metaversumsökning till den **preferredDataLocation** attribut i Azure AD:

1. Gå till den **synkronisering regler Editor**.
2. Ange sökfilter **riktning** ska **utgående**.
3. Välj **Lägg till ny regel**.
4. Under den **beskrivning** fliken, ange följande konfiguration:

    | Attribut | Värde | Information |
    | ----- | ------ | --- |
    | Namn | *Ange ett namn* | Till exempel ”Out till Azure AD-användaren preferredDataLocation” |
    | Beskrivning | *Ange en beskrivning* ||
    | Det anslutna systemet | *Välj den Azure AD-koppling* ||
    | Anslutna System objekttyp | **Användaren** ||
    | Typ av Metaversumobjekt | **Person** ||
    | Länktypen | **Anslut dig** ||
    | Prioritet | *Välj ett tal mellan 1 – 99* | 1 – 99 är reserverat för synkronisering av anpassade regler. Välj inte ett värde som används av en annan regel för synkronisering. |

5. Gå till den **Scoping filter** fliken och Lägg till en målgrupp filter grupp med två klausuler:

    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | sourceObjectType | LIKA MED | Användare |
    | cloudMastered | NOTEQUAL | True |

    Målgrupp filter avgör vilka Azure AD-objekt den här utgående synkroniseringsregeln tillämpas på. I det här exemplet använder vi ”Out till AD-användaridentitet” samma målgrupp filtret OOB (out of box) synkroniseringsregeln. Det förhindrar att synkroniseringsregeln tillämpas på **användaren** objekt som inte är synkroniserade från lokala Active Directory. Du kan behöva justera filtret målgrupp enligt din Azure AD Connect-distribution.

6. Gå till den **omvandling** fliken och implementera följande omvandlingsregeln:

    | Flöde | Målattribut | Källa | Använda en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    | Direkt | PreferredDataLocation | PreferredDataLocation | Alternativet är avmarkerat | Uppdatering |

7. Stäng **Lägg till** att skapa regel för utgående trafik.

![Skärmbild av Skapa regel för utgående synkronisering](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>Steg 6: Kör en fullständig synkroniseringscykel
I allmänhet krävs fullständig synkroniseringscykel. Detta beror på att du har lagt till nya attribut till både Active Directory och Azure AD Connector schemat och introduceras anpassade Synkroniseringsregler. Bekräfta ändringarna innan du exporterar dem till Azure AD. Du kan använda följande steg för att verifiera ändringarna, när du kör de steg som utgör en fullständig synkroniseringscykel manuellt.

1. Kör **fullständig import** på den lokala Active Directory-kopplingen:

   1. Gå till den **Operations** fliken i hanteraren för synkroniseringstjänsten.
   2. Högerklicka på den **lokala Active Directory-kopplingen**, och välj **kör**.
   3. I dialogrutan Välj **fullständig Import**, och välj **OK**.
   4. Vänta tills åtgärden har slutförts.

    > [!NOTE]
    > Du kan hoppa över fullständig import på den lokala Active Directory-koppling om källattributet ingår redan i listan över importerade attribut. Du med andra ord inte göra några ändringar under steg 2 ovan.

2. Kör **fullständig import** Azure AD-anslutningen:

   1. Högerklicka på den **Azure AD Connector**, och välj **kör**.
   2. I dialogrutan Välj **fullständig Import**, och välj **OK**.
   3. Vänta tills åtgärden har slutförts.

3. Kontrollera synkronisering regeländringar på en befintlig **användaren** objekt.

   Källattributet från lokala Active Directory, och **preferredDataLocation** från Azure AD har importerats till varje respektive anslutningsplatsen. Innan du fortsätter med fullständig synkronisering steg, gör du en förhandsgranskning på en befintlig **användaren** objekt i det lokala Active Directory-kopplingen utrymmet. Det objekt du valt bör ha källattributet fylls i. En lyckad förhandsgranskning med **preferredDataLocation** i metaversum är en bra indikator som du har konfigurerat synkronisering regler på rätt sätt. Information om hur du gör en förhandsgranskning finns [bekräfta ändringen](active-directory-aadconnectsync-change-the-configuration.md#verify-the-change).

4. Kör **fullständig synkronisering** på den lokala Active Directory-kopplingen:

   1. Högerklicka på den **lokala Active Directory-kopplingen**, och välj **kör**.
   2. I dialogrutan Välj **fullständig synkronisering**, och välj **OK**.
   3. Vänta tills åtgärden har slutförts.

5. Kontrollera **väntande exporter** till Azure AD:

   1. Högerklicka på den **Azure AD Connector**, och välj **söka Anslutarplats**.
   2. I den **söka Anslutarplats** dialogrutan:

        a. Ange **omfång** till **väntande Export**.<br>
        b. Markera alla tre kryssrutor, inklusive **lägga till, ändra och ta bort**.<br>
        c. Om du vill visa en lista med objekt med ändringar exporteras **Sök**. Dubbelklicka på objektet om du vill granska ändringarna för ett angivet objekt.<br>
        d. Kontrollera att ändringarna är förväntat.

6. Kör **exportera** på den **Azure AD-koppling**

   1. Högerklicka på den **Azure AD Connector**, och välj **kör**.
   2. I den **kör Connector** dialogrutan **exportera**, och välj **OK**.
   3. Vänta tills åtgärden har slutförts.

> [!NOTE]
> Du kan se steg som inte inkluderar steget fullständig synkronisering i Azure AD Connector eller export steg på den Active Directory-koppling. Steg som krävs inte, eftersom attributvärdena som flödar in från lokala Active Directory till Azure AD.

## <a name="step-7-re-enable-sync-scheduler"></a>Steg 7: Återaktivera sync scheduler
Återaktivera inbyggda sync scheduler:

1. Starta en PowerShell-session.
2. Aktivera schemalagd synkronisering igen genom att köra denna cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>Steg 8: Kontrollera resultatet
Det är nu tid att verifiera konfigurationen och aktivera den för användarna.

1. Lägg till geografiska i det valda attributet för en användare. Listan över tillgängliga regioner finns i [tabellen](#enable-synchronization-of-preferreddatalocation).  
![Skärmbild av AD-attribut som läggs till en användare](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Vänta på attributet som ska synkroniseras till Azure AD.
3. Använder Exchange Online PowerShell, kontrollera att den postlåda regionen har ställts in korrekt.  
![Skärmbild av Exchange Online PowerShell](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Under förutsättning att din klient har markerats för att kunna använda den här funktionen, flyttas postlådan till rätt geo. Detta kan kontrolleras genom att titta på namnet på server där postlådan finns.
4. Om du vill verifiera att den här inställningen har effektiva över många postlådor, använder du skriptet i den [TechNet-galleriet](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e). Det här skriptet har också en lista över de server-prefix för alla Office 365-datacenter och vilka geo den finns i. Den kan användas som en referens i föregående steg för att kontrollera platsen för postlådan.

## <a name="next-steps"></a>Nästa steg

Läs mer om flera Geo i Office 365:

* [Flera Geo sessioner på Ignite](https://aka.ms/MultiGeoIgnite)
* [Flera Geo i OneDrive](https://aka.ms/OneDriveMultiGeo)
* [Flera Geo i SharePoint Online](https://aka.ms/SharePointMultiGeo)

Läs mer om Konfigurationsmodell i Synkroniseringsmotorn:

* Läs mer om Konfigurationsmodell i [förstå deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Läs mer om Uttrycksspråk i [förstå uttryck för deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

Översikt översiktsavsnitt:

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
