---
title: Ansluta Azure Active Directory Identity Protection till Azure Security Center | Microsoft Docs
description: Lär dig hur Azure Security Center kan integreras med Azure Active Directory Identity Protection.
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: 0d4b77c2-dba4-4e46-8f55-ab04ddd92496
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 6ab3611d47b32eddd41736df69a3e7382c2b220d
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298769"
---
# <a name="connecting-azure-active-directory-identity-protection-to-azure-security-center"></a>Ansluta Azure Active Directory Identity Protection till Azure Security Center
Det här dokumentet hjälper dig att konfigurera integrering mellan Azure Active Directory (AD) Identity Protection och Azure Security Center.

## <a name="why-connect-azure-ad-identity-protection"></a>Varför ska man ansluta Azure AD Identity Protection?
[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) upptäcka potentiella problem som påverkar organisationens identiteter. När du är ansluten, kan du visa Azure AD Identity Protection-aviseringar i Security Center. Den här integrationen kan du visa, korrelera och undersöka alla säkerhetsaviseringar som är relaterade till dina hybridmolnarbetsbelastningar i Security Center. 

## <a name="how-do-i-configure-this-integration"></a>Hur konfigurerar jag den här integreringen?
Om din organisation använder redan Azure AD Identity Protection, följer du stegen nedan för att konfigurera integreringen:

1. Öppna instrumentpanelen **Security Center**.
2. På den vänstra rutan klickar du på **säkerhetslösningar**. Security Center identifierar automatiskt om Azure AD Identity Protection är aktiverad för din organisation.

    ![AADIP](./media/security-center-aadip-integration/security-center-aadip-integration-fig1.png)

3. Klicka på **CONNECT**.
4. I den **integrera Azure AD Identity Protection** , rulla nedåt och välj rätt arbetsyta:

    ![arbetsyta](./media/security-center-aadip-integration/security-center-aadip-integration-fig2.png)

5. Klicka på **Anslut**.

När du är klar med den här konfigurationen, Azure AD Identity Protection-lösning som visas i den **säkerhetslösningar** sidan under **anslutna lösningar**. 

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure AD Identity Protection till Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Ansluta Microsoft Advanced Threat Analytics till Azure Security Center](security-center-ata-integration.md)
* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
- [Datasäkerhet i Azure Security Center](security-center-data-security.md) – Lär dig hur data hanteras och skyddas i Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – senaste nytt i Azure-säkerhet och information.


