---
title: Använd Robomongo för Azure Cosmos DB | Microsoft Docs
description: 'Lär dig hur du använder Robomongo med en Azure-Cosmos-DB: API för MongoDB-konto'
keywords: robomongo
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: sngun
ms.openlocfilehash: b6d64d7d7b30d4175fb8c8bf6c98127465427d4e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795041"
---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Använda Robomongo med en Azure-Cosmos-DB: API för MongoDB-konto
Att ansluta till en Azure-Cosmos-DB: API för MongoDB-konto med hjälp av Robomongo, måste du:

* Hämta och installera [Robomongo](https://robomongo.org/)
* Har Azure Cosmos-DB: API för MongoDB konto [anslutningssträngen](connect-mongodb-account.md) information

## <a name="connect-using-robomongo"></a>Ansluta med Robomongo
Att lägga till Azure Cosmos-DB: API MongoDB-kontot Robomongo MongoDB anslutningar, utför följande steg.

1. Hämta Azure Cosmos-DB: API: et för anslutningsinformationen för MongoDB-konto med hjälp av anvisningarna [här](connect-mongodb-account.md).

    ![Skärmbild av bladet anslutning sträng](./media/mongodb-robomongo/connectionstringblade.png)
2. Kör *Robomongo.exe*

3. Klicka på anslutningsknappen under **filen** att hantera dina anslutningar. Klicka på **skapa** i den **MongoDB anslutningar** fönster som öppnas i **anslutningsinställningar** fönster.

4. I den **anslutningsinställningar** fönster, Välj ett namn. Leta sedan upp den **värden** och **Port** från informationen i steg 1 och ange dem till **adress** och **Port**respektive.

    ![Skärmbild av Robomongo hantera anslutningar](./media/mongodb-robomongo/manageconnections.png)
5. På den **autentisering** klickar du på **utför autentisering**. Ange sedan databasen (standard är *Admin*), **användarnamn** och **lösenord**.
Båda **användarnamn** och **lösenord** finns i informationen i steg 1.

    ![Skärmbild av fliken Robomongo autentisering](./media/mongodb-robomongo/authentication.png)
6. På den **SSL** markerar **Använd SSL-protokollet**, ändrar den **autentiseringsmetod** till **ett självsignerat certifikat**.

    ![Skärmbild av fliken Robomongo SSL](./media/mongodb-robomongo/SSL.png)
7. Klicka slutligen på **Test** verifiera att du kan ansluta sedan **spara**.

## <a name="next-steps"></a>Nästa steg
* Utforska Azure Cosmos DB: API för MongoDB [exempel](mongodb-samples.md).
