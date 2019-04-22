---
title: Autentisering i Azure Data Lake Storage Gen1 med hjälp av Azure Active Directory | Microsoft Docs
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
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58884643"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Autentisering med Azure Data Lake Storage Gen1 med hjälp av Azure Active Directory

Azure Data Lake Storage Gen1 använder Azure Active Directory för autentisering. Innan du skapar ett program som fungerar med Data Lake Storage Gen1, måste du bestämma hur du autentiserar ditt program med Azure Active Directory (AD Azure).

## <a name="authentication-options"></a>Autentiseringsalternativ

* **Slutanvändarautentisering** -användarens autentiseringsuppgifter för Azure används för att autentisera med Data Lake Storage Gen1. Programmet du skapar för att arbeta med Data Lake Storage Gen1 ber om autentiseringsuppgifterna för användaren. Därför kan den här autentiseringsmekanismen är *interaktiva* och programmet körs i kontexten för den inloggade användaren. Mer information och instruktioner finns i [slutanvändarautentisering för Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Tjänst-till-tjänst-autentisering** – Använd det här alternativet om du vill att ett program för att autentisera sig själv med Data Lake Storage Gen1. I sådana fall kan du skapa ett Azure Active Directory (AD)-program och använder nyckeln från Azure AD-programmet för att autentisera med Data Lake Storage Gen1. Därför kan den här autentiseringsmekanismen är *icke-interaktiv*. Mer information och instruktioner finns i [tjänst-till-tjänst-autentisering för Data Lake Storage Gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md).

I följande tabell visas hur slutanvändare och tjänst-till-tjänst-autentiseringsmekanismer stöds för Data Lake Storage Gen1. Här är hur du genom att läsa tabellen.

* Symbolen ✔ * anger att autentiseringsalternativet som stöds och länkar till en artikel som visar hur du använder alternativ för autentisering. 
* Symbolen ✔ anger att det finns stöd för alternativ för autentisering. 
* Tomma celler anger att autentiseringsalternativet inte stöds.


|Använd det här autentiseringsalternativet med...                   |.NET         |Java     |PowerShell |Azure CLI | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Slutanvändare (utan MFA **)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(inaktuell)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Slutanvändare (med MFA)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|(Med klientnyckel) tjänst-till-tjänst         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|(Med klientcertifikat) tjänst-till-tjänst |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Klickar du på den <b>✔\* </b> symbolen. Det är en länk.</i><br>
<i>** MFA står för multifaktorautentisering</i>

Se [Autentiseringsscenarier för Azure Active Directory](../active-directory/develop/authentication-scenarios.md) för mer information om hur du använder Azure Active Directory för autentisering.

## <a name="next-steps"></a>Nästa steg

* [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Tjänst-till-tjänst-autentisering](data-lake-store-service-to-service-authenticate-using-active-directory.md)


