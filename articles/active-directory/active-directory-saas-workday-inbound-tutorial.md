---
title: "Självstudier: Konfigurera Workday för automatisk användaretablering med Azure Active Directory | Microsoft Docs"
description: "Lär dig hur du använder Workday som datakälla identitet för Active Directory och Azure Active Directory."
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/26/2018
ms.author: asmalser
ms.openlocfilehash: ed35a703774fdb2f2896414b6022b6f13fb7a307
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Självstudier: Konfigurera Workday för automatisk användaretablering

Syftet med den här kursen är att visa de steg som du behöver utföra för att importera personer från Workday till Azure Active Directory- och Active Directory-med valfritt tillbakaskrivning av vissa attribut till Workday. 



## <a name="overview"></a>Översikt

Den [Azure Active Directory-användare som etableras](active-directory-saas-app-provisioning.md) kan integreras med den [Workday personal API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) för att kunna etablera användarkonton. Azure AD använder den här anslutningen för att aktivera etablering arbetsflöden följande användare:

* **Användare i Active Directory-etablering** -Synkronisera markerade uppsättningar med användare från Workday till en eller flera Active Directory-skogar. 

* **Endast molnbaserad användare till Azure Active Directory-etablering** -Hybrid-användare som finns i både Active Directory och Azure Active Directory kan etableras till senare med [AAD Connect](connect/active-directory-aadconnect.md). Användare som är molnbaserad kan dock etableras direkt från Workday till Azure Active Directory med Azure AD-tjänsten för användaretablering.

* **Tillbakaskrivning av e-post-adresser till Workday** -Azure AD etableras kan skriva vald användarattribut för Azure AD tillbaka till Workday, till exempel e-postadress.

### <a name="scenarios-covered"></a>Scenarier som tas upp

Workday användaren etablering arbetsflöden stöds av Azure AD-användaren etablering tjänsten aktivera automatisering av följande personal och identitet livscykel scenarier:

* **Uthyrning nyanställda** – när en ny medarbetare har lagts till i Workday, ett konto skapas automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](active-directory-saas-app-provisioning.md), med återskrivning av e-postadressen till Workday.

* **Medarbetare-attribut och profilen uppdateringar** – när en anställd uppdateras i Workday (till exempel deras namn, avdelning eller manager), sitt användarkonto uppdateras automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](active-directory-saas-app-provisioning.md).

* **Medarbetare uppsägningar** – när en anställd avslutas i arbetsdagen, sitt användarkonto inaktiveras automatiskt i Active Directory, Azure Active Directory och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](active-directory-saas-app-provisioning.md).

* **Medarbetare nytt anlitar** – när en anställd rehired i Workday, sina gamla kontot kan automatiskt återaktivera eller etablerats igen (beroende på dina inställningar) till Active Directory, Azure Active Directory, och eventuellt Office 365 och [andra SaaS-program som stöds av Azure AD](active-directory-saas-app-provisioning.md).


## <a name="planning-your-solution"></a>Planerar din lösning

Kontrollera krav nedan och Läs följande riktlinjer om hur du matchar din aktuella Active Directory-arkitektur och krav för användaretablering med solution(s) som tillhandahålls av Azure Active Directory innan du börjar din Workday-integrering.

### <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

* En giltig Azure AD Premium P1-prenumeration med global administratörsbehörighet
* En klient för implementering av Workday för testning och integrering
* Administratörsbehörighet i Workday för att skapa en användare för integration av system och göra ändringar på testdata medarbetare för testning
* För användaretablering till Active Directory, en domänansluten server som kör tjänsten Windows 2012 eller senare krävs för att värden i [lokalt lösenordssynkroniseringsagenten](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](connect/active-directory-aadconnect.md) för synkronisering mellan Active Directory och Azure AD


### <a name="solution-architecture"></a>Lösningsarkitektur

Azure AD innehåller en omfattande uppsättning etablering kopplingar för att lösa etablering och livscykel Identitetshantering från Workday till Active Directory, Azure AD, SaaS-appar och framåt. Vilka funktioner du använder och hur du ställer in lösningen varierar beroende på din organisations miljö och behov. Går igenom hur många av följande är närvarande och distribuerade i din organisation som ett första steg:

* Hur många Active Directory-skogar finns i använda?
* Hur många Active Directory-domäner finns i använda?
* Hur många Active Directory organisationsenheter (OU) används?
* Hur många Azure Active Directory-klienter finns i använda?
* Finns det användare som behöver etableras både Active Directory och Azure Active Directory (t.ex. ”hybrid” användare)?
* Finns det användare som ska etableras på Azure Active Directory, men inte Active Directory (t.ex. ”endast molnbaserad” användare)?
* Behöver användare e-postadresser som ska skrivas tillbaka till Workday?

När du har svaren på dessa frågor kan planera du din arbetsdag etablering distributionen genom att följa anvisningarna nedan.

#### <a name="using-provisioning-connector-apps"></a>Använda etablering connector appar

Azure Active Directory stöder förintegrerade etablering kopplingar för Workday och många andra SaaS-program. 

En allokering kontakt med en enda källsystemet API-gränssnitt och hjälper etableringsdata till ett enda mål-system. De flesta etablering kopplingar som har stöd för Azure AD är för ett enda käll- och system (t.ex. Azure AD att ServiceNow) och kan konfigureras genom att lägga till appen i fråga från appgalleriet för Azure AD (t.ex. ServiceNow). 

Det finns en 1: 1-relation mellan etablering koppling instanser och app-instanser i Azure AD:

| Källsystemet | Målsystem |
| ---------- | ---------- | 
| Azure AD-klient | SaaS-program |


När du arbetar med Workday och Active Directory, finns det dock flera käll- och system för att ses som:

| Källsystemet | Målsystem | Anteckningar |
| ---------- | ---------- | ---------- |
| Arbetsdagar | Active Directory-skog | Varje skog behandlas som ett distinkta målsystem |
| Arbetsdagar | Azure AD-klient | Som krävs för endast molnbaserad användare |
| Active Directory-skog | Azure AD-klient | Detta flöde hanteras av AAD Connect idag |
| Azure AD-klient | Arbetsdagar | För tillbakaskrivning av e-postadresser |

