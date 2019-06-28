---
title: Tillåtna certifikatutfärdare för att aktivera anpassad HTTPS på Azure ytterdörren Service | Microsoft Docs
description: Om du använder ett eget certifikat för att aktivera HTTPS på en anpassad domän måste du använda en tillåtna certifikatutfärdare (CA) för att skapa den.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: e2bab9f9e1ae099952b34e66f7250e7ecbc8e689
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330765"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Tillåtna certifikatutfärdare för att aktivera anpassad HTTPS på Azure ytterdörren Service

För en anpassad Azure ytterdörren Service-domän när du [aktivera HTTPS-funktionen med hjälp av ditt eget certifikat](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), måste du använda en tillåtna certifikatutfärdare (CA) för att skapa ett SSL-certifikat. Annars, om du använder en Certifikatutfärdare som inte är tillåtna eller ett självsignerat certifikat kan din begäran kommer att avvisas.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
