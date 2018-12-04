---
title: 'Självstudie: Konfigurera Workday för automatisk användaretablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory för att automatiskt etablera och avetablera användarkonton till Workday.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/18/2018
ms.author: chmutali
ms.openlocfilehash: 754c3278cb01e010718fa4d3cb257acf6ffe99c9
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849861"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning-preview"></a>Självstudie: Konfigurera Workday för automatisk användar-etablering (förhandsversion)

Målet med den här självstudien är att visa dig vad du behöver utföra för att importera worker profiler från Workday till både Active Directory och Azure Active Directory, med valfritt tillbakaskrivning av e-postadress till Workday.

## <a name="overview"></a>Översikt

Den [tjänst Azure Active Directory för användaretablering](../manage-apps/user-provisioning.md) kan integreras med den [Workday Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) för att etablera användarkonton. Azure AD använder den här anslutningen för att aktivera etablering arbetsflöden följande användare:

* **Etablera användare i Active Directory** -synkronisera valda uppsättningar med användare från Workday till en eller flera Active Directory-domäner.

* **Etablera endast molnbaserade användare till Azure Active Directory** – i scenarier där lokala Active Directory inte används, användare kan etableras direkt från Workday till Azure Active Directory med Azure AD-tjänst för användaretablering. 

* **Tillbakaskrivning av e-post-adresser till Workday** -Azure AD-tjänst för användaretablering kan skriva e-postadresserna för Azure AD-användare tillbaka till Workday. 

### <a name="what-human-resources-scenarios-does-it-cover"></a>Vilka personalfrågor scenarier täcker den?

Workday användaren etablering arbetsflöden stöds av etableringstjänsten för Azure AD-användare aktivera automatisering av följande personal och scenarier för hantering av identitet livscykel:

* **Anställa nyanställda** – när en ny medarbetare har lagts till i Workday, skapas automatiskt ett användarkonto i Active Directory, Azure Active Directory och du kan också Office 365 och [andra SaaS-program som stöds av Azure AD](../manage-apps/user-provisioning.md), med återskrivning e-postadress till Workday.

* **Medarbetaren attribut och profilen uppdateringar** – när en medarbetarpost uppdateras i Workday (till exempel deras namn, avdelning eller manager), användarkontot uppdateras automatiskt i Active Directory, Azure Active Directory och du kan också Office 365 och [andra SaaS-program som stöds av Azure AD](../manage-apps/user-provisioning.md).

* **Medarbetaren uppsägningar** – när en anställd avslutas i Workday, användarkontot inaktiveras automatiskt i Active Directory, Azure Active Directory och du kan också Office 365 och [andra SaaS-program som stöds av Azure AD](../manage-apps/user-provisioning.md).