För att underlätta dessa flera olika arbetsflöden på flera datorer för käll- och innehåller Azure AD flera etablering connector-appar som du kan lägga till från appgalleriet för Azure AD:

![AAD App-galleriet](./media/active-directory-saas-workday-inbound-tutorial/WD_Gallery.PNG)

* **Arbetsdagar Active Directory-etablering** -den här appen förenklar konto användarförsörjning från Workday till en Active Directory-skog. Om du har flera skogar, kan du lägga till en instans av den här appen från Azure AD app-galleriet för varje Active Directory-skog måste du etablera till.

* **Arbetsdagar till Azure AD-etablering** - medan AAD Connect är ett verktyg som ska användas för att synkronisera Active Directory användare till Azure Active Directory, den här appen kan användas för att underlätta etablering av endast molnbaserad användare från Workday till en enda Azure Active Directory-klient.

* **Arbetsdagar tillbakaskrivning** -den här appen förenklar tillbakaskrivning av användarens e-postadresser från Azure Active Directory till Workday.

> [!TIP]
> Vanliga ”Workday” appen används för att konfigurera enkel inloggning mellan Workday och Azure Active Directory. 

Hur du skapar och konfigurerar apparna särskilda etablering connector är föremål för de återstående avsnitten i den här kursen. Vilka appar som du vill konfigurera beror på vilka system måste du etablera till, och hur många Active Directory-skogar och Azure AD klienter finns i din miljö.

![Översikt](./media/active-directory-saas-workday-inbound-tutorial/WD_Overview.PNG)

## <a name="configure-a-system-integration-user-in-workday"></a>Konfigurera en systemanvändare integrering i Workday
Ett vanligt krav på alla Workday etablering kopplingar är de kräver autentiseringsuppgifter för ett Workday-integrering systemkonto att ansluta till Workday personal API. Det här avsnittet beskrivs hur du skapar ett konto för system integrator i Workday.

> [!NOTE]
> Det är möjligt att kringgå den här proceduren och i stället använda ett globalt administratörskonto för Workday som systemkonto för integrering. Detta fungerar bra för demonstrationer, men det rekommenderas inte för Produktionsdistribution.

### <a name="create-an-integration-system-user"></a>Skapa en integration system-användare

**Skapa en integration systemanvändare:**

1. Logga in på din Workday-klient med ett administratörskonto. I den **Workday arbetsstationen**, ange skapa användare i sökrutan och klicka sedan på **skapa Integration systemanvändare**. 
   
    ![Skapa användare](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "skapa användare")
2. Slutför den **skapa Integration systemanvändare** aktiviteten genom att ange ett användarnamn och lösenord för en ny Integration systemanvändare.  
 * Lämna den **kräver nya lösenord vid nästa inloggning** alternativet är avmarkerat, eftersom den här användaren loggar in via programmering. 
 * Lämna den **antal minuter för sessionens Timeout** med standardvärdet 0, vilket förhindrar att användarens sessioner timeout för tidigt. 
   
    ![Skapa Integration systemanvändare](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "skapa Integration systemanvändare")

### <a name="create-a-security-group"></a>Skapa en säkerhetsgrupp
Du måste skapa en säkerhetsgrupp för obegränsad integration system och tilldela användaren till den.

**Skapa en säkerhetsgrupp:**

1. Ange skapa säkerhetsgrupp i sökrutan och klicka sedan på **skapa säkerhetsgruppen**. 
   
    ![CreateSecurity grupp](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "CreateSecurity grupp")
2. Slutför den **skapa säkerhetsgrupp** aktivitet.  
3. Välj Integration Systemsäkerhetsgrupp – obegränsad från den **typ av innehavare säkerhetsgrupp** listrutan.
4. Skapa en säkerhetsgrupp som medlemmar uttryckligen läggs. 
   
    ![CreateSecurity grupp](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "CreateSecurity grupp")

### <a name="assign-the-integration-system-user-to-the-security-group"></a>Tilldela säkerhetsgruppen systemanvändaren integrering

**Så här tilldelar systemanvändaren integrering:**

1. Ange redigera säkerhetsgrupp i sökrutan och klicka sedan på **redigera säkerhetsgrupp**. 
   
    ![Redigera säkerhetsgrupp](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "redigera säkerhetsgrupp")
2. Söka efter och välj den nya säkerhetsgruppen för integrering av namn. 
   
    ![Redigera säkerhetsgrupp](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "redigera säkerhetsgrupp")
3. Lägg till den nya integration systemanvändaren till den nya säkerhetsgruppen. 
   
    ![Systemsäkerhetsgrupp](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "Systemsäkerhetsgrupp")  

### <a name="configure-security-group-options"></a>Konfigurera alternativ för säkerhet
I det här steget ska du ge domänens säkerhet princip behörigheter för worker-data som skyddas av säkerhetsprinciper för följande domän:


| Åtgärd | Domain Security Policy |
| ---------- | ---------- | 
| Hämta och skicka |  Externa konto-etablering |
| Hämta och skicka | Worker Data: Public Worker rapporter |
| Hämta och skicka | Worker Data: Alla positioner |
| Hämta och skicka | Worker Data: Current bemanning Information |
| Hämta och skicka | Worker Data: Business titel på Worker profil |
| Visa och ändra | Worker Data: E-post |

**Konfigurera alternativ för säkerhet:**

1. Ange säkerhetsprinciper för domänen i sökrutan och klicka på länken **domän säkerhetsprinciper för funktionsområde**.  
   
    ![Domän säkerhetsprinciper](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "säkerhetsprinciper för domänen")  
2. Sök efter system och välj den **System** funktionsområde.  Klicka på **OK**.  
   
    ![Domän säkerhetsprinciper](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "säkerhetsprinciper för domänen")  
3. I listan över säkerhetsprinciper för de funktionella området System, expanderar **säkerhetsadministration** och välj säkerhetsprincip för domän **externa Kontoetablering**.  
   
    ![Domän säkerhetsprinciper](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "säkerhetsprinciper för domänen")  
