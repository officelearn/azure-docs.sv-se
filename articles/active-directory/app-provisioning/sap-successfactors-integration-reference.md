---
title: Azure Active Directory-och SAP SuccessFactors-integrerings referens
description: Teknisk djup inblick i SAP SuccessFactors – HR driven-etablering
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/20/2020
ms.author: chmutali
ms.openlocfilehash: 3c1d0d05554fafb4b18d8dc7043cca3e8479b35e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099995"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Hur Azure Active Directory etablering integreras med SAP SuccessFactors 

[Azure Active Directory användar etablerings tjänsten](../app-provisioning/user-provisioning.md) integreras med [SAP SuccessFactors personal Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) för att hantera användarens identitets livs cykel. Azure Active Directory erbjuder tre färdiga integreringar: 

* SuccessFactors till lokala Active Directory användar etablering
* SuccessFactors för att Azure Active Directory användar etablering
* SuccessFactors tillbakaskrivning

Den här artikeln förklarar hur integreringen fungerar och hur du kan anpassa etablerings beteendet för olika HR-scenarier. 

## <a name="establishing-connectivity"></a>Upprätta anslutning 
Azure AD Provisioning-motorn använder grundläggande autentisering för att ansluta till medarbetarnas centrala OData API-slutpunkter. När du konfigurerar SuccessFactors Provisioning-appen använder du *klientens URL* -parameter i avsnittet *admin-autentiseringsuppgifter* för att konfigurera API- [datacenter-URL: en](https://apps.support.sap.com/sap/support/knowledge/en/2215682). 

För att ytterligare skydda anslutningen mellan Azure AD Provisioning-tjänsten och SuccessFactors kan du lägga till Azure AD IP-intervall i IP-listan för SuccessFactors IP-adresser med hjälp av stegen som beskrivs nedan:

* Hämta de [senaste IP-intervallen](https://www.microsoft.com/download/details.aspx?id=56519) för det offentliga Azure-molnet 
* Öppna filen och Sök efter Taggar **AzureActiveDirectory** och **AzureActiveDirectoryDomainServices** 

  >[!div class="mx-imgBorder"] 
  >![IP-intervall för Azure AD](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

* Kopiera alla IP-adressintervall som anges i elementets *addressPrefixes* och Använd intervallet för att bygga listan över IP-adresser.
* Översätt CIDR-värdena till IP-intervall.  
* Logga in på SuccessFactors Admin Portal för att lägga till IP-intervall i listan över tillåtna. Se SAP [support anmärkning 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200). Nu kan du [Ange IP-intervall](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) i det här verktyget. 

## <a name="supported-entities"></a>Entiteter som stöds
För varje användare i SuccessFactors hämtar Azure AD Provisioning-tjänsten följande entiteter. Varje entitet expanderas med hjälp av OData API- *$Expand* Frågeparametern. Se kolumnen *hämtnings regel* nedan. Vissa entiteter expanderas som standard, medan vissa entiteter expanderas endast om ett visst attribut finns i mappningen. 

| \# | SuccessFactors-entitet                  | OData-nod     | Hämtnings regel |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | *rotnod*                  | Alltid           |
| 2  | PerPersonal                            | personalInfoNav              | Alltid           |
| 3  | PerPhone                               | phoneNav                     | Alltid           |
| 4  | PerEmail                               | emailNav                     | Alltid           |
| 5  | EmpEmployment                          | employmentNav                | Alltid           |
| 6  | Användare                                   | employmentNav/userNav        | Alltid           |
| 7  | EmpJob                                 | employmentNav/jobInfoNav     | Alltid           |
| 8  | EmpEmploymentTermination               | activeEmploymentsCount       | Alltid           |
| 9  | FOCompany                              | employmentNav/jobInfoNav/companyNav | Endast om attributet Company eller companyId har mappats |
| 10 | FODepartment                           | employmentNav/jobInfoNav/departmentNav | Endast om avdelnings-eller departmentId-attributet har mappats |
| 11 | FOBusinessUnit                         | employmentNav/jobInfoNav/businessUnitNav | Endast om businessUnit-eller businessUnitId-attributet har mappats |
| 12 | FOCostCenter                           | employmentNav/jobInfoNav/costCenterNav | Endast om costCenter-eller costCenterId-attributet har mappats |
| 13 | FODivision                             | employmentNav/jobInfoNav/divisionNav  | Endast om division eller divisionId-attribut har mappats |
| 14 | FOJobCode                              | employmentNav/jobInfoNav/jobCodeNav  | Endast om jobCode-eller jobCodeId-attributet har mappats |
| 15 | FOPayGrade                             | employmentNav/jobInfoNav/payGradeNav  | Endast om payGrade-attributet har mappats |
| 16 | FOLocation                             | employmentNav/jobInfoNav/locationNav  | Endast om attributet Location har mappats |
| 17 | FOCorporateAddressDEFLT                | employmentNav/jobInfoNav/addressNavDEFLT  | Om mappningen innehåller något av följande attribut: officeLocationAddress, officeLocationCity, officeLocationZipCode |
| 18 | FOEventReason                          | employmentNav/jobInfoNav/eventReasonNav  | Endast om eventReason-attributet har mappats |
| 19 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | Endast om assignmentType har mappats |
| 20 | EmploymentType-listruta                | employmentNav/jobInfoNav/employmentTypeNav | Endast om employmentType har mappats |
| 21 | EmployeeClass-listruta                 | employmentNav/jobInfoNav/employeeClassNav | Endast om employeeClass har mappats |
| 22 | EmplStatus-listruta                    | employmentNav/jobInfoNav/emplStatusNav | Endast om emplStatus har mappats |
| 23 | AssignmentType-listruta                | employmentNav/empGlobalAssignmentNav/assignmentTypeNav | Endast om assignmentType har mappats |

## <a name="how-full-sync-works"></a>Hur fullständig synkronisering fungerar
Baserat på mappning av attribut, under fullständig synkronisering, skickar Azure AD Provisioning-tjänsten följande "GET"-OData API-fråga för att hämta effektiva data för alla aktiva användare. 

> [!div class="mx-tdCol2BreakAll"]
>| Parameter | Beskrivning |
>| ----------|-------------|
>| OData API-värd | Lägger till https i *klient-URL: en*. Exempel: `https://api4.successfactors.com` |
>| OData API-slutpunkt | `/odata/v2/PerPerson` |
>| Frågeparameter för OData-$format | `json` |
>| Frågeparameter för OData-$filter | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| Frågeparameter för OData-$expand | Detta parameter värde beror på vilka attribut som mappas. Exempel: `employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| Frågeparameter för OData-customPageSize | `100` |

> [!NOTE]
> Under den första första fullständiga synkroniseringen hämtar inte Azure AD Provisioning-tjänsten inaktiva/avslutade arbets uppgifter.

För varje SuccessFactors-användare söker etablerings tjänsten efter ett konto i målet (Azure AD/on-premises Active Directory) med matchande attribut som definierats i mappningen. Exempel: om *personIdExternal* mappar till *Anställningsnr* och har angetts som matchande attribut, använder etablerings tjänsten värdet *personIdExternal* för att söka efter användaren med filtret *Anställningsnr* . Om en användar matchning hittas uppdateras målattributet. Om ingen matchning hittas skapas en ny post i målet. 

Om du vill verifiera de data som returneras av din OData API-slutpunkt för en viss *personIdExternal*uppdaterar du *SuccessFactorsAPIEndpoint* i API-frågan nedan med URL: en för API-datacenter-servern och använder ett verktyg som [Postman](https://www.postman.com/downloads/) för att anropa frågan. 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>Så här fungerar stegvis synkronisering

Efter fullständig synkronisering upprätthåller Azure AD Provisioning-tjänsten *LastExecutionTimestamp* och använder den för att skapa delta frågor för att hämta stegvisa ändringar. De tidsstämplar-attribut som finns i varje SuccessFactors entitet, till exempel *lastModifiedDateTime*, *StartDate*, *EndDate*och *latestTerminationDate*, utvärderas för att se om ändringen ligger mellan *LastExecutionTimestamp* och *CurrentExecutionTime*. Om ja, anses post ändringen vara effektiv och bearbetas för synkronisering. 

## <a name="reading-attribute-data"></a>Läser attribut data

När Azure AD Provisioning-tjänsten frågar SuccessFactors, hämtar den en JSON-resultat uppsättning. JSON-resultatet innehåller ett antal attribut lagrade i personal Central. Som standard konfigureras etablerings schema för att bara hämta en delmängd av dessa attribut. 

Om du vill hämta ytterligare attribut följer du stegen i listan nedan:
    
* Gå till **företags program**  ->  **SuccessFactors app**  ->  **Provisioning**  ->  **Edit Revisioning**  ->  **Attribute Mapping-sidan**.
* Rulla nedåt och klicka på **Visa avancerade alternativ**.
* Klicka på **Redigera attributlistan för SuccessFactors**. 

> [!NOTE] 
> Om alternativet **Redigera attributlistan för SuccessFactors** inte visas i Azure Portal använder du URL: en *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* för att komma åt sidan. 

* I kolumnen **API-uttryck** i den här vyn visas de JSONPath-uttryck som används av anslutningen.
  >[!div class="mx-imgBorder"] 
  >![API – uttryck](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  
* Du kan antingen redigera ett befintligt JSONPath-värde eller lägga till ett nytt attribut med ett giltigt JSONPath-uttryck i schemat. 

Nästa avsnitt innehåller en lista över vanliga scenarier för att redigera JSONPath-värden. 

## <a name="handling-different-hr-scenarios"></a>Hantera olika HR-scenarier

JSONPath är ett frågespråk för JSON som liknar XPath för XML. Som XPath tillåter JSONPath extrahering och filtrering av data från en JSON-nyttolast.
Genom att använda JSONPath-omvandling kan du anpassa beteendet för Azure AD Provisioning-appen för att hämta anpassade attribut och hantera scenarier som återställning, konvertering av arbetare och global tilldelning. 

Det här avsnittet beskriver hur du kan anpassa etablerings appen för följande HR-scenarier: 
* [Hämtar ytterligare attribut](#retrieving-additional-attributes)
* [Hämtar anpassade attribut](#retrieving-custom-attributes)
* [Hantera konverterings scenario för arbetare](#handling-worker-conversion-scenario)
* [Hantering av återställnings scenario](#handling-rehire-scenario)
* [Hanterar scenario för global tilldelning](#handling-global-assignment-scenario)
* [Scenario för att hantera samtidiga jobb](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>Hämtar ytterligare attribut

Azure AD SuccessFactors-Provisioning-appens schema levereras med [90 och fördefinierade attribut](sap-successfactors-attribute-reference.md). Använd stegen nedan om du vill lägga till fler SuccessFactors-attribut i det etablerings schema som anges nedan: 

* Använd OData-frågan nedan för att hämta data för en giltig test användare från personal Central. 

```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

* Bestäm vilken anställd som är associerad med attributet
  * Om attributet är en del av *EmpEmployment* -entiteten söker du efter attributet under *employmentNav* -noden. 
  * Om attributet är en del av entiteten *användare* söker du efter attributet under noden *employmentNav/userNav* .
  * Om attributet är en del av *EmpJob* -entiteten söker du efter attributet under *employmentNav/jobInfoNav-* noden. 
* Konstruera den JSON-sökväg som är kopplad till attributet och Lägg till det här nya attributet i listan över SuccessFactors-attribut. 
  * Exempel 1: anta att du vill lägga till attributet *okToRehire*, som är en del av *employmentNav* -entiteten, och sedan använda JSONPath`$.employmentNav.results[0].okToRehire`
  * Exempel 2: anta att du vill lägga till attributet *timeZone*, som är en del av *userNav* -entiteten, och sedan använda JSONPath`$.employmentNav.results[0].userNav.timeZone`
  * Exempel 3: anta att du vill lägga till attributet *flsaStatus*, som är en del av *jobInfoNav* -entiteten, och sedan använda JSONPath`$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`
* Spara schemat. 
* Starta om etablering.

### <a name="retrieving-custom-attributes"></a>Hämtar anpassade attribut

Som standard är följande anpassade attribut i förväg definierade i Azure AD SuccessFactors Provisioning-appen: 
* *custom01 – Custom15* från användarens (userNav) entitet
* *customString1 – customString15* från EmpEmployment-entiteten (employmentNav) som kallas *empNavCustomString1-empNavCustomString15*
* *customString1 – customString15* från EmpJobInfo-entiteten (jobInfoNav) som kallas *empJobNavCustomString1-empNavJobCustomString15*

Låt oss säga att *customString35* -attributet i *EmpJobInfo* lagrar plats beskrivningen i din anställdes centrala instans. Du vill flöda det här värdet till Active Directory *physicalDeliveryOfficeName* -attribut. Använd stegen nedan för att konfigurera attributmappning för det här scenariot: 

* Redigera SuccessFactors-attributlistan för att lägga till ett nytt attribut med namnet *empJobNavCustomString35*.
* Ange JSONPath API-uttryck för det här attributet som:`$.employmentNav.results[0].jobInfoNav.results[0].customString35`
* Spara och Läs in mappnings ändringen i Azure Portal.  
* I bladet attributmappning mappar du *empJobNavCustomString35* till *physicalDeliveryOfficeName*.
* Spara mappningen.

Utöka det här scenariot: 
* Om du vill mappa *custom35* -attribut från entiteten *användare* använder du JSONPath`$.employmentNav.results[0].userNav.custom35`
* Om du vill mappa *customString35* -attribut från entiteten *EmpEmployment* använder du JSONPath`$.employmentNav.results[0].customString35`

### <a name="handling-worker-conversion-scenario"></a>Hantera konverterings scenario för arbetare

Konvertering av arbetare är en process där en befintlig heltids anställd konverteras till en entreprenör eller vice versa. I det här scenariot lägger personal Central till en ny *EmpEmployment* -entitet tillsammans med en ny *användar* enhet för samma *person* entitet. Den *användar* enhet som är kapslad under den tidigare *EmpEmployment* -entiteten har angetts till null. Om du vill hantera det här scenariot så att nya anställnings data visas när en konvertering inträffar kan du samla in ett program schema för etablering med hjälp av stegen i listan nedan:  

* Öppna bladet attributmappning för SuccessFactors Provisioning-appen. 
* Rulla nedåt och klicka på **Visa avancerade alternativ**.
* Klicka på länken **granska schemat här** för att öppna schema redigeraren. 
  >![granska – schema](media/sap-successfactors-integration-reference/review-schema.png#lightbox)
* Klicka på **nedladdnings** länken för att spara en kopia av schemat innan du redigerar. 
  >![Ladda ned – schema](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
* Tryck på CTRL-H-tangenten i schema redigeraren för att öppna kontrollen Sök och ersätt.
* Kopiera i text rutan Sök och klistra in värdet`$.employmentNav.results[0]`
* I rutan Ersätt text kopierar du och klistrar in värdet `$.employmentNav.results[?(@.userNav != null)]` . Observera det blank steg `!=` som omger operatorn, vilket är viktigt för lyckad bearbetning av JSONPath-uttrycket. 
  >![Sök-Ersätt-Conversion](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
* Klicka på alternativet för att ersätta alla för att uppdatera schemat. 
* Spara schemat. 
* Ovanstående process uppdaterar alla JSONPath-uttryck enligt följande: 
  * Gammal JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Ny JSONPath:`$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
* Starta om etablering. 

### <a name="handling-rehire-scenario"></a>Hantering av återställnings scenario

Det finns vanligt vis två alternativ för att bearbeta återställningar: 
* Alternativ 1: skapa en ny person profil i personal centralen
* Alternativ 2: Återanvänd befintlig person profil i personal centralen

Om din HR-process använder alternativ 1, krävs inga ändringar för etablerings schemat. Om din HR-process använder alternativ 2 lägger personal centralen till en ny *EmpEmployment* -entitet tillsammans med *en ny entitet för entiteten* *person* . Till skillnad från konverterings scenariot behåller den tidigare *EmpEmployment* -entiteten entiteten *användare* och har inte angetts till null. 

Om du vill hantera det här återställnings scenariot (alternativ 2), så att de senaste anställnings uppgifterna visas för återställnings profiler, kan du samla in ett etablerings program schema enligt stegen nedan:  

* Öppna bladet attributmappning för SuccessFactors Provisioning-appen. 
* Rulla nedåt och klicka på **Visa avancerade alternativ**.
* Klicka på länken **granska schemat här** för att öppna schema redigeraren.   
* Klicka på **nedladdnings** länken för att spara en kopia av schemat innan du redigerar.   
* Tryck på CTRL-H-tangenten i schema redigeraren för att öppna kontrollen Sök och ersätt.
* Kopiera i text rutan Sök och klistra in värdet`$.employmentNav.results[0]`
* I rutan Ersätt text kopierar du och klistrar in värdet `$.employmentNav.results[-1:]` . Det här JSONPath-uttrycket returnerar den senaste *EmpEmployment* -posten.   
* Klicka på alternativet för att ersätta alla för att uppdatera schemat. 
* Spara schemat. 
* Ovanstående process uppdaterar alla JSONPath-uttryck enligt följande: 
  * Gammal JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Ny JSONPath:`$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
* Starta om etablering. 

### <a name="handling-global-assignment-scenario"></a>Hanterar scenario för global tilldelning

När en användare i personal Central bearbetas för global tilldelning lägger SuccessFactors till en ny *EmpEmployment* -entitet och anger *ASSIGNMENTCLASS* till "ga". Det skapar också en ny entitet för *användare* . Därför har användaren nu:
* En *EmpEmployment*-  +  *användarsession* som motsvarar hem tilldelning med *assignmentClass* inställd på "St" och 
* En annan *EmpEmployment*  +  *User* entitet som motsvarar den globala tilldelningen med *assignmentClass* inställt på "ga"

Använd stegen nedan för att hämta attribut som tillhör standard tilldelningen och användar profilen för global tilldelning: 

* Öppna bladet attributmappning för SuccessFactors Provisioning-appen. 
* Rulla nedåt och klicka på **Visa avancerade alternativ**.
* Klicka på länken **granska schemat här** för att öppna schema redigeraren.   
* Klicka på **nedladdnings** länken för att spara en kopia av schemat innan du redigerar.   
* Tryck på CTRL-H-tangenten i schema redigeraren för att öppna kontrollen Sök och ersätt.
* Kopiera i text rutan Sök och klistra in värdet`$.employmentNav.results[0]`
* I rutan Ersätt text kopierar du och klistrar in värdet `$.employmentNav.results[?(@.assignmentClass == 'ST')]` . 
* Klicka på alternativet för att ersätta alla för att uppdatera schemat. 
* Spara schemat. 
* Ovanstående process uppdaterar alla JSONPath-uttryck enligt följande: 
  * Gammal JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Ny JSONPath:`$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
* Läs in bladets attribut mappnings blad igen. 
* Rulla nedåt och klicka på **Visa avancerade alternativ**.
* Klicka på **Redigera attributlistan för SuccessFactors**.
* Lägg till nya attribut för att hämta globala tilldelnings data. Exempel: om du vill hämta det avdelnings namn som är associerat med en global tilldelnings profil kan du lägga till attributet **globalAssignmentDepartment** med JSONPath-uttrycket inställt på `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` . 
* Du kan nu antingen flöda båda avdelnings värden till Active Directory attribut eller selektivt flöda ett värde med uttrycks mappning. Exempel: uttrycket nedan anger värdet för AD *Department* -attribut att *globalAssignmentDepartment* om det finns, annars ställer det in värdet på den *avdelning* som är kopplad till standard tilldelning. 
  * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`
* Spara mappningen. 
* Starta om etablering. 

### <a name="handling-concurrent-jobs-scenario"></a>Scenario för att hantera samtidiga jobb

När en användare i Employee Central har samtidiga/flera jobb, finns det två *EmpEmployment* -och entiteter för *användare* med *assignmentClass* inställt på "St". Använd stegen nedan om du vill hämta attribut som hör till båda jobben: 

* Öppna bladet attributmappning för SuccessFactors Provisioning-appen. 
* Rulla nedåt och klicka på **Visa avancerade alternativ**.
* Klicka på **Redigera attributlistan för SuccessFactors**.
* Anta att du vill hämta den avdelning som är kopplad till jobb 1 och jobb 2. Den fördefinierade Attribute- *avdelningen* hämtar redan värdet för avdelning för det första jobbet. Du kan definiera ett nytt attribut med namnet *secondJobDepartment* och ange JSONPath-uttrycket till`$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
* Du kan nu antingen flöda båda avdelnings värden till Active Directory attribut eller selektivt flöda ett värde med uttrycks mappning. 
* Spara mappningen. 
* Starta om etablering. 

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du konfigurerar SuccessFactors för att Active Directory etablering](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Lär dig hur du konfigurerar tillbakaskrivning till SuccessFactors](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [Läs mer om SuccessFactors-attribut som stöds för inkommande etablering](sap-successfactors-attribute-reference.md)










