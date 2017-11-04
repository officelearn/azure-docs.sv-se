---
title: Azure Key Vault-stacken introduktion | Microsoft Docs
description: "Lär dig hur Azure-stacken Key Vault hanterar nycklar och hemligheter"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/04/2017
ms.author: sngun
ms.openlocfilehash: 621a0cb865d0c050d7271d10bd14076f9f0c6f67
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2017
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Introduktion till Nyckelvalvet i Azure-stacken

## <a name="prerequisites"></a>Krav 

* Du måste prenumerera på ett erbjudande som innehåller Azure Key Vault-tjänsten.  
* [PowerShell har konfigurerats för användning med Azure Stack](azure-stack-powershell-configure-user.md).
 
## <a name="key-vault-basics"></a>Key Vault-grunderna
Key Vault i Azure-stacken hjälper dig att skydda krypteringsnycklar och hemligheter som molnet program och tjänster använder. Med Key Vault kan kryptera du nycklar och hemligheter, som:
   * Autentiseringsnycklar 
   * Lagringskontonycklar
   * Datakrypteringsnycklar
   * PFX-filer
   * Lösenord

Key Vault förenklar nyckelhanteringen och låter dig behålla kontrollen över nycklar som kommer åt och krypterar data. Utvecklare kan skapa nycklar för utveckling och testning på några minuter och sedan sömlöst migrera dem till produktionsnycklar. Säkerhetsadministratörer kan bevilja (och återkalla) behörighet till nycklar efter behov.

Vem som helst med en Stack för Azure-prenumeration kan skapa och använda nyckelvalv. Även om Key Vault hjälper utvecklare och säkerhetsadministratörer, kan en operatör som hanterar andra Azure-Stack-tjänster för en organisation implementera och hantera den. Till exempel skapa Azure-stacken operatorn kan logga in med en Stack för Azure-prenumeration, ett valv för organisationen att lagra nycklar och sedan ansvara för dessa åtgärder:

* Skapa eller importera en nyckel eller hemlighet.
* Återkalla eller ta bort en nyckel eller hemlighet.
* Auktorisera användare eller program åtkomst till nyckelvalvet, så att de kan sedan hantera eller använda sina nycklar och hemligheter.
* Konfigurera nyckelanvändningen (till exempel signera eller kryptera).

Operatorn kan sedan ge utvecklare Uniform Resource Identifier () att anropa från sina program. Operatörer kan också ge säkerhetsadministratörer nyckelanvändning loggningsinformation.

Utvecklare kan också hantera nycklar direkt, med hjälp av API:er. Mer information finns i guiden för Key Vault-utvecklare.

## <a name="scenarios"></a>Scenarier
Nedan beskrivs hur Key Vault kan hjälpa dig att möta behoven hos utvecklare och säkerhetsadministratörer.

### <a name="developer-for-an-azure-stack-application"></a>Utvecklare för ett program för Azure-stacken
**Problem:** jag vill skriva ett program för Azure-stacken som använder nycklar för signering och kryptering. Jag vill att dessa nycklar ska vara externa från mitt program så att lösningen passar för ett program som distribueras geografiskt.

**Instruktionen:** nycklar lagras i ett valv och anropas av en URI vid behov.

### <a name="developer-for-software-as-a-service-saas"></a>Utvecklare av programvara som en tjänst (SaaS)
**Problem:** jag vill inte ansvar eller hitta potentiella ansvar för min kunds nycklar och hemligheter. Jag vill att kunderna ska äga och hantera sina nycklar så att jag kan koncentrera dig på att göra vad jag gör bäst, vilket är att erbjuda grundläggande funktioner.

**Instruktionen:** kunder kan importera sina egna nycklar till Azure-stacken och hantera dem. 

### <a name="chief-security-officer-cso"></a>Chefen säkerhetsansvarig (rederiets)
**Problem:** jag vill se till att min organisation har kontrollen över nyckelns livscykel och kan övervaka nyckelanvändningen.

**Instruktionen:** Key Vault är utformat så att Microsoft inte kan se eller extrahera dina nycklar. När ett program behöver utföra kryptografiska åtgärder med hjälp av kunders nycklar används Key Vault nycklar för programmet. Programmet kan inte se kundernas nycklar. Även om vi använder flera Azure-stacken tjänster och resurser kan du hantera nycklarna från en enda plats i Azure-stacken. Valvet tillhandahåller ett enda gränssnitt, oavsett hur många valv du har i Azure-stacken, vilka regioner de support och vilka program som använder dem.

## <a name="next-steps"></a>Nästa steg

* [Hantera Key Vault i Azure-stacken med hjälp av portalen](azure-stack-kv-manage-portal.md)  
* [Hantera Key Vault i Azure-stacken med hjälp av PowerShell](azure-stack-kv-manage-powershell.md)