4. Klicka på **Redigera behörigheter**, och klicka sedan på den **Redigera behörigheter** dialogrutan sidan lägger till den nya säkerhetsgruppen i listan säkerhetsgrupper med **hämta** och **placera**  integrering behörigheter. 
   
    ![Redigera behörighet](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "redigera behörighet")  
5. Upprepa steg 1 ovan för att återgå till skärmen för att välja huvudområden och nu, Sök efter personal, Välj den **bemanning funktionsområde** och på **OK**.
   
    ![Domän säkerhetsprinciper](./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "säkerhetsprinciper för domänen")  
6. I listan över säkerhetsprinciper för funktionsområdet Staffing, expanderar **Worker Data: Staffing** och upprepa ovanstående steg 4 för varje återstående säkerhetsprinciper:

   * Worker Data: Public Worker rapporter
   * Worker Data: Alla positioner
   * Worker Data: Current bemanning Information
   * Worker Data: Business titel på Worker profil
   
7. Upprepa steg 1, ovan, för att återgå till skärmen för att välja funktionella områden och den här tiden kan söka efter **kontaktinformation**, Välj funktionsområdet Staffing och på **OK**.

8.  I listan över säkerhetsprinciper för funktionsområdet Staffing, expanderar **Worker Data: Kontakta arbetsinformation**, och upprepa steg 4 ovan för säkerhetsprinciper nedan:

    * Worker Data: E-post

    ![Domän säkerhetsprinciper](./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "säkerhetsprinciper för domänen")  
    
### <a name="activate-security-policy-changes"></a>Aktivera ändringar i säkerhet

**Att aktivera ändringar i säkerhet:**

1. Ange aktivera i sökrutan och klicka på länken **aktivera väntande ändringar i säkerhet**. 
   
    ![Aktivera](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "aktivera") 
2. Börja aktiviteten aktivera väntande ändringar i säkerhet genom att ange en kommentar för granskningsändamål och klicka sedan på **OK**. 
   
    ![Aktivera väntande säkerhet](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "aktivera väntande säkerhet")   
3. Slutföra uppgiften på nästa skärm genom att markera kryssrutan **Bekräfta**, och klicka sedan på **OK**. 
   
    ![Aktivera väntande säkerhet](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "aktivera väntande säkerhet")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfigurering av användarförsörjning från Workday till Active Directory
Följ instruktionerna för att konfigurera användarkonto etablering från Workday till varje Active Directory-skog som du behöver etablering till.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Del 1: Lägga till appen etablering koppling och skapa anslutningen till Workday

**Konfigurera Workday för Active Directory-etablering:**

1.  Gå till <https://portal.azure.com>

2.  I det vänstra navigeringsfältet väljer **Azure Active Directory**

3.  Välj **företagsprogram**, sedan **alla program**.

4.  Välj **lägga till ett program**, och välj den **alla** kategori.

5.  Sök efter **Workday etablering till Active Directory**, och Lägg till appen från galleriet.

6.  När appen har lagts till och skärmen app information är visas, väljer **etablering**

7.  Ändra den **etablering** **läge** till **automatisk**

8.  Slutför den **administratörsautentiseringsuppgifter** avsnittet på följande sätt:

   * **Admin Username** – ange användarnamnet för system-kontot Workday-integrering med klienten domännamnet tillagt. **Ska se ut ungefär:username@contoso4**

   * **Administratörslösenordet –** ange lösenordet för kontot Workday-integrering system

   * **Klient URL –** anger du Webbadressen till Workday web services-slutpunkten för din klient. Detta ska se ut: https://wd3-impl-services1.workday.com/ccx/service/contoso4 där contoso4 ersätts med rätt-klientnamn och wd3 impl ersätts med rätt miljö-sträng.

   * **Active Directory-skogar -** ”Name” för din Active Directory-skog, som returneras av powershell-kommandot Get-ADForest. Detta är vanligtvis en sträng som: *contoso.com*

   * **Active Directory-behållare -** teckensträngen behållare, som innehåller alla användare i din AD-skog. Exempel: *OU = standardanvändare, OU = Users, DC = contoso, DC = test*

   * **E-postmeddelande –** ange din e-postadress och markera kryssrutan ”Skicka e-post om fel inträffar”.

   * Klicka på den **Testanslutningen** knappen. Om Anslutningstestet lyckas klickar du på den **spara** längst upp. Om det misslyckas, kan du kontrollera att Workday-autentiseringsuppgifter är giltiga i Workday. 

![Azure Portal](./media/active-directory-saas-workday-inbound-tutorial/WD_1.PNG)

### <a name="part-2-configure-attribute-mappings"></a>Del 2: Konfigurera attributmappning 

I det här avsnittet ska du konfigurera hur informationen flödar från Workday till Active Directory.

1.  På fliken etablering under **mappningar**, klickar du på **synkronisera Workday anställda för OnPremises**.

2.  I den **källa Objektområde** fält, kan du välja vilka uppsättningar med användare i Workday ska vara i omfånget för etablering i AD, genom att definiera en uppsättning attributbaserad filter. Standardvärde är ”alla användare i Workday”. Exempel filter:

   * Exempel: Omfång för användare med Worker-ID: N mellan 1000000 och 2000000

      * Attributet: WorkerID

      * Operatorn: REGEX-matchning

      * Value: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exempel: Endast anställda och inte contingent personer 

      * Attributet: EmployeeID

      * Operatorn: Inte är NULL

3.  I den **mål objektåtgärder** fält du kan globalt filtrera vilka åtgärder ska kunna utföras på Active Directory. **Skapa** och **uppdatering** är de vanligaste.

4.  I den **attributet mappningar** avsnitt, kan du definiera hur enskilda Workday attribut mappar till Active Directory-attribut.

