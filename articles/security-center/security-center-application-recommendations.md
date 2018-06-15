---
title: Skydda dina program i Azure Security Center | Microsoft Docs
description: Det här dokumentet behandlar rekommendationerna i Azure Security Center som hjälper dig skydda dina Azure-program och uppfyller säkerhetsprinciper.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: b5fc7a9e-24b1-415f-b3b5-62a53f5dd424
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: terrylan
ms.openlocfilehash: b5bc02082fa8c2681aa67910729870921fec329d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866270"
---
# <a name="protecting-your-applications-in-azure-security-center"></a>Skydda dina program i Azure Security Center
Azure Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsrisker, skapar rekommendationer som hjälper dig att konfigurera nödvändiga kontroller.  Rekommendationer gäller för Azure resurstyper: virtuella datorer (VM), nätverk, SQL och program.

Den här artikeln tar rekommendationer som gäller för program.  Programmet rekommendationer center runt distribution av en brandvägg för webbaserade program.  Använd tabellen nedan som referens för att förstå rekommendationerna som tillgängliga program och det var och en gör om du använder den.

## <a name="available-application-recommendations"></a>Rekommendationer för tillgängliga program
| Rekommendation | Beskrivning |
| --- | --- |
| [Lägga till en brandvägg för webbappar](security-center-add-web-application-firewall.md) |Rekommenderar att du distribuerar en brandvägg för webbaserade program (Brandvägg) för web-slutpunkter. En Brandvägg rekommendation visas för alla offentliga Internetriktade IP-adresser (instans nivå IP eller Load belastningsutjämnade IP) som har en nätverkssäkerhetsgrupp med öppna webbplats för inkommande portar (80,443).</br></br>Security Center rekommenderar att du etablerar en Brandvägg för att skydda mot attacker målobjekt för webbaserade program på virtuella datorer samt på Apptjänst-miljö. En App Service miljö (ASE) är en [Premium](https://azure.microsoft.com/pricing/details/app-service/) service plan alternativet för Azure App Service som tillhandahåller en helt isolerad och dedikerad miljö för Azure App Service-program som körs på ett säkert sätt. Mer information om ASE finns i [dokumentationen till App Service-miljö](../app-service/environment/intro.md).</br></br>Du kan skydda flera webbprogram i Security Center genom att lägga till dessa program till din befintliga Brandvägg-distributioner. |
| [Slutför programskydd](security-center-add-web-application-firewall.md#finalize-application-protection) |Om du vill slutföra konfigurationen av en Brandvägg måste trafik dirigeras till Brandvägg-enhet. Efter den här rekommendationen är klar nödvändiga ändringar. |

## <a name="see-also"></a>Se även
Mer information om rekommendationer som gäller för andra typer av Azure finns i:

* [Skydda dina virtuella datorer i Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Skydda ditt nätverk i Azure Security Center](security-center-network-recommendations.md)
* [Skydda din SQL Azure-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
