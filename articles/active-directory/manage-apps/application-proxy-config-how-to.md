---
title: Konfigurera ett programproxyprogram | Microsoft-dokument
description: Lär dig hur du skapar och konfigurerar ett APplication Proxy-program i några enkla steg
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7aaf2eb282bc3fd0b9f3853ce493c479a3d3c3a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67807859"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Konfigurera ett programproxyprogram

Den här artikeln hjälper dig att förstå hur du konfigurerar ett programproxyprogram i Azure AD för att exponera dina lokala program för molnet.

## <a name="recommended-documents"></a>Rekommenderade dokument

Om du vill veta mer om de första konfigurationerna och skapandet av ett programproxyprogram via administratörsportalen följer du [publiceringsprogrammen med Azure AD Application Proxy](application-proxy-add-on-premises-application.md).

Mer information om hur du konfigurerar anslutningsappar finns [i Aktivera programproxy i Azure-portalen](application-proxy-add-on-premises-application.md).

Information om hur du laddar upp certifikat och använder anpassade domäner finns [i Arbeta med anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Skapa programmet/ställa in webbadresserna

Om du följer stegen i [Publiceringsprogrammen med Azure AD Application Proxy-dokumentation](application-proxy-add-on-premises-application.md) och får ett felmeddelande om hur du skapar programmet läser du felinformationen för information och förslag på hur du åtgärdar programmet. De flesta felmeddelanden innehåller en föreslagen korrigering. För att undvika vanliga fel, kontrollera:

- Du är administratör med behörighet att skapa ett programproxyprogram
- Den interna webbadressen är unik
- Den externa webbadressen är unik
- Webbadresserna börjar med http eller https och slutar med ett "/"
- Webbadressen ska vara ett domännamn, inte en IP-adress

Felmeddelandet ska visas i det övre högra hörnet när du skapar programmet. Du kan också välja meddelandeikonen för att se felmeddelandena.

![Visar var meddelandeprompten finns i Azure-portalen](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Konfigurera kopplingar/kopplingsgrupper

Om du har problem med att konfigurera programmet på grund av varning om kopplingar och anslutningsgrupper läser du instruktioner om hur du aktiverar Programproxy för mer information om hur du hämtar kopplingar. Om du vill veta mer om kopplingar läser du [dokumentationen](application-proxy-connectors.md)för kopplingar .

Om dina kopplingar är inaktiva innebär det att de inte kan nå tjänsten. Detta beror ofta på att alla nödvändiga portar inte är öppna. Information om hur du ser en lista över nödvändiga portar finns i avsnittet för erfordringar i dokumentationen för programproxy.

## <a name="upload-certificates-for-custom-domains"></a>Ladda upp certifikat för anpassade domäner

Med anpassade domäner kan du ange domänen för externa webbadresser. Om du vill använda anpassade domäner måste du ladda upp certifikatet för den domänen. Information om hur du använder anpassade domäner och certifikat finns i [Arbeta med anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md).

Om du stöter på problem med att ladda upp certifikatet letar du efter felmeddelandena i portalen efter ytterligare information om problemet med certifikatet. Vanliga certifikatproblem är:

- Utgånget certifikat
- Certifikatet är självsignerat
- Certifikatet saknar den privata nyckeln

Felmeddelandet visas i det övre högra hörnet när du försöker ladda upp certifikatet. Du kan också välja meddelandeikonen för att se felmeddelandena.

## <a name="next-steps"></a>Nästa steg

[Publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md)
