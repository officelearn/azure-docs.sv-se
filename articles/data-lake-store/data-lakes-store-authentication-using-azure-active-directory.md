---
title: Autentisering i Data Lake Store med Azure Active Directory | Microsoft Docs
description: Lär dig att autentisera med Data Lake Store med Azure Active Directory
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 7db48b03a6f34655917c82702c12dbc9bc4c987a
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494185"
---
# <a name="authentication-with-data-lake-store-using-azure-active-directory"></a>Autentisering med Data Lake Store med Azure Active Directory

Azure Data Lake Store använder Azure Active Directory för autentisering. Innan du skapar ett program som fungerar med Azure Data Lake Store, måste du bestämma hur du autentiserar ditt program med Azure Active Directory (AD Azure).

## <a name="authentication-options"></a>Autentiseringsalternativ

* **Slutanvändarautentisering** -användarens autentiseringsuppgifter för Azure används för att autentisera med Data Lake Store. Programmet du skapar för att arbeta med Data Lake Store ber om autentiseringsuppgifterna för användaren. Därför kan den här autentiseringsmekanismen är *interaktiva* och programmet körs i kontexten för den inloggade användaren. Mer information och instruktioner finns i [slutanvändarautentisering för Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Tjänst-till-tjänst-autentisering** – Använd det här alternativet om du vill att ett program för att autentisera sig själv med Data Lake Store. I sådana fall kan du skapa ett Azure Active Directory (AD)-program och använder nyckeln från Azure AD-programmet för att autentisera med Data Lake Store. Därför kan den här autentiseringsmekanismen är *icke-interaktiv*. Mer information och instruktioner finns i [tjänst-till-tjänst-autentisering för Data Lake Store](data-lake-store-service-to-service-authenticate-using-active-directory.md).

I följande tabell visas hur slutanvändare och tjänst-till-tjänst-autentiseringsmekanismer stöds för Data Lake Store. Här är hur du genom att läsa tabellen.

* Symbolen ✔ * anger att autentiseringsalternativet som stöds och länkar till en artikel som visar hur du använder alternativ för autentisering. 
* Symbolen ✔ anger att det finns stöd för alternativ för autentisering. 
* Tomma celler anger att autentiseringsalternativet inte stöds.


|Använd det här autentiseringsalternativet med...                   |.NET         |Java     |PowerShell |CLI 2.0 | Python   |REST     |
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


