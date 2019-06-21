---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 53ccd439429dc7df311e086d79f7234e26fd8b0e
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302330"
---
> [!IMPORTANT]
> Din nyckel för lagringskontot liknar rotlösenordet för lagringskontot. Var alltid noga med att skydda din kontonyckel. Undvik att dela ut den till andra användare, hårdkoda den eller spara den var som helst i en oformaterad textfil som andra har åtkomst till. Återskapa din kontonyckel med hjälp av Azure Portal om du misstänker att den komprometterats.
>
> SAS (Shared Access Signature)-token är viktiga för att skydda precis som åtkomstnycklarna för kontot. När du tillhandahåller granularitet SAS ger klienter åtkomst till resurserna i ditt storage-konto och inte ska delas offentligt. När delning krävs för att felsöka orsaker Använd en redigerade version av alla loggfiler eller tar bort SAS-token (om sådan finns) i loggfilerna och kontrollera att skärmbilderna inte innehåller SAS-information antingen.
