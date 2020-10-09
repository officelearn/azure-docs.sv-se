---
title: Konfigurera Kundhanterade nycklar för Azure API för FHIR
description: Ta med din egen nyckel funktion som stöds i Azure API för FHIR via Cosmos DB
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: matjazl
ms.openlocfilehash: 535bb5d21beafaabb50769a6c03478dbd1f942d4
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2020
ms.locfileid: "91844316"
---
# <a name="configure-customer-managed-keys"></a>Konfigurera kundhanterade nycklar

När du skapar ett nytt Azure API för FHIR-konto krypteras dina data med hjälp av Microsoft-hanterade nycklar som standard. Nu kan du lägga till ett sekundärt krypterings lager för data med hjälp av din egen nyckel som du väljer och hanterar själv.

I Azure utförs detta vanligt vis med hjälp av en krypterings nyckel i kundens Azure Key Vault (AKV). Azure SQL, Azure Storage och Cosmos DB är några exempel som tillhandahåller den här funktionen idag. Azure API för FHIR utnyttjar detta stöd från Cosmos DB. När du skapar ett konto kan du välja att ange en AKV-nyckel-URI. Vi kommer att skicka den här nyckeln till Cosmos DB när DB-kontot har tillhandahållits. När en FHIR-begäran görs hämtar Cosmos DB nyckeln och använder den för att kryptera/dekryptera data. För att komma igång kan du referera till följande länkar:

- [Registrera Azure Cosmos DB Resource Provider för din Azure-prenumeration](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#register-resource-provider) 
- [Konfigurera din AKV-instans](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#configure-your-azure-key-vault-instance)
-  [Lägg till en åtkomst princip till din AKV-instans](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#add-an-access-policy-to-your-azure-key-vault-instance)
- [Generera en nyckel i AKV](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#generate-a-key-in-azure-key-vault)

När du har skapat ditt Azure API för FHIR-konto på Azure Portal kan du se konfigurations alternativet "data kryptering" under "databas inställningar" på fliken "ytterligare inställningar". Som standard väljs alternativet tjänst hanterad nyckel. Du kan ange din AKV-nyckel här genom att välja alternativet "kundhanterad nyckel". Du kan ange den kopierade nyckel-URI: n här.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Skapa Azure API för FHIR":::

Eller så kan du välja din nyckel från nyckel väljaren:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Skapa Azure API för FHIR" enligt nedan. Konfigurations alternativet kan inte ändras när det har valts. Du kan dock ändra och uppdatera din nyckel.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Skapa Azure API för FHIR":::

Dessutom kan du skapa en ny version av den angivna nyckeln, efter vilken dina data kommer att krypteras med den nya versionen utan avbrott i tjänsten. Du kan också ta bort åtkomsten till nyckeln för att ta bort åtkomsten till data.
