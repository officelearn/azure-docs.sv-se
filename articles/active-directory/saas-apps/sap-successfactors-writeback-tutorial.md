---
title: 'Självstudie: Konfigurera SuccessFactors tillbakaskrivning i Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar tillbakaskrivning av attribut till SuccessFactors från Azure AD
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060056"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Självstudie: Konfigurera tillbakaskrivning av attribut från Azure AD till SAP SuccessFactors (för hands version)
Syftet med den här självstudien är att visa de steg som du behöver utföra för att ångra attribut från Azure AD till SuccessFactors personal Central. Det enda attribut som stöds för tillbakaskrivning är e-postattributet. 

## <a name="overview"></a>Översikt

När du har installerat integrering av inkommande etablering med antingen [SuccessFactors till en lokal AD](sap-successfactors-inbound-provisioning-tutorial.md) -etablerings app eller [SUCCESSFACTORS till Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) Provisioning-appen, kan du konfigurera SuccessFactors tillbakaskrivning-appen för att skriva e-postadressen tillbaka till SuccessFactors. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Vem är den här användar etablerings lösningen som passar bäst för?

Den här SuccessFactors-lösningen för tillbakaskrivning av användare passar utmärkt för:

* Organisationer som använder Office 365 som vill kunna tillbakaskrivning av auktoritativa attribut som hanteras av IT (till exempel e-postadress) tillbaka till SuccessFactors

## <a name="configuring-successfactors-for-the-integration"></a>Konfigurera SuccessFactors för integrering

Ett gemensamt krav för alla SuccessFactors etablerings anslutningar är att de kräver autentiseringsuppgifter för ett SuccessFactors-konto med rätt behörighet för att anropa SuccessFactors OData-API: erna. I det här avsnittet beskrivs steg för att skapa tjänst kontot i SuccessFactors och bevilja lämpliga behörigheter. 

