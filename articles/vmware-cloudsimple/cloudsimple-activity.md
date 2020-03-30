---
title: CloudSimple aktivitetshantering
titleSuffix: Azure VMware Solution by CloudSimple
description: Beskriver aktivitetskoncept för VMware-lösning från CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 985fcc0aa99723fde5e0c11babe3777c31cbd4c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025103"
---
# <a name="activity-management-overview"></a>Översikt över aktivitetshantering

CloudSimple håller reda på all aktivitet som kan påverka hur din privata molnmiljö fungerar. Aktiviteter inkluderar aviseringar, händelser, uppgifter och granskningsaktivitet. [Aktivitetssidorna](monitor-activity.md) sammanfattar all aktuell aktivitet och gör att du kan öka detaljnivån för ytterligare information.

## <a name="events"></a>Händelser

Händelser spårar användar- och systemaktivitet på CloudSimple-portalen. Händelser visar aktiviteten som är associerad med en viss resurs och hur allvarlig effekten är.  Du kan visa händelserna från CloudSimple-portalen.

## <a name="alerts"></a>Aviseringar

Aviseringar är aviseringar om någon betydande aktivitet i din CloudSimple-miljö. Händelser som påverkar fakturering eller användaråtkomst visas som aviseringar.  Du kan bekräfta aviseringar från CloudSimple-portalen.

## <a name="tasks"></a>Aktiviteter

Uppgifter spårar alla användaråtgärder som tar mer än 30 sekunder att slutföra. Du kan övervaka förloppet för en uppgift från CloudSimple-portalen.  För slutförda uppgifter innehåller informationen den totala tiden för slutförande.

## <a name="audit"></a>Granska

Granskningsloggar håller reda på användaråtgärder. Granskningsloggar innehåller de parametrar som tillhandahålls för åtgärden av användaren.  Du kan använda granskningsloggar för att övervaka användaraktivitet för alla användare.

## <a name="next-steps"></a>Nästa steg

* [Visa sammanfattning av kontot](account.md)