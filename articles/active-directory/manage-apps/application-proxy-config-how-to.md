---
title: Så här konfigurerar du ett program med Application Proxy | Microsoft Docs
description: Lär dig hur du skapar en konfigurera ett programproxy-program i några enkla steg
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95f22f064043467bf52c23cab547a7e6c8ba2205
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172322"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Så här konfigurerar du ett programproxy-program

Den här artikeln hjälper dig att förstå hur du konfigurerar en Application Proxy-program i Azure AD att exponera dina lokala program till molnet.

## <a name="recommended-documents"></a>Rekommenderade dokument 

Mer information om att första konfigurationer och skapa ett programproxy-program via administrationsportalen, följer du de [publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md).

Mer information om hur du konfigurerar anslutningar finns i [aktivera Application Proxy på Azure-portalen](application-proxy-add-on-premises-application.md).

Information om att ladda upp certifikat och använda anpassade domäner finns i [arbeta med anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Skapa programinställning/URL: er

Om du följer stegen i den [publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md) dokumentation och att du får ett fel som skapar program, se felinformationen för information och förslag på hur du åtgärdar den programmet. De vanligaste felmeddelanden som innehåller en föreslagen åtgärd. Kontrollera om du vill undvika vanliga fel:

-   Du är administratör med behörighet att skapa en Application Proxy-program

-   Den interna URL: en är unikt

-   Den externa URL: en är unikt

-   URL: er börjar med http eller https och sluta med en ”/”

-   URL: en ska vara ett domännamn, inte en IP-adress

Ett felmeddelande visas i det övre högra hörnet när du skapar programmet. Du kan också välja meddelandeikonen till felmeddelanden visas.

   ![Fråga om meddelande](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Konfigurera anslutningar/anslutningsapp-grupper

Om du har svårt att konfigurera ditt program på grund av varning om kopplingar och anslutningsapp-grupper, läser du anvisningarna om hur du aktiverar programproxy för information om hur du hämtar kopplingar. Om du vill veta mer om anslutningsappar finns i den [kopplingar dokumentation](application-proxy-connectors.md).

Om dina anslutningar är inaktiva, innebär det att de är det går inte att nå tjänsten. Det här är ofta eftersom inte alla nödvändiga portar är öppna. Om du vill se en lista över portar som krävs finns i avsnittet förutsättningar för att aktivera Application Proxy-dokumentationen.

## <a name="upload-certificates-for-custom-domains"></a>Ladda upp certifikat för egna domäner

Anpassade domäner kan du ange domänen för ditt externa URL: er. Om du vill använda anpassade domäner, måste du ladda upp certifikatet för domänen. Information om hur du använder anpassade domäner och certifikat finns i [arbeta med anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md). 

Om du stöter på problem som har överfört certifikatet, leta efter felmeddelanden i portal för mer information om problem med certifikatet. Vanliga problem med säkerhetscertifikat är:

-   Utgånget certifikat

-   Certifikatet är självsignerat

-   Certifikatet saknar privat nyckel

Ett felmeddelande visas i det övre högra hörnet när du försöker ladda upp certifikatet. Du kan också välja meddelandeikonen till felmeddelanden visas.

   ![Fråga om meddelande](./media/application-proxy-config-how-to/error-message2.png)

## <a name="next-steps"></a>Nästa steg
[Publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md)
