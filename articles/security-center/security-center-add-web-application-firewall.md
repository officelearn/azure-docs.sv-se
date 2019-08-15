---
title: Lägg till en brand vägg för webbaserade program i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver hur du implementerar Azure Security Center rekommendationer **Lägg till en brand vägg för webbaserade program** och **Slutför program skydd**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: 63852ccab842f11f30bcbe695206fedf72931911
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "60706292"
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Lägg till en brand vägg för webbaserade program i Azure Security Center
Azure Security Center kan rekommendera att du lägger till en brand vägg för webbaserade program (WAF) från en Microsoft-partner för att skydda dina webb program. Det här dokumentet vägleder dig genom ett exempel på hur du använder den här rekommendationen.

En WAF-rekommendation visas för alla offentliga IP-adresser (antingen IP-adresser på instans nivå eller belastningsutjämnade IP) som har en associerad nätverks säkerhets grupp med öppna inkommande webb portar (80 443).

Security Center rekommenderar att du etablerar en WAF för att hjälpa till att skydda dina webb program på virtuella datorer och i externa App Service miljöer (ASE) som distribueras i [](https://azure.microsoft.com/pricing/details/app-service/windows/) den isolerade tjänste planen. I Isolerad miljö finns dina appar i en privat särskild Azure-miljö vilket passar perfekt för appar som kräver säkra anslutningar till det lokala nätverket eller ytterligare prestanda och skalning. Förutom att din app är i en isolerad miljö måste appen ha en extern IP-adress för belastningsutjämnaren. Mer information om ASE finns i App Service-miljön- [dokumentationen](../app-service/environment/intro.md).

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det här dokumentet är inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. Under **rekommendationer**väljer du **skydda webb program med brand vägg för webbaserade program**.
   ![Säkert webb program][1]
2. Under **skydda dina webb program med brand vägg för webbaserade program**väljer du ett webb program. **Lägg till en brand vägg för webbaserade program** öppnas.
   ![Lägga till en brandvägg för webbappar][2]
3. Du kan välja att använda en befintlig brand vägg för webbaserade program om den är tillgänglig eller så kan du skapa en ny. I det här exemplet finns det inga befintliga WAF-tillgängliga så vi skapar en WAF.
4. Om du vill skapa en WAF väljer du en lösning i listan över integrerade partner. I det här exemplet väljer vi **brand vägg för Barracuda-webbprogram**.
5. **Barracuda webb program brand vägg** öppnas och ger dig information om partner lösningen. Välj **Skapa**.

   ![Informations blad för brand vägg][3]

6. **Ny brand vägg för webbaserade program** öppnas där du kan utföra konfigurations steg för **virtuella datorer** och tillhandahålla **WAF-information**. Välj **konfiguration av virtuell dator**.
7. Under **VM-konfiguration**anger du information som krävs för att skapa den virtuella dator som kör WAF.

   ![Konfiguration av virtuell dator][4]
   
8. Återgå till **ny brand vägg för webbaserade program** och välj **WAF-information**. Under **WAF information**konfigurerar du själva WAF. Steg 7 kan du konfigurera den virtuella dator som WAF körs på och steg 8 gör att du kan etablera själva WAF.

## <a name="finalize-application-protection"></a>Slutför programskydd
1. Gå tillbaka till **rekommendationer**. En ny post genererades efter att du har skapat WAF, kallat **Slutför program skydd**. Med den här posten kan du veta att du behöver slutföra processen för att själva koppla upp WAF i Azure-Virtual Network så att den kan skydda programmet.

   ![Slutför programskydd][5]

2. Välj **Slutför program skydd**. Ett nytt blad öppnas. Du kan se att det finns ett webb program som måste omdirigeras av trafiken.
3. Välj webb programmet. Ett blad öppnas där du får steg för att slutföra installationen av brand väggen för webb program. Slutför stegen och välj sedan **begränsa trafik**. Security Center sedan kan du använda den här kabeln.

   ![Begränsa trafik][6]

> [!NOTE]
> Du kan skydda flera webb program i Security Center genom att lägga till dessa program i dina befintliga WAF-distributioner.
>
>

Loggarna från den WAF är nu helt integrerade. Security Center kan börja samla in och analysera loggar automatiskt så att den kan visa viktiga säkerhets aviseringar.

## <a name="next-steps"></a>Nästa steg
Det här dokumentet visar hur du implementerar Security Center rekommendationen "Lägg till ett webb program". Mer information om hur du konfigurerar en brand vägg för webbaserade program finns i följande avsnitt:

* [Konfigurera en brandvägg för webbaserade program (WAF) för en App Service-miljö](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md) – lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhets blogg](https://blogs.msdn.com/b/azuresecurity/) – hitta blogg inlägg om säkerhet och efterlevnad i Azure.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
