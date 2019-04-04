---
title: Kryptera autentiseringsuppgifter i Azure Data Factory | Microsoft Docs
description: Lär dig mer om att kryptera och lagra autentiseringsuppgifter för dina lokala datalager på en dator med lokal integration runtime.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 8e705a4430f6ccee847dc7d41ef80456a6dc4ea5
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58903802"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Kryptera autentiseringsuppgifterna för den lokala datalager i Azure Data Factory
Du kan kryptera och lagra autentiseringsuppgifter för dina lokala datalager (länkade tjänster med känslig information) på en dator med lokal integration runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du skickar en JSON-definition-fil med autentiseringsuppgifter för att den <br/>[**Ny AzDataFactoryV2LinkedServiceEncryptedCredential** ](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) cmdlet för att skapa en JSON-definition utdatafil med de krypterade autentiseringsuppgifterna. Sedan använder du uppdaterade JSON-definition för att skapa länkade tjänster.

## <a name="author-sql-server-linked-service"></a>Skapa länkad SQL Server-tjänsten
Skapa en JSON-fil med namnet **SqlServerLinkedService.json** i valfri mapp med följande innehåll:  

Ersätt `<servername>`, `<databasename>`, `<username>`, och `<password>` med värden för SQL-servern innan du sparar filen. Och Ersätt `<integration runtime name>` med namnet på din integration runtime. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
            }
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Kryptera autentiseringsuppgifter
För att kryptera känsliga data från JSON-nyttolasten på en lokal lokal integration runtime kan köra **New-AzDataFactoryV2LinkedServiceEncryptedCredential**, och skicka vidare JSON-nyttolast. Denna cmdlet säkerställer att autentiseringsuppgifterna krypteras med hjälp av DPAPI och lagras på lokal integration runtime-noden lokalt. Nyttolasten i utdata som innehåller krypterade referensen till autentiseringsuppgifter kan omdirigeras till en annan JSON-fil (i det här fallet ”encryptedLinkedService.json”).

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Använda JSON med krypterade autentiseringsuppgifter
Nu kan använda JSON-filen för utdata från föregående kommando som innehåller de krypterade autentiseringsuppgifterna för att ställa in den **SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Nästa steg
Information om säkerhetsrelaterade aspekter för dataförflyttning finns [säkerhetsöverväganden vid dataflytt](data-movement-security-considerations.md).