5. Klicka på en befintlig attributmappning att uppdatera det, eller klicka på **Lägg till ny mappning** längst ned på skärmen för att lägga till nya mappningar. En enskild attributmappning stöder dessa egenskaper:

      * **Avbildningstyp**

         * **Direkt** – skriver värdet för Workday-attribut till attributet AD utan ändringar

         * **Konstant** -skriva ett statiska, konstant strängvärde till AD-attribut

         * **Uttrycket** – du kan skriva ett anpassat värde till AD-attributet baserat på en eller flera Workday-attribut. [Mer information finns i den här artikeln på uttryck](active-directory-saas-writing-expressions-for-attribute-mappings.md).

      * **Källattributet** -användarattribut från Workday.

      * **Standardvärde** – det är valfritt. Om källattributet har ett tomt värde, skrivs mappningen det här värdet i stället.
            De vanligaste konfigurationen är att du lämnar fältet tomt.

      * **Målattribut** – användarattribut i Active Directory.

      * **Matcha objekt med hjälp av det här attributet** – oavsett om den här mappningen som ska användas för att unikt identifiera användarna mellan Workday och Active Directory. Detta anges normalt Worker ID-fältet för Workday som vanligtvis är mappad till en medarbetare ID-attribut i Active Directory.

      * **Matchar prioritet** – flera matchande attribut kan anges. När det finns flera, utvärderas de i den ordning som anges av det här fältet. När en matchning hittas matchar ingen ytterligare attribut utvärderas.

      * **Använd den här mappningen**
       
         * **Alltid** – tillämpa den här mappningen för både skapa användare och uppdatera åtgärder

         * **Endast under skapa** -mappningen tillämpas endast på användare creation-åtgärder

6. Om du vill spara dina mappningar klickar du på **spara** överst i avsnittet attributmappning.

![Azure Portal](./media/active-directory-saas-workday-inbound-tutorial/WD_2.PNG)

**Här följer några exempel attributmappning mellan Workday och Active Directory med vissa vanliga uttryck**

-   Det uttryck som mappar till parentDistinguishedName AD-attributet kan användas för att etablera en användare till en viss Organisationsenhet baserat på en eller flera Workday källa attribut. Det här exemplet placerar användare i olika organisationsenheter beroende på deras stad data i Workday.

-   Det uttryck som mappar till attributet userPrincipalName AD skapa ett UPN för firstName.LastName@contoso.com. Den ersätter också ogiltiga specialtecken.