* **Medarbetare igen nyanställda** – när en anställd rehired i Workday, sina gamla kontot kan automatiskt återaktiverats eller etableras igen (beroende på dina preferenser) till Active Directory, Azure Active Directory, och du kan också Office 365 och [andra SaaS-program som stöds av Azure AD](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Vem är den här användaren etablering lösning som bäst passar för?

Den här lösningen för Workday av användaretablering finns för närvarande i offentlig förhandsversion och är idealisk för:

* Organisationer som vill ha en färdiga, Microsofts molnbaserade lösning för Workday användaretablering

* Organisationer som kräver direkta användarförsörjning från Workday till Active Directory eller Azure Active Directory

* Organisationer som kräver att användare som ska etableras med hjälp av data som hämtas från Workday HCM-modul (se [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisationer som behöver ansluta till, flytta, och låter användare att synkroniseras till en eller flera Active Directory-skogar, domäner och organisationsenheter baseras bara på Ändra användarinformation i Workday HCM-modulen (se [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisationer som använder Office 365 för e-post

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="solution-architecture"></a>Lösningsarkitektur

Det här avsnittet beskrivs slutpunkt till slutpunkt-arkitektur för vanliga hybridmiljöer för användaretablering. Det finns två relaterade flöden:

* **Auktoritativ HR-dataflöde – från Workday till en lokal Active Directory:** i det här flödet worker-händelser (till exempel nyanställda, dataöverföringar, uppsägningar) först inträffar i molnet Workday HR-klient och sedan av data flödar till den lokala aktiv Directory via Azure AD och Etableringsagenten. Beroende på händelsen, kan det leda till skapa/uppdatera/aktivera/inaktivera operations i AD.
* **E-tillbakaskrivning av Flow – från en lokal Active Directory till Workday:** när kontot datafabriken har skapats i Active Directory, den är synkroniserad med Azure AD via Azure AD Connect och e-postadressattribut som hämtas från Active Directory kan skrivas tillbaka till Workday.

![Översikt](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Dataflöde för slutpunkt till slutpunkt-användare

1. HR-teamet utför worker-transaktioner (trädkopplingar/flyttfirman/förtid uppgår eller nya anställningar/överföringar/uppsägningar) i Workday HCM-system
2. Azure AD Provisioning-tjänsten körs schemalagda synkroniseringar identiteter från Workday HR och identifierar ändringar som måste bearbetas för synkronisering med lokala Active Directory.
3. Azure AD Provisioning-tjänsten anropar den lokala AAD Connect etablering agenten med en nyttolast för begäran som innehåller AD-konto, skapa/uppdatera/aktivera/inaktivera åtgärder.
4. Azure AD Connect etablering agenten använder ett tjänstkonto för att lägga till/uppdatera AD-kontodata.
5. Azure AD Connect / AD-Synkroniseringsmotorn körs Deltasynkronisering för att hämta uppdateringar i AD.
6. Active Directory-uppdateringarna har synkroniserats med Azure Active Directory.
7. Om anslutningen för tillbakaskrivning av Workday har konfigurerats är det skrivning hakar e-attributet till Workday, baserat på det matchande attribut som används.


## <a name="planning-your-deployment"></a>Planera distributionen

Innan du börjar din Workday-integrering, kontrollera krav nedan och Läs följande riktlinjer för hur som motsvarar dina nuvarande Active Directory-arkitekturen och krav för användaretablering med lösningar som tillhandahålls av Azure Active Directory.

### <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En giltig Azure AD Premium P1-prenumeration med global administratör för användaråtkomst
* En klient för implementering av Workday för testning och integration
* Administratörsbehörighet i Workday att skapa en systemanvändare integrering och göra ändringar till medarbetare testdata för testning
* För användaretablering till Active Directory, en server som kör Windows Server 2012 eller senare med .NET 4.7 + runtime krävs till värden i [lokala etableringsagenten](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) för synkronisering mellan Active Directory och Azure AD

### <a name="planning-considerations"></a>Planera överväganden

Azure AD tillhandahåller en omfattande uppsättning etablering kopplingar för att hjälpa dig att lösa etablering och identitet livscykelhantering från Workday till Active Directory, Azure AD och SaaS-appar och mer. Vilka funktioner du använder och hur du ställer in lösningen varierar beroende på organisationens miljö och krav. Som ett första steg går du igenom hur många av följande finns på plats och distribuerade i din organisation:

* Hur många Active Directory-skogar är används?
* Hur många Active Directory-domäner är används?
* Hur många Active Directory-organisationsenheter (OU) är i användning?
* Hur många Azure Active Directory-klienter är används?
* Finns det några användare som behöver etableras till både Active Directory och Azure Active Directory (till exempel ”” hybridanvändare)?
* Finns det några användare som behöver etableras till Azure Active Directory, men inte Active Directory (till exempel ”molnbaserad” användare)?
* Behöver användare e-postadresser skrivas tillbaka till Workday?

När du har svaren på dessa frågor kan planera du dina Workday etablering distribution genom att följa anvisningarna nedan.

#### <a name="planning-deployment-of-aad-connect-provisioning-agent"></a>Planera distribution av AAD Connect etablering Agent

Workday etableringen av AD-användare lösning kräver distribution av en eller flera etablering agenter på servrar som kör Windows 2012 R2 eller senare med minst 4 GB RAM-minne och .NET 4.7 + runtime. Följande överväganden måste beaktas innan du installerar agenten etablering:

* Se till att värdservern kör agenten etablering har nätverksåtkomst till måldomänen AD
* Guiden för konfiguration av etablering agenten registrerar agenten med Azure AD-klienten och registreringen kräver åtkomst till *. msappproxy.net på port 8082. Kontrollera att utgående brandväggsregler är som att den här kommunikationen.
* Etablering agenten använder ett tjänstkonto för att kommunicera med lokalt AD-domäner. Du rekommenderas att du skapar ett tjänstkonto med användarbehörigheter egenskaper Läs/Skriv- och ett lösenord som inte upphör att gälla före installationen av agenten.  
* Vid etablering agentkonfiguration väljer du domänkontrollanter som ska hantera etablering begäranden. Om du har flera geografiskt utspridda domänkontrollanter kan installera agenten etablering på samma plats som din prioriterade domänkontrollanter att förbättra tillförlitlighet och prestanda för slutpunkt till slutpunkt-lösning
* För hög tillgänglighet som du kan distribuera flera etablering agenten och registrera den för att hantera samma uppsättning lokala AD-domäner.

> [!IMPORTANT]
> Microsoft rekommenderar att du har minst 3 etablering agenter som konfigurerats med Azure AD-klienten för hög tillgänglighet i produktionsmiljöer.

#### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Att välja etablering connector-appar som kan distribueras

När du integrerar Workday och Active Directory, finns det flera käll- och system för att anses vara:

| Källsystem | Målsystemet | Anteckningar |
| ---------- | ---------- | ---------- |
| Workday | Active Directory-domän | Varje domän hanteras som ett distinkt målsystem |
| Workday | Azure AD-klient | Efter behov för molnexklusiva användare |
| Active Directory-skog | Azure AD-klient | Det här flödet hanteras av AAD Connect i dag |
| Azure AD-klient | Workday | För tillbakaskrivning av e-postadresser |

För att underlätta etablering av arbetsflöden mellan Workday och Active Directory, tillhandahåller Azure AD flera etablering connector-appar som du kan lägga till från appgalleriet för Azure AD:

![AAD-Appgalleriet](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday till Active Directory-etablering** – den här appen förenklar konto användarförsörjning från Workday till en enda Active Directory-domän. Om du har flera domäner, kan du lägga till en instans av den här appen från Azure AD-appgalleriet för varje Active Directory-domän som du måste distribuera till.

* **Workday till Azure AD-etablering** – AAD Connect är verktyget som ska användas för att synkronisera Active Directory användare till Azure Active Directory, den här appen kan användas för att underlätta etablering av endast molnbaserade användare från Workday till en enda Azure Active Directory-klient.

* **Workday tillbakaskrivning av** -den här appen förenklar tillbakaskrivningen av användarens e-postadresser från Azure Active Directory till Workday.

> [!TIP]
> Appen regelbundna ”Workday” används för att konfigurera enkel inloggning mellan Workday och Azure Active Directory. 

#### <a name="determine-workday-to-ad-user-attribute-mapping-and-transformations"></a>Fastställa Workday till AD-användare attributmappning och transformeringar

Beakta följande frågor innan du konfigurerar användaretablering för en Active Directory-domän. Svaren på dessa frågor avgör hur din Omfångsfilter och attributmappningar måste anges.

* **Vad användare i Workday måste etableras för den här Active Directory-skog?**

   * *Exempel: Användare där Workday ”företagets” attributet innehåller värdet ”Contoso” och ”Worker_Type”-attributet innehåller ”vanliga”*

* **Hur dirigeras användare till olika organisationsenheter (OU)?**

   * *Exempel: Användare dirigeras till organisationsenheter som motsvarar en arbetsplats, enligt definitionen i Workday ”kommuner” och ”Country_Region_Reference”-attribut*

* **Hur ska du fylls följande attribut i Active Directory?**

   * Nätverksnamn (cn)
      * *Exempel: Använd Workday User_ID-värdet som anges av personalfrågor*
      
   * Anställnings-ID (employeeId)
      * *Exempel: Använd Workday Worker_ID värdet*
      
   * SAM-kontonamn (sAMAccountName)
      * *Exempel: Använd det värde som Workday User_ID kan filtreras via en Azure AD etablering uttryck att ta bort ogiltiga tecken*
      
   * Användarens huvudnamn (userPrincipalName)
      * *Exempel: Använda Workday User_ID-värde med en Azure AD etablering uttryck för att lägga till ett domännamn*

* **Hur ska användarna matchas mellan Workday och Active Directory?**

  * *Exempel: Användare med en arbetsdag ”Worker_ID” värde matchas med Active Directory-användare där ”employeeID” har samma värde. Om Worker_ID-värdet inte finns i Active Directory, skapar du en ny användare.*
  
* **Innehåller Active Directory-skogen redan användar-ID krävs för den matchande logiken att fungera?**

  * *Exempel: Om det här är en ny Workday-distribution, rekommenderas att Active Directory är förifyllda med rätt Workday Worker_ID värden (eller unika ID-värdet med) att hålla den matchande logiken som är så enkelt som möjligt.*



Hur du skapar och konfigurerar apparna särskilda connector etablering är föremål för de återstående avsnitten i den här självstudien. Vilka appar som du väljer att konfigurera beror på vilka system som du behöver etablera till, och hur många Active Directory-domäner och Azure AD klienter finns i din miljö.



## <a name="configure-integration-system-user-in-workday"></a>Konfigurera integration systemanvändare i Workday

Ett vanligt krav på alla Workday etablering kopplingar är de kräver autentiseringsuppgifter för ett Workday-integrering systemkonto att ansluta till API: et för Workday Human Resources. Det här avsnittet beskriver hur du skapar en användare i ett integrerat system i Workday.

> [!NOTE]
> Det är möjligt att kringgå den här proceduren och i stället använda ett globalt administratörskonto för Workday som integrationskontot system. Detta fungerar bra för demonstrationer, men det rekommenderas inte för Produktionsdistribution.

### <a name="create-an-integration-system-user"></a>Skapa en användare i ett integrerat system

**Skapa en användare i ett integrerat system:**

1. Logga in på din Workday-klient med ett administratörskonto. I den **Workday programmet**, ange skapa användare i sökrutan och klicka sedan på **skapa Integration systemanvändare**.

    ![Skapa användare](./media/workday-inbound-tutorial/wd_isu_01.png "skapa användare")
2. Slutför den **skapa Integration systemanvändare** uppgift genom att ange ett användarnamn och lösenord för en ny Integration systemanvändare.  
 * Lämna den **kräver nya lösenord vid nästa inloggning** alternativet är avmarkerat, eftersom den här användaren loggar in via programmering.
 * Lämna den **minuter för sessionens Timeout** med standardvärdet 0, vilket förhindrar att användarens sessioner timeout för tidigt.
 * Välj alternativet **gör inte tillåta Användargränssnittet sessioner** eftersom det ger dig ett extra säkerhetslager som förhindrar att en användare med lösenordet för integration-system som loggar in på Workday. 

    ![Skapa Integration systemanvändare](./media/workday-inbound-tutorial/wd_isu_02.png "skapa Integration systemanvändare")

### <a name="create-a-security-group"></a>Skapa en säkerhetsgrupp
I det här steget ska du skapa en säkerhetsgrupp för obegränsad integration system i Workday och tilldela integration systemanvändaren som skapades i föregående steg i den här gruppen.

**Skapa en säkerhetsgrupp:**

1. Ange skapa säkerhetsgrupp i sökrutan och klicka sedan på **skapa säkerhetsgrupp**.

    ![CreateSecurity grupp](./media/workday-inbound-tutorial/wd_isu_03.png "CreateSecurity grupp")
2. Slutför den **skapa säkerhetsgrupp** uppgift.  
   * Välj **integrering Systemsäkerhetsgrupp (obegränsat)** från den **typ Backups säkerhetsgrupp** listrutan.

    ![CreateSecurity grupp](./media/workday-inbound-tutorial/wd_isu_04.png "CreateSecurity grupp")

3. När den säkerhetsgruppen har skapats, visas en sida där du kan tilldela medlemmar till gruppen. Lägg till den nya integration systemanvändaren i den här säkerhetsgruppen och välj lämplig organisationens omfång.
![Redigera säkerhetsgrupp](./media/workday-inbound-tutorial/wd_isu_05.png "redigera grupp")
 
### <a name="configure-domain-security-policy-permissions"></a>Konfigurera principen för domänbehörigheter
I det här steget ska du ge ”domain security” principbehörigheter för worker-data i gruppen.

**Du konfigurerar principen för domänbehörigheter:**

1. Ange **domän säkerhetskonfiguration** i sökrutan och sedan klicka på länken **Domain Security konfigurationsrapport**.  

    ![Domän-säkerhetsprinciper](./media/workday-inbound-tutorial/wd_isu_06.png "domän säkerhetsprinciper")  
2. I den **domän** textrutan, Sök efter följande domäner och lägga till dem i filtret en i taget.  
   * *Extern etablering*
   * *Worker Data: Public Worker rapporter*
   * *Personliga Data: Arbeta kontaktinformation*
   * *Worker Data: Alla positioner*
   * *Worker Data: Current bemanning Information*
   * *Worker Data: Företag titel på Worker profil*
 
    ![Domän-säkerhetsprinciper](./media/workday-inbound-tutorial/wd_isu_07.png "domän säkerhetsprinciper")  

    ![Domän-säkerhetsprinciper](./media/workday-inbound-tutorial/wd_isu_08.png "domän säkerhetsprinciper") 

    Klicka på **OK**.

3. I den rapport som visas, väljer du ellipsen (...) visas bredvid **externa Kontoetablering** och klicka på menyalternativet **domän -> Redigera principen säkerhetsbehörigheter**

    ![Domän-säkerhetsprinciper](./media/workday-inbound-tutorial/wd_isu_09.png "domän säkerhetsprinciper")  

4. På den **redigera domän princip säkerhetsbehörigheter** sida, rulla ned till avsnittet **integrering behörigheter**. Klicka på tecknet ”+” för att lägga till gruppen integration system i listan över säkerhetsgrupper med **hämta** och **placera** integrering behörigheter.

    ![Redigera](./media/workday-inbound-tutorial/wd_isu_10.png "redigera behörighet")  

5. Klicka på tecknet ”+” för att lägga till gruppen integration system i listan över säkerhetsgrupper med **hämta** och **placera** integrering behörigheter.

    ![Redigera](./media/workday-inbound-tutorial/wd_isu_11.png "redigera behörighet")  

6. Upprepa steg 3 – 5 ovan för var och en av dessa återstående säkerhetsprinciper:

   | Åtgärd | Säkerhetsprincip för domän |
   | ---------- | ---------- | 
   | Hämta och skicka | Worker Data: Public Worker rapporter |
   | Hämta och skicka | Personliga Data: Arbeta kontaktinformation |
   | Hämta | Worker Data: Alla positioner |
   | Hämta | Worker Data: Current bemanning Information |
   | Hämta | Worker Data: Företag titel på Worker profil |

### <a name="configure-business-process-security-policy-permissions"></a>Konfigurera business process säkerhetsbehörigheter princip
I det här steget ska du ge ”företag processen security” principbehörigheter för worker-data i gruppen. Detta krävs för att konfigurera tillbakaskrivning av Workday app connector. 

**Du konfigurerar säkerhetsbehörigheter för principen på företag processen:**

1. Ange **processen företagsprincip** i sökrutan och sedan klicka på länken **redigera säkerhetsprincip för företag processen** uppgift.  

    ![Säkerhetsprinciper för företag-processen](./media/workday-inbound-tutorial/wd_isu_12.png "säkerhetsprinciper för företag-processen")  

2. I den **företag processtypen** textrutan, Sök efter *Kontakta* och välj **kontakta ändra** affärsprocesser och klicka på **OK**.

    ![Säkerhetsprinciper för företag-processen](./media/workday-inbound-tutorial/wd_isu_13.png "säkerhetsprinciper för företag-processen")  

3. På den **redigera säkerhetsprincip för företag processen** , bläddrar du till den **underhålla kontaktinformation (webbtjänst)** avsnittet.

    ![Säkerhetsprinciper för företag-processen](./media/workday-inbound-tutorial/wd_isu_14.png "säkerhetsprinciper för företag-processen")  

4. Välj och Lägg till den nya säkerhetsgruppen för integration-system i listan över säkerhetsgrupper som kan starta web services-begäran. Klicka på **klar**. 

    ![Säkerhetsprinciper för företag-processen](./media/workday-inbound-tutorial/wd_isu_15.png "säkerhetsprinciper för företag-processen")  

 
### <a name="activate-security-policy-changes"></a>Aktivera säkerhetsprincipsändringar

**Aktivera säkerhetsprincipsändringar:**

1. Ange aktivera i sökrutan och sedan klicka på länken **aktivera väntande Säkerhetsprincipsändringar**.

    ![Aktivera](./media/workday-inbound-tutorial/wd_isu_16.png "aktivera") 
2. Starta Aktivera väntande Säkerhetsprincipsändringar aktiviteten genom att ange en kommentar i granskningssyfte och klicka sedan på **OK**. 

    ![Aktivera väntande Security](./media/workday-inbound-tutorial/wd_isu_17.png "aktivera väntande säkerhet")  
1. Slutföra uppgiften på nästa skärm genom att markera kryssrutan **Bekräfta**, och klicka sedan på **OK**.

    ![Aktivera väntande Security](./media/workday-inbound-tutorial/wd_isu_18.png "aktivera väntande säkerhet")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfigurera användaretablering från Workday till Active Directory

Följ dessa instruktioner för att konfigurera användarkonto etablering från Workday till varje Active Directory-domän inom omfånget för din integrering.

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>Del 1: Installera och konfigurera den lokala etablering agenter

För att etablera till Active Directory lokalt, måste en agent installeras på en server med .NET 4.7 + Framework och nätverket åtkomst till den önskade Active Directory-domäner.

> [!TIP]
> Du kan kontrollera version av .NET framework på servern med hjälp av anvisningarna [här](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed).
> Om servern inte har .NET 4.7 eller senare installerat, du kan ladda ned det från [här](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).  

När du har distribuerat .NET 4.7 + kan du ladda ned den **[lokala etableringsagenten här](https://go.microsoft.com/fwlink/?linkid=847801)** och följ anvisningarna nedan för att slutföra agentkonfigurationen.

1. Logga in på Windows-servern där du vill installera den nya agenten.
2. Starta installationsprogrammet för etablering agenten, Godkänn villkoren och klicka på den **installera** knappen.
![Installera skärmen](./media/workday-inbound-tutorial/pa_install_screen_1.png "installera skärmen")

3. När installationen är klar, startar guiden och du ser den **Anslut Azure AD** skärmen. Klicka på den **autentisera** knapp för att ansluta till din Azure AD-instans.
![Ansluter Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "ansluter Azure AD")

4. Autentisera till din Azure AD-instans med hjälp av autentiseringsuppgifter som Global administratör. 
![Administratören Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Admin Auth")

5. Efter en lyckad autentisering med Azure AD, visas den **Anslut Active Directory** skärmen. I det här steget anger du ditt AD-domännamn och klicka på den **Lägg till katalog** knappen.
![Lägg till katalog](./media/workday-inbound-tutorial/pa_install_screen_4.png "Lägg till katalog")

6. Nu uppmanas du att ange de autentiseringsuppgifter som krävs för att ansluta till AD-domänen. På samma skärm, kan du använda den **Välj domain controller prioritet** ange domänkontrollanter som agenten ska använda för att skicka etableringsbegäranden.
![Inloggningsuppgifter för domänen](./media/workday-inbound-tutorial/pa_install_screen_5.png "inloggningsuppgifter för domänen")

7. När du har konfigurerat domänen visar installationsprogrammet en lista över konfigurerade domäner. På den här skärmen kan du upprepa steg #5 och 6 för # att lägga till fler domäner eller klicka på **nästa** att fortsätta till registreringen. 
![Konfigurerade domäner](./media/workday-inbound-tutorial/pa_install_screen_6.png "konfigurerats domäner")

   > [!NOTE]
   > Om du har flera AD-domäner (t.ex. na.contoso.com, emea.contoso.com) och sedan Lägg till varje domän individuellt i listan. Endast att lägga till den överordnade domänen (t.ex. contoso.com) inte är tillräckligt och vi rekommenderar att du registrerar varje underordnad domän med agenten. 

8. Granska konfigurationsinformationen och klicka på **Bekräfta** att registrera agenten. 
![Bekräfta skärmen](./media/workday-inbound-tutorial/pa_install_screen_7.png "bekräfta skärmen")

9. Guiden visar förloppet för registreringen av.
![Agentregistreringen](./media/workday-inbound-tutorial/pa_install_screen_8.png "Agentregistreringen")

10. När agentregistreringen är klar, kan du klicka på **avsluta** vill avsluta guiden. 
![Avsluta skärmen](./media/workday-inbound-tutorial/pa_install_screen_9.png "avsluta skärmen")

11. Verifiera installationen av agenten och kontrollera att den körs genom att öppna snapin-modulen ”Tjänster” och Sök efter tjänsten med namnet ”Microsoft Azure AD Connect etablering Agent” ![tjänster](./media/workday-inbound-tutorial/services.png)  


**Agenten felsökning**

Den [Windows-händelseloggen](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) på Windows Server innehåller datorn som är värd för agenten händelser för alla åtgärder som utförs av agenten. Visa dessa händelser:
    
1. Öppna **Eventvwr.msc**.
2. Välj **Windows loggar > program**.
3. Visa alla händelser som loggats under källan **AAD. Connect.ProvisioningAgent**. 
4. Sök efter fel och varningar.

    
### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Del 2: Lägga till appen etablering connector och skapa anslutningen till Workday

**Konfigurera Workday till Active Directory-etablering:**

1.  Gå till <https://portal.azure.com>

2.  I det vänstra navigeringsfältet väljer **Azure Active Directory**

3.  Välj **företagsprogram**, sedan **alla program**.

4.  Välj **lägga till ett program**, och välj den **alla** kategori.

5.  Sök efter **Workday Provisioning för Active Directory**, och Lägg till appen från galleriet.

6.  När appen har lagts till och appen informationsskärmen är visas, väljer **etablering**

7.  Ändra den **etablering** **läge** till **automatisk**

8.  Slutför den **administratörsautentiseringsuppgifter** avsnittet på följande sätt:

   * **Administratören Username** – ange användarnamnet för system-kontot Workday-integrering med klientens domännamn tillagt. **Bör se ut ungefär som: username@contoso4**

   * **Adminlösenord –** anger du lösenordet för kontot system Workday-integrering

   * **Klient-URL –** anger du Webbadressen till Workday web services-slutpunkt för din klient. Det bör se ut: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, där contoso4 ersätts med rätt klient-namn och wd3 impl ersätts med rätt miljö-sträng.

   * **Active Directory-skog -** ”namn” på din Active Directory-domän, eftersom registrerad med agenten. Detta är vanligtvis en sträng som: *contoso.com*

   * **Active Directory-behållare –** ange behållaren DN där agenten ska skapa användarkonton som standard. 
        Exempel: *OU = standardanvändare, OU = Users, DC = contoso, DC = test*
> [!NOTE]
> Den här inställningen kommer endast betydelse för användaren kontoskapningar om den *parentDistinguishedName* attributet har inte konfigurerats på attributmappningarna. Den här inställningen används inte för användarsökning eller uppdaterar åtgärder. Hela domänen sub trädet ingår i omfånget för search-åtgärd.
   * **E-postmeddelande –** ange din e-postadress och markera kryssrutan ”Skicka e-post om fel uppstår”.
> [!NOTE]
> Azure AD Provisioning-tjänsten skickar e-postmeddelande om Etableringsjobbet hamnar i en [karantän](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) tillstånd.

   * Klicka på den **Testanslutningen** knappen. Om Anslutningstestet lyckas klickar du på den **spara** längst upp. Om det misslyckas kontrollerar du att Workday-autentiseringsuppgifter och AD konfigurerat på agentinställningen är giltiga.

![Azure Portal](./media/workday-inbound-tutorial/wd_1.png)

### <a name="part-2-configure-attribute-mappings"></a>Del 2: Konfigurera attributmappningar 

I det här avsnittet konfigurerar du hur informationen flödar från Workday till Active Directory.

1.  På fliken etablering under **mappningar**, klickar du på **synkronisera Workday arbetare till den lokala**.

2.  I den **omfång för källobjekt** fältet, kan du välja vilka uppsättningar med användare i Workday ska vara i omfånget för etablering i AD, genom att definiera en uppsättning attributbaserade filter. Standardvärde är ”alla användare i Workday”. Exempel filter:

   * Exempel: Omfång för användare med Worker-ID: N mellan 1000000 och 2000000

      * Attributet: WorkerID

      * Operatorn: REGEX matchning

      * Värde: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exempel: Endast anställda och inte tillfällig arbetare 

      * Attributet: EmployeeID

      * Operatorn: Är inte NULL

3.  I den **åtgärder för målobjekt** fältet du globalt filtrera vilka åtgärder ska kunna utföras på Active Directory. **Skapa** och **uppdatering** är de vanligaste.

4.  I den **attributmappningar** avsnittet, kan du definiera hur enskilda Workday attribut mappar till Active Directory-attribut.

5. Klicka på en befintlig attributmappning att uppdatera den, eller klicka på **Lägg till ny mappning** längst ned på skärmen för att lägga till nya mappningar. En enskild attributmappning har stöd för dessa egenskaper:

      * **Mappningstyp**

         * **Direkt** – skriver värdet för attributet Workday till attributet AD utan ändringar

         * **Konstant** -skriva en statisk, konstant strängvärde till AD-attribut

         * **Uttrycket** – kan du skriva ett anpassat värde till attributet AD baserat på en eller flera Workday-attribut. [Mer information finns i den här artikeln på uttryck](../manage-apps/functions-for-customizing-application-data.md).

      * **Källattributet** -användarattributet från Workday. Om attributet som du letar efter inte finns, se [anpassning av listan över Workday-användarattribut](#customizing-the-list-of-workday-user-attributes).

      * **Standardvärde** – valfritt. Om källattributet har ett tomt värde, skriver mappningen du det här värdet i stället.
            De vanligaste konfiguration är att lämna fältet tomt.

      * **Målattribut** – användarattribut i Active Directory.

      * **Matcha objekt med hjälp av det här attributet** – oavsett om den här mappningen ska användas för att unikt identifiera användare mellan Workday och Active Directory. Detta är vanligtvis inställt på Worker-ID-fältet för Workday, vilket vanligtvis är mappad till ett anställnings-ID-attribut i Active Directory.

      * **Matchningsprioritet** – flera matchande attribut kan anges. När det finns flera, utvärderas de i den ordning som anges av det här fältet. När en matchning hittas, matchar ingen ytterligare attribut utvärderas.

      * **Tillämpa den här mappningen**
       
         * **Alltid** – tillämpa den här mappningen för både skapa användare och uppdatera åtgärder

         * **Endast när skapas** -tillämpa den här mappningen endast på åtgärder för skapande av användare

6. Om du vill spara din mappningar, klickar du på **spara** överst i avsnittet attribut mappar.

![Azure Portal](./media/workday-inbound-tutorial/WD_2.PNG)

**Nedan visas några exempel attributmappningar Workday och Active Directory, med vissa vanliga uttryck**

-   Det uttryck som mappar till attributet parentDistinguishedName används för att etablera en användare till olika organisationsenheter baserat på en eller flera Workday källa attribut. Det här exemplet här placerar användare i olika organisationsenheter baserat på vilken stad de finns i.

-   UserPrincipalName-attribut i Active Directory skapas genom att sammanfoga Workday användar-ID med ett domänsuffix

-   [Det finns dokumentation om hur du skriver uttryck här](../manage-apps/functions-for-customizing-application-data.md). Det finns exempel på hur du tar bort specialtecken.

  
| WORKDAY ATTRIBUT | ACTIVE DIRECTORY-ATTRIBUT |  MATCHANDE ID? | SKAPA / UPPDATERA |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Ja** | Skrivna på enbart skapa |
| **Användar-ID**    |  CN    |   |   Skrivna på enbart skapa |
| **Ansluta till (”@”, [användar-ID], ”contoso.com”)**   | userPrincipalName     |     | Skrivna på enbart skapa 
| **Ersätt(Mid(Ersätt(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\ \\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    SAMAccountName            |     |         Skrivna på enbart skapa |
| **Växel (\[Active\],, ”0”, ”True”, ”1”, ”FALSKT”)** |  accountDisabled      |     | Skapa och uppdatera |
| **Förnamn**   | givenName       |     |    Skapa och uppdatera |
| **Efternamn**   |   SN   |     |  Skapa och uppdatera |
| **PreferredNameData**  |  displayName |     |   Skapa och uppdatera |
| **Företagets**         | Företagets   |     |  Skapa och uppdatera |
| **SupervisoryOrganization**  | Avdelning  |     |  Skapa och uppdatera |
| **ManagerReference**   | ansvarig  |     |  Skapa och uppdatera |
| **BusinessTitle**   |  rubrik     |     |  Skapa och uppdatera | 
| **AddressLineData**    |  streetAddress  |     |   Skapa och uppdatera |
| **Kommuner**   |   L   |     | Skapa och uppdatera |
| **CountryReferenceTwoLetter**      |   CO |     |   Skapa och uppdatera |
| **CountryReferenceTwoLetter**    |  c  |     |         Skapa och uppdatera |
| **CountryRegionReference** |  St     |     | Skapa och uppdatera |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Skapa och uppdatera |
| **Postnummer**  |   Postnummer  |     | Skapa och uppdatera |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Skapa och uppdatera |
| **Fax**      | facsimileTelephoneNumber     |     |    Skapa och uppdatera |
| **mobila**  |    mobila       |     |       Skapa och uppdatera |
| **LocalReference** |  preferredLanguage  |     |  Skapa och uppdatera |                                               
| **Växel (\[kommuner\]”, OU = standardanvändare, OU = användare, OU = standard, OU = platser, DC = contoso, DC = com”, ”Dallas” ”, OU = standardanvändare, OU = användare, OU = Dallas, OU = platser, DC = contoso, DC = com”, ”Austin” ”, OU = standardanvändare, OU = Användare, OU = Austin, OU = platser, DC = contoso, DC = com ”,” Seattle ””, OU = standardanvändare, OU = användare, OU = Seattle, OU = platser, DC = contoso, DC = com ”,” London ””, OU = standardanvändare, OU = användare, OU = London, OU = platser, DC = contoso, DC = com ”)**  | parentDistinguishedName     |     |  Skapa och uppdatera |
  


### <a name="part-4-start-the-service"></a>Del 4: Starta tjänsten
När delar 1 – 3 har slutförts, kan du starta etableringstjänsten tillbaka i Azure-portalen.

1.  I den **etablering** fliken genom att ange den **Etableringsstatus** till **på**.

2. Klicka på **Spara**.

3. Detta startar den första synkroniseringen, vilket kan ta flera timmar beroende på hur många användare finns i Workday variabeln.

4. När som helst kontrollera den **granskningsloggar** flik i Azure portal för att se vilka åtgärder som etableringstjänsten har utförts. Granskningsloggarna innehåller alla enskilda synkroniseringar händelser utförs av etableringstjänsten, till exempel vilka användare som ska läsa från Workday och sedan därefter läggs till eller uppdateras till Active Directory. **[Finns i guiden för etablering reporting för detaljerade anvisningar om hur du tolkar granskningsloggar](../manage-apps/check-status-user-account-provisioning.md)**

1.  Kontrollera den [Windows-händelseloggen](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) på Windows Server-datorn som är värd för agenten för nya fel eller varningar. Dessa händelser kan visas genom att starta **Eventvwr.msc** på servern och välja **Windows loggar > program**. Alla etablering-meddelanden loggas under källan **AADSyncAgent**.

6. En klar skrivs en sammanfattning av granskningsrapporten den **etablering** fliken enligt nedan.

![Azure Portal](./media/workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Konfigurera användaretablering till Azure Active Directory
Hur du konfigurerar etablering i Azure Active Directory beror på dina etablering krav som beskrivs i tabellen nedan.

| Scenario | Lösning |
| -------- | -------- |
| **Användarna behöver etableras till Active Directory och Azure AD** | Använd  **[AAD Connect](../hybrid/whatis-hybrid-identity.md)** |
| **Användarna behöver bara ska etableras till Active Directory** | Använd  **[AAD Connect](../hybrid/whatis-hybrid-identity.md)** |
| **Användarna behöver etableras till Azure AD endast (moln)** | Använd den **Workday till Azure Active Directory etableras** app i appgalleriet |

Anvisningar om hur du konfigurerar Azure AD Connect finns i den [dokumentation för Azure AD Connect](../hybrid/whatis-hybrid-identity.md).

I följande avsnitt beskrivs hur du konfigurerar en anslutning mellan Workday och Azure AD för att etablera molnexklusiva användare.

> [!IMPORTANT]
> Endast följer du anvisningarna nedan om du har endast molnbaserade användare som behöver etableras till Azure AD och inte en lokal Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Del 1: Lägga till Azure AD-etablering connector-app och skapa anslutningen till Workday

**Konfigurera Workday till Azure Active Directory-etablering för molnexklusiva användare:**

1.  Gå till <https://portal.azure.com>.

2.  I det vänstra navigeringsfältet väljer **Azure Active Directory**

3.  Välj **företagsprogram**, sedan **alla program**.

4.  Välj **lägga till ett program**, och välj sedan den **alla** kategori.

5.  Sök efter **Workday till Azure AD-etablering**, och Lägg till appen från galleriet.

6.  När appen har lagts till och appen informationsskärmen är visas, väljer **etablering**

7.  Ändra den **etablering** **läge** till **automatisk**

8.  Slutför den **administratörsautentiseringsuppgifter** avsnittet på följande sätt:

   * **Administratören Username** – ange användarnamnet för system-kontot Workday-integrering med klientens domännamn tillagt. Bör se ut ungefär som: username@contoso4

   * **Adminlösenord –** anger du lösenordet för kontot system Workday-integrering

   * **Klient-URL –** anger du Webbadressen till Workday web services-slutpunkt för din klient. Det bör se ut: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, där contoso4 ersätts med rätt klient-namn och wd3 impl ersätts med rätt miljö-sträng. Om den här URL: en inte är känd, arbeta med din Workday-integrering-partner eller support representant för att fastställa rätt Webbadress för användning.

   * **E-postmeddelande –** ange din e-postadress och markera kryssrutan ”Skicka e-post om fel uppstår”.

   * Klicka på den **Testanslutningen** knappen.

   * Om Anslutningstestet lyckas klickar du på den **spara** längst upp. Om det misslyckas kontrollerar du att Workday-URL och autentiseringsuppgifter är giltiga i Workday.

### <a name="part-2-configure-attribute-mappings"></a>Del 2: Konfigurera attributmappningar 

I det här avsnittet konfigurerar du hur informationen flödar från Workday till Azure Active Directory för molnexklusiva användare.

1. På fliken etablering under **mappningar**, klickar du på **synkronisera arbetare till Azure AD**.

2. I den **omfång för källobjekt** fältet, kan du välja vilka uppsättningar med användare i Workday ska vara i omfånget för etablering till Azure AD genom att definiera en uppsättning attributbaserade filter. Standardvärde är ”alla användare i Workday”. Exempel filter:

   * Exempel: Omfång för användare med Worker-ID: N mellan 1000000 och 2000000

      * Attributet: WorkerID

      * Operatorn: REGEX matchning

      * Värde: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exempel: Endast tillfällig arbetare och inte fast anställda

      * Attributet: ContingentID

      * Operatorn: Är inte NULL

3. I den **åtgärder för målobjekt** fältet du globalt filtrera vilka åtgärder ska kunna utföras på Azure AD. **Skapa** och **uppdatering** är de vanligaste.

4. I den **attributmappningar** avsnittet, kan du definiera hur enskilda Workday attribut mappar till Active Directory-attribut.

5. Klicka på en befintlig attributmappning att uppdatera den, eller klicka på **Lägg till ny mappning** längst ned på skärmen för att lägga till nya mappningar. En enskild attributmappning har stöd för dessa egenskaper:

   * **Mappningstyp**

      * **Direkt** – skriver värdet för attributet Workday till attributet AD utan ändringar

      * **Konstant** -skriva en statisk, konstant strängvärde till AD-attribut

      * **Uttrycket** – kan du skriva ett anpassat värde till attributet AD baserat på en eller flera Workday-attribut. [Mer information finns i den här artikeln på uttryck](../manage-apps/functions-for-customizing-application-data.md).

   * **Källattributet** -användarattributet från Workday. Om attributet som du letar efter inte finns, se [anpassning av listan över Workday-användarattribut](#customizing-the-list-of-workday-user-attributes).

   * **Standardvärde** – valfritt. Om källattributet har ett tomt värde, skriver mappningen du det här värdet i stället.
            De vanligaste konfiguration är att lämna fältet tomt.

   * **Målattribut** – användarattribut i Azure AD.

   * **Matcha objekt med hjälp av det här attributet** – oavsett om den här mappningen ska användas för att unikt identifiera användare mellan Workday och Azure AD. Detta är vanligtvis inställt på Worker-ID-fältet för Workday, vilket vanligtvis är mappad till attributet anställnings-ID (ny) eller ett tilläggsattribut i Azure AD.

   * **Matchningsprioritet** – flera matchande attribut kan anges. När det finns flera, utvärderas de i den ordning som anges av det här fältet. När en matchning hittas, matchar ingen ytterligare attribut utvärderas.

   * **Tillämpa den här mappningen**

     * **Alltid** – tillämpa den här mappningen för både skapa användare och uppdatera åtgärder

     * **Endast när skapas** -tillämpa den här mappningen endast på åtgärder för skapande av användare

6. Om du vill spara din mappningar, klickar du på **spara** överst i avsnittet attribut mappar.

### <a name="part-3-start-the-service"></a>Del 3: Starta tjänsten
När du delar 1 – 2 har slutförts, kan du starta etableringstjänsten.

1. I den **etablering** fliken genom att ange den **Etableringsstatus** till **på**.

2. Klicka på **Spara**.

3. Detta startar den första synkroniseringen, vilket kan ta flera timmar beroende på hur många användare finns i Workday variabeln.

4. Enskilda synkronisera händelser kan visas i den **granskningsloggar** fliken. **[Finns i guiden för etablering reporting för detaljerade anvisningar om hur du tolkar granskningsloggar](../manage-apps/check-status-user-account-provisioning.md)**

5. En klar skrivs en sammanfattning av granskningsrapporten den **etablering** fliken enligt nedan.

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Konfigurera tillbakaskrivning av e-postadresser till Workday
Följ dessa instruktioner för att konfigurera tillbakaskrivning av användare e-postadresser från Azure Active Directory till Workday.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Del 1: Lägga till appen etablering connector och skapa anslutningen till Workday

**Att konfigurera tillbakaskrivning av Workday-anslutningen:**

1. Gå till <https://portal.azure.com>

2. I det vänstra navigeringsfältet väljer **Azure Active Directory**

3. Välj **företagsprogram**, sedan **alla program**.

4. Välj **lägga till ett program**och välj sedan den **alla** kategori.

5. Sök efter **Workday tillbakaskrivning av**, och Lägg till appen från galleriet.

6. När appen har lagts till och appen informationsskärmen är visas, väljer **etablering**

7. Ändra den **etablering** **läge** till **automatisk**

8. Slutför den **administratörsautentiseringsuppgifter** avsnittet på följande sätt:

   * **Administratören Username** – ange användarnamnet för system-kontot Workday-integrering med klientens domännamn tillagt. Bör se ut ungefär som: username@contoso4

   * **Adminlösenord –** anger du lösenordet för kontot system Workday-integrering

   * **Klient-URL –** anger du Webbadressen till Workday web services-slutpunkt för din klient. Det bör se ut: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, där contoso4 ersätts med rätt klient-namn och wd3 impl ersätts med rätt miljö-sträng (vid behov).

   * **E-postmeddelande –** ange din e-postadress och markera kryssrutan ”Skicka e-post om fel uppstår”.

   * Klicka på den **Testanslutningen** knappen. Om Anslutningstestet lyckas klickar du på den **spara** längst upp. Om det misslyckas kontrollerar du att Workday-URL och autentiseringsuppgifter är giltiga i Workday.

### <a name="part-2-configure-attribute-mappings"></a>Del 2: Konfigurera attributmappningar 

I det här avsnittet konfigurerar du hur informationen flödar från Workday till Active Directory.

1. På fliken etablering under **mappningar**, klickar du på **synkronisera Azure AD-användare för Workday**.

2. I den **omfång för källobjekt** fältet, du kan också filtrera vilka uppsättningar med användare i Azure Active Directory bör ha sina e-postadresser skrivas tillbaka till Workday. Standardvärde är ”alla användare i Azure AD”. 

3. I den **attributmappningar** avsnittet, uppdatera matchande-ID för att ange attributet i Azure Active Directory där Workday arbetar-ID eller anställnings-ID lagras. En populär matchande metod är att synkronisera Workday arbetar-ID eller anställnings-ID till extensionAttribute1-15 i Azure AD och sedan använda det här attributet i Azure AD för att matcha användare tillbaka i Workday. 

4. Om du vill spara din mappningar, klickar du på **spara** överst i avsnittet attribut mappar.

### <a name="part-3-start-the-service"></a>Del 3: Starta tjänsten
När du delar 1 – 2 har slutförts, kan du starta etableringstjänsten.

1. I den **etablering** fliken genom att ange den **Etableringsstatus** till **på**.

2. Klicka på **Spara**.

3. Detta startar den första synkroniseringen, vilket kan ta flera timmar beroende på hur många användare finns i Workday variabeln.

4. Enskilda synkronisera händelser kan visas i den **granskningsloggar** fliken. **[Finns i guiden för etablering reporting för detaljerade anvisningar om hur du tolkar granskningsloggar](../manage-apps/check-status-user-account-provisioning.md)**

5. En klar skrivs en sammanfattning av granskningsrapporten den **etablering** fliken enligt nedan.

## <a name="customizing-the-list-of-workday-user-attributes"></a>Anpassning av listan över Workday-användarattribut
Workday tillhandahållning av appar för Active Directory och Azure AD som båda innehåller en standardlistan över Workday-användarattribut som du kan välja mellan. De här listorna är dock inte omfattande. Workday har stöd för flera hundra möjliga användarattribut som kan vara antingen standard eller unikt för din Workday-klient. 

Den Azure AD-etableringstjänsten stöder möjligheten att anpassa din lista eller Workday-attributet för att inkludera alla attribut som visas i den [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) driften av personal-API.

Om du vill göra detta måste du använda [Workday Studio](https://community.workday.com/studio-download) att extrahera XPath-uttryck som representerar de attribut som du vill använda och Lägg sedan till dem i din allokering konfiguration med hjälp av avancerade Attributredigeraren i Azure-portalen.

**Att hämta ett XPath-uttryck för ett Workday-användarattribut:**

1. Ladda ned och installera [Workday Studio](https://community.workday.com/studio-download). Du behöver en Workday community-konto för åtkomst till installationsprogrammet.

2. Hämta filen Workday Human_Resources Gränssnittsbeskrivningar från denna URL: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Starta Workday Studio.

4. I kommandofältet, Välj den **Workday > Test-webbtjänsten i Tester** alternativet.

5. Välj **externa**, och välj Human_Resources WSDL-filen som du hämtade i steg 2.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Ange den **plats** fältet `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, men ersätt ”IMPL-CC” med den faktiska instanstyp och ”KLIENTORGANISATION” med ditt verkliga klientnamn.

7. Ange **åtgärden** till **Get_Workers**

8.  Klicka på lilla **konfigurera** länkarna begäran/svar-fönster för att ställa in dina Workday-autentiseringsuppgifter. Kontrollera **autentisering**, och ange användarnamn och lösenord för system för Workday integrationskontot. Se till att formatera användarnamnet som name@tenant, och lämna den **WS-Security UsernameToken** alternativ som valts.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Välj **OK**.

10. Den **begära** fönstret klistra in XML-filen nedan och ange **Employee_ID** till anställnings-ID för en verklig användare i din Workday-klient. Välj en användare som har attributet fylls i som du vill extrahera.

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
            <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```
 
11. Klicka på den **skicka förfrågan** (grön pil) för att köra kommandot. Om autentiseringen lyckas kan svaret ska visas i den **svar** fönstret. Kontrollera svaret för att se till att den har data av användar-ID som du angav och inte ett fel.

12. Om detta lyckas, kopiera XML-filen från den **svar** fönstret och spara det som en XML-fil.

13. I kommando-fältet i Studio för Workday Välj **fil > Öppna fil...**  och öppna XML-filen som du sparade. Då öppnas det i Workday Studio XML-redigerare.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. I trädet filen navigerar **/ENV: kuvert > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker** att hitta dina användares data. 

15. Under **wd: Worker**, hitta det attribut som du vill lägga till och markera den.

16. Kopiera XPath-uttrycket för din valda attributet av den **dokumentsökväg** fält.

1. Ta bort den **/env:Envelope / env:Body / wd:Get_Workers_Response / wd:Response_Data /** prefixet från det kopierade uttrycket.

18. Om det sista objektet i det kopierade uttrycket är en nod (exempel ”: / wd: Birth_Date”), lägger till **/text()** i slutet av uttrycket. Detta är inte nödvändigt om det sista objektet är ett attribut (exempel ”:/@wd: typen”).

19. Resultatet bör vara något som liknar `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Det här är vad du kommer att kopiera till Azure-portalen.


**Lägga till din anpassade Workday-användarattribut i konfigurationen av etablering:**

1. Starta den [Azure-portalen](https://portal.azure.com), och gå till avsnittet etablering arbetsdagen etablering program, vilket beskrivs tidigare i den här självstudien.

2. Ange **Etableringsstatus** till **av**, och välj **spara**. På så sätt se till att ändringarna träder i kraft endast när du är klar.

3. Under **mappningar**väljer **synkronisera arbetare till den lokala** (eller **synkronisera arbetare till Azure AD**).

4. Bläddra längst ned på nästa skärm och välj **visa avancerade alternativ**.

5. Välj **redigera attributlista för Workday**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Rulla till slutet av attributlistan där indatafält finns.

7. För **namn**, ange ett namn för din attributet.

8. För **typ**, Välj typ som motsvarar din attributet på rätt sätt (**sträng** är de vanligaste).

9. För **API-uttryck**, ange XPath-uttryck som du kopierade från Workday Studio. Exempel: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Välj **Lägg till attribut**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Välj **spara** ovan, och sedan **Ja** till dialogrutan. Stäng attributmappning skärmen om det fortfarande är öppen.

12. Tillbaka på huvudsidan **etablering** fliken **synkronisera arbetare till den lokala** (eller **synkronisera arbetare till Azure AD**) igen.

13. Välj **Lägg till ny mappning**.

14. Din nya attributet bör nu visas i den **källattribut** lista.

15. Lägg till en mappning för din nya attribut som du vill.

16. När du är klar, Kom ihåg att ange **Etableringsstatus** tillbaka till **på** och spara.

## <a name="known-issues"></a>Kända problem

* Skriva data till thumbnailPhoto-attributet för användare i den lokala Active Directory stöds inte för närvarande.

* ”Workday till Azure AD” anslutningen stöds inte för närvarande på Azure AD-klienter där AAD Connect är aktiverat.  

## <a name="managing-personal-data"></a>Hantera personliga data

Workday etablera lösningen för Active Directory kräver en synkroniseringsagent installeras på en domänansluten server och den här agenten skapar loggar i Windows-händelseloggen som kan innehålla personligt identifierbar information.

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggarna och få rapporter om etablering aktivitet](../manage-apps/check-status-user-account-provisioning.md)
* [Lär dig hur du konfigurerar enkel inloggning mellan Workday och Azure Active Directory](workday-tutorial.md)
* [Lär dig hur du integrerar andra SaaS-program med Azure Active Directory](tutorial-list.md)

