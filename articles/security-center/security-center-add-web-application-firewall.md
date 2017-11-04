---
title: "Lägg till en brandvägg för webbaserade program i Azure Security Center | Microsoft Docs"
description: "Det här dokumentet beskrivs hur du implementerar Azure Security Center-rekommendationerna ** lägga till en web application firewall ** och ** slutför programmet skydd **."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: terrylan
ms.openlocfilehash: e858db97c3e7a832ad01e16a60d486a758109d7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Lägg till en brandvägg för webbaserade program i Azure Security Center
Azure Security Center kan rekommenderar att du lägger till en brandvägg för webbaserade program (Brandvägg) från en Microsoft-partner att skydda dina webbprogram. Det här dokumentet vägleder dig genom ett exempel på hur du använder den här rekommendationen.

En Brandvägg rekommendation visas för alla offentliga Internetriktade IP-adresser (instans nivå IP eller Load belastningsutjämnade IP) som har en nätverkssäkerhetsgrupp med öppna webbplats för inkommande portar (80,443).

Security Center rekommenderar att du etablerar en Brandvägg för att skydda mot attacker målobjekt för webbaserade program på virtuella datorer samt på Apptjänst-miljö. En App Service miljö (ASE) är en [Premium](https://azure.microsoft.com/pricing/details/app-service/) service plan alternativet för Azure App Service som tillhandahåller en helt isolerad och dedikerad miljö för Azure App Service-program som körs på ett säkert sätt. Mer information om ASE finns i [dokumentationen till App Service-miljö](../app-service/environment/intro.md).

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det här dokumentet är inte en stegvis guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer** bladet väljer **säkra webbprogram med hjälp av Brandvägg för webbaserade program**.
   ![Skydda webb-program][1]
2. I den **skydda ditt webbprogram med hjälp av Brandvägg för webbaserade program** bladet Välj ett webbprogram. Den **lägga till en brandvägg för webbaserade program** blad öppnas.
   ![Lägga till en brandvägg för webbappar][2]
3. Du kan välja att använda en befintlig Brandvägg för webbaserade program om det är tillgängligt eller du kan skapa en ny. I det här exemplet finns det inga befintliga WAFs så skapar vi en Brandvägg.
4. Välj en lösning för att skapa en Brandvägg, i listan med integrerade partnerleverantörer. I det här exemplet väljer vi **Barracuda Brandvägg för webbaserade program**.
5. Den **Barracuda Brandvägg för webbaserade program** öppnas ett blad med information om Partnerlösningen. Välj **skapa** i bladet information.

   ![Brandväggen information bladet][3]

6. Den **ny Brandvägg för webbaserade program** blad öppnas, där du kan utföra **VM-konfiguration** steg och ange **Brandvägg Information**. Välj **VM-konfiguration**.
7. I den **VM-konfiguration** bladet anger du information som krävs för att få igång den virtuella datorn som kör Brandvägg.
   ![VM-konfiguration][4]
8. Gå tillbaka till den **ny Brandvägg för webbaserade program** och välj **Brandvägg Information**. I den **Brandvägg Information** bladet du konfigurera Brandvägg sig själv. Steg 7 kan du konfigurera den virtuella datorn som Brandvägg körs på och steg 8 kan du etablera Brandvägg sig själv.

## <a name="finalize-application-protection"></a>Slutför programskydd
1. Gå tillbaka till den **rekommendationer** bladet. En ny post skapades när du har skapat Brandvägg, kallas **Slutför programskydd**. Den här posten kan du vet att du måste slutföra processen för att koppla samman Brandvägg i det virtuella Azure-nätverket så att den kan skydda programmet.

   ![Slutför programskydd][5]

2. Välj **Slutför programskydd**. Ett nytt blad öppnas. Du kan se att det finns ett program som måste ha sin trafik dirigeras om.
3. Välj webbprogrammet. Då öppnas ett blad som ger steg för att slutföra inställningarna för webbprogrammet brandväggen. Utför stegen och välj sedan **begränsa trafik**. Security Center sedan gör kablar upp.

   ![Begränsa trafik][6]

> [!NOTE]
> Du kan skydda flera webbprogram i Security Center genom att lägga till dessa program till din befintliga Brandvägg-distributioner.
>
>

Loggar från den Brandvägg är nu fullständigt integrerat. Security Center kan börja samla in och analysera loggarna så att den kan ansluta viktiga säkerhetsaviseringar du automatiskt.

## <a name="next-steps"></a>Nästa steg
Det här dokumentet visar dig hur du implementerar Security Center-rekommendationen ”Lägg till ett webbprogram”. Mer information om hur du konfigurerar en brandvägg för webbaserade program finns i följande avsnitt:

* [Konfigurera en brandvägg för webbaserade program (Brandvägg) för Apptjänst-miljö](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) --hittar du blogginlägg om säkerhet och Azure kompatibilitet.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
