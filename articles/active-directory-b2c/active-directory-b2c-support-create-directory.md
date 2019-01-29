---
title: Felsöka när klienter i Azure Active Directory B2C | Microsoft Docs
description: Problem och lösningar för att skapa en Azure Active Directory eller Azure Active Directory B2C-klient.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b8075e084481a9951c13896fa4db41613adc388f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55160377"
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Felsökning för att skapa en Azure Active Directory eller Azure Active Directory B2C-klient 

## <a name="create-an-azure-ad-tenant"></a>Skapa en Azure AD-klient
Om du inte kan skapa en Azure Active Directory (Azure AD)-klient på det första försöket, försök igen. Kontakta Azure-supporten om problemet kvarstår.

## <a name="create-an-azure-ad-b2c-tenant"></a>Skapa en Azure AD B2C-klient
Om det uppstår problem när du [skapa en Azure Active Directory B2C (Azure AD B2C) klient](active-directory-b2c-get-started.md), försök med följande alternativ:

* Om Azure AD B2C-klient inte visas i din lista över klienter, försök igen att skapa klienten.
* Om Azure AD B2C-klient visas i listan över klienter och du får ett felmeddelande, ta bort klienten och skapa det igen:

    ”Det gick inte att slutföra skapandet av B2C-klient” contosob2c ”. Läs dessa [länken](https://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) för mer information ”.
* Det finns kända problem när du tar bort en befintlig Azure AD B2C-klient och skapa den igen med samma domännamn. När du skapar en ny Azure AD B2C-klient, måste du använda ett annat domännamn.
* Om dessa lösningar inte fungerar kontaktar du supporten för Azure. Mer information finns i [filen supportbegäranden för Azure AD B2C](active-directory-b2c-support.md).

