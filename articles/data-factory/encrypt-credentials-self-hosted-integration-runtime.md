---
title: Kryptera autentiseringsuppgifter i Azure Data Factory
description: Lär dig hur du krypterar och lagrar autentiseringsuppgifter för dina lokala datalager på en dator med självvärdförd integreringskörning.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 67ba2fadd5376997b528af4fcd2c5a666bb134a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443989"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Kryptera autentiseringsuppgifter för lokala datalager i Azure Data Factory
Du kan kryptera och lagra autentiseringsuppgifter för dina lokala datalager (länkade tjänster med känslig information) på en dator med självvärdbaserad integrationskörning. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du skickar en JSON-definitionsfil med referenser till <br/>[**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) cmdlet att producera en utgång JSON definition fil med krypterade autentiseringsuppgifter. Använd sedan den uppdaterade JSON-definitionen för att skapa de länkade tjänsterna.

## <a name="author-sql-server-linked-service"></a>Länkad tjänst för författare till SQL Server
Skapa en JSON-fil med namnet **SqlServerLinkedService.json** i alla mappar med följande innehåll:  

Ersätt `<servername>` `<databasename>`, `<username>`, `<password>` och med värden för SQL Server innan du sparar filen. Och ersätt `<integration runtime name>` med namnet på din integrationskörning. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
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
Om du vill kryptera känsliga data från JSON-nyttolasten på en lokal självvärderad integrationskörning kör du **New-AzDataFactoryV2LinkedServiceEncryptedCredential**och vidarebefordrar JSON-nyttolasten. Den här cmdleten säkerställer att autentiseringsuppgifterna krypteras med DPAPI och lagras på den självvärderade integrationsnoden lokalt. Utdatanyttolasten som innehåller den krypterade referensen till autentiseringsuppgifterna kan omdirigeras till en annan JSON-fil (i det här fallet "encryptedLinkedService.json").

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Använda JSON med krypterade autentiseringsuppgifter
Använd nu den utdata-JSON-filen från föregående kommando som innehåller den krypterade autentiseringsfilen för att konfigurera **SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Nästa steg
Information om säkerhetsaspekter för dataflyttning finns i [Säkerhetsaspekter för data.](data-movement-security-considerations.md)

