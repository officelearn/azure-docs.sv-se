---
title: Kontohantering - Azure VMware-lösning via CloudSimple-portal
description: Beskriver hur du hanterar konton på Azure VMware-lösningen via CloudSimple-portalen
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025375"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Hantera konton på Azure VMware-lösningen via CloudSimple-portalen

När du skapar din CloudSimple-tjänst skapas ett konto på CloudSimple. Kontot är kopplat till din Azure-prenumeration där tjänsten finns. Alla användare med ägar- och deltagarroller i prenumerationen har åtkomst till CloudSimple-portalen. Azure-prenumerations-ID och klient-ID som är associerade med CloudSimple-tjänsten finns på sidan Konton.

Om du vill hantera konton i CloudSimple-portalen [öppnar du portalen](access-cloudsimple-portal.md) och väljer **Konto** på sidomenyn.

Välj **Sammanfattning** om du vill visa information om företagets CloudSimple-konfiguration. Den aktuella kapaciteten för din molnkonfiguration visas, inklusive antal privata moln, total lagring, vSphere-klusterkonfiguration, antal noder och antal beräkningskärnor. En länk ingår för att köpa ytterligare noder om den aktuella konfigurationen inte uppfyller alla dina behov.

## <a name="email-alerts"></a>E-postaviseringar

Du kan lägga till e-postadresser till alla personer som du vill meddela om ändringar i konfigurationen för privat moln.

1. Klicka på **Lägg till ny**i området Ytterligare **e-postaviseringar.**
2. Ange e-postadressen.
3. Tryck på Retur.  

Om du vill ta bort en post klickar du på **X**.

## <a name="cloudsimple-operator-access"></a>CloudSimple-operatörsåtkomst

Inställningen för operatörsåtkomst gör att CloudSimple kan hjälpa dig med felsökning genom att tillåta en supporttekniker att logga in på din CloudSimple-portal.  Inställningen är aktiverad som standard. Alla åtgärder som utförs av supportteknikern när de är inloggade på ditt kundkonto registreras och är tillgängliga för din granskning på sidan > **Aktivitetsgranskning.** **Activity**

Klicka på **cloudsimple-operatörens åtkomst aktiverad** växlingsknapp för att aktivera eller inaktivera åtkomst.
