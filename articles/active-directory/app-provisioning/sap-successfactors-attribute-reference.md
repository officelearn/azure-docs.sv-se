---
title: Referens för SAP SuccessFactors-attribut
description: Lär dig vilka attribut från SuccessFactors som stöds av SuccessFactors-HR driven-etablering
services: active-directory
author: cmmdesai
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.reviewer: celested
ms.openlocfilehash: ef2da377c7720cfb7b431d1ce0fed56656a2b8c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87808515"
---
# <a name="sap-successfactors-attribute-reference"></a>Referens för SAP SuccessFactors-attribut

I den här artikeln hittar du information om:

- [SuccessFactors entiteter och attribut](#supported-successfactors-entities-and-attributes)
- [Mappning av standardattribut](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>SuccessFactors entiteter och attribut som stöds

I tabellen nedan samlas listan över SuccessFactors-attribut som ingår som standard i följande två etablerings appar:

- [SuccessFactors för att Active Directory användar etablering](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [SuccessFactors till användar etablering i Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)

I [integrerings referensen för SAP SuccessFactors](./sap-successfactors-integration-reference.md#retrieving-additional-attributes) finns mer information om hur du utökar schemat för ytterligare attribut. 

| \# | SuccessFactors-entitet                  | SuccessFactors-attribut     | Åtgärds typ |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Läs           |
| 2  | PerPerson                              | personId                     | Läs           |
| 3  | PerPerson                              | perPersonUuid                | Läs           |
| 4  | PerPersonal                            | displayName                  | Läs           |
| 5  | PerPersonal                            | firstName                    | Läs           |
| 6  | PerPersonal                            | kön                       | Läs           |
| 7  | PerPersonal                            | lastName                     | Läs           |
| 8  | PerPersonal                            | middleName                   | Läs           |
| 9  | PerPersonal                            | preferredName                | Läs           |
| 10 | Användare                                   | addressLine1                 | Läs           |
| 11 | Användare                                   | addressLine2                 | Läs           |
| 12 | Användare                                   | addressLIne3                 | Läs           |
| 13 | Användare                                   | businessPhone                | Läs           |
| 14 | Användare                                   | cellPhone                    | Läs           |
| 15 | Användare                                   | city                         | Läs           |
| 16 | Användare                                   | land                      | Läs           |
| 17 | Användare                                   | custom01                     | Läs           |
| 18 | Användare                                   | custom02                     | Läs           |
| 19 | Användare                                   | custom03                     | Läs           |
| 20 | Användare                                   | custom04                     | Läs           |
| 21 | Användare                                   | custom05                     | Läs           |
| 22 | Användare                                   | custom06                     | Läs           |
| 23 | Användare                                   | custom07                     | Läs           |
| 24 | Användare                                   | custom08                     | Läs           |
| 25 | Användare                                   | custom09                     | Läs           |
| 26 | Användare                                   | anpassad10                     | Läs           |
| 27 | Användare                                   | custom11                     | Läs           |
| 28 | Användare                                   | custom12                     | Läs           |
| 29 | Användare                                   | custom13                     | Läs           |
| 30 | Användare                                   | custom14                     | Läs           |
| 31 | Användare                                   | empId                        | Läs           |
| 32 | Användare                                   | homePhone                    | Läs           |
| 33 | Användare                                   | jobFamily                    | Läs           |
| 34 | Användare                                   | smek namn                     | Läs           |
| 35 | Användare                                   | state                        | Läs           |
| 36 | Användare                                   | Tidszon                     | Läs           |
| 37 | Användare                                   | användarnamn                     | Läs           |
| 38 | Användare                                   | Postummer                      | Läs           |
| 39 | PerPhone                               | areaCode                     | Läs           |
| 40 | PerPhone                               | countryCode                  | Läs           |
| 41 | PerPhone                               | utöka                    | Läs           |
| 42 | PerPhone                               | phoneNumber                  | Läs           |
| 43 | PerPhone                               | phoneType                    | Läs           |
| 44 | PerEmail                               | emailAddress                 | Läsa, skriva    |
| 45 | PerEmail                               | emailType                    | Läs           |
| 46 | EmpEmployment                          | firstDateWorked              | Läs           |
| 47 | EmpEmployment                          | lastDateWorked               | Läs           |
| 48 | EmpEmployment                          | userId                       | Läs           |
| 49 | EmpEmployment                          | isContingentWorker           | Läs           |
| 50 | EmpJob                                 | countryOfCompany             | Läs           |
| 51 | EmpJob                                 | emplStatus                   | Läs           |
| 52 | EmpJob                                 | endDate                      | Läs           |
| 53 | EmpJob                                 | /SD                    | Läs           |
| 54 | EmpJob                                 | Befattning                     | Läs           |
| 55 | EmpJob                                 | position                     | Läs           |
| 65 | EmpJob                                 | customString13               | Läs           |
| 56 | EmpJob                                 | managerId                    | Läs           |
| 57 | EmpJob \. BusinessUnit                   | businessUnit                 | Läs           |
| 58 | EmpJob \. BusinessUnit                   | businessUnitId               | Läs           |
| 59 | EmpJob- \. företag                        | company                      | Läs           |
| 60 | EmpJob- \. företag                        | companyId                    | Läs           |
| 61 | EmpJob \. Company \. CountryOfRegistration | twoCharCountryCode           | Läs           |
| 62 | EmpJob \. CostCenter                     | costCenter                   | Läs           |
| 63 | EmpJob \. CostCenter                     | costCenterId                 | Läs           |
| 64 | EmpJob \. CostCenter                     | costCenterDescription        | Läs           |
| 65 | EmpJob- \. avdelning                     | avdelning                   | Läs           |
| 66 | EmpJob- \. avdelning                     | departmentId                 | Läs           |
| 67 | EmpJob- \. avdelning                       | vändning                     | Läs           |
| 68 | EmpJob- \. avdelning                       | divisionId                   | Läs           |
| 69 | EmpJob \. JobCode                        | jobCode                      | Läs           |
| 70 | EmpJob \. JobCode                        | jobCodeId                    | Läs           |
| 71 | EmpJob- \. plats                       | LocationName                 | Läs           |
| 72 | EmpJob- \. plats                       | officeLocationAddress        | Läs           |
| 73 | EmpJob- \. plats                       | officeLocationCity           | Läs           |
| 74 | EmpJob- \. plats                       | officeLocationCustomString4  | Läs           |
| 75 | EmpJob- \. plats                       | officeLocationZipCode        | Läs           |
| 76 | EmpJob \. PayGrade                       | payGrade                     | Läs           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Läs           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Läs           |

## <a name="default-attribute-mapping"></a>Mappning av standardattribut

Tabellen nedan innehåller standardattributets mappning mellan SuccessFactors-attribut som anges ovan och AD/Azure AD-attribut. I bladet för Azure AD Provisioning-appen kan du ändra standard mappningen för att inkludera attribut från listan ovan. 

| \# | SuccessFactors-entitet                  | SuccessFactors-attribut | AD/Azure AD-attributmappning som är standard   | Bearbetar kommentar                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | Anställnings                              | Används som matchande attribut                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[Inte mappad \- används som käll ankare\] | Under den inledande synkroniseringen länkar etablerings tjänsten personUuid till befintlig objectGuid\.  |
| 3  | PerPersonal                            | displayName              | displayName                             | Ej tillämpligt                                                                                           |
| 4  | PerPersonal                            | firstName                | förnamn                               | Ej tillämpligt                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | Ej tillämpligt                                                                                           |
| 6  | Användare                                   | addressLine1             | streetAddress                           | Ej tillämpligt                                                                                           |
| 7  | Användare                                   | city                     | l                                       | Ej tillämpligt                                                                                           |
| 8  | Användare                                   | land                  | co                                      | Ej tillämpligt                                                                                           |
| 9  | Användare                                   | state                    | st                                      | Ej tillämpligt                                                                                           |
| 10 | Användare                                   | användarnamn                 | samAccountName                          | Ej tillämpligt                                                                                           |
| 11 | Användare                                   | Postummer                  | Post nummer                              | Ej tillämpligt                                                                                           |
| 12 | PerEmail                               | emailAddress             | e-post                                    | Ej tillämpligt                                                                                           |
| 13 | EmpJob                                 | Befattning                 | title                                   | Ej tillämpligt                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | Ej tillämpligt                                                                                           |
| 15 | EmpJob \. Company \. CountryOfRegistration | twoCharCountryCode       | c                                       | Ej tillämpligt                                                                                           |
| 16 | EmpJob- \. avdelning                     | avdelning               | avdelning                              | Ej tillämpligt                                                                                           |
| 17 | EmpJob- \. avdelning                       | vändning                 | company                                 | Ej tillämpligt                                                                                           |
| 18 | EmpJob- \. plats                       | officeLocationAddress    | streetAddress                           | Ej tillämpligt                                                                                           |
| 19 | EmpJob- \. plats                       | officeLocationZipCode    | Post nummer                              | Ej tillämpligt                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | om activeEmploymentsCount = 0 inaktiverar du account\.                                           |