* [Skapa/identifiera API-användarkonto i SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Skapa en roll för API-behörigheter](#create-an-api-permissions-role)
* [Skapa en behörighets grupp för API-användaren](#create-a-permission-group-for-the-api-user)
* [Bevilja behörighets rollen till behörighets gruppen](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Skapa/identifiera API-användarkonto i SuccessFactors
Arbeta med ditt SuccessFactors-administratörs team eller implementerings partner för att skapa eller identifiera ett användar konto i SuccessFactors som ska användas för att anropa OData-API: erna. Autentiseringsuppgifterna för användar namn och lösen ord för det här kontot kommer att krävas när du konfigurerar etablerings appar i Azure AD. 

### <a name="create-an-api-permissions-role"></a>Skapa en roll för API-behörigheter

* Logga in på SAP SuccessFactors med ett användar konto som har åtkomst till administrations centret.
* Sök efter *Hantera behörighets roller*och välj **Hantera behörighets roller** från Sök resultaten.
  ![hantera behörighets roller](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* I listan behörighets roll klickar du på **Skapa ny**.
  > [!div class="mx-imgBorder"]
  > ![skapa ny behörighets roll](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Lägg till ett **roll namn** och en **Beskrivning** för den nya behörighets rollen. Namnet och beskrivningen ska ange att rollen är för API-användnings behörigheter.
  > [!div class="mx-imgBorder"]
  > information](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png) för ![behörighets roll
* Under behörighets inställningar klickar du på **behörighet...** och bläddrar sedan ned behörighets listan och klickar på **Hantera integrerings verktyg**. Markera kryss rutan om **du vill tillåta administratörs åtkomst till OData-API via grundläggande autentisering**.
  > [!div class="mx-imgBorder"]
  > ![hantera integrerings verktyg](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Rulla nedåt i samma ruta och välj **medarbetares centrala API**. Lägg till behörigheter som visas nedan för att läsa med ODATA API och redigera med ODATA API. Välj alternativet Redigera om du planerar att använda samma konto för SuccessFactors-scenariot för tillbakaskrivning. 
  > [!div class="mx-imgBorder"]
  > ![Läs Skriv behörighet](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Klicka på **färdig**. Klicka på **Spara ändringar**.

### <a name="create-a-permission-group-for-the-api-user"></a>Skapa en behörighets grupp för API-användaren

* I SuccessFactors administrations Center söker du efter *Hantera behörighets grupper*och väljer **Hantera behörighets grupper** från Sök resultaten.
  > [!div class="mx-imgBorder"]
  > ![hantera behörighets grupper](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* I fönstret Hantera behörighets grupper klickar du på **Skapa nytt**.
  > [!div class="mx-imgBorder"]
  > ![lägga till ny grupp](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Lägg till ett grupp namn för den nya gruppen. Grupp namnet ska indikera att gruppen är för API-användare.
  > [!div class="mx-imgBorder"]
  > ![behörighets gruppens namn](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Lägg till medlemmar i gruppen. Du kan till exempel välja **användar namn** i list rutan personer i poolen och sedan ange användar namnet för det API-konto som ska användas för integreringen. 
  > [!div class="mx-imgBorder"]
  > ![Lägga till gruppmedlemmar](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Slutför skapandet av behörighets gruppen genom att klicka på **klar** .

### <a name="grant-permission-role-to-the-permission-group"></a>Bevilja behörighets rollen till behörighets gruppen

* I SuccessFactors administrations Center kan du söka efter *Hantera behörighets roller*och sedan välja **Hantera behörighets roller** från Sök resultaten.
* I **listan behörighets roll**väljer du den roll som du har skapat för behörigheter för API-användning.
* Under **tilldela den här rollen till... klickar du**på **Lägg till...** -knapp.
* Välj **behörighets grupp...** från den nedrullningsbara menyn och klicka sedan på **Välj...** för att öppna fönstret grupper för att söka efter och välja den grupp som skapades ovan. 
  > [!div class="mx-imgBorder"]
  > ![Lägg till behörighets grupp](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Granska behörighets rollen bevilja behörighets gruppen. 
  > [!div class="mx-imgBorder"]
  > ![behörighets roll och grupp information](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Klicka på **Spara ändringar**.

## <a name="configuring-successfactors-writeback"></a>Konfigurera SuccessFactors tillbakaskrivning

Det här avsnittet innehåller steg för 

* [Lägg till etablerings anslutnings programmet och konfigurera anslutningen till SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Konfigurera mappningar för attribut](#part-2-configure-attribute-mappings)
* [Aktivera och starta användar etablering](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Del 1: Lägg till etablerings anslutnings programmet och konfigurera anslutningen till SuccessFactors

**Så här konfigurerar du tillbakaskrivning av SuccessFactors:**

1. Gå till <https://portal.azure.com>

2. I det vänstra navigerings fältet väljer du **Azure Active Directory**

3. Välj **företags program**och sedan **alla program**.

4. Välj **Lägg till ett program**och välj kategorin **alla** .

5. Sök efter **tillbakaskrivning av SuccessFactors**och Lägg till den appen från galleriet.

6. När appen har lagts till och skärmen information om appen visas väljer du **etablering**

7. Ändra **etablerings** **läget** till **automatiskt**

8. Slutför avsnittet **admin credentials** enligt följande:

   * **Administratörens användar namn** – Ange användar namnet för SuccessFactors-API: t med det företags-ID som lagts till. Formatet: **användar namn\@companyID**

   * **Administratörs lösen ord –** Ange lösen ordet för SuccessFactors-API-användarkontot. 

   * **Klient-URL –** Ange namnet på SuccessFactors OData API Services-slutpunkten. Ange bara värd namnet för servern utan http eller https. Det här värdet bör se ut så här: **API-Server-Name.SuccessFactors.com**.

   * **E-postavisering –** Ange din e-postadress och markera kryss rutan "skicka e-post om fel inträffar".
    > [!NOTE]
    > Azure AD Provisioning-tjänsten skickar e-postavisering om etablerings jobbet hamnar i [karantän](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) .

   * Klicka på knappen **Testa anslutning** . Om anslutnings testet lyckas, klickar du på knappen **Spara** längst upp. Om det Miss lyckas kontrollerar du att autentiseringsuppgifterna och URL: en för SuccessFactors är giltiga.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * När autentiseringsuppgifterna har sparats visar avsnittet **mappningar** standard mappningen för **synkronisering Azure Active Directory användare till SuccessFactors**

### <a name="part-2-configure-attribute-mappings"></a>Del 2: Konfigurera mappningar för attribut

I det här avsnittet ska du konfigurera hur användar data flödar från SuccessFactors till Active Directory.

1. På fliken etablering under **mappningar**klickar du på **Synkronisera Azure Active Directory användare till SuccessFactors**.

1. I fältet **käll objekt omfånget** kan du välja vilka uppsättningar av användare i Azure AD som ska beaktas för tillbakaskrivning genom att definiera en uppsättning attributbaserade filter. Standard omfånget är "alla användare i Azure AD". 
   > [!TIP]
   > När du konfigurerar etablerings appen för första gången måste du testa och verifiera dina mappningar och uttryck för att kontrol lera att det ger önskat resultat. Microsoft rekommenderar att du använder omfångs filter under **käll objekt omfånget** för att testa dina mappningar med några test användare från Azure AD. När du har kontrollerat att mappningarna fungerar kan du antingen ta bort filtret eller gradvis expandera det så att det innehåller fler användare.

1. **Åtgärds fältet för mål objekt** stöder bara **uppdaterings** åtgärden.

1. I avsnittet **mappningar av attribut** kan du bara ändra MATCHNINGS-ID som används för att länka en SuccessFactors användar profil med Azure AD-användare och vilket attribut i Azure AD som fungerar som källa till e-postmeddelandet. 
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >SuccessFactors-tillbakaskrivning stöder bara e-postattributet. Använd inte **Lägg till ny mappning** för att lägga till nya attribut. 

1. Spara dina mappningar genom att klicka på **Spara** överst i avsnittet attribut-mappning.

När du har slutfört konfigurationen av attributmappning kan du nu [Aktivera och starta användar etablerings tjänsten](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Aktivera och starta användar etablering

När SuccessFactors-konfigurationen har slutförts kan du aktivera etablerings tjänsten i Azure Portal.

> [!TIP]
> Som standard när du aktiverar etablerings tjänsten kommer den att initiera etablerings åtgärder för alla användare i omfånget. Om det uppstår fel i mappnings-eller data frågor för data lagret kan etablerings jobbet Miss Miss kan och gå in i karantäns läget. För att undvika detta rekommenderar vi att du konfigurerar **käll objekt omfångs** filter och testar dina mappningar av attribut med några test användare innan du startar den fullständiga synkroniseringen för alla användare. När du har kontrollerat att mappningarna fungerar och ger dig önskade resultat kan du antingen ta bort filtret eller gradvis expandera det så att det innehåller fler användare.

1. På fliken **etablering** ställer du in **etablerings status** på **på**.

2. Klicka på **Save** (Spara).

3. Den här åtgärden startar den inledande synkroniseringen, vilket kan ta ett variabelt antal timmar beroende på hur många användare som finns i SuccessFactors-klienten. Du kan kontrol lera förlopps indikatorn för att följa synkroniseringens förlopp. 

4. Gå till fliken **gransknings loggar** i Azure Portal för att se vilka åtgärder som etablerings tjänsten har utfört. I gransknings loggarna visas alla enskilda synkroniseringsfel som utförs av etablerings tjänsten, t. ex. vilka användare som ska läsas ut från Workday och därefter läggs till eller uppdateras till Active Directory. 

5. När den inledande synkroniseringen har slutförts skrivs en gransknings sammanfattnings rapport på fliken **etablering** , som visas nedan.

   > [!div class="mx-imgBorder"]
   > förlopps indikator för ![etablering](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
* [Lär dig hur du konfigurerar enkel inloggning mellan SuccessFactors och Azure Active Directory](successfactors-tutorial.md)
* [Lär dig hur du integrerar andra SaaS-program med Azure Active Directory](tutorial-list.md)
* [Lär dig hur du exporterar och importerar dina etablerings konfigurationer](../app-provisioning/export-import-provisioning-configuration.md)

