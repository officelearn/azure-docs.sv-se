---
title: "Diagnostik- och återställning för Azure Import/Export jobb | Microsoft Docs"
description: "Lär dig hur du aktiverar utförlig loggning för Microsoft Azure Import/Export service jobb."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 096cc795-9af6-4335-9fe8-fffa9f239a17
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 0068aae9d6780aa41a070db0eb191d0d5a165d21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="diagnostics-and-error-recovery-for-azure-importexport-jobs"></a>Diagnostik- och återställning för Azure Import/Export-jobb
Tjänsten Azure Import/Export skapar en fellogg i det associerade lagringskontot för varje enhet som bearbetas. Du kan också aktivera utförlig loggning genom att ange den `LogLevel` egenskapen `Verbose` när du anropar den [placera jobbet](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) eller [uppdatering jobbegenskaper](/rest/api/storageimportexport/jobs#Jobs_Update) operations.

 Som standard loggarna skrivs till en behållare med namnet `waimportexport`. Du kan ange ett annat namn genom att ange den `DiagnosticsPath` egenskapen när du anropar den `Put Job` eller `Update Job Properties` åtgärder. Loggfilerna lagras som blockblobar med följande namngivningskonvention: `waies/jobname_driveid_timestamp_logtype.xml`.

 Du kan hämta URI av loggar för ett jobb genom att anropa den [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) igen. URI för den detaljerade loggen returneras den `VerboseLogUri` egenskap för varje enhet, medan URI för felloggen returneras den `ErrorLogUri` egenskapen.

Du kan använda loggningsdata för att identifiera följande problem.

## <a name="drive-errors"></a>Fel för enheten

Följande objekt är klassificerade som enheten fel:

-   Fel i åtkomst till eller läsa manifestfilen

-   Felaktig BitLocker-nycklar

-   Enheten läsning och skrivning fel

## <a name="blob-errors"></a>BLOB-fel

Följande objekt är klassificerade som blob-fel:

-   Felaktig eller motstridig blob eller namn

-   Filer som saknas

-   Det gick inte att hitta BLOB

-   Trunkerat filer (filerna på disken är mindre än vad som angetts i manifestet)

-   Skadad fil innehåll (för importjobb som identifieras med en felaktig matchning av MD5 kontrollsumma)

-   Skadat blob-metadata och egenskapen-filer som (identifieras med en felaktig matchning av MD5 kontrollsumma)

-   Felaktigt schema för blob-egenskaper och/eller metadatafiler

Det kan finnas fall där vissa delar av en import- eller jobbet inte kan slutföras medan övergripande jobbet slutförs ändå. I det här fallet kan du antingen ladda upp eller hämta saknade delar av data över nätverket och du kan skapa ett nytt jobb för att överföra data. Finns det [referens för Azure Import/Export verktyg](storage-import-export-tool-how-to-v1.md) information om hur du reparera data över nätverket.

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av tjänsten Import/Export REST API](storage-import-export-using-the-rest-api.md)
