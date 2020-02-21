---
title: Attribut referens för SAP SuccessFactors | Microsoft Docs
description: Lär dig vilka attribut från SuccessFactors som stöds av SuccessFactors-HR driven-etablering
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 00b16f969525e7b802c008ba247ecba015875689
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522364"
---
# <a name="sap-successfactors-attribute-reference"></a>Referens för SAP SuccessFactors-attribut

## <a name="supported-successfactors-entities-and-attributes"></a>SuccessFactors entiteter och attribut som stöds

I tabellen nedan samlas listan över SuccessFactors-attribut som stöds av följande två etablerings appar: 
* [SuccessFactors för att Active Directory användar etablering](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors till användar etablering i Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

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
| 16 | Användare                                   | ursprungslandet                      | Läs           |
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
| 32 | Användare                                   | Hemtelefon                    | Läs           |
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
| 54 | EmpJob                                 | jobTitle                     | Läs           |
| 55 | EmpJob                                 | position                     | Läs           |
| 65 | EmpJob                                 | customString13               | Läs           |
| 56 | EmpJob                                 | managerId                    | Läs           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | Läs           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Läs           |
| 59 | EmpJob\.Company                        | company                      | Läs           |
| 60 | EmpJob\.Company                        | companyId                    | Läs           |
| 61 | EmpJob\.företag\.CountryOfRegistration | twoCharCountryCode           | Läs           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Läs           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Läs           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Läs           |
| 65 | EmpJob\.avdelning                     | avdelning                   | Läs           |
| 66 | EmpJob\.avdelning                     | departmentId                 | Läs           |
| 67 | EmpJob\.-avdelning                       | vändning                     | Läs           |
| 68 | EmpJob\.-avdelning                       | divisionId                   | Läs           |
| 69 | EmpJob\.JobCode                        | jobCode                      | Läs           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Läs           |
| 71 | EmpJob\.plats                       | LocationName                 | Läs           |
| 72 | EmpJob\.plats                       | officeLocationAddress        | Läs           |
| 73 | EmpJob\.plats                       | officeLocationCity           | Läs           |
| 74 | EmpJob\.plats                       | officeLocationCustomString4  | Läs           |
| 75 | EmpJob\.plats                       | officeLocationZipCode        | Läs           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Läs           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Läs           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Läs           |


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
| 8  | Användare                                   | ursprungslandet                  | CO                                      | Ej tillämpligt                                                                                           |
| 9  | Användare                                   | state                    | St                                      | Ej tillämpligt                                                                                           |
| 10 | Användare                                   | användarnamn                 | Sam                          | Ej tillämpligt                                                                                           |
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

