---
title: Använd Robomongo för Azure Cosmos DB | Microsoft Docs
description: 'Lär dig hur du använder Robomongo med en Azure-Cosmos-DB: API för MongoDB-konto'
keywords: robomongo
services: cosmos-db
author: AndrewHoh
manager: kfile
documentationcenter: ''
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: anhoh
ms.openlocfilehash: 7d318880b7b0078e4c03acb66885f4aed5534ba1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
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
