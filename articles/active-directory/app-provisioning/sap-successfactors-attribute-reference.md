---
title: Referens för SAP SuccessFactors-attribut
description: Lär dig vilka attribut från SuccessFactors som stöds av SuccessFactors-HR driven-etablering
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: article
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.reviewer: celested
ms.openlocfilehash: cdd6abf5d10a529aa4c529d0364b2ba9f1df72e3
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593192"
---
# <a name="sap-successfactors-attribute-reference"></a>Referens för SAP SuccessFactors-attribut

I den här artikeln hittar du information om:

- [SuccessFactors entiteter och attribut som stöds](#supported-successfactors-entities-and-attributes)
- [Mappning av standardattribut](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>SuccessFactors entiteter och attribut som stöds

I tabellen nedan samlas listan över SuccessFactors-attribut som stöds av följande två etablerings appar:

- [SuccessFactors för att Active Directory användar etablering](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [SuccessFactors till användar etablering i Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)


| \# | SuccessFactors-entitet                  | SuccessFactors-attribut     | Åtgärdstyp |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Läsa           |
| 2  | PerPerson                              | personId                     | Läsa           |
| 3  | PerPerson                              | perPersonUuid                | Läsa           |
| 4  | PerPersonal                            | displayName                  | Läsa           |
| 5  | PerPersonal                            | firstName                    | Läsa           |
| 6  | PerPersonal                            | kön                       | Läsa           |
| 7  | PerPersonal                            | lastName                     | Läsa           |
| 8  | PerPersonal                            | middleName                   | Läsa           |
| 9  | PerPersonal                            | preferredName                | Läsa           |
| 10 | Användare                                   | addressLine1                 | Läsa           |
| 11 | Användare                                   | addressLine2                 | Läsa           |
| 12 | Användare                                   | addressLIne3                 | Läsa           |
| 13 | Användare                                   | businessPhone                | Läsa           |
| 14 | Användare                                   | cellPhone                    | Läsa           |
| 15 | Användare                                   | city                         | Läsa           |
| 16 | Användare                                   | land                      | Läsa           |
| 17 | Användare                                   | custom01                     | Läsa           |
| 18 | Användare                                   | custom02                     | Läsa           |
| 19 | Användare                                   | custom03                     | Läsa           |
| 20 | Användare                                   | custom04                     | Läsa           |
| 21 | Användare                                   | custom05                     | Läsa           |
| 22 | Användare                                   | custom06                     | Läsa           |
| 23 | Användare                                   | custom07                     | Läsa           |
| 24 | Användare                                   | custom08                     | Läsa           |
| 25 | Användare                                   | custom09                     | Läsa           |
| 26 | Användare                                   | anpassad10                     | Läsa           |
| 27 | Användare                                   | custom11                     | Läsa           |
| 28 | Användare                                   | custom12                     | Läsa           |
| 29 | Användare                                   | custom13                     | Läsa           |
| 30 | Användare                                   | custom14                     | Läsa           |
| 31 | Användare                                   | empId                        | Läsa           |
| 32 | Användare                                   | homePhone                    | Läsa           |
| 33 | Användare                                   | jobFamily                    | Läsa           |
| 34 | Användare                                   | smek namn                     | Läsa           |
| 35 | Användare                                   | state                        | Läsa           |
| 36 | Användare                                   | Tidszon                     | Läsa           |
| 37 | Användare                                   | användarnamn                     | Läsa           |
| 38 | Användare                                   | Postummer                      | Läsa           |
| 39 | PerPhone                               | areaCode                     | Läsa           |
| 40 | PerPhone                               | countryCode                  | Läsa           |
| 41 | PerPhone                               | utöka                    | Läsa           |
| 42 | PerPhone                               | phoneNumber                  | Läsa           |
| 43 | PerPhone                               | phoneType                    | Läsa           |
| 44 | PerEmail                               | emailAddress                 | Läsa, skriva    |
| 45 | PerEmail                               | emailType                    | Läsa           |
| 46 | EmpEmployment                          | firstDateWorked              | Läsa           |
| 47 | EmpEmployment                          | lastDateWorked               | Läsa           |
| 48 | EmpEmployment                          | userId                       | Läsa           |
| 49 | EmpEmployment                          | isContingentWorker           | Läsa           |
| 50 | EmpJob                                 | countryOfCompany             | Läsa           |
| 51 | EmpJob                                 | emplStatus                   | Läsa           |
| 52 | EmpJob                                 | endDate                      | Läsa           |
| 53 | EmpJob                                 | /SD                    | Läsa           |
| 54 | EmpJob                                 | Befattning                     | Läsa           |
| 55 | EmpJob                                 | position                     | Läsa           |
| 65 | EmpJob                                 | customString13               | Läsa           |
| 56 | EmpJob                                 | managerId                    | Läsa           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | Läsa           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Läsa           |
| 59 | EmpJob\.-företag                        | company                      | Läsa           |
| 60 | EmpJob\.-företag                        | companyId                    | Läsa           |
| 61 | EmpJob\.Company\.CountryOfRegistration | twoCharCountryCode           | Läsa           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Läsa           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Läsa           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Läsa           |
| 65 | EmpJob\.-avdelning                     | avdelning                   | Läsa           |
| 66 | EmpJob\.-avdelning                     | departmentId                 | Läsa           |
| 67 | EmpJob\.-avdelning                       | vändning                     | Läsa           |
| 68 | EmpJob\.-avdelning                       | divisionId                   | Läsa           |
| 69 | EmpJob\.JobCode                        | jobCode                      | Läsa           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Läsa           |
| 71 | EmpJob\.-plats                       | LocationName                 | Läsa           |
| 72 | EmpJob\.-plats                       | officeLocationAddress        | Läsa           |
| 73 | EmpJob\.-plats                       | officeLocationCity           | Läsa           |
| 74 | EmpJob\.-plats                       | officeLocationCustomString4  | Läsa           |
| 75 | EmpJob\.-plats                       | officeLocationZipCode        | Läsa           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Läsa           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Läsa           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Läsa           |

## <a name="default-attribute-mapping"></a>Mappning av standardattribut

Tabellen nedan innehåller standardattributets mappning mellan SuccessFactors-attribut som anges ovan och AD/Azure AD-attribut. I bladet för Azure AD Provisioning-appen kan du ändra standard mappningen för att inkludera attribut från listan ovan. 

| \# | SuccessFactors-entitet                  | SuccessFactors-attribut | AD/Azure AD-attributmappning som är standard   | Bearbetar kommentar                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | Anställnings                              | Används som matchande attribut                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[Inte mappad \- används som käll ankare\] | Under den inledande synkroniseringen länkar etablerings tjänsten personUuid till befintlig objectGuid\.  |
| 3  | PerPersonal                            | displayName              | displayName                             | NA                                                                                           |
| 4  | PerPersonal                            | firstName                | förnamn                               | NA                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | NA                                                                                           |
| 6  | Användare                                   | addressLine1             | streetAddress                           | NA                                                                                           |
| 7  | Användare                                   | city                     | l                                       | NA                                                                                           |
| 8  | Användare                                   | land                  | företag                                      | NA                                                                                           |
| 9  | Användare                                   | state                    | st                                      | NA                                                                                           |
| 10 | Användare                                   | användarnamn                 | samAccountName                          | NA                                                                                           |
| 11 | Användare                                   | Postummer                  | Post nummer                              | NA                                                                                           |
| 12 | PerEmail                               | emailAddress             | e-post                                    | NA                                                                                           |
| 13 | EmpJob                                 | Befattning                 | title                                   | NA                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | NA                                                                                           |
| 15 | EmpJob\.Company\.CountryOfRegistration | twoCharCountryCode       | c                                       | NA                                                                                           |
| 16 | EmpJob\.-avdelning                     | avdelning               | avdelning                              | NA                                                                                           |
| 17 | EmpJob\.-avdelning                       | vändning                 | company                                 | NA                                                                                           |
| 18 | EmpJob\.-plats                       | officeLocationAddress    | streetAddress                           | NA                                                                                           |
| 19 | EmpJob\.-plats                       | officeLocationZipCode    | Post nummer                              | NA                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | om activeEmploymentsCount = 0 inaktiverar du account\.                                           |
