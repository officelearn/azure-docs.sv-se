---
title: Lägg till en brandvägg för webbaserade program i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur du implementerar Azure Security Center-rekommendationer **lägga till en brandvägg för webbaserade program** och **Slutför programskydd**.
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
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105051"
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Lägg till en brandvägg för webbaserade program i Azure Security Center
Azure Security Center rekommendera att du lägger till en brandvägg för webbaserade program (WAF) från en Microsoft-partner för att skydda dina webbprogram. Det här dokumentet vägleder dig genom ett exempel på hur du använder den här rekommendationen.

En WAF-rekommendation visas för valfri offentlig IP-adress (instans nivå IP eller belastningsutjämnade IP-Load) som har en nätverkssäkerhetsgrupp med öppna inkommande webbportar (80,443).

Security Center rekommenderar att du etablerar en WAF för att skydda mot hot mot dina webbprogram på virtuella datorer och på extern App Service-miljöer (ASE) distribueras den [isolerad](https://azure.microsoft.com/pricing/details/app-service/windows/) service-plan. I Isolerad miljö finns dina appar i en privat särskild Azure-miljö vilket passar perfekt för appar som kräver säkra anslutningar till det lokala nätverket eller ytterligare prestanda och skalning. Förutom att din app i en isolerad miljö, måste din app har en extern IP-adress som belastningsutjämnare. Mer information om ASE finns i [dokumentation för App Service Environment](../app-service/environment/intro.md).

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det här dokumentet är inte en stegvis guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. Under **rekommendationer**väljer **skydda webbprogram med Brandvägg för webbaserade program**.
   ![Skydda webb-program][1]
2. Under **skyddar dina webbprogram med hjälp av Brandvägg för webbaserade program**, Välj ett webbprogram. **Lägg till en brandvägg för webbaserade program** öppnas.
   ![Lägga till en brandvägg för webbappar][2]
3. Du kan välja att använda en befintlig waf om det är tillgängligt eller du kan skapa en ny. I det här exemplet finns det inga befintliga Waf så skapar vi en WAF.
4. Välj en lösning för att skapa en WAF i listan med integrerade partnerleverantörer. I det här exemplet väljer vi **Barracuda Web Application Firewall**.
5. **Barracuda Web Application Firewall** öppnas ger dig information om Partnerlösningen. Välj **Skapa**.

   ![Brandväggen information bladet][3]

6. **Ny Brandvägg för webbaserade program** öppnas, där du kan utföra **konfiguration av virtuell dator** steg och ange **WAF-Information**. Välj **konfiguration av virtuell dator**.
7. Under **konfiguration av virtuell dator**, anger du information som krävs för att skapa virtuell dator som kör WAF.

   ![Konfiguration av virtuell dator][4]
   
8. Gå tillbaka till **ny Brandvägg för webbaserade program** och välj **WAF-Information**. Under **WAF-Information**, du konfigurerar WAF själva. Steg 7 kan du konfigurera den virtuella datorn där WAF körs och steg 8 gör det möjligt att etablera WAF själva.

## <a name="finalize-application-protection"></a>Slutföra programskydd
1. Gå tillbaka till **rekommendationer**. En ny post genererades när du har skapat WAF, kallas **Slutför programskydd**. Den här posten kan du vet att du behöver att slutföra processen att faktiskt koppla samman med WAF i det virtuella Azure-nätverket så att den kan skydda programmet.

   ![Slutföra programskydd][5]

2. Välj **Slutför programskydd**. Ett nytt blad öppnas. Du kan se att det finns ett webbprogram som måste ha sin trafik dirigeras om.
3. Välj webbprogrammet. Då öppnas ett blad som ger dig steg för Slutför konfigurationen av brandvägg webbaserade program. Utför stegen och välj sedan **begränsa trafiken**. Security Center gör sedan koppla upp åt dig.

   ![Begränsa trafik][6]

> [!NOTE]
> Du kan skydda flera webbprogram i Security Center genom att lägga till dessa program till din befintliga WAF-distributioner.
>
>

Loggar från att WAF är nu fullständigt integrerade. Security Center kan börja samla in och analysera loggarna så att den kan lyfta fram viktiga säkerhetsvarningar till dig automatiskt.

## <a name="next-steps"></a>Nästa steg
Det här dokumentet visade hur du implementerar Security Center-rekommendationen ”Lägg till ett webbprogram”. Mer information om hur du konfigurerar en brandvägg för webbaserade program finns följande:

* [Konfigurera en brandvägg för webbaserade program (WAF) för en App Service-miljö](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) --hittar du blogginlägg om Azure-säkerhet och regelefterlevnad.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
