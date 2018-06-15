---
title: Översikt över Azure Container Instances
description: Förstå Azure Container Instances
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 03/29/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 2da61d52e43857237f6e231c03ea62ba5eefdadd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33767979"
---
# <a name="azure-container-instances"></a>Azure Container Instances

Behållare är på väg att bli det bästa sättet att paketera, distribuera och hantera molnprogram. Azure Container Instances erbjuder det snabbaste och enklaste sättet att köra en behållare i Azure, utan att behöva hantera några virtuella datorer och utan att behöva använda en tjänst på högre nivå.

Azure Container Instances är en bra lösning för alla scenarier som kan fungera i isolerade behållare, däribland enkla program, automatisering av uppgifter och att skapa jobb. För scenarier där du behöver fullständig behållarorkestrering, inklusive tjänstidentifiering för flera behållare, automatisk skalning och koordinerade programuppgraderingar rekommenderar vi [Azure Kubernetes Service (AKS)](../aks/index.yml).

## <a name="fast-startup-times"></a>Snabba starttider

Med behållare får du betydande startfördelar jämfört med virtuella datorer. Azure Container Instances kan starta en behållare i Azure på några sekunder utan att några virtuella datorer behöver etableras eller hanteras.

## <a name="public-ip-connectivity-and-dns-name"></a>Offentlig IP-anslutning och DNS-namn

Med Azure Container Instances kan du exponera dina behållare direkt för Internet med en IP-adress och ett fullständigt domännamn (FQDN). När du skapar en behållarinstans kan du ange en anpassad DNS-namnsetikett, så att programmet kan nås på *customlabel*. *azureregion*. azurecontainer.io.

## <a name="hypervisor-level-security"></a>Säkerhet på hypervisornivå

Tidigare har behållare erbjudit isolering av programberoenden och resursstyrning men har inte ansetts vara tillräckligt strikta för fientlig användning med flera innehavare. Azure Container Instances garanterar att ditt program är lika isolerat i en behållare som i en virtuell dator.

## <a name="custom-sizes"></a>Anpassade storlekar

Behållare är vanligtvis optimerade för att endast köra ett program, men de specifika behoven för dessa program kan skilja sig åt avsevärt. Azure Container Instances erbjuder optimal användning eftersom det tillåter exakta specifikationer av CPU-kärnor och minne. Du betalar för vad du behöver och faktureras per sekund, så att du kan finjustera dina utgifter utifrån dina faktiska behov.

## <a name="persistent-storage"></a>Beständig lagring

Vi erbjuder direkt [montering av Azure Files-resurser](container-instances-mounting-azure-files-volume.md) för att hämta och bevara tillstånd med Azure Container Instances.

## <a name="linux-and-windows-containers"></a>Linux- och Windows-behållare

Azure Container Instances kan schemalägga både Windows- och Linux-behållare med samma API. Ange typ av operativsystem när du skapar dina [behållargrupper](container-instances-container-groups.md).

Vissa funktioner är för närvarande begränsade till Linux-behållare. Medan vi arbetar med att förse Windows-behållare med funktionsparitet kan du se de nuvarande skillnaderna mellan plattformarna i informationen om [kvoter och regional tillgänglighet för Azure Container Instances](container-instances-quotas.md).

## <a name="co-scheduled-groups"></a>Samordna schemalagda grupper

Azure Container Instances stöder schemaläggning av [grupper med flera behållare](container-instances-container-groups.md) som delar en värddator, lokalt nätverk, lagring och livscykel. Det gör att du kan kombinera din huvudprogrambehållare med andra stödrollsbehållare, t.ex. sidecar-filer för loggning.

## <a name="next-steps"></a>Nästa steg

Försök att distribuera en behållare till Azure med ett enda kommando med hjälp av vår snabbstartsguide:

> [!div class="nextstepaction"]
> [Azure Container Instances Snabbstart](container-instances-quickstart.md)
