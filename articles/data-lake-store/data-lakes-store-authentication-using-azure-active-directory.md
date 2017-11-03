---
title: Autentisering i Data Lake Store med Azure Active Directory | Microsoft Docs
description: "Lär dig hur du autentiserar med Data Lake Store med Azure Active Directory"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 6cbdda98c079d25c9cce1342c79dea6e66aec3ad
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2017
---
# <a name="authentication-with-data-lake-store-using-azure-active-directory"></a>Autentisering med Data Lake Store med Azure Active Directory

Azure Data Lake Store använder Azure Active Directory för autentisering. Innan du skapar ett program som fungerar med Azure Data Lake Store kan måste du bestämma hur du autentiserar ditt program med Azure Active Directory (AD Azure). 

## <a name="authentication-options"></a>Autentiseringsalternativ

* **Slutanvändarens autentisering** -autentiseringsuppgifter för en slutanvändare Azure används för att autentisera med Data Lake Store. Programmet som du skapar för att arbeta med Data Lake Store efterfrågar dessa autentiseringsuppgifter. Därför kan den här autentiseringsmekanismerna *interaktiva* och programmet körs i kontexten för den inloggade användaren. Mer information och instruktioner finns i [slutanvändarens autentisering för Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Tjänst-till-tjänst autentisering** – Använd det här alternativet om du vill att ett program för att autentisera sig själv med Data Lake Store. I sådana fall måste du skapa ett program i Azure Active Directory (AD) och använda nyckeln från Azure AD-program för att autentisera med Data Lake Store. Därför kan den här autentiseringsmekanismerna *icke-interaktiv*. Mer information och instruktioner finns i [tjänst-till-tjänst-autentisering för Data Lake Store](data-lake-store-service-to-service-authenticate-using-active-directory.md).

Följande tabell visar hur slutanvändare och -tjänster autentiseringsmekanismer stöds för Data Lake Store. Här är hur du läser tabellen.

* ✔ * symbolen anger att autentiseringsalternativet som stöds och länkar till en artikel som visar hur du använder autentiseringsalternativet. 
* ✔ symbolen anger att autentiseringsalternativet som stöds. 
* Tomma celler anger att autentiseringsalternativet inte stöds.


|Använd det här autentiseringsalternativet med...                   |.NET         |Java     |PowerShell |CLI 2.0 | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Slutanvändare (utan MFA **)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(föråldrad)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Slutanvändare (med MFA)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|(Med nyckel-klienten) tjänst-till-tjänst         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|(Med klientcertifikat) tjänst-till-tjänst |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Klickar du på den <b>✔\* </b> symbolen. Det är en länk.</i><br>
<i>** MFA står för multifaktorautentisering</i>

Se [Autentiseringsscenarier för Azure Active Directory](../active-directory/develop/active-directory-authentication-scenarios.md) mer information om hur du använder Azure Active Directory för autentisering.

## <a name="next-steps"></a>Nästa steg

* [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Tjänst-till-tjänst-autentisering](data-lake-store-service-to-service-authenticate-using-active-directory.md)


