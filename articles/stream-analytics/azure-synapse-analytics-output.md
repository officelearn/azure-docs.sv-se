---
title: Azure Synapse Analytics-utdata från Azure Stream Analytics
description: I den här artikeln beskrivs Azure Synapse Analytics som utdata för Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 0b3bec9c4d4476b95279e35953ff89177f4488d4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305839"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Azure Synapse Analytics-utdata från Azure Stream Analytics

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) (tidigare SQL Data Warehouse) är en obegränsad analys tjänst som sammanför företags data lager hantering och stor data analys. 

Azure Stream Analytics jobb kan matas ut till en dedikerad SQL-adresspool i Azure Synapse Analytics och kan bearbeta data flödes hastigheter upp till 200 MB/s. Detta stöder de mest krävande real tids analys och frekventa data bearbetnings behov för arbets belastningar som rapportering och instrument paneler.  

Tabellen för den dedikerade SQL-poolen måste finnas innan du kan lägga till den som utdata till ditt Stream Analytics-jobb. Tabellens schema måste matcha fälten och deras typer i jobbets utdata. 

Om du vill använda Azure-Synapse som utdata måste du kontrol lera att lagrings kontot har kon figurer ATS. Gå till lagrings konto inställningarna för att konfigurera lagrings kontot. Endast de lagrings konto typer som stöder tabeller är tillåtna: General-Purpose v2 och General-Purpose v1. Välj endast standard nivå. Premium-nivån stöds inte.

## <a name="output-configuration"></a>Konfiguration av utdata

I följande tabell visas egenskaps namn och beskrivningar för att skapa am Azure Synapse Analytics-utdata.

|Egenskapsnamn|Beskrivning|
|-|-|
|Utdataalias |Ett eget namn som används i frågor för att dirigera frågeresultatet till den här databasen. |
|Databas |ett dedikerat SQL-poolnamn där du skickar dina utdata. |
|Servernamn |Namn på Azure Synapse-Server.  |
|Användarnamn |Det användar namn som har Skriv behörighet till databasen. Stream Analytics stöder endast SQL-autentisering. |
|lösenordsinställning |Lösen ordet för att ansluta till databasen. |
|Tabeller  | Tabell namnet där utdata skrivs. Tabell namnet är Skift läges känsligt. Schemat för den här tabellen ska exakt matcha det antal fält och deras typer som jobbets utdata genererar.|

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med hjälp av Azure CLI](quick-create-azure-cli.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med en ARM-mall](quick-create-azure-resource-manager.md)
* [Snabb start: skapa ett Stream Analytics jobb med Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb i Visual Studio Code](quick-create-visual-studio-code.md)