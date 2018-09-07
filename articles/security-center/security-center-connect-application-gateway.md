---
title: Ansluta Microsoft Azure Application Gateway till Azure Security Center | Microsoft Docs
description: Lär dig hur du integrerar Application Gateway och Azure Security Center för att förbättra den övergripande säkerheten för dina resurser.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: rkarlin
ms.openlocfilehash: 588932e10e0e0d95feaa4bc8d889249ebebeb766
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44026627"
---
# <a name="connecting-microsoft-azure-application-gateway-to-azure-security-center"></a>Ansluta Microsoft Azure Application Gateway till Azure Security Center
Det här dokumentet hjälper dig att konfigurera integrationen med Application Gateway waf (WAF) och Security Center.

## <a name="why-connect-application-gateway"></a>Varför ska man ansluta Application Gateway?
WAF i Application Gateway skyddar webbprogram från vanliga webbaserade attacker som SQL injection, cross-site skriptattacker och sessionskapningar. Security Center kan integreras med Application Gateway för att förhindra och identifiera hot mot oskyddade webbappar i din miljö.

## <a name="how-do-i-configure-this-integration"></a>Hur konfigurerar jag den här integreringen?
Security Center identifierar tidigare distribuerad WAF-instanser i prenumerationen. Ansluta dessa lösningar till Security Center för att aktivera integration av aviseringar.

> [!NOTE]
> Application Gateway WAF kan också etableras från Security Center **rekommendationer** enligt beskrivningen i [lägga till en brandvägg för webbaserade program](security-center-add-web-application-firewall.md).
>
>

1. Logga in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/).

2. På menyn **Microsoft Azure** väljer du **Security Center**.

3. Under **RESOURCE SECURITY hygien**väljer **säkerhetslösningar**.

  ![Security Center – Översikt](./media/security-center-connect-application-gateway/overview.png)

4. Under **identifierade lösningar** hitta Microsoft SaaS-baserad Brandvägg för webbaserade program och välj **CONNECT**.

  ![Identifierade lösningar](./media/security-center-connect-application-gateway/connect.png)

5. **Anslut WAF-lösning** öppnas.  Välj **Connect** att integrera WAF och Security Center.

  ![Anslut WAF-lösning](./media/security-center-connect-application-gateway/waf-solution.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att integrera Application Gateway WAF i Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Integrera säkerhetslösningar i Security Center](security-center-partner-integration.md)
* [Ansluta Microsoft Advanced Threat Analytics till Security Center](security-center-ata-integration.md)
* [Ansluta Azure Active Directory Identity Protection till Security Center](security-center-aadip-integration.md)
* [Övervakning av säkerhetshälsa i Security Center](security-center-monitoring.md).
* [Övervaka partnerlösningar med Security Center](security-center-partner-solutions.md).
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md).
* [Azure säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/).
