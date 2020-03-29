---
title: 'Självstudiekurs: Konfigurera successfactors inkommande etablering i Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar inkommande etablering från SuccessFactors till Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: fac4f61e-d942-4429-a298-9ba74db95077
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 09501a80d6ddcbbc9fa6cc08e36f47beb13d1663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063230"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning-preview"></a>Självstudiekurs: Konfigurera SAP SuccessFactors till Azure AD-användareetablering (förhandsversion)
Syftet med den här självstudien är att visa de steg du behöver utföra för att etablera arbetardata från SuccessFactors Employee Central i Azure Active Directory, med valfri tillbakaskrivning av e-postadress till SuccessFactors. Den här integreringen är i offentlig förhandsversion och stöder hämtning av mer än [70+ användarattribut](../app-provisioning/sap-successfactors-attribute-reference.md) från SuccessFactors Employee Central. 

>[!NOTE]
>Använd den här självstudien om de användare som du vill etablera från SuccessFactors är molnanvändare som inte behöver ett lokalt AD-konto. Om användarna bara kräver lokalt AD-konto eller både AD- och Azure AD-konto, se självstudien om [hur SAP SuccessFactors konfigureras till Active](sap-successfactors-inbound-provisioning-tutorial.md#overview) Directory-användareetablering. 

## <a name="overview"></a>Översikt

[Azure Active Directory-tjänsten för användaretablering](../app-provisioning/user-provisioning.md) integreras med [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) för att hantera användarnas identitetslivscykel. 

Den SuccessFactors användaretablering arbetsflöden som stöds av Azure AD användare etablering tjänst möjliggör automatisering av följande mänskliga resurser och identitet livscykelhantering scenarier:

* **Anställa nya medarbetare** – När en ny medarbetare läggs till i SuccessFactors skapas ett användarkonto automatiskt i Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md), med tillbakaskrivning av e-postadressen till SuccessFactors.

* **Medarbetarattribut och profiluppdateringar** – När en medarbetarpost uppdateras i SuccessFactors (till exempel namn, titel eller chef) uppdateras deras användarkonto automatiskt Azure Active Directory och eventuellt Office 365 och [andra SaaS-program](../app-provisioning/user-provisioning.md)som stöds av Azure AD .

* **Uppsägning av medarbetare** - När en medarbetare sägs upp i SuccessFactors inaktiveras deras användarkonto automatiskt i Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md).

* **Medarbetare återanställer** - När en medarbetare återanställs i SuccessFactors kan deras gamla konto återaktiveras eller återetableras (beroende på dina önskemål) till Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Vem är den här lösning för etablering av användare som är bäst lämpad för?

Den här SuccessFactors to Azure Active Directory-användaretableringslösningen är idealisk för:

* Organisationer som önskar en förbyggd, molnbaserad lösning för SuccessFactors-användaresetablering

* Organisationer som kräver direkt användaretablering från SuccessFactors till Azure Active Directory

