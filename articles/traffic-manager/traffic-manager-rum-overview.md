---
title: Verkliga användarmätningar i Azure Traffic Manager
description: I den här introduktionen får du lära dig hur Azure Traffic Manager Real User Measurements fungerar.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: e38e1255b1a84ab5d3fd37e16eb65c76001dbfa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938436"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Översikt över Faktisk slutanvändarmätning i Traffic Manager

När du ställer in en Traffic Manager-profil för att använda prestandaroutningsmetoden tittar tjänsten på var DNS-frågebegäranden kommer ifrån och fattar routningsbeslut för att dirigera dessa begäranden till Azure-regionen som ger dem den lägsta svarstiden. Detta åstadkoms genom att använda den nätverksfördröjningsinformation som Traffic Manager underhåller för olika slutanvändarnätverk.

Med verkliga användarmätningar kan du mäta mätningar av nätverksfördröjningar till Azure-regioner, från de klientprogram som slutanvändarna använder, och låta Traffic Manager beakta den informationen också när du fattar routningsbeslut. Genom att välja att använda realanvändarmätningarna kan du öka noggrannheten i routningen för begäranden som kommer från de nätverk där slutanvändarna finns. 

## <a name="how-real-user-measurements-work"></a>Så här fungerar verkliga användarmätningar

Verkliga användarmätningar fungerar genom att låta klientprogrammen mäta svarstid till Azure-regioner som det ses från slutanvändarnätverken där de används. Om du till exempel har en webbsida som används av användare på olika platser (till exempel i de nordamerikanska regionerna) kan du använda Real User Measurements med prestandaroutningsmetod för att få dem till den bästa Azure-regionen där servern programmet är värd.

Det börjar med att bädda in en Azure som JavaScript (med en unik nyckel i det) på dina webbsidor. När detta är gjort, när en användare besöker den webbsidan, frågar JavaScript Traffic Manager för att få information om Azure-regioner som den ska mäta. Tjänsten returnerar en uppsättning slutpunkter till skriptet som sedan mäter dessa regioner i följd genom att hämta en enda pixelavbildning som finns i dessa Azure-regioner och notera svarstiden mellan den tidpunkt då begäran skickades och den tidpunkt då den första bytet togs emot . Dessa mätningar rapporteras sedan tillbaka till trafikhanterarens tjänst.

Med tiden händer detta många gånger och i många nätverk som leder till att Traffic Manager får mer exakt information om svarstidsegenskaperna för de nätverk där dina slutanvändare finns. Den här informationen börjar få ingå i trafikhanterarens routningsbeslut. Som ett resultat leder det till ökad noggrannhet i dessa beslut baserat på de verkliga användarmätningar som skickas.

När du använder Riktiga användarmätningar faktureras du baserat på antalet mätningar som skickas till Traffic Manager. Mer information om prissättningen finns på [traffic manager-prissidan](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Vanliga frågor och svar

* [Vilka är fördelarna med att använda riktiga användarmätningar?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-benefits-of-using-real-user-measurements)

* [Kan jag använda riktiga användarmätningar med regioner som inte är Azure?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-non-azure-regions)

* [Vilken routningsmetod drar nytta av verkliga användarmätningar?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#which-routing-method-benefits-from-real-user-measurements)

* [Måste jag aktivera Real User Measurements varje profil separat?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [Hur inaktiverar jag Riktiga användarmätningar för min prenumeration?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Kan jag använda realanvändarmätningar med andra klientprogram än webbsidor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Hur många mätningar görs varje gång min webbsida med riktiga användarmätningar återges?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Finns det en fördröjning innan skriptet Real User Measurements körs på min webbsida?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Kan jag använda riktiga användarmätningar med endast de Azure-regioner som jag vill mäta?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [Kan jag begränsa antalet mätningar som gjorts till ett visst antal?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [Kan jag se mätningarna som gjorts av mitt klientprogram som en del av Real User Measurements?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Kan jag ändra måttskriptet från Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Kommer det att vara möjligt för andra att se nyckeln jag använder med riktiga användarmätningar?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [Kan andra missbruka min RUM-nyckel?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-others-abuse-my-rum-key)

* [Måste jag lägga mätningen JavaScript i alla mina webbsidor?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [Kan information om mina slutanvändare identifieras av Traffic Manager om jag använder riktiga användarmätningar?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [Måste webbsidan som mäter verkliga användarmätningar använda Traffic Manager för routning?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [Måste jag vara värd för någon tjänst i Azure-regioner som ska användas med riktiga användarmätningar?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [Kommer min Azure-bandbreddsanvändning att öka när jag använder riktiga användarmätningar?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du använder [riktiga användarmätningar med webbsidor](traffic-manager-create-rum-web-pages.md)
- Läs om [hur Traffic Manager fungerar](traffic-manager-overview.md)
- Läs mer om [Mobile Center](https://docs.microsoft.com/mobile-center/)
- Läs mer om [de trafikroutningsmetoder](traffic-manager-routing-methods.md) som stöds av Traffic Manager
- Läs om hur du [skapar en Traffic Manager-profil](traffic-manager-create-profile.md)

