---
title: ATTRIBUTreferens för SAP SuccessFactors | Microsoft-dokument
description: Lär dig vilka attribut från SuccessFactors som stöds av SuccessFactors-HR-driven etablering
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522364"
---
# <a name="sap-successfactors-attribute-reference"></a>ATTRIBUTreferens för SAP SuccessFactors

## <a name="supported-successfactors-entities-and-attributes"></a>Framgångsfaktorer med stöd för entiteter och attribut

Tabellen nedan visar listan över SuccessFactors-attribut som stöds av följande två etableringsappar: 
* [SuccessFactors till Active Directory-användaretablering](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors till Azure AD-användareetablering](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | Entiteten SuccessFactors                  | Attributet SuccessFactors     | Åtgärdstyp |
|----|----------------------------------------|------------------------------|----------------|
| 1  | Perperson                              | personIdExternal             | Läsa           |
| 2  | Perperson                              | personId (personId)                     | Läsa           |
| 3  | Perperson                              | perPersonUuid                | Läsa           |
| 4  | Per Personligt                            | displayName                  | Läsa           |
| 5  | Per Personligt                            | firstName                    | Läsa           |
| 6  | Per Personligt                            | kön                       | Läsa           |
| 7  | Per Personligt                            | lastName                     | Läsa           |
| 8  | Per Personligt                            | middleName (mittNamn)                   | Läsa           |
| 9  | Per Personligt                            | preferredName (preferredName)                | Läsa           |
| 10 | Användare                                   | adressLine1                 | Läsa           |
| 11 | Användare                                   | adressLine2                 | Läsa           |
| 12 | Användare                                   | adressLIne3                 | Läsa           |
| 13 | Användare                                   | företagTelefon                | Läsa           |
| 14 | Användare                                   | Mobiltelefon                    | Läsa           |
| 15 | Användare                                   | city                         | Läsa           |
| 16 | Användare                                   | land                      | Läsa           |
| 17 | Användare                                   | custom01                     | Läsa           |
| 18 | Användare                                   | custom02                     | Läsa           |
| 19 | Användare                                   | custom03                     | Läsa           |
| 20 | Användare                                   | custom04                     | Läsa           |
| 21 | Användare                                   | custom05 (custom05)                     | Läsa           |
| 22 | Användare                                   | custom06                     | Läsa           |
| 23 | Användare                                   | custom07                     | Läsa           |
| 24 | Användare                                   | custom08                     | Läsa           |
| 25 | Användare                                   | anpassade09                     | Läsa           |
| 26 | Användare                                   | anpassade10                     | Läsa           |
| 27 | Användare                                   | anpassad11                     | Läsa           |
| 28 | Användare                                   | anpassade12                     | Läsa           |
| 29 | Användare                                   | anpassade13                     | Läsa           |
| 30 | Användare                                   | anpassade14                     | Läsa           |
| 31 | Användare                                   | empId (på annat sätt)                        | Läsa           |
| 32 | Användare                                   | homePhone (hemTelefon)                    | Läsa           |
| 33 | Användare                                   | jobbFamilj                    | Läsa           |
| 34 | Användare                                   | Smeknamn                     | Läsa           |
| 35 | Användare                                   | state                        | Läsa           |
| 36 | Användare                                   | Tidszon                     | Läsa           |
| 37 | Användare                                   | användarnamn                     | Läsa           |
| 38 | Användare                                   | Postnummer                      | Läsa           |
| 39 | Per Telefon                               | areaCode (areaCode)                     | Läsa           |
| 40 | Per Telefon                               | countryCode (landsKod)                  | Läsa           |
| 41 | Per Telefon                               | Förlängning                    | Läsa           |
| 42 | Per Telefon                               | phoneNumber                  | Läsa           |
| 43 | Per Telefon                               | phoneType (telefontyp)                    | Läsa           |
| 44 | PerEmail (peremail)                               | Emailaddress                 | Läs, skriv    |
| 45 | PerEmail (peremail)                               | e-posttyp                    | Läsa           |
| 46 | EmpEmployment (EmpEmployment)                          | firstDateWorked              | Läsa           |
| 47 | EmpEmployment (EmpEmployment)                          | lastDateWorked               | Läsa           |
| 48 | EmpEmployment (EmpEmployment)                          | userId                       | Läsa           |
| 49 | EmpEmployment (EmpEmployment)                          | ärKontingentWorker           | Läsa           |
| 50 | EmpJob (0)                                 | landOfCompany             | Läsa           |
| 51 | EmpJob (0)                                 | emplStatus (emplStatus)                   | Läsa           |
| 52 | EmpJob (0)                                 | endDate (endDate)                      | Läsa           |
| 53 | EmpJob (0)                                 | Startdate                    | Läsa           |
| 54 | EmpJob (0)                                 | jobbTitle                     | Läsa           |
| 55 | EmpJob (0)                                 | position                     | Läsa           |
| 65 | EmpJob (0)                                 | customString13               | Läsa           |
| 56 | EmpJob (0)                                 | chefId                    | Läsa           |
| 57 | EmpJob\.Affärsunit                   | företagUnit                 | Läsa           |
| 58 | EmpJob\.Affärsunit                   | företagUnitId               | Läsa           |
| 59 | EmpJob\.Företag                        | company                      | Läsa           |
| 60 | EmpJob\.Företag                        | företagId                    | Läsa           |
| 61 | EmpJob\.\.Företag CountryOfRegistration | twoCharCountryCode           | Läsa           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Läsa           |
| 63 | EmpJob\.CostCenter                     | kostnadCenterId                 | Läsa           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Läsa           |
| 65 | EmpJob-avdelningen\.                     | avdelning                   | Läsa           |
| 66 | EmpJob-avdelningen\.                     | departmentId                 | Läsa           |
| 67 | EmpJob-divisionen\.                       | Division                     | Läsa           |
| 68 | EmpJob-divisionen\.                       | divisionId (divisionId)                   | Läsa           |
| 69 | EmpJob\.JobCode                        | jobCode (jobCode)                      | Läsa           |
| 70 | EmpJob\.JobCode                        | jobCodeId (jobCodeId)                    | Läsa           |
| 71 | EmpJob-plats\.                       | Platsnamn                 | Läsa           |
| 72 | EmpJob-plats\.                       | officeLocationAdress        | Läsa           |
| 73 | EmpJob-plats\.                       | officeLocationCity           | Läsa           |
| 74 | EmpJob-plats\.                       | officeLocationCustomString4  | Läsa           |
| 75 | EmpJob-plats\.                       | officeLocationZipCode        | Läsa           |
| 76 | EmpJob\.PayGrade                       | payGrade (lönGrade)                     | Läsa           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Läsa           |
| 78 | EmpEmploymentTermination               | senasteTerminationDate        | Läsa           |


## <a name="default-attribute-mapping"></a>Standardattributmappning

Tabellen nedan innehåller standardattributmappningen mellan SuccessFactors-attribut som anges ovan och AD/Azure AD-attribut. I azure AD-etableringsappen "Mapping" kan du ändra den här standardmappningen så att den innehåller attribut från listan ovan. 

| \# | Entiteten SuccessFactors                  | Attributet SuccessFactors | Standardmappning av AD/Azure AD-attribut   | Bearbeta anmärkning                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | Perperson                              | personIdExternal         | Employeeid                              | Används som matchande attribut                                                                   |
| 2  | Perperson                              | perPersonUuid            | \[Inte mappad \- som används som källankare\] | Under den första synkroniseringen länkar etableringstjänsten personenUuid till befintlig objectGuid\.  |
| 3  | Per Personligt                            | displayName              | displayName                             | Ej tillämpligt                                                                                           |
| 4  | Per Personligt                            | firstName                | förnamn                               | Ej tillämpligt                                                                                           |
| 5  | Per Personligt                            | lastName                 | sn                                      | Ej tillämpligt                                                                                           |
| 6  | Användare                                   | adressLine1             | streetAddress (streetAddress)                           | Ej tillämpligt                                                                                           |
| 7  | Användare                                   | city                     | l                                       | Ej tillämpligt                                                                                           |
| 8  | Användare                                   | land                  | Co                                      | Ej tillämpligt                                                                                           |
| 9  | Användare                                   | state                    | st                                      | Ej tillämpligt                                                                                           |
| 10 | Användare                                   | användarnamn                 | samAccountName                          | Ej tillämpligt                                                                                           |
| 11 | Användare                                   | Postnummer                  | Postnummer                              | Ej tillämpligt                                                                                           |
| 12 | PerEmail (peremail)                               | Emailaddress             | e-post                                    | Ej tillämpligt                                                                                           |
| 13 | EmpJob (0)                                 | jobbTitle                 | title                                   | Ej tillämpligt                                                                                           |
| 14 | EmpJob (0)                                 | chefId                | manager                                 | Ej tillämpligt                                                                                           |
| 15 | EmpJob\.\.Företag CountryOfRegistration | twoCharCountryCode       | c                                       | Ej tillämpligt                                                                                           |
| 16 | EmpJob-avdelningen\.                     | avdelning               | avdelning                              | Ej tillämpligt                                                                                           |
| 17 | EmpJob-divisionen\.                       | Division                 | company                                 | Ej tillämpligt                                                                                           |
| 18 | EmpJob-plats\.                       | officeLocationAdress    | streetAddress (streetAddress)                           | Ej tillämpligt                                                                                           |
| 19 | EmpJob-plats\.                       | officeLocationZipCode    | Postnummer                              | Ej tillämpligt                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | Om activeEmploymentsCount=0 inaktiverar du kontot\.                                           |

