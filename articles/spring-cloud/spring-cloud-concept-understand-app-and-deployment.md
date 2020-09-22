---
title: Förstå appar och distribution i Azure våren Cloud
description: Skillnaden mellan program och distribution i Azure våren-molnet.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9e909db0041979eb7bc4fc30bd9551382e83c488
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90892509"
---
# <a name="understand-app-and-deployment-in-azure-spring-cloud"></a>Förstå appar och distribution i Azure våren Cloud

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

**App** och **distribution** är de två viktiga begreppen i resurs modellen i Azure våren Cloud. I Azure våren Cloud är en *app* en abstraktion av en affärsappar eller en mikrotjänst.  En version av kod eller binär distribution som *appen* körs i en *distribution*.  Appar körs i en *Azure våren moln tjänst instans*eller bara *tjänst instans*, som du ser härnäst.

 ![Appar och distributioner](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

Du kan ha flera tjänst instanser i en enda Azure-prenumeration, men moln tjänsten Azure våren är enkel att använda när alla appar som utgör en affärsapp eller mikrotjänst finns i en enda tjänst instans.

Azure våren Cloud standard-nivån tillåter att en app har en produktions distribution och en mellanlagrings distribution, så att du enkelt kan göra en blå/grön-distribution.

## <a name="app"></a>App
Följande funktioner/egenskaper definieras på App-nivå.

| Enum | Definition |
|:--|:----------------|
| Offentliga</br>Slutpunkt | URL: en för att komma åt appen |
| Anpassad</br>Domän | CNAME-post som skyddar den anpassade domänen |
| Tjänst</br>Bindning | Bindnings konfigurations egenskaper som anges i function.jspå filen och attributet *ServiceBusTrigger* |
| Hanterade</br>Identitet | Hanterad identitet med Azure Active Directory gör att din app enkelt kan komma åt andra Azure AD-skyddade resurser som Azure Key Vault |
| Permanent</br>Storage | Inställning som gör att data kan sparas efter omstart av appar |

## <a name="deployment"></a>Distribution

Följande funktioner/egenskaper definieras på distributions nivå och kommer att bytas ut när du växlar produktion/mellanlagrings distribution.

| Enum | Definition |
|:--|:----------------|
| Processor | Antal virtuella kärnor per app-instans |
| Minne | Inställning som allokerar minne för att skala upp eller skala ut distributioner |
| Instans</br>Antal | Antalet App-instanser, ange manuellt eller automatiskt |
| Automatisk skalning | Skala instans antal automatiskt baserat på fördefinierade regler och scheman |
| JVM</br>Alternativ | inställning: JAVA_OPTS |
| Miljö</br>Variabler | Inställningar som gäller för hela Azure våren Cloud-miljön |
| Körmiljö</br>Version | Java 8/Java 11|

## <a name="restrictions"></a>Begränsningar

* **En app måste ha en produktions distribution**: om du tar bort en produktions distribution blockeras API: et. Den bör växlas till mellanlagringsplatsen innan du tar bort den.
* **En app kan ha högst två distributioner**: om du skapar fler än två distributioner blockeras API: et. Distribuera din nya binärfil till antingen den befintliga produktions-eller mellanlagrings distributionen.
* **Distributions hantering är inte tillgängligt på Basic-nivån**: Använd standard nivån för blå och grön distributions kapacitet.

## <a name="see-also"></a>Se även
* [Konfigurera en utvecklings miljö i Azure våren Cloud](spring-cloud-howto-staging-environment.md)
