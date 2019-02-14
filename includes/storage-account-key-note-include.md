---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3cfdca99c91dc54a711801d92aa0da91fb9703e4
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56247189"
---
> [!IMPORTANT]
> Din nyckel för lagringskontot liknar rotlösenordet för lagringskontot. Var alltid noga med att skydda din kontonyckel. Undvik att dela ut den till andra användare, hårdkoda den eller spara den var som helst i en oformaterad textfil som andra har åtkomst till. Återskapa din kontonyckel med hjälp av Azure Portal om du misstänker att den komprometterats.
> 
> SAS (Shared Access Signature)-token är viktiga för att skydda precis som åtkomstnycklarna för kontot. När du tillhandahåller granularitet SAS ger klienter åtkomst till resurserna i ditt storage-konto och inte ska delas offentligt. När delning krävs för att felsöka orsaker Använd en redigerade version av alla loggfiler eller tar bort SAS-token (om sådan finns) i loggfilerna och kontrollera att skärmbilderna inte innehåller SAS-information antingen.
> 
> Microsoft rekommenderar att du använder Azure Active Directory (Azure AD)-autentisering för din Blob- och Queue storage-program (förhandsversion) när det är möjligt för ökad säkerhet. Mer information finns i [autentisera åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory (förhandsversion)](https://docs.microsoft.com/azure/storage/common/storage-auth-aad).
