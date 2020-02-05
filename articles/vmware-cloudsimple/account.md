---
title: Konto hantering – Azure VMware-lösningar (AVS) Portal
description: Beskriver hur du hanterar konton på Azure VMware-lösningar (AVS)-portalen
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1fd7cb1c55fb664828448cef0b67ea9b16323bdf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025375"
---
# <a name="manage-accounts-on-the-azure-vmware-solutions-avs-portal"></a>Hantera konton på Azure VMware-lösningar (AVS)-portalen

När du skapar en AVS-tjänst skapas ett konto i AVS. Kontot är associerat med din Azure-prenumeration där tjänsten finns. Alla användare med rollen ägare och deltagare i prenumerationen har åtkomst till AVS-portalen. Det ID för Azure-prenumeration och klient-ID som är kopplat till AVS-tjänsten finns på konto sidan.

Om du vill hantera konton i AVS-portalen går [du till portalen](access-cloudsimple-portal.md) och väljer **konto** på menyn på sidan.

Välj **Sammanfattning** om du vill visa information om företagets AVS-konfiguration. Den aktuella kapaciteten för moln konfigurationen visas, inklusive antal moln privata moln, totalt lagrings utrymme, kluster konfiguration för vSphere, antal noder och antalet beräknings kärnor. En länk ingår för att köpa ytterligare noder om den aktuella konfigurationen inte uppfyller alla dina behov.

## <a name="email-alerts"></a>E-postaviseringar

Du kan lägga till e-postadresser till alla personer som du vill meddela om ändringar i konfigurationen för molnets privata moln.

1. Klicka på **Lägg till nytt**i avsnittet **ytterligare e-postaviseringar** .
2. Ange e-postadressen.
3. Tryck på RETUR.  

Klicka på **X**om du vill ta bort en post.

## <a name="avs-operator-access"></a>Åtkomst till AVS-operatör

Med åtkomst inställningen operatör kan AVS hjälpa dig med fel sökning genom att tillåta en support tekniker att logga in på din AVS-Portal. Inställningen är aktive rad som standard. Alla åtgärder som utförs av support teknikern när de är inloggade på ditt kund konto registreras och är tillgängliga för din granskning på sidan för **aktivitets** > **granskning** .

Klicka på den **AVS-operatörens åtkomst aktive rad** växla för att aktivera eller inaktivera åtkomst.
