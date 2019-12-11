---
title: Attribut referens för SAP SuccessFactors | Microsoft Docs
description: Lär dig vilka attribut från SuccessFactors som stöds av SuccessFactors-HR driven-etablering
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 6f7497e62be0036c13d5c33fa82301469df16f26
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971119"
---
# <a name="sap-successfactors-attribute-reference"></a>Referens för SAP SuccessFactors-attribut

## <a name="supported-successfactors-entities-and-attributes"></a>SuccessFactors entiteter och attribut som stöds

I tabellen nedan samlas listan över SuccessFactors-attribut som stöds av följande två etablerings appar: 
* [SuccessFactors för att Active Directory användar etablering](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors till användar etablering i Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

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
| 32 | Användare                                   | Hemtelefon                    | Läsa           |
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
| 54 | EmpJob                                 | jobTitle                     | Läsa           |
| 55 | EmpJob                                 | position                     | Läsa           |
| 65 | EmpJob                                 | customString13               | Läsa           |
| 56 | EmpJob                                 | managerId                    | Läsa           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | Läsa           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Läsa           |
| 59 | EmpJob\.Company                        | company                      | Läsa           |
| 60 | EmpJob\.Company                        | companyId                    | Läsa           |
| 61 | EmpJob\.företag\.CountryOfRegistration | twoCharCountryCode           | Läsa           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Läsa           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Läsa           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Läsa           |
| 65 | EmpJob\.avdelning                     | avdelning                   | Läsa           |
| 66 | EmpJob\.avdelning                     | departmentId                 | Läsa           |
| 67 | EmpJob\.-avdelning                       | vändning                     | Läsa           |
| 68 | EmpJob\.-avdelning                       | divisionId                   | Läsa           |
| 69 | EmpJob\.JobCode                        | jobCode                      | Läsa           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Läsa           |
| 71 | EmpJob\.plats                       | LocationName                 | Läsa           |
| 72 | EmpJob\.plats                       | officeLocationAddress        | Läsa           |
| 73 | EmpJob\.plats                       | officeLocationCity           | Läsa           |
| 74 | EmpJob\.plats                       | officeLocationCustomString4  | Läsa           |
| 75 | EmpJob\.plats                       | officeLocationZipCode        | Läsa           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Läsa           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Läsa           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Läsa           |


## <a name="default-attribute-mapping"></a>Mappning av standardattribut

Tabellen nedan innehåller standardattributets mappning mellan SuccessFactors-attribut som anges ovan och AD/Azure AD-attribut. I bladet för Azure AD Provisioning-appen kan du ändra standard mappningen för att inkludera attribut från listan ovan. 

| \# | SuccessFactors-entitet                  | SuccessFactors-attribut | AD/Azure AD-attributmappning som är standard   | Bearbetar kommentar                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | Anställnings                              | Används som matchande attribut                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[som inte har mappats \- används som käll ankare\] | Under den inledande synkroniseringen länkar etablerings tjänsten personUuid till befintlig objectGuid\.  |
| 3  | PerPersonal                            | displayName              | displayName                             | Ej tillämpligt                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | Ej tillämpligt                                                                                           |
| 5  | PerPersonal                            | lastName                 | SN                                      | Ej tillämpligt                                                                                           |
| 6  | Användare                                   | addressLine1             | streetAddress                           | Ej tillämpligt                                                                                           |
| 7  | Användare                                   | city                     | L                                       | Ej tillämpligt                                                                                           |
| 8  | Användare                                   | land                  | CO                                      | Ej tillämpligt                                                                                           |
| 9  | Användare                                   | state                    | St                                      | Ej tillämpligt                                                                                           |
| 10 | Användare                                   | användarnamn                 | samAccountName                          | Ej tillämpligt                                                                                           |
| 11 | Användare                                   | Postummer                  | Postnummer                              | Ej tillämpligt                                                                                           |
| 12 | PerEmail                               | emailAddress             | e-post                                    | Ej tillämpligt                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | title                                   | Ej tillämpligt                                                                                           |
| 14 | EmpJob                                 | managerId                | ansvarig                                 | Ej tillämpligt                                                                                           |
| 15 | EmpJob\.företag\.CountryOfRegistration | twoCharCountryCode       | c                                       | Ej tillämpligt                                                                                           |
| 16 | EmpJob\.avdelning                     | avdelning               | avdelning                              | Ej tillämpligt                                                                                           |
| 17 | EmpJob\.-avdelning                       | vändning                 | company                                 | Ej tillämpligt                                                                                           |
| 18 | EmpJob\.plats                       | officeLocationAddress    | streetAddress                           | Ej tillämpligt                                                                                           |
| 19 | EmpJob\.plats                       | officeLocationZipCode    | Postnummer                              | Ej tillämpligt                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | om activeEmploymentsCount = 0 inaktiverar du account\.                                           |

