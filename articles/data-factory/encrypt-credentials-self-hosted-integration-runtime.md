---
title: Kryptera dina autentiseringsuppgifter i Azure Data Factory | Microsoft Docs
description: Lär dig mer om att kryptera och lagra autentiseringsuppgifter för dina lokala datakällor på en dator med automatisk värdbaserade integration runtime.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: b577c276627c3a187215cd0da551428fbb32791f
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050914"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Kryptera dina autentiseringsuppgifter för lokalt datalager i Azure Data Factory
Du kan kryptera och lagra autentiseringsuppgifter för dina lokala datalager (länkade tjänster med känslig information) på en dator med automatisk värdbaserade integration runtime. 

Du skickar en JSON-definitionsfil med autentiseringsuppgifter för att den <br/>[**Nya AzureRmDataFactoryV2LinkedServiceEncryptedCredential** ](https://docs.microsoft.com/powershell/module/azurerm.datafactoryv2/New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential?view=azurermps-4.4.0) för att skapa en JSON-definitionen utdatafil med krypterade autentiseringsuppgifter. Använd sedan den uppdaterade JSON-definitionen för att skapa länkade tjänster.

## <a name="author-sql-server-linked-service"></a>Skapad SQL Server som är länkad tjänst
Skapa en JSON-fil med namnet **SqlServerLinkedService.json** i en mapp med följande innehåll:  

Ersätt `<servername>`, `<databasename>`, `<username>`, och `<password>` med värden för SQL Server innan du sparar filen. Och Ersätt `<integration runtime name>` med namnet på din integrering runtime. 

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
För att kryptera känsliga data från JSON-nyttolast på en lokal själva värdbaserade integration körning, kör **ny AzureRmDataFactoryV2LinkedServiceEncryptedCredential**, och vidarebefordra JSON-nyttolast. Denna cmdlet garanterar autentiseringsuppgifterna krypteras med hjälp av DPAPI och lagras i själva värdbaserade integration runtime noden lokalt. Nyttolasten i utdata kan omdirigeras till en annan JSON-fil (i det här fallet 'encryptedLinkedService.json') som innehåller krypterade autentiseringsuppgifter.

```powershell
New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Använd JSON med krypterade autentiseringsuppgifter
Nu kan använda JSON-filen för utdata från det föregående kommandot som innehåller de kryptera autentiseringsuppgifterna för att ställa in den **SqlServerLinkedService**.

```powershell
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Nästa steg
Information om säkerhet för dataflyttning finns [Data movement säkerhetsaspekter](data-movement-security-considerations.md).

