---
title: Klassiska portal Azure AD App Proxy kopplingar | Microsoft Docs
description: Beskriver hur du skapar och hanterar grupper av kopplingar i Azure AD Application Proxy.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: b283796a-9679-4c79-b703-802bb850f65d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: fc65c4053c45d9c16c62ee0fe65924133a4bb94a
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicera program på separata nätverk och platser med hjälp av connector-grupper
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-application-proxy-connectors-azure-portal.md)
> * [Klassisk Azure-portal](active-directory-application-proxy-connectors.md)
>
>

Kopplingen grupper är användbara för olika scenarier, inklusive:

* Webbplatser med flera sammankopplade datacenter. I så fall måste vill du behålla så mycket trafik i datacentret som möjligt eftersom länkar mellan datacenter är dyrt och långsamt. Du kan distribuera kopplingar i varje datacenter för att hantera de program som finns inom datacentret. Den här metoden minimerar länkar mellan datacenter och ger en upplevelse som helt transparent för användarna.
* Hantera program på isolerade nätverk som inte är en del av de huvudsakliga företagsnätverket. Du kan använda connector grupper för att installera dedikerade kopplingar på isolerade nätverk att också isolera program i nätverket.
* För program som installerats på IaaS för molnåtkomst betjäna connector grupper vanliga för säker åtkomst till alla appar. Kopplingen grupper inte skapa ytterligare beroende på företagets nätverk eller Fragmentera app-upplevelse. Kopplingar kan installeras på varje molndatacenter och hantera program som finns i det här nätverket. Du kan installera flera kopplingar för att uppnå hög tillgänglighet.
* Stöd för miljöer med flera skogar där specifika kopplingar kan distribueras per skog och ange att hantera specifika program.
* Kopplingen grupper kan användas i Disaster Recovery platser att antingen identifiera växling vid fel eller som säkerhetskopiering för den centrala platsen.
* Kopplingen grupper kan också användas för att hantera flera företag från en enskild klient.

## <a name="prerequisite-create-your-connectors"></a>Förutsättning: Skapa kopplingar
Gruppera dina kopplingar [installera flera kopplingar](active-directory-application-proxy-enable.md), namnge och gruppera dem. Slutligen har du tilldela dem till specifika appar.

## <a name="step-1-create-connector-groups"></a>Steg 1: Skapa grupper för anslutningstjänsten
Du kan skapa så många connector-grupper som du vill använda. Skapa en koppling sker i den klassiska Azure-portalen.

1. Välj din katalog och klickar på **konfigurera**.  
    ![Programproxy, konfigurera skärmbild - Klicka på Hantera connector grupper](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)
2. Klicka på under Application Proxy **hantera Connector grupper** och skapa en koppling grupp genom att ge gruppen ett namn.  
    ![Application proxy connector grupper skärmbild - namn på ny grupp](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## <a name="step-2-assign-connectors-to-your-groups"></a>Steg 2: Tilldela kopplingar till grupper
När connector-grupper skapas bör du flytta kopplingarna till lämplig grupp.

1. Under **Application Proxy**, klickar du på **hantera kopplingar**.
2. Under **grupp**, Välj den grupp som du vill använda för varje koppling. Det kan ta upp till 10 minuter ska aktiveras i den nya gruppen kopplingar.  
    ![Application proxy kopplingar skärmbild – Välj grupp från listrutan](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## <a name="step-3-assign-applications-to-your-connector-groups"></a>Steg 3: Tilldela program till connector-grupper
Det sista steget är att ange varje program i gruppen koppling som hanterar den.

1. Välj det program som du vill tilldela till gruppen och klicka på i den klassiska Azure-portalen i din katalog **konfigurera**.
2. Under **Connector grupp**, Välj den grupp som du vill att programmet ska använda. Den här ändringen tillämpas omedelbart.  
    ![Application proxy connector grupp skärmbild - Välj grupp från listrutan](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)

## <a name="see-also"></a>Se även
* [Aktivera Application Proxy](active-directory-application-proxy-enable.md)
* [Aktivera enkel inloggning](active-directory-application-proxy-sso-using-kcd.md)
* [Aktivera villkorlig åtkomst](active-directory-application-proxy-conditional-access.md)
* [Felsökning av problem med Application Proxy](active-directory-application-proxy-troubleshoot.md)

Läs mer om de senaste nyheterna och uppdateringarna i [bloggen om Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)
