---
title: "Azure Active Directory B2C: Felsöka skapar hyresgäster | Microsoft Docs"
description: "Problem och lösningar för att skapa en Azure Active Directory eller Azure Active Directory B2C-klient."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 7ba4c6b2-161b-45b5-b3bd-ccb662f5d7a0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 81af4536fc223319369aff262d42149cfbf1a1e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
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

