---
title: 'Azure Active Directory Connect-synkronisering: Konfigurera önskad Dataplats för Multi-Geo-funktioner i Office 365 | Microsoft Docs'
description: Beskriver hur du lägger till dina resurser för användare av Office 365 nära användaren med Azure Active Directory Connect-synkronisering.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 38bb0840269d4b7bacbb0101590b389c8542a9b1
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814239"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Azure Active Directory Connect-synkronisering: Konfigurera önskad Dataplats för Office 365-resurser
Syftet med det här avsnittet är att vägleder dig genom hur du konfigurerar attributet för önskad Dataplats i Azure Active Directory (Azure AD) Connect-synkronisering. När någon använder Multi-Geo-funktioner i Office 365 använder du det här attributet för att ange geografiska av användarens Office 365-data. (Villkoren *region* och *geo* används synonymt.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Aktivera synkronisering av önskad Dataplats
Som standard finns Office 365-resurser för dina användare i samma geografiska område som Azure AD-klienten. Exempel: om din klient finns i Nordamerika, sedan användarnas Exchange-postlådor finns också i Nordamerika. För en flerspråkig miljö kan det vara optimala.

Genom att ange attributet **preferredDataLocation**, kan du definiera en användares geo. Du kan ha användarens Office 365 resurser, till exempel postlåda och OneDrive, i samma geografiska område som användare, och fortfarande har en klient för hela organisationen.

> [!IMPORTANT]
> Hjälpdokumentation för flera geografiska är för närvarande tillgängligt för kunder med minst 2 500 prenumerationer för Office 365-tjänster. Kontakta din Microsoft-representant för mer information.
>
>

En lista över alla regioner för Office 365 finns i [var är dina data finns?](https://aka.ms/datamaps).

Geografiska områden i Office 365 som är tillgängliga för flera Geo är:

| Geografi | preferredDataLocation värde |
| --- | --- |
| Asien och stillahavsområdet | APC |
| Australien | AU: ER |
| Kanada | CAN |
| Europeiska unionen | EUR |
| Frankrike | FRA |
| Indien | IND |
| Japan | JPN |
| Korea | KOR |
| Storbritannien | GBR |
| USA | NAM |

* Om ett geografiskt område inte visas i den här tabellen (till exempel Sydamerika) kan sedan den inte användas för Multi-GEO-replikering.

* Inte alla Office 365-arbetsbelastningar stöd för användning av att ställa in en användares geo.

### <a name="azure-ad-connect-support-for-synchronization"></a>Support för Azure AD Connect för synkronisering

Azure AD Connect stöder synkronisering av den **preferredDataLocation** attributet för **användaren** objekt i version 1.1.524.0 och senare. Närmare bestämt:

* Schemat för objekttypen **användaren** i Azure AD Connector utökas för att inkludera den **preferredDataLocation** attribut. Attributet är av typen, enstaka string.
* Schemat för objekttypen **Person** i metaversum utökas för att inkludera den **preferredDataLocation** attribut. Attributet är av typen, enstaka string.

Som standard **preferredDataLocation** har inte aktiverats för synkronisering. Den här funktionen är avsedd för stora organisationer. Du måste även identifiera ett attribut för att lagra Office 365 geografiskt område för dina användare, eftersom det finns inga **preferredDataLocation** attribut i den lokala Active Directory. Detta kommer att vara olika för varje organisation.

> [!IMPORTANT]
> Azure AD kan den **preferredDataLocation** attributet **användarobjekt i molnet** konfigureras direkt med hjälp av Azure AD PowerShell. Azure AD tillåter inte längre den **preferredDataLocation** attributet **synkroniseras användarobjekt** konfigureras direkt med hjälp av Azure AD PowerShell. Konfigurera det här attributet på **synkroniseras användarobjekt**, måste du använda Azure AD Connect.

Innan du aktiverar synkronisering:

* Bestäm vilka lokala Active Directory-attributet som ska användas som källattributet. Det bör vara av typen **enkelvärdesattribut sträng**. I de steg som följer någon av de **extensionAttributes** används.
* Om du tidigare har konfigurerat den **preferredDataLocation** attributet på befintliga **synkroniseras användarobjekt** i Azure AD med hjälp av Azure AD PowerShell måste du backport attributvärden till den motsvarande **användaren** objekt i den lokala Active Directory.

    > [!IMPORTANT]
    > Om du gör inte backport dessa värden kan Azure AD Connect tar bort de befintliga attributvärdena i Azure AD när synkronisering för de **preferredDataLocation** aktiverat.

* Konfigurera attribut för datakälla på minst ett par med en lokal Active Directory-användare objekt nu. Du kan använda detta för att bekräfta senare.

Följande avsnitt innehåller stegen för att aktivera synkronisering av den **preferredDataLocation** attribut.

> [!NOTE]
> Stegen beskrivs i samband med en Azure AD-distribution med topologi med en enda skog och utan anpassade Synkroniseringsregler. Om du har en topologi med flera skogar, anpassade Synkroniseringsregler konfigurerats eller har en fristående server, du bör anpassa stegen i enlighet med detta.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Steg 1: Inaktivera sync scheduler och verifiera det finns ingen synkronisering pågår
Se till att ingen synkronisering sker när du är mitt uppdaterar Synkroniseringsregler för att undvika oväntade ändringar exporteras till Azure AD. Inaktivera inbyggda sync scheduler:

1. Starta en PowerShell-session på Azure AD Connect-servern.
2. Inaktivera schemalagd synkronisering genom att köra denna cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Starta den **hanteraren för synkroniseringstjänsten** genom att gå till **starta** > **synkroniseringstjänsten**.
4. Välj den **Operations** fliken och bekräfta att ingen åtgärd med statusen *pågår*.

![Skärmbild av hanteraren för synkroniseringstjänsten](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Steg 2: Lägga till källattributet i den lokala Active Directory-koppling schemat
Inte alla Azure AD-attribut har importerats till det lokala Active Directory-koppling utrymmet. Om du har valt för att använda ett attribut som inte synkroniseras som standard, måste du importera den. Lägga till källattributet i listan över importerade attribut:

1. Välj den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
2. Högerklicka på den lokala Active Directory-koppling och välj **egenskaper**.
3. I popup-rutan, gå till den **Välj attribut** fliken.
4. Kontrollera att du har valt för att använda källattributet är markerat i attributlistan. Om du inte ser din attributet väljer du den **visa alla** markerar du kryssrutan.
5. Välj för att spara **OK**.

![Dialogrutan Egenskaper och Skärmbild av hanteraren för synkroniseringstjänsten](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Steg 3: Lägg till **preferredDataLocation** i Azure AD Connector-schemat
Som standard den **preferredDataLocation** attributet har inte importerats till Azure AD-anslutningsplatsen. Lägga till den i listan över importerade attribut:

1. Välj den **kopplingar** fliken i hanteraren för synkroniseringstjänsten.
2. Högerklicka på Azure AD-anslutningsappen och välj **egenskaper**.
3. I popup-rutan, gå till den **Välj attribut** fliken.
4. Välj den **preferredDataLocation** attribut i listan.
5. Välj för att spara **OK**.

![Dialogrutan Egenskaper och Skärmbild av hanteraren för synkroniseringstjänsten](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule"></a>Steg 4: Skapa en regel för inkommande synkronisering
Regel för inkommande synkronisering tillåter attributvärdet som flödar från källattributet i den lokala Active Directory till metaversum.

1. Starta den **Synchronization Rules Editor** genom att gå till **starta** > **Synchronization Rules Editor**.
2. Ange sökfiltret **riktning** vara **inkommande**.
3. Om du vill skapa en ny inkommande regel, Välj **Lägg till ny regel**.
4. Under den **beskrivning** fliken tillhandahåller följande konfiguration:

    | Attribut | Värde | Information |
    | --- | --- | --- |
    | Namn | *Ange ett namn* | Till exempel ”i från AD – användaren preferredDataLocation” |
    | Beskrivning | *Ange en anpassad beskrivning* |  |
    | Anslutna System | *Välj en lokal Active Directory-koppling* |  |
    | Anslutna System objekttyp | **Användaren** |  |
    | Typ av Metaversumobjekt | **Person** |  |
    | Länktyp | **Anslut dig** |  |
    | Prioritet | *Välj ett tal mellan 1 – 99* | 1 – 99 är reserverad för av anpassade Synkroniseringsregler. Välj inte ett värde som används av andra Synkroniseringsregler. |

5. Behåll den **Scoping filter** tom ska innehålla alla objekt. Du kan behöva justera Omfångsfilter enligt din Azure AD Connect-distribution.
6. Gå till den **omvandling fliken**, och implementera följande omvandlingsregeln:

    | Flow-typ | Målattribut | Källa | Använda en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    |Direkt | preferredDataLocation | Välj attribut för datakälla | Alternativet är avmarkerat | Uppdatering |

7. Om du vill skapa den inkommande regeln, Välj **Lägg till**.

![Skärmbild av Skapa regel för inkommande synkronisering](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule"></a>Steg 5: Skapa en regel för utgående synkronisering
Den utgående synkroniseringsregeln tillåter attributvärdet som flödar från metaversum till den **preferredDataLocation** attribut i Azure AD:

1. Gå till den **synkronisering regler redigeraren**.
2. Ange sökfiltret **riktning** vara **utgående**.
3. Välj **Lägg till ny regel**.
4. Under den **beskrivning** fliken tillhandahåller följande konfiguration:

    | Attribut | Värde | Information |
    | ----- | ------ | --- |
    | Namn | *Ange ett namn* | Till exempel ”ut till Azure AD – användaren preferredDataLocation” |
    | Beskrivning | *Ange en beskrivning* ||
    | Anslutna System | *Välj den Azure AD-koppling* ||
    | Anslutna System objekttyp | **Användaren** ||
    | Typ av Metaversumobjekt | **Person** ||
    | Länktyp | **Anslut dig** ||
    | Prioritet | *Välj ett tal mellan 1 – 99* | 1 – 99 är reserverad för av anpassade Synkroniseringsregler. Välj inte ett värde som används av andra Synkroniseringsregler. |

5. Gå till den **Scoping filter** fliken och lägga till en enda gemensam Filtergrupp med två satser:

    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Användare |
    | cloudMastered | NOTEQUAL | True |

    Omfångsfilter avgör vilka objekt som den här utgående synkroniseringsregeln tillämpas på Azure AD. I det här exemplet använder vi samma Omfångsfilter från ”ut till AD – användaridentitet” synkroniseringsregel för OOB (out of box). Det förhindrar synkroniseringsregeln tillämpas **användaren** objekt som inte är synkroniserade från en lokal Active Directory. Du kan behöva justera Omfångsfilter enligt din Azure AD Connect-distribution.

6. Gå till den **omvandling** fliken och implementera följande omvandlingsregeln:

    | Flow-typ | Målattribut | Källa | Använda en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    | Direkt | preferredDataLocation | preferredDataLocation | Alternativet är avmarkerat | Uppdatering |

7. Stäng **Lägg till** att skapa regel för utgående trafik.

![Skärmbild av skapa utgående synkroniseringsregel](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>Steg 6: Kör fullständig synkroniseringscykel
I allmänhet krävs fullständig synkroniseringscykel. Detta är eftersom du har lagt till nya attribut till både Active Directory och Azure AD Connector schemat och introducerades anpassade Synkroniseringsregler. Bekräfta ändringarna innan du exporterar dem till Azure AD. Du kan använda följande steg för att verifiera ändringarna, samtidigt som du kör de steg som utgör en fullständig synkroniseringscykel manuellt.

1. Kör **fullständig import** på den lokala Active Directory-koppling:

   1. Gå till den **Operations** fliken i hanteraren för synkroniseringstjänsten.
   2. Högerklicka på den **lokala Active Directory-koppling**, och välj **kör**.
   3. I dialogrutan Välj **fullständig Import**, och välj **OK**.
   4. Vänta tills åtgärden har slutförts.

    > [!NOTE]
    > Du kan hoppa över fullständig import på en lokal Active Directory-koppling om källattributet ingår redan i listan över importerade attribut. Du med andra ord inte göra ändringar i steg 2 ovan.

2. Kör **fullständig import** i Azure AD Connector:

   1. Högerklicka på den **Azure AD Connector**, och välj **kör**.
   2. I dialogrutan Välj **fullständig Import**, och välj **OK**.
   3. Vänta tills åtgärden har slutförts.

3. Bekräfta synkronisering regeln ändringarna på en befintlig **användaren** objekt.

   Källattributet från en lokal Active Directory, och **preferredDataLocation** från Azure AD har importerats till varje respektive anslutningsplatsen. Innan du fortsätter med det fullständiga synkronisering steget, gör du en förhandsgranskning på en befintlig **användaren** objekt i det lokala Active Directory-koppling utrymmet. Det objekt du valt bör ha fyllts i källattributet. En förhandsversionen med **preferredDataLocation** ifylld i metaversum är en bra indikator som du har konfigurerat synkroniseringen regler korrekt. Information om hur du gör en förhandsgranskning finns i [bekräfta ändringen](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Kör **fullständig synkronisering** på den lokala Active Directory-koppling:

   1. Högerklicka på den **lokala Active Directory-koppling**, och välj **kör**.
   2. I dialogrutan Välj **fullständig synkronisering**, och välj **OK**.
   3. Vänta tills åtgärden har slutförts.

5. Kontrollera **väntande exporter** till Azure AD:

   1. Högerklicka på den **Azure AD Connector**, och välj **Search Connector Space**.
   2. I den **Search Connector Space** dialogrutan:

        a. Ange **omfång** till **väntande Export**.<br>
        b. Markera alla tre kryssrutorna, inklusive **lägga till, ändra och ta bort**.<br>
        c. Om du vill visa listan över objekt med ändringar som ska exporteras, Välj **Search**. Dubbelklicka på objektet om du vill kontrollera ändringarna för ett angivet objekt.<br>
        d. Kontrollera att ändringarna är förväntat.

6. Kör **exportera** på den **Azure AD-koppling**

   1. Högerklicka på den **Azure AD Connector**, och välj **kör**.
   2. I den **kör anslutningstjänsten** dialogrutan **exportera**, och välj **OK**.
   3. Vänta tills åtgärden har slutförts.

> [!NOTE]
> Du kanske märker att stegen inte omfattar fullständig synkronisering steg i Azure AD Connector eller export steg på den Active Directory-koppling. Stegen är inte krävs, eftersom attributvärdena som flödar in från en lokal Active Directory till Azure AD.

## <a name="step-7-re-enable-sync-scheduler"></a>Steg 7: Återaktivera synkroniseringsschemaläggaren
Återaktivera inbyggda sync scheduler:

1. Starta en PowerShell-session.
2. Aktivera schemalagd synkronisering igen genom att köra denna cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>Steg 8: Kontrollera resultatet
Nu är det dags att kontrollera konfigurationen och aktivera den för användarna.

1. Lägga till geografiskt till det valda attributet på en användare. Listan över tillgängliga geografiska områden kan hittas i den här tabellen.  
![Skärmbild av AD-attribut som läggs till en användare](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Vänta tills attributet som ska synkroniseras till Azure AD.
3. Med Exchange Online PowerShell, kontrollera att regionen postlåda har ställts in korrekt.  
![Skärmbild av Exchange Online PowerShell](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Om vi antar att din klientorganisation har markerats för att kunna använda den här funktionen, flyttas postlådan till rätt geo. Detta kan verifieras genom att titta på namnet på servern där postlådan finns.
4. För att kontrollera att den här inställningen har effektiva över många postlådor, använder du skriptet i den [TechNet-galleriet](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e). Det här skriptet har också en lista över server-prefix för alla Office 365-datacenter och vilka geo är placerad i. Det kan användas som referens i föregående steg för att kontrollera platsen för postlådan.

## <a name="next-steps"></a>Nästa steg

Läs mer om flera Geo i Office 365:

* [Hjälpdokumentation för flera geografiska sessioner på Ignite](https://aka.ms/MultiGeoIgnite)
* [Hjälpdokumentation för flera geografiska i OneDrive](https://aka.ms/OneDriveMultiGeo)
* [Hjälpdokumentation för flera geografiska i SharePoint Online](https://aka.ms/SharePointMultiGeo)

Läs mer om konfigurationsmodellen i Synkroniseringsmotorn:

* Läs mer om konfigurationsmodellen i [förstå deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Läs mer om Uttrycksspråk i [förstå uttryck för deklarativ etablering](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Översikt över ämnen:

* [Azure AD Connect-synkronisering: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
