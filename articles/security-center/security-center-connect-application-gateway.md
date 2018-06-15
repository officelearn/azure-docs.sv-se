---
title: Ansluta Microsoft Azure Programgateway till Azure Security Center | Microsoft Docs
description: Lär dig hur du integrerar Programgateway och Azure Security Center för att förbättra den övergripande säkerheten för dina resurser.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: terrylan
ms.openlocfilehash: 7c15e5a86df7ff2a374aa9b62d2775b1eb035fc6
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
ms.locfileid: "29854495"
---
# <a name="connecting-microsoft-azure-application-gateway-to-azure-security-center"></a>Ansluta Microsoft Azure Programgateway till Azure Security Center
Det här dokumentet hjälper dig att konfigurera integrationen med Brandvägg för Programgateway webbaserade program (Brandvägg) och Security Center.

## <a name="why-connect-application-gateway"></a>Varför ska jag ansluta Programgateway?
Brandvägg i Programgateway skyddar webbprogram från vanliga webbaserade attacker som SQL injection, webbplatser scripting attacker och sessionen hijacks. Security Center kan integreras med Programgateway att undvika och upptäcka hot mot oskyddade webbprogram i din miljö.

## <a name="how-do-i-configure-this-integration"></a>Hur konfigurerar jag den här integreringen?
Security Center identifierar tidigare distribuerade Brandvägg instanser i prenumerationen. Dessa lösningar för att ansluta till Security Center för att aktivera integration av aviseringar.

> [!NOTE]
> Gateway-Brandvägg program också etableras från Security Center **rekommendationer** enligt beskrivningen i [lägga till en brandvägg för webbaserade program](security-center-add-web-application-firewall.md).
>
>

1. Logga in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/).

2. På den **Microsoft Azure-menyn**väljer **Security Center**. **Security Center – Översikt** öppnas.

3. Under **översikt**väljer **säkerhetslösningar**.

  ![Säkerhetscenter: översikt](./media/security-center-connect-application-gateway/overview.png)

4. Under **identifierade lösningar** Microsoft SaaS-baserade Brandvägg för webbaserade program och markera **Anslut**.

  ![Identifierade lösningar](./media/security-center-connect-application-gateway/connect.png)

5. **Ansluta Brandvägg lösningen** öppnas.  Välj **Anslut** att integrera Brandvägg och Security Center.

  ![Anslut WAF-lösning](./media/security-center-connect-application-gateway/waf-solution.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du integrerar program Gateway-Brandvägg i Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Integrera säkerhetslösningar i Security Center](security-center-partner-integration.md)
* [Ansluta Microsoft Advanced Threat Analytics till Security Center](security-center-ata-integration.md)
* [Azure Active Directory identitetsskydd ansluter till Security Center](security-center-aadip-integration.md)
* [Övervakning av säkerhetshälsa i Security Center](security-center-monitoring.md).
* [Övervaka partnerlösningar med Security Center](security-center-partner-solutions.md).
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md).
* [Azure säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/).
