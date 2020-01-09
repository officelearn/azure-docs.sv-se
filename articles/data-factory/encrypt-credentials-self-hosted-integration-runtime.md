---
title: Kryptera autentiseringsuppgifter i Azure Data Factory
description: Lär dig hur du krypterar och lagrar autentiseringsuppgifter för dina lokala data lager på en dator med integration runtime med egen värd.
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443989"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Kryptera autentiseringsuppgifter för lokala data lager i Azure Data Factory
Du kan kryptera och lagra autentiseringsuppgifter för dina lokala data lager (länkade tjänster med känslig information) på en dator med integration runtime med egen värd. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du skickar en JSON-definitions fil med autentiseringsuppgifter till <br/>Cmdlet: en [**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) för att skapa en definitions fil för utdata-JSON med de krypterade autentiseringsuppgifterna. Använd sedan den uppdaterade JSON-definitionen för att skapa de länkade tjänsterna.

## <a name="author-sql-server-linked-service"></a>Redigera SQL Server länkad tjänst
Skapa en JSON-fil med namnet **SqlServerLinkedService. JSON** i en mapp med följande innehåll:  

Ersätt `<servername>`, `<databasename>`, `<username>`och `<password>` med värden för din SQL Server innan du sparar filen. Och ersätt `<integration runtime name>` med namnet på din integration Runtime. 

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
Om du vill kryptera känsliga data från JSON-nyttolasten på en lokal lokal integration runtime kör du **New-AzDataFactoryV2LinkedServiceEncryptedCredential**och skickar den till JSON-nyttolasten. Denna cmdlet säkerställer att autentiseringsuppgifterna krypteras med DPAPI och lagras på den lokala integration runtime-noden lokalt. Nytto lasten för utdata som innehåller den krypterade referensen till autentiseringsuppgiften kan omdirigeras till en annan JSON-fil (i det här fallet ' encryptedLinkedService. JSON ').

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Använd JSON med krypterade autentiseringsuppgifter
Använd nu den utgående JSON-filen från föregående kommando som innehåller krypterade autentiseringsuppgifter för att konfigurera **SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Nästa steg
Information om säkerhets överväganden för data förflyttning finns i [säkerhets överväganden för data förflyttning](data-movement-security-considerations.md).

