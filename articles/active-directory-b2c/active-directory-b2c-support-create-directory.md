---
title: Felsöka skapar hyresgäster i Azure Active Directory B2C | Microsoft Docs
description: Problem och lösningar för att skapa en Azure Active Directory eller Azure Active Directory B2C-klient.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 90d9d2fb80dfbd094754850b7d1270a5fafcdd96
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712512"
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Felsökning: skapa en Azure Active Directory eller Azure Active Directory B2C-klient 

## <a name="create-an-azure-ad-tenant"></a>Skapa en Azure AD-klient
Om du inte skapa en Azure Active Directory (Azure AD)-klient på det första försöket, försök igen. Kontakta Azure-supporten om problemet kvarstår.

## <a name="create-an-azure-ad-b2c-tenant"></a>Skapa en Azure AD B2C-klient
Om du får problem när du [skapa ett Azure Active Directory B2C-klient (Azure AD B2C)](active-directory-b2c-get-started.md), försök med följande alternativ:

* Om Azure AD B2C-klient inte visas i din lista över klienter, försök igen att skapa klienten.
* Om Azure AD B2C-klient visas i listan över klienter och följande felmeddelande, ta bort innehavaren och skapa den igen:

    ”Det gick inte att slutföra skapandet av B2C-klient 'contosob2c'. Finns det [länken](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) för mer hjälp ”.
* Det finns kända problem när du tar bort en befintlig Azure AD B2C-klient och skapa den igen med samma domännamn. När du skapar en ny Azure AD B2C-klient, måste du använda ett annat domännamn.
* Kontakta Azure-supporten om dessa lösningar inte fungerar. Mer information finns i [filbegäranden stöd för Azure AD B2C](active-directory-b2c-support.md).