* Organisationer som kräver att användare etableras med hjälp av data som hämtats från [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organisationer som använder Office 365 för e-post

## <a name="solution-architecture"></a>Lösningsarkitektur

I det här avsnittet beskrivs lösningsarkitekturen för slut-till-slutanvändaret för användare som bara är molnet. Det finns två relaterade flöden:

* **Auktoritärt HR-dataflöde – från SuccessFactors till Azure Active Directory:** I det här flödet uppstår arbetarhändelser (till exempel Nyanställda, Överföringar, Uppsägningar) först i molnet SuccessFactors Employee Central och sedan flödar händelsedata till Azure Active Directory. Beroende på händelsen kan det leda till att skapa/uppdatera/aktivera/inaktivera åtgärder i Azure AD.
* **Återskrivningsflöde för e-post – från lokal Active Directory till SuccessFactors:** När kontoskapandet är klart i Azure Active Directory kan e-postattributvärdet eller UPN som genereras i Azure AD skrivas tillbaka till SuccessFactors.

  ![Översikt](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Dataflöde från till användare

1. HR-teamet utför arbetartransaktioner (Joiners/Movers/Leavers eller New Hires/Transfers/Terminations) i SuccessFactors Employee Central
2. Azure AD-etableringstjänsten kör schemalagda synkroniseringar av identiteter från SuccessFactors EC och identifierar ändringar som måste bearbetas för synkronisering med lokal Active Directory.
3. Azure AD-etableringstjänsten bestämmer ändringen och anropar åtgärden skapa/uppdatera/aktivera/inaktivera för användaren i Azure AD.
4. Om [SuccessFactors-tillbakaskrivningsappen](sap-successfactors-writeback-tutorial.md) är konfigurerad hämtas användarens e-postadress från Azure AD. 
5. Azure AD-etableringstjänsten skriver tillbaka e-postattribut till SuccessFactors, baserat på det matchande attribut som används.

## <a name="planning-your-deployment"></a>Planera distributionen

Konfigurera Cloud HR-driven användaretablering från SuccessFactors till Azure AD kräver omfattande planering som täcker olika aspekter, till exempel:

* Bestämma matchnings-ID 
* Attributmappning
* Omvandling av attribut 
* Omfångsfilter

Se [moln-HR-distributionsplanen](../app-provisioning/plan-cloud-hr-provision.md) för omfattande riktlinjer kring dessa ämnen. 

## <a name="configuring-successfactors-for-the-integration"></a>Konfigurera SuccessFactors för integrationen

Ett vanligt krav för alla SuccessFactors-etableringskopplingar är att de kräver autentiseringsuppgifter för ett SuccessFactors-konto med rätt behörighet att anropa SuccessFactors OData-API:er. I det här avsnittet beskrivs steg för att skapa tjänstkontot i SuccessFactors och bevilja lämpliga behörigheter. 

* [Skapa/identifiera API-användarkonto i SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Skapa en API-behörighetsroll](#create-an-api-permissions-role)
* [Skapa en behörighetsgrupp för API-användaren](#create-a-permission-group-for-the-api-user)
* [Bevilja behörighetsroll till behörighetsgruppen](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Skapa/identifiera API-användarkonto i SuccessFactors
Arbeta med ditt SuccessFactors-administratörsteam eller implementeringspartner för att skapa eller identifiera ett användarkonto i SuccessFactors som ska användas för att anropa OData-API:erna. Användarnamn och lösenord autentiseringsuppgifter för det här kontot kommer att krävas när du konfigurerar etableringsapparna i Azure AD. 

### <a name="create-an-api-permissions-role"></a>Skapa en API-behörighetsroll

* Logga in på SAP SuccessFactors med ett användarkonto som har åtkomst till administrationscentret.
* Sök efter *Hantera behörighetsroller*och välj sedan **Hantera behörighetsroller** från sökresultaten.
  ![Hantera behörighetsroller](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Klicka på **Skapa nytt**i listan Behörighetsroll .
  > [!div class="mx-imgBorder"]
  > ![Skapa ny behörighetsroll](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Lägg till ett **rollnamn** och **en beskrivning** för den nya behörighetsrollen. Namnet och beskrivningen bör ange att rollen är för API-användningsbehörigheter.
  > [!div class="mx-imgBorder"]
  > ![Information om behörighetsroll](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Under Behörighetsinställningar klickar du på **Behörighet...** och rullar sedan ned i behörighetslistan och klickar på **Hantera integrationsverktyg**. Markera kryssrutan **Tillåt admin att komma åt OData API via grundläggande autentisering**.
  > [!div class="mx-imgBorder"]
  > ![Hantera integrationsverktyg](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Bläddra nedåt i samma ruta och välj **Personal central-API**. Lägg till behörigheter som visas nedan för att läsa med ODATA API och redigera med ODATA API. Välj redigeringsalternativet om du planerar att använda samma konto för scenariot Skriv tillbaka till SuccessFactors. 
  > [!div class="mx-imgBorder"]
  > ![Läs skrivbehörigheter](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Klicka på **Klar**. Klicka på **Spara ändringar**.

### <a name="create-a-permission-group-for-the-api-user"></a>Skapa en behörighetsgrupp för API-användaren

* Sök efter *hantera behörighetsgrupper*i Administrationscentret för SuccessFactors och välj sedan **Hantera behörighetsgrupper** från sökresultaten.
  > [!div class="mx-imgBorder"]
  > ![Hantera behörighetsgrupper](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Klicka på **Skapa nytt**i fönstret Hantera behörighetsgrupper .
  > [!div class="mx-imgBorder"]
  > ![Lägg till ny grupp](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Lägg till ett gruppnamn för den nya gruppen. Gruppnamnet ska ange att gruppen är för API-användare.
  > [!div class="mx-imgBorder"]
  > ![Namn på behörighetsgrupp](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Lägg till medlemmar i gruppen. Du kan till exempel välja **Användarnamn** på listrutan Kontakterpool och sedan ange användarnamnet för API-kontot som ska användas för integreringen. 
  > [!div class="mx-imgBorder"]
  > ![Lägga till gruppmedlemmar](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Klicka på **Klar** om du vill slutföra skapandet av behörighetsgruppen.

### <a name="grant-permission-role-to-the-permission-group"></a>Bevilja behörighetsroll till behörighetsgruppen

* I Administrationscenter för SuccessFactors söker du efter *Hantera behörighetsroller*och väljer sedan **Hantera behörighetsroller** från sökresultaten.
* Välj den roll som du skapade för API-användningsbehörigheter i **listan Behörigheter**för behörigheter för behörigheter för behörigheter för behörigheter för behörigheter för API-användning.
* Under **Grant denna roll till...**, klicka på Lägg **till...** knappen.
* Välj **Behörighetsgrupp...** på den nedrullningsbara menyn och klicka sedan på **Välj...** för att öppna fönstret Grupper för att söka och markera den grupp som skapats ovan. 
  > [!div class="mx-imgBorder"]
  > ![Lägga till behörighetsgrupp](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Granska behörighetsrollsbidraget till behörighetsgruppen. 
  > [!div class="mx-imgBorder"]
  > ![Behörighetsroll och gruppdetalj](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Klicka på **Spara ändringar**.

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Konfigurera användaretablering från SuccessFactors till Azure AD

Det här avsnittet innehåller steg för etablering av användarkonton från SuccessFactors till Azure AD.

* [Lägga till etableringsappen och konfigurera anslutningen till SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Konfigurera attributmappningar](#part-2-configure-attribute-mappings)
* [Aktivera och starta etablering av användare](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Del 1: Lägg till etableringsappen och konfigurera anslutningen till SuccessFactors

**Så här konfigurerar du SuccessFactors till Azure AD-etablering:**

1. Gå till <https://portal.azure.com>

2. Välj **Azure Active Directory** i det vänstra navigeringsfältet

3. Välj **Företagsprogram**och sedan **alla program**.

4. Välj **Lägg till ett program**och välj kategorin **Alla.**

5. Sök efter **SuccessFactors till Azure Active Directory User Provisioning**och lägg till appen från galleriet.

6. När appen har lagts till och skärmen för appinformation visas väljer du **Etablera**

7. Ändra **etableringsläget** **Mode** till **Automatiskt**

8. Fyll i avsnittet **Administratörsautentiseringsuppgifter** på följande sätt:

   * **Administratörsanvändarnamn** – Ange användarnamnet för SuccessFactors API-användarkonto, med företags-ID bifogat. Den har formatet: **\@användarnamn companyID**

   * **Admin lösenord -** Ange lösenordet för användarkontot för SuccessFactors API. 

   * **Url till klient –** Ange namnet på slutpunkten för SuccessFactors OData API-tjänster. Ange endast värdnamnet på servern utan http eller https. Det här värdet ska se ut så här: **api-server-name.successfactors.com**.

   * **E-postmeddelande –** Ange din e-postadress och markera kryssrutan "skicka e-post om fel inträffar" .
    > [!NOTE]
    > Azure AD-etableringstjänsten skickar e-postmeddelande om etableringsjobbet hamnar i [karantäntillstånd.](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

   * Klicka på knappen **Testa anslutning.** Om anslutningstestet lyckas klickar du på knappen **Spara** högst upp. Om det misslyckas dubbelkollar du att autentiseringsuppgifterna och URL:en för SuccessFactors är giltiga.
    >[!div class="mx-imgBorder"]
    >![Azure-portalen](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * När autentiseringsuppgifterna har **sparats** visas standardmappningen **Synkronisera lyckadesfaktareanvändare till Azure Active Directory**

### <a name="part-2-configure-attribute-mappings"></a>Del 2: Konfigurera attributmappningar

I det här avsnittet ska du konfigurera hur användardata flödar från SuccessFactors till Active Directory.

1. Klicka på **Synkronisera lyckadesfaktorer till Azure Active Directory**på fliken Etablering under **Mappningar.**

1. I fältet **Källobjektomfattning** kan du välja vilka uppsättningar användare i SuccessFactors som ska vara i omfång för etablering till Azure AD, genom att definiera en uppsättning attributbaserade filter. Standardomfånget är "alla användare i SuccessFactors". Exempelfilter:

   * Exempel: Omfattning till användare med personIdExternal mellan 1000000 och 2000000 (exklusive 2000000)

      * Attribut: personIdExternal

      * Operatör: REGEX Match

      * Värde: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exempel: Endast anställda och inte villkorade arbetstagare

      * Attribut: EmployeeID

      * Operator: ÄR INTE NULL

   > [!TIP]
   > När du konfigurerar etableringsappen för första gången måste du testa och verifiera attributmappningar och uttryck för att se till att den ger dig önskat resultat. Microsoft rekommenderar att du använder omfångsfiltren under **Källobjektomfattning** för att testa mappningarna med några testanvändare från SuccessFactors. När du har verifierat att mappningarna fungerar kan du antingen ta bort filtret eller gradvis expandera det till att omfatta fler användare.

   > [!CAUTION] 
   > Standardbeteendet för etableringsmotorn är att inaktivera/ta bort användare som inte omfattas. Detta kanske inte är önskvärt i dina SuccessFactors till Azure AD-integrering. Om du vill åsidosätta det här standardbeteendet finns i artikeln [Hoppa över borttagning av användarkonton som inte omfattas](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. I fältet **Målobjektåtgärder** kan du filtrera vilka åtgärder som utförs i Active Directory globalt. **Skapa** och **uppdatera** är vanligast.

1. I avsnittet **Attributmappningar** kan du definiera hur enskilda SuccessFactors-attribut mappas till Active Directory-attribut.

  >[!NOTE]
  >En fullständig lista över Attributet SuccessFactors som stöds av programmet finns i Attribute Reference för [SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)


1. Klicka på en befintlig attributmappning för att uppdatera den, eller klicka på **Lägg till ny mappning** längst ned på skärmen för att lägga till nya mappningar. En enskild attributmappning stöder följande egenskaper:

      * **Mappningstyp**

         * **Direkt** – Skriver värdet för Attributet SuccessFactors till AD-attributet, utan ändringar

         * **Constant** - Skriv ett statiskt, konstant strängvärde till AD-attributet

         * **Uttryck** – Gör att du kan skriva ett anpassat värde till AD-attributet, baserat på ett eller flera SuccessFactors-attribut. [Mer information finns i den här artikeln om uttryck](../app-provisioning/functions-for-customizing-application-data.md).

      * **Källattribut** - Användarattributet från SuccessFactors

      * **Standardvärde** – Valfritt. Om källattributet har ett tomt värde skrivs det här värdet i stället.
            Vanligaste konfigurationen är att lämna detta tomt.

      * **Målattribut** – Användarattributet i Active Directory.

      * **Matcha objekt med det här attributet** – Om den här mappningen ska användas för att unikt identifiera användare mellan SuccessFactors och Active Directory. Det här värdet anges vanligtvis i fältet Arbetar-ID för SuccessFactors, som vanligtvis mappas till ett av medarbetar-ID-attributen i Active Directory.

      * **Matchande prioritet** – Flera matchande attribut kan ställas in. När det finns flera utvärderas de i den ordning som definieras av det här fältet. Så snart en matchning hittas utvärderas inga ytterligare matchande attribut.

      * **Använd den här mappningen**

         * **Alltid** – Använd den här mappningen på både åtgärder för att skapa användare och uppdatera

         * **Endast under skapandet** - Använd den här mappningen endast på åtgärder för att skapa användare

1. Om du vill spara mappningarna klickar du på **Spara** högst upp i avsnittet Attributmappning.

När konfigurationen för attributmappning är klar kan du nu [aktivera och starta tjänsten för användaretablering](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Aktivera och starta etablering av användare

När SuccessFactors-etableringsappkonfigurationerna har slutförts kan du aktivera etableringstjänsten i Azure-portalen.

> [!TIP]
> Som standard när du aktiverar etableringstjänsten initieras etableringsåtgärder för alla användare i omfånget. Om det finns fel i mappnings- eller arbetsdagsdataproblemen kan etableringsjobbet misslyckas och gå in i karantäntillståndet. För att undvika detta, som en bästa praxis, rekommenderar vi att konfigurera **källobjektomfattningsfilter** och testa attributmappningarna med några testanvändare innan du startar fullständig synkronisering för alla användare. När du har kontrollerat att mappningarna fungerar och ger dig önskat resultat kan du antingen ta bort filtret eller gradvis expandera det till att omfatta fler användare.

1. På fliken **Etablering** anger du **etableringsstatusen** **till På**.

2. Klicka på **Spara**.

3. Den här åtgärden startar den första synkroniseringen, vilket kan ta ett varierande antal timmar beroende på hur många användare som finns i SuccessFactors-klienten. Du kan kontrollera förloppsindikatorn till spåret förloppet för synkroniseringscykeln. 

4. Kontrollera när som helst fliken **Granskningsloggar** i Azure-portalen för att se vilka åtgärder etableringstjänsten har utfört. Granskningsloggarna visar alla enskilda synkroniseringshändelser som utförs av etableringstjänsten, till exempel vilka användare som läses ut från Workday och sedan läggs till eller uppdateras i Active Directory. 

5. När den första synkroniseringen är klar skrivs en granskningssammanfattningsrapport på fliken **Etablering,** som visas nedan.

   > [!div class="mx-imgBorder"]
   > ![Etablera förloppsindikator](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Nästa steg

* [Läs mer om SuccessFactors-attribut som stöds för inkommande etablering](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Lär dig hur du konfigurerar tillbakaskrivning av e-post till SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
* [Lär dig hur du konfigurerar enkel inloggning mellan SuccessFactors och Azure Active Directory](successfactors-tutorial.md)
* [Lär dig hur du integrerar andra SaaS-program med Azure Active Directory](tutorial-list.md)
* [Lär dig hur du exporterar och importerar etableringskonfigurationer](../app-provisioning/export-import-provisioning-configuration.md)


