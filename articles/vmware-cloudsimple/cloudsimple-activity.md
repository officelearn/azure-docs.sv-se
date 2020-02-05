---
title: Aktivitetsledning
description: Beskriver aktivitets koncept i VMware-lösning enligt AVS
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ca20413b4f21ddfcf01021f8b003b30ad2195579
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025103"
---
# <a name="activity-management-overview"></a>Översikt över aktivitets hantering

AVS håller koll på all aktivitet som kan påverka driften av din moln miljö för privata moln. Aktiviteterna omfattar aviseringar, händelser, aktiviteter och gransknings aktiviteter. [Aktivitets sidorna](monitor-activity.md) sammanfattar all aktuell aktivitet och gör det möjligt att öka detalj nivån för ytterligare information.

## <a name="events"></a>Events

Händelser spårar användare och system aktivitet på AVS-portalen. Händelser visar den aktivitet som är associerad med en speciell resurs och påverkannas allvarlighets grad.  Du kan visa händelserna från AVS-portalen.

## <a name="alerts"></a>Aviseringar

Aviseringar är aviseringar om eventuella betydande aktiviteter i din AVS-miljö. Händelser som påverkar fakturerings-eller användar åtkomst visas som aviseringar. Du kan bekräfta aviseringar från AVS-portalen.

## <a name="tasks"></a>Aktiviteter

Uppgifter spårar alla användar åtgärder som tar mer än 30 sekunder att slutföra. Du kan övervaka förloppet för en aktivitet från AVS-portalen. För slutförda uppgifter omfattar informationen den totala tiden för slut för ande.

## <a name="audit"></a>Granska

Gransknings loggar håller reda på användar åtgärder. Gransknings loggar innehåller de parametrar som har angetts för åtgärden av användaren. Du kan använda gransknings loggar för att övervaka användar aktivitet för alla användare.

## <a name="next-steps"></a>Nästa steg

* [Visa konto sammanfattningen](account.md)