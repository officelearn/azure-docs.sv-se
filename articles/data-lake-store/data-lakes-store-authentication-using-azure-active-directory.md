---
title: Autentisering i Azure Data Lake Storage Gen1 med Azure Active Directory | Microsoft-dokument
description: Lär dig hur du autentiserar med Azure Data Lake Storage Gen1 med Azure Active Directory
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: f83cf183bee930dd07c707b0eb49125cecd70b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60193602"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Autentisering med Azure Data Lake Storage Gen1 med Azure Active Directory

Azure Data Lake Storage Gen1 använder Azure Active Directory för autentisering. Innan du redigerar ett program som fungerar med Data Lake Storage Gen1 måste du bestämma hur du autentiserar ditt program med Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Autentiseringsalternativ

* **Autentisering av slutanvändare** – En slutanvändares Azure-autentiseringsuppgifter används för att autentisera med Data Lake Storage Gen1. Programmet som du skapar för att arbeta med Data Lake Storage Gen1 frågar efter dessa användarautentiseringsuppgifter. Därför är den här autentiseringsmekanismen *interaktiv* och programmet körs i den inloggade användarens kontext. Mer information och instruktioner finns i [Slutanvändarens autentisering för Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Autentisering från service till tjänst** – Använd det här alternativet om du vill att ett program ska autentisera sig själv med Data Lake Storage Gen1. I sådana fall skapar du ett AD-program (Azure Active Directory) och använder nyckeln från Azure AD-programmet för att autentisera med Data Lake Storage Gen1. Därför är den här autentiseringsmekanismen *icke-interaktiv*. Mer information och instruktioner finns i Autentisering från [Service-to-service för Gen1 för datasjölagring](data-lake-store-service-to-service-authenticate-using-active-directory.md).

Följande tabell visar hur autentiseringsmekanismer för slutanvändare och service till tjänst stöds för Data Lake Storage Gen1. Så här läser du tabellen.

* Symbolen ✔* anger att autentiseringsalternativ stöds och länkar till en artikel som visar hur du använder autentiseringsalternativet. 
* Symbolen ✔ anger att autentiseringsalternativet stöds. 
* De tomma cellerna anger att autentiseringsalternativet inte stöds.


|Använd det här autentiseringsalternativet med...                   |.NET         |Java     |PowerShell |Azure CLI | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Slutanvändare (utan MFA**)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(föråldrad)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Slutanvändare (med MFA)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Service-till-tjänst (med klientnyckel)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Service-till-tjänst (med klientcertifikat) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Klicka på <b>✔\* </b> symbolen. Det är en länk.</i><br>
<i>** MFA står för multifaktorautentisering</i>

Mer information om hur du använder Azure Active Directory för autentisering finns i [Autentiseringsscenarier för Azure Active Directory.](../active-directory/develop/authentication-scenarios.md)

## <a name="next-steps"></a>Nästa steg

* [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Tjänst-till-tjänst-autentisering](data-lake-store-service-to-service-authenticate-using-active-directory.md)


