---
title: 'Självstudiekurs: Konfigurera successfactors inkommande etablering i Azure Active Directory | Microsoft-dokument'
description: Lär dig hur du konfigurerar inkommande etablering från SuccessFactors
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: 1ff90231-1312-463e-8949-7d976e433fc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2019
ms.author: chmutali
ms.openlocfilehash: d9317a68c8967fbe0728e8c47e59dd33367c6163
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249690"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning-preview"></a>Självstudiekurs: Konfigurera SAP SuccessFactors till etablering av Active Directory-användare (förhandsversion)
Syftet med den här självstudien är att visa de steg du behöver utföra för att etablera användare från SuccessFactors Employee Central i Active Directory (AD) och Azure AD, med valfri tillbakaskrivning av e-postadress till SuccessFactors. Den här integreringen är i offentlig förhandsversion och stöder hämtning av mer än [70+ användarattribut](../app-provisioning/sap-successfactors-attribute-reference.md) från SuccessFactors Employee Central.

>[!NOTE]
>Använd den här självstudien om de användare som du vill etablera från SuccessFactors behöver ett lokalt AD-konto och eventuellt ett Azure AD-konto. Om användare från SuccessFactors bara behöver Azure AD-konto (molnanvändare) läser du självstudien om [hur SAP SuccessFactors konfigureras till Azure](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) AD-användareetablering. 


## <a name="overview"></a>Översikt

[Azure Active Directory-tjänsten för användaretablering](../app-provisioning/user-provisioning.md) integreras med [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) för att hantera användarnas identitetslivscykel. 

Den SuccessFactors användaretablering arbetsflöden som stöds av Azure AD användare etablering tjänst möjliggör automatisering av följande mänskliga resurser och identitet livscykelhantering scenarier:

* **Anställa nya medarbetare** – När en ny medarbetare läggs till i SuccessFactors skapas ett användarkonto automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md), med tillbakaskrivning av e-postadressen till SuccessFactors.

* **Medarbetarattribut och profiluppdateringar** – När en medarbetarpost uppdateras i SuccessFactors (till exempel namn, titel eller chef) uppdateras deras användarkonto automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och [andra SaaS-program](../app-provisioning/user-provisioning.md)som stöds av Azure AD .

* **Uppsägning av medarbetare** – När en medarbetare sägs upp i SuccessFactors inaktiveras deras användarkonto automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md).

* **Medarbetare återanställs** - När en medarbetare återanställs i SuccessFactors kan deras gamla konto återaktiveras eller återetableras (beroende på dina önskemål) till Active Directory, Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Vem är den här lösning för etablering av användare som är bäst lämpad för?

Den här SuccessFactors to Active Directory-lösningen för användaretablering är idealisk för:

* Organisationer som önskar en förbyggd, molnbaserad lösning för SuccessFactors-användaresetablering

* Organisationer som kräver direkt användaretablering från SuccessFactors till Active Directory

