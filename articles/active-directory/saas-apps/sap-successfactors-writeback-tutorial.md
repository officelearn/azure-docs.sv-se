---
title: 'Självstudiekurs: Konfigurera SuccessFactors-tillbakaskrivning i Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar attributskrivning till SuccessFactors från Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 2de0cdd32428884170f549afacdbd52c3a10c93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060056"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Självstudiekurs: Konfigurera attributåterskrivning från Azure AD till SAP SuccessFactors (förhandsversion)
Syftet med den här självstudien är att visa de steg du behöver utföra för att skriva tillbaka attribut från Azure AD till SuccessFactors Employee Central. Det enda attribut som för närvarande stöds för tillbakaskrivning är e-postattributet. 

## <a name="overview"></a>Översikt

När du har konfigurerat inkommande etableringsintegration med hjälp av [SuccessFactors till lokala AD-etableringsapp](sap-successfactors-inbound-provisioning-tutorial.md) [eller SuccessFactors till Azure](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) AD-etableringsapp, kan du konfigurera appen SuccessFactors-tillbakaskrivning för att skriva tillbaka e-postadressen till SuccessFactors. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Vem är den här lösning för etablering av användare som är bäst lämpad för?

Den här SuccessFactors-etableringslösningen för tillbakaskrivningsanvändare är idealisk för:

* Organisationer som använder Office 365 som vill skriva tillbaka auktoritära attribut som hanteras av IT (t.ex. e-postadress) tillbaka till SuccessFactors

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

## <a name="configuring-successfactors-writeback"></a>Konfigurera tillbakaskrivning av SuccessFactors

I det här avsnittet finns steg för 

* [Lägga till etableringsappen och konfigurera anslutningen till SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Konfigurera attributmappningar](#part-2-configure-attribute-mappings)
* [Aktivera och starta etablering av användare](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Del 1: Lägg till etableringsappen och konfigurera anslutningen till SuccessFactors

**Så här konfigurerar du Tillbakaskrivning av SuccessFactors:**

1. Gå till <https://portal.azure.com>

2. Välj **Azure Active Directory** i det vänstra navigeringsfältet

3. Välj **Företagsprogram**och sedan **alla program**.

4. Välj **Lägg till ett program**och välj kategorin **Alla.**

5. Sök efter **SuccessFactors-tillbakaskrivning**och lägg till appen från galleriet.

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
    >![Azure-portalen](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * När autentiseringsuppgifterna har sparats visas **standardmappningen Synkronisera Azure Active Directory-användare till SuccessFactors i** avsnittet **Mappningar**

### <a name="part-2-configure-attribute-mappings"></a>Del 2: Konfigurera attributmappningar

I det här avsnittet ska du konfigurera hur användardata flödar från SuccessFactors till Active Directory.

1. Klicka på Synkronisera Azure Active **Directory-användare till SuccessFactors**på fliken Etablering under **Mappningar.**

1. I fältet **Källobjektomfattning** kan du välja vilka uppsättningar användare i Azure AD som ska beaktas för tillbakaskrivning genom att definiera en uppsättning attributbaserade filter. Standardomfattningen är "alla användare i Azure AD". 
   > [!TIP]
   > När du konfigurerar etableringsappen för första gången måste du testa och verifiera attributmappningar och uttryck för att se till att den ger dig önskat resultat. Microsoft rekommenderar att du använder omfångsfiltren under **Källobjektomfattning** för att testa dina mappningar med några testanvändare från Azure AD. När du har verifierat att mappningarna fungerar kan du antingen ta bort filtret eller gradvis expandera det till att omfatta fler användare.

1. Fältet **Målobjektåtgärder** stöder bara **uppdateringsåtgärden.**

1. I avsnittet **Attributmappningar** kan du bara ändra det matchande ID som används för att länka en SuccessFactors-användarprofil med Azure AD-användare och vilket attribut i Azure AD fungerar som e-postkälla. 
    >[!div class="mx-imgBorder"]
    >![Azure-portalen](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >SuccessFactors-tillbakaskrivningen stöder bara e-postattributet. Använd inte **Lägg till ny mappning** för att lägga till nya attribut. 

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

* [Läs om hur du granskar loggar och hämtar rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
* [Lär dig hur du konfigurerar enkel inloggning mellan SuccessFactors och Azure Active Directory](successfactors-tutorial.md)
* [Lär dig hur du integrerar andra SaaS-program med Azure Active Directory](tutorial-list.md)
* [Lär dig hur du exporterar och importerar etableringskonfigurationer](../app-provisioning/export-import-provisioning-configuration.md)

