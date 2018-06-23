---
title: Så här konfigurerar du ett program med Application Proxy | Microsoft Docs
description: Lär dig hur du skapar en konfigurera ett program med APplication Proxy i några enkla steg
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 047679b52de1b095112948e869f35811346b846b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331631"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Så här konfigurerar du ett program med Application Proxy

Den här artikeln hjälper dig att förstå hur du konfigurerar ett program med Application Proxy i Azure AD att exponera dina lokala program till molnet.

## <a name="recommended-documents"></a>Rekommenderade dokument 

Mer information om inledande konfiguration och skapande av ett program för Application Proxy via Admin Portal, följer du de [publicera program med Azure AD Application Proxy](manage-apps/application-proxy-publish-azure-portal.md).

Mer information om hur du konfigurerar kopplingar finns [aktivera Application Proxy på Azure-portalen](manage-apps/application-proxy-enable.md).

Information om överföring av certifikat och använder anpassade domäner finns i [arbeta med anpassade domäner i Azure AD Application Proxy](manage-apps/application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Skapa Programinställningen URL: er

Om du följer stegen i den [publicera program med Azure AD Application Proxy](manage-apps/application-proxy-publish-azure-portal.md) dokumentation och kan komma inte att skapa programmet, finns i felinformationen och förslag om hur du löser den programmet. De vanligaste felmeddelanden som innehåller en föreslagen åtgärd. Kontrollera följande för att undvika vanliga fel:

-   Du är administratör med behörighet att skapa ett program med Application Proxy

-   Intern URL är unikt

-   Den externa URL: en är unikt

-   URL: er börja med http eller https och sluta med en ”/”

-   URL-Adressen ska vara ett domännamn, inte en IP-adress

Felmeddelandet ska visas i det övre högra hörnet när du skapar i program. Du kan också välja meddelandeikonen att se felmeddelanden.

   ![Meddelande-fråga](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Konfigurera grupper kopplingar-koppling

Om du har problem med hur du konfigurerar programmet på grund av varning om kopplingar och connector grupper finns instruktioner om hur du aktiverar programproxy för information om hur du hämtar kopplingar. Om du vill veta mer om kopplingar finns i [kopplingar dokumentationen](manage-apps/application-proxy-connectors.md).

Om din kopplingar är inaktiva, betyder det att de inte går att nå tjänsten. Detta är ofta eftersom portarna som krävs inte är öppna. Om du vill se en lista över portar som krävs finns i avsnittet förutsättningar för att aktivera Application Proxy-dokumentationen.

## <a name="upload-certificates-for-custom-domains"></a>Överför certifikat för anpassade domäner

Anpassade domäner kan du ange domänen för de externa URL: er. Om du vill använda anpassade domäner, måste du ladda upp certifikatet för domänen. Information om hur du använder anpassade domäner och certifikat finns i [arbeta med anpassade domäner i Azure AD Application Proxy](manage-apps/application-proxy-configure-custom-domain.md). 

Om du upptäcker problem överföra ditt certifikat, leta efter felmeddelanden för ytterligare information om problem med certifikatet i portalen. Vanliga problem med säkerhetscertifikat inkluderar:

-   Certifikatet har upphört att gälla

-   Certifikatet är självsignerat

-   Certifikatet saknar privat nyckel

Ett felmeddelande visas i det övre högra hörnet som du försöker ladda upp certifikatet. Du kan också välja meddelandeikonen att se felmeddelanden.

   ![Meddelande-fråga](./media/application-proxy-config-how-to/error-message2.png)

## <a name="next-steps"></a>Nästa steg
[Publicera program med Azure AD Application Proxy](manage-apps/application-proxy-publish-azure-portal.md)