* Organisationer som kräver att användare etableras med hjälp av data som hämtats från [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organisationer som kräver att ansluta, flytta och låta användare synkroniseras till en eller flera Active Directory-skogar, domäner och organisationsenheter baserat endast på ändringsinformation som identifierats i [SuccessFactors Employee Central (EG)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organisationer som använder Office 365 för e-post

## <a name="solution-architecture"></a>Lösningsarkitektur

I det här avsnittet beskrivs lösningsarkitekturen för heltäckande användaretablering för vanliga hybridmiljöer. Det finns två relaterade flöden:

* **Auktoritärt HR-dataflöde – från SuccessFactors till lokal Active Directory:** I det här flödet uppstår arbetarhändelser (till exempel Nyanställda, Överföringar, Uppsägningar) först i molnet SuccessFactors Employee Central och sedan flödar händelsedata till lokala Active Directory via Azure AD och etableringsagenten. Beroende på händelsen kan det leda till att skapa/uppdatera/aktivera/inaktivera åtgärder i AD.
* **Återskrivningsflöde för e-post – från lokal Active Directory till SuccessFactors:** När kontoskapandet är klart i Active Directory synkroniseras det med Azure AD via Azure AD Connect-synkronisering och e-postattribut kan skrivas tillbaka till SuccessFactors.

  ![Översikt](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Dataflöde från till användare

1. HR-teamet utför arbetartransaktioner (Joiners/Movers/Leavers eller New Hires/Transfers/Terminations) i SuccessFactors Employee Central
2. Azure AD-etableringstjänsten kör schemalagda synkroniseringar av identiteter från SuccessFactors EC och identifierar ändringar som måste bearbetas för synkronisering med lokal Active Directory.
3. Azure AD-etableringstjänsten anropar den lokala Azure AD Connect-etableringsagenten med en begäranhetstjänst som innehåller AD-konto skapa/uppdatera/aktivera/inaktivera åtgärder.
4. Azure AD Connect-etableringsagenten använder ett tjänstkonto för att lägga till/uppdatera AD-kontodata.
5. Azure AD Connect Sync-motorn kör deltasynkronisering för att hämta uppdateringar i AD.
6. Active Directory-uppdateringarna synkroniseras med Azure Active Directory.
7. Om [SuccessFactors-tillbakaskrivningsappen](sap-successfactors-writeback-tutorial.md) är konfigurerad skriver den tillbaka e-postattributet till SuccessFactors, baserat på det matchande attribut som används.

## <a name="planning-your-deployment"></a>Planera distributionen

Konfigurera Cloud HR-driven användaretablering från SuccessFactors till AD kräver omfattande planering som täcker olika aspekter, till exempel:
* Installation av etableraren för Azure AD Connect 
* Antal SuccessFactors till AD-användaretableringsappar som ska distribueras
* Matchande ID-, attributmappning, omvandling och omfångsfilter

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

  >[!NOTE]
  >För den fullständiga listan över attribut som hämtats av den här etableringsappen, se [Attribute Reference för SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)

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

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>Konfigurera användaretablering från SuccessFactors till Active Directory

I det här avsnittet finns steg för etablering av användarkonton från SuccessFactors till varje Active Directory-domän inom ramen för integreringen.

* [Lägg till etableringsappen och hämta etableringsagenten](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Installera och konfigurera lokala etableringsagenter](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Konfigurera anslutning till SuccessFactors och Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Konfigurera attributmappningar](#part-4-configure-attribute-mappings)
* [Aktivera och starta etablering av användare](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Del 1: Lägg till etableringsappen och hämta etableringsagenten

**Så här konfigurerar du SuccessFactors till Active Directory-etablering:**

1. Gå till <https://portal.azure.com>

2. Välj **Azure Active Directory** i det vänstra navigeringsfältet

3. Välj **Företagsprogram**och sedan **alla program**.

4. Välj **Lägg till ett program**och välj kategorin **Alla.**

5. Sök efter **SuccessFactors i Active Directory User Provisioning**och lägg till appen från galleriet.

6. När appen har lagts till och skärmen för appinformation visas väljer du **Etablera**

7. Ändra **etableringsläget** **Mode** till **Automatiskt**

8. Klicka på informationsbanderollen som visas för att hämta etableringsagenten. 
   > [!div class="mx-imgBorder"]
   > ![Ladda ner Agent](./media/sap-successfactors-inbound-provisioning/download-pa-agent.png "Skärmen Ladda ner agent")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Del 2: Installera och konfigurera lokala etableringsagenter

Om du vill etablera den lokala Active Directory måste etableringsagenten installeras på en server med .NET 4.7.1+ Framework och nätverksåtkomst till önskade Active Directory-domäner.

> [!TIP]
> Du kan kontrollera versionen av .NET-ramverket på servern med hjälp av instruktionerna [här](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Om servern inte har .NET 4.7.1 eller högre installerat kan du hämta den [härifrån](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Överför den nedladdade agentinstallationsprogrammet till servervärden och följ stegen nedan för att slutföra agentkonfigurationen.

1. Logga in på Windows Server där du vill installera den nya agenten.

1. Starta installationsprogrammet för etableringsagenten, godkänn villkoren och klicka på knappen **Installera.**

   ![Installera skärm](./media/workday-inbound-tutorial/pa_install_screen_1.png "Installera skärm")
   
1. När installationen är klar startar guiden och du ser skärmen **Anslut Azure AD.** Klicka på knappen **Autentisera** för att ansluta till din Azure AD-instans.

   ![Ansluta till Azure Active Directory](./media/workday-inbound-tutorial/pa_install_screen_2.png "Ansluta till Azure Active Directory")
   
1. Autentisera till din Azure AD-instans med hjälp av globala administratörsautentiseringsuppgifter.

   ![Admin Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Admin Auth")

   > [!NOTE]
   > Azure AD-administratörsautentiseringsuppgifterna används endast för att ansluta till din Azure AD-klientorganisation. Agenten lagrar inte autentiseringsuppgifterna lokalt på servern.

1. När autentiseringen med Azure AD har slutförts visas skärmen **Anslut Active Directory.** I det här steget anger du ditt AD-domännamn och klickar på knappen **Lägg till katalog.**

   ![Lägg till katalog](./media/workday-inbound-tutorial/pa_install_screen_4.png "Lägg till katalog")
  
1. Du uppmanas nu att ange de autentiseringsuppgifter som krävs för att ansluta till AD-domänen. På samma skärm kan du använda **prioriteten Välj domänkontrollant** för att ange domänkontrollanter som agenten ska använda för att skicka etableringsbegäranden.

   ![Domänautentiseringsuppgifter](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. När du har konfigurerat domänen visar installationsprogrammet en lista över konfigurerade domäner. På den här skärmen kan du upprepa steg #5 och #6 lägga till fler domäner eller klicka på **Nästa** för att gå vidare till agentregistrering.

   ![Konfigurerade domäner](./media/workday-inbound-tutorial/pa_install_screen_6.png "Konfigurerade domäner")

   > [!NOTE]
   > Om du har flera AD-domäner (t.ex. na.contoso.com, emea.contoso.com) lägger du till varje domän individuellt i listan.
   > Det räcker inte att bara lägga till den överordnade domänen (t.ex. contoso.com). Du måste registrera varje underordnad domän hos agenten.
   
1. Granska konfigurationsinformationen och klicka på **Bekräfta** för att registrera agenten.
  
   ![Bekräfta skärm](./media/workday-inbound-tutorial/pa_install_screen_7.png "Bekräfta skärm")
   
1. Konfigurationsguiden visar förloppet för agentregistreringen.
  
   ![Registrering av ombud](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registrering av ombud")
   
1. När agentregistreringen har slutförts kan du klicka på **Avsluta** för att avsluta guiden.
  
   ![Avsluta skärm](./media/workday-inbound-tutorial/pa_install_screen_9.png "Avsluta skärm")
   
1. Verifiera installationen av agenten och se till att den körs genom att öppna snapin-modulen "Tjänster" och leta efter tjänsten "Microsoft Azure AD Connect Provisioning Agent"
  
   ![Tjänster](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>Del 3: Konfigurera anslutningen till SuccessFactors och Active Directory i etableringsappen
I det här steget upprättar vi anslutning med SuccessFactors och Active Directory i Azure-portalen. 

1. Gå tillbaka till SuccessFactors till Active Directory User Provisioning App som skapats i [del 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent) i Azure-portalen.

1. Fyll i avsnittet **Administratörsautentiseringsuppgifter** på följande sätt:

   * **Administratörsanvändarnamn** – Ange användarnamnet för SuccessFactors API-användarkonto, med företags-ID bifogat. Den har formatet: **\@användarnamn companyID**

   * **Admin lösenord -** Ange lösenordet för användarkontot för SuccessFactors API. 

   * **Url till klient –** Ange namnet på slutpunkten för SuccessFactors OData API-tjänster. Ange endast värdnamnet på servern utan http eller https. Det här värdet ska se ut så här: **<api-server-namn>.successfactors.com**.

   * **Active Directory Skog -** Namnet på din Active Directory-domän, som registrerats hos agenten. Använd listrutan för att välja måldomänen för etablering. Det här värdet är vanligtvis en sträng som: *contoso.com*

   * **Active Directory-behållare -** Ange behållaren DN där agenten ska skapa användarkonton som standard.
        Exempel: *OU=Användare,DC=contoso,DC=com*
        > [!NOTE]
        > Den här inställningen spelar bara in för att skapa användarkonton om *attributet parentDistinguishedName* inte har konfigurerats i attributmappningarna. Den här inställningen används inte för användarsökning eller uppdateringsåtgärder. Hela domänunderträdet faller i sökåtgärdens omfattning.

   * **E-postmeddelande –** Ange din e-postadress och markera kryssrutan "skicka e-post om fel inträffar" .
    > [!NOTE]
    > Azure AD-etableringstjänsten skickar e-postmeddelande om etableringsjobbet hamnar i [karantäntillstånd.](/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

   * Klicka på knappen **Testa anslutning.** Om anslutningstestet lyckas klickar du på knappen **Spara** högst upp. Om det misslyckas dubbelkollar du att autentiseringsuppgifterna för SuccessFactors och AD-autentiseringsuppgifterna som konfigurerats för agentinställningarna är giltiga.
    >[!div class="mx-imgBorder"]
    >![Azure-portalen](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * När autentiseringsuppgifterna har **sparats** visas **standardmappningen Synkronisera lyckadesoläsare till Lokal Active Directory**

### <a name="part-4-configure-attribute-mappings"></a>Del 4: Konfigurera attributmappningar

I det här avsnittet ska du konfigurera hur användardata flödar från SuccessFactors till Active Directory.

1. Klicka på **Synkronisera lyckadesfaktorer användare till Lokal Active Directory**på fliken Etablering under **Mappningar.**

1. I fältet **Källobjektomfattning** kan du välja vilka uppsättningar användare i SuccessFactors som ska vara i omfång för etablering till AD genom att definiera en uppsättning attributbaserade filter. Standardomfånget är "alla användare i SuccessFactors". Exempelfilter:

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
   > Standardbeteendet för etableringsmotorn är att inaktivera/ta bort användare som inte omfattas. Detta kanske inte är önskvärt i dina SuccessFactors till AD-integrering. Om du vill åsidosätta det här standardbeteendet finns i artikeln [Hoppa över borttagning av användarkonton som inte omfattas](../app-provisioning/skip-out-of-scope-deletions.md)
  
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
> Som standard när du aktiverar etableringstjänsten initieras etableringsåtgärder för alla användare i omfånget. Om det finns fel i dataproblemen för mappning eller SuccessFactors kan etableringsjobbet misslyckas och gå in i karantäntillståndet. För att undvika detta, som en bästa praxis, rekommenderar vi att konfigurera **källobjektomfattningsfilter** och testa attributmappningarna med några testanvändare innan du startar fullständig synkronisering för alla användare. När du har kontrollerat att mappningarna fungerar och ger dig önskat resultat kan du antingen ta bort filtret eller gradvis expandera det till att omfatta fler användare.

1. På fliken **Etablering** anger du **etableringsstatusen** **till På**.

2. Klicka på **Spara**.

3. Den här åtgärden startar den första synkroniseringen, vilket kan ta ett varierande antal timmar beroende på hur många användare som finns i SuccessFactors-klienten. Du kan kontrollera förloppsindikatorn till spåret förloppet för synkroniseringscykeln. 

4. Kontrollera när som helst fliken **Granskningsloggar** i Azure-portalen för att se vilka åtgärder etableringstjänsten har utfört. Granskningsloggarna visar alla enskilda synkroniseringshändelser som utförs av etableringstjänsten, till exempel vilka användare som läses ut från SuccessFactors och sedan läggs till eller uppdateras i Active Directory. 

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
