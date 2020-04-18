---
title: Anpassa Azure AD-attributmappningar | Microsoft-dokument
description: Lär dig vilka attributmappningar för SaaS-appar i Azure Active Directory som du kan ändra dem för att tillgodose dina affärsbehov.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfee19e9cfd1def71ebad82c2210ffc10146c896
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639749"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Anpassa användaretablering av attributmappningar för SaaS-program i Azure Active Directory

Microsoft Azure AD ger stöd för användaretablering till SaaS-program från tredje part, till exempel Salesforce, G Suite och andra. Om du aktiverar användaretablering för ett SaaS-program från tredje part styr Azure-portalen sina attributvärden genom attributmappningar.

Det finns en förkonfigurerad uppsättning attribut och attributmappningar mellan Azure AD-användarobjekt och varje SaaS-apps användarobjekt. Vissa appar hanterar andra typer av objekt tillsammans med användare, till exempel Grupper.

Du kan anpassa standardattributmappningarna efter dina affärsbehov. Du kan därför ändra eller ta bort befintliga attributmappningar eller skapa nya attributmappningar.

## <a name="editing-user-attribute-mappings"></a>Redigera mappningar av användarattribut

Följ dessa steg för att komma åt funktionen **Mappningar** för användaretablering:

1. Logga in på [Azure Active Directory-portalen](https://aad.portal.azure.com).
1. Välj **Enterprise-program** i den vänstra rutan. En lista över alla konfigurerade appar visas, inklusive appar som har lagts till från galleriet.
1. Välj en app om du vill läsa in apphanteringsfönstret, där du kan visa rapporter och hantera appinställningar.
1. Välj **Etablering** om du vill hantera etableringsinställningar för användarkonton för den valda appen.
1. Expandera **mappningar** för att visa och redigera användarattribut som flödar mellan Azure AD och målprogrammet. Om målprogrammet stöder det kan du i det här avsnittet konfigurera etablering av grupper och användarkonton.

   ![Använda Mappningar för att visa och redigera användarattribut](./media/customize-application-attributes/21.png)

1. Välj en **mappningskonfiguration** för att öppna den relaterade skärmen **Attributmappning.** Vissa attributmappningar krävs av ett SaaS-program för att fungera korrekt. För obligatoriska attribut är **funktionen Ta bort** inte tillgänglig.

   ![Använda attributmappning för att konfigurera attributmappningar för appar](./media/customize-application-attributes/22.png)

   I den här skärmbilden kan du se att **attributet Användarnamn för** ett hanterat objekt i Salesforce fylls med värdet **userPrincipalName** för det länkade Azure Active Directory-objektet.

1. Välj en befintlig **attributmappning** för att öppna skärmen **Redigera attribut.** Här kan du redigera användarattributen som flödar mellan Azure AD och målprogrammet.

   ![Använda Redigera attribut för att redigera användarattribut](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Förstå attributmappningstyper

Med attributmappningar styr du hur attribut fylls i i ett SaaS-program från tredje part.
Det finns fyra olika mappningstyper som stöds:

- **Direkt** – målattributet fylls med värdet för ett attribut för det länkade objektet i Azure AD.
- **Konstant** – målattributet fylls med en specifik sträng som du har angett.
- **Uttryck** - målattributet fylls i baserat på resultatet av ett skriptliknande uttryck.
  Mer information finns i [Skriva uttryck för attributmappningar i Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md).
- **Ingen** - målattributet lämnas oförändrad. Men om målattributet någonsin är tomt fylls det med det standardvärde som du anger.

Tillsammans med dessa fyra grundläggande typer stöder anpassade attributmappningar konceptet med en valfri standardvärdetilldelning. **default** Standardvärdetilldelningen säkerställer att ett målattribut fylls i med ett värde om det inte finns ett värde i Azure AD eller på målobjektet. Den vanligaste konfigurationen är att lämna detta tomt.

### <a name="understanding-attribute-mapping-properties"></a>Förstå egenskaper för attributmappning

I föregående avsnitt har du redan introducerats till egenskapen attributmappningstyp.
Tillsammans med den här egenskapen stöder attributmappningar även följande attribut:

- **Källattribut** - Användarattributet från källsystemet (exempel: Azure Active Directory).
- **Målattribut** – Användarattributet i målsystemet (exempel: ServiceNow).
- **Standardvärde om null (valfritt)** - Det värde som skickas till målsystemet om källattributet är null. Det här värdet etableras endast när en användare skapas. Standardvärdet när null inte etableras när en befintlig användare uppdateras. Om du till exempel vill etablera alla befintliga användare i målsystemet med en viss befattning (när den är null i källsystemet) kan du använda följande [uttryck](../app-provisioning/functions-for-customizing-application-data.md): Switch(IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]). Se till att ersätta "Standardvärdet" med vad du vill etablera när null i källsystemet. 
- **Matcha objekt med det här attributet** – Om den här mappningen ska användas för att unikt identifiera användare mellan käll- och målsystemen. Det är vanligtvis inställt på attributet userPrincipalName eller mail i Azure AD, som vanligtvis mappas till ett användarnamnsfält i ett målprogram.
- **Matchande prioritet** – Flera matchande attribut kan ställas in. När det finns flera utvärderas de i den ordning som definieras av det här fältet. Så snart en matchning hittas utvärderas inga ytterligare matchande attribut.
- **Använd den här mappningen**
  - **Alltid** – Använd den här mappningen på både åtgärder för att skapa användare och uppdatera.
  - **Endast under skapandet** - Använd den här mappningen endast på åtgärder för att skapa användare.

## <a name="matching-users-in-the-source-and-target--systems"></a>Matcha användare i käll- och målsystemen
Azure AD-etableringstjänsten kan distribueras i både "greenfield"-scenarier (där användare inte avslutar i målsystemet) och "brownfield"-scenarier (där användare redan finns i målsystemet). För att stödja båda scenarierna använder etableringstjänsten begreppet matchande attribut. Med matchande attribut kan du bestämma hur du unikt identifierar en användare i källan och matchar användaren i målet. Som en del av planeringen av distributionen identifierar du det attribut som kan användas för att unikt identifiera en användare i käll- och målsystemen. Saker att notera:

- **Matchande attribut ska vara unika:** Kunder använder ofta attribut som userPrincipalName, e-post eller objekt-ID som matchande attribut.
- **Flera attribut kan användas som matchande attribut:** Du kan definiera flera attribut som ska utvärderas när användare matchas och i vilken ordning de utvärderas (definieras som matchande prioritet i användargränssnittet). Om du till exempel definierar tre attribut som matchande attribut och en användare matchas unikt efter att ha utvärderat de två första attributen, utvärderar tjänsten inte det tredje attributet. Tjänsten utvärderar matchande attribut i den angivna ordningen och slutar utvärdera när en matchning hittas.  
- **Värdet i källan och målet behöver inte matcha exakt:** Värdet i målet kan vara en enkel funktion av värdet i källan. Så kan man ha ett e-postadressattribut i källan och användarenPrincipalName i målet och matcha med en funktion av attributet emailAddress som ersätter vissa tecken med ett konstant värde.  
- **Matchning baserat på en kombination av attribut stöds inte:** De flesta program stöder inte frågor baserat på två egenskaper. Därför är det inte möjligt att matcha baserat på en kombination av attribut. Det är möjligt att utvärdera enskilda egenskaper efter den andra.
- **Alla användare måste ha ett värde för minst ett matchande attribut:** Om du definierar ett matchande attribut måste alla användare ha ett värde för attributet i källsystemet. Om du till exempel definierar userPrincipalName som matchande attribut måste alla användare ha ett userPrincipalName. Om du definierar flera matchande attribut (t.ex. extensionAttribute1 och e-post) behöver inte alla användare ha samma matchande attribut. En användare kan ha en extensionAttribute1 men inte e-post medan en annan användare kan ha e-post men ingen extensionAttribute1. 
- **Målprogrammet måste stödja filtrering på det matchande attributet:** Programutvecklare tillåter filtrering för en delmängd av attribut på användarens eller gruppens API. För program i galleriet ser vi till att standardattributmappningen är för ett attribut som målprogrammets API stöder filtrering på. När du ändrar standardmatchningsattributet för målprogrammet kontrollerar du API-dokumentationen från tredje part för att säkerställa att attributet kan filtreras efter.  

## <a name="editing-group-attribute-mappings"></a>Redigera gruppattributmappningar

Ett markerat antal program, till exempel ServiceNow, Box och G Suite, stöder möjligheten att etablera gruppobjekt och användarobjekt. Gruppobjekt kan innehålla gruppegenskaper som visningsnamn och e-postalias, tillsammans med gruppmedlemmar.

![Exempel visar ServiceNow med etablerade grupp- och användarobjekt](./media/customize-application-attributes/24.png)

Gruppetablering kan aktiveras eller inaktiveras genom att välja gruppmappningen under **Mappningar**och ange **Aktiverat** för det alternativ du vill använda på skärmen **Attributmappning.**

Attributen som etablerats som en del av gruppobjekt kan anpassas på samma sätt som användarobjekt som beskrivits tidigare. 

> [!TIP]
> Etablering av gruppobjekt (egenskaper och medlemmar) är ett annat begrepp än [att tilldela grupper](../manage-apps/assign-user-or-group-access-portal.md) till ett program. Det är möjligt att tilldela en grupp till ett program, men endast etablera användarobjekten i gruppen. Etablering av fullständiga gruppobjekt krävs inte för att använda grupper i tilldelningar.

## <a name="editing-the-list-of-supported-attributes"></a>Redigera listan över attribut som stöds

De användarattribut som stöds för ett visst program är förkonfigurerade. De flesta programs API:er för användarhantering stöder inte schemaidentifiering. Azure AD-etableringstjänsten kan därför inte dynamiskt generera listan över attribut som stöds genom att ringa anrop till programmet.

Vissa program stöder dock anpassade attribut och Azure AD-etableringstjänsten kan läsa och skriva till anpassade attribut. Om du vill ange sina definitioner i Azure-portalen markerar du kryssrutan **Visa avancerade alternativ** längst ned på skärmen **Attributmappning** och väljer sedan **Redigera attributlista för** din app.

Program och system som stöder anpassning av attributlistan är:

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory ([Microsoft Graph REST API v1.0-referens](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0) och anpassade katalogtillägg stöds)
- Appar som stöder [SCIM 2.0](https://tools.ietf.org/html/rfc7643), där attribut som definieras i [det grundläggande schemat](https://tools.ietf.org/html/rfc7643) måste läggas till

> [!NOTE]
> Att redigera listan över attribut som stöds rekommenderas endast för administratörer som har anpassat schemat för sina program och system och har förstahandsinformation om hur deras anpassade attribut har definierats. Detta kräver ibland förtrogenhet med API: er och utvecklarverktyg som tillhandahålls av ett program eller system.

När du redigerar listan över attribut som stöds anges följande egenskaper:

- **Namn** - Attributets systemnamn enligt definitionen i målobjektets schema.
- **Typ** - Den typ av data som attributet lagrar, enligt definitionen i målobjektets schema, som kan vara någon av följande typer:
  - *Binär -* Attributet innehåller binära data.
  - *Booleskt* - Attributet innehåller ett sant eller falskt värde.
  - *DateTime* - Attributet innehåller en datumsträng.
  - *Heltal* - Attributet innehåller ett heltal.
  - *Referens* - Attributet innehåller ett ID som refererar till ett värde som lagras i en annan tabell i målprogrammet.
  - *Sträng* - Attributet innehåller en textsträng.
- **Primärnyckel?** - Om attributet definieras som ett primärnyckelfält i målobjektets schema.
- **Krävs?** - Om attributet måste fyllas i i målprogrammet eller målsystemet.
- **Flera värden?** - Om attributet stöder flera värden.
- **Exakt fall?** - Om attributvärdena utvärderas på ett skiftlägeskänsligt sätt.
- **API-uttryck** - Använd inte, om inte instrueras att göra det av dokumentationen för en specifik etableringskoppling (till exempel Arbetsdag).
- **Refererat objektattribut** - Om det är ett referenstypsattribut kan du på den här menyn välja tabellen och attributet i målprogrammet som innehåller värdet som är associerat med attributet. Om du till exempel har ett attribut med namnet "Avdelning" vars lagrade värde refererar till ett objekt i en separat "Avdelningar"-tabell, väljer du "Departments.Name". Referenstabellerna och de primära ID-fält som stöds för ett visst program är förkonfigurerade och kan för närvarande inte redigeras med Azure-portalen, men kan redigeras med Hjälp av [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Etablera ett anpassat tilläggsattribut till ett SCIM-kompatibelt program
SCIM RFC definierar ett centralt användar- och gruppschema, samtidigt som tillägg till schemat kan uppfylla programmets behov. Så här lägger du till ett anpassat attribut i ett SCIM-program:
   1. Logga in på [Azure Active Directory-portalen,](https://aad.portal.azure.com)välj **Enterprise Applications,** välj ditt program och välj sedan **Etablering**.
   2. Under **Mappningar**markerar du det objekt (användare eller grupp) som du vill lägga till ett anpassat attribut för.
   3. Längst ned på sidan väljer du **Visa avancerade alternativ**.
   4. Välj **Redigera attributlista för AppName**.
   5. Längst ned i attributlistan anger du information om det anpassade attributet i de angivna fälten. Välj sedan **Lägg till attribut**.

För SCIM-program måste attributnamnet följa mönstret som visas i exemplet nedan. "CustomExtensionName" och "CustomAttribute" kan anpassas enligt programmets krav, till exempel:  
 * urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User:CustomAttribute 
 * urn:ietf:params:scim:schemas:extension:2.0:CustomExtensionName:CustomAttribute  
 * urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User.CustomAttributeName:value

Dessa instruktioner gäller endast för SCIM-aktiverade program. Program som ServiceNow och Salesforce är inte integrerade med Azure AD med SCIM, och därför kräver de inte det här specifika namnområdet när de lägger till ett anpassat attribut.

Anpassade attribut kan inte referera attribut eller multi-value attribut. Anpassade tilläggsattribut med flera värden stöds för närvarande endast för program i galleriet.  
 
**Exempelrepresentation av en användare med ett tilläggsattribut:**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>Etablera en roll i en SCIM-app
Följ stegen nedan för att etablera roller för en användare till ditt program. Observera att beskrivningen nedan är specifik för anpassade SCIM-program. För galleriprogram som Salesforce och ServiceNow använder du de fördefinierade rollmappningarna. Punkterna nedan beskriver hur du omvandlar attributet AppRoleAssignments till det format som programmet förväntar sig.

- Mappning av en appRoleAssignment i Azure AD till en roll i ditt program kräver att du omvandlar attributet med hjälp av ett [uttryck](../app-provisioning/functions-for-customizing-application-data.md). Attributet appRoleAssignment **bör inte mappas direkt** till ett rollattribut utan att använda ett uttryck för att tolka rollinformationen. 

- **Enkeltilldelning** 
  - **När du ska använda:** Använd uttrycket SingleAppRoleAssignment för att etablera en enskild roll för en användare och ange den primära rollen. 
  - **Konfigurerar du:** Använd stegen som beskrivs ovan för att navigera till sidan attributmappningar och använda uttrycket SingleAppRoleAssignment för att mappa till rollattributet. Det finns tre rollattribut att välja mellan: (roller[primär eq "True"].display, roller[primär eq "True].typ och roller[primär eq "True"].värde). Du kan välja att inkludera något eller alla rollattribut i dina mappningar. Om du vill inkludera mer än en lägger du bara till en ny mappning och inkluderar den som målattribut.  
  
  ![Lägg till SingleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Saker att tänka på**
    - Kontrollera att flera roller inte har tilldelats en användare. Vi kan inte garantera vilken roll som kommer att tillhandahållas.
    
  - **Exempel på utdata** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
- **AppRoleAssignmentsKomplex** 
  - **När du ska använda:** Använd uttrycket AppRoleAssignmentsComplex för att etablera flera roller för en användare. 
  - **Konfigurerar du:** Redigera listan över attribut som stöds enligt beskrivningen ovan för att inkludera ett nytt attribut för roller: 
  
    ![Lägga till roller](./media/customize-application-attributes/add-roles.png)<br>

    Använd sedan uttrycket AppRoleAssignmentsComplex för att mappa till det anpassade rollattributet som visas i bilden nedan:

    ![Lägg till AppRoleAssignmentsKomplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Saker att tänka på**
    - Alla roller etableras som primära = false.
    - POST innehåller rolltypen. PATCH-begäran innehåller inte typ. Vi arbetar på att skicka typen i både POST och PATCH förfrågningar.
    
  - **Exempel på utdata** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>Etablera ett attribut med flera värden
Vissa attribut som phoneNumbers och e-postmeddelanden är attribut med flera värden där du kan behöva ange olika typer av telefonnummer eller e-postmeddelanden. Använd uttrycket nedan för attribut med flera värden. Det låter dig ange attributtyp och mappa det till motsvarande Azure AD-användarattribut för värdet. 

* phoneNumbers[typ eq "arbete"].värde
* phoneNumbers[typ eq "mobil"].värde
* phoneNumbers[typ eq "fax"].värde

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Återställa standardattribut och attributmappningar

Om du behöver börja om och återställa dina befintliga mappningar tillbaka till standardtillståndet kan du markera kryssrutan **Återställ standardmappningar** och spara konfigurationen. Om du gör det anges alla mappningar och omfångsfilter som om programmet just har lagts till i din Azure AD-klientorganisation från programgalleriet.

Om du väljer det här alternativet kommer det att tvinga en omsynkronisering av alla användare medan etableringstjänsten körs.

> [!IMPORTANT]
> Vi rekommenderar starkt att **etableringsstatus** ställs in på **Av** innan du anropar det här alternativet.

## <a name="what-you-should-know"></a>Det här bör du känna till

- Microsoft Azure AD tillhandahåller en effektiv implementering av en synkroniseringsprocess. I en initierad miljö bearbetas endast objekt som kräver uppdateringar under en synkroniseringscykel.
- Uppdatering av attributmappningar påverkar prestanda för en synkroniseringscykel. En uppdatering av attributmappningskonfigurationen kräver att alla hanterade objekt omvärderas.
- En rekommenderad metod är att hålla antalet på varandra följande ändringar i attributmappningarna till ett minimum.
- Det går inte att lägga till ett fotoattribut som ska etableras i en app i dag eftersom du inte kan ange vilket format som ska synkronisera fotot. Du kan begära funktionen på [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory)
- Attributet IsSoftDeleted är ofta en del av standardmappningarna för ett program. IsSoftdeleted kan vara sant i ett av fyra scenarier (användaren är utanför omfånget på grund av att den inte har tilldelats från programmet, användaren är utanför omfånget på grund av att inte uppfylla ett omfångsfilter, användaren har tagits bort mjukt i Azure AD eller egenskapen AccountEnabled är inställd på false på användaren). 
- Azure AD-etableringstjänsten stöder inte etablering av null-värden

## <a name="next-steps"></a>Nästa steg

- [Automatisera etablering av användare/avetablering till SaaS-appar](user-provisioning.md)
- [Skriva uttryck för attributmappningar](../app-provisioning/functions-for-customizing-application-data.md)
- [Omfångsfilter för etablering av användare](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](use-scim-to-provision-users-and-groups.md)
- [Lista över guider om hur man integrerar SaaS-appar](../saas-apps/tutorial-list.md)
