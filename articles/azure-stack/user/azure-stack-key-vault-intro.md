---
title: Introduktion till Azure Stack Key Vault | Microsoft Docs
description: Lär dig hur Azure Stack Key Vault hanterar nycklar och hemligheter
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/05/2019
ms.author: sethm
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: afbc4d08e898cb0a465b8561dde65614f2cb6211
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251341"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Introduktion till Nyckelvalv i Azure Stack

## <a name="prerequisites"></a>Förutsättningar

* Du måste prenumerera på ett erbjudande som innehåller Azure Key Vault-tjänsten.  
* [PowerShell är konfigurerat för användning med Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="key-vault-basics"></a>Key Vault-grunderna

Key Vault i Azure Stack hjälper till att skydda kryptografiska nycklar och hemligheter som program och tjänster i molnet använder. Med Key Vault kan kryptera du nycklar och hemligheter, till exempel:

* Autentiseringsnycklar
* Lagringskontonycklar
* Datakrypteringsnycklar
* PFX-filer
* Lösenord

Key Vault förenklar nyckelhanteringen och låter dig behålla kontrollen över nycklar som kommer åt och krypterar data. Utvecklare kan skapa nycklar för utveckling och testning på några minuter och sedan sömlöst migrera dem till produktionsnycklar. Säkerhetsadministratörer kan bevilja (och återkalla) behörighet till nycklar efter behov.

Vem som helst med en Azure Stack-prenumeration kan skapa och använda nyckelvalv. Även om Key Vault hjälper utvecklare och säkerhetsadministratörer, kan en operatör som hanterar andra Azure Stack-tjänster för en organisation implementera och hantera den. Azure Stack operatör kan logga in med ett Azure Stack-prenumerationen kan till exempel skapa ett valv för organisationen att lagra nycklar och sedan är ansvarig för dessa operativa uppgifter:

* Skapa eller importera en nyckel eller hemlighet.
* Återkalla eller ta bort en nyckel eller hemlighet.
* Auktorisera användare eller program åtkomst till nyckelvalvet, så att de kan sedan hantera eller använda sina nycklar och hemligheter.
* Konfigurera nyckelanvändningen (till exempel signera eller kryptera).

Operatören kan sedan ge utvecklare med Uniform Resource Identifier (URI: er) för att anropa från sina program. Operatörer kan också ge säkerhetsadministratörer nyckelanvändning loggningsinformation.

Utvecklare kan också hantera nycklar direkt, med hjälp av API:er. Mer information finns i utvecklarguide för Key Vault.

## <a name="scenarios"></a>Scenarier

Följande scenarier beskriver hur Key Vault kan hjälpa dig att uppfylla kraven från utvecklare och säkerhetsadministratörer.

### <a name="developer-for-an-azure-stack-application"></a>Utvecklare för ett Azure Stack-program

**Problem:** Jag vill skriva ett program för Azure Stack som använder nycklar för signering och kryptering. Jag vill att de ska vara externa från mitt program så att lösningen passar ett geografiskt distribuerat program.

**Instruktion:** Nycklar lagras i ett valv och anropas av en URI vid behov.

### <a name="developer-for-software-as-a-service-saas"></a>Utvecklare av programvara som en tjänst (SaaS)

**Problem:** Jag vill inte det ansvaret för Mina kunders nycklar och hemligheter. Jag vill att kunderna ska äga och hantera sina nycklar så att jag kan koncentrera dig på att göra vad jag gör bäst, dvs. att erbjuda grundläggande funktioner.

**Instruktion:** Kunder kan importera sina egna nycklar till Azure Stack och hantera dem sedan.

### <a name="chief-security-officer-cso"></a>Chief Security Officer (CSO)

**Problem:** Jag vill vara säker på att min organisation har kontrollen över nycklarnas livscykel och kan övervaka nyckelanvändningen.

**Instruktion:** Key Vault har utformats så att Microsoft varken ser eller extraherar dina nycklar. När ett program behöver utföra kryptografiska åtgärder med hjälp av Kundnycklar, använder Key Vault nycklarna åt programmet. Programmet kan inte se kundernas nycklar. Även om vi använder flera Azure Stack-tjänster och resurser, kan du hantera nycklarna från en enda plats i Azure Stack. Valvet innehåller ett enda gränssnitt, oavsett hur många valv du har i Azure Stack, vilka regioner de support och använda dem för vilka program.

## <a name="next-steps"></a>Nästa steg

* [Hantera Nyckelvalv i Azure Stack-portalen](azure-stack-key-vault-manage-portal.md)  
* [Hantera Nyckelvalv i Azure Stack med hjälp av PowerShell](azure-stack-key-vault-manage-powershell.md)