-   [Det finns dokumentationen om hur du skriver här uttryck](active-directory-saas-writing-expressions-for-attribute-mappings.md)

  
| ARBETSDAGAR ATTRIBUT | ACTIVE DIRECTORY-ATTRIBUT |  MATCHANDE ID? | SKAPA / UPPDATERA |
| ---------- | ---------- | ---------- | ---------- |
|  **WorkerID**  |  EmployeeID | **Ja** | Skrivas i Skapa endast | 
|  **Namnet**   |   l   |     | Skapa och uppdatera |
|  **Företag**         | Företag   |     |  Skapa och uppdatera |
|  **CountryReferenceTwoLetter**      |   CO |     |   Skapa och uppdatera |
| **CountryReferenceTwoLetter**    |  c  |     |         Skapa och uppdatera |
| **SupervisoryOrganization**  | Avdelning  |     |  Skapa och uppdatera |
|  **PreferredNameData**  |  displayName |     |   Skapa och uppdatera |
| **EmployeeID**    |  cn    |   |   Skrivas i Skapa endast |
| **Fax**      | facsimileTelephoneNumber     |     |    Skapa och uppdatera |
| **Förnamn**   | givenName       |     |    Skapa och uppdatera |
| **Växel (\[Active\],, ”0”, ”True”, ”1”)** |  accountDisabled      |     | Skapa och uppdatera |
| **Mobile**  |    mobila       |     |       Skapa och uppdatera |
| **E-postadress**    | E-post    |     |     Skapa och uppdatera |
| **ManagerReference**   | manager  |     |  Skapa och uppdatera |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Skapa och uppdatera |
| **PostalCode**  |   Postnummer  |     | Skapa och uppdatera |
| **LocalReference** |  preferredLanguage  |     |  Skapa och uppdatera |
| **Ersätt (Mid (Ersätt (\[EmployeeID\]”, (\[ \\ \\ / \\ \\ \\ \\ \\ \\\[\\\\\]\\\\:\\\\;\\ \\|\\\\=\\\\,\\\\+\\\\\*\\ \\? \\ \\ &lt; \\ \\ &gt; \]) ””, ”,), 1, 20)”, ([\\\\.) \* \$] (file:///\\.) *$)", , "", , )**      |    sAMAccountName            |     |         Skrivas i Skapa endast |
| **Efternamn**   |   SN   |     |  Skapa och uppdatera |
| **CountryRegionReference** |  St     |     | Skapa och uppdatera |
| **AddressLineData**    |  streetAddress  |     |   Skapa och uppdatera |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Skapa och uppdatera |
| **BusinessTitle**   |  rubrik     |     |  Skapa och uppdatera |
| **Ansluta (”@”, Ersätt (Ersätt (ersätta (Ersätt (ersätta (Ersätt (ersätta (ersätta (Ersätt ((Ersätt (Ersätt (Ersätt (Ersätt (Ersätt (Ersätt (Ersätt (Ersätt (Ersätt (Ersätt (Ersätt (Ersätt (Ersätt (Ersätt (Ersätt (Ersätt (Ersätt ( Ersätt (ansluta till (””., [Förnamn], [Efternamn]), ”([Øø])”, ”Outlook Express”,), ”[Ææ]”, ”ae”,), ”([äãàâãåáąÄÃÀÂÃÅÁĄA])”, ”a”,), ”[B]”, ”b”,), ”([CçčćÇČĆ])”, ”c”,), ”([ďĎD])”, ”d”,), ”([ëèéêęěËÈÉÊĘĚE])”, ”e”,), ”[F]”, ”f”,), ”([G])” ”g”,), ”[H]”, ”h”,), ”([ïîìíÏÎÌÍI])”, ”i”,), ”[J]”, ”j”,), ”([K])”, ”k”,), ”([ľłŁĽL])”, ”l”,), ”([M])”, ”m”,), ”([ñńňÑŃŇN])”, ”n”,), ”([öòőõôóÖÒŐÕÔÓO])”, ”o”,), ”([P])”, ”p”,), ”([Q])”, ”q”,),  ”([ŘŘR])”, ”r”,), ”([ßšśŠŚS])”, ”s”,), ”([TŤť])”, ”t”,), ”([üùûúůűÜÙÛÚŮŰU])”, ”u”,), ”([V])”, ”v”,), ”([B])”, ”b”,), ”([ýÿýŸÝY])”, ”y”,), ”([źžżŹŽŻZ])”, ”z”,) ”,”, ””,), ”contoso.com”)**   | userPrincipalName     |     | Skrivas i Skapa endast                                                   
| **Växel (\[namnet\]”, OU standardanvändare, OU = = användare, OU = standard, OU = platser, DC = contoso, DC = com”, ”Dallas” ”, OU standardanvändare, OU = = användare, OU Dallas, OU = platser, DC = = contoso, DC = com”, ”Austin” ”OU standardanvändare, OU = = Användare, OU Austin, OU = platser, DC = = contoso, DC = com ”,” Seattle ””, OU standardanvändare, OU = = användare, OU Seattle, OU = = platser, DC = contoso, DC = com ”,” London ””, OU standardanvändare, OU = = användare, OU London, OU = platser, DC = = contoso, DC = com ”)**  | parentDistinguishedName     |     |  Skapa och uppdatera |
  
### <a name="part-3-configure-the-on-premises-synchronization-agent"></a>Del 3: Konfigurera lokal synkronisering agent

För att kunna etablera till Active Directory lokalt, måste en agent installeras på en domänansluten server i önskan Active Directory-skog. Domänadministratörer (eller företagsadministratör) autentiseringsuppgifter krävs för att slutföra åtgärden.

**[Du kan hämta den lokala lösenordssynkroniseringsagenten här](https://go.microsoft.com/fwlink/?linkid=847801)**

När du har installerat agenten, kör Powershell-kommandona nedan att konfigurera agenten för din miljö.

**Kommandot #1**

> CD C:\\programfiler\\Microsoft Azure Active Directory-synkronisering Agent\\moduler\\AADSyncAgent

> import-module AADSyncAgent.psd1

**Kommandot #2**

> Add-ADSyncAgentActiveDirectoryConfiguration

* Indata: För ”katalognamn”, ange namnet på AD-skog, som har angetts i del \#2
* Indata: Admin användarnamn och lösenord för Active Directory-skog

**Kommandot #3**

> Add-ADSyncAgentAzureActiveDirectoryConfiguration

* Indata: Global administratörsanvändarnamnet och lösenordet för din Azure AD-klient

>[!IMPORTANT]
>Det finns för närvarande ett känt problem med global administratörsbehörighet fungerar inte om de använder en anpassad domän (exempel: admin@contoso.com). Som en tillfällig lösning kan du skapa och använda ett globalt administratörskonto med en onmicrosoft.com-domän (exempel: admin@contoso.onmicrosoft.com)


**Kommandot #4**

> Get-AdSyncAgentProvisioningTasks

* Åtgärd: Kontrollera data returneras. Det här kommandot upptäcker automatiskt Workday etablering appar i Azure AD-klienten. Exempel på utdata:

> Namn: Min AD-skog
>
> Aktiverad: True
>
> DirectoryName: mydomain.contoso.com
>
> Trovärdig: False
>
> ID: WDAYdnAppDelta.c2ef8d247a61499ba8af0a29208fb853.4725aa7b-1103-41e6-8929-75a5471a5203

**Kommandot #5**

> Start-AdSyncAgentSynchronization-automatisk

**Kommandot #6**

> net stop aadsyncagent

**Kommandot #7**

> net start aadsyncagent

>[!TIP]
>Förutom ”net” kommandon i Powershell synkronisering agent-tjänsten kan också startas och stoppas med **Services.msc**. Om det uppstår fel när du kör Powershell-kommandon, se till att den **Microsoft Azure AD Connect etablering Agent** körs i **Services.msc**.

![Tjänster](./media/active-directory-saas-workday-inbound-tutorial/Services.png)  

**Ytterligare konfiguration för kunder i Europeiska unionen**

Om din Azure Active Directory-klient finns i något av de EU-datacenter, följer du stegen nedan.

1. Öppna **Services.msc** , och stoppa den **Microsoft Azure AD Connect etablering Agent** service.
2. Gå till installationsmappen för agent (exempel: C:\Program Files\Microsoft Azure AD Connect etablering Agent).
3. Öppna **SyncAgnt.exe.config** i en textredigerare.
4. Ersätt https://manage.hub.syncfabric.windowsazure.com/Management med **https://eu.manage.hub.syncfabric.windowsazure.com/Management**
5. Ersätt https://provision.hub.syncfabric.windowsazure.com/Provisioning med **https://eu.provision.hub.syncfabric.windowsazure.com/Provisioning**
6. Spara den **SyncAgnt.exe.config** fil.
7. Öppna **Services.msc**, och starta den **Microsoft Azure AD Connect etablering Agent** service.

**Agenten felsökning**

Den [Windows-händelseloggen](https://technet.microsoft.com/en-us/library/cc722404(v=ws.11).aspx) på Windows Server-dator som värd för agenten innehåller händelser för alla åtgärder som utförs av agenten. Visa dessa händelser:
    
1. Öppna **Eventvwr.msc**.
2. Välj **Windows-loggar > programmet**.
3. Visa alla händelser som loggats under källan **AADSyncAgent**. 
4. Sök efter fel och varningar.

Om det finns ett problem med behörigheter med Active Directory eller Azure Active Directory-autentiseringsuppgifter som anges i Powershell-kommandon, visas ett fel som den här: 
    
![Händelseloggar](./media/active-directory-saas-workday-inbound-tutorial/Windows_Event_Logs.png) 


### <a name="part-4-start-the-service"></a>Del 4: Starta tjänsten
När delarna 1-3 har slutförts, kan du starta tjänsten etablering tillbaka i Azure-portalen.

1.  I den **etablering** ställer du in den **Status för etablering** till **på**.

2. Klicka på **Spara**.

3. Detta startar den första synkroniseringen, vilket kan ta flera timmar beroende på hur många användare finns i Workday variabeln.

4. När som helst kontrollera den **granskningsloggar** fliken i Azure portal och se vilka åtgärder som tjänsten etablering har utförts. Granskningsloggarna innehåller alla enskilda sync händelser utförs av tjänsten etablering, t.ex vilka användare som ska läsas från Workday och sedan senare läggs till eller uppdateras i Active Directory. **[Finns i guiden för etablering reporting detaljerade anvisningar om hur du tolkar granskningsloggarna](active-directory-saas-provisioning-reporting.md)**

5.  Kontrollera den [Windows-händelseloggen](https://technet.microsoft.com/en-us/library/cc722404(v=ws.11).aspx) på Windows Server-datorn som är värd för agenten om det finns nya fel eller varningar. Dessa händelser kan visas genom att starta **Eventvwr.msc** på servern och välja **Windows-loggar > programmet**. Alla etablering meddelanden loggas under källan **AADSyncAgent**. 
    

6. En klar skrivs en översikt över kontrollrapport den **etablering** fliken enligt nedan.

![Azure Portal](./media/active-directory-saas-workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Konfigurering av användarförsörjning till Azure Active Directory
Hur du konfigurerar etablering till Azure Active Directory beror på dina etablering krav som anges i tabellen nedan.

| Scenario | Lösning |
| -------- | -------- |
| **Användarna behöver för att etableras till Active Directory och Azure AD** | Använd  **[AAD Connect](connect/active-directory-aadconnect.md)** |
| **Användarna behöver för att etableras endast till Active Directory** | Använd  **[AAD Connect](connect/active-directory-aadconnect.md)** |
| **Användarna behöver för att etableras till Azure AD endast (moln)** | Använd den **arbetsdagar Azure Active Directory-etablering** app i appgalleriet |

Anvisningar om hur du konfigurerar Azure AD Connect finns i [dokumentation för Azure AD Connect](connect/active-directory-aadconnect.md).

I följande avsnitt beskrivs hur du konfigurerar en anslutning mellan Workday och Azure AD för att etablera endast molnbaserad användare.

> [!IMPORTANT]
> Endast följer du anvisningarna nedan om du har endast molnbaserad användare som behöver etableras till Azure AD och inte lokala Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Del 1: Lägga till Azure AD-appar för etablering koppling och skapa anslutningen till Workday

**Konfigurera Workday till Azure Active Directory-etablering för endast molnbaserad användare:**

1.  Gå till <https://portal.azure.com>.

2.  I det vänstra navigeringsfältet väljer **Azure Active Directory**

3.  Välj **företagsprogram**, sedan **alla program**.

4.  Välj **lägga till ett program**, och välj sedan den **alla** kategori.

5.  Sök efter **Workday till Azure AD-etablering**, och Lägg till appen från galleriet.

6.  När appen har lagts till och skärmen app information är visas, väljer **etablering**

7.  Ändra den **etablering** **läge** till **automatisk**

8.  Slutför den **administratörsautentiseringsuppgifter** avsnittet på följande sätt:

   * **Admin Username** – ange användarnamnet för system-kontot Workday-integrering med klienten domännamnet tillagt. Ska se ut ungefär:username@contoso4

   * **Administratörslösenordet –** ange lösenordet för kontot Workday-integrering system

   * **Klient URL –** anger du Webbadressen till Workday web services-slutpunkten för din klient. Detta ska se ut: https://wd3-impl-services1.workday.com/ccx/service/contoso4 där contoso4 ersätts med rätt-klientnamn och wd3 impl ersätts med rätt miljö-sträng. Om denna URL inte är känt kan arbeta med dina Workday-integrering partner eller support representant om du vill se Webbadressen till rätt att använda.

   * **E-postmeddelande –** ange din e-postadress och markera kryssrutan ”Skicka e-post om fel inträffar”.

   * Klicka på den **Testanslutningen** knappen.

   * Om Anslutningstestet lyckas klickar du på den **spara** längst upp. Om det misslyckas, kan du kontrollera att Workday-URL och autentiseringsuppgifter är giltiga i Workday.


### <a name="part-2-configure-attribute-mappings"></a>Del 2: Konfigurera attributmappning 

I det här avsnittet ska du konfigurera hur informationen flödar från Workday till Azure Active Directory för endast molnbaserad användare.

1.  På fliken etablering under **mappningar**, klickar du på **synkronisera arbetare till Azure AD**.

2.   I den **källa Objektområde** fält, kan du välja vilka uppsättningar med användare i Workday ska vara i omfånget för etablering i Azure AD genom att definiera en uppsättning attributbaserad filter. Standardvärde är ”alla användare i Workday”. Exempel filter:

   * Exempel: Omfång för användare med Worker-ID: N mellan 1000000 och 2000000

      * Attributet: WorkerID

      * Operatorn: REGEX-matchning

      * Value: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Exempel: Endast contingent arbetare och inte fast anställda

      * Attributet: ContingentID

      * Operatorn: Inte är NULL

3.  I den **mål objektåtgärder** fält du kan globalt filtrera vilka åtgärder ska kunna utföras på Azure AD. **Skapa** och **uppdatering** är de vanligaste.

4.  I den **attributet mappningar** avsnitt, kan du definiera hur enskilda Workday attribut mappar till Active Directory-attribut.

5. Klicka på en befintlig attributmappning att uppdatera det, eller klicka på **Lägg till ny mappning** längst ned på skärmen för att lägga till nya mappningar. En enskild attributmappning stöder dessa egenskaper:

   * **Avbildningstyp**

      * **Direkt** – skriver värdet för Workday-attribut till attributet AD utan ändringar

      * **Konstant** -skriva ett statiska, konstant strängvärde till AD-attribut

      * **Uttrycket** – du kan skriva ett anpassat värde till AD-attributet baserat på en eller flera Workday-attribut. [Mer information finns i den här artikeln på uttryck](active-directory-saas-writing-expressions-for-attribute-mappings.md).

   * **Källattributet** -användarattribut från Workday.

   * **Standardvärde** – det är valfritt. Om källattributet har ett tomt värde, skrivs mappningen det här värdet i stället.
            De vanligaste konfigurationen är att du lämnar fältet tomt.

   * **Målattribut** – användarattribut i Azure AD.

   * **Matcha objekt med hjälp av det här attributet** – oavsett om den här mappningen som ska användas för att unikt identifiera användarna mellan Workday och Azure AD. Detta anges normalt Worker ID-fältet för Workday som vanligtvis är mappad till attributet anställnings-ID (nya) eller ett attribut för tillägget i Azure AD.

   * **Matchar prioritet** – flera matchande attribut kan anges. När det finns flera, utvärderas de i den ordning som anges av det här fältet. När en matchning hittas matchar ingen ytterligare attribut utvärderas.

   * **Använd den här mappningen**

     * **Alltid** – tillämpa den här mappningen för både skapa användare och uppdatera åtgärder

     * **Endast under skapa** -mappningen tillämpas endast på användare creation-åtgärder

6. Om du vill spara dina mappningar klickar du på **spara** överst i avsnittet attributmappning.

### <a name="part-3-start-the-service"></a>Del 3: Starta tjänsten
När delar 1 – 2 har slutförts, kan du starta tjänsten etablering.

1.  I den **etablering** ställer du in den **Status för etablering** till **på**.

2. Klicka på **Spara**.

3. Detta startar den första synkroniseringen, vilket kan ta flera timmar beroende på hur många användare finns i Workday variabeln.

4. Enskilda sync händelser kan visas i den **granskningsloggar** fliken. **[Finns i guiden för etablering reporting detaljerade anvisningar om hur du tolkar granskningsloggarna](active-directory-saas-provisioning-reporting.md)**

5. En klar skrivs en översikt över kontrollrapport den **etablering** fliken enligt nedan.


## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Konfigurera tillbakaskrivning av e-postadresser till Workday
Följ dessa instruktioner för att konfigurera tillbakaskrivning av användare e-postadresser från Azure Active Directory till Workday.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Del 1: Lägga till appen etablering koppling och skapa anslutningen till Workday

**Konfigurera Workday för Active Directory-etablering:**

1.  Gå till <https://portal.azure.com>

2.  I det vänstra navigeringsfältet väljer **Azure Active Directory**

3.  Välj **företagsprogram**, sedan **alla program**.

4.  Välj **lägga till ett program**och välj den **alla** kategori.

5.  Sök efter **Workday tillbakaskrivning**, och Lägg till appen från galleriet.

6.  När appen har lagts till och skärmen app information är visas, väljer **etablering**

7.  Ändra den **etablering** **läge** till **automatisk**

8.  Slutför den **administratörsautentiseringsuppgifter** avsnittet på följande sätt:

   * **Admin Username** – ange användarnamnet för system-kontot Workday-integrering med klienten domännamnet tillagt. Ska se ut ungefär:username@contoso4

   * **Administratörslösenordet –** ange lösenordet för kontot Workday-integrering system

   * **Klient URL –** anger du Webbadressen till Workday web services-slutpunkten för din klient. Detta ska se ut: https://wd3-impl-services1.workday.com/ccx/service/contoso4 där contoso4 ersätts med rätt-klientnamn och wd3 impl ersätts med rätt miljö-sträng (vid behov).

   * **E-postmeddelande –** ange din e-postadress och markera kryssrutan ”Skicka e-post om fel inträffar”.

   * Klicka på den **Testanslutningen** knappen. Om Anslutningstestet lyckas klickar du på den **spara** längst upp. Om det misslyckas, kan du kontrollera att Workday-URL och autentiseringsuppgifter är giltiga i Workday.


### <a name="part-2-configure-attribute-mappings"></a>Del 2: Konfigurera attributmappning 


I det här avsnittet ska du konfigurera hur informationen flödar från Workday till Active Directory.

1.  På fliken etablering under **mappningar**, klickar du på **synkronisera Azure AD-användare Workday**.

2.  I den **källa Objektområde** fält du kan du filtrera vilka uppsättningar med användare i Azure Active Directory ska ha sina e-postadresser skrivs tillbaka till Workday. Standardvärde är ”alla användare i Azure AD”. 

3.  I den **attributet mappningar** avsnitt, kan du definiera hur enskilda Workday attribut mappar till Active Directory-attribut. Det finns en mappning för e-postadressen som standard. Det matchande ID måste dock uppdateras för att matcha användare i Azure AD med sina motsvarande poster i Workday. En populär matchande metod är att synkronisera Workday worker-ID eller anställnings-ID-extensionAttribute1 15 i Azure AD och sedan använda det här attributet i Azure AD för att matcha användare i Workday.

4.  Om du vill spara dina mappningar klickar du på **spara** överst i avsnittet attributmappning.

### <a name="part-3-start-the-service"></a>Del 3: Starta tjänsten
När delar 1 – 2 har slutförts, kan du starta tjänsten etablering.

1.  I den **etablering** ställer du in den **Status för etablering** till **på**.

2. Klicka på **Spara**.

3. Detta startar den första synkroniseringen, vilket kan ta flera timmar beroende på hur många användare finns i Workday variabeln.

4. Enskilda sync händelser kan visas i den **granskningsloggar** fliken. **[Finns i guiden för etablering reporting detaljerade anvisningar om hur du tolkar granskningsloggarna](active-directory-saas-provisioning-reporting.md)**

5. En klar skrivs en översikt över kontrollrapport den **etablering** fliken enligt nedan.


## <a name="customizing-the-list-of-workday-user-attributes"></a>Anpassa lista över användarattribut för Workday
Arbetsdagen tillhandahållning av appar för Active Directory och Azure AD innehåller en standardlistan över Workday användarattribut som du kan välja från. De här listorna är dock inte omfattande. Workday stöder många hundratals möjliga användarattribut som kan vara antingen standard eller unik för din Workday-klient. 

Azure AD Etablerar tjänsten stöder möjligheten att anpassa listan eller Workday-attribut innehåller alla attribut som visas i den [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v29.2/Get_Workers.html) driften av personal-API.

Om du vill göra detta måste du använda [Workday Studio](https://community.workday.com/studio-download) att extrahera XPath-uttryck som representerar de attribut som du vill använda och Lägg sedan till dem i etablering konfigurationen med hjälp av avancerade attributet editor i Azure-portalen.

**Att hämta ett XPath-uttryck för ett Workday användarattribut:**

1. Hämta och installera [Workday Studio](https://community.workday.com/studio-download). Du behöver en arbetsdag community-konto för åtkomst till installationsprogrammet.

2. Hämta filen Workday Human_Resources Gränssnittsbeskrivningar från denna URL: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v29.2/Human_Resources.wsdl

3. Starta arbetsdagar Studio.

4. I kommandofältet väljer du den **Workday > Test-webbtjänsten i Tester** alternativet.

5. Välj **externa**, och välj Human_Resources WSDL-filen som du hämtade i steg 2.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio1.PNG)

6. Ange den **plats** till `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, men ersätt ”IMPL-CC” med den faktiska instanstyp och ”klient” med din verkliga innehavarens namn.

7. Ange **åtgärden** till **Get_Workers**

8.  Klicka på små **konfigurera** länken nedan rutorna begäran och svar för att ange dina Workday-autentiseringsuppgifter. Kontrollera **autentisering**, och ange användarnamn och lösenord för kontot Workday-integrering system. Se till att formatera användarnamnet som name@tenant, och lämna den **WS-Security UsernameToken** alternativ som valts.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio2.PNG)

9. Välj **OK**.

10. Den **begära** rutan klistra in XML-koden nedan och ange **Employee_ID** till medarbetare-ID för en verklig användare i din Workday-klient. Välj en användare som har attributet fylls i om du vill extrahera.

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v28.0">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```
 
11. Klicka på den **skicka begäran** (grön pil) för att köra kommandot. Om lyckas svaret ska visas i den **svar** fönstret. Kontrollera svaret på Kontrollera den innehåller data för användar-ID du angav och inte ett fel.

12. Om det lyckas, kopiera XML-filen från den **svar** fönstret och spara den som en XML-fil.

13. I kommando-fältet i Studio för Workday Välj **fil > öppen fil...**  och öppna XML-filen som du just har sparat. Då öppnas den i Workday Studio XML-redigeraren.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio3.PNG)

14. I trädet filen navigera genom **/env:Envelope > ENV > wd:Get_Workers_Response > wd:Response_Data > wd:Worker** att hitta dina användardata. 

15. Under **wd:Worker**, hitta attributet som du vill lägga till och markera den.

16. Kopiera XPath-uttrycket för din valda attribut från den **dokumentsökväg** fältet.

17. Ta bort den **/env:Envelope / ENV / wd:Get_Workers_Response / wd:Response_Data /** prefix från det kopierade uttrycket. 

18. Om det sista objektet i det kopierade uttrycket är en nod (exempel ”: / wd:Birth_Date”), lägger **/text()** i slutet av uttrycket. Detta är inte nödvändigt om det sista objektet är ett attribut (exempel ”:/@wd:type”).

19. Resultatet bör vara ungefär `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Det här är vad du kopierar till Azure-portalen.


**Lägg till din anpassade Workday användarattribut i konfigurationen av etablering:**

1. Starta den [Azure-portalen](https://portal.azure.com), och gå till avsnittet etablering arbetsdagen etablering programmet, enligt beskrivningen tidigare i den här kursen.

2. Ange **Status för etablering** till **av**, och välj **spara**. Detta bidrar till att ändringarna börjar gälla endast när du är klar.

3. Under **mappningar**väljer **synkronisera anställda för OnPremises** (eller **synkronisera arbetare till Azure AD**).

4. Bläddra längst ned på nästa skärm och välj **visa avancerade alternativ**.

5. Välj **redigera attributlistan för Workday**.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio_AAD1.PNG)

6. Bläddra längst ned i attributlistan till där indatafält är.

7. För **namn**, ange ett namn för din attribut.

8. För **typen**, Välj typ som motsvarar korrekt till attribut (**sträng** är de vanligaste).

9. För **API uttryck**, ange det XPath-uttryck som du kopierade från Workday Studio. Exempel:`wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Välj **lägga till attributet**.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio_AAD2.PNG)

11. Välj **spara** ovan, och sedan **Ja** till dialogrutan. Stäng fönstret attributmappning om det fortfarande är öppet.

12. Tillbaka på primära **etablering** väljer **synkronisera anställda för OnPremises** (eller **synkronisera arbetare till Azure AD**) igen.

13. Välj **Lägg till ny mappning**.

14. Din nya attributet bör nu visas i den **källattribut** lista.

15. Lägga till en mappning för din nya attributet efter behov.

16. När du är klar, Kom ihåg att ange **Status för etablering** tillbaka till **på** och spara.


## <a name="known-issues"></a>Kända problem

* När du kör den **Lägg till ADSyncAgentAzureActiveDirectoryConfiguration** Powershell-kommandot, det är för närvarande ett känt problem med global administratörsbehörighet fungerar inte om de använder en anpassad domän (exempel: admin@contoso.com) . Som en tillfällig lösning kan skapa och använda ett konto som global administratör i Azure AD med en onmicrosoft.com-domän (exempel: admin@contoso.onmicrosoft.com).

* En tidigare med visas inte i Azure AD-klienter finns i Europeiska unionen-granskningsloggarna är löst. Dock krävs ytterligare konfigurationen för Azure AD-klienter i EU. Mer information finns i [del 3: konfigurera lokal synkronisering agent](#Part 3: Configure the on-premises synchronization agent)


## <a name="additional-resources"></a>Ytterligare resurser
* [Självstudier: Konfigurera enkel inloggning mellan Workday och Azure Active Directory](active-directory-saas-workday-tutorial.md)
* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granska loggarna och få rapporter om etablering aktivitet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting)
