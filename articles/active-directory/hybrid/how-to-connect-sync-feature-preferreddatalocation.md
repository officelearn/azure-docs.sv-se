---
title: 'Azure AD Connect: Konfigurera önskad dataplats för Office 365-resurser'
description: I artikeln beskrivs hur du placerar dina Office 365-användarresurser nära användaren med Azure Active Directory Connect-synkronisering.
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
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a71c5328c6fa85f85db4bd7e6103f6470b86d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258336"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Synkronisering av Azure Active Directory Connect: Konfigurera önskad dataplats för Office 365-resurser
Syftet med det här avsnittet är att gå igenom hur du konfigurerar attributet för önskad dataplats i Azure Active Directory (Azure AD) Connect-synkronisering. När någon använder Multi-Geo-funktioner i Office 365 använder du det här attributet för att ange geografisk plats för användarens Office 365-data. (Termerna *region* och *geo* används omväxlande.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Aktivera synkronisering av önskad dataplats
Som standard finns Office 365-resurser för användarna i samma geo som din Azure AD-klientorganisation. Om din klient finns i Nordamerika finns till exempel även användarnas Exchange-postlådor i Nordamerika. För en multinationell organisation kanske detta inte är optimalt.

Genom att ange **attributet preferredDataLocation**kan du definiera en användares geo. Du kan ha användarens Office 365-resurser, till exempel postlådan och OneDrive, i samma geo som användaren och fortfarande ha en klient för hela organisationen.

> [!IMPORTANT]
> Multi-Geo är för närvarande tillgängligt för kunder med ett aktivt Enterprise-avtal och minst 500 Office 365-tjänster.Multi-Geo is currently available to customers with an active Enterprise Agreement and a minimum of 500 Office 365 Services subscriptions. Kontakta din Microsoft-representant för mer information.
>
>

En lista över alla geos för Office 365 finns i [Var finns dina data?](https://aka.ms/datamaps).

Geos i Office 365 som är tillgängliga för Multi-Geo är:

| Geografi | preferredDataLocation-värde |
| --- | --- |
| Asien och stillahavsområdet | Apc |
| Australien | Aus |
| Kanada | Kan |
| Europeiska unionen | EUR |
| Frankrike | FRA |
| Indien | IND |
| Japan | JPN |
| Korea | KOR |
| Sydafrika | ZAF (andra) |
| Förenade Arabemiraten | Är |
| Storbritannien | Gbr |
| USA | Nam |

* Om en geo inte finns med i den här tabellen (till exempel Sydamerika) kan den inte användas för Multi-Geo.

* Alla Office 365-arbetsbelastningar stöder inte användningen av att ange en användares geo.

### <a name="azure-ad-connect-support-for-synchronization"></a>Azure AD Connect-stöd för synkronisering

Azure AD Connect stöder synkronisering av **attributet PreferredDataLocation** för **användarobjekt** i version 1.1.524.0 och senare. Mer specifikt:

* Schemat för objekttypen **Användare** i Azure AD Connector utökas till att omfatta **attributet preferredDataLocation.** Attributet är av typen, envärdessträng.
* Schemat för objekttypen **Person** i metaversumet utökas till att omfatta **attributet PreferredDataLocation.** Attributet är av typen, envärdessträng.

Som standard är **preferredDataLocation** inte aktiverat för synkronisering. Den här funktionen är avsedd för större organisationer. Active Directory-schemat i Windows Server 2019 har ett attribut **msDS-preferredDataLocation** som du bör använda för detta ändamål. Om du inte har uppdaterat Active Directory-schemat och inte kan göra det måste du identifiera ett attribut som innehåller Office 365-geon för användarna. Detta kommer att vara olika för varje organisation.

> [!IMPORTANT]
> Azure AD gör att **attributet PreferredDataLocation** på **molnanvändarobjekt** kan konfigureras direkt med hjälp av Azure AD PowerShell. Om du vill konfigurera det här attributet på **synkroniserade användarobjekt**måste du använda Azure AD Connect.

Innan du aktiverar synkronisering:

* Om du inte har uppgraderat Active Directory-schemat till 2019 bestämmer du vilket lokalt Active Directory-attribut som ska användas som källattribut. Det bör vara av typen, **single-valued string**.
* Om du tidigare har konfigurerat **attributet preferredDataLocation** på befintliga **synkroniserade användarobjekt** i Azure AD med hjälp av Azure AD PowerShell, måste du bakåtportera attributvärdena till motsvarande **användarobjekt** i lokal Active Directory.

    > [!IMPORTANT]
    > Om du inte backport dessa värden, Azure AD Connect tar bort befintliga attributvärden i Azure AD när synkronisering för **önskadDataLocation** attribut är aktiverat.

* Konfigurera källattributet på minst ett par lokala Active Directory-användarobjekt nu. Du kan använda detta för verifiering senare.

Följande avsnitt innehåller stegen för att aktivera synkronisering av **attributet PreferredDataLocation.**

> [!NOTE]
> Stegen beskrivs i samband med en Azure AD-distribution med enskogstopologi och utan anpassade synkroniseringsregler. Om du har en topologi för flera skogar, anpassade synkroniseringsregler konfigurerade eller har en mellanlagringsserver bör du justera stegen därefter.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Steg 1: Inaktivera synkroniseringsschemaläggare och kontrollera att ingen synkronisering pågår
Om du vill undvika att oavsiktliga ändringar exporteras till Azure AD kontrollerar du att ingen synkronisering sker när du är mitt uppe i att uppdatera synkroniseringsregler. Så här inaktiverar du den inbyggda synkroniseringsschemat:

1. Starta en PowerShell-session på Azure AD Connect-servern.
2. Inaktivera schemalagd synkronisering genom att köra `Set-ADSyncScheduler -SyncCycleEnabled $false`den här cmdlet: .
3. Starta **synkroniseringstjänsthanteraren** genom att gå till > **START-synkroniseringstjänsten**. **START**
4. Välj fliken **Operationer** och bekräfta att det inte finns någon åtgärd med statusen *pågår*.

![Skärmbild av Synkroniseringstjänsthanteraren](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>Steg 2: Uppdatera schemat för Active Directory
Om du har uppdaterat Active Directory-schemat till 2019 och Connect installerades före schematillägget, har inte det uppdaterade schemat det uppdaterade schemat. Du måste sedan uppdatera schemat från guiden så att det visas i användargränssnittet.

1. Starta Azure AD Connect-guiden från skrivbordet.
2. Markera alternativet **Uppdatera katalogschema** och klicka på **Nästa**.
3. Ange dina Azure AD-autentiseringsuppgifter och klicka på **Nästa**.
4. Kontrollera att alla skogar är markerade på sidan **Uppdatera katalogschema** och klicka på **Nästa**.
5. När du är klar stänger du guiden.

![Skärmbild av Uppdatera katalogschema i Anslutningsguiden](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Steg 3: Lägga till källattributet i det lokala Active Directory Connector-schemat
**Det här steget behövs bara om du kör Connect version 1.3.21 eller äldre. Om du är på 1.4.18 eller nyare, hoppa sedan till steg 5.**  
Alla Azure AD-attribut importeras inte till det lokala Active Directory-anslutningsutrymmet. Om du har valt att använda ett attribut som inte är synkroniserat som standard måste du importera det. Så här lägger du till källattributet i listan över importerade attribut:

1. Välj fliken **Kopplingar** i Synkroniseringstjänsthanteraren.
2. Högerklicka på den lokala Active Directory Connector och välj **Egenskaper**.
3. Gå till fliken **Välj attribut** i popup-dialogrutan.
4. Kontrollera att det källattribut som du valde att använda är markerat i attributlistan. Om du inte ser attributet markerar du kryssrutan **Visa alla.**
5. Spara genom att välja **OK**.

![Skärmbild av dialogrutan Synkroniseringstjänsthanteraren och egenskaper](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Steg 4: Lägg till **preferredDataLocation** till Azure AD Connector-schemat
**Det här steget behövs bara om du kör Connect version 1.3.21 eller äldre. Om du är på 1.4.18 eller nyare, hoppa sedan till steg 5.**  
Som standard **importeras inte attributet PreferredDataLocation** till Azure AD Connector-utrymmet. Så här lägger du till den i listan över importerade attribut:

1. Välj fliken **Kopplingar** i Synkroniseringstjänsthanteraren.
2. Högerklicka på Azure AD-kopplingen och välj **Egenskaper**.
3. Gå till fliken **Välj attribut** i popup-dialogrutan.
4. Välj **attributet preferredDataLocation** i listan.
5. Spara genom att välja **OK**.

![Skärmbild av dialogrutan Synkroniseringstjänsthanteraren och egenskaper](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>Steg 5: Skapa en inkommande synkroniseringsregel
Den inkommande synkroniseringsregeln gör att attributvärdet kan flöda från källattributet i lokal Active Directory till metaversumet.

1. Starta **redigeraren** för synkroniseringsregler genom att gå till > **START-redigeraren för synkroniseringsregler**. **START**
2. Ange att sökfiltret **Riktning** ska vara **inkommande**.
3. Om du vill skapa en ny inkommande regel väljer du **Lägg till ny regel**.
4. Ange följande konfiguration under fliken **Beskrivning:**

    | Attribut | Värde | Information |
    | --- | --- | --- |
    | Namn | *Ange ett namn* | Till exempel "In från AD – User preferredDataLocation" |
    | Beskrivning | *Ange en anpassad beskrivning* |  |
    | Anslutet system | *Välj den lokala Active Directory Connector* |  |
    | Ansluten systemobjekttyp | **Användare** |  |
    | Objekttyp för metaversum | **Person** |  |
    | Länktyp | **Anslut** |  |
    | Prioritet | *Välj ett nummer mellan 1–99* | 1–99 är reserverat för anpassade synkroniseringsregler. Välj inte ett värde som används av en annan synkroniseringsregel. |

5. Håll **filtret Omfångsfilter** tomt så att alla objekt ingår. Du kan behöva justera filtret för omfång enligt din Azure AD Connect-distribution.
6. Gå till **fliken Omformning**och implementera följande omvandlingsregel:

    | Flödestyp | Målattribut | Källa | Ansök en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    |Direct | preferredDataLocation | Välj källattributet | Avmarkerat | Uppdatering |

7. Om du vill skapa den inkommande regeln väljer du **Lägg till**.

![Skärmbild av regeln Skapa inkommande synkronisering](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>Steg 6: Skapa en utgående synkroniseringsregel
Regeln för utgående synkronisering gör att attributvärdet kan flöda från metaversum till **attributet preferredDataLocation** i Azure AD:

1. Gå till Redigeraren för **synkroniseringsregler**.
2. Ange att sökfiltret **Riktning** ska vara **utgående**.
3. Välj **Lägg till ny regel**.
4. Ange följande konfiguration under fliken **Beskrivning:**

    | Attribut | Värde | Information |
    | ----- | ------ | --- |
    | Namn | *Ange ett namn* | Till exempel "Ut till Azure AD – Användaren föredrarDataLocation" |
    | Beskrivning | *Ge en beskrivning* ||
    | Anslutet system | *Välj Azure AD-anslutning* ||
    | Ansluten systemobjekttyp | **Användare** ||
    | Objekttyp för metaversum | **Person** ||
    | Länktyp | **Anslut** ||
    | Prioritet | *Välj ett nummer mellan 1–99* | 1–99 är reserverat för anpassade synkroniseringsregler. Välj inte ett värde som används av en annan synkroniseringsregel. |

5. Gå till fliken **Omfångsfilter** och lägg till en enda filtergrupp med två satser:

    | Attribut | Operator | Värde |
    | --- | --- | --- |
    | sourceObjectType | Lika | Användare |
    | cloudMastered | NOTER | True |

    Omfångsfilter avgör vilka Azure AD-objekt som den här utgående synkroniseringsregeln tillämpas på. I det här exemplet använder vi samma omfångsfilter från "Ut till Azure AD – Användaridentitet" OOB (out-of-box) synkroniseringsregel. Det förhindrar att synkroniseringsregeln tillämpas på **användarobjekt** som inte är synkroniserade från en lokal Active Directory. Du kan behöva justera filtret för omfång enligt din Azure AD Connect-distribution.

6. Gå till fliken **Omformning** och implementera följande omvandlingsregel:

    | Flödestyp | Målattribut | Källa | Ansök en gång | Kopplingstyp |
    | --- | --- | --- | --- | --- |
    | Direct | preferredDataLocation | preferredDataLocation | Avmarkerat | Uppdatering |

7. Stäng **Lägg till** för att skapa den utgående regeln.

![Skärmbild av regeln Skapa utgående synkronisering](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>Steg 7: Kör hela synkroniseringscykeln
I allmänhet krävs fullständig synkroniseringscykel. Detta beror på att du har lagt till nya attribut i både Active Directory- och Azure AD Connector-schemat och infört anpassade synkroniseringsregler. Verifiera ändringarna innan du exporterar dem till Azure AD. Du kan använda följande steg för att verifiera ändringarna, medan du kör stegen manuellt för att skapa en fullständig synkroniseringscykel.

1. Kör **fullständig import** på den lokala Active Directory Connector:

   1. Gå till fliken **Operationer** i Synkroniseringstjänsthanteraren.
   2. Högerklicka på den **lokala Active Directory Connector**och välj **Kör**.
   3. Välj **Fullständig import**i dialogrutan och välj **OK**.
   4. Vänta tills åtgärden är klar.

      > [!NOTE]
      > Du kan hoppa över fullständig import på den lokala Active Directory Connector om källattributet redan finns med i listan över importerade attribut. Med andra ord behövde du inte göra några ändringar under steg 2 tidigare i den här artikeln.

2. Kör **fullständig import** på Azure AD Connector:

   1. Högerklicka på **Azure AD Connector**och välj **Kör**.
   2. Välj **Fullständig import**i dialogrutan och välj **OK**.
   3. Vänta tills åtgärden är klar.

3. Verifiera synkroniseringsregeländringarna för ett befintligt **användarobjekt.**

   Källattributet från lokala Active Directory och **preferredDataLocation** från Azure AD har importerats till respektive anslutningsutrymme. Innan du fortsätter med det fullständiga synkroniseringssteget gör du en förhandsgranskning av ett befintligt **användarobjekt** i det lokala Active Directory Connector-utrymmet. Objektet du valde ska ha källattributet ifyllt. En lyckad förhandsgranskning med **preferredDataLocation** som fylls i i metaversumet är en bra indikator på att du har konfigurerat synkroniseringsreglerna korrekt. Information om hur du gör en förhandsgranskning finns i [Verifiera ändringen](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Kör **fullständig synkronisering** på den lokala Active Directory Connector:

   1. Högerklicka på den **lokala Active Directory Connector**och välj **Kör**.
   2. Välj **Fullständig synkronisering**i dialogrutan och välj **OK**.
   3. Vänta tills åtgärden är klar.

5. **Verifiera väntande export till** Azure AD:

   1. Högerklicka på **Azure AD Connector**och välj **Sök kopplingsutrymme**.
   2. I dialogrutan **Sökkopplingsutrymme:**

        a. Ange **scope** till **väntande export**.<br>
        b. Markera alla tre kryssrutorna, inklusive **Lägg till, Ändra och Ta bort**.<br>
        c. Om du vill visa listan över objekt med ändringar som ska exporteras väljer du **Sök**. Om du vill undersöka ändringarna för ett visst objekt dubbelklickar du på objektet.<br>
        d. Kontrollera att ändringarna förväntas.

6. Kör **export** på **Azure AD-anslutning**

   1. Högerklicka på **Azure AD Connector**och välj **Kör**.
   2. Välj **Exportera**i dialogrutan **Kör koppling** och välj **OK**.
   3. Vänta tills åtgärden är klar.

> [!NOTE]
> Du kanske märker att stegen inte innehåller det fullständiga synkroniseringssteget på Azure AD Connector eller exportsteget på Active Directory Connector. Stegen krävs inte, eftersom attributvärdena flödar från lokala Active Directory till Azure AD.

## <a name="step-8-re-enable-sync-scheduler"></a>Steg 8: Återaktivera synkroniseringsschemaläggare
Aktivera den inbyggda synkroniseringsschemat igen:

1. Starta en PowerShell-session.
2. Återaktivera schemalagd synkronisering genom att köra den här cmdleten:`Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-9-verify-the-result"></a>Steg 9: Verifiera resultatet
Det är nu dags att verifiera konfigurationen och aktivera den för användarna.

1. Lägg till geo i det valda attributet för en användare. Listan över tillgängliga geos finns i den här tabellen.  
![Skärmbild av AD-attribut som lagts till för en användare](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Vänta tills attributet ska synkroniseras till Azure AD.
3. Kontrollera att postlåderegionen har ställts in korrekt med Exchange Online PowerShell.  
![Skärmbild av Exchange Online PowerShell](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Förutsatt att din klient har markerats för att kunna använda den här funktionen flyttas postlådan till rätt geo. Detta kan verifieras genom att titta på servernamnet där postlådan finns.

## <a name="next-steps"></a>Nästa steg

Läs mer om Multi-Geo i Office 365:

* [Multi-Geo sessioner på Ignite](https://aka.ms/MultiGeoIgnite)
* [Multi-Geo i OneDrive](https://aka.ms/OneDriveMultiGeo)
* [Multi-Geo i SharePoint Online](https://aka.ms/SharePointMultiGeo)

Läs mer om konfigurationsmodellen i synkroniseringsmotorn:

* Läs mer om konfigurationsmodellen i [Förstå deklarativa etablering .](concept-azure-ad-connect-sync-declarative-provisioning.md)
* Läs mer om uttrycksspråket i [Förstå deklarativa etableringsuttryck](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Översikt ämnen:

* [Synkronisering av Azure AD Connect: Förstå och anpassa synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
